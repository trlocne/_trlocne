---
title: AvgPool
type: docs
---

## AvgPool1d
### Syntax
```python
torch.nn.AvgPool1d(kernel_size, stride=None, padding=0, ceil_mode=False, 
count_include_pad=True)
```

### Parameters
+ **kernel_size** (số nguyên hoặc tuple): Kích thước của cửa sổ (window) được sử dụng để pooling.
+ **stride** (số nguyên hoặc tuple): Bước di chuyển của cửa sổ pooling; nếu không được chỉ định, mặc định bằng kernel_size.
+ **padding** (số nguyên hoặc tuple): Số lượng pixel padding (đệm) thêm vào mỗi bên của dữ liệu.
+ **ceil_mode** (bool): Nếu được đặt là True, sử dụng hàm làm tròn lên (ceil) để tính toán kích thước đầu ra thay vì làm tròn xuống (floor).
+ **count_include_pad** (bool): Nếu được đặt là True, bao gồm các giá trị padding trong quá trình tính trung bình (áp dụng với các lớp pooling trung bình như AvgPool).

### Example

```python
>>> # pool with window of size=3, stride=2
>>> m = nn.AvgPool1d(3, stride=2)
>>> m(torch.tensor([[[1., 2, 3, 4, 5, 6, 7]]]))
tensor([[[2., 4., 6.]]])
```

## AvgPool2d
### Syntax
```python
torch.nn.AvgPool2d(kernel_size, stride=None, padding=0, ceil_mode=False, 
count_include_pad=True, divisor_override=None)
```

### Parameters

- **kernel_size (Union[int, Tuple[int, int]])**: Kích thước của cửa sổ pooling.
- **stride (Union[int, Tuple[int, int]])**: Bước di chuyển của cửa sổ; nếu không chỉ định, mặc định bằng kernel_size.
- **padding (Union[int, Tuple[int, int]])**: Số lượng zero padding được thêm vào mỗi bên.
- **ceil_mode (bool)**: Nếu True, sử dụng hàm làm tròn lên (ceil) để tính kích thước đầu ra thay vì làm tròn xuống (floor).
- **count_include_pad (bool)**: Nếu True, bao gồm các giá trị padding trong phép tính trung bình.
- **divisor_override (Optional[int])**: Nếu được chỉ định, giá trị này sẽ được dùng làm mẫu số thay vì sử dụng kích thước của vùng pooling.

### Example

```python
>>> # pool of square window of size=3, stride=2
>>> m = nn.AvgPool3d(3, stride=2)
>>> # pool of non-square window
>>> m = nn.AvgPool3d((3, 2, 2), stride=(2, 1, 2))
>>> input = torch.randn(20, 16, 50, 44, 31)
>>> output = m(input)
```
## AvgPool3d

*Tương tự như AvgPool2d*
