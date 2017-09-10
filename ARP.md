
##Tìm hiểu về ARP



**1) Giới thiệu về giao thức ARP**

Trong một hệ thống mạng máy tính, có 2 địa chỉ được gán cho máy tính là:

Địa chỉ logic: là địa chỉ của các giao thức mạng như IP, IPX, ... Loại địa chỉ này chỉ mang tính chất tương đói, có thể thay đổi theo sự cần thiết của người dùng. Các địa chỉ này thường được phân thành 2 phần riêng biệt là phần địa chỉ mạng và phần địa chỉ máy. Cách đánh địa chỉ như vậy nhắm giúp cho việc tìm ra các đường kết nối từ hệ thống mạng này sang hệ thống mạng khác dễ dàng hơn.

Địa chỉ vật lý: hay còn gọi là địa chỉ MAC - Medium Access Control address là địa chỉ 48 bit, dùng để định danh duy nhất do nhà cung cấp gán cho mỗi thiết bị. Đây là loại địa chỉ phẳng, không phân lớp, nên rất khó dùng để định tuyến.

Trên thực tế, các card mạng (NIC) chỉ có thể kết nối với nhau theo địa chỉ MAC, địa chỉ cố định và duy nhất của phần cứng.

=> Do vậy phải có một cơ chế để ánh xạ địa chỉ logic - lớp 3 sang địa chỉ vật lý - lớp 2 để các thiết bị có thể giao tiếp với nhau.

– ARP là phương thức phân giải địa chỉ động giữa địa chỉ lớp network và địa chỉ lớp datalink. Quá trình thực hiện bằng cách: một thiết bị IP trong mạng gửi một gói tin local broadcast đến toàn mạng yêu cầu thiết bị khác gửi trả lại địa chỉ phần cứng ( địa chỉ lớp datalink ) hay còn gọi là Mac Address của mình.

**2) Cấu trúc bản tin ARP**

Kích thước bản tin ARP là 28 byte, được đóng gói trong frame Ethernet II nên trong mô hình OSI, ARP được coi như là giao thức lớp 3 cấp thấp.

Cấu trúc bản tin ARP được mô tả như hình sau:

![Caption for the picture.](https://i.imgur.com/Jjvti2f.png)

- **Hardware type:**

Xác định kiểu bộ giao tiếp phần cứng cần biết.

Xác định với kiểu Ethernet giá trị 1.

- **Protocol type:**

Xác định kiểu giao thức cấp cao (layer 3) máy gửi sử dụng để giao tiếp.

Giao thức dành cho IP có giá trị 0x0800.

- **Hardware address length:** Xác định độ dài địa chỉ vật lý (tính theo đơn vị byte). Địa chỉ MAC nên giá trị của nó sẽ là 6.

- **Protocol address length:** Xác định độ dài địa chỉ logic được sử dụng ở tầng trên (layer 3). Tùy thuộc vào IP sử dụng mà có giá trị khác nhau, hiện tại IPv4 được sử dụng rộng rãi nên trường này sẽ có giá trị là 4 (byte).

- **Operation code:** Xác định loại bản tin ARP mà máy gửi gửi. Có một số giá trị phổ biến:

1 : bản tin ARP request.

2 : bản tin ARP rely.

3 : bản tin RARP request.

4 : bản tin RARP reply.

- **Sender hardware address (SHA):** Xác định địa chỉ MAC máy gửi.

Trong bản tin ARP request: trường này xác định địa chỉ MAC của host gửi request.

Trong bản tin ARP reply: trường này xác định địa chỉ MAC của máy host mà máy gửi bên trên muốn tìm kiếm.

- **Sender protocol address (SPA):** Xác định địa chỉ IP máy gửi.

- **Target hardware address (THA):** Xác định địa chỉ MAC máy nhận mà máy gửi cần tìm.

Trong bản tin ARP request: Trường này chưa được xác định (nên sẽ để giá trị là: 00:00:00:00:00:00)

Trong bản tin ARP reply: Trường này sẽ điền địa chỉ của máy gửi bản tin ARP request.

- **Target protocol address (PTA):** Xác định địa chỉ IP máy gửi (máy cần tìm).


**3) Cách thức hoạt động của ARP**

**a) Hoạt động trong mạng LAN**

![Caption for the picture.](https://i.imgur.com/6PHxonK.png)

Bước 1: Máy gửi kiểm tra cache của mình. Nếu đã có thông tin về sự ánh xạ giữa địa chỉ IP và địa chỉ MAC thì chuyển sang Bước 7.

Bước 2: Máy gửi khởi tạo gói tin ARP request với địa chỉ SHA và SPA là địa chỉ của nó, và địa chỉ TPA là địa chỉ IP của máy cần biết MAC. (Trường THA để giá trị toàn 0 để biểu hiện là chưa tìm được địa chỉ MAC)

Bước 3: Gửi quảng bá gói tin ARP trên toàn mạng (Địa chỉ MAC đích của gói tin Ethernet II là địa chỉ MAC quảng bá ff:ff:ff:ff:ff:ff).

Bước 4: Các thiết bị trong mạng đều nhận được gói tin ARP request. Gói tin được xử lý bằng cách các thiết bị đều nhìn vào trường địa chỉ Target Protocol Address.

Các thiết bị không trùng địa chỉ TPA thì hủy gói tin.

Thiết bị với IP trùng với IP trong trường Target Protocol Address sẽ bắt đầu quá trình khởi tạo gói tin ARP Reply bằng cách lấy các trường Sender Hardware Address và Sender Protocol Address trong gói tin ARP nhận được đưa vào làm Target trong gói tin gửi đi. Đồng thời thiết bị sẽ lấy địa chỉ MAC của mình để đưa vào trường Sender Hardware Address. Đồng thời cập nhất giá trị ánh xạ địa chỉ IP và MAC của máy gửi vào bảng ARP cache của mình để giảm thời gian xử lý cho các lần sau.

Bước 5: Thiết bị đích bắt đầu gửi gói tin Reply đã được khởi tạo đến thiết bị nguồn vừa gửi bản tin ARP request. Gói tin reply là gói tin gửi unicast.

Bước 6: Thiết bị nguồn nhận được gói tin reply và xử lý bằng cách lưu trường Sender Hardware Address trong gói reply như địa chỉ phần cứng của thiết bị đích cần tìm.

Bước 7: Thiết bị nguồn update vào ARP cache của mình giá trị tương ứng giữa địa chỉ IP và địa chỉ MAC của thiết bị đích. Lần sau sẽ không còn cần tới ARP request.

**b) Hoạt động trong môi trường liên mạng**

Hoạt động của ARP trong một môi trường phức tạp hơn đó là hai hệ thống mạng gắn với nhau thông qua một Router.

- Máy A thuộc mạng A muốn gửi gói tin tới máy B thuộc mạng B. 2 mạng này kết nối với nhau thông qua router C.

- Do các broadcast lớp MAC không thể truyền qua Router nên khi đó máy A sẽ xem Router C như một cầu nối hay một trung gian (Agent) để truyền dữ liệu. Trước đó, máy A sẽ biết được địa chỉ IP của Router C (địa chỉ Gateway) và biết được rằng để truyền gói tin tới B phải đi qua C.

- Để tới được router C thì máy A phải gửi gói tin tới port X của router C (là gateway trong LAN A). Quy trình truyền dữ liệu được mô tả như sau:

1) Máy A gửi ARP request để tìm MAC của port X.

2) Router C trả lời, cung cấp cho A địa chỉ MAC của port X.

3) Máy A truyền gói tin tới port X của router C (với địa chỉ MAC đích là MAC của port X, IP đích là IP máy B).

4) Router C nhận được gói tin của A, forward ra port Y. Trong gói tin có chứa địa chỉ IP máy B, router C sẽ gửi ARP request để tìm MAC của máy B.

5) Máy B sẽ trả lời router C MAC của mình, sau đó router sẽ gửi gói tin của A tới B.

![Caption for the picture.](https://i.imgur.com/giJFM9d.png)
Trên thực tế ngoài dạng bảng định tuyến người ta còn dùng phương pháp **proxy ARP**, trong đó có một thiết bị đảm nhận nhiệm vụ phân giải địa chỉ cho tất cả các thiết bị khác. Theo đó các máy trạm không cần giữ bảng định tuyến nữa Router C sẽ có nhiệm vụ thực hiện, trả lời tất cả các ARP request của tất cả các máy.

**4) Proxy ARP và ARP Caching**

**Proxy ARP**
ARP được thiết kế cho các thiết bị nằm trong nội mạng, có tính chất local. Tuy nhiên nếu hai thiết bị A và B bị chia cắt bởi 1 router thì chúng sẽ được coi như là không local với nhau nữa. Khi A muốn gửi thông tin đến B, A sẽ không gửi trực tiếp được đến B theo địa chỉ lớp hai, mà phải gửi qua router và được coi là cách nhau 1 hop ở lớp ba.

- Công nghệ này nhắm đáp ứng cho việc gửi bản tin trong môi trường liên mạng. Router nằm giữa 2 mạng local sẽ được cấu hình để đáp ứng các gói tin broadcast gửi từ A thay cho B.

- Công nghệ này được mô tả như ở trong phần mô tả hoạt động của ARP trong môi trường liên mạng

**ARP Caching**

ARP là một giao thức phân giải địa chỉ động. Quá trình gửi gói tin Request và Reply sẽ tiêu tốn băng thông mạng. Chính vì vậy càng hạn chế tối đa việc gửi gói tin Request và Reply sẽ càng góp phần làm tăng khả năng họat động của mạng. Vì vậy cần có cơ chế ARP Caching.

Ngoài việc làm giảm lưu lượng mạng, ARP cache cũng đảm bảo độ phân giải các địa chỉ thường dùng là nhanh chóng, đảm bảo hiệu suất hoạt động tổng thể của mạng.
