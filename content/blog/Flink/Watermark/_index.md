---
title: Watermarks, Late Elements & Allowed Lateness
type: docs
weight: 7
idtree: "flink"
---

## 1. Timestamps và Watermarks trong Event Time Windows

+ **Timestamps**: Mỗi phần tử (event) trong stream được gắn một timestamp biểu thị thời điểm sự kiện đó xảy ra. Đây là cơ sở để phân loại và xử lý các event theo thời gian thực sự (event time).

+ **Watermarks** là các giá trị timestamp đặc biệt được sinh ra định kỳ để biểu thị "tiến độ" của thời gian sự kiện trong stream. Một watermark cho biết rằng không có phần tử nào với timestamp nhỏ hơn hoặc bằng giá trị watermark đó (ngoại trừ các phần tử trễ) sẽ đến sau này.

  + *Tạo Watermark*: Watermark được tạo ra bằng hàm `assignTimestampAndWatermarks`, tức là quá trình gán timestamp và sinh watermark được thực hiện thông qua hàm này trong Flink.
  + *Không liên tục cập nhật*: Watermark không được cập nhật mỗi khi có một phần tử mới (ngay lập tức), mà được tạo ra định kỳ theo một khoảng thời gian cố định.
  + setAutoWatermarkInterval: Khoảng thời gian này được cấu hình thông qua phương thức `setAutoWatermarkInterval`, do đó watermark có thể không luôn bằng với timestamp của phần tử cuối cùng, mà phụ thuộc vào thời gian tạo watermark.
  + `Watermarks` giúp trigger của cửa sổ (window trigger) xác định thời điểm đủ điều kiện để xử lý cửa sổ.

## **2. Xử Lý Dữ Liệu Ngoài Thứ Tự (Out-of-Order Events) và Late Elements**

### **2.1. Xử lý dữ liệu ngoài thứ tự (Out-of-Order Events)**

Trong thực tế, các event thường đến không theo thứ tự do các vấn đề như độ trễ mạng hay sự chậm trễ của nguồn dữ liệu. **Watermarks** giúp hệ thống xử lý chính xác các event không theo thứ tự bằng cách "tiến độ" event time dựa trên các **watermark**.

### **2.2. Late Elements**

Là các event đến sau khi watermark đã vượt qua giá trị timestamp của chúng.

+ **Mặc định**, các late elements sẽ bị loại bỏ.
+ Tuy nhiên, Flink cho phép cấu hình **allowed latenes**s để cho phép các event trễ được xử lý:
  + **Allowed Lateness**: Xác định khoảng thời gian cho phép các event đến trễ so với watermark. Ví dụ: nếu allowed lateness là 10 giây, thì các event có timestamp nhỏ hơn watermark nhưng trong khoảng 10 giây sau đó vẫn được tính vào cửa sổ hiện hành.
  + Khi có late elements được xử lý, cửa sổ có thể "nổ" (fire) lại, nghĩa là sẽ tái tính toán và phát ra kết quả mới dựa trên dữ liệu bổ sung này. Tuy nhiên, cần chú ý rằng điều này có thể tạo ra kết quả trùng lặp, do đó cần xử lý việc hợp nhất hoặc loại bỏ các kết quả không cần thiết.

### **2.3.  Side Output cho Dữ Liệu Rất Trễ**

Trong trường hợp các event đến muộn hơn cả khoảng allowed lateness, Flink cho phép sử dụng side output để thu thập và xử lý riêng các late elements này.

Side Output:
    + Cho phép định nghĩa một kênh (tag) phụ để thu thập các phần tử không thuộc cửa sổ chính nữa.
    + Các phần tử này có thể được xử lý riêng biệt sau đó, ví dụ như lưu vào hệ thống lưu trữ log hoặc xử lý bằng các logic khác.
    + Việc này được cấu hình bằng cách sử dụng phương thức `.sideOutputLateData(tag)` sau khi thiết lập allowed lateness cho cửa sổ.










