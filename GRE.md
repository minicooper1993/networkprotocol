## Tìm hiểu về GRE




**1) Giới thiệu**

GRE-Generic Routing Encapsulation là giao thức được phát triển đầu tiên bởi Cisco, Giao thức này sẽ đóng gói một số kiểu gói tin vào bên trong các IP tunnels để tạo thành các kết nối điểm-điểm (point-to-point) ảo. Các IP tunnel chạy trên hạ tầng mạng công cộng.
Gói tin sau khi đóng gói được truyền qua mạng IP và sử dụng GRE header để định tuyến. Mỗi lần gói tin GRE đi đến đích, lần lượt các header ngoài cùng sẽ được gỡ bỏ cho đến khi gói tin ban đầu được mở ra.

**2) Tính năng**

GRE thêm vào tối thiểu 24 byte vào gói tin, trong đó bao gồm 20-byte IP header mới, 4 byte còn lại là GRE header. GRE có thể tùy chọn thêm vào 12 byte mở rộng để cung cấp tính năng tin cậy như: checksum, key chứng thực, sequence number.

![Caption for the picture.](https://i.imgur.com/hvR7wmD.png)

- GRE là công cụ tạo tunnel khá đơn giản nhưng hiệu quả. Nó có thể tạo tunnel cho bấy kì giao thức lớp 3 nào.
- GRE cho phép những giao thức định tuyến hoạt động trên kênh truyền của mình.
- GRE không có cơ chế bảo mật tốt. Trong khi đó, IPSec cung cấp sự tin cậy cao. Do đó nhà quản trị thường kết hợp GRE với IPSec để tăng tính bảo mật, đồng thời cũng hỗ trợ IPSec trong việc định tuyến và truyền những gói tin có địa chỉ IP Muliticast.

**3) GRE Header**

GRE header bản thân nó chứa đựng 4 byte, đây là kích cỡ nhỏ nhất của một GRE header khi không thêm vào các tùy chọn. 2 byte đầu tiên là các cờ (flags) để chỉ định những tùy chọn GRE. Những tùy chọn này nếu được active, nó thêm vào GRE header. Bảng sau mô tả những tùy chọn của GRE header.

![Caption for the picture.](https://i.imgur.com/JzW6h04.png)
 
Trong GRE header 2 byte còn lại chỉ định cho trường giao thức. 16 bits này xác định kiểu của gói tin được mang theo trong GRE tunnel. Hình sau mô tả cách mà một gói tin GRE với tất cả tùy chọn được gán vào một IP header và data.

![Caption for the picture.](https://i.imgur.com/Tgi9hDB.png)

**4) GRE over IPSec**

Giao thức IPSec có độ an toàn và bảo mật cao, trong khi GRE thì bản thân nó không bảo mật. Nhưng ngược lại, GRE cung cấp khả năng định tuyến và hỗ trợ multicast còn IPSec thì không. Do đó, sự kết hợp giữa IPSec và GRE tạo nên một giải pháp tối ưu khi triển khai mạng DMVPN, mang lại các tính năng sau:
- Sự bảo mật, toàn vẹn dữ liệu và chứng thực đầu cuối
- Tăng khả năng mở rộng cho việc thiết kế mạng
- Đáp ứng các ứng dụng multicast.

**Hoạt Động**

GRE over IPSec là sự kết hợp giữa GRE và IPSec. Lúc này các gói tin GRE sẽ được truyền dẫn qua kênh truyền bảo mật do IPSec thiết lập. Điều này được thực hiện thông qua việc IPSec sẽ đóng gói packets GRE bởi các tham số bảo mật của mình.
GRE over IPSec cũng có hai mode hoạt động; Tunnel mode và Transport mode

![Caption for the picture.](https://i.imgur.com/ABt5fQb.png)

Nhìn hình trên ta thấy, có nhiều lớp IP bên trong một gói tin GRE over IPSec. Lớp trong cùng là gói tin IP ban đầu . Gói tin này được bao bọc trong một GRE header để cho phép chạy những giao thức định tuyến bên trong GRE tunnel. Cuối cùng, IPSec được thêm vào lớp  ngoài cùng để cung cấp sự bảo mật và toàn vẹn. Kết quả là hai site có thể trao đổi thông tin định tuyến và những mạng IP với nhau một cách an toàn.
GRE over IPSec thường sử dụng chế độ transport, nếu những điểm cuối GRE và IPSec là một, ngược lại thì sử dụng tunnel mode. Dù sử dụng tunnel hay transport mode, IP header và gói tin ban đầu cũng được bảo vệ một cách đầy đủ.

**5) Triển khai GRE**

**Mô Hình lab**


![Caption for the picture.](https://i.imgur.com/8VctlkN.png)

Trước tiên để cấu hình GRE Tunnel cần kiểm tra xem IP_GRE trên các host
```sh
modprobe ip_gre
lsmod | grep gre
```
**Cấu hình Note1**

```sh
ip tunnel add tun8 mode gre remote 10.0.2.17 local 10.0.2.18 ttl 255
ip link set tun8 up
ip addr add 20.0.0.2 dev tun8
```
Kiểm tra cấu hình 
```sh
# ip a
6: tun8@NONE: <POINTOPOINT,NOARP,UP,LOWER_UP> mtu 1476 qdisc noqueue state UNKNOWN group default qlen 1
    link/gre 10.0.2.18 peer 10.0.2.17
    inet 20.0.0.2/24 scope global tun8
       valid_lft forever preferred_lft forever
    inet6 fe80::5efe:a00:212/64 scope link 
       valid_lft forever preferred_lft forever
```
**Cấu hình trên host B**
```sh
ip tunnel add tun8 mode gre remote 10.0.2.18 local 10.0.2.17 ttl 255
ip link set tun8 up
ip addr add 20.0.0.1 dev tun8
```
Kiểm tra kết nối

```sh
# ip a
6: tun8@NONE: <POINTOPOINT,NOARP,UP,LOWER_UP> mtu 1476 qdisc noqueue state UNKNOWN group default qlen 1
    link/gre 10.0.2.17 peer 10.0.2.18
    inet 20.0.0.1/24 scope global tun8
       valid_lft forever preferred_lft forever
    inet6 fe80::5efe:a00:211/64 scope link 
       valid_lft forever preferred_lft forever

```

**Cấu hình định tuyến trên Node1**

```sh
root@node2:~# ip r
default via 10.0.2.2 dev enp0s3 onlink 
10.0.2.0/24 dev enp0s3  proto kernel  scope link  src 10.0.2.17 
20.0.0.0/24 dev tun8  proto kernel  scope link  src 20.0.0.1 
192.168.56.0/24 via 20.0.0.1 dev tun8 
192.168.57.0/24 dev enp0s8  proto kernel  scope link  src 192.168.57.5 
```



