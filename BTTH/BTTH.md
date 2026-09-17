## NHIỆM VỤ 1: NHẬN DIỆN LỚP VÀ KHAI BÁO CẤU TRÚC 3 NGĂN (LESSON 01)
Hệ thống nhận diện 4 Lớp đối tượng cốt lõi đại diện cho các thực thể quan trọng nhất trong chu trình vận hành. Cấu trúc mỗi Lớp được chuẩn hóa theo 3 ngăn UML:
1. **Ngăn 1 (Class Name):** Định danh Lớp dạng danh từ viết hoa theo quy tắc PascalCase.
2. **Ngăn 2 (Attributes):** Chứa các thuộc tính dữ liệu mô tả trạng thái của thực thể.
3. **Ngăn 3 (Operations/Methods):** Chứa các hành vi nghiệp vụ và phương thức thao tác dữ liệu.

###1. Lớp Khách hàng (`Customer`)
- **Tên Lớp:** `Customer`
- **Thuộc tính:**
  - `customerId: String`
  - `fullName: String`
  - `phoneNumber: String`
  - `email: String`
  - `deliveryAddress: String`
- **Phương thức:**
  - `getCustomerId(): String`
  - `getDeliveryAddress(): String`
  - `updateProfile(name: String, phone: String, address: String): boolean`
  - `placeOrder(): Order`

###2. Lớp Đơn hàng (`Order`)
- **Tên Lớp:** `Order`
- **Thuộc tính:**
  - `orderId: String`
  - `orderDate: DateTime`
  - `totalAmount: double`
  - `finalAmount: double`
  - `status: OrderStatus`
- **Phương thức:**
  - `getOrderId(): String`
  - `calculateTotal(): double`
  - `applyVoucher(voucher: Voucher): boolean`
  - `cancelOrder(): boolean`

###3. Lớp Món ăn (`MenuItem`)
- **Tên Lớp:** `MenuItem`
- **Thuộc tính:**
  - `itemId: String`
  - `itemName: String`
  - `basePrice: double`
  - `isAvailable: boolean`
- **Phương thức:**
  - `getItemId(): String`
  - `getPrice(): double`
  - `updateAvailability(status: boolean): void`

###4. Lớp Nhân viên (`Employee`)
- **Tên Lớp:** `Employee`
- **Thuộc tính:**
  - `employeeId: String`
  - `fullName: String`
  - `citizenId: String`
  - `phoneNumber: String`
  - `salary: double`
- **Phương thức:**
  - `getEmployeeId(): String`
  - `getSalary(): double`
  - `checkIn(): void`
  - `updateContact(phone: String): void`

---

##NHIỆM VỤ 2: THIẾT LẬP BỔ TỪ TRUY CẬP VÀ BẢO VỆ DỮ LIỆU (LESSON 02)
Để giải quyết bài toán rò rỉ và can thiệp trái phép vào dữ liệu nhạy cảm, nguyên lý Đóng gói (Encapsulation) được áp dụng triệt để:
- **Private (`-`):** Ẩn toàn bộ biến thành viên để không thể truy cập trực tiếp từ bên ngoài.
- **Public (`+`):** Cung cấp giao diện truy xuất và thực thi nghiệp vụ có kiểm soát thông qua Getter và Business Methods.

###1. Đặc tả Chi tiết Lớp `Customer`
```text
+-----------------------------------------------------------------------+
|                               Customer                                |
+-----------------------------------------------------------------------+
| - customerId: String                                                  |
| - fullName: String                                                    |
| - phoneNumber: String                                                 |
| - email: String                                                       |
| - deliveryAddress: String                                             |
+-----------------------------------------------------------------------+
| + getCustomerId(): String                                             |
| + getFullName(): String                                               |
| + getPhoneNumber(): String                                            |
| + getDeliveryAddress(): String                                        |
| + updateProfile(name: String, phone: String, address: String): boolean|
| + placeOrder(): Order                                                 |
+-----------------------------------------------------------------------+
```
*Mục đích bảo vệ:* Giữ bí mật số điện thoại, email và địa chỉ nhà của khách hàng trước các module không có thẩm quyền truy xuất trực tiếp.

###2. Đặc tả Chi tiết Lớp `Employee`
```text
+-----------------------------------------------------------------------+
|                               Employee                                |
+-----------------------------------------------------------------------+
| - employeeId: String                                                  |
| - fullName: String                                                    |
| - citizenId: String                                                   |
| - phoneNumber: String                                                 |
| - salary: double                                                      |
+-----------------------------------------------------------------------+
| + getEmployeeId(): String                                             |
| + getFullName(): String                                               |
| + getSalary(): double                                                 |
| + checkIn(): void                                                     |
| + updateContact(phone: String): void                                  |
+-----------------------------------------------------------------------+
```
*Mục đích bảo vệ:* Thuộc tính căn cước công dân (`citizenId`) và mức lương (`salary`) là dữ liệu cá nhân mật, chỉ được phép đọc qua getter có xác thực quyền hạn.

---

##NHIỆM VỤ 3: PHÂN TÍCH VÀ LỰA CHỌN MỐI QUAN HỆ GIỮA CÁC LỚP (LESSON 03)

| Cặp liên kết | Loại quan hệ UML | Ký hiệu UML | Lý do lựa chọn và Phân tích bản chất kỹ thuật |
| :--- | :--- | :---: | :--- |
| **Category – MenuItem** | **Aggregation** *(Tập hợp)* | `o--` *(Hình thoi rỗng tại Category)* | **Liên kết lỏng lẻo (Loose coupling):** `Category` nhóm các món ăn lại thành danh mục (ví dụ: "Đồ uống", "Món chay"). Khi một danh mục bị xóa khỏi hệ thống, các món ăn thuộc danh mục đó vẫn tồn tại độc lập trong cơ sở dữ liệu và có thể được tái chỉ định sang danh mục khác. Vòng đời của `MenuItem` không gắn chết với `Category`. |
| **Order – OrderItem** | **Composition** *(Cấu thành sinh tử)* | `*--` *(Hình thoi đặc tại Order)* | **Liên kết sinh tử (Strong ownership):** Mỗi dòng đặt món (`OrderItem`) chỉ có nghĩa khi nằm trong một đơn hàng (`Order`) cụ thể. Nếu đơn hàng bị xóa vật lý hoặc hủy bỏ hoàn toàn khỏi hệ thống, tất cả các `OrderItem` cấu thành nên đơn hàng đó phải bị hủy đồng thời. Vòng đời của `OrderItem` phụ thuộc hoàn toàn vào `Order`. |
| **Employee – Shipper** | **Generalization** *(Kế thừa)* | `--\|>` *(Tam giác rỗng trỏ về Employee)* | **Mối quan hệ IS-A:** Tài xế giao hàng (`Shipper`) bản chất là một Nhân viên (`Employee`) của hệ thống QuickBite. `Shipper` kế thừa toàn bộ thuộc tính và phương thức cơ bản (`employeeId`, `fullName`, `salary`, `checkIn()`), đồng thời mở rộng thêm các thuộc tính chuyên biệt: biển số xe (`licensePlate`), vị trí GPS (`currentLocation`). |

---

##NHIỆM VỤ 4: XÁC ĐỊNH BỘI SỐ VÀ KHÓA CHẶT RÀNG BUỘC SỐ LƯỢNG (LESSON 04)

###1. Bảng xác định Bội số (Multiplicity)
| Cặp liên kết | Đầu A | Bội số A | Đầu B | Bội số B | Ý nghĩa kỹ thuật |
| :--- | :---: | :---: | :---: | :---: | :--- |
| **Customer – Order** | `Customer` | `1` | `Order` | `0..*` | Một khách hàng tại thời điểm khởi tạo có thể chưa có đơn nào (`0`), sau đó có thể tích lũy nhiều đơn (`*`). Một đơn hàng bắt buộc phải gắn với duy nhất 1 khách hàng (`1`). |
| **Order – OrderItem** | `Order` | `1` | `OrderItem` | `1..*` | Một đơn hàng bắt buộc phải có từ 1 đến nhiều dòng món ăn (`1..*`). Mỗi dòng món ăn chỉ thuộc về đúng 1 đơn hàng (`1`). |
| **Order – Voucher** | `Order` | `*` | `Voucher` | `0..1` | Một đơn hàng có thể không dùng voucher (`0`) hoặc dùng tối đa 1 voucher (`1`). Một voucher có thể được áp dụng cho nhiều đơn hàng khác nhau (`*`). |

###2. Giải thích cơ chế ràng buộc chống lỗi nghiệp vụ
1. **Khóa chặt lỗi Đơn hàng rỗng (0 món) bằng Bội số `1..*`:**
   - *Vấn đề nghiệp vụ:* Khi người dùng thêm món vào giỏ hàng rồi xóa sạch nhưng vẫn bấm thanh toán, hoặc do lỗi mạng khiến mảng món ăn bị rỗng khi gọi API tạo đơn.
   - *Cơ chế chặn:* Khai báo bội số `1..*` ở phía `OrderItem` đóng vai trò là một bất biến (Invariant) trong kiến trúc hướng đối tượng. Tầng Domain Entity / Service sẽ ném ngoại lệ (Exception) hoặc từ chối tạo đối tượng `Order` nếu danh sách `OrderItem` có độ dài `size < 1`.
2. **Khóa chặt gian lận khuyến mại bằng Bội số `0..1`:**
   - *Vấn đề nghiệp vụ:* Tình trạng người dùng lạm dụng áp dụng đồng thời nhiều voucher giảm giá (coupon stacking) khiến giá trị thanh toán bị âm hoặc giảm quá mức cho phép.
   - *Cơ chế chặn:* Thuộc tính liên kết trong `Order` được thiết kế dưới dạng tham chiếu đơn `voucher: Voucher?` (hoặc `nullable object`) thay vì danh sách `List<Voucher>`, đảm bảo ở mức cấu trúc dữ liệu rằng một đơn hàng không bao giờ chứa nhiều hơn 1 mã giảm giá.

---

##NHIỆM VỤ 5: CHUYỂN ĐỔI KỊCH BẢN ĐẶC TẢ USE CASE SANG CLASS DIAGRAM (LESSON 05)

###1. Bối cảnh kịch bản Use Case: "Thanh toán Đơn hàng trực tuyến"
> "Khách hàng xác nhận thông tin **Đơn hàng (Order)** đã bao gồm các **Dòng món ăn (OrderItem)** và tổng giá tiền. Khách hàng lựa chọn phương thức **Thanh toán (Payment)** trực tuyến. Hệ thống thực hiện ủy quyền trừ tiền qua cổng thanh toán và tự động phát hành **Hóa đơn điện tử (Receipt)** ghi nhận đầy đủ thuế VAT và mã số giao dịch."

###2. Bảng Phân tích Trích xuất Danh từ / Động từ (Noun / Verb Analysis)

| Từ vựng trong Kịch bản | Bản chất Ngữ pháp | Phân loại UML tương ứng | Chi tiết Ánh xạ vào Mô hình Lớp |
| :--- | :--- | :--- | :--- |
| **Đơn hàng (Order)** | Danh từ riêng/Chủ thể | **Lớp (Class)** | Lớp `Order` |
| **Dòng món ăn (OrderItem)** | Danh từ riêng/Thành phần | **Lớp (Class)** | Lớp `OrderItem` |
| **Thanh toán (Payment)** | Danh từ/Giao dịch | **Lớp (Class)** | Lớp `Payment` |
| **Hóa đơn (Receipt)** | Danh từ/Chứng từ | **Lớp (Class)** | Lớp `Receipt` |
| Mã thanh toán, số tiền, ngày tạo, phương thức | Cụm danh từ mô tả | **Thuộc tính (Attributes)** | `paymentId`, `amount`, `paymentDate`, `paymentMethod` (trong `Payment`) |
| Mã hóa đơn, thuế VAT, thời gian xuất | Cụm danh từ mô tả | **Thuộc tính (Attributes)** | `receiptId`, `taxAmount`, `finalTotal`, `issuedDate` (trong `Receipt`) |
| Tính tổng tiền, áp dụng khuyến mãi | Động từ hành động | **Phương thức (Methods)** | `calculateTotal()`, `applyVoucher()` (trong `Order`) |
| Xử lý thanh toán, hoàn tiền | Động từ xử lý giao dịch | **Phương thức (Methods)** | `processPayment()`, `refund()` (trong `Payment`) |
| Xuất hóa đơn, in hóa đơn | Động từ nghiệp vụ chứng từ | **Phương thức (Methods)** | `generateReceipt()`, `printReceipt()` (trong `Receipt`) |

###3. Phác thảo Cấu trúc 4 Lớp trích xuất từ Use Case

#### Lớp `Order`
```text
+---------------------------------------+
|                 Order                 |
+---------------------------------------+
| - orderId: String                     |
| - orderDate: DateTime                 |
| - totalAmount: double                 |
| - finalAmount: double                 |
| - status: OrderStatus                 |
+---------------------------------------+
| + calculateTotal(): double            |
| + applyVoucher(voucher: Voucher): bool|
| + cancelOrder(): boolean              |
+---------------------------------------+
```

#### Lớp `OrderItem`
```text
+---------------------------------------+
|               OrderItem               |
+---------------------------------------+
| - orderItemId: String                 |
| - quantity: int                       |
| - priceAtPurchase: double             |
| - note: String                        |
+---------------------------------------+
| + getSubTotal(): double               |
+---------------------------------------+
```

#### Lớp `Payment`
```text
+---------------------------------------+
|                Payment                |
+---------------------------------------+
| - paymentId: String                   |
| - amount: double                      |
| - paymentMethod: String               |
| - paymentDate: DateTime               |
| - status: PaymentStatus               |
+---------------------------------------+
| + processPayment(): boolean           |
| + refund(): boolean                   |
| + getPaymentStatus(): PaymentStatus   |
+---------------------------------------+
```

#### Lớp `Receipt`
```text
+---------------------------------------+
|                Receipt                |
+---------------------------------------+
| - receiptId: String                   |
| - taxAmount: double                   |
| - finalTotal: double                  |
| - issuedDate: DateTime                |
+---------------------------------------+
| + generateReceipt(): void             |
| + printReceipt(): String              |
+---------------------------------------+
```

###Mối liên kết giữa 4 Lớp trích xuất
1. `Order` **1** `*--` **1..\*** `OrderItem` *(Composition)*: Đơn hàng sở hữu sinh tử các dòng đặt món.
2. `Order` **1** `--` **1** `Payment` *(Association)*: Mỗi đơn hàng gắn liền với duy nhất một giao dịch thanh toán.
3. `Payment` **1** `..>` **1** `Receipt` *(Dependency/Association)*: Khi thanh toán thành công, hệ thống sinh ra một Hóa đơn điện tử tương ứng phục vụ đối soát và gửi cho khách hàng.
