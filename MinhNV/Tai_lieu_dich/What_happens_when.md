# What happens when...
Trong bài viết này tác giả giải thích về "Điều gì sẽ xảy ra khi bạn gõ google.com vào ô tìm kiếm ở trình duyệt của bạn và nhấn phím Enter "

## Mục lục: 

- [Khi nhấn phím "g"](#1)
- [Phím "enter" được ấn xuống](#2)
- [Ngắt (không dành cho bàn phím USB)](#3)
- [(Windows) Một thông điệp `WM_KEYDOWN` được gửi tới ứng dụng ](#4)
- [(Mac OS X) Một KeyDown NSEvent được gửi đến ứng dụng](#5)
- [(GNU/Linux)  Xorg server lắng nghe mã phím ](#6)
- [Phân tích cú pháp URL](#7)
- [Đó có phải là một URL hay một cụm từ tìm kiếm?](#8)
- [Chuyển đổi ký tự Unicode (không phải ASCII) trong tên máy chủ ](#9)
- [Kiểm tra danh sách HSTS](#10)
- [DNS lookup ](#11)
- [ARP process](#12)
- [Khởi tạo một socket ](#13)
- [TLS handshake](#14)
- [HTTP protocol](#15)
- [Xử lý yêu cầu HTTP Server ](#16)
- [Phía sau trình duyệt](#17)
- [Trình duyệt](#18)
- [HTML parsing](#19)
- [Thuật toán phân tích ](#20)
- [CSS interpretation](#21)
- [Page Rendering](#22)
- [GPU Rendering](#23)
- [Window Server](#24)
- [Thực thi Post-rendering và user-induced](#25)

<a name="1"></a>
### Phím 'g' được nhấn 

Các phần sau giải thích tất cả về bàn phím vật lý và gián đoạn hệ điều hành. Nhưng rất nhiều điều xảy ra sau đó mà không giải thích được. Khi bạn chỉ cần bấm "g" trình duyệt nhận được sự kiện và toàn bộ máy tự động hoàn thành kicks into high gear. Tùy thuộc vào thuật toán của trình duyệt của bạn và nếu bạn ở chế độ riêng tư / ẩn danh hoặc không phải các đề xuất khác nhau sẽ được trình bày cho bạn trong hộp chứa dưới thanh URL. Hầu hết các thuật toán này ưu tiên các kết quả dựa trên lịch sử tìm kiếm và dấu trang. Bạn sẽ nhập "google.com" vì vậy không có vấn đề gì xảy ra, nhưng rất nhiều code sẽ chạy trước khi bạn đến đó và các đề xuất sẽ được chọn lọc với mỗi lần nhấn phím. Nó thậm chí có thể đề xuất "google.com" trước khi bạn gõ.

<a name="2"></a>
### Phím 'Enter' bên dưới

Để chọn một điểm zero hãy chọn nút Enter trên bàn phím nhấn ở cuối dải ô của nó. Tại thời điểm này, một mạch điện cụ thể cho phím enter được đóng lại. Điều này cho phép một lượng nhỏ dòng điện chảy vào mạch logic của bàn phím, quét trạng thái của mỗi khoá chuyển đổi, giảm tiếng ồn điện của sự đóng cửa liên tục của chuyển đổi, và chuyển nó thành số nguyên mã phím.
Bộ điều khiển bàn phím sau đó mã hóa mã khóa để chuyển đến máy tính. 

Trong trường hợp bàn phím USB 

- Các mạch của bàn phím USB được cấp nguồn bởi nguồn 5V từ được cung cấp qua pin của máy tính 
- Mã khoá sinh ra được lưu trữ bởi bộ nhớ mạch trong bàn phím trong một thanh ghi được gọi là "endpoint".
- Bộ điều khiển USB cứ sau khoảng 10ms (giá trị nhỏ nhất được khai báo bởi bàn phím) sẽ kiểm tra "endpoint" để lấy mã phím được lưu trữ.
- Giá trị này chuyển tới USB SIE (Serial Interface Engine) được chuyển đổi thành một hoặc nhiều gói dữ liệu USB theo giao thức USB cấp thấp.
- Các gói tin này được truyền từ bàn phím đến máy tính với tốc độ lên tới 1,5 Mb/s thông qua D+ hoặc D-pins (hai chân ở giữa). Tốc độ bị giới hạn là do HID (Human Interface Device) luôn được khai báo là "thiết bị tốc độ thấp" - "tuân thủ USB 2.0" 
- Tín hiệu nối tiếp này được giải mã tại bộ USB controller của máy tính, và được thông dịch bởi HID (Human Interface Device) của máy tính. Giá trị của phím sau đó được chuyển vào lớp trừu tượng phần cứng của hệ điều hành.

Trường hợp bàn phím ảo (như màn hình cảm ứng)

- Khi người dùng đặt ngón tay lên màn hình cảm ứng, một lượng nhỏ dòng điện được chuyển sang ngón tay. 
Mạch điện hoàn thành thông qua các trường tĩnh điện của lớp dẫn điện và tạo ra một điện áp tại thời điểm đó trên màn hình. 
Sau đó, `screen controller` gây ra một ngắt, báo cáo vị trí nhấp chuột.
- Sau đó, hệ điều hành di động thông báo cho ứng dụng thực thi một sự kiện ở vị trí đó (mà trường hợp này là các nút bàn phím ảo).
- Bàn phím ảo bây giờ có thể kích hoạt phần mềm để gửi thông điệp `key pressed` trở lại cho hệ điều hành.
- Thông báo này được trả lại để thông báo cho ứng dụng đang hoạt động có sự kiện `key pressed`

<a name="3"></a>
### Ngắt (không dành cho bàn phím USB)

Bàn phím gửi một tín hiệu trên đường dây yêu cầu gián đoạn (IRQ), và tín hiệu được ánh xạ bởi bộ điều khiển gián đoạn tới một vector ngắt (số nguyên).
CPU sử dụng Bảng mô tả ngắt (Interrupt Descriptor Table - IDT) để lập bản đồ các vector gián đoạn tới các hàm (xử lý gián đoạn) do kernel cung cấp. 
Khi một ngắt xuất hiện, CPU đánh chỉ mục các IDT với vector ngắt và chạy trình xử lý thích hợp. Do đó kernel được nhập vào.

<a name="4"></a>
### (Windows) Một thông điệp `WM_KEYDOWN` được gửi tới ứng dụng 

HID được vận chuyển qua sự kiện ấn phím đến trình điều khiển `KBDHID.sys`, nó sẽ chuyển đổi HID thành scancode. 
Trong trường hợp này, mã quét là `VK_RETURN` (0x0D). Giao diện trình điều khiển `KBDHID.sys` giao tiếp với trình điều khiển lớp `KBDCLASS.sys`. 
Trình điều khiển này có trách nhiệm xử lý tất cả phím nhập vào một cách an toàn. 
Sau đó nó sẽ gọi đến `Win32K.sys` (sau khi có thể truyền thông báo qua các bộ lọc bàn phím bên thứ 3 đã được cài đặt). 
Tất cả điều này xảy ra trong chế độ nhân.

`Win32K.sys` tìm cửa sổ nào là cửa sổ đang hoạt động thông qua API `GetForegroundWindow()`. 
API này trả về thanh địa chỉ trình duyệt hiện tại. 
Windows chính "bơm thông báo" sau đó gọi `SendMessage(hWnd, WM_KEYDOWN, VK_RETURN, lParam)`.
`lParam` là một bitmask cho biết thêm thông tin về `keypress: repeat count`  (trường hợp này là 0), mã quét thực tế (có thể phụ thuộc OEM, nhưng nói chung không phải là VK_RETURN), cho dù các phím mở rộng (alt, shift, ctrl) cũng được nhấn, và một số trạng thái khác.

`Windows SendMessage API` là một hàm đơn giản để thêm thông điệp vào một hàng đợi cho các cửa sổ cụ thể xử lý (`hWnd`). 
Sau đó, hàm xử lý thông điệp chính (gọi là `WindowProc`) được gán cho `hWnd` được gọi là để xử lý từng thông điệp trong hàng đợi.

Cửa sổ (`hWnd`) đang hoạt động thực ra là một điều khiển chỉnh sửa và trong trường hợp này, `WindowProc` có một trình xử lý để xử lý các thông điệp `WM_KEYDOWN`. 
Mã này xem bên trong tham số thứ ba đã được truyền đến `SendMessage(wParam)` bởi vì `VK_RETURN` biết người dùng đã nhấn phím ENTER.

<a name="5"></a>
### (Mac OS X) Một KeyDown NSEvent được gửi đến ứng dụng

Tín hiệu ngắt gây ra sự gián đoạn cho driver bàn phím I/O Kit Kext. 
Driver chuyển tín hiệu thành mã phím và chuyển nó tới quá trình WindowServer của OS X. 
Kết quả là, WindowServer gửi một sự kiện đến bất kỳ ứng dụng thích hợp (ví dụ như active hoặc listening) thông qua cổng Mach của họ, nơi nó được đặt vào một hàng đợi sự kiện. 
Các sự kiện sau đó có thể được đọc từ hàng đợi này bởi hàm `mach_ipc_dispatch` với quyền hạn đủ cao.
Điều này thường được tạo ra bởi một vòng lặp sự kiện chính - `NSApplication` , và được xử lý thông qua một `NSEvent` của `NSEventType KeyDown`.

<a name="6"></a>
### (GNU/Linux)  Xorg server lắng nghe mã phím 

Khi `X server` được sử dụng, X sẽ sử dụng trình điều khiển sự kiện chung `evdev` để có được phím bấm. 
X Server sẽ ánh xạ giá trị mã phím vào mã quét theo một quy tắc cụ thể. 
Khi quá trình lập bản đồ này hoàn thành, máy chủ X sẽ gửi ký tự cho trình quản lý cửa sổ (DWM, metacity, i3, vv), do đó trình quản lý cửa sổ sẽ gửi ký tự tới cửa sổ hiện tại. 
API đồ hoạ của cửa sổ nhận ký tự sẽ in biểu tượng, font thích hợp.

<a name="7"></a>
### Phân tích URL

Bây giờ trình duyệt có các thông tin sau trong URL (Uniform Resource Locator):

```
Giao thức : http (Sử dụng 'Hyper Text Transfer Protocol')
Tài nguyên "/" (Truy xuất vào trang chủ)
```

<a name="8"></a>
### Đó có phải là một URL hay một cụm từ tìm kiếm?

Khi giao thức hoặc tên máy chủ không hợp lệ, trình duyệt tìm kiến văn bản sử dụng công cụ tìm kiếm mặc định.
Trong nhiều trường hợp, URL có một đoạn văn bản đặc biệt được nối vào nó để báo cho công cụ tìm kiếm rằng nó là URL của trình duyệt cụ thể.

<a name="9"></a>
### Chuyển đổi ký tự Unicode (không phải ASCII) trong tên máy chủ 

- Trình duyệt sẽ kiểm tra xem đầu vào có chứa ký tự khác với a-z, A-Z, 0-9 hoặc ..

- Ở đây, tên máy chủ là google.com, vì vậy không có ký tự không phải là ASCII, nếu có, trình duyệt sử dụng mã hóa [Punycode](https://en.wikipedia.org/wiki/Punycode) cho phần tên máy chủ lưu trữ

<a name="10"></a>
### Kiểm tra danh sách HSTS

- Trình duyệt kiểm tra danh sách "HSTS (HTTP Strict Transport Security) đã được tải trước". Đây là danh sách chứa các trang web yêu cầu trình duyệt kết nối chỉ sử dụng HTTPS.
- Nếu trang web nằm trong danh sách, trình duyệt sẽ gửi yêu cầu của mình qua HTTPS thay vì HTTP. Nếu không, yêu cầu ban đầu được gửi qua HTTP.
(Lưu ý rằng ngay cả khi trang web không có trong danh sách HSTS, bạn có thể yêu cầu trình duyệt của bạn truy cập chính sách HSTS của riêng bạn.
Yêu cầu HTTP đầu tiên cho trang web mà người dùng truy cập sẽ nhận được phản hồi yêu cầu người dùng chỉ gửi yêu cầu HTTPS. 
Tuy nhiên, yêu cầu HTTP đơn này có thể khiến người dùng dễ bị [downgrade attack](https://en.wikipedia.org/wiki/Moxie_Marlinspike#Notable_research), đó là lý do tại sao danh sách HSTS được cấu hình sẵn trong các trình duyệt hiện tại)

<a name="11"></a>
### DNS lookup 

- Trình duyệt kiểm tra xem domain nằm trong cache hay không. (Xem cache Google chrome : chrome://net-internals/#dns	)
- Nếu không, trình duyệt gọi hàm `gethostbyname`  (có thể khác nhau với nhiều OS) để thực hiện tìm kiếm 
- `gethostbyname` kiểm tra xem tên máy chủ có thể được phân giải bằng tham chiếu trong file hosts (có vị trí khác nhau theo hệ điều hành) trước khi phân giải tên máy chủ thông qua DNS.
- Nếu `gethostbyname` không có bản ghi cache cho tên miền này và cũng không thấy trong file hosts thì nó sẽ yêu cầu máy chủ DNS được cấu hình sẵn. 
Đây thường là máy chủ DNS cache của một bộ định tuyến cục bộ hoặc ISP.
- Nếu máy chủ DNS trên cùng một mạng con, hệ thống sẽ thực hiện `ARP process` đến máy chủ DNS 
- Nếu máy chủ DNS trên mạng khác, hệ thống sẽ thực hiện `ARP process` đến default gateway

<a name="12"></a>
### ARP process

Để gửi một ARP (Address Resolution Protocol) broadcast, chúng ta cần có một địa chỉ IP đích và chúng ta cũng cần phải biết địa chỉ MAC của giao diện đã được sử dụng để gửi phát ARP.

Đầu tiên kiểm tra bộ nhớ cache ARP, nếu có thì trả về kết quả Target IP = MAC. 

Ngược lại, nếu nó không trong ARP cache : 

- Kiểm tra bảng định tuyến để xem địa chỉ IP đích có nằm trong một mạng con trong bảng định tuyến cục bộ hay không. 
Nếu có, sử dụng giao diện kết nối với mạng con đó, nếu không sử dụng giao diện kết nối với cổng mặc định. 
- Truy vấn địa chỉ MAC của giao diện mạng đã chọn
- Thư viện mạng gửi một yêu cầu ARP ở lớp 2 (lớp liên kết dữ liệu của mô hình [OSI](https://en.wikipedia.org/wiki/OSI_model)) :

`ARP Request:`

```
Sender MAC: interface:mac:address:here
Sender IP: interface.ip.goes.here
Target MAC: FF:FF:FF:FF:FF:FF (Broadcast)
Target IP: target.ip.goes.here
```

Tùy thuộc vào loại phần cứng giữa máy tính và router :

Kết nối trực tiếp: 
- Nếu máy tính kết nối trực tiếp với router, router sẽ trả về một ARP Reply (xem bên dưới).

Hub: 
- Nếu máy tính kết nối với một hub, hub sẽ phát các yêu cầu ARP tới tất cả các cổng khác, và nếu router được "kết nối", nó sẽ trả về một ARP Reply.

Switch:
- Nếu máy tính kết nối với một switch, switch sẽ kiểm tra bảng CAM/MAC cục bộ của nó để xem port nào có địa chỉ MAC mà chúng ta đang tìm kiếm. 
Nếu switch không tìm thấy, nó sẽ phát lại yêu cầu ARP tới tất cả các cổng khác.
Nếu switch có một mục tương ứng trong bảng MAC/CAM, nó sẽ gửi yêu cầu ARP đến cổng có địa chỉ MAC mà chúng ta đang tìm kiếm.
- Nếu router cũng được "kết nối" trong nó, nó sẽ trả về một ARP reply.

`ARP Reply:`

```
Sender MAC: target:mac:address:here
Sender IP: target.ip.goes.here
Target MAC: interface:mac:address:here
Target IP: interface.ip.goes.here
```

Bây giờ chúng ta có địa chỉ IP của máy chủ DNS hoặc cổng mặc định, chúng ta có thể tiếp tục với yêu cầu DNS:

- Sử dụng cổng 53 để gửi một gói tin UDP  request đến máy chủ DNS. Nếu gói phản hồi quá lớn, giao thức TCP sẽ được sử dụng.
- Nếu máy chủ DNS cục bộ/ISP không tìm thấy kết quả, nó sẽ gửi một yêu cầu truy vấn đệ quy, truy vấn lớp máy chủ DNS cấp cao theo lớp cho đến khi nó tìm được ủy quyền ban đầu (SOA), nếu nó tìm thấy, nó sẽ trả về kết quả.

<a name="13"></a>
### Khởi tạo một socket 

Khi trình duyệt nhận được địa chỉ IP của máy chủ đích và số cổng được cung cấp trong URL (cổng mặc định http là 80, cổng mặc định https là 443), nó gọi đến thư viện hệ thống và và thực hiện cuộc gọi tới hàm thư viện hệ thống có tên socket và yêu cầu một luồng socket TCP - AF_INET / AF_INET6 và SOCK_STREAM.
- Yêu cầu này đầu tiên được chuyển tới tầng Transport và đóng gói thành segment TCP. Port đích được thêm vào header, port nguồn được chọn động trong khoảng của kernel (ip_local_port_range  trong Linux)
- Segment này được gửi tới tầng Network, nó sẽ được bọc thêm IP header. Địa chỉ IP của server đích và máy được đóng gói vào IP header 
- Gói tin sẽ chuyển xuống tầng Data-link, gói tin được thêm Frame header bao gồm địa chỉ MAC của NIC máy và MAC của gateway (local router). Trước đó, nếu kernel không biết MAC của gateway, nó phải phát truy vấn ARP để tìm nó.

Tại thời điểm này, gói tin đã sẵn sàng để truyền qua:
- [Ethernet](https://en.wikipedia.org/wiki/IEEE_802.3)
- [Wifi](https://en.wikipedia.org/wiki/IEEE_802.11)
- [Mạng dữ liệu di động](https://en.wikipedia.org/wiki/Cellular_data_communication_protocol)

Đối với hầu hết các kết nối Internet gia đình hoặc doanh nghiệp nhỏ, gói tin sẽ truyền từ máy tính của bạn, có thể thông qua một mạng cục bộ, và sau đó thông qua một modem (MOdulator / DEModulator) chuyển đổi số 1 và 0 thành tín hiệu analog thích hợp để truyền qua điện thoại, hoặc kết nối điện thoại không dây. 
Ở đầu kia của kết nối là một modem khác chuyển đổi tín hiệu analog sang dữ liệu số sẽ được xử lý bởi nút mạng tiếp theo. Địa chỉ đích và địa chỉ nguồn của nút sẽ được thảo luận sau.

Hầu hết các doanh nghiệp lớn hơn và một số kết nối với dân cư mới sẽ sử dụng cáp quang hoặc trực tiếp, trong trường hợp này, tín hiệu luôn là số và sẽ được chuyển đến nút mạng tiếp theo để xử lý.

Cuối cùng, gói tin sẽ truy cập router quản lý mạng cục bộ. Từ đó, nó sẽ tiếp tục đi đến các router biên của hệ thống tự trị (AS), các AS khác, và cuối cùng đến máy chủ đích. 
Mỗi router trên đường đi sẽ chiết xuất địa chỉ đích từ tiêu đề gói tin IP và định tuyến nó tới hop thích hợp. 
Thời gian sống (TTL) trong tiêu đề gói tin IP được giảm 1 đơn vị khi đi qua mỗi router. 
Gói này sẽ bị loại bỏ nếu trường TTL đạt đến số không hoặc nếu router hiện tại không có không gian trong hàng đợi của nó (có thể là do tắc nghẽn mạng).

Việc gửi và nhận này xảy ra nhiều lần theo dòng kết nối TCP:
- Client chọn một số thứ tự ban đầu (ISN) và gửi gói tin đến máy chủ với bit SYN được bật để cho biết nó muốn thiết lập một kết nối và đặt số thứ tự ban đầu.
- Server nhận gói SYN nếu nó có thể thiết lập một kết nối:
+ Server lựa chọn số thứ tự ban đầu
+ Server đặt SYN để cho biết nó đang chọn ISN của nó
+ Server sao chép (client ISN +1) vào trường ACK của nó và thêm cờ ACK để cho biết nó đang xác nhận nhận gói tin đầu tiên
- Client xác nhận kết nối bằng cách gửi một gói:
+ Tăng số thứ tự của nó (+1)
+ Tăng giá trị ACK +1
+ Bật cờ ACK
- Dữ liệu được truyền như sau:
+ Khi một bên gửi N byte dữ liệu, nó sẽ tăng SEQ + N 
+ Khi phía bên kia xác nhận nhận được gói tin đó (hoặc một chuỗi các gói tin), nó sẽ gửi một gói ACK với giá trị ACK bằng với Khi phía bên kia thừa nhận nhận được gói tin đó (hoặc một chuỗi các gói tin), nó sẽ gửi một gói ACK với giá trị ACK bằng với Khi phía bên kia thừa nhận nhận được gói tin đó (hoặc một chuỗi các gói tin), nó sẽ gửi một gói ACK với giá trị ACK bằng với số thứ tự cuối cùng của gói dữ liệu nhận được
- Để đóng kết nối 
+ Bên đóng kết nối gửi gói tin FIN 
+ Bên kia nhận gói tin FIN, sẽ gửi lại 1 gói FIN-ACK 
+ Bên đóng sẽ gửi gói ACK để xác nhận rằng FIN đã nhận được

<a name="14"></a>
### TLS handshake

- Client gửi thông điệp `ClientHello` với phiên bản  Transport Layer Security (TLS) của nó, liệt kê các thuật toán mã hóa và phương thức nén khả dụng.
- Server gửi thông điệp `ServerHello` với phiên bản TLS, chọn giải thuật mã hóa và phương pháp nén và chứng chỉ của server được ký bởi một CA (Certificate Authority). 

Chứng chỉ này chứa public key, được sử dụng để client mã hóa phần còn lại của quá trình bắt tay đến khi thống nhất được khóa đối xứng.

- Client xác nhận chứng chỉ số của server nhờ các CA tin cậy. Nếu chứng chỉ này được chứng thực là tin cậy, client sinh một chuỗi ngẫu nhiên và mã hóa nó bằng khóa công khai của server. 
Các byte ngẫu nhiên này có thể dùng để xác định mã đối xứng.
- Server giải mã các byte ngẫu nhiên này bằng khóa riêng của nó, và sử dụng các byte này để tạo khóa đối xứng. 
- Client gửi gói tin `Finished` tới server, sử dụng mã đối xứng để mã hóa thông điệp kèm một mã băm của thông điệp để gửi cho server 
- Server giải mã và băm thông điệp, so sánh giá trị băm được với giá trị băm client gửi có bằng nhau không. Nếu bằng nhau, nó sẽ gửi gói tin `Finished` tới client sử dụng mã hóa đối xứng.
- Từ bây giờ, phiên TLS truyền dữ liệu ứng dụng (HTTP) được mã hóa bằng khóa đối xứng được đồng ý.

<a name="15"></a>

### HTTP protocol

Nếu sử dụng trình duyệt web được Google viết, thay vì gửi yêu cầu HTTP để truy xuất trang, nó sẽ gửi yêu cầu và thương lượng với server "upgrade" từ HTTP lên SPDY.

Nếu client đang sử dụng giao thức HTTP và không hỗ trợ SPDY, nó sẽ gửi yêu cầu tới máy chủ :

```
GET / HTTP/1.1
Host: google.com
Connection: close
[other headers]
```

[other headers] là cặp các khóa:giá trị đặc tả theo khuôn dạng HTTP và được phân tách bằng các dòng. 
(Điều này giả định không có bất kỳ lỗi vi phạm nào về HTTP spec và trình duyệt web sử dụng HTTP/1.1, nếu không Header có thể không chứa trường Host và trường Version chuyển thành HTTP/1.0 hoặc HTTP/0.9)

HTTP/1.1 định nghĩa tùy chọn đóng kết nối để bên gửi báo hiệu kết nối bị đóng sau khi hoàn thành phản hồi. VÍ dụ: 

```
Connection: close
```

Các ứng dụng HTTP/1.1 không hỗ trợ kết nối liên tục phải bao gồm tùy chọn "close" kết nối trong mỗi thông điệp.

Sau khi gửi yêu cầu và tiêu đề, trình duyệt web gửi một dòng trống mới đến máy chủ chỉ ra rằng nội dung được gửi đã kết thúc.

Máy chủ trả lời bằng một mã phản hồi cho biết trạng thái của yêu cầu:

```
200 OK
[response headers]
```

Tiếp theo là một dòng mới, và sau đó gửi payload của nội dung HTML của www.google.com. 
Server sau đó có thể đóng kết nối, hoặc nếu tiêu đề được gửi bởi client yêu cầu nó giữ kết nối để được sử dụng lại cho các yêu cầu tiếp theo.

Nếu các tiêu đề HTTP được gửi bởi trình duyệt web bao gồm đầy đủ thông tin cho máy chủ web để xác định xem phiên bản của file cache trong trình duyệt web đã được sửa đổi chưa kể từ lần truy hồi cuối cùng
(ví dụ: nếu trình duyệt web bao gồm một `ETag header`) thì thông điệp phản hồi là:

```
304 Not Modified
[response headers]
```

và không có payload và trình duyệt sẽ nhận nội dung từ cache.

Sau khi phân tích cú pháp HTML, trình duyệt web (và máy chủ) lặp lại quá trình này cho mọi tài nguyên (image, CSS, favicon.ico, vv) được tham chiếu bởi trang HTML, 
ngoại trừ GET / HTTP/1.1 yêu cầu sẽ là GET / $(URL tương đối với www.google.com) HTTP / 1.1.

Nếu HTML tham chiếu tới một tài nguyên trên một miền khác với www.google.com, trình duyệt web sẽ quay trở lại các bước liên quan đến phân giải miền khác và làm theo tất cả các bước cho đến thời điểm này cho tên miền đó. 
Tiêu đề Host trong yêu cầu sẽ được đặt thành tên máy chủ thích hợp thay vì google.com

<a name="16"></a>
### Xử lý yêu cầu HTTP Server  

Máy chủ HTTPD (HTTP Daemon) là một máy chủ xử lý yêu cầu/phản hồi ở phía máy chủ. 
Các máy chủ HTTPD phổ biến nhất là Apache hoặc nginx dành cho Linux và IIS cho Windows.
- HTTPD (HTTP Daemon) nhận được yêu cầu.
- Máy chủ chia nhỏ yêu cầu thành các tham số sau:
+ HTTP Request (GET, HEAD, POST, PUT, DELETE, CONNECT, OPTIONS, hoặc TRACE). Trong trường hợp một URL được nhập trực tiếp vào thanh địa chỉ, nó sẽ là GET.
+ Domain, trong trường hợp này - google.com.
+ Đường dẫn/trang yêu cầu, trong trường hợp này là / (vì không có đường dẫn/trang cụ thể nào được yêu cầu, / là đường dẫn mặc định).
- Máy chủ xác minh rằng có một máy chủ ảo được cấu hình tương ứng với google.com.
- Máy chủ xác minh rằng google.com có ​​thể chấp nhận yêu cầu GET.
- Máy chủ xác minh rằng client được phép sử dụng phương pháp này (bằng IP, xác thực, v.v.).
- Nếu máy chủ đã cài đặt các mô đun ghi đè URL (ví dụ mod_rewrite cho Apache và IIS Rewrite cho URL), máy chủ sẽ cố gắng khớp nguyên tắc viết lại và nếu nó khớp, máy chủ sẽ viết lại yêu cầu theo các quy tắc
- Máy chủ nhận được phản hồi tương ứng dựa trên thông tin yêu cầu. Trong trường hợp này, bởi vì đường dẫn truy cập là "/", nó sẽ truy cập tệp tin trang chủ (bạn có thể ghi đè quy tắc này, nhưng đây là cách phổ biến nhất).
- Máy chủ sẽ sử dụng trình xử lý đã chỉ định để phân tích và xử lý file. Nếu Google sử dụng PHP, máy chủ sẽ sử dụng PHP để phân tích file index, trả về kết quả cho client.

<a name="17"></a>
### Phía sau trình duyệt
Khi máy chủ cung cấp các tài nguyên (HTML, CSS, JS, image, v.v.) cho trình duyệt, nó sẽ trải qua quá trình dưới đây:

- Phân tích cú pháp HTML, CSS, JS
- Rendering : Xây dựng DOM Tree → Render Tree → Phân lớp Render Tree → Vẽ Render Tree

<a name="18"></a>
### Trình duyệt 

Chức năng của trình duyệt là truy xuất tài nguyên bạn muốn từ máy chủ và hiển thị nó trong cửa sổ trình duyệt. 
Tài nguyên thường là các tệp HTML, hoặc các tệp PDF, hình ảnh hoặc các loại nội dung khác. 
Vị trí của tài nguyên được xác định bởi URI (Uniform Resource Identifier).

Cách trình duyệt giải thích và hiển thị các tệp HTML được mô tả chi tiết trong các tiêu chuẩn HTML và CSS. 
Các tiêu chuẩn này được duy trì bởi W3C (World Wide Web Consortium).

Giao diện của trình duyệt có các thành phần phổ biến : 
- Một thanh địa chỉ
- Nút quay lại và chuyển tiếp
- Tùy chọn dấu trang
- Nút Làm mới và dừng lại
- Nút Home

#### Cấu trúc bậc cao của trình duyệt 
Các thành phần của trình duyệt bao gồm : 

- *User interface* : Giao diện người dùng chứa thanh địa chỉ, nút chuyển tiếp và trở lại, bookmark, vv, ngoại trừ trang yêu cầu, tất cả những gì bạn thấy là một phần của giao diện người dùng
- *Browser engine* : Browser engine điều phối giao diện người dùng với Rendering engine
- *Rendering engine* : Rendering engine có trách nhiệm hiển thị nội dung yêu cầu. Ví dụ: nếu nội dung được yêu cầu là HTML, công cụ kết xuất phân tách HTML và CSS và hiển thị nội dung được phân tích cú pháp trên màn hình.
- *Networking* : Các thành phần mạng chịu trách nhiệm cho các cuộc gọi mạng, chẳng hạn như yêu cầu HTTP, triển khai các cách khác nhau cho các nền tảng khác nhau phía sau giao diện nền tảng độc lập.
- *UI backend* : UI được sử dụng để vẽ các thành phần UI cơ bản như hộp danh sách thả xuống và cửa sổ. UI backend cho thấy một giao diện độc lập nền tảng thống nhất. Bên dưới nó sử dụng phương pháp giao diện người dùng hệ điều hành
- *JavaScript engine* : Phân tích và thực thi mã JavaScript
- *Data storage* : Trình duyệt có thể cần lưu trữ nhiều loại dữ liệu cục bộ, chẳng hạn như các cookie. Các trình duyệt cũng cần hỗ trợ các cơ chế lưu trữ chẳng hạn như localStorage, IndexedDB, WebSQL và FileSystem

<a name="19"></a>
### HTML parsing

Rendering engine bắt đầu nhận nội dung của tài liệu được yêu cầu từ lớp mạng. Điều này thường sẽ được thực hiện trong khối 8kB.

Công việc chính của trình phân tích cú pháp HTML là phân tích đánh dấu HTML vào một cây phân tích cú pháp.

Cây đầu ra ("cây phân tích") là một cây của các phần tử DOM và các nút thuộc tính. 
DOM là viết tắt của Document Object Model. 
Đây là sự trình bày đối tượng của tài liệu HTML và giao diện các phần tử HTML và phần bên ngoài như JavaScript. Gốc của cây là đối tượng "Document".
Toàn bộ DOM và tài liệu HTML là mối quan hệ gần như một-một.

<a name="20"></a>
#### Thuật toán phân tích 

HTML không thể phân tích theo kiếu top-down hoặc bottom-up như thông thường.

Lý do là :
- Tính tự nhiên của ngôn ngữ 
- Chính HTML có thể không đầy đủ, đối với các lỗi phổ biến, các trình duyệt cần phải có một cơ chế chống lỗi truyền thống để hỗ trợ chúng
- Quá trình phân tích cú pháp cần được lặp lại. 
Đối với các ngôn ngữ khác, mã nguồn không thay đổi trong quá trình phân tích cú pháp, nhưng đối với HTML, mã động như script gọi đến phương thức `document.write()` để thêm nội dung vào mã nguồn, tức là nội dung của đầu vào của quá trình phân tích cú pháp sẽ thay đổi 

Do không có khả năng sử dụng các kỹ thuật phân tích phổ biến, các trình duyệt đã tạo ra các trình phân tích cú pháp được thiết kế đặc biệt để phân tích cú pháp HTML. 
Thuật toán phân tích cú pháp được mô tả chi tiết theo đặc tả của HTML5.

Thuật toán bao gồm hai giai đoạn: tokenization và xây dựng cây.

#### Các hành động sau khi phân tích kết thúc
Trình duyệt bắt đầu tải các tài nguyên bên ngoài (CSS, hình ảnh, file Javascript, vv) của trang web.

Tại thời điểm này, trình duyệt đánh dấu tài liệu là tương tác và trình duyệt bắt đầu phân tích cú pháp các script trong chế độ "deferred", nghĩa là những gì cần phải được thực hiện sau khi tài liệu đã được phân tích cú pháp. 
Sau khi trạng thái của tài liệu thay đổi thành "complete", trình duyệt sẽ kích hoạt sự kiện "load".

Lưu ý rằng không bao giờ có lỗi "Invalid Syntax" khi phân tích cú pháp một trang web HTML, trình duyệt khắc phục bất kỳ nội dung không hợp lệ nào và sau đó tiếp tục phân tích cú pháp.

<a name="21"></a>
### CSS interpretation

- Phân tích các tệp CSS, nội dung thẻ <style> và các thuộc tính style bằng cách sử dụng "CSS lexical and syntax grammar"
- Mỗi tệp CSS được phân tách thành một đối tượng StyleSheet, trong đó mỗi đối tượng chứa các quy tắc CSS với bộ chọn và các đối tượng tương ứng với ngữ pháp CSS.
- Một trình phân tích cú pháp CSS có thể là từ trên xuống dưới hoặc từ dưới lên.

<a name="22"></a>
### Page Rendering

- Tạo 'Frame Tree' hoặc 'Render Tree' bằng cách đi qua các nút DOM, và tính các giá trị CSS style cho mỗi nút.
- Tính chiều rộng ưa thích của mỗi nút trong 'Frame Tree' từ dưới lên bằng cách tổng hợp chiều rộng ưa thích của các nút con và các lề nằm ngang, biên và padding của nút.
- Tính toán chiều rộng thực tế của mỗi nút từ trên xuống bằng cách cấp phát chiều rộng cho mỗi nút của con.
- Tính toán chiều cao của mỗi nút từ dưới lên bằng cách áp dụng gói văn bản và tổng hợp các chiều cao nút con và các lề, biên và lề của nút.
- Tính tọa độ của mỗi nút bằng cách sử dụng thông tin được tính toán ở trên.
- Có nhiều tính toán phức tạp hơn khi có một phần tử được `floated` và vị trí `absolutely` hoặc `relatively`, vv, xem http://dev.w3.org/csswg/css2/ và http://www.w3.org/
- Tạo lớp để mô tả phần nào của trang có thể được nhóm lại mà không bị phân mảnh. Mỗi đối tượng frame/render được gán cho một layer.
- Textures được cấp phát cho mỗi lớp của trang 
- Các  frame/render cho mỗi layer được traversed và các lệnh vẽ được thực thi cho từng lớp tương ứng.  Điều này có thể được rasterized bởi CPU hoặc vẽ trực tiếp trên GPU sử dụng D2D/SkiaGL.
- Tất cả các bước trên có thể sử dụng lại các giá trị được tính từ lần cuối cùng trang web được hiển thị, do đó các thay đổi gia tăng đòi hỏi ít công việc hơn.
- Các layer kết hợp với nhau tạo ra các nội dung có thể nhìn thấy được.
- Cuối cùng, các vị trí layer được tính toán và các lệnh ghép được phát hành qua Direct3D/OpenGL. 
Bộ đệm lệnh GPU được đưa vào GPU để hiển thị không đồng bộ và khung được gửi tới window server.

<a name="23"></a>
### GPU Rendering
- Trong quá trình render, lớp xử lý đồ họa có thể sử dụng một CPU mục đích chung, hoặc một GPU xử lý đồ họa
- Khi sử dụng GPU để tính toán kết xuất đồ hoạ, các lớp phần mềm đồ họa chia công việc thành nhiều phần, vì vậy nó có thể tận dụng ưu thế song song lớn của GPU để tính toán điểm nổi trong quá trình dựng hình.

<a name="24"></a>
### Window Server

<a name="25"></a>
### Thực thi Post-rendering và user-induced

Sau khi hiển thị, trình duyệt chạy mã JavaScript (chẳng hạn như hình động của Google Doodle) hoặc tương tác với người dùng (nhập các thuật ngữ tìm kiếm trong thanh tìm kiếm) dựa trên cơ chế thời gian. 
Plugin như Flash và Java sẽ chạy, mặc dù không có trong trang chủ của Google. 
Các tập lệnh này có thể kích hoạt các yêu cầu web và cũng có thể thay đổi nội dung và bố cục của các trang web để tạo ra một vòng rendering và painting khác.

