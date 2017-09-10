##Tìm hiểu về giao thức DHCP



**DHCP-Dynamic host configuaration protocol**
Trong một hệ thống mạng ta có thể cấu hình IP cho các máy client theo 2 cách là cấu hình STATIC và sử dụng DHCP để cấp phát IP động. Với chế độ Dynamic một địa chỉ IP động là một địa chỉ sẽ thay đổi trong khoảng thời gian xác định. Người quản trị dùng dịch vụ DHCP để cấp phát địa chỉ IP động này cho các máy trạm trong mạng. Việc cấp phát IP động có những ưu điểm sau:
– Khắc phục được tình trạng đụng địa chỉ IP và giảm chi phí quản trị cho hệ thống mạng. 
– Giúp cho các nhà cung cấp dịch vụ (ISP) tiết kiệm được số lượng địa chỉ IP thật (public IP). 
– Phù hợp với các máy tính thường xuyên di chuyển qua lại giữa các mạng. 

**1) Cơ chế hoạt động**
Giao thức DHCP làm việc theo mô hình client/server. Quá trình tương tác giữa DHCP client và server diễn ra theo 4 bước sau đây : 
a. IP lease request 
b. IP lease offer 
c. IP lease selection 
d. IP lease acknowledgement

**a. IP Lease Request**


![Minion](https://i.imgur.com/KmfVEYP.png)

Đầu tiên, client sẽ broadcast một message tên là DHCPDISCOVER, vì client lúc này chưa có địa chỉ IP cho nên nó sẽ dùng một địa chỉ source(nguồn) là 0.0.0.0 và cũng vì client không biết địa chỉ của DHCP server nên nó sẽ gửi đến một địa chỉ broadcast là 255.255.255.255. Lúc này gói tin DHCPDISCOVER này sẽ broadcast lên toàn mạng. Gói tin này cũng chứa một địa chỉ MAC (Media Access Control – là địa chỉ mà mỗi một network adapter (card mạng) sẽ được nhà sản xuất cấp cho và là mã số để phân biệt các card mạng với nhau) và đồng thời nó cũng chứa computer name của máy client để DHCP server có thể biết được client nào đã gởi yêu cầu đến.
**b. IP Lease Offer**
Nếu có một DHCP hợp lệ (nghĩa là nó có thể cấp địa chỉ IP cho một client) nhận được gói tin DHCPDISCOVER của client thì nó sẽ trả lời lại bằng một gói tin DHCPOFFER, gói tin này đi kèm theo những thông tin sau: 
+ MAC address của client 
+ Một IP address cấp cho (offer IP address) 
+ Một subnet mask 
+ Thời gian thuê (mặc định là 8 ngày) 
+ Địa chỉ IP của DHCP cấp IP cho client này 
Lúc này DHCP server sẽ được giữ lại một IP đã offer (cấp) cho client để nó không cấp cho DHCP client nào khác. DHCP client chờ một vài giây cho một offer, nếu nó không nhận một offer nó sẽ rebroadcast (broadcast gói DHCPDISCOVER) trong khoảng thời gian là 2-, 4-, 8- và 16- giây, bao gồm một khoảng thời gian ngẫu nhiên từ 0 – 1000 mili giây. Nếu DHCP client không nhận một offer sau 4 lần yêu cầu, nó sử dụng một địa chỉ IP trong khoảng 169.254.0.1 đến 169.254.255.254 với subnet mask là 255.255.0.0 DHCP client tiếp tục cố gắng tìm kiếm một DHCP server sau mỗi 5 phút.
![Caption for the picture.](https://i.imgur.com/AP61wtG.png)

**c. IP Lease Selection**
DHCP client đã nhận được gói tin DHCPOFFER thì nó sẽ phản hồi broadcast lại một gói DHCPREQUEST để chấp nhận offer đó. DHCPREQUEST bao gồm thông tin về DHCP server cấp địa chỉ cho nó. Sau đó, tất cả DHCP server khác sẽ rút lại các offer (trường hợp này là trong mạng có nhiều hơn 1 DHCP server) và sẽ giữ lại IP address cho các yêu cầu xin IP address khác.

![Caption for the picture.](https://i.imgur.com/lkiekCv.png)

**d. IP Lease Acknowledgement**
DHCP server nhận được DHCPREQUEST sẽ gởi trả lại DHCP client một DHCPACK để cho biết là đã chấp nhận cho DHCP client sử dụng IP Address đó. Gói tin này bao gồm địa chỉ IP và các thông tin cấu hình khác (DNS server, WINS server… ). Khi DHCP client nhận được DHCPACK thì cũng có nghĩa là kết thúc quá trình xin cấp IP của mình. (Tất cả việc trao đổi thông tin giữa một DHCP server và DHCP client sẽ sử dụng UDP port là 67 và 68 (User Datagram Protocol).

![Caption for the picture.](https://i.imgur.com/85pxxIs.png)

**2) Cơ chế tự refresh lại thời gian đăng ký**
Bây giờ ta coi như là DHCP client đã đăng ký được một IP address rồi. Theo mặc định của DHCP server thì mỗi IP lease chỉ được có 8 ngày. Nếu theo như mặc định (8 ngày) thì một DHCP client sau một khoảng thời gian là 50% (tức là 4 ngày) nó sẽ tự động xin lại IP address với DHCP mà nó đã xin ban đầu.DHCP client lúc này sẽ gởi một DHCPREQUEST trực tiếp (unicast) đến DHCP server mà nó đã xin ban đầu.

![Caption for the picture.](https://i.imgur.com/DKum018.png)

Nếu mà DHCP server đó “còn sống”, nó sẽ trả lời bằng một gói DHCPACK để renew (cho thuê mới lại) tới DHCP client, gói này bao gồm thông các thông số cấu hình mới cập nhật nhất trên DHCP server. Nếu DHCP server “đã chết”, thì DHCP client này sẽ tiếp tục sử dụng cấu hình hiện thời của nó.

![Caption for the picture.](https://i.imgur.com/EZvAcAc.png)

Và nếu sau 87.5% (7 ngày) của thời gian thuê hiện thời của client, nó sẽ broadcast một DHCPDISCOVER để update địa chỉ IP của nó. Vào lúc này, nó không kiếm tới DHCP server ban đầu cho nó thuê nữa mà nó là sẽ chấp nhận bất cứ một DHCP server nào khác. Nếu thời gian đăng ký đã hết, thì client sẽ ngay lập tức dừng lại việc sử dụng IP address đã đăng ký đó. Và DHCP client sau đó sẽ bắt đầu tiến trình xin cấp một địa chỉ như ban đầu. Chú ý: khi bạn khởi động (restart) lại DHCP client thì nó sẽ tự động renew lại IP address mà trước khi nó shut down.

**3) DHCP Replay Agent**
DHCP Replay Agent là một máy tính hoặc một Router được cấu hình để lắng nghe và chuyển tiếp các gói tin giữa DHCP Client và DHCP Server từ subnet này sang subnet khác.

![Caption for the picture.](https://i.imgur.com/b5ZcRVu.png)

**Cơ chế hoạt động**

a. Client Broadcasts gói tin DHCP Discover trong nội bộ mạng.

![Caption for the picture.](https://i.imgur.com/J2cBYJz.png)

b. DHCP Relay Agent trên cùng mạng với Client sẽ nhận gói tin đó và chuyển đến DHCP server bằng tín hiệu Unicast.

![Caption for the picture.](https://i.imgur.com/eltPlVX.png)

c. DHCP server dùng tín hiệu Unicast gởi trả DHCP Relay Agent một gói DHCP Offer.

![Caption for the picture.](https://i.imgur.com/C5HzBHW.png)

d. DHCP Relay Agent Broadcasts gói tin DHCP Offer đó đến các Client

![Caption for the picture.](https://i.imgur.com/iZLWQqO.png)

e. Sau khi nhận được gói tin DHCP Offer, client Broadcasts tiếp gói tin DHCP Request.

![Caption for the picture.](https://i.imgur.com/t88ZV19.png)

f. DHCP Relay Agent nhận gói tin DHCP Request đó từ Client và chuyển đến DHCP server cũng bằng tín hiệu Unicast.

g. DHCP server dùng tín hiệu Unicast gởi trả lời cho DHCP Relay Agent một gói DHCP ACK.

h. Và cuối cùng DHCP Relay Agent Broadcasts gói tin DHCP ACK đến Client. Đến đây là hoàn tất quy trình tiếp nhận xử lý và chuyển tiếp thông tin của DHCP Relay Agent.

![Caption for the picture.](https://i.imgur.com/qOHAKaR.png)

**4) Mô hình lab và cấu hình ISC-DHCP**
**Mô hình lab** 

![Caption for the picture.](https://i.imgur.com/Z01XkFa.png)

- Mô hình gồm DHCP-Server chạy trên Ubuntu 16.04
- Client ubuntu 16.04 cấu hình dịch vụ dhcp để nhận IP động. 
- Client và Server cùng chung 1 dải mạng ( Chung Subnet Mask) để triển khai lab mô hình client server khác dải mạng cần deploy thêm DHCP-Agent.

**Cấu hình** 

Nếu có nhiều hơn 1 card mạng ta cần chỉ định card mạng cho DHCP Server, truy cập theo đường dẫn sau và thêm card mạng chỉ định cho DHCP Server.
```sh
vim /etc/default/isc-dhcp-server
```
Chỉ định card mạng cho DHCP như sau.
```sh
INTERFACES="enp0s3" 
```
Sau khi chỉ định card mạng cho DHCP ta tiến hành cấu hình DHCP Server như sau, truy cập theo đường dẫn 

```sh
vim /etc/dhcp/dhcpd.conf
```
Cấu hình domain-name server

```sh
option domain-name "example.org";
option domain-name-servers 10.0.2.15;
```
Cấu hình các thông số mạng cấp cho client 
```sh
subnet 10.0.2.0 netmask 255.255.255.0 {
  option routers 10.0.2.2;
  range dynamic-bootp 10.0.2.50 10.0.2.60;
  option subnet-mask 255.255.255.0;
}

```
Restart Service isc-dhcp
```sh
systemctl restart isc-dhcp-server
```
Check Status DHCP
```sh
systemctl status isc-dhcp-server
```
```sh
● isc-dhcp-server.service - ISC DHCP IPv4 server
   Loaded: loaded (/lib/systemd/system/isc-dhcp-server.service; enabled; vendor preset: enabled)
   Active: active (running) since T7 2017-09-09 10:38:33 ICT; 6s ago
     Docs: man:dhcpd(8)
 Main PID: 5296 (dhcpd)
    Tasks: 1
   Memory: 9.1M
      CPU: 29ms
   CGroup: /system.slice/isc-dhcp-server.service
           └─5296 dhcpd -user dhcpd -group dhcpd -f -4 -pf /run/dhcp-server/dhcpd.pid -cf /etc/dhcp/dhcpd.conf enp0s3
```
Kết quả trên máy DHCP client đã nhận IP động theo cấu hình của DHCP-Server 
![Caption for the picture.](https://i.imgur.com/e9Tp7Zd.png)