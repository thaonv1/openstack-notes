# Octavia architecture overview

## Giới thiệu

Octavia là một giải pháp cân bằng tải được thiết kế để hoạt động với OpenStack.

Trước đây, neutron có phát triển một module để cung cấp LBaaS đó là neutron-lbaas, tuy nhiên kể từ bản Queens thì Octavia đã được recommend để thay thế cho nó. Về bản chất, Octavia là 1 project độc lập mặc dù nó kế thừa gần như hoàn toàn kiến trúc của neutron-lbaas và đương nhiên, nó cũng sẽ có bộ API độc lập hoàn toàn so với người tiền nhiệm.

## Các concept cơ bản

- `LBaaS`: Load Balancing as a Service
- `LoadBalancer`: Root object của dịch vụ cân bằng tải, người dùng định nghĩa các cấu hình trên đây.
- `VIP`: Địa chỉ IP ảo được liên kết với LoadBalancer. Mỗi LoadBalancer có ít nhất một VIP để bên ngoài truy cập vào back-end
- `Listener`: Listener thuộc LB, có thể được cấu hình để monitor loại truy cập tới VIP.
- `Pool`: Cụm cluster máy chủ ở backend.
- `Member`: Cloud host thuộc pool
- `Health Monitor`: Liên kết với pool, định kì sẽ check các member trong pool
- `L7 Policy`: Policy layer 7 sẽ quyết định hành động foward packet
- `L7 Rule`: Thuộc L7 policy, định nghĩa việc matching giữa domain với phần packet forward

## Quy trình sử dụng cơ bản

Đầu tiên là ta cần nhìn qua phần kiến trúc về network của Octavia

<img src="https://i.imgur.com/7aWc1Lj.png">

Trong kiến trúc trên sẽ có những thành phần:

- `Amphora(e)`: Là một thực thể thực hiện các nhiệm vụ của một load balancer, nó cũng là provider mặc định khi dùng Octavia
- `lb-mgmt-net`: Network kết nối OpenStack mgnt/api network với Amphora và Octavia Service.
- `tenant-net` : Network của các máy chủ cloud ở backend
- `vip-net` : Network cung cấp các VIP để truy cập từ bên ngoài vào.

**Lưu ý**: tenant-network và vip-net hoàn toàn có thể gộp thành 1, thậm chí bạn có thể gộp luôn cả lb-mgmt-net vào. Tuy nhiên ta nên tách ra để có thể phân hoạch cũng như sử dụng các chính sách bảo mật tốt hơn trong môi trường production.

Đây là mô hình khởi đầu khi chưa có kết nối của Octavia

<img src="https://i.imgur.com/32QIv1E.png">

Các bước để khởi tạo LB trên dashboard

**Bước 1**: Khai báo VIP, nó sẽ được lựa chọn để gán trực tiếp qua DHCP

<img src="https://i.imgur.com/wZ6ufc2.png">

**Bước 2**: Chỉ định giao thức và port sẽ được monitor bởi listener

Như dưới đây là ta sẽ truy cập thông qua địa chỉ sau: `http://VIP:8080`

<img src="https://i.imgur.com/K2RXdJY.png">

**Bước 3**: Xác định thuật toán LB, ta có 3 loại để có thể lựa chọn

<img src="https://i.imgur.com/3D9WCnb.png">

**Bước 4**: Chỉ định member cho pool, bạn cũng cần phải xác định port để chuyển tiếp kết nối và mức độ ưu tiên thông qua 2 thông số là port và weight

<img src="https://i.imgur.com/mlwgI1A.png">

**Bước 5**: Xác định health check rule, nếu member không thể phản hồi health check, member đó sẽ bị đánh dấu fail và không còn chấp nhận chuyển tiếp từ trên LB nữa

<img src="https://i.imgur.com/HUXEguX.png">

Sau đây là mô hình sau khi hoàn thành việc tạo amphora

<img src="https://i.imgur.com/PSk1S5F.png">

Như vậy thì đây là ý tưởng của octavia:

- Amphora sẽ chủ động giao tiếp với Octavia service thông qua agent được cài sẵn trong nó
- Sau khi octavia service nhận được yêu cầu về việc khởi tạo lb cũng như vip thì nó sẽ nói chuyện với agent để thay đổi file cấu hình của haproxy và keepalived
- Amphora sẽ được kết nối tới subnet chứa các member và haproxy sẽ chuyển tiếp thông qua ip và port

<img src="https://i.imgur.com/NlGgsl6.png">

## Kiến trúc phần mềm

<img src="https://i.imgur.com/GHsK1Pj.png">

Thiết kế của Octavia vẫn theo hướng provider - client giao tiếp bất đồng bộ. API tách biệt với worker và giao tiếp với nhau thông qua Message Queue.

- Octavia API : Octavia API v2 là superset của LBaaS v2 API và chúng tương thích lẫn nhau.
- Octavia Controller Worker: Thành phần core của octavia, nó là layer ở dưới tương thích với thiết kế Driver & Plugin và là nền tảng cho 3 layer ở phía trên:
    - Octavia Worker: Chịu trách nhiệm hoàn thành các yêu cầu API và thực thi các chức năng xương sống của Octavia.
    - Health Manager: đảm bảo tính HA 
    - Housekeeping Manager: dọn dẹp môi trường cho octavia, nó sẽ dựa vào cấu hình và db để dọn dẹp các amphora ko còn cần thiết nữa

Với kiến trúc Driver & Plugin thì Amphora có thể sẵn sàng cho việc tích hợp với các provider khác ví dụ như f5.
