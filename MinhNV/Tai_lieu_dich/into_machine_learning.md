# Giới thiệu về Machine Leaning 
## Giới thiệu 

Machine Leaning là tập con của trí tuệ nhân tạo (Artificial Intelligence). Mục đích của Machine Leaning đại loại là để hiểu cấu trúc của dữ liệu và làm dữ liệu đó thành các models mà mọi người có thể hiểu và sử dụng.

Mặc dù machine leaning là một lĩnh vực trong ngành khoa học máy tính, nó khác với phương pháp tính toán truyền thống. Trong tính toán truyền thống các thuật toán là các tập lệnh được lập trình một cách rõ ràng được sử dụng bởi máy tính nhằm cho việc tính toán và giải quyết vấn đề. Thuật toán machine leaning thay vào đó cho phép máy tính học từ giá trị dữ liệu đầu vào và sử dụng phân tích thống kê để đưa ra các giá trị nằm trong phạm vi cụ thể. Vì thế, machine leaning tạo điều kiện cho máy tính xây dựng các models từ những dữ liệu mẫu dể tự động đưa ra quyết định dựa trên các dữ liệu đầu vào đã cho.

Bất cứ dân công nghệ nào ngày nay cũng được hưởng lợi từ machine learning. Công nghệ nhận diện khuôn mặt cho phép các nền tảng mạng xã hội hỗ trợ người dùng gắn thẻ và chia sẻ ảnh của bạn bè hay việc sử dụng để gợi ý các bộ phim sẽ phát tiếp theo dựa trên sở thích của người dùng ngoài ra nhưng chiếc xe tự lái sẽ sớm có để phục vụ mọi người nhờ maching leaning. 


Machine Learning là một lĩnh vực phát triển liên lục. Vì vậy có một số nguyên tắc cần lưu ý khi bạn làm việc với các phương pháp hoặc phân tích tác động của các quy trình học máy.

Trong hướng dẫn này chúng ta sẽ xem xét:
- Các thuật toán phổ biến trong machine learning và cách tiếp cận chúng.  
- Ngôn ngữ lập trình được sử dụng nhiều nhất trong machin learning. Ưu và nhược điểm của nó.
- Thảo luận về các thành kiến còn tồn tại ở các thuật tuấn và tìm khắc phục khi xây dựng thuật toán.

## Các phương pháp học máy (Machine Learning Methods)

 Trong machine learning, các nhiệm vụ thường được phân loại thành các mảng lớn. Các mảng được xây dựng đựa dựa vào những sự trải nghiệm và học hỏi trong quá trình phát triển hệ thống

 Trong số phương pháp này có 2 phương pháp được sử dụng phổ biến là supervised learning và unsupervised learning. Trong đó supervised learning xây dựng các thuật toán dựa trên các dữ liệu mẫu được gán nhãn bởi con người còn unsupervised learning cung cấp thuật toán với dữ liệu không được gắn nhãn để nó tìm cấu trúc, qui luật bên trong của dữ diệu đầu vào.

 Cùng tìm hiểu những phương pháp này chi tiết hơn nhé !

 ### Supervised Learning

 Ở supervised learning máy tính được cung cấp các dữ liệu đầu vào mẫu được gán nhãn với các giá trị đầu ra mong muốn của chúng. Mục đích của thuật toán này để so sánh giữa kết quả của quá trình nó được học và kết quả thực tế để từ đó tìm sai số và sửa đổi mô hình một cách phù hợp. Do đó supervised learning sử dụng các mẫu để dự đoán giá trị nhãn trên các dữ liệu chưa gắn nhãn.

 VD: Thuật toán có thể được cung cấp dữ liệu hình ảnh cá mập được gắn nhãn là cá và đại dương được gắn nhãn là nước. Bằng cách sử dụng thuật toán này sau này sẽ xác định được hình ảnh cá mập mà không có nhãn là cá và hình ảnh đại đương không có nhãn gián là nước. 

 Trong trường hợp sử dụng supervised learning, sử dụng dữ liệu đã thu thập được để dự đoán các sự kiện của tương lai.
 Nó có thể sử dụng các thông tin thị trường chứng khoán đã lên sàn để dự đoán các biến động sắp tới hoặc được sử dụng để lọc các email spam.

### Unsupervised Learning

Trong unsupervised learning, dữ liệu không được đánh nhãn. Vì vậy thuật toán này dùng để tìm sự tương đồng giữa các dư liệu đầu vào của nó. Vì dữ liệu không được dán nhãn phong phú hơn dữ liệu có nhãn nên thuật toán này vô cùng có giá trị. 

Mục đích của thuật toán này có thể đơn giản dùng để khám phá những mẫu ẩn bên trong tập dữ liệu nhưng nó có thể học các tính năng , phân loại các dữ liệu thô. 

Unsupervised learning thường dùng cho transactional data. Bạn có thể có một tập dữ liệu lớn của khách hàng và mua hàng của họ, nhưng bạn chưa chắc có thể hiểu được nhưng thuộc tính tương tự từ các hồ sơ khánh hàng và mua hàng của họ. Với dữ liệu này được đưa vào thuật toán unsupervised learning có thể xác định rằng phụ nữ ở độ tuổi nhất định mua xà phòng không mùi có khả năng mang thai và do đó chiến dịch tiếp thị liên quan đến thai kỳ và sản phẩm em bé có thể được nhắm mục tiêu đến đối tượng này theo thứ tự để tăng số lượng mua hàng của họ.

Unsupervised learning thường được sử dụng để phát hiện bất thường bao gồm việc thẻ tín dụng gian lận và hệ thống giới thiệu đề xuất sản phẩm nào cần mua tiếp theo. Trong unsupervised learning những bức ảnh chú chó không được gắn nhãn có thể được dùng là dữ liệu đầu vào cho thuật toán để tìm các điểm giống nhau và phân loại ảnh con chó lại với nhau.


## Phương pháp tiếp cận 

Machine learning có liên kết chặt chẽ đến thống kê tính toán, do đó có kiến thức về toán thống kê rất hữu ích chó việc hiểu và vận dụng thuật toán trong machine learning.

Đối với những người có thể chưa nghiên cứu thống kê nên tìm hiểu sự tương quan và đệ qui trước đã vì chúng thường được sử dụng để nghiên cứu mối quan hệ giữa các biến định lượng. 

Tương quan là một thước đo của sự liên kết giữa hai biến không được chỉ định là phụ thuộc hay độc lập. Hồi quy được sử dụng để kiểm tra mối quan hệ giữa một biến phụ thuộc và một biến độc lập. Bởi vì số liệu thống kê hồi quy có thể được sử dụng để dự đoán biến phụ thuộc khi biến độc lập được biết, hồi quy cho phép khả năng dự đoán.

### k-nearest neighbor

Thuật toán k-nearest neighbor là một mô hình nhận dạng mẫu có thể được sử dụng để phân loại cũng như hồi quy
Thuật toán được viết tắt là k-NN trong đó là số nguyên dương, k thường có giá trị bé.
 
Trong sơ đồ bên dưới, có các vật thể kim cương màu xanh và các vật thể sao màu da cam. Chúng thuộc về hai lớp riêng biệt: lớp kim cương và lớp sao.

<img src="https://assets.digitalocean.com/articles/machine-learning/intro-to-ml/k-NN-1-graph.png">

Khi một đối tượng mới được thêm vào, trong trường hợp này là trái tim xanh. Chúng ta sẽ muốn thuật toán để phân loại trái tim thành một lớp nhất định.

<img src="https://assets.digitalocean.com/articles/machine-learning/intro-to-ml/k-NN-2-graph.png">

Chọn k = 3 thuật toán sẽ tìm 3 đối tượng gần nhất của trái tim màu xanh để phân loại nó vào lớp kim cương hay lớp sao


Trong sơ đồ ở dưới 3 đối tượng gần với trái tim màu xanh nhất là 1 viên kim cương xanh và 2 ngôi sao. Do đó thuật toán sẽ phân loại trái tim xanh cùng với lớp sao 

<img src = "https://assets.digitalocean.com/articles/machine-learning/intro-to-ml/k-NN-3-graph.png">

Trong số các thuật toán của machine learning thuật toán này được coi là một loại "học lười" vì việc phân loại dữ liệu không xảy ra cho đến khí một truy vấn được thực hiện đến hệ thống.

### Decision Tree Learning

Decision Tree được sử dụng để đại diện trực quan các quyết định và trình bày hoặc thông báo cho ra quyết định. Khi làm việc với machine learning và data mining, decision trees được sử dụng như một mô hình dự báo. 

Mục tiêu của thuật toán này là để tạo ra các mô hình dự đoán của một mục tiêu dựa trên các thông số đầu vào. 

Trong mô hình dự báo, các thuộc tính của dữ liệu được xác định được biểu diễn bằng cách nhánh còn khi kết luận về các giá trị cuối của dữ liệu được thể hiện trong các hình lá. 

Khi "học" một cây, dữ liệu nguồn được chia thành các tập con dựa trên một bài kiểm tra giá trị thuộc tính được lặp đi lặp lại trên mỗi tập con có nguồn gốc đệ quy. Khi tập hợp con tại một nút có giá trị tương đương với giá trị đích của nó, quá trình đệ quy sẽ hoàn thành.

Hãy xem xét một ví dụ về các điều kiện khác nhau có thể xác định xem có nên đi câu cá hay không. Điều này bao gồm điều kiện thời tiết cũng như điều kiện áp suất khí quyển.

<img src="https://assets.digitalocean.com/articles/machine-learning/intro-to-ml/decision-tree-diagram.png">

Cây phân loại các điều kiện của một ngày dựa trên điều kiện có phù hợp để đi câu cá hay không.

Bộ dữ liệu cây phân loại thực sự sẽ có nhiều tính năng hơn so với những gì được nêu ở trên nhưng các mối quan hệ phải đơn giản để xác định.

### Deep Learning

Deep learning cố gắng bắt chước bộ não của con người có thể xử lý âm thanh và ánh sáng từ thính giác và thị giác. Kiến trúc của deep learning được lấy cảm hứng từ các mạng lưới noron sinh học vào bao gồm nhiều lớp trong nơ-ron nhận tạo thành phần cứng vào GPU

Deep learning sử dụng một tầng các lớp đơn vị xử lý phi tuyến để trích xuất hoặc chuyển đổi các tính năng (hoặc các biểu diễn) của dữ liệu. Đầu ra của một lớp đóng vai trò là đầu vào của lớp kế tiếp. Trong deep learnning các thuật toán có thể là supervised phân loại dữ liệu hoặc unsupervised thực hiện phân tích mẫu.

Trong số các thuật toán học máy đang được sử dụng và phát triển, deep learning ngốn nhiều dữ liệu nhất và có thể đánh bại con người trong một số công việc về nhận thức. Bới nhưng thuộc tính này deep learning đã trở thành phương pháp tiếp cận để hiện thức hóa trí tuệ nhân tạo.

Nhận dạng hình ảnh và giọng nói đều nhận ra những tiến bộ đáng kể từ deep learning. IBM Watson là một ví dụ nổi tiếng về một hệ thống thúc đẩy việc "học sâu".

## Programming Languages

Từ dữ liệu được lấy từ quảng cáo việc làm trên trang thực tế vào tháng 12 năm 2016, có thể suy ra rằng Python là ngôn ngữ lập trình được tìm kiếm nhiều nhất trong lĩnh vực chuyên môn học máy. Python được theo sau bởi Java, sau đó là R, sau đó là C ++.

## Human Biases

Mặc dù dữ liệu và phân tích tính toán có thể khiến chúng ta nghĩ rằng chúng ta đang nhận được thông tin khách quan nhưng đây không phải là trường hợp, dựa trên dữ liệu không có nghĩa là kết quả đầu ra của machine learning là trung tính. Sự thiên vị của con người đóng một vai trò trong cách dữ liệu được thu thập, tổ chức và cuối cùng trong các thuật toán xác định sẽ tương tác với dữ liệu đó.

Ví dụ, nếu mọi người đang cung cấp hình ảnh cho “cá” làm dữ liệu để huấn luyện thuật toán, và những người này áp đảo chọn hình ảnh cá vàng, một máy tính có thể không phân loại cá mập là cá. Điều này sẽ tạo ra một thiên vị chống lại cá mập là cá và cá mập sẽ không được tính là cá.

Trong thực tế, nghiên cứu gần đây đã chỉ ra rằng các chương trình AI và machine learning thể hiện những thành kiến ​​giống như con người bao gồm các định kiến ​​về chủng tộc và giới tính. 

Khi machine learning được áp dụng vào kinh doanh những thành kiến ​​không thể lường trước có thể duy trì các vấn đề mang tính hệ thống. 

Bởi vì sự thiên vị của con người có thể tác động tiêu cực đến người khác, điều cực kỳ quan trọng là phải nhận thức được nó và cũng làm việc hướng tới việc loại bỏ nó càng nhiều càng tốt. Một cách để hướng tới việc đạt được điều này là đảm bảo rằng có nhiều người khác nhau làm việc trong một dự án và những người đa dạng đang thử nghiệm và xem xét nó. Những người khác đã kêu gọi các bên thứ ba quản lý theo dõi và kiểm toán các thuật toán, xây dựng các hệ thống thay thế có thể phát hiện các thành kiến ​​và đánh giá đạo đức như một phần của kế hoạch dự án khoa học dữ liệu.

## Kết luận 

Bài viết này xem xét về một số  trường hợp sử dụng machine learning và các phương pháp phổ biến được sử dụng trong lĩnh vực này, ngôn ngữ lập trình học máy phù hợp và cũng đề cập đến một số điều cần lưu ý trong các thuật toán vô thức.

Được dịch từ : https://www.digitalocean.com/community/tutorials/an-introduction-to-machine-learning?utm_source=feedly&utm_medium=display&utm_campaign=2018_Brand
