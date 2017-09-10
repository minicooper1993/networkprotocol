## Tìm hiểu về DNS


**1) Giới thiệu**

– Mỗi máy tính, thiết bị mạng tham gia vào mạng Internet đều giao tiếp với nhau bằng địa chỉ IP (Internet Protocol) . Để thuận tiện cho việc sử dụng và dễ nhớ ta dùng tên (domain name) để xác định thiết bị đó. Hệ thống tên miền (Domain Name System) được sử dụng để ánh xạ tên miền thành địa chỉ IP. Vì vậy, khi muốn liên hệ tới các máy, chúng chỉ cần sử dụng chuỗi ký tự dễ nhớ (domain name) như: www.microsoft.com, www.ibm.com…, thay vì sử dụng địa chỉ IP là một dãy số dài khó nhớ.
– Ban đầu, khi DNS chưa ra đời, người ta sử dụng một file tên Host.txt, file này sẽ lưu thông tin về tên host và địa chỉ của host của tất cả các máy trong mạng, file này được lưu ở tất cả các máy để chúng có thể truy xuất đến máy khác trong mạng. Khi đó, nếu có bất kỳ sự thay đổi về tên host, địa chỉ IP của host thì ta phải cập nhật lại toàn bộ các file Host.txt trên tất cả các máy. Do vậy đến năm 1984 Paul Mockpetris thuộc viện USC’s Information Sciences Institute phát triển một hệ thống quản lý tên miền mới lấy tên là Hệ thống tên miền – Domain Name
– Hệ thống tên miền này cũng sữ dụng một file tên host.txt, lưu thông tịn của tất cả các máy trong mạng, nhưng chỉ được đặt trên máy làm máy chủ tên miền (DNS). Khi đó, các Client trong mạng muốn truy xuất đến các Client khác, thì nó chỉ việc hỏi DNS.
– Như vậy, mục đích của DNS là :
+ Phân giải địa tên máy thành địa chỉ IP và ngược lại.
+ Phân giải tên domain.
– DNS là Domain Name System, dns là Domain Name Server chạy Domain Name Service.

**2) Cấu trúc của hệ thống tên miền ( DNS Namespace)**
– Hiện nay hệ thống tên miền được phân thành nhiêu cấp :
– Gốc (Domain root) : Nó là đỉnh của nhánh cây của tên miền. Nó có thể biểu diễn đơn giản chỉ là dấu chấm “.”
– Tên miền cấp một (Top-level-domain) : gồm vài kí tự xác định một nước, khu vưc hoặc tổ chức. Nó đươc thể hiện là " .com ” , “.edu” ….
– Tên miền cấp hai (Second-level-domain): Nó rất đa dạng rất đa dạng có thể là tên một công ty, một tổ chức hay một cá nhân.
– Tên miền cấp nhỏ hơn (Subdomain) : Chia thêm ra của tên miền cấp hai trở xuống thường được sử dụng như chi nhánh, phòng ban của một cơ quan hay chủ đề nào đó.
![Caption for the picture.](https://i.imgur.com/oQGKH4H.png)

**Phân loại tên miền**

– Com : Tên miền này được dùng cho các tổ chức thương mại.
– Edu : Tên miền này được dùng cho các cơ quan giáo dục, trường học.
– Net : Tên miền này được dùng cho các tổ chức mạng lớn.
– Gov : Tên miền này được dùng cho các tổ chức chính phủ.
– Org : Tên miền này được dùng cho các tổ chức khác.
– Int : Tên miền này dùng cho các tổ chức quốc tế.
– Info : Tên miền này dùng cho việc phục vụ thông tin.
– Arpa : Tên miền ngược.
– Mil : Tên miền dành cho các tổ chức quân sự, quốc phòng.
– Mã các nước trên thế giới tham gia vào mạng internet, các quốc gia này được qui định bằng hai chữ cái theo tiêu chuẩn ISO-3166 .Ví dụ : Việt Nam là .vn, Singapo là sg….

**3) DNS Server**

– Là một máy tính có nhiệm vụ là DNS Server, chạy dịch vụ DNS service.
– DNS Server là một cơ sở dữ liệu chứa các thông tin về vị trí của các DNS domain và phân giải các truy vấn xuất phát từ các Client.
– DNS Server có thể cung cấp các thông tin do Client yêu cầu, và chuyển đến một DNS Server khác để nhờ phân giải hộ trong trường hợp nó không thể trả lời được các truy vấn về những tên miền không thuộc quyền quản lý và cũng luôn sẵn sàng trả lời các máy chủ khác về các tên miền mà nó quản lý. DNS Server lưu thông tin của Zone, truy vấn và trả kết quả cho DNS Client.
– Máy chủ quản lý DNS cấp cao nhất là Root Server do tổ chức ICANN quản lý:
+ Là Server quản lý toàn bộ cấu trúc của hệ thống tên miền
+ Root Server không chứa dữ liệu thông tin về cấu trúc hệ thống DNS mà nó chỉ chuyển quyền (delegate) quản lý xuống cho các Server cấp thấp hơn và do đó Root Server có khả năng định đường đến của một domain tại bất kì đâu trên mạng

**Các loại truy vấn giữa các DNS Server**
Trước khi đi tìm hiểu về các loại DNS Server ta sẽ tìm hiểu về các loại truy vấn từ client tới DNS Server và giữa các DNS Server với nhau, có hai loại Truy vấn đó là  **Recursion Query** và   **Iteration Query **:

– Khi DNS Server không phân giải được host name, nó sẽ chuyển đến một DNS Server khác (forwarded) trong mạng. Quá trình này được gọi là kiểu yêu cầu Recursive ( phân giải đệ quy).

– Nếu Recursion bị disable thì nó sẽ sử dụng Iterative (tương tác), tức là nó sẽ gởi yêu cầu phân giải lại tên của host name. Khi có một truy vấn từ Client, trước hết nó sẽ tìm trong cơ sở dữ liệu của chính nó, nếu không có, nó sẽ cho biết một máy chủ khác mà từ đó có thể tìm thấy kết quả truy vấn.

– Nói cách khác, Recursion chỉ query trong local, còn Iterative có thể query ra ngoài internet. 

**Phân loại DNS Server**

**a) Primary Server**

– Được tạo khi ta add một Primary Zone mới thông qua New Zone Wizard.
– Thông tin về tên miền do nó quản lý được lưu trữ tại đây và sau đó có thể được chuyển sang cho các Secondary Server.
– Các tên miền do Primary Server quản lý thì được tạo và sửa đổi tai Primary Server và được cập nhật đến các Secondary Server.

**b) Secondary Server**

– DNS được khuyến nghị nên sử dụng ít nhất là hai DNS Server để lưu cho mỗi một Zone. Primary DNS Server quản lý các Zone và Secondary Server sử dụng để lưu trữ dự phòng cho Primary Server. Secondary DNS Server được khuyến nghị dùng nhưng không nhất thiết phải có.
– Secondary Server được phép quản lý domain những dữ liệu về tên miền (domain), nhưng Secondary Server không tạo ra các bản ghi về tên miền (domain) mà nó lấy về từ Primary Server.
– Khi lượng truy vấn Zone tăng cao tại Primary Server thì nó sẽ chuyển bớt tải sang cho Secondary Server .Hoặc khi Primary Server gặp sự cố không hoạt động được thì Secondary Server sẽ hoạt động thay thế cho đến khi Primary Server hoạt động trở lại.
– Primary Server thường xuyên thay đổi hoặc thêm vào các Zone mới. Nên DNS Server sử dụng cơ chế cho phép Secondary lấy thông tin từ Primary Server và lưu trữ nó. Có hai giải pháp lấy thông tin về các Zone mới là lấy toàn bộ (full) hoặc chỉ lấy phần thay đổi (incremental).

**c) Caching-only Server**

– Tất cả các DNS Server đều có khả năng lưu trữ dữ liệu trên bộ nhớ cache của máy để trả lời truy vấn một cách nhanh chóng. Nhưng hê thống DNS còn có một loại Caching-only Server.
– Loại này chỉ sử dụng cho việc truy vấn, lưu giữ câu trả lời dựa trên thông tin có trên cache của máy và cho kết quả truy vấn. Chúng không hề quản lý một domain nào và thông tin mà nó chỉ giới hạn những gì được lưu trên cache của Server.
– Lúc ban đầu khi Server bắt đầu chạy thì nó không lưu thông tin nào trong cache. Thông tin sẽ được cập nhật theo thời gian khi các Client Server truy vấn dịch vụ DNS. Nếu sử dụng kết nối mạng WAN tốc độ thấp thì việc sử dụng caching-only DNS Server là giải pháp hữu hiệu cho phép giảm lưu lượng thông tin truy vấn trên đường truyền.
– Caching-only có khả năng trả lời các câu truy vấn đến Client. Nhưng không chứa Zone nào và cũng không có quyền quản lý bất kì domain nào. Nó sử dụng bộ cache của mình để lưu các truy vấn của DNS của Client. Thông tin sẽ được lưu trong cache để trả lời các truy vấn đến Client.

**Cơ chế đồng bộ dữ liệu giữa các DNS Server**

Như đã trình bày ở trên khi triển khai 1 Primary DNS Server thì khuyến nghị  cần thiết lập thêm một Secondary DNS Server để phục vụ lưu trữ dự phòng và load balancing cho Primary DNS Server vậy phải có cơ chế để đồng bộ dữ liệu giữa hai loại DNS Server này và khi nào thì Secondary Server sẽ được đồng bộ với Primary Server?
– Với trao đổi IXFR (Incremental Zone transfer Request ) Zone thì sự khác nhau giữa số serial của nguồn dữ liệu và bản sao của nó. Nếu cả hai đều có cùng số serial thì việc truyền dữ liệu của Zone sẽ không thực hiên.
– Nếu số serial cho dữ liệu nguồn lớn hơn số serial của Secondary Server thì nó sẽ thực hiện gửi những thay đổi của bản ghi nguồn (Resource record – RR) của Zone ở Primary Server.
– Để truy vấn IXFR thực hiên thành công và các thay đổi được gửi thì tai DNS Server nguồn của Zone phải được lưu giữ các phần thay đổi để truyền đến nơi yêu cầu của truy vấn IXFR. Incremental sẽ cho phép lưu lượng truyền dữ liệu it và thực hiện nhanh hơn.
– Zone transfer sẽ xảy ra khi có những hành động sau xảy ra:
+ Khi quá trình làm mới của Zone đã kết thúc (refresh exprire).
+ Khi Secondary Server được thông báo Zone đã thay đổi tại nguồn quản lý Zone.
+ Khi thêm mới Secondary Server.
+ Tại Secondary Server yêu cầu đồng bộ dữ liệu.
– Các bước yêu cầu chuyển dữ liệu từ Secondary Server đến Primary Zone để yêu cầu lấy dữ liệu về Zone mà nó quản lý :
+ Khi cấu hình có DNS Server mới, thì nó sẽ gửi truy vấn yêu cầu gửi toàn bộ Zone ( All Zone transfer request (AXFR) ) đến DNS Server chính quản lý dữ liệu của Zone.
+ Primary DNS Server quản lý dữ liệu của Zone trả lời và chuyển toàn bộ dữ liệu về Zone cho Secondary Server (destination) mới cấu hình..
+ Để xác định có chuyển dữ liệu hay không thì nó dựa vào số serial được khai báo bằng bản ghi SOA.
+ Khi thời gian làm mới (refresh interval ) của Zone đã hết, thì Secondary DNS Server sẽ truy vấn yêu cầu làm mới Zone tới Primary DNS Server chứa dữ liêu Zone.
+ Primary DNS Server quản lý dữ liệu sẽ trả lời truy vấn và gửi lại dữ liệu. Trả lời truy vấn dữ liệu gồm số serial của Zone tại Primary DNS Server.
+ Secondary DNS Server về Zone và sẽ kiểm tra số serial trong trả lời và quyết định xem có cần truyền dữ liêu không :
o Nếu giá trị của số serial của Primary Server bằng với số serial lưu tại nó thì sẽ kết thúc luôn. Và nó sẽ thiết lập lại với các thông số cũ lưu trong máy.
o Nếu giá trị của số serial tại Primary Server lớn hơn giá trị serial hiện tại Secondary DNS Server. Thì nó kết luận Zone cần được cập nhật và cần đồng bộ dữ liệu giữa hai DNS Server
+ Nếu Secondary DNS Server nhận kết luận rằng Zone cần phải lấy dữ liệu thì nó sẽ gửi yêu cầu IXFR tới Primary DNS Server để yêu cầu truyền dữ liệu của Zone.
+ Primary DNS Server sẽ trả lời với việc gửi những thay đổi của Zone hoặc toàn bộ Zone :
o Nếu Primary DNS Server có hỗ trợ việc gửi những thay đổi của Zone thì nó sẽ gửi những phần thay đổi của nó (Incremental Zone transfer of the Zone).
o Nếu Primary DNS Server không hỗ trợ thì nó sẽ gửi toàn bộ Zone (Full AXFR transfer of the Zone).

Cấu hình bản ghi SOA có những thông số điểu khiển quá trình đồng bộ giữa Primary Server và Secondary Server 

```sh
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     test.vcc.hannv.com. root.vcc.hannv.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
; name servers - NS records
     IN      NS      test.vcc.hannv.com.
; name servers - A records
test.vcc.hannv.com.          IN      A       10.0.2.15

```
– Serial number : là giá trị được áp dụng cho tất cả dữ liệu trong một Zone. Khi Secondary DNS kết nối Primary DNS để thực hiện chuyển dữ liệu về, Secondary DNS sẽ hỏi giá trị serial number của Primary DNS. Nếu giá trị trên Primary DNS lớn hơn Secondary DNS thì quá trình chuyển dữ liệu mới được bắt đầu.
– Refresh interval : thời gian cập nhật các record.
– Retry interval : trong trường hợp Secondary DNS không thể kết nối với Primary DNS, thì Secondary DNS sẽ thực hiện kết nối lại sau khoảng thời gian Retry interval.
– Expires after : đây là khoảng thời gian mà Secondary DNS không thể kết nối với Primary DNS, dữ liệu trên Secondary DNS sẽ bị hủy.
– Minimum (default) TTL : đây là giá trị áp dụng cho tất cả các bản ghi trong file dữ liệu, tham số này xác định dữ liệu sẽ được cache tại Primary DNS trong bao lâu.
– TTL ( time to live) : Primary DNS không thể caching dữ liệu mãi mãi, khi dữ liệu về tên miền thay đổi, và sự thay đổi này sẽ không được cập nhật. Để tránh điều này, người ta đưa ra TTL. Đây là khoảng thời gian Primary DNS đươc caching dữ liệu. Khi hết khoảng thời gian này, dữ liệu được caching sẽ bị xóa.

**4) DNS Zone**
– DNS Zone là tập hợp các ánh xạ từ host đến địa chỉ IP và từ IP đến host của một phần liên tục trong một nhánh của domain..
– Thông tin của DNS Zones là những record gồm tên Host và địa chỉ IP được lưu trong DNS Server, DNS Server quản lý và trả lời những yêu cầu từ Client liên quan đến DNS Zones này.
– Hệ thống tên miền (DNS) cho phép phân chia tên miền để quản lý và nó chia hệ thống tên miền thành Zone và trong Zone quản lý tên miền được phân chia đó.Các Zone chứa thông tin vê miền cấp thấp hơn, có khả năng chia thành các Zone cấp thấp hơn và phân quyền cho các DNS Server khác quản lý.
– Zone file : Lưu thông tin của Zone, có thể ở dạng text hoặc trong Active Dicrectory.
– Có 2 loại DNS Zone : Standard Primary Zone và Active Directory Integrated Zones.

**Resource Record**


| Record Type | Mục đích |
| ------ | ------ |
| A | Host – Phân giải tên máy thành địa chỉ IP (IPv4) |
| MX | Mail exchange – Chỉ đến mail Server trong domain. |
| CNAME (Alias)| Canonical name – Cho phép một host có thể có nhiều tên.     |
| NS| Name Server – Chứa địa chỉ IP của DNS Server cùng với các thông tin về domain đó. |
| SOA | Start of Authority – Bao gồm các thông tin về domain trên DNS Server. |
| SRV| Service – Được sử dụng bởi Active Directory để lưu thông tin về vị trí của Domain Controllers |
| AAAA| Host – Phân giải tên máy thành địa chỉ IP (IPv6) |
| PTR| Pointer – Phân giải địa chỉ IP thành tên máy. |

**5) DNS Resolvers**
– Là dịch vụ sử dụng để truy vấn thông tin từ DNS Server của Client. Qui trình phân giải tên miền được tiến hành như sau :

**Liên hệ thực tế:** Quá trình máy tính cá nhân (gọi tắt là A) truy vấn tới địa chỉ www.vccloud.vn. Lúc này máy tính đang trỏ DNS tới DNS google 8.8.8.8 quá trình sẽ diễn ra như sau:
- Đầu tiên A gửi request hỏi DNS Server google hỏi thông tin về www.vccloud.vn, server DNS google sẽ gửi truy vấn đến server top level domain 
- Top level domain lưu trữ thông tin về mọi tên miền trên mạng. Do đó nó sẽ gửi lại cho server DNS google địa chỉ IP của server quản lý tên miền vn (gọi tắt là server vn).
- Khi có địa chỉ IP của server vn thì lúc này server DNS google sẽ hỏi server vn về vccloud.vn server vn quản lý toàn bộ những trang web có domain vn, chúng sẽ gửi địa chỉ ip của server vccloud.vn cho server google.
- Sau đó server DNS google lại tiếp tục gửi truy vấn đến server vccloud.vn để hỏi thông tin về server quản lý dịch vụ www của vccloud.vn.
- Server vccloud.vn khi nhận được truy vấn sẽ gửi lại IP của server www.vccloud.vn cho server DNS google
- Và cuối cùng server DNS google sẽ gửi lại địa chỉ địa chỉ IP của server www.vccloud.vn cho A và bây giờ A có thể kết nối trực tiếp tới www.vccloud.vn.

**6) Forwarder**
– Cũng giống như Root Hint, forwarder cũng trỏ đến DNS Server khác, khi nó không thể phân giải được các truy vấn do Client gởi đến.
– Một số DNS Server nội bộ không cho truy cập đến Internet vì mục đích bảo mật, nên DNS Server không thể truy vấn đến Root Server bằng Root Hint, vì thế ta phải sử dụng Forwarder, để chuyển các truy vấn của Client đến DNS Server được chỉ định.


**7) Cấu hình DNS Server Bin9**

**Mô hình Lab**

![Caption for the picture.](https://i.imgur.com/wWgrPNc.png)

- Mô hình triển khai với máy client Ubuntu 16.04, DNS Server cài trên Ubuntu 16.04 
- Mô hình chỉ gồm 1 Primary DNS Server 
- Có hai Zone: Zone forward phân giải từ tên miền ra IP và Zone Reverse để dịch từ IP sang tên miền 

**Cấu hình**

Trước tiên cần cài đặt Bind9
 
```sh
apt-get install bind9 bind9utils 
```
**Cấu hình Primary DNS Server**

a) Cấu hình Options File

```sh
vim /etc/bind/named.conf.options
```
Cấu hình ACL cho phép client có thể truy vấn đến DNS Server 

```sh
acl "trusted" {
        10.0.2.15;
        10.0.2.16;
};
```
cấu hình phần options 


```sh
 options {
        directory "/var/cache/bind";

        dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };

        recursion yes;                 # enables resursive queries
        allow-recursion { trusted; };  # allows recursive queries from "trusted" clients
        listen-on { 10.0.2.15; };   # ns1 private IP address - listen on private network only
        allow-transfer { none; };      # disable zone transfers by default
    
        forwarders {
                8.8.8.8;
                8.8.4.4;
        };
  };

```

b) Cấu hình Local Files

```sh
vim /etc/bind/named.conf.local
``` 
Cấu hình Zone Forward ( phân giải từ tên miền sang địa chỉ IP)

```sh
zone "vcc.hannv.com" {
    type master;
    file "/etc/bind/zones/db.vcc.hannv.com";
};
``` 
Cấu hình Zone Reverse ( Phân giải từ IP sang tên miền)

```sh
zone "2.0.10.in-addr.arpa" {
    type master;
    file "/etc/bind/zones/db.10.0.2";
};
```
c) Tạo và cấu hình Forward Zone file 
Trước tiên cần tạo thư mục zones

```sh
sudo mkdir /etc/bind/zones
```
Sau đó tạo forward zone file
```sh
vim /etc/bind/zones/db.vcc.hannv.com
``` 
Và cấu hình như sau

```sh
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     test.vcc.hannv.com. root.vcc.hannv.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
; name servers - NS records
     IN      NS      test.vcc.hannv.com.
; name servers - A records
test.vcc.hannv.com.          IN      A       10.0.2.15
```

Tạo Reverse Zone file 

```sh
vim /etc/bind/zones/db.10.0.2
```
Cấu hình reverse zone file

```sh
;
; BIND reverse data file for local loopback interface
;
$TTL    604800
@       IN      SOA     vcc.hannv.com. root.vcc.hannv.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
; name servers
      IN     NS      test.vcc.hannv.com.
; PTR Records
15     IN      PTR    test.vcc.hannv.com.
```

Sau khi cấu hình xong Restart lại service bind9

```sh
systemctl restart bind9
```

Kết quả 

Tại máy client địa chỉ ip 10.0.2.16 nslookup kiểm tra 

```sh
root@ubuntu:~# nslookup 
> test.vcc.hannv.com
Server:         10.0.2.15
Address:        10.0.2.15#53

Name:   test.vcc.hannv.com
Address: 10.0.2.15
> 10.0.2.15
Server:         10.0.2.15
Address:        10.0.2.15#53

15.2.0.10.in-addr.arpa  name = test.vcc.hannv.com.
> 
```