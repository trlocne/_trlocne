---
title: Model Generalization
type: docs
weight: 3
sidebar:
  open: true
tags:
  - CNN
  - MLP
  - Deep Learning
math: true
comments: true
password: aiovn
blog: true
---

## 1. Motivation

{{% details title="Nhắc lại về CNN training" closed="true" %}}

Buổi hôm trước chúng ta thảo luận về việc huấn luyện mô hình. generalization là một khái niệm quan trọng trong deep learning. Một mô hình được coi là generalization tốt khi nó có khả năng dự đoán tốt trên dữ liệu mới mà nó chưa từng thấy. Hiện tại kiến trúc buổi trước tập huấn luyện đạt được độ chính xác cao nhưng khi áp dụng vào tập test thì kết quả không tốt. Điều này chứng tỏ mô hình không generalization tốt.

![alt text](image.png)

{{% /details %}}

{{< callout type="info" >}}
  Mục tiêu của bài này sẽ cãi thiện độ chính xác trên tập kiểm tra (test accuracy) và giảm khoảng cách giữa độ chính xác trên tập huấn luyện và tập kiểm tra.
{{< /callout >}}

## 2. Problem

![alt text](image-1.png)

{{% details title="Trước khi **huấn luyện**" closed="true" %}}
+ **Mô hình** bắt đầu ở trạng thái **ngẫu nhiên** với các **biên phân tách (decision boundary)** **không chính xác**.  
+ Đây là **điểm khởi đầu** của **mô hình**, **mô hình** **không thể dự đoán chính xác** trên tập **huấn luyện** và **tập kiểm tra**.
{{% /details %}}

{{% details title="Giai đoạn **huấn luyện**" closed="true" %}}
+ **Mô hình** dần dần **cải thiện**, **học được** các **xu hướng chính** của tập dữ liệu.  
+ **Biên phân tách** trở nên **hợp lý** hơn, phản ánh tốt các **đặc điểm** của dữ liệu.
{{% /details %}}

{{% details title="**Huấn luyện** tối ưu - **robust fit**" closed="true" %}}
+ **Mô hình** đã **học được** các **đặc điểm chính** của tập dữ liệu, **biên phân tách** trở nên **chính xác**.  
+ Tại trạng thái này, **mô hình** có khả năng **tổng quát hóa (generalization)** **tốt**, tức là có **khả năng dự đoán tốt** trên **dữ liệu mới**.
{{% /details %}}

{{% details title="Trạng thái cuối cùng - **overfitting**" closed="true" %}}
+ **Mô hình** **huấn luyện** **quá mức** dẫn đến việc **học "quá mức"** các **đặc điểm** của tập dữ liệu.  
+ **Biên phân tách** trở nên **quá phức tạp**, **mô hình** không thể **tổng quát hóa** **tốt** trên **dữ liệu mới**.
{{% /details %}}


## 3. Solution

### 3.1. Trick 1: 'learn hard' - randomly add noise to training data

{{% details title="Motivation" closed="true" %}}
Nếu dữ liệu quá sạch thì mô hình không thể học được các đặc trưng phức tạp hoặc khó khăn. Khi gặp dữ liệu thử nghiệm chứa nhiều nhiễu hoặc khó hơn, mô hình dễ bị sai (fail)
![alt text](image-2.png)
{{% /details %}}

{{% details title="Kỹ thuật 'learn hard'" closed="true" %}}
+ **Thêm nhiễu** vào dữ liệu huấn luyện, giúp mô hình **học được** các **đặc điểm phức tạp** hơn. 
![alt text](image-3.png)

{{% /details %}}


{{% details title="Code" closed="true" %}}
```python
transforms.Compose([
    transforms.ToTensor(),
    transforms.Normalize([0.4914, 0.4822, 0.4465], 
                         [0.2470, 0.2435, 0.2616]),
    transforms.RandomErasing(
    p=0.75,          # Xác suất áp dụng Random Erasing (75% ảnh sẽ bị xóa ngẫu nhiên một phần)
    scale=(0.01, 0.3),  # Kích thước phần bị xóa: tối thiểu 1% - tối đa 30% diện tích ảnh
    ratio=(1.0, 1.0),   # Tỷ lệ khung hình (width/height) của vùng bị xóa luôn là 1.0 (hình vuông)
    value=0,           # Giá trị pixel thay thế (0 tức là tô màu đen)
    inplace=True       # Thay đổi ảnh gốc thay vì tạo bản sao mới
)
])
```
{{% /details %}}

{{% details title="Result" closed="true" %}}
![alt text](image-4.png)
{{% /details %}}

### 3.2. Trick 2: Batch Normalization

{{% details title="Target" closed="true" %}}
+ Batch Normalization giúp giảm thiểu **Internal Coveriate Shift**, một vấn đề xáy ra khi phân phối của các đặc trưng thay đổi liên tục trong qúa trình huấn luyện do sự cập nhật trọng số lần trước đó. Bằng cách chuẩn hóa giá trị đầu vào của mỗi lớp, Batch Normalization giúp mô hình học nhanh hơn và hiệu quả hơn, duy trì sự ổn định của phân phối dữ liệu, giúp các lơp phía sau không cần phải liên tục điều chỉnh để thích nghi. Điều này giúp mô hình tăng tốc độ hội tụ và làm quá trình huấn luyện hiệu quả hơn.

+ Batch Normalization vô tình thêm nhiễu (noise) vào quá trình huấn luyện trong mean và variancr giữa các mini-batch. Nhiễu này hoạt động như một dạng **regularization**, làm giảm nguy cơ overfitting. Nhờ đó mô hình không chỉ hoạt động tốt trên tập huấn luyện mà còn cải thiện khả năng tổng quát hóa trên tập kiểm tra, đảm bảo hiệu suất cao hơn khi gặp dữ liệu chưa từng thấy.

<div style='display: flex'>
<img src="image-5.png" alt="alt text" width="45%"/>
<img src="image-6.png" alt="alt text" width="45%"/>
</div>

{{% details title="Tại sao batch_normalize lại giúp tăng accuracy của test (ở những bài trước thì mình thảo luận nó giúp tăng ở tập train)" closed="true" %}}
Bởi vì mỗi lần chuẩn hóa, dữ liệu sẽ bị biến đổi, và sự biến đổi này khác nhau sau mỗi epoch do sự thay đổi mean và variance giữa các mini-batch. Điều này làm cho các feature map thay đổi liên tục qua từng epoch, đồng thời thêm một lượng nhiễu tự nhiên vào quá trình huấn luyện. Nhờ đó, mô hình không chỉ học được các đặc trưng chính xác của tập huấn luyện mà còn học được cách xử lý dữ liệu nhiễu, giúp tăng khả năng tổng quát hóa của mô hình.
![alt text](image-7.png)
![alt text](image-8.png)
{{% /details %}}

{{% /details %}}

{{% details title="Backward" closed="true" %}}
Từ công thức chúng ta có thể mô hình hóa như sau đây:

![alt text](image-19.png)

Kết quả của tính backward

![alt text](image-20.png)

{{% /details %}}

{{% details title="Result" closed="true" %}}
![alt text](image-9.png)
{{% /details %}}

### 3.3. Trick 3: Dropout

{{% details title="Target" closed="true" %}}
+ **Dropout** là một kỹ thuật regularization phổ biến trong deep learning. Ý tưởng của Dropout là loại bỏ ngẫu nhiên một số lượng node trong mạng neural network trong quá trình huấn luyện. Điều này giúp mô hình trở nên **robust** hơn, giảm nguy cơ overfitting, tăng khả năng tổng quát hóa của mô hình.

+ **Dropout** giúp mô hình học được các đặc trưng chính xác của dữ liệu, đồng thời giảm khả năng mô hình học "quá mức" các đặc trưng của tập huấn luyện. Điều này giúp mô hình có khả năng dự đoán tốt trên dữ liệu mới, giảm khoảng cách giữa độ chính xác trên tập huấn luyện và tập kiểm tra.

![alt text](image-11.png)
{{% /details %}}

{{% details title="Deeply in Dropout" closed="true" %}}
{{% details title="How dropout works mathematically" closed="true" %}}

<img src="image-12.png" alt="alt text" width="300px"/>

Pytorch sẽ sinh ra D có giá trị = {0,1}, dot hadamard với giá trị của layer đó.

<img src="image-13.png" alt="alt text" width="300px"/>

**Hệ số $scale$ :**

Bởi vì tắt node thì đầu ra các layer sẽ bị giảm, cần scale lên cho các node không tắt  để giữ cho **tổng năng lượng (magnitude)** của đầu ra không đổi.


{{% /details %}}

{{% details title="Code" closed="true" %}}
Với tỷ lệ dropout $r$:

- Ngẫu nhiên đưa các node đầu vào về giá trị 0 với **tần suất là $r$**.
- Chỉ áp dụng trong **chế độ huấn luyện** (training mode)

![alt text](image-14.png)

![alt text](image-15.png)
{{% /details %}}

{{% /details %}}


{{% details title="Result" closed="true" %}}
Thực tế thì mọi người hay xài dropout ở những layer cuối

![alt text](image-16.png)

{{% /details %}}


### 3.4. Trick 4: Kernel regularizer

{{% details title="Target" closed="true" %}}

Thêm hệ số $W$ vào hàm Loss , mà mình mong muốn giảm thiểu hàm Loss xuống nên $W$ cũng nhỏ xuống 

—> Mục địch chính của regular là không muốn $W$ quá lớn

![alt text](image-17.png)

{{% /details %}}

{{% details title="Code" closed="true" %}}
```python
optimizer = torch.optim.Adam(model.parameters(), lr=0.
001, weight_decay=1e-5) ## weight_decay là hệ số lambda
```
{{% /details %}}


{{% details title="Result" closed="true" %}}
![alt text](image-18.png)
{{% /details %}}


### 3.5. Trick 5: Data Augmentation

{{% details title="Target" closed="true" %}}
![alt text](image-21.png)
{{% /details %}}

{{% details title="Code" closed="true" %}}

![alt text](image-23.png)
```python
transforms.Compose([
    transforms.RandomHorizontalFlip(p=0.5),
    transforms.RandomCrop(),
    transforms.ToTensor(),
    transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))
])
```
{{% /details %}}

{{% details title="Result" closed="true" %}}

![alt text](image-22.png)

{{% /details %}}


{{< callout type="error" >}}

![alt text](image-24.png)

Đến đây, chúng ta đã đạt được mục tiêu ban đầu: tăng độ chính xác (accuracy) trên tập kiểm tra (test) và giảm khoảng cách giữa độ chính xác trên tập huấn luyện và tập kiểm tra. Tuy nhiên, nếu muốn tăng thêm độ chính xác trên tập kiểm tra, liệu có khả thi hay không?

{{% details title="Giải pháp:" closed="true" %}}
Để cải thiện độ chính xác trên tập kiểm tra, chúng ta cần làm:

**Tăng độ chính xác trên tập huấn luyện trước (train_accuracy)**:

- Mục tiêu là để mô hình học được các đặc trưng phức tạp hơn trong dữ liệu.
- Điều này tương đương với việc bạn muốn đạt điểm cao hơn trên bài kiểm tra thực tế, thì trước tiên bạn phải nắm vững kiến thức trong quá trình ôn luyện.

—> Tăng model capacity

{{% /details %}}

{{< /callout >}}


### 3.6. Trick 6: Trick 6: Reduce learning rate (Adam + Weight decay)

{{% details title="Target" closed="true" %}}
Ban đầu, mô hình cần một learning rate cao để khám phá không gian tham số.
Sau đó, giảm learning rate giúp tinh chỉnh mô hình và hội tụ tốt hơn.
Nếu giữ LR quá cao, mô hình có thể dao động quanh điểm tối ưu thay vì hội tụ.
![alt text](image-25.png)
{{% /details %}}

{{% details title="Results" closed="true" %}}
![alt text](image-26.png)

{{% /details %}}


### 3.7. Trick 7: Increase model capacity (and use more data augmentation)

{{% details title="Target" closed="true" %}}
- **Tăng model capacity** giúp mô hình học được các đặc trưng phức tạp hơn trong dữ liệu.
- **Data augmentation** giúp mô hình học được các đặc trưng chính xác của dữ liệu, giảm nguy cơ overfitting, tăng khả năng tổng quát hóa của mô hình.

![alt text](image-27.png)
{{% /details %}}

{{% details title="Results" closed="true" %}}
![alt text](image-28.png)
{{% /details %}}

### 3.8. Trick 8: Using skip-connection

{{% details title="Target" closed="true" %}}
- **Skip connection** giúp truyền ngược thông tin từ các lớp thấp đến các lớp cao hơn, giúp mô hình học được các đặc trưng phức tạp hơn trong dữ liệu.
- **Skip connection** giúp giảm nguy cơ vanishing gradient, giúp mô hình học nhanh hơn và hiệu quả hơn.
- **Skip connection** giúp mô hình học được các đặc trưng chính xác của dữ liệu, giảm nguy cơ overfitting, tăng khả năng tổng quát hóa của mô hình.
- **Skip connection** giúp mô hình học được các đặc trưng cục bộ và toàn cục của dữ liệu, giúp mô hình hiểu được cấu trúc không gian và hình học của dữ liệu.

![alt text](image-29.png)
{{% /details %}}

{{% details title="Results" closed="true" %}}
![alt text](image-31.png)
{{% /details %}}

### 3.9. Trick 9: Increase model capacity once more

{{% details title="Target" closed="true" %}}

Tăng gấp đôi out_channels của các layer Conv2d

![alt text](image-33.png)

{{% /details %}}

{{% details title="Results" closed="true" %}}
![alt text](image-32.png)

{{% /details %}}

## 4. Summary

![alt text](image-34.png)

## 5. Câu hỏi ôn tập

{{% details title="Hiện tượng nào xảy ra khi khoảng cách giữa training accuracy và test accuracy quá lớn?" closed="true" %}}
Hiện tượng **overfitting** xảy ra khi mô hình học quá tốt trên tập huấn luyện nhưng không tổng quát hóa được trên tập kiểm tra.
{{% /details %}}

{{% details title="Mục tiêu của buổi học này là gì?" closed="true" %}}
Mục tiêu là **cải thiện độ chính xác trên tập kiểm tra** và **giảm khoảng cách** giữa `training accuracy` và `test accuracy`.
{{% /details %}}

{{% details title="Trong giai đoạn đầu huấn luyện, biên phân tách của mô hình có đặc điểm gì?" closed="true" %}}
Biên phân tách dần trở nên hợp lý hơn, phản ánh tốt các đặc điểm của dữ liệu.
![image.png](image-1.png)
{{% /details %}}

{{% details title="Khi nào mô hình đạt trạng thái tổng quát hóa tốt (generalization)?" closed="true" %}}
Khi mô hình học được các đặc trưng chính của dữ liệu mà không làm phức tạp hóa biên phân tách.
![image.png](image-1.png)
{{% /details %}}

{{% details title="Hiện tượng gì xảy ra khi mô hình huấn luyện quá mức (overfitting)?" closed="true" %}}
Mô hình trở nên quá phức tạp, khớp với cả các outliers và không phản ánh xu hướng tổng quát của dữ liệu.
![image.png](image-1.png)
{{% /details %}}

{{% details title="Trước khi huấn luyện, trạng thái của mô hình như thế nào?" closed="true" %}}
Mô hình chưa học được quy luật dữ liệu và có thể có biên phân tách không hợp lý.
![image.png](image-1.png)
{{% /details %}}

{{% details title="Tại sao việc thêm nhiễu vào dữ liệu huấn luyện lại giúp cải thiện khả năng tổng quát hóa của mô hình?" closed="true" %}}
Thêm nhiễu giúp mô hình học cách xử lý dữ liệu phức tạp hơn, tránh tình trạng "học thuộc lòng" và cải thiện khả năng dự đoán trên dữ liệu mới.
{{% /details %}}

{{% details title="Trong PyTorch, kỹ thuật nào được sử dụng để thêm nhiễu vào dữ liệu?" closed="true" %}}
Kỹ thuật **RandomErasing** trong thư viện `torchvision.transforms` được sử dụng để thêm nhiễu một cách ngẫu nhiên vào dữ liệu huấn luyện.
{{% /details %}}

{{% details title="Batch Normalization giúp giảm thiểu vấn đề gì trong quá trình huấn luyện?" closed="true" %}}
Batch Normalization giúp giảm thiểu **Internal Covariate Shift**, tức là sự thay đổi phân phối của các đặc trưng đầu vào trong quá trình huấn luyện.
{{% /details %}}

{{% details title="Vì sao Batch Normalization có thể hoạt động như một dạng regularization?" closed="true" %}}
Batch Normalization thêm nhiễu vào quá trình huấn luyện do sự khác biệt giữa **mean** và **variance** trong các mini-batch, từ đó giảm nguy cơ **overfitting** và cải thiện khả năng tổng quát hóa.
{{% /details %}}

{{% details title="Batch Normalization làm thay đổi điều gì trong mỗi epoch của quá trình huấn luyện?" closed="true" %}}
Batch Normalization làm thay đổi **mean** và **variance** giữa các mini-batch, dẫn đến các **feature map** biến đổi liên tục qua từng epoch.
{{% /details %}}

{{% details title="Sau khi áp dụng Batch Normalization, độ chính xác kiểm tra (val_accuracy) đã tăng từ bao nhiêu lên bao nhiêu?" closed="true" %}}
Độ chính xác kiểm tra tăng từ khoảng **80.9%** lên **84%**.
{{% /details %}}

{{% details title="Kỹ thuật Dropout hoạt động như thế nào trong quá trình huấn luyện?" closed="true" %}}
Dropout vô hiệu hóa ngẫu nhiên một số **nodes** (đặt giá trị bằng 0) trong mạng để giảm sự phụ thuộc và cải thiện khả năng tổng quát hóa.
{{% /details %}}

{{% details title="Tại sao Dropout giúp mô hình tránh overfitting?" closed="true" %}}
Dropout ngăn mô hình học quá mức từ một node hoặc feature cụ thể, buộc mô hình học các đặc trưng tổng quát hơn và tạo ra nhiều phiên bản mô hình khác nhau như một phương pháp **ensemble**.
{{% /details %}}

{{% details title="Kernel Regularizer (L2 Regularization) có mục đích gì trong quá trình huấn luyện mô hình?" closed="true" %}}
Mục đích của Kernel Regularizer là giảm kích thước trọng số \(w\), ngăn mô hình tập trung quá mức vào các đặc trưng cụ thể và tránh overfitting.
{{% /details %}}

{{% details title="Trong PyTorch, tham số nào của Adam optimizer thực hiện chức năng L2 Regularization?" closed="true" %}}
Tham số **`weight_decay`** trong Adam optimizer thực hiện chức năng L2 Regularization.
{{% /details %}}

{{% details title="Data augmentation có mục đích gì trong quá trình huấn luyện mô hình?" closed="true" %}}
Data augmentation giúp tăng kích thước và sự đa dạng của tập dữ liệu huấn luyện, giúp mô hình học tốt hơn và cải thiện khả năng tổng quát hóa.
{{% /details %}}

{{% details title="Kể tên một số kỹ thuật phổ biến của data augmentation?" closed="true" %}}
Các kỹ thuật phổ biến bao gồm **horizontal flip** (lật ngang), **rotate** (xoay), và **crop and resize** (cắt và thay đổi kích thước).
{{% /details %}}

{{% details title="Mục tiêu chính của Data Augmentation trong quá trình huấn luyện là gì?" closed="true" %}}
Mục tiêu là tăng độ chính xác trên tập kiểm tra (**test accuracy**) và giảm khoảng cách giữa tập huấn luyện và tập kiểm tra.
{{% /details %}}

{{% details title="Giải pháp nào được đề xuất để cải thiện độ chính xác trên tập training (train accuracy)?" closed="true" %}}
Tăng **model capacity** để mô hình học được nhiều đặc trưng phức tạp hơn từ dữ liệu.
{{% /details %}}

{{% details title="Trong kỹ thuật Reduce Learning Rate, công thức giảm learning rate theo epoch là gì?" closed="true" %}}
Công thức là $ \eta = \eta_0 \times \gamma^{\text{epoch}} $ với $ \gamma $ là hệ số giảm learning rate.
{{% /details %}}

{{% details title="Trong đoạn code, tham số nào trong ExponentialLR kiểm soát tốc độ giảm learning rate?" closed="true" %}}
Tham số **gamma** trong **ExponentialLR** kiểm soát tốc độ giảm learning rate, ở đây $ \gamma = 0.96 $.
{{% /details %}}

