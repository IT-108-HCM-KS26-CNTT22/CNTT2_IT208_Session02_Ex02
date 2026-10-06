# **BÀI 2: PHÂN TÍCH HIỆU NĂNG HỆ THỐNG VÀ QUẢN LÝ ĐƯỜNG DẪN TỆP**

## **1\. Phân tích vấn đề đường dẫn tệp**

### **1.1. Đường dẫn tuyệt đối là gì?**

**Đường dẫn tuyệt đối (Absolute Path)** là đường dẫn chỉ rõ vị trí đầy đủ của một file từ thư mục gốc của ổ đĩa.

Ví dụ:

C:\\Users\\Nam\\Documents\\Shopee-Lite\\assets\\logo.png

Đường dẫn này chỉ đúng khi file `logo.png` thực sự nằm tại đúng vị trí đó trên máy của Nam.

### **1.2. Tại sao đổi máy lại xảy ra lỗi FileNotFoundError?**

Khi Nam gửi dự án cho bạn, nếu chương trình sử dụng đường dẫn tuyệt đối:

logo \= Image.open("C:\\\\Users\\\\Nam\\\\Documents\\\\Shopee-Lite\\\\assets\\\\logo.png")

thì máy của người bạn có thể không có thư mục:

C:\\Users\\Nam\\Documents\\Shopee-Lite\\

Ví dụ trên máy bạn thân, dự án được đặt tại:

D:\\Projects\\Shopee-Lite\\

Do đó Python vẫn cố tìm file ở:

C:\\Users\\Nam\\Documents\\Shopee-Lite\\assets\\logo.png

nhưng file không tồn tại tại vị trí đó và phát sinh:

FileNotFoundError

**Nguyên nhân không phải do thiếu file**, mà do chương trình đang tìm file ở **sai vị trí**.

---

# **2\. Giải pháp: Sử dụng đường dẫn tương đối**

**Đường dẫn tương đối (Relative Path)** xác định vị trí file dựa trên vị trí hiện tại của dự án/chương trình thay vì phụ thuộc vào tên ổ đĩa hoặc tên người dùng.

Ví dụ cấu trúc dự án:

Shopee-Lite/  
├── main.py  
├── assets/  
│   └── logo.png  
└── src/  
    └── app.py

Nếu `main.py` cần mở:

assets/logo.png

có thể sử dụng:

logo\_path \= "./assets/logo.png"

Trong đó:

* `.` \= thư mục hiện tại.  
* `./assets/` \= thư mục `assets` nằm trong thư mục hiện tại.

Nếu file Python nằm trong thư mục `src`:

Shopee-Lite/  
├── assets/  
│   └── logo.png  
└── src/  
    └── app.py

thì từ `src` muốn quay lên thư mục cha rồi vào `assets`, có thể viết:

logo\_path \= "../assets/logo.png"

Trong đó:

* `..` \= thư mục cha.  
* `../assets/` \= đi lên một cấp rồi đi vào thư mục `assets`.

### **So sánh**

**Không nên:**

"C:\\\\Users\\\\Nam\\\\Documents\\\\Shopee-Lite\\\\assets\\\\logo.png"

**Nên:**

"./assets/logo.png"

hoặc:

"../assets/logo.png"

Cách này giúp dự án dễ di chuyển sang máy khác mà không phải sửa lại đường dẫn.

---

# **3\. Phân tích luồng IPO của ứng dụng**

IPO gồm:

* **Input – Nhập**  
* **Processing – Xử lý**  
* **Output – Xuất**

Đối với ứng dụng **Shopee-Lite**, quá trình có thể diễn ra như sau:

Storage → RAM → CPU → RAM → Output

### **Bước 1: Storage**

Mã nguồn Python, hình ảnh logo, dữ liệu sản phẩm... được lưu trên SSD/HDD.

Ví dụ:

Shopee-Lite/  
├── main.py  
├── assets/  
│   └── logo.png  
└── data/  
    └── products.json

Storage có nhiệm vụ **lưu trữ dữ liệu lâu dài**.

---

### **Bước 2: Nạp dữ liệu vào RAM**

Khi mở ứng dụng, hệ điều hành đọc chương trình và dữ liệu cần thiết từ SSD vào **RAM**.

Ví dụ:

SSD → RAM

RAM cung cấp không gian làm việc tạm thời cho chương trình đang chạy.

---

### **Bước 3: CPU xử lý**

CPU lấy các lệnh và dữ liệu cần thiết từ RAM để thực hiện.

Ví dụ:

* Đọc thông tin sản phẩm.  
* Xử lý dữ liệu.  
* Tính toán giá tiền.  
* Xử lý thao tác tìm kiếm.  
* Tải và hiển thị hình ảnh.

Có thể biểu diễn:

RAM → CPU → RAM  
---

### **Bước 4: Output**

Sau khi CPU xử lý xong, kết quả được đưa ra thiết bị xuất.

Ví dụ:

* Hiển thị logo.  
* Hiển thị danh sách sản phẩm.  
* Hiển thị giá.  
* Hiển thị kết quả tìm kiếm trên màn hình.

CPU → RAM → Màn hình  
---

# **4\. Tại sao máy bạn thân bị lag khi RAM 98%?**

Khi Task Manager báo **RAM 98%**, điều đó có nghĩa là gần như toàn bộ bộ nhớ RAM đang được sử dụng.

Ví dụ máy có 8 GB RAM:

8 GB × 98% ≈ 7,84 GB

Khi RAM gần đầy, hệ điều hành có thể phải sử dụng bộ nhớ ảo trên Storage để hỗ trợ.

Khi đó:

RAM gần đầy  
      ↓  
Thiếu không gian làm việc  
      ↓  
Sử dụng bộ nhớ ảo trên SSD  
      ↓  
Tốc độ chậm hơn RAM  
      ↓  
Ứng dụng phản hồi chậm / máy bị lag

Vì vậy, RAM 98% là một dấu hiệu quan trọng cần kiểm tra khi máy bị treo hoặc hoạt động chậm.

---

# **5\. Ba bước xử lý để giảm lag**

## **Bước 1: Kiểm tra và đóng ứng dụng chiếm nhiều RAM**

Mở **Task Manager → Processes → Memory** và sắp xếp theo mức sử dụng RAM.

Đóng những chương trình không cần thiết, đặc biệt là các ứng dụng đang chiếm nhiều RAM.

Ví dụ:

Task Manager  
→ Processes  
→ Memory  
→ Xác định ứng dụng chiếm nhiều RAM  
→ Đóng ứng dụng không cần thiết  
---

## **Bước 2: Giảm chương trình chạy nền và Startup**

Kiểm tra các chương trình tự động khởi động cùng Windows.

Vào:

Task Manager → Startup apps

Tắt các chương trình không cần thiết để giảm lượng RAM sử dụng ngay khi khởi động máy.

Không nên tắt các tiến trình hệ thống quan trọng nếu không biết rõ chức năng của chúng.

---

## **Bước 3: Kiểm tra và tối ưu ứng dụng Shopee-Lite**

Kiểm tra chương trình Python xem có đang:

* Mở quá nhiều file cùng lúc.  
* Nạp quá nhiều ảnh vào RAM.  
* Đọc dữ liệu lớn cùng một lúc.  
* Tạo quá nhiều tiến trình hoặc đối tượng không cần thiết.

Có thể tối ưu bằng cách chỉ tải dữ liệu khi cần và giải phóng tài nguyên sau khi sử dụng.

Nếu dự án thường xuyên cần nhiều RAM, có thể cân nhắc **nâng cấp RAM** nếu phần cứng của máy hỗ trợ.

---

# **6\. Kết luận**

Lỗi `FileNotFoundError` xảy ra khi chương trình sử dụng **đường dẫn tuyệt đối** phụ thuộc vào vị trí cụ thể trên máy của người lập trình. Khi chuyển dự án sang máy khác, đường dẫn đó có thể không còn tồn tại.

Giải pháp phù hợp là sử dụng **đường dẫn tương đối** như:

"./assets/logo.png"

hoặc:

"../assets/logo.png"  
