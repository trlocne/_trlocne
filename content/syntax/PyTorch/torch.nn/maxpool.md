---
title: MaxPool
type: docs
---

## MaxPool1d
### Syntax
```python
torch.nn.MaxPool1d(kernel_size, stride=None, padding=0, dilation=1, return_indices=False, 
ceil_mode=False)
```

### Parameters

+ **kernel_size (Union[int, Tuple[int]])** – Kích thước của cửa sổ trượt, phải lớn hơn 0.

+ **stride (Union[int, Tuple[int]])** – Bước nhảy của cửa sổ trượt, phải lớn hơn 0. Giá trị mặc định là kernel_size.

+ **padding (Union[int, Tuple[int]])** – Thêm độ đệm âm vô hạn ẩn (negative infinity) vào cả hai bên, phải lớn hơn hoặc bằng 0 và nhỏ hơn hoặc bằng kernel_size / 2.

+ **dilation (Union[int, Tuple[int]])** – Khoảng cách giữa các phần tử bên trong một cửa sổ trượt, phải lớn hơn 0.

+ **return_indices (bool)** – Nếu đặt thành True, hàm sẽ trả về chỉ số của giá trị lớn nhất (argmax) cùng với các giá trị cực đại. Tùy chọn này hữu ích cho việc sử dụng sau này với `torch.nn.MaxUnpool1d`.

+ **ceil_mode (bool)** – Nếu đặt thành True, hàm sẽ sử dụng hàm làm tròn lên (ceil) thay vì làm tròn xuống (floor) để tính toán kích thước đầu ra. Điều này đảm bảo rằng mọi phần tử trong tensor đầu vào đều được bao phủ bởi một cửa sổ trượt.

### Example

```python
>>> # pool of size=3, stride=2
>>> m = nn.MaxPool1d(3, stride=2)
>>> input = torch.randn(20, 16, 50)
>>> output = m(input)
```

## MaxPool2d
### Syntax
```python
torch.nn.MaxPool2d(kernel_size, stride=None, padding=0, dilation=1, return_indices=False, 
ceil_mode=False)
```

### Parameters

- **kernel_size (Union[int, Tuple[int, int]])** – Kích thước của cửa sổ dùng để lấy giá trị cực đại.

- **stride (Union[int, Tuple[int, int]])** – Bước nhảy của cửa sổ. Giá trị mặc định là kernel_size.

- **padding (Union[int, Tuple[int, int]])** – Độ đệm âm vô hạn ẩn sẽ được thêm vào cả hai bên.

- **dilation (Union[int, Tuple[int, int]])** – Một tham số kiểm soát khoảng cách giữa các phần tử trong cửa sổ.

- **return_indices (bool)** – Nếu đặt thành True, hàm sẽ trả về các chỉ số của giá trị cực đại cùng với các giá trị đầu ra. Tùy chọn này hữu ích cho torch.nn.MaxUnpool2d sau này.

- **ceil_mode (bool)** – Khi đặt thành True, hàm sẽ sử dụng hàm làm tròn lên (ceil) thay vì làm tròn xuống (floor) để tính toán kích thước đầu ra.


### Example

```python
>>> # pool of square window of size=3, stride=2
>>> m = nn.MaxPool2d(3, stride=2)
>>> # pool of non-square window
>>> m = nn.MaxPool2d((3, 2), stride=(2, 1))
>>> input = torch.randn(20, 16, 50, 32)
>>> output = m(input)
```
## MaxPool3d

### Syntax

```python
torch.nn.MaxPool3d(kernel_size, stride=None, padding=0, dilation=1, return_indices=False, 
ceil_mode=False)
```

*Tương tự như MaxPool3d*

## MaxUnpool1d

Khôi phục lại MaxPool1d.

Ví dụ

```python
import torch
import torch.nn.functional as F

# Dữ liệu đầu vào
input_tensor = torch.tensor([[[1, 3, 2, 0, 4, 5, 6, 1]]], dtype=torch.float)

# Lớp MaxPool1d với kernel_size=2, stride=2
pool = torch.nn.MaxPool1d(kernel_size=2, stride=2, return_indices=True)
output, indices = pool(input_tensor)

print("MaxPool1d Output:")
print(output)
print("Indices:")
print(indices)

# Lớp MaxUnpool1d để phục hồi lại dữ liệu
unpool = torch.nn.MaxUnpool1d(kernel_size=2, stride=2)
reconstructed = unpool(output, indices, output_size=input_tensor.shape)

print("MaxUnpool1d Output:")
print(reconstructed)
```

```bash {filename="OUTPUT"}
Kết quả đầu vào:
>> [[[1, 3, 2, 0, 4, 5, 6, 1]]]
Sau MaxPool1d với kernel_size=2, stride=2, kết quả sẽ là:
>> [[[3, 2, 5, 6]]]
indices sẽ lưu lại vị trí các phần tử được chọn:
>> [[[1, 2, 5, 6]]]
Sau MaxUnpool1d, giá trị lớn nhất sẽ được đặt lại vào đúng vị trí ban đầu, còn các giá trị khác là 0:
>> [[[0, 3, 2, 0, 0, 5, 6, 0]]]
```

### Syntax

```python
torch.nn.MaxUnpool1d(kernel_size, stride=None, padding=0)
```

### Parameters

+ **kernel_size (int or tuple)** – Size of the max pooling window.

+ **stride (int or tuple)** – Stride of the max pooling window. It is set to kernel_size by default.

+ **padding (int or tuple)** – Padding that was added to the input

### Example

```python
>>> pool = nn.MaxPool1d(2, stride=2, return_indices=True)
>>> unpool = nn.MaxUnpool1d(2, stride=2)
>>> input = torch.tensor([[[1., 2, 3, 4, 5, 6, 7, 8]]])
>>> output, indices = pool(input)
>>> unpool(output, indices)
tensor([[[ 0.,  2.,  0.,  4.,  0.,  6.,  0., 8.]]])

>>> # Example showcasing the use of output_size
>>> input = torch.tensor([[[1., 2, 3, 4, 5, 6, 7, 8, 9]]])
>>> output, indices = pool(input)
>>> unpool(output, indices, output_size=input.size())
tensor([[[ 0.,  2.,  0.,  4.,  0.,  6.,  0., 8.,  0.]]])

>>> unpool(output, indices)
tensor([[[ 0.,  2.,  0.,  4.,  0.,  6.,  0., 8.]]])
```