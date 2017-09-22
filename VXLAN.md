**Tìm hiểu về VXLAN


**Giới thiệu**

- Trong data center, yêu cầu hàng nghìn VLAN để cô lập traffic môi trường multi-tenant chia sẻ như cơ sở hạ tầng L2/L3 cho Cloud Service Provider. Hiện tại, giới hạn 4096 VLANs là ko đủ.

- Do ảo hóa server, mỗi Virtual Machine (VM) yêu cầu 1 địa chỉ MAC duy nhất và 1 địa chỉ IP duy nhất. Vì vậy, có hàng ngàn bảng MAC trên các thiết bị switches. Điều này đặt ra nhu cầu lớn hơn về công suất của switches.

- VLANs quá hạn chế về khoảng cách và triển khai. VTP có thể được sử dụng để triển khai các VLAN qua các L2 switchese nhưng hầu hết mọi người muốn tắt VTP vì tính tác hại của nó.

- VXLAN là giao thức tunneling, thuộc giữa lớp 2 và lớp 3.

**VXLAN-Virtual Extension LAN**

- Địa chỉ VXLAN giải quyết các thách thức ở trên. Công nghệ VXLAN cung cấp các dịch vụ kết nối các Ethernet end systems và cung cấp phương tiện mở rộng mạng LAN qua mạng L3. VXLAN ID (VXLAN Network Identifier hoặc VNI) là 1 chuỗi 24-bits so với 12 bits của của VLAN ID. Do đó cung cấp hơn 16 triệu ID duy nhất.

- VXLAN Tunnel End Point (VTEP) dùng để kết nối switch (hiện tại là virtual switch) đến mạng IP. VTEP nằm trong hypervisor chứa VMs. Chức năng của VTEP là đóng gói VM traffic trong IP header để gửi qua mạng IP.

![Caption for the picture.](https://i.imgur.com/lwv3ZtM.png)

Như đề cập ở trên, mỗi VTEP có 2 interface, 1 interface đến Bridge Domain (trunk port) để truy cập (virtual switch), và 1 cái khác là IP interface đến IP network. VTEP được chỉ định 1 địa chỉ IP và hoạt động như 1 IP host đến mạng IP. Bridge Domain được liên kết với 1 nhóm IP multicast group. Có 2 loại truyền thông VM-to-VM.

- VM-to-VM communication : Unicast traffic VM trong server bên trái trong hình trên gửi traffic đến VM trong server bên phải. Dựa trên cấu hình trong Bridge Domain, VM traffic được chỉ định VNI. VTEP xác định xem đích của VM có trên cùng 1 segment hay không? VTEP đóng gói Ethernet frame với outer MAC header, outer IP header và VXLAN header. Gói tin đầy đủ được gửi đến mạng IP với địa chỉ IP đích của remote VTEP được kết nối với VM đích. Remote VTEP decapsulates packet và forward frame đến VM được kết nối. Remote VTEP cũng học địa chỉ inner Source MAC và địa chỉ outer Source IP .

- VM-to-VM communication : Broadcast và Unknown Unicast traffic VM trong server bên trái muốn truyền thông với VM trong server bên phải trong cùng 1 subnet. VM sử gửi gói tin ARP Broadcast, UDP header và VXLAN header. Tuy nhiên, gói tin này được gửi đến IP Multicast group mà liên kết với VXLAN ID. VTEP sẽ gửi đi gói tin IGMP Membership Report tới upstream router để kết nối/ rời IP multicast groups liên quan đến VXLAN. Remote VTEP là bộ thộ thu cho IP multicast groups đó, nhận được traffic từ VTEP nguồn. 1 lần nữa, nó tạo ra 1 mapping của địa chỉ inner Source MAC và địa chỉ outer Source IP, và chuyển tiếp lưu lượng truy cập đến VM đích. VM đích gửi ARP response tiêu chuẩn sử dụng IP unicast. VTEP đóng gói gói tin trở lại cho VTEP kết nối VM bằng cách sử dụng IP unicast VXLAN encapsulation.

**VXLAN Frame Format**

Một VXLAN header có 8bytes. Và nó chứa:

- Trường Flag ( 8 bit) - Cờ I được đặt thành 1 có nghĩa là Header có chứa a giá trị VXLAN ID hợp lệ. Các bit còn lại là reverse (R) và set về 0
- VXLAN Network ID-VNI ( 24 bit) - Chỉ định một mạng VXLAN mà các máy ảo có thể kết nối . Các máy ảo khác VXLAN không thể kết nối với nhau. 

![Caption for the picture.](https://i.imgur.com/CDFrkXm.png)

**VXLAN Encapsulation**

- Ngoài IP header và VXLAN header, VTEP cũng chèn thêm UDP header. Trong ECMP, switch/router bao gồm UDP header để thực hiện chức năng băm. VTEP tính source port bằng cách thực hiện băm inner Ethernet frame của header. Destination UDP port là VXLAN port.

- Outer IP header chứa địa chỉ Source IP của VTEP thực hiện việc encapsulation. Địa chỉ IP đích là địa chỉ IP remote VTEP hoặc địa chỉ IP Multicast group. VXLAN đôi khi còn được gọi là công nghệ MAC-in-IP-encapsulation.

![Caption for the picture.](https://i.imgur.com/z6EHLme.png)

VXLAN thêm 50 bytes. Để tránh phân mảnh và tái lắp ráp, tất cả các thiết bị mạng vật lý vẫn chuyển lưu lượng VXLAN phải chứa được gói tin này. Vì vậy, MTU cũng nên được điều chỉnh tương ứng.

**Cấu hình LAB**

**Mô hình Lab**

![Caption for the picture.](https://i.imgur.com/asFzNEg.png)

**Cấu hình trên Node1**

```sh
ip link add vxlan10 type vxlan id 80 remote 10.0.2.17 local 10.0.2.18 dev enp0s3  dstport 4789
ip link set vxlan10 up
ip addr add 20.0.0.2/24 dev vxlan10
```
Kiểm tra thông tin vxlan10 vừa tạo
```sh
root@node1:~# ip -d link show vxlan10
4: vxlan10: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/ether ea:b6:30:51:c9:a5 brd ff:ff:ff:ff:ff:ff promiscuity 0 
    vxlan id 80 remote 10.0.2.17 local 10.0.2.18 dev enp0s3 srcport 0 0 dstport 4789 ageing 300 addrgenmode eui64
```
**Cấu hình trên Node2**

```sh
ip link add vxlan10 type vxlan id 80 remote 10.0.2.18 local 10.0.2.17 dev enp0s3  dstport 4789
ip link set vxlan10 up
ip addr add 20.0.0.1/24 dev vxlan10
```
Kiểm tra thông tin vxlan10 vừa tạo
```sh
root@node2:~# ip -d link show vxlan10
4: vxlan10: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/ether 5a:4d:e5:58:b7:29 brd ff:ff:ff:ff:ff:ff promiscuity 0 
    vxlan id 80 remote 10.0.2.18 local 10.0.2.17 dev enp0s3 srcport 0 0 dstport 4789 ageing 300 addrgenmode eui64
```
**Thêm đường route cần thiết trên Node1**

Route dải Lan2: 192.168.57.0/24 via 20.0.0.1

```sh
root@node1:~# ip r
default via 10.0.2.2 dev enp0s3 onlink 
10.0.2.0/24 dev enp0s3  proto kernel  scope link  src 10.0.2.18 
20.0.0.0/24 dev vxlan10  proto kernel  scope link  src 20.0.0.2 
192.168.56.0/24 dev enp0s8  proto kernel  scope link  src 192.168.56.2 
192.168.57.0/24 via 20.0.0.1 dev vxlan10 
```
**Thêm đường route cần thiết trên Node2**

Route dải Lan1: 192.168.56.0/24 via 20.0.0.2
```sh
root@node2:~# ip r
default via 10.0.2.2 dev enp0s3 onlink 
10.0.2.0/24 dev enp0s3  proto kernel  scope link  src 10.0.2.17 
20.0.0.0/24 dev vxlan10  proto kernel  scope link  src 20.0.0.1 
192.168.56.0/24 via 20.0.0.2 dev vxlan10 
192.168.57.0/24 dev enp0s8  proto kernel  scope link  src 192.168.57.5 
```
**Kiểm tra kết nối từ COM1 đến COM2 bằng ICMP + SSH và ngược lại**

Ping từ com1 --> com2
```sh
root@com1:~# ping 192.168.57.4
PING 192.168.57.4 (192.168.57.4) 56(84) bytes of data.
64 bytes from 192.168.57.4: icmp_seq=1 ttl=62 time=0.656 ms
64 bytes from 192.168.57.4: icmp_seq=2 ttl=62 time=4.45 ms
64 bytes from 192.168.57.4: icmp_seq=3 ttl=62 time=0.869 ms
64 bytes from 192.168.57.4: icmp_seq=4 ttl=62 time=0.614 ms
64 bytes from 192.168.57.4: icmp_seq=5 ttl=62 time=1.43 ms
```

SSH từ com1 ---> com2

```sh
root@com1:~# ssh hannv@192.168.57.4
hannv@192.168.57.4's password: 
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.4.0-87-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

57 packages can be updated.
37 updates are security updates.


Last login: Fri Sep 22 14:35:15 2017 from 192.168.56.4
hannv@Com2:~$ ifconfig 
enp0s3    Link encap:Ethernet  HWaddr 08:00:27:e9:e4:d4  
          inet addr:192.168.57.4  Bcast:192.168.57.255  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:fee9:e4d4/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:2700 errors:0 dropped:0 overruns:0 frame:0
          TX packets:6520 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:243676 (243.6 KB)  TX bytes:1149999 (1.1 MB)
```

Ping từ com2 --> com1
```sh
root@Com2:~# ping 192.168.56.4
PING 192.168.56.4 (192.168.56.4) 56(84) bytes of data.
64 bytes from 192.168.56.4: icmp_seq=1 ttl=62 time=0.663 ms
64 bytes from 192.168.56.4: icmp_seq=2 ttl=62 time=0.628 ms
64 bytes from 192.168.56.4: icmp_seq=3 ttl=62 time=0.699 ms
64 bytes from 192.168.56.4: icmp_seq=4 ttl=62 time=0.708 ms
64 bytes from 192.168.56.4: icmp_seq=5 ttl=62 time=0.754 ms
```

SSH từ com2 ---> com1
```sh
root@Com2:~# ssh hannv@192.168.56.4
hannv@192.168.56.4's password: 
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.4.0-87-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

57 packages can be updated.
37 updates are security updates.


Last login: Fri Sep 22 14:34:47 2017 from 192.168.57.4
hannv@com1:~$ ifconfig 
enp0s3    Link encap:Ethernet  HWaddr 08:00:27:e7:de:03  
          inet addr:192.168.56.4  Bcast:192.168.56.255  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:fee7:de03/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:4403 errors:0 dropped:0 overruns:0 frame:0
          TX packets:8420 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:365927 (365.9 KB)  TX bytes:2120725 (2.1 MB)
```










