---
title: LSTM/GRU
type: docs
weight: 5
sidebar:
  open: true
tags:
  - Deep Learning
math: true
comments: true
password: 1234
blog: true
---
## Table of Contents

- [Table of Contents](#table-of-contents)
- [1. Review: Recurrent Neural Network (RNN)](#1-review-recurrent-neural-network-rnn)
- [2. LSTM](#2-lstm)
- [3. GRU](#3-gru)
- [4. Time Series – Basic Knowledge](#4-time-series--basic-knowledge)
- [5. LSTM, GRU for Time Series Data](#5-lstm-gru-for-time-series-data)
- [6.  LSTM, GRU for Text Data](#6--lstm-gru-for-text-data)
- [7. Summary](#7-summary)

## 1. Review: Recurrent Neural Network (RNN)

{{% details title="Neural Network limit" closed="true" %}}

<img src="assets\20250225_010136_image.png" width="300"/>

Giới hạn của neural network là fix input đầu vào, làm cho mô hình không flexible.

{{% /details %}}

{{% details title="RNN review" closed="true" %}}
<img src="2025-02-25-10-25-15.png" width="300"/>

chúng ta muốn dự đoán thời điểm hiện tại nhờ vào các thời điểm trong quá khứ, thời gian trước đó.

{{< callout >}}
  Nhưng lại có vấn đề chúng ta chỉ nhớ thông tin ngắn hạn không thể nhớ được các thông tin dài hạn hơn, làm cho mạng ko flexible. Chúng ta cần một mạng flexible.
{{< /callout >}}

<img src="2025-02-25-10-28-59.png" width="300"/>

RNN lấy idea từ não người, bộ nhớ người gồm các short term (bộ nhớ ngắn hạn), long term (bộ nhớ dài hạn). bộ nhớ ngắn hạn sẽ ghi nhớ thông tin ngắn hạn, bộ nhớ dài hạn sẽ ghi nhớ thông tin dài hạn. Có những thông tin não người có thể nhớ rất đâu từ nhỏ đến giờ thì được lưu vào long term memory.

<img src="2025-02-25-10-33-16.png" width="300"/>

Các thông tin chính của RNN:

{{% details title="Recurent means SEQUENCE to sequence" closed="true" %}}

<img src="2025-02-25-10-35-07.png" width="300"/>

có nghĩa là các thông tin được truyền từ đầu đến cuối của mạng.
{{% /details %}}

{{% details title="Recurent means FEEDBACK loops" closed="true" %}}
<img src="2025-02-25-10-36-27.png" width="300"/>

Output của trạng thái trước được đưa vào input của trạng thái hiện tại.

{{% /details %}}

{{% details title="Recurent means SHARING WEIGHT" closed="true" %}}
<img src="2025-02-25-10-37-30.png" width="300"/>

Tất cả các trạng thái đều dùng chung các tham số.

{{% /details %}}

{{% /details %}}


{{% details title="Different types of RNNs" closed="true" %}}
+ Many to many (equal input to output)

<img src="2025-02-25-10-41-58.png" width="300"/>

+ Many to many (unequal input to output)

<img src="2025-02-25-10-42-38.png" width="300"/>

+ One to many

<img src="2025-02-25-10-43-00.png" width="300"/>


+ Many to one

<img src="image.png" width="300"/>
{{% /details %}}

{{% details title="Why RRN is not Use Often" closed="true" %}}
<img src="image-1.png" width="300"/>

việc tính toán trạng thái hiện tại phụ thuộc vào trạng thái trước đó làm mô hình khó train và gây ra vấn đề vashing/exploding gradient. 

<img src="image-2.png" width="300"/>

khi trọng số bằng 2 vậy thì giá trị output sẽ rất lớn gây ra vấn đề **Exploding gradient**

<img src="image-3.png" width="300"/>

Khi trọng số bằng 0.5 thì output sẽ rất nhỏ có thể gây ra vấn đề vanishing gradient.

{{< callout type="info" >}}
  Vậy làm cách nào để tránh được 2 vấn đề trên.
{{< /callout >}}
{{% /details %}}

## 2. LSTM

{{% details title="LSTM idea" closed="true" %}}

<img src="image-4.png" width="500"/>

LSTM là kiểu mạng giải quyết vấn đề gradient/vanishing gradient. với idea thay vì chỉ lấy thông tin ngắn hạn thì chúng ta cần lấy thêm thông tin dài hạn long term memory.
{{% /details %}}

{{% details title="Three Stages in Single LSTM Unit" closed="true" %}}
<img src="image-5.png" width="500"/>

LSTM gồm 3 **stages**:
1. **Forget gate**: quyết định thông tin nào cần quên và thông tin nào cần nhớ.
2. **Input gate**: quyết định thông tin nào cần cập nhật vào long term memory.
3. **Output gate**: quyết định thông tin nào cần lấy ra từ long term memory.
{{% /details %}}

{{% details title="Details LSTM" closed="true" %}}
<img src="image-6.png" width="500"/>

- **Long term memories** lưu trữ lại dữ liệu quá khứ dài hạn, không có bias và trọng số nên tránh được tình trạng exploding gradient và vanishing gradient.

<img src="image-7.png" width="500"/>

- **Short term memories** lưu trữ dữ liệu ngắn hạn, có bias và trọng số.

> Vậy các nào để **long term memories** và **short term memories** giao tiếp với nhau.

{{% details title="Forget gate" closed="true" %}}
<img src="image-8.png" width="500"/>

Block trên xác định số lượng thông tin nào cần quên và thông tin nào cần nhớ.
{{% /details %}}

{{% details title="Input gate" closed="true" %}}
<img src="image-9.png" width="500"/>

Pontential long term sẽ cho biết cần nhớ bao nhiêu % input để làm long term memory và khối bên phải sẽ quyết định thông tin nào cần cập nhật vào long term memory.
{{% /details %}}

{{% details title="Output gate" closed="true" %}}
<img src="image-10.png" width="500"/>

**Output gate** sẽ quyết định thông tin nào cần lấy ra từ **long term memory**.

{{% /details %}}

{{% /details %}}

{{% details title="Example LSTM" closed="true" %}}

<img src="image-11.png" width="500"/>

Giả sử input đầu vào day 1 đến day 4 và xác định day 5 sử dụng LSTM.

Khởi tạo tham số ban đầu đều bằng 0.

+ Bắt đầu với **ngày 1**:
  
<img src="image-12.png" width="500"/>

<img src="image-13.png" width="500"/>

<img src="image-14.png" width="500"/>

<img src="image-15.png" width="500"/>


+ Input đầu vào **ngày 2** và output đầu vào **ngày 1**:
  
<img src="image-16.png" width="500"/>

+ Input đầu vào **ngày 3** và output đầu vào **ngày 2**:
  
<img src="image-17.png" width="500"/>

+ Input đầu vào **ngày 4** và output đầu vào **ngày 3**:

<img src="image-18.png" width="500"/>

{{% /details %}}

{{% details title="Long short term memory - Summary" closed="true" %}}
<img src="image-19.png" width="300"/>

+ **Forget gate**: quyết định thông tin nào cần quên và thông tin nào cần nhớ.

<img src="image-20.png" width="300"/>

<img src="image-21.png" width="300"/>


+ **Input gate**: quyết định thông tin nào cần cập nhật vào long term memory.

<img src="image-22.png" width="300"/>

<img src="image-23.png" width="300"/>

<img src="image-24.png" width="300"/>

<img src="image-25.png" width="300"/>

+ **Output gate**: quyết định thông tin nào cần lấy ra từ long term memory.

<img src="image-26.png" width="300"/>

<img src="image-27.png" width="300"/>

<img src="image-28.png" width="300"/>

<img src="image-29.png" width="300"/>


{{% details title="Summaey" closed="true" %}}
<img src="image-30.png" width="300"/>

{{% /details %}}
{{% /details %}}

## 3. GRU

{{% details title="GRU overview" closed="true" %}}
<img src="image-31.png" width="500"/>

GRU là một phiên bản đơn giản hơn của LSTM, GRU không có **long term memory** và **short term memory** như LSTM.

{{% /details %}}

{{% details title="architecture GRU" closed="true" %}}
+ Bỏ qua **long term memory** và **short term memory** như LSTM.

<img src="image-32.png" width="500"/>
 
+ Cách GRU hoạt động:

<img src="image-33.png" width="500"/>

<img src="image-34.png" width="500"/>

<img src="image-35.png" width="500"/>

<img src="image-36.png" width="500"/>

<img src="image-37.png" width="500"/>

<img src="image-38.png" width="500"/>

<img src="image-39.png" width="500"/>
{{% /details %}}

{{% details title="Summary" closed="true" %}}
![alt text](image-40.png)

{{% /details %}}

## 4. Time Series – Basic Knowledge

{{% details title="Các loại dữ liệu" closed="true" %}}
+  **Evenly Sampled**: các điểm dữ liệu được lấy mẫu cách đều nhau.

<img src="image-41.png" width="300"/>

+  **Unevenly Sampled**: các điểm dữ liệu không có mẫu cách đều nhau.

<img src="image-42.png" width="300"/>

{{% /details %}}

{{% details title="Forecasting" closed="true" %}}
![alt text](image-43.png)

+ **Single step (short term)**: dự đoán giá trị tại thời điểm tiếp theo.

<img src="image-44.png" width="300"/>

+ **Multi step (long term)**: dự đoán giá trị tại nhiều thời điểm tiếp theo.

  + **Iterated Multi-step Forecasting**: dự đoán giá trị tại nhiều thời điểm tiếp theo dựa vào giá trị dự đoán trước đó.

  <img src="image-45.png" width="300"/>

  + **Direct Multi-step Forecasting**:

  <img src="image-46.png" width="300"/>

{{% details title="Size of Training data" closed="true" %}}
![alt text](image-47.png)

{{% /details %}}

{{% details title="Rolling window (Cross-validation)" closed="true" %}}
![alt text](image-48.png)

{{% /details %}}
{{% /details %}}


{{% details title="Single-step Forecasting – Rolling Window" closed="true" %}}
![alt text](image-49.png)

{{% /details %}}

## 5. LSTM, GRU for Time Series Data

{{% details title="Dataset" closed="true" %}}
![alt text](image-50.png)
{{% /details %}}

{{% details title="RNN" closed="true" %}}
  <img src="image-51.png" width="500"/>

+ Tại t = 0:

  <img src="image-52.png" width="500"/>

+ Tại t = 1:

  <img src="image-53.png" width="500"/>

+ Tại t = 2:

  <img src="image-55.png" width="500"/>

+ **Stacked RNN**: output của một RNN sẽ là input của RNN tiếp theo.

  <img src="image-56.png" width="500"/>

+ **Bidirectional RNN**: giúp cho học thông tin theo các chiều khác nhau.

  <img src="image-57.png" width="500"/>
{{% /details %}}

{{% details title="LSTM" closed="true" %}}
![alt text](image-58.png 'Khởi tạo các tham số')

+ Tại thời điểm **t=0**:

![alt text](image-59.png)

![alt text](image-60.png)

![alt text](image-61.png)

![alt text](image-62.png)

![alt text](image-63.png)

![alt text](image-64.png)

![alt text](image-65.png)

+ Tại thời điểm **t=1**:

![alt text](image-66.png)

+ Tại thời điểm **t=2**:

![alt text](image-67.png)
{{% /details %}}

{{% details title="GRU" closed="true" %}}

+ Tại thời điểm **t=0**:
![alt text](image-68.png)

![alt text](image-69.png)

![alt text](image-70.png)

+ Tại thời điểm **t=1**:

![alt text](image-71.png)

+ Tại thời điểm **t=2**:

![alt text](image-72.png)

{{% /details %}}

## 6.  LSTM, GRU for Text Data

{{% details title="Dataset" closed="true" %}}
![alt text](image-73.png)

{{% /details %}}

{{% details title="RNN" closed="true" %}}
![alt text](image-74.png)

![alt text](image-75.png)

![alt text](image-76.png)

![alt text](image-77.png)

{{% /details %}}

{{% details title="LSTM" closed="true" %}}
![alt text](image-78.png)

![alt text](image-79.png)

![alt text](image-80.png)

![alt text](image-81.png)
{{% /details %}}

{{% details title="GRU" closed="true" %}}
![alt text](image-82.png)

![alt text](image-83.png)

{{% /details %}}

## 7. Summary

![alt text](image-84.png)