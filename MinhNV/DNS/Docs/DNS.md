
# Tìm hiểu về DNS 
## Mục lục 

- [I. DNS là gì? ](#1)
- [II. Chức năng](#2)
- [III. Workflow](#3)
- [IV. Cấu trúc của DNS](#4)
    
    + [1. Cấu trúc cơ sở dữ liệu](#5)
    + [2. Các bản ghi](#6)

<a name="1"></a>
## I.DNS là gì? 

- DNS là từ viết tắt của Domain Name System, là Hệ thống phân giải tên được phát minh vào năm 1984 cho Internet, chỉ một hệ thống cho phép thiết lập tương ứng giữa địa chỉ IP và tên miền. Hệ thống tên miền (DNS) là một hệ thống đặt tên theo thứ tự cho máy vi tính, dịch vụ, hoặc bất kỳ nguồn lực tham gia vào Internet. Nó liên kết nhiều thông tin đa dạng với tên miền được gán cho những người tham gia. Quan trọng nhất là, nó chuyển tên miền có ý nghĩa cho con người vào số định danh (nhị phân), liên kết với các trang thiết bị mạng cho các mục đích định vị và địa chỉ hóa các thiết bị khắp thế giới. 

- Nó phục vụ như một “Danh bạ điện thoại” để tìm trên Internet bằng cách dịch tên máy chủ máy tính thành địa chỉ IP 

Ví dụ, www.example.com dịch thành 208.77.188.166.

- Hệ thống tên miền giúp cho nó có thể chỉ định tên miền cho các nhóm người sử dụng Internet trong một cách có ý nghĩa, độc lập với mỗi địa điểm của người sử dụng. Bởi vì điều này, World-Wide Web (WWW) siêu liên kết và trao đổi thông tin trên Internet có thể duy trì ổn định và cố định ngay cả khi định tuyến dòng Internet thay đổi hoặc những người tham gia sử dụng một thiết bị di động. Tên miền internet dễ nhớ hơn các địa chỉ IP như là 208.77.188.166 (IPv4) hoặc 2001: db8: 1f70:: 999: de8: 7648:6 e8 (IPv6).

- Mọi người tận dụng lợi thế này khi họ thuật lại có nghĩa các URL và địa chỉ email mà không cần phải biết làm thế nào các máy sẽ thực sự tìm ra chúng.

### Phân loại các Domain Name Server.

- Tên miền riêng (Primary Name Server) : Mỗi một máy chủ tên miền có mọt tên miền riêng. Tên miền riêng này được đăng ký trên internet.

- Tên miền dự phòng - tên miền thứ hai (Secondary Name Server) : Đây là một DNS server được sử dụng để thay thế cho Primary server DNS server bằng cách sao lưu lại tất cả bản ghi dữ liệu trên Primary Name server và nếu Primary Name server bị gián đoạn thì nó sẽ đảm nhận việc phần giải ánh xạ tên miền và địa chỉ IP.

- Caching Name Server : Đây là một server đảm nhiệm việc lưu tất cả những tên miền, địa chỉ IP, đã được phân giải và ánh xạ thành công. Nó được dùng trong các trường hợp sau :

    + Làm tăng tốc độ phân giải bằng cách sử dụng cache
    + Làm giảm bớt gánh nặng phân giải tên máy cho các DNS server
    + Giảm lưu lượng tham gia vào mạng và giảm độ trễ trên mạng (rất quang trọng).

<a name="2"></a>
## II. Chức năng của DNS

Mỗi Website có một tên (là tên miền hay đường dẫn URL:Universal Resource Locator) và một địa chỉ IP. Địa chỉ IP gồm 4 nhóm số cách nhau bằng dấu chấm(Ipv4). Khi mở một trình duyệt Web và nhập tên website, trình duyệt sẽ đến thẳng website mà không cần phải thông qua việc nhập địa chỉ IP của trang web. Quá trình "dịch" tên miền thành địa chỉ IP để cho trình duyệt hiểu và truy cập được vào website là công việc của một DNS server. Các DNS trợ giúp qua lại với nhau để dịch địa chỉ "IP" thành "tên" và ngược lại. Người sử dụng chỉ cần nhớ "tên", không cần phải nhớ địa chỉ IP (địa chỉ IP là những con số rất khó nhớ)

<a name="3"></a>
### III. Workflow 

<img src="https://github.com/MinhKMA/meditech-thuctap/blob/master/MinhNV/DNS/Images/dns-e5.gif">

1. Khi máy tính của bạn cần kết nối với máy chủ lưu trữ trên Internet (ví dụ: MyGreatName.com), bạn chỉ cần nhập Tên miền (ví dụ: MyGreatName.com) vào URL của trình duyệt. Sau đó, máy tính của bạn sẽ liên hệ với Name Servers được cấu hình hoặc mặc định (thường là ISP Name Server), yêu cầu địa chỉ IP của máy chủ lưu trữ (ví dụ MyGreatName.com).

2. Nếu ISP Name Server của bạn có thông tin về địa chỉ IP của máy chủ truy vấn, nó sẽ cho máy tính của bạn biết ngay 

3. Giả sử rằng ISP Name Server của bạn không có thông tin của MyGreatName.com. ISP Name Server của bạn sẽ hỏi DNS Root Name Server ngay lập tức Name Server có thông tin của MyGreatName.com.

    ISP Name Server của bạn có thể biết máy chủ Root Name như thế nào? Root Name Server nào cần hỏi?

    Trên thực tế tất cả các Name Server sẽ tải về và cài đặt một tệp tin từ máy chủ FTP của liên quốc tế. File được gọi là "named.cache" hoặc "named.root". File đó có địa chỉ IP của tất cả Root Name Servers.

    Đây là file named.cache năm 2005: 

    ```sh 
    ; This file holds the information on root name servers 
    ; needed to initialize cache of Internet domain name 
    ; servers (e.g. reference this file in the 
    ; "cache  .  " configuration file of BIND domain 
    : name servers).
    ;
    ; This file is made available by InterNIC registration 
    ; services under anonymous FTP as
    ;     file             /domain/named.root
    ;     on server        FTP.RS.INTERNIC.NET
    ; -OR- under Gopher at RS.INTERNIC.NET
    ;     under menu     InterNIC Registration Services (NSI)
    ;        submenu     InterNIC Registration Archives
    ;     file           named.root
    ;
    ; last update:    Aug 22, 1997
    ; related version of root zone:   1997082200
    ;
    ;
    ; formerly NS.INTERNIC.NET
    ;
    .                     3600000 IN  NS  A.ROOT-SERVERS.NET.
    A.ROOT-SERVERS.NET.   3600000     A   198.41.0.4
    ;
    ; formerly NS1.ISI.EDU
    ;
    .                     3600000     NS  B.ROOT-SERVERS.NET.
    B.ROOT-SERVERS.NET.   3600000     A   128.9.0.107
    ;
    ; formerly C.PSI.NET
    ;
    .                     3600000     NS  C.ROOT-SERVERS.NET.
    C.ROOT-SERVERS.NET.   3600000     A   192.33.4.12
    ;
    ; formerly TERP.UMD.EDU
    ;
    .                     3600000     NS  D.ROOT-SERVERS.NET.
    D.ROOT-SERVERS.NET.   3600000     A   128.8.10.90
    ;
    ; formerly NS.NASA.GOV
    ;
    .                     3600000     NS  E.ROOT-SERVERS.NET.
    E.ROOT-SERVERS.NET.   3600000     A   192.203.230.10
    ;
    ; formerly NS.ISC.ORG
    ;
    .                     3600000     NS  F.ROOT-SERVERS.NET.
    F.ROOT-SERVERS.NET.   3600000     A   192.5.5.241
    ;
    ; formerly NS.NIC.DDN.MIL
    ;
    .                     3600000     NS  G.ROOT-SERVERS.NET.
    G.ROOT-SERVERS.NET.   3600000     A   192.112.36.4
    ;
    ; formerly AOS.ARL.ARMY.MIL
    ;
    .                     3600000     NS  H.ROOT-SERVERS.NET.
    H.ROOT-SERVERS.NET.   3600000     A   128.63.2.53
    ;
    ; formerly NIC.NORDU.NET
    ;
    .                     3600000     NS  I.ROOT-SERVERS.NET.
    I.ROOT-SERVERS.NET.   3600000     A   192.36.148.17
    ;
    ; temporarily housed at NSI (InterNIC)
    ;
    .                     3600000     NS  J.ROOT-SERVERS.NET.
    J.ROOT-SERVERS.NET.   3600000     A   198.41.0.10
    ;
    ; housed in LINX, operated by RIPE NCC
    ;
    .                     3600000     NS  K.ROOT-SERVERS.NET.
    K.ROOT-SERVERS.NET.   3600000     A   193.0.14.129
    ;
    ; temporarily housed at ISI (IANA)
    ;
    .                     3600000     NS  L.ROOT-SERVERS.NET.
    L.ROOT-SERVERS.NET.   3600000     A   198.32.64.12
    ;
    ; housed in Japan, operated by WIDE
    ;
    .                     3600000     NS  M.ROOT-SERVERS.NET.
    M.ROOT-SERVERS.NET.   3600000     A   202.12.27.33
    ; End of File
    ```

    Từ tệp named.cache nói trên, chúng ta biết rằng có 13 Name Server Root trên internet và được phân phối trên tất cả mọi nơi trên toàn thế giới 

    Root Name Servers có tất cả thông tin của Autoritative Domain Name Servers cho top level domain names (VD: .com, .org, .net, .com.hk, etc ..)

4. Khi ISP Name Server của bạn không có thông tin địa chỉ IP của MyGreatName.com, nó sẽ kiểm tra tập tin named.cache và yêu cầu trợ giúp từ Root NameServer. Nếu Root NameServer bị lỗi hoặc không có phản hồi, Name Server ISP của bạn sẽ hỏi Root NameServer thứ 2.

5. Root Name Serve sẽ nói ch ISP Name Server của bạn authoritative Name Server của MyGreatName.com là 212.69.192.10 Primary Name Server) và 212.69.192.11 (Secondary Name Server). 

    Bây giờ, bạn nên biết rằng tại sao bạn cần phải gửi thông tin của hai Name Server khi đăng ký tên miền mới.

6. Bây giờ ISP Name Server của bạn đã có địa chỉ ip của Authoritative Name Server MyGreatName.com. ISP Name Server của bạn sẽ liên lạc với Authoritative Name Server của MyGreatName.com (212.69.192.10). Authoritative Name Server của MyGreatName.com sẽ kiểm tra và xác nhận thông tin của MyGreatName.com. Sau đó nó cho biết địa chỉ IP của MyGreatName.com (212.69.204.148) với ISP của bạn.

7. ISP Name Server của bạn bây giờ có địa chỉ IP của MyGreatName.com, nó sẽ cho máy tính của bạn ngay lập tức.

8. Khi máy tính của bạn nhận được địa chỉ IP của MyGreatName.com, máy tính của bạn có thể giao tiếp với MyGreatName.com.

<a name="4"></a>
## IV. Cấu trúc của DNS 

<a name="5"></a>
### 1. Cấu trúc cơ sở dữ liệu.

- Cơ sở dữ liệu của hệ thống DNS là hệ thống cơ sở dữ liệu phân tán và phân cấp hình cây. Với `.Root server` là đỉnh của cây và sau đó các miền (domain) được phân nhánh dần xuống dưới và phân quyền quản lý.

- Khi một máy khách (client) truy vấn một tên miền nó sẽ đi lần lượt từ root phân cấp xuống dưới để đến DNS quản lý domain cần truy vấn.

#### Zone 

- Hệ thống tên miền(DNS) cho phép phân chia tên miền để quản lý và nó chia hệ thống tên miền thành zone và trong zone quản lý tên miền được phân chia đó.

- Các Zone chứa thông tin vê miền cấp thấp hơn, có khả năng chia thành các zone cấp thấp hơn và phân quyền cho các DNS server khác quản lý.

- Ví dụ : Zone “.vn” thì do DNS server quản lý zone “.vn” chứa thông tin về các bản ghi có đuôi là “.vn” và có khả năng chuyển quyền quản lý (delegate) các zone cấp thấp hơn cho các DNS khác quản lý như “.fpt.vn” là vùng (zone) do fpt quản lý.

Hệ thống cơ sở dữ liệu của DNS là hệ thống dữ liệu phân tán hình cây như cấu trúc đó là cấu trúc logic trên mạng Internet

<a name="6"></a>
### 2.Các bản ghi thường có trong cơ sở dữ liệu của DNS server

#### 2.1 Bản ghi SOA (Start of Authority )

- Bản ghi này xác định máy chủ DNS có thẩm quyền cung cấp thông tin về tên miền xác định trên DNS.

- Trong mỗi tập tin CSDL phải có 1 và chỉ 1 record SOA. Bảng ghi SOA này chỉ ra rằng Primary Name Server là nơi cung cấp thông tin tin cậy từ dữ liệu có trong zone.

- Cú pháp:

```sh
[tên-miền] IN SOA [tên-DNS-Server] [địa-chỉ-email] (

Serial number;

Refresh number;

Retry number;

Experi number;

Time-to-line number)
```

- Ví dụ: 

```sh
meditech.com. IN SOA ns.meditech.com. root.meditech.com. (

1 ; serial

10800 ; refresh after 3 hours

3600 ; retry after 1 hours

604800 ; expire after 1 week

86400 ) ; minimum TTL of 1 day
```

Giải thích ý nghĩa ví dụ trên :

- Tên Domain : meditech.com. phải ở vị trí cột đầu tiên và kết thúc bằng dấu chấm (.).
- IN là Internet
- ns.meditech.com. là tên FQDN của Primary Name Server của dữ liệu này.
- root.meditech.com. là địa chỉ email của người phụ trách dữ liệu này. Lưu ý là địa chỉ email thay thế dấu @ bằng dấu chấm sau root.
- Dấu ( ) cho phép ta mở rộng ra viết thành nhiều dòng, tất cả các tham số trong dấu ( ) được dùng cho các Secondary Name Server.

Các thành phần bên trong cú pháp của record SOA :

- Serial : áp dụng cho mọi dữ liệu trong zone và là 1 số nguyên. Trong ví dụ, giá trị này là 1 nhưng thông thường người ta sẽ sử dụng theo định dạng thời gian như 2007092001. Định dạng này theo kiểu yyyymmddnn, trong đó nn là số lần sửa đổi dữ liệu zone trong ngày. Bất kể theo định dạng nào thì luôn luôn phải tăng số này lên mỗi lần sửa đổi dữ liệu zone. Khi Secondary Name Server liên lạc với Primary Name Server thì trước tiên nó sẽ hỏi số serial này. Nếu số serial của máy Secondary nhỏ hơn số serial của máy Primary tức là dữ liệu trên Secondary đã cũ và sau đó máy Secondary sẽ sao chép dữ liệu mới từ máy Primary thay cho dữ liệu đang có.
- Refresh : chỉ ra khoản thời gian máy Secondary kiểm tra dữ liệu zone trên máy Primary để cập nhật nếu cần. Trong ví dụ trên thì cứ mổi 3 giờ máy chủ Secondary sẽ liên lạc với máy chủ Primary để cập nhật nếu có. Giá trị này thay đổi theo tần suất thay đổi dữ liệu trong zone.
- Retry : nếu máy Secondary không kết nối được với máy Primary theo thời hạn mô tả trong refresh (ví dụ trường hợp máy Primary shutdown máy vào lúc đó) thì máy Secondary sẽ tìm cách kết nối lại với máy Primary theo chu kỳ thời gian được xác định trong retry. Thông thường giá trị này nhỏ hơn giá trị refresh
- Expire : nếu sau khoản thời gian này mà máy Secondary không cập nhật được thông tin mới trên máy Primay thì giá trị của zone này trên máy Secondary sẽ bị hết hạn. Nếu bị expire thì Secondary sẽ không trả lời bất cứ 1 truy vấn nào về zone này. Giá trị expire này phải lớn hơn giá trị refresh và giá trị retry.
- TTL : giá trị này áp dụng cho mọi record trong zone và được đính kèm trong thông tin trả lời 1 truy vấn. Mục đích của nó là chỉ ra thời gian mà các máy DNS Server khác cache lại thông tin trả lời. Giúp giảm lưu lượng truy vấn DNS trên mạng.


#### 2.1 Bản ghi NS 

- Bản ghi NS dùng để khai báo máy chủ tên miền cho một tên miền. Nó cho biết các thông tin về tên miền quản lý, do đó yêu cầu có tối thiểu hai bản ghi NS cho mỗi tên miền.
- Cú pháp của bản ghi NS:

    ``<tên miền> IN NS <tên của máy chủ tên miền>``

- Ví dụ:

    ```sh
    meditech.com IN NS dns1.meditech.com
    meditech.com IN NS dns2.meditech.com
    ```

Với khai báo trên, tên miền ksec.com sẽ do máy chủ tên miền có tên dns.meditech.com quản lý. Điều này có nghĩa, các bản ghi như A, CNAME, MX … của tên miền cấp dưới của nó sẽ được khai báo trên máy chủ dns1.meditech.com. và dns2.meditech.com.

#### 2.3 Bản ghi kiểu A 

- Bản ghi kiểu A được dùng để khai báo ánh xạ giữa tên của một máy tính trên mạng và địa chỉ IP của một máy tính trên mạng.
- Bản ghi kiểu A có cú pháp như sau:

    ``Domain IN A <địa chỉ IP của máy>``

- Ví dụ :

    `meditech.com IN A 10.10.10.100`

    Theo ví dụ trên, tên miền ksec.com được khai với bản ghi kiểu A trỏ đến địa chỉ 10.10.10.100 sẽ là tên của máy tính này.

- Một tên miền có thể được khai nhiều bản ghi kiểu A khác nhau để trỏ đến các địa chỉ IP khác nhau. Như vậy có thể có nhiều máy tính có cùng tên trên mạng. Ngược lại một máy tính có một địa chỉ IP có thể có nhiều tên miền trỏ đến, tuy nhiên chỉ có duy nhất một tên miền được xác định là tên của máy, đó chính là tên miền được khai với bản ghi kiểu A trỏ đến địa chỉ của máy.

#### 2.4 Bản ghi kiểu AAAA

- Ánh xạ tên máy (hostname) vào địa chỉ IP version 6
- Cú pháp :
    
    ``[tên-máy-tính] IN AAAA [địa-chỉ-IPv6]``

- Ví dụ :

    ``Server IN AAAA 1243:123:456:7892:3:456ab``

#### 2.5 Bản ghi CNAME

- Bản ghi CNAME cho phép một máy tính có thể có nhiều tên. Nói cách khác bản ghi CNAME cho phép nhiều tên miền cùng trỏ đến một địa chỉ IP cho trước.

- Để có thể khai báo bản ghi CNAME , bắt buộc phải có bản ghi kiểu A để khai báo tên của máy. Tên miền được khai báo trong bản ghi kiểu A trỏ đến địa chỉ IP của máy được gọi là tên miền chính (canonical domain ). Các tên miền khác muốn trỏ đến máy tính này phải được khai báo là bí danh của tên máy (alias domain).

- Bản ghi CNAME có cú pháp như sau :

    ``alias-domain IN CNAME canonical domain``

- Ví dụ :

    ``www.meditech.com IN CNAME meditech.com``

Tên miền www.meditech.com sẽ là tên bí danh của tên miền meditech.com, hai tên miền www.meditech.com sẽ cùng trỏ đến địa chỉ IP 10.10.10.100

#### 2.6 Bản ghi MX

- Bản ghi MX dùng để khai báo trạm chuyển tiếp thư điện tử của một tên miền.

- Ví dụ : Để các thư điện tử có cấu trúc user@ksec.com được gửi đến trạm chuyển tiếp thư điện tử có tên mail.ksec.com, trên cơ sở dữ liệu cần khai báo bản ghi MX như sau:

    ``meditech.com IN MX 10 mail.meditech.com``

- Các thông số được khai báo trong bản ghi MX nêu trên gồm có:

    + meditech.com : là tên miền được khai báo để sử dụng như địa chỉ thư điện tử.
    + mail.meditech.com: là tên của trạm chuyển tiếp thư điện tử, nó thực tế là tên của máy tính dùng làm máy trạm chuyển tiếp thư điện tử.
    + 10: Là giá tri ưu tiên, giá trị ưu tiên có thể là một số nguyên bất kì từ 1 đến 225, nếu giá trị ưu tiên này càng nhỏ thì trạm chuyển tiếp thư điện tử được khai báo sau đó sẽ là trạm chuyển tiếp thư điện tử được chuyển đến đầu tiên.

#### 2.7 Bản ghi PTR

- Hệ thống DNS không những thực hiện việc chuyển đổi từ tên miền sang địa chỉ IP mà còn thực hiện chuyển đổi địa chỉi IP mà còn thực hiện chuyển đổi địa chỉ IP sang tên miền.

- Bản ghi PTR cho phép thực hiện chuyển đổi địa chỉ IP sang tên miền. Cú pháp của bản ghi PTR:

    ``100.10.10.10.in-addr.arpa IN PTR www.meditech.com``

