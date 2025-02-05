---
title: CNN Training
password: 1234
blog: true
---

How to increase training accuracy?

## 1. Motivation

Qua bài trước chúng ta biết được CNN gồm các lớp Conv + MaxPooling vậy chúng ta sẽ xây dựng như thế nào để training trở nên tốt nhất có thể. Xây dựng các xây dựng một mạng Neural Network

## 2. Setting-up Context

![alt text](image.png 'Lịch sử phát triển của CNN')

### 2.1. VGG-16

{{% details title="Vì sao gọi là VGG-16" closed="true" %}}

Bởi vì có tất cả 16 layer có tham số.

{{% /details %}}

![alt text](image-1.png)

Input vào các lớp Conv rồi tới Pooling sau đó tiếp tục cuối cùng đến Dense ( Linear)

#### 2.1.1. VGG16 for ImageNet

![alt text](image-2.png)

![alt text](image-3.png)

#### 2.1.2. VGG16-like for Cifar-10

![alt text](image-4.png)

![alt text](image-5.png)

### 2.2. Network Training

Triển khai hai lớp conv, sau đó cho qua MLP để phân loại. Quan sát biểu đồ, mô hình huấn luyện khá hiệu quả.

![alt text](image-6.png)

<div style='display: flex'>
<img src="image-11.png" width='45%'>
<img src="image-9.png" width='50%'>
</div>

Chúng ta thử một bộ dữ liệu khác có độ phức tạp hơn `Cifar-10 dataset`. Khi áp dụng vào mô hình ta có thể thấy được mô hình huấn luyện không được tốt. 

![alt text](image-12.png)

Bằng các truyền thống chúng ta sẽ thêm nhiều layer hơn sẽ học được mô hình.

![alt text](image-14.png)

![alt text](image-16.png)


Chúng ta có thể thấy được accuracy tăng lên nhưng khi thực tế lại bị overfitting. Vậy chúng ta thử xem thêm 1 layer.

![alt text](image-17.png)

![alt text](image-15.png)

Qua kết quả trên chúng ta có thể thấy mô hình không thể học được dataset nữa. Vậy vấn đề ở đâu

>[!NOTE]
>Ở bài trước chúng ta điều biết khi đạo hàm hàm `sigmoid` sẽ giảm qua từng lớp khi chúng ta huấn luyện với quá nhiều layer việc mà các tham số không thể cập nhật có thể lường trước được.

Việc thay thế activation bằng ReLU giúp mô hình học ổn định hơn, mang lại kết quả khả quan cho hầu hết tập dữ liệu. 

![alt text](image-18.png)

![alt text](image-19.png)

Bây giờ chúng ta hãy thử thêm nhiều layer nữa xem mô hình có tốt hơn không tại chúng ta đang thấy val accuracy còn khá thấp.

![alt text](image-22.png)

![alt text](image-21.png)

có thể thấy mô hình không học được nữa.

## 3. Solutions for the Context

![alt text](image-23.png 'Summary of the current network')

### 3.1. Solution 1: Observation

![alt text](image-24.png)

Với mô hình đề cập ở section trước, với dataset là MNIST thì có vẻ như mọi thứ hoạt động ổn nhưng với Cifar-10 thì có vẻ val acc khá thấp.

### 3.2. Solution 1: Idea

Có vẻ tại vì Cifar-10 phức tạp hơn so với MNIST vậy nên nó làm cho mô hình khó học được hơn.

> Chúng ta thử normalization 

![alt text](image-26.png)

Có vẻ như `val_acc` tăng một ít.

![alt text](image-27.png)

Chúng ta thử normalization trong khoảng [-1, 1]

![alt text](image-28.png)

### 3.3. Solution 2: Batch Normalization

![alt text](image-29.png)

Trong thực tế mỗi input đi qua các node thì mỗi node làm cho giá trị lệch một cách khác nhau vì vậy ta cần normalization theo từng node thì sẽ hiệu quả hơn.


<img src="image-30.png" width='300px'>

{{% details title="Example Batch Normalization" closed="true" %}}

<img src="image-31.png" width='600px'>

{{% /details %}}

![alt text](image-32.png)

![alt text](image-33.png)

Ta có thể thấy được kết quả đã tốt hơn rất nhiều.

### 3.4. Solution 3: Use more robust initialization

![alt text](image-34.png)

Như chúng ta đã biết trong bàng `model initiation` thì relu được sử dụng với he inititaiton mà trong bài lại dùng Glorot: 

![alt text](image-35.png)

![alt text](image-36.png)

### 3.5. Solution 4: Using advanced activation

![alt text](image-37.png)

![alt text](image-38.png)

### 3.6. Solution 5: Skip connection

![alt text](image-40.png)

![alt text](image-41.png)

![alt text](image-42.png)

![alt text](image-43.png)

### 3.7. Solution 6: Reduce learning rate

![alt text](image-44.png)

![alt text](image-45.png)

## 4. Summary

![alt text](image-46.png)
