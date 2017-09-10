##Tìm hiểu về VXLAN

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

![Caption for the picture.](https://i.imgur.com/gtakrU6.png)


Trước tiên cần cài openvswitch lên các host
```sh
apt-get install openvswitch-switch
```
**Cấu hình trên Host1**

```sh
ovs-vsctl add-br br1
ovs-vsctl add-port br1 enp0s3
ip link set enp0s3 up
ip link set br1 up
```
Tạo tun9 với type=vxlan và VNI= 123

```sh
ovs-vsctl add-port br1 tun9 -- set interface tun9 type=vxlan options:remote_ip=192.168.58.3 options:key=123
```

Kiểm tra cấu hình


**Cấu hình trên Host2**

```sh
ovs-vsctl add-br br2
ovs-vsctl add-port br1 enp0s3
ip link set enp0s3 up
ip link set br2 up
```
Tạo tun9 với type-vxlan và VNI=123 trên Host2
```sh
ovs-vsctl add-port br2 tun9 -- set interface tun9 type=vxlan options:remote_ip=192.168.58.3 options:key=123
```
Kiểm tra kết nối

**Chú ý** 
Để kiểm tra một cách chính xác xem vxlan đã hoạt động ra sao, đóng gói như thế nào ta cần sử dụng Wireshark để chặn bắt gói tin và phân tích.

Ping từ Host1 tới Host2

```sh
root@ubuntu:~# ping 10.0.2.16
PING 10.0.2.16 (10.0.2.16) 56(84) bytes of data.
64 bytes from 10.0.2.16: icmp_seq=1 ttl=64 time=0.469 ms
64 bytes from 10.0.2.16: icmp_seq=2 ttl=64 time=1.18 ms
64 bytes from 10.0.2.16: icmp_seq=3 ttl=64 time=0.892 ms
64 bytes from 10.0.2.16: icmp_seq=4 ttl=64 time=1.25 ms
64 bytes from 10.0.2.16: icmp_seq=5 ttl=64 time=1.21 ms
```




















