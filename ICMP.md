##Tìm hiểu về ICMP



**Giới thiệu**
IP không có cơ chế để biết được data nó gửi đã đến được đích chưa, nên mới sinh ra cái gọi là Internet Control Messenger Protocol (ICMP).ICMP messenger có nhiệm vụ đơn giản là thông báo cho sender biết việc gửi data đi có vấn đề gì.

Ví dụ: host A gửi 1 datagram tới host B, nhưng do một số nguyên nhân mà gói thông tin gửi tới không đúng đích.

- Các thiết bị trung gian như routing protocol không đúng, chúng được gọi là unreachable network.
- Cấu hình TCP/IP chưa đúng về địa chỉ, subnetmask hay default gateway, chúng được gọi là unreachable host.
- Host đích không hỗ trợ upper-layer protocol, được gọi là unreachable protocol.
- Host đích không hỗ trợ loại dịch vụ cần truy cập, gọi là unreachable port/socket


-Khi đó thiết bị trung gian (router) nơi xảy ra vấn đề sẽ gửi lại một gói tin trong đó có ICMP messenger chỉ dành cho sender để thông báo về nguyên nhân. Các thiết bị trung gian khác không nhận được messenger trên và hoàn toàn không biết là có vấn đề trên đường truyền.

– Đã là một messenger được truyền đi trong mạng thì nó cũng phải đi qua những lớp dưới. Và dữ liệu (nội dung chính của messenger đó) sẽ được encap cũng như các loại data khác ( nghĩa là cũng phải có Frame header, IP datagram header..) sau đó nó sẽ được chuyền đi.

– Để tránh trường hợp khi truyền ICMP messenger lại xảy ra lỗi và lại sinh ra một cái error report nữa và làm tăng (congestion – tắc nghẽn) ICMP sẽ không có error report nữa.

– Có nhiều loại ICMP messenger khác nhau và mỗi loại mang 1 thông điệp lỗi cụ thể khác nhau. Kiểu messenger được nhận ra nhờ format dữ liệu của messenger đó.

**Định dạng của gói tin ICMP**

Gói tin ICMP được mang trong phần dữ liệu của gói tin IP. Mặc dù mỗi gói tin ICMP có dạng riêng của nó, nhưng chúng đều bắt đầu với ba trường sau:

- TYPE (8bit): là một số nguyên 8bit để xác định thông điệp.
- CODE (8bit): cung cấp thêm thông tin về kiểu thông điệp.
- CHECKSUM(16bit) : ICMP sử dụng thuật checksum như IP, nhưng ICMP checksum chỉ tính đến thông điệp ICMP.

![Caption for the picture.](https://i.imgur.com/G3JZV5s.png)


Hơn nữa, các thông điệp ICMP thông báo lỗi luôn luôn bao gồm phần đầu và 64bit đầu tiên của packet gây nên lỗi. Lý do có thêm phần đầu này cùng với phần đầu packet là để cho phép nơi nhận xác định chính xác hơn những giao thức nào và chương trình ứng dụng có trách nhiệm đối với packet.

Trường TYPE của ICMP xác định ý nghĩa của thông điệp cũng như định dạng của nó.Các kiểu bao gồm:

– 0: Echo reply
– 3: Destination unreachable
– 4: Source quench
– 5: Redirect
– 8: Echo
– 9: Router advertisement
– 10: Router solicitation
– 11: Time exceeded
– 12: Parameter problem
– 13: Timestamp request
– 14: Timestamp reply
– 15: Information request (obsolete)
– 16: Information reply (obsolete)
– 17: Address mask request
– 18: Address mask reply
– 30: Traceroute
– 31: Datagram conversion error
– 32: Mobile host redirect
– 33: Ipv6 Where-Are-You
– 34: Ipv6 I-Am-Here
– 35: Mobile registration request
– 36: Mobile registration reply
– 37: Domain name request
– 38: Domain name reply
– 39: SKIP
– 40: Photuris

**Các loại ICMP messenger thường thấy**

![Caption for the picture.](https://i.imgur.com/UBKZwDX.png)

Bảng nhận dạng lỗi 

![Caption for the picture.](https://i.imgur.com/VFhhTe0.png)
