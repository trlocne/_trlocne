---
title: Object Detection (Part 3 - YOLO family)
blog: true
weight: 5
math: true
comments: true
---

## 1. YOLOv1

### 1.1. One-stage vs. Two-stage

{{% details title="Nội dung" closed="true" %}}
Trong lĩnh vực phát hiện đối tượng (object detection), các mô hình thường được chia thành hai nhóm chính: Two-stage detectors và One-stage detectors.

![alt text](image.png)

+ **Two-stage detectors** như Faster R-CNN hoạt động theo hai bước:
Bước đầu tiên là tạo ra các vùng đề xuất (region proposals) – những khu vực có khả năng chứa đối tượng, thông qua một mạng gọi là Region Proposal Network (RPN). Sau đó, những vùng này được cắt ra từ feature map và đưa vào một mạng con khác để phân loại đối tượng và tinh chỉnh lại vị trí bounding box.
Ưu điểm của phương pháp này là độ chính xác cao, đặc biệt với các đối tượng nhỏ, nhưng tốc độ xử lý thường chậm hơn.

+  **One-stage detectors** như YOLO, SSD thực hiện việc phân loại và hồi quy bounding box trực tiếp trên toàn bộ feature map, bỏ qua bước sinh region proposal. Mỗi ô trên feature map sẽ chịu trách nhiệm dự đoán một hoặc nhiều hộp chứa đối tượng với các tọa độ và nhãn lớp.
Phương pháp này thường nhanh hơn đáng kể, phù hợp với ứng dụng thời gian thực, tuy nhiên có thể hy sinh một phần độ chính xác, đặc biệt ở các object nhỏ hoặc bị che khuất.

{{% /details %}}

![alt text](image-1.png 'Object Detection Milestones')

### 1.2. Motivation

{{% details title="Lịch sử phát triển" closed="true" %}}
Vào năm 2015, **Joseph Redmon** (Đại học washington) đã phát triển mô hình YOLO (You Only Look Once), như một giải pháp phát hiện đối tượng nhanh chóng và hiệu quả hơn. Cùng thời điểm đó, **Ross Girshick** (Microsoft Research) cũng công bố mô hình Faster R-CNN. Cả hai phương pháp đều sử dụng các lớp tích chập (convolutional layers) để trích xuất đặc trưng từ ảnh đầu vào, tuy nhiên cách tiếp cận rất khác nhau.

Faster R-CNN là một mô hình hai giai đoạn (two-stage), trong đó bước đầu tiên tạo ra các vùng đề xuất (region proposals) trước khi thực hiện phân loại và hồi quy vị trí. Trong khi đó, YOLO là một mô hình một giai đoạn (one-stage), không có bước đề xuất vùng riêng biệt, mà thực hiện phân loại và định vị trực tiếp trên toàn bộ ảnh. Điều này giúp YOLO nhanh hơn đáng kể so với Faster R-CNN, mặc dù ban đầu độ chính xác không cao bằng.

{{% /details %}}

![alt text](image-2.png 'Chỉ sử dụng một stage duy nhất để predict object detection')

### 1.3. Step in YOlO


#### Chia ảnh thành grid cells
Chia ảnh đầu vào thành $SxS$ ô (grid cells). Mỗi ô sẽ dự đoán 2 bouding box. Các grid này không overlap với nhau và thường có size là $7x7$.

{{% details title="Example" closed="true" %}}
Ví dụ image có size là $28x28$ thì ta sẽ có 10 cái patch có size $7x7$.

![alt text](image-3.png)


{{% /details %}}

#### Bouding box prediction

Mỗi bouding box sẽ có 5 tham số: $x, y, w, h, confidence score$

Các tham số này có thể được biểu diễn theo nhiều các khác nhau:

+ Tạo độ tuyệt đối (absolute coordinates):
    - $x$ và $y$ là tọa độ của tâm bouding box trong ô (grid cell) đó.
    - $w$ và $h$ là chiều rộng và chiều cao của bouding box.
+ Tọa độ tương đối (relative coordinates):
  + (x, y) có thể là tọa độ tương đối so với một điểm mốc nào đó (ví dụ như tâm của grid cell trong YOLO).
  + (w, h) có thể là tỷ lệ so với chiều rộng/chiều cao của ảnh hoặc một vùng nhất định
+ Offset(độ lệch): Trong một số mô hình, ta dự đoán offset (độ lệch của tâm) bouding box và kích thước của nó so với một anchor box (khung chữ nhật) được xác định trước.

{{% details title="Example" closed="true" %}}
Giả sử bạn có một ảnh với size $500\times 300$ pixel. Bounding box của một vật thể có thể được biểu diễn là:

- **Tọa độ tuyệt đối:** (x=100, y=50, w=200, h=150)
- **Tọa độ tương đối:** (x=0.2, y=0.167, w=0.4, h=0.5) (tỉ lệ so với chiều rộng/cao của ảnh)

Và cuối cùng là **Confidence score** được tính dựa trên công thức:

$$
\text{p(Object)} \times \text{IoU}^{\text{truth}}_{\text{predict}}
$$

Trong đó: 

- $\text{p(Object)}$ là xác suất dự đoán đối tượng (có được thông qua hàm Softmax ở layer cuối).

![alt text](image-4.png)

{{% /details %}}


#### Label Encoding

**Binary vector** với độ dài = số class cần dự đoán. Ví dụ 10 class thì vector có size là 10.

Ta sẽ có 2 giá trị, nếu model nhận diện được class nào thì giá trị tại class đó sẽ là 1, còn lại là 0.

![alt text](image-5.png)


#### Ouput Yolo gồm những gì?

Vector gồm 3 thành phần:
1. Confidence score - $p$
2. Tọa độ bounding box $B$
3. Vector label encoding

Ví dụ bên dưới class 1 là dog, class 2 là cat. Model nhận diện được cat nên class 2 = 1, kèm theo đó là confidence score và tọa độ bounding box tương ứng.

![alt text](image-6.png)

{{% details title="Việc thực hiện Selective search để xác định Region proposal có cần thiết không ?" closed="true" %}}
Không cần vì các ảnh đã tách thành từng Patch, sau đó xác định xem cell nào chịu trách nhiệm chính để detect Object đó.


*Cách xác định cell nào chịu trách nhiệm chính:*

+ ![alt text](image-7.png)

{{% /details %}}


{{< callout type="info" >}}
  Trong mỗi cell thay vì dự đoán 1 bouding box thì YOLOv1 sử dụng 2 cái bouding box

  ![alt text](image-8.png)

  Do sử dụng 2 bounding box nên ta sẽ xem xét cái nào có highest confidence score để giữ lại. Mình đoán vậy
{{< /callout >}}


#### Summary

![alt text](image-9.png)


### 1.4. YOLOv1 Architecture

**YOLOv1** dựa trên kiến trúc của **GoogleNet**, ta có thể điều chỉnh bằng cách tăng/giảm số layers, neurons tùy ý. Việc bê kiến trúc nhưng điều chỉnh layers như vậy gọi chung là **DarkNet**

![alt text](image-10.png)


![alt text](image-12.png)


#### Training process

![alt text](image-11.png)


#### Loss function

Điểm đặc biệt của **YOLOv1** nằm ở hàm loss, với mỗi grid cell sẽ có loss tương ứng -> ta sẽ đặt trọng số cho mỗi cell.

Dựa trên ground truth mà ta sẽ tính được cell đó có object hay không mà sử dụng trọng số tương ứng.

![alt text](image-13.png)


{{< callout type="warning" >}}
  Vậy loss cho từng cell được tính như nào?

Đây là công thức cụ thể.
![alt text](image-17.png)
{{< /callout >}}


{{% details title="Chi tiết về từng phần ở trong hàm loss và giải thích" closed="true" %}}
![alt text](image-15.png)

![alt text](image-16.png)


{{% details title="Cách tính Classification Loss" closed="true" %}}
![alt text](image-22.png)

Vector ở trên là ground truth, ở dưới là predict

![alt text](image-19.png)

{{% /details %}}

{{% details title="Cách tính Confidence score" closed="true" %}}

![alt text](image-21.png)
![alt text](image-20.png)

Trọng số của grid cell là 0.5 bởi vì để focus tập trung vào những thằng detect Object hơn.

{{% /details %}}

{{% details title="Cách tính Localization loss" closed="true" %}}

![alt text](image-23.png)

![alt text](image-24.png)


{{% details title="Tại sao công thức Localization loss, term thứ 2 có căn bậc 2 trọng số?" closed="true" %}}
Những **bounding box nhỏ** sẽ có lỗi ít hơn trong khi những **bounding box lớn** cover được nhiều thông tin object hơn lại thường có loss lớn hơn. Điều này có vẻ không fair bờ lay cho lắm.

Nên ta sẽ để dưới dạng căn bậc 2 như một cách regularization để giảm bớt loss cho bounding box lớn.

![alt text](image-25.png)

{{% /details %}}

{{% /details %}}

{{% /details %}}

#### Label dataset

{{% details title="Với mỗi bộ dataset, người ta đã thực hiện format tọa độ các ground truth bounding box một cách khác nhau." closed="true" %}}

<div style="display: flex; justify-content: space-between;">
<img src="image-26.png" alt="alt text" style="width: 200px;"/>
<img src="image-27.png" alt="alt text" style="width: 200px;"/>
</div>

![alt text](image-28.png)

{{% details title="Một số thông tin có trong **Pascal VOC Dataset Format**" closed="true" %}}
Có **2 trường thông tin**, 1 là thông tin về bức ảnh, bao gồm chiều dài, chiều rộng và depth, thường là 3 (RGB)

<img src="image-29.png" alt="alt text" style="width: 200px;"/>

Thứ 2 là thông tin ground truth về bounding box, bao gồm class, tọa độ 2 điểm góc trái trên và góc phải dưới.

<img src="image-30.png" alt="alt text" style="width: 200px;"/>


{{% /details %}}
{{% /details %}}

{{% details title="Thông tin grid cell sau khi rescale bounding box về tọa độ tương đối." closed="true" %}}
![alt text](image-31.png)

{{% details title="Cho ví dụ minh họa output target sau khi training" closed="true" %}}
![alt text](image-32.png)

![alt text](image-33.png)

{{% /details %}}

{{% /details %}}


### 1.5. Summary

![alt text](image-34.png)

### 1.6. Advantages and Disadvantages

1. Mỗi cell dự đoán đến **2 bounding box**, vậy nếu chia image thành 7x7 = 49 Patch → 98 bounding box → lựa ra 49 highest box → **giới hạn lại khả năng của model**.
2. Không detect được object quá nhỏ, YOLOv1 chưa handle được multi-scale.

![alt text](image-35.png)

## 2. Yolov2

### 2.1. Motivation

+ Yolo v1 nhanh hơn Faster R-CNN, nhưng độ chính xác thì thấp hơn 
+ Điểm yếu của YOLO v1 nằm ở độ chính xác của bounding box. Nó không dự đoán tốt vị trí và kích thước của vật thể, đặc biệt là rất kém khi phát hiện vật thể nhỏ.
+ SSD một mô hình phát triển vật thể single object, đã lập kỷ lục mới khi **Chính xác hơn faster RCNN và cả Yolov1**

### 2.2. Improvements

#### Áp dụng BatchNorm

![alt text](image-36.png)

#### Tăng resolution = cách resize ảnh lên

![alt text](image-37.png)

![alt text](image-38.png)


#### Bỏ các lớp FC, sử dụng hoàn toàn CNN.

#### Bỏ luôn Max-Pool, thay vì chia image thành 7x7 Patch, chia nhỏ hơn thành 14x14 Patch

![alt text](image-39.png)

{{% details title="Tại sao họ đổi từ 14x14 → 13x13?" closed="true" %}}
Sử dụng số chẵn thì không chọn được center thuộc về cell nào để chọn ra cell quan trọng nhất.

![alt text](image-40.png)

{{% /details %}}

#### Apply skip connection

Size không bằng nhau, cái trước $26x26$, cái sau $13x13$.

![alt text](image-41.png)

{{% details title="Để giải quyết vấn đề trên họ sử dụng gì?" closed="true" %}}
Concatenate channel-wise.

![alt text](image-42.png)

Chia cái 26 thành 4 phần, mỗi phần có size 13x13 và depth là $c_1$

<img src="image-43.png" alt="alt text" style="width: 200px;"/>

Sau đó concate lại, tương đương với tăng depth lên 4 lần → $4c_1$.
<img src="image-44.png" alt="alt text" style="width: 200px;"/>

Lúc này đã có thể skip-connection bình thường, depth = $4c_1 + c_2$.

![alt text](image-45.png)

{{% /details %}}


{{< callout type="info" >}}
  Tóm lại idea chính cần nhớ của YOLOv2 là bỏ hoàn toàn FC, chỉ sử dụng CNN.
{{< /callout >}}

{{< callout type="error" >}}
Ở phần trước chúng ta đã biết một image có thể có nhiều object, và các object này có thể bị overlap với nhau. YOLO không sử dụng Selective Search hay Sliding window nên không thể handle điều này?
  
Cách giải quyết là sử dụng Anchor box. Mỗi object sẽ có nhiều anchor box với tỉ lệ khác nhau.
{{< /callout >}}


### 2.3. Anchor box

Sử dụng K-means, số lượng anchor box thì dùn ELBOW method (chạy thực nghiệm) như bình thường.

![alt text](image-46.png)

Trước đó Faster R-CNN dự đoán $\Delta x$, $\Delta y$

Giờ họ cộng thêm giá trị đó nhưng đưa qua hàm sigmoid $\sigma$  để đưa các giá trị này về khoảng 0 → 1 giúp optimization loss smooth hơn. Còn tại sao nó làm được smooth hơn thì mình chưa hiểu.

Ngoài ra cần chú ý vào cái bw, bh, chỗ này chưa hiểu nên thôi skip.

![alt text](image-47.png)


## 3. Summary Yolov1 vs Yolov2

### 3.1. Yolov1 vs Yolov2
YOLOv2 bỏ FC + thêm vào 5 anchor cho mỗi cell và tỉ lệ được xác định bằng K-Means.

![alt text](image-48.png)

![alt text](image-49.png)


### 3.2. So sánh loss function

![alt text](image-50.png)

### 3.3. Một số bài báo transformer trong object detection

![alt text](image-51.png)
## Resource

{{% details title="YOLO" closed="true" %}}
{{< pdf "Slide.pdf" >}}
{{% /details %}}


