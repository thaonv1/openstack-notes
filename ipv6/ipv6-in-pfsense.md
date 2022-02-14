# Hướng dẫn cấu hình cấp DHCP IPv6 với pfsense

**Bước 1: Đặt ipv6 cho interface**  

<img src="images/Screenshot_1.png">

<img src="images/Screenshot_2.png">

Sau đó save lại.

**Bước 2: Mở rule cho ipv6 trên interface tương ứng**

<img src="images/Screenshot_3.png">

**Bước 3: Bật dhcpv6**

<img src="images/Screenshot_4.png">

<img src="images/Screenshot_5.png">

<img src="images/Screenshot_6.png">

**Bước 6: Bật máy ảo dùng dhcp và kiểm tra**

Lưu ý: Cần đảm bảo os đã enable ipv6
Ví dụ ở đây mình sử dụng ubuntu20.04

Cấu hình network như sau:

<img src="images/Screenshot_7.png">

<img src="images/Screenshot_8.png">

<img src="images/Screenshot_9.png">

Lưu ý: Ipv6 không cấp prefix length qua dhcp. Bản tin RA sẽ cung cấp các thông tin về routing.

