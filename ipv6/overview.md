## 1. Biểu diễn của địa chỉ
Địa chỉ IPv6 dài 128 bit, được chia làm 8 nhóm, mỗi nhóm gồm 16 bit, được ngăn cách với nhau bằng dấu hai chấm “:”. Mỗi nhóm được biểu diễn bằng 4 số hexa.
Ví dụ: FEDC:BA98:768A:0C98:FEBA:CB87:7678:1111
1080:0000:0000:0070:0000:0989:CB45:345F

Những địa chỉ này lớn, khả năng cung cấp địa chỉ cho nhiều node và cung cấp cấu trúc phân cấp linh hoạt, nhưng nó không dễ để viết ra. Vì vậy cần có 1 số nguyên tắc để nhằm rút ngắn lại cách biểu diễn địa chỉ IPv6. Sau đây là các quy tắc để rút gọn IPv6:
– Cho phép bỏ các số 0 nằm trước mỗi nhóm (octet).
– Thay bằng số 0 cho nhóm có toàn số 0.
– Thay bằng dấu “::” cho các nhóm liên tiếp nhau có toàn số 0.

Ví dụ về nén địa chỉ IPv6:
Cho một địa chỉ: 1080:0000:0000:0070:0000:0989:CB45:345F
Dựa theo các quy tắc đã nêu trên, có thể nén địa chỉ IP trên như sau: 1080::70:0:989:CB45:345F hoặc 1080:0:0:70::989: CB45:345F

Chú ý: Dấu “::” chỉ sử dụng đƣợc 1 lần trong toàn bộ địa chỉ IPv6 (nhiều dấu “::” có thể gây ra sự nhầm lẫn hoặc không thể biết đúng vị trí của các octet trong địa chỉ IPv6).

## 2. Biểu diễn của Address Prefixes
Prefix của địa chỉ IPv6 được biểu diễn tương tự với kí hiệu IPv4 CIDR. IPv6 prefix được biểu diễn như sau: IPv6-address/ prefix-length
Trong đó:
IPv6-address là bất kì địa chỉ có giá trị, Prefix-length là số bit liền kề nhau được bao gồm trong prefix.

Ví dụ: Sau đây là quy tắc biểu diễn cho 56 bit prefix 200F00000000AB:
200F::AB00:0:0:0:0/56
200F:0:0:AB00::/56

Chú ý với địa chỉ IPv6, kí hiệu “::” được sử dụng 1 lần duy nhất trong mỗi sự biểu diễn.
Theo sau là các cách biểu diễn sai của 56 bit prefix:
200F:0:0:AB/56
200F::AB00/56
200F::AB/56

Cách biểu diễn đầu tiên là không hợp lệ bởi vì các số 0 theo sau trong vòng một trường 16-bit (AB00) bị mất, và địa chỉ không đủ chiều dài hợp lệ. Địa chỉ IPv6 trên bên trái của dấu gạch chéo “/” phải là một địa chỉ IPv6 có chiều dài đầy đủ hoặc được nén hợp lệ. Cách biểu diễn thứ hai và thứ ba là địa chỉ IPv6 được nén hợp lệ nhưng nó không giãn ra thành địa chỉ chính xác. Thay vì 200F:0000:0000:AB00:0000:0000:0000:0000 nó sẽ giãn thành 200F:0000:0000:0000:0000:0000:0000:AB00 và 200F:0000: 0000:0000:0000:0000:0000:00AB, tương ứng.

Để xem một địa chỉ IPv6 được phân chia như thế nào thành các phần con khác nhau của nó, bạn hãy quan sát đến địa chỉ dưới đây:

2001:0DC8:0000:0000:1005:2F43:0BCD:FFFF/64

Trong IPv6, một LAN, thường nhận /64 là tiền tố subnet (subnet prefix) , có nghĩa 64 bit được sử dụng để biểu diễn phần mạng, trong khi đó, 64 bit còn lại được sử dụng như ID giao diện (interface ID). Do vậy, tiền tố subnet là 2001:0DC8:0000:0000 và định danh giao diện 1005:2F43:0BCD:FFFF.

Khi một tổ chức nhận vùng địa chỉ IPv6 phân bổ từ một ISP, trong đa số trường hợp là 48 bit (/48), ví dụ 2001:260:20::/48. Khi đó, 16 bit là tự do sử dụng làm subnet ID để xây dựng lên các subnet khác nhau. Ví dụ 2001:260:20:1000::/64 (subnet ID 1000) và 2001:260:20:2000::/64(subnet ID 2000) là thuộc cùng một mạng, vì chúng có 48 bít đầu là như nhau (2001:260:20::).

## 3. Phân loại địa chỉ IPv6
Một địa chỉ IPv6 có thể được phân thành 1 trong 3 loại:

– Unicast
Một địa chỉ unicast được định nghĩa duy nhất trên một cổng của một node IPv6. Một gói tin được gởi đến một địa chỉ unicast được đưa đến cổng được định nghĩa bởi địa chỉ đó.

– Multicast
Một địa chỉ multicast định nghĩa một nhóm các cổng IPv6. Một gói tin gởi đến địa chỉ multicast được xử lý bởi tất cả những thành viên của nhóm multicast.

– Anycast
Một địa chỉ anycast được đăng kí cho nhiều cổng (trên nhiều node). Một gói tin được gởi đến một địa chỉ anycast là được chuyển đến chỉ một trong số các cổng này, thường là gần nhất.

Global Routing Prefixes
Bảng sau đây đưa ra các prefix được gán hiện tại của tiền tố dành riêng và địa chỉ đặc biệt, ví dụ như địa chỉ Link-local hoặc địa chỉ multicast. Phần chính của không gian địa chỉ (hơn 80%) không được gán, để chỗ cho những đăng kí trong tương lai.