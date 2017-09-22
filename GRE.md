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
**Cấu hình Node1**

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
**Cấu hình trên Node2**
```sh
ip tunnel add tun8 mode gre remote 10.0.2.18 local 10.0.2.17 ttl 255
ip link set tun8 up
ip addr add 20.0.0.1 dev tun8
```
Kiểm tra cau hinh

```sh
# ip a
6: tun8@NONE: <POINTOPOINT,NOARP,UP,LOWER_UP> mtu 1476 qdisc noqueue state UNKNOWN group default qlen 1
    link/gre 10.0.2.17 peer 10.0.2.18
    inet 20.0.0.1/24 scope global tun8
       valid_lft forever preferred_lft forever
    inet6 fe80::5efe:a00:211/64 scope link 
       valid_lft forever preferred_lft forever

```

**Cấu hình định tuyến trên Node2**

Từ Node2 ta cần định tuyến cho dải LAN1:192.168.56.0/24 có được Route với ip tunel 20.0.0.1
```sh
root@node2:~# ip r
default via 10.0.2.2 dev enp0s3 onlink 
10.0.2.0/24 dev enp0s3  proto kernel  scope link  src 10.0.2.17 
20.0.0.0/24 dev tun8  proto kernel  scope link  src 20.0.0.1 
192.168.56.0/24 via 20.0.0.1 dev tun8 
192.168.57.0/24 dev enp0s8  proto kernel  scope link  src 192.168.57.5 
```
Từ Node1 ta cần định tuyến cho dải LAN2:192.167.57.0/24 Route với ip tunel 20.0.0.2
```sh
root@node1:~# ip r
default via 10.0.2.2 dev enp0s3 onlink 
10.0.2.0/24 dev enp0s3  proto kernel  scope link  src 10.0.2.18 
20.0.0.0/24 dev tun8  proto kernel  scope link  src 20.0.0.2 
192.168.56.0/24 dev enp0s8  proto kernel  scope link  src 192.168.56.2 
192.168.57.0/24 via 20.0.0.2 dev tun8
```
**Kiểm tra kết nối sử dụng Ping + SSH**

**Từ Com1 ping và ssh tới Com2**
```sh
root@com1:~# ping 192.168.57.4
PING 192.168.57.4 (192.168.57.4) 56(84) bytes of data.
64 bytes from 192.168.57.4: icmp_seq=1 ttl=62 time=0.786 ms
64 bytes from 192.168.57.4: icmp_seq=2 ttl=62 time=0.599 ms
64 bytes from 192.168.57.4: icmp_seq=3 ttl=62 time=0.675 ms
64 bytes from 192.168.57.4: icmp_seq=4 ttl=62 time=0.599 ms
64 bytes from 192.168.57.4: icmp_seq=5 ttl=62 time=0.666 ms
64 bytes from 192.168.57.4: icmp_seq=6 ttl=62 time=0.604 ms
64 bytes from 192.168.57.4: icmp_seq=7 ttl=62 time=0.637 ms
64 bytes from 192.168.57.4: icmp_seq=8 ttl=62 time=0.581 ms
64 bytes from 192.168.57.4: icmp_seq=9 ttl=62 time=0.635 ms
64 bytes from 192.168.57.4: icmp_seq=10 ttl=62 time=0.579 ms
```

**Từ com1 ssh tới com 2 thành công và TCPDUMP trên tun8 thu được kết quả**
```sh
11:15:58.495634 IP 192.168.57.4.ssh > 192.168.56.4.48998: Flags [P.], seq 1:37, ack 36, win 582, options [nop,nop,TS val 1773076 ecr 1773688], length 36
11:15:58.495808 IP 192.168.56.4.48998 > 192.168.57.4.ssh: Flags [.], ack 37, win 668, options [nop,nop,TS val 1773688 ecr 1773076], length 0
11:15:58.718034 IP 192.168.56.4.48998 > 192.168.57.4.ssh: Flags [P.], seq 36:72, ack 37, win 668, options [nop,nop,TS val 1773744 ecr 1773076], length 36
11:15:58.718749 IP 192.168.57.4.ssh > 192.168.56.4.48998: Flags [P.], seq 37:73, ack 72, win 582, options [nop,nop,TS val 1773132 ecr 1773744], length 36
11:15:58.718905 IP 192.168.56.4.48998 > 192.168.57.4.ssh: Flags [.], ack 73, win 668, options [nop,nop,TS val 1773744 ecr 1773132], length 0
11:15:58.814205 IP 192.168.56.4.48998 > 192.168.57.4.ssh: Flags [P.], seq 72:108, ack 73, win 668, options [nop,nop,TS val 1773768 ecr 1773132], length 36
11:15:58.815088 IP 192.168.57.4.ssh > 192.168.56.4.48998: Flags [P.], seq 73:109, ack 108, win 582, options [nop,nop,TS val 1773156 ecr 1773768], length 36
11:15:58.815246 IP 192.168.56.4.48998 > 192.168.57.4.ssh: Flags [.], ack 109, win 668, options [nop,nop,TS val 1773768 ecr 1773156], length 0
11:15:58.998075 IP 192.168.56.4.48998 > 192.168.57.4.ssh: Flags [P.], seq 108:144, ack 109, win 668, options [nop,nop,TS val 1773814 ecr 1773156], length 36
11:15:58.998848 IP 192.168.57.4.ssh > 192.168.56.4.48998: Flags [P.], seq 109:145, ack 144, win 582, options [nop,nop,TS val 1773202 ecr 1773814], length 36
```
**Từ Com2 ping và ssh tới Com1**

```sh
root@Com2:~# ping 192.168.56.4
PING 192.168.56.4 (192.168.56.4) 56(84) bytes of data.
64 bytes from 192.168.56.4: icmp_seq=1 ttl=62 time=1.11 ms
64 bytes from 192.168.56.4: icmp_seq=2 ttl=62 time=0.589 ms
64 bytes from 192.168.56.4: icmp_seq=3 ttl=62 time=1.44 ms
64 bytes from 192.168.56.4: icmp_seq=4 ttl=62 time=0.570 ms
^C
--- 192.168.56.4 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3001ms
rtt min/avg/max/mdev = 0.570/0.929/1.442/0.368 ms
```
**SSH**
```sh
root@Com2:~# ssh hannv@192.168.56.4
hannv@192.168.56.4's password: 
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.4.0-87-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

57 packages can be updated.
37 updates are security updates.

Last login: Fri Sep 22 11:12:51 2017 from 192.168.56.1
hannv@com1:~$
```
