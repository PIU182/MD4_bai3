# BÀI TẬP 3 – TƯ DUY THIẾT KẾ THEO DOMAIN (DDD)

## 1. Domain-Driven Design (DDD) là gì?

**Domain-Driven Design (DDD)** là phương pháp tiếp cận thiết kế phần mềm đặt **nghiệp vụ (domain)** vào vị trí trung tâm. Thay vì chỉ bắt đầu từ bảng cơ sở dữ liệu hoặc framework, nhóm phát triển cần tìm hiểu cách doanh nghiệp vận hành, các quy tắc nghiệp vụ và khái niệm quan trọng, sau đó thể hiện chúng thành mô hình và mã nguồn.

DDD đặc biệt có giá trị với các hệ thống có **logic nghiệp vụ phức tạp**, nhiều quy tắc và thường xuyên thay đổi, chẳng hạn thương mại điện tử, ngân hàng hoặc bảo hiểm.

**Ví dụ:** Trong hệ thống bán hàng, “Đơn hàng” không chỉ là một bảng `orders` mà còn có các quy tắc như: chỉ được xác nhận khi thông tin hợp lệ, không được giao đơn đã hủy và không được hoàn tất thanh toán hai lần.

## 2. Những khái niệm cốt lõi của DDD

### 2.1. Domain và Subdomain

- **Domain:** Lĩnh vực nghiệp vụ mà hệ thống giải quyết, ví dụ thương mại điện tử.
- **Subdomain:** Các phần nghiệp vụ nhỏ hơn như quản lý đơn hàng, thanh toán, kho hàng, khuyến mãi.
- **Core Domain:** Nghiệp vụ tạo lợi thế cạnh tranh hoặc khác biệt chính cho doanh nghiệp, cần được đầu tư mô hình hóa sâu nhất. Core Domain không nhất thiết giống nhau giữa các doanh nghiệp.

### 2.2. Ubiquitous Language – Ngôn ngữ chung

Là hệ thống thuật ngữ được thống nhất giữa **người hiểu nghiệp vụ (domain expert)** và **nhóm kỹ thuật**. Những thuật ngữ này được sử dụng nhất quán khi trao đổi yêu cầu, viết tài liệu, đặt tên lớp, phương thức và kiểm thử.

**Ví dụ:** Nếu doanh nghiệp phân biệt “Đơn đã đặt”, “Đơn đã thanh toán” và “Đơn đã giao”, lập trình viên không nên dùng chung một trạng thái mơ hồ như `done`. Thay vào đó, sử dụng các tên sát nghiệp vụ như `PLACED`, `PAID`, `DELIVERED`.

**Lợi ích:** Giảm hiểu nhầm giữa khách hàng và lập trình viên, từ đó hạn chế xây dựng sai chức năng.

### 2.3. Bounded Context – Ranh giới ngữ cảnh

Mỗi **Bounded Context** là phạm vi mà một mô hình nghiệp vụ và một bộ thuật ngữ có ý nghĩa nhất quán. Các ngữ cảnh khác nhau có thể dùng cùng một từ nhưng hiểu theo cách khác nhau.

**Ví dụ:**

- Trong **Order Context**, “Customer” là người đặt hàng, có địa chỉ giao và lịch sử đơn.
- Trong **Payment Context**, khách hàng là chủ thể liên quan đến giao dịch, phương thức thanh toán và trạng thái thanh toán.

Hai context không nhất thiết dùng chung một đối tượng `Customer` chứa mọi thông tin. Mỗi context nên chỉ giữ thông tin và quy tắc cần thiết cho trách nhiệm của nó.

### 2.4. Entity, Value Object, Aggregate và Domain Event

| Khái niệm | Ý nghĩa | Ví dụ |
|---|---|---|
| **Entity** | Đối tượng có danh tính riêng, được theo dõi xuyên suốt vòng đời. | `Order` có `orderId`. |
| **Value Object** | Đối tượng được nhận diện qua giá trị, thường bất biến. | `Money(amount, currency)`, `Address`. |
| **Aggregate** | Nhóm đối tượng nghiệp vụ cần đảm bảo những quy tắc nhất quán bên trong một ranh giới giao dịch. | Aggregate `Order` quản lý các `OrderItem` và quy tắc tính tổng tiền. |
| **Aggregate Root** | Đối tượng đầu mối để bên ngoài tương tác với một Aggregate. | Thao tác thay đổi đơn hàng thông qua `Order`. |
| **Domain Event** | Sự kiện mô tả điều quan trọng đã xảy ra trong nghiệp vụ. | `OrderPlaced`, `PaymentCompleted`. |

**Lưu ý:** DDD không bắt buộc phải dùng Microservices. Các Bounded Context vẫn có thể được tổ chức thành các module trong một ứng dụng Monolith.

## 3. Ưu điểm của DDD

### 3.1. Gắn kết mã nguồn với nghiệp vụ

Lớp, phương thức và trạng thái trong code phản ánh các quy tắc kinh doanh thực tế. Điều này giúp mã nguồn dễ hiểu hơn đối với cả lập trình viên lẫn người phụ trách nghiệp vụ.

### 3.2. Thống nhất cách hiểu nhờ Ubiquitous Language

DDD khuyến khích các bên thống nhất thuật ngữ trước và trong quá trình phát triển. Nhờ vậy, các yêu cầu dễ được diễn đạt chính xác, giảm trường hợp “người dùng nói một đằng, lập trình viên hiểu một nẻo”.

### 3.3. Chia hệ thống thành các ranh giới rõ ràng

Bounded Context giúp xác định phần nào sở hữu quy tắc nào. Nếu áp dụng tốt, các module có trách nhiệm rõ hơn, giảm việc sửa một chức năng nhưng ảnh hưởng hàng loạt chức năng không liên quan.

### 3.4. Dễ kiểm thử và thay đổi quy tắc nghiệp vụ

Khi logic cốt lõi tập trung trong các mô hình nghiệp vụ thay vì rải rác ở Controller, Service và câu lệnh SQL, nhóm phát triển có thể viết unit test để kiểm tra các quy tắc quan trọng và điều chỉnh dễ hơn khi chính sách thay đổi.

### 3.5. Hỗ trợ xác định ranh giới dịch vụ nếu cần Microservices

Các Bounded Context là cơ sở hữu ích khi quyết định chia hệ thống thành dịch vụ độc lập. Tuy nhiên, **một Bounded Context không bắt buộc tương ứng đúng một Microservice**.

## 4. Nhược điểm và khó khăn khi áp dụng DDD

### 4.1. Đường cong học tập cao

Nhóm phát triển phải làm quen với nhiều khái niệm như Entity, Value Object, Aggregate, Bounded Context, Domain Event. Nếu hiểu sai, hệ thống có thể trở nên phức tạp hơn trước.

### 4.2. Tốn thời gian khám phá nghiệp vụ

DDD cần trao đổi thường xuyên với người hiểu quy trình kinh doanh. Các hoạt động như phỏng vấn, xây dựng mô hình hoặc **Event Storming** đòi hỏi thời gian và sự tham gia của nhiều bên.

### 4.3. Có thể thiết kế quá mức với dự án đơn giản

Nếu ứng dụng chỉ có chức năng CRUD cơ bản và ít quy tắc nghiệp vụ, việc áp dụng đầy đủ các mẫu DDD có thể tạo thêm lớp, đối tượng và chi phí bảo trì không cần thiết.

### 4.4. Phụ thuộc vào sự hợp tác giữa kỹ thuật và nghiệp vụ

Nếu domain expert không tham gia hoặc yêu cầu thường xuyên thiếu rõ ràng, nhóm kỹ thuật khó hình thành mô hình chính xác, làm giảm hiệu quả của DDD.

### 4.5. Khó xác định ranh giới ban đầu

Các nghiệp vụ thực tế thường liên quan với nhau. Việc xác định sai Bounded Context có thể dẫn đến giao tiếp chồng chéo, trùng lặp quy tắc hoặc một module phải biết quá nhiều về module khác.

### 4.6. Không tự động giải quyết nợ kỹ thuật

Một hệ thống đã trở thành **Big Ball of Mud** thường có cả vấn đề về chất lượng code, kiểm thử, quy trình làm việc và cách tổ chức nhóm. Chỉ đổi tên các lớp theo thuật ngữ DDD sẽ không tự động cải thiện kiến trúc.

## 5. Đề xuất áp dụng DDD cho dự án đang phình to

Theo em, Team Leader nên áp dụng **DDD theo từng bước**, thay vì viết lại toàn bộ hệ thống:

1. **Khảo sát hiện trạng:** Xác định chức năng khó bảo trì, module phụ thuộc chéo và quy tắc nghiệp vụ đang bị lặp.
2. **Làm việc với domain expert:** Thống nhất thuật ngữ, quy trình và các trường hợp ngoại lệ; có thể tổ chức workshop/Event Storming.
3. **Xác định Subdomain và Bounded Context:** Chẳng hạn Đơn hàng, Thanh toán, Kho hàng; mô tả rõ nhiệm vụ và dữ liệu thuộc từng context.
4. **Chọn một khu vực nghiệp vụ phức tạp để cải tiến trước:** Đưa các quy tắc quan trọng vào mô hình domain, tránh để Controller xử lý toàn bộ nghiệp vụ.
5. **Bổ sung kiểm thử và quy ước giao tiếp:** Viết test cho các quy tắc nghiệp vụ và định nghĩa rõ API/sự kiện trao đổi giữa các context.
6. **Đánh giá hiệu quả trước khi mở rộng:** Theo dõi thời gian sửa lỗi, tốc độ thêm tính năng và mức độ phụ thuộc giữa các module.

**Ví dụ áp dụng với Java Spring Boot:** Controller nhận request và chuyển đến lớp xử lý use case; tầng domain đảm nhiệm các quy tắc như “không được hủy đơn đã giao”; Repository hỗ trợ lưu/truy xuất dữ liệu. Cách tổ chức cụ thể có thể khác nhau, nhưng logic nghiệp vụ quan trọng không nên chỉ nằm trong Controller.

## Kết luận

**DDD không chỉ là kỹ thuật tổ chức class hay lựa chọn framework, mà là cách tư duy lấy nghiệp vụ làm trung tâm.** Giá trị lớn nhất của DDD nằm ở việc giúp nhóm hiểu đúng vấn đề kinh doanh, xây dựng ngôn ngữ chung và thiết kế các ranh giới nghiệp vụ rõ ràng. Dù tốn thời gian học tập và phân tích, DDD đặc biệt hữu ích với hệ thống có nghiệp vụ phức tạp, cần phát triển lâu dài. Với dự án đơn giản, nên áp dụng chọn lọc để tránh làm kiến trúc cồng kềnh.
