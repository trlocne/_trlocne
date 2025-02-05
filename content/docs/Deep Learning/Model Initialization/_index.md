---
title: Model Initialization
type: docs
weight: 4
sidebar:
  open: true
tags:
  - Model Initialization
math: true
comments: true
password: 'aio2024'
blog: true
---

## 1. Motivation

> [!NOTE]
> Trong bài Insight MLP chúng ta thảo luận về vấn đề Gradient Vanishing khi khởi tạo các giá trị tham số $\theta$ bằng âm hoặc bằng không , vậy có cách nào khởi tạo $\theta$ mà tránh được trường hợp đó không ? 

{{% details title="Hint" closed="true" %}}

Chúng ta không thể khởi tạo bằng con số mình thích vì :

- Không có tính linh hoạt trong bài toán
- Chưa chắc con số của mình đã tốt , có thể tốt với model ở bài toán này nhưng theo thời gian dữ liệu sẽ thay đổi thì chưa chắc $\theta$ của mình đã còn tốt

Các con số chạy đến vô cùng nên mình khó có thể tìm được con số tốt nhất, vậy nên mình sẽ cố định trong khoảng nào đó , rồi random $\theta$ trong khoảng đó.

{{% /details %}}

Bài hôm nay chúng ta sẽ đi tìm các khoảng khởi tạo để tránh việc đó, bao gồm 2 loại : 

- Xavier Glorot Init : dành cho activation `Sigmoid` và `Tanh`
- Kaiming He Init : dành cho activation `Relu`

## 2. Case Studies

Chúng ta sẽ thảo luận tiếp các trường hợp dẫn đến Gradient Vanishing/Exploding khác trước khi đi sâu vào cách khắc phục nó

> [!NOTE]
> Những case study các bạn có thể tự thực nghiệm lại trên code Pytorch

### 2.1. Large weight initialization

Ví dụ khởi tạo ở $w = 6.74$ ở node 1, $L_{w1}'$ sẽ còn rất nhỏ (bằng $9*10^{-7}$) làm xảy ra Gradient Vanishing

![alt text](image.png)

> [!TIP]
> Bởi Gradient được hình thành từ chain-rule , vậy chỉ cần gradient local rất nhỏ (ví dụ = 0.001 ) khi nhân cùng với những gradient local khác cũng làm global gradient trở nên nhỏ 

Vậy trong mạng Nơ ron ở case này , phần gradient local đạt giá trị nhỏ là : 

$$
\frac{\partial sigmoid}{\partial z}
$$

Chúng ta đi qua phần đạo hàm Sigmoid để visualization :

$$
\text{sigmoid}'(x) = \text{sigmoid}(x) \cdot \left(1 - \text{sigmoid}(x) \right)
$$

Visualization : theo hình bạn thấy từ khoảng $(-\infty, -4) \, \text{và} \, (4, \infty)$ giá trị đạo hàm dường như tiến đến 0.

![alt text](image-1.png)

Quay trở lại bài , ta có : 

$$
z = wx + b 
$$

$$
z = 14,15 
$$

$$
\frac{d}{dz}\text{sigmoid}(z) = \text{sigmoid}(z) \cdot (1 - \text{sigmoid}(z))
$$

$$
\text{sigmoid}'(14.15) = 0.9999992912 \cdot (1 - 0.9999992912) 
$$
$$
\text{sigmoid}'(14.15) \approx 7.08 \times 10^{-7}
$$

Đến đây chắc bạn cũng hiểu tại sao gradient local của $\frac{\partial sigmoid}{\partial z}$  quá nhỏ làm xảy ra hiện tượng Gradient Vanishing

### 2.2. Using appropriate weight initialization

Trong ví dụ này, chúng ta giả định đã tìm được trọng số thích hợp, nhưng model lại có rất nhiều layer.

a. Ví dụ 1 :

Trước khi đi vào bài toán chính, ta ví dụ một bài tính toán tiểu học chút: Giả sử bạn có một số ban đầu là 100 và mỗi lần bạn nhân số này với 0.1 (tương tự như gradient nhỏ dần qua từng lớp). Hãy tính giá trị sau 5 lần nhân

$$
\text{Lần 1: } 100 \times 0.1 = 10
$$
$$
\text{Lần 2: } 10 \times 0.1 = 1 
$$
$$
\text{Lần 3: } 1 \times 0.1 = 0.1 
$$
$$
\text{Lần 4: } 0.1 \times 0.1 = 0.01 
$$
$$
\text{Lần 5: } 0.01 \times 0.1 = 0.001
$$

Vậy càng nhân với số nhỏ $< 1$ thì càng nhân càng tiến đến gần $0$ 

b. Ví dụ 2:

![alt text](image-2.png)

> [!NOTE]
> Nhìn hình ta thấy , giá trị maximum của đạo hàm là xấp xỉ 0.25

Ví dụ cho mạng nơ ron 2 layer ta tính như sau :
$$
\frac{dy}{dx} = \frac{df^{(2)}}{df^{(1)}} \cdot \frac{df^{(1)}}{dx} 
$$$$
\frac{dy}{dx} = 0.25 \cdot \frac{df^{(1)}}{dx}
$$

> Vì vậy, càng thêm 1 layer, giá trị của Gradient Global sẽ bị giảm ít nhất  1/4 giá trị . Ví dụ 2 layer thì 0.25*0.25 = 0.0625, 3 layer thì 0.25*0.25*0.25 = 0.015625,…..

Quay trở lại bài toán chính , giả sử ta có một mạng nơ ron như sau

Tính backward

Tương tự ta cũng thấy đạo hàm giảm qua từng layer

![alt text](image-3.png)

> [!WARNING]
> Kết quả : cập nhật $\theta $ có giảm nhưng không đáng kể

![alt text](image-4.png)

### 2.3. Large weight initialization and large learning rate

Khi mà khởi tạo trọng số quá lớn,không chỉ có thể xảy ra Vanishing mà còn hiện tượng Exploding gradients (bùng nổ gradient) làm quá trình trainning không hội tụ.

![alt text](image-5.png)

Visualization : 
<div style="display: flex">
  <div>
    <img src="image-6.png" width='200px' height='200px'>
    Gradient càng tăng khi càng qua mỗi layer
  </div>
  <div>
  <img src="image-7.png" width='200px' height='200px'>
  Gradient quá lớn làm model không hội tụ được
  </div>
</div>

## 3. Cơ sở lý thuyết

### 3.1. Mean, variance, standard deviation

#### 3.1.1. Mean

<div style="display: flex;">
  <div style="width: 50%; padding-right: 1em;">
    <img src="image-10.png" alt="alt text" style="max-width: 100%;" />
  </div>
  <div style="width: 50%;">
    <p>Trung bình có trọng số của hai biến ngẫu nhiên độc lập</p>
    <img src="image-11.png" alt="alt text" style="max-width: 100%;" />
  </div>
</div>

#### 3.1.2. Variance & standard deviation

<div style="display: flex;">
  <div style="width: 50%; padding-right: 1em;">
    <p><img src="image-12.png" alt="alt text" style="max-width: 100%;" /></p>
  </div>
  <div style="width: 50%;">
    <p><strong>1. Biến đổi công thức tính phương sai</strong></p>
    <p><img src="image-13.png" alt="alt text" style="max-width: 100%;" /></p>
    <p><strong>2. Phương sai của hai biến ngẫu nhiên độc lập</strong></p>
    <p><img src="image-14.png" alt="alt text" style="max-width: 100%;" /></p>
  </div>
</div>


{{% details title="Tại sao $\sum_{i=1}^N X_i^2 P_X(X_i) = E(X^2)$" closed="true" %}}

mọi người sẽ thắc mắc phải đổi $P_X(X_i) -> P_{X^2}(X_i^2)$ lúc đó mới đúng công thức. 

Có thể giải thích như sau :

+ Chúng ta đang xem xét phân phối của cùng một biến ngẫu nhiên $ X$, trong đó chỉ thay đổi giá trị từ $X$ sang $X^2$ (khác độ lớn). Trong trường hợp này, phân phối xác suất $P(X)$ được giữ nguyên và không thay đổi.

+ Vậy nên khi thay đổi xác $X_i -> X_i^2 $ mình muốn thay đổi đầu vào nhưng vẫn để có cùng phân phối xác suất cũ thì xài lại hàm mật độ xác suất ($P_X(X_i)$) 

  ![alt text](image-15.png)

Ví dụ : 

Giả sử bạn khảo sát khoảng cách từ nhà đến trường của một nhóm học sinh. Khoảng cách $X$  được đo bằng **km**, với các giá trị và tỷ lệ xác suất sau:

- **1 km:**  30% học sinh = $P(X = 1) = 0.3$
- **2 km:** 40% học sinh. $P(X = 2) = 0.4$
- **3 km:** 30% học sinh. $P(X = 3) = 0.3$

Bây giờ, bạn đổi ý , không thích thống kê bằng $km$  mà thích $km^2$ 

$$
S = X^2
$$

nhưng bạn muốn giữ nguyên ý nghĩa thống kê của bài toán (tức là phân phối xác suất không thay đổi), chỉ thay đổi đại lượng đo lường.

- $P(X^2 = 1) = P(X = 1) = 0.3$
- $P(X^2 = 4) = P(X = 2) = 0.4$
- $P(X^2 = 9) = P(X = 3) = 0.3$

{{% /details %}}

### 3.2. Uniform & Gaussian Distribution

#### 3.2.1. Uniform Distribution

Định nghĩa: Uniform distribution mô tả trường hợp mà tất cả các sự kiện đều có khả năng xảy ra như nhau.
Đồ thị của Uniform Distribution luôn có dạng hình chữ nhật, vì xác suất của mọi sự kiện đều bằng nhau.

Ví dụ : 

- **Discrete Uniform Distribution:** Tung một con xúc xắc, mỗi mặt đều có xác suất bằng nhau (1/6).

Continuous Uniform Distribution: Xe buýt rời khỏi bến mỗi giờ một lần (every hour).Tuy nhiên, không biết thời gian cụ thể mà chuyến xe buýt gần nhất đã rời đi. Xác suất chờ trong khoảng thời gian bất kỳ được tính như thế nào?

Dựa vào uniform distribution , mình sẽ giải thích như sau

<img src="image-16.png" width = '400px'>

Vì vậy : 

- Thời gian chờ ngắn nhất là **0 phút**, do đó **Lower Bound = 0**.
- Thời gian chờ dài nhất là **60 phút**, nên **Upper Bound = 60**

Vậy xác suất chờ xe buýt mỗi phút là $\frac{1}{60}$ , hay là mỗi phút đều có xác suất xảy ra như nhau

Ví dụ xác suất chờ xe buýt trong khoảng 5-20 phút là bao nhiêu: $\frac{20-5}{60} = 0.25 = 25\%$

<img src="image-17.png" width = '400px'>

Mean and Variance

> [!NOTE]
> $$\text{với biến rời rạc : } E(X) = \sum_{i} x_i P(x_i) $$
> $$\text{với biến liên tục : } E(X) = \int_{-\infty}^{\infty} x f(x) \, dx$$

<div style="display: flex;">
  <div style="width: 50%; padding-right: 1em;">
    <img src="image-18.png" alt="alt text" style="max-width: 100%;" />
  </div>
  <div style="width: 50%;">
    <img src="image-19.png" alt="alt text" style="max-width: 100%;" />
  </div>
</div>

#### 3.2.2. Gaussian Distribution

Comming soon ...

### 3.3. Maclaurin series

Khai triển Maclaurin là một trường hợp đặc biệt của khai triển Taylor, được dùng để xấp xỉ một hàm số $f(x)$ gần đúng bằng $g(x)$ tại các giá trị gần $ x = 0$

![alt text](image-20.png)

Mục đích:

- Trong các ứng dụng thực tế, tính trực tiếp một hàm phức tạp có thể rất khó khăn hoặc tốn kém.
- Sử dụng Maclaurin giúp thay thế hàm bằng một đa thức bậc thấp, dễ dàng tính toán hơn

#### 3.3.1. Áp dụng cho Tanh

<img src="image-21.png" width = '400px'>
Maclaurin phát biểu rằng : khi giá trị x nhỏ ($\approx 0$) thì $Tanh(x) = x $.


Bây giờ chúng ta sẽ bắt đầu xây dựng và tìm khoảng khởi tạo , để làm được điều này cần phải thỏa mãn 2 quy tắc (những quy tắc này được rút ra trong quá trình thực nghiệm của NCKH). 

```python
import numpy as np
import matplotlib.pyplot as plt

# Generate x values from -0.5 to 0.5
x = np.linspace(-0.5, 0.5, 100)

# Calculate tanh(x) and its Maclaurin approximation
y = np.tanh(x)
y_approx = x  # Maclaurin approximation: tanh(x) ≈ x for small x

# Create the plot
plt.plot(x, y, label="tanh(x)", color="blue")  # Actual tanh(x)
plt.plot(x, y_approx, label="Maclaurin Approximation (x)",
         linestyle="--", color="red")  # Approximation

# Add labels and title
plt.xlabel("x")
plt.ylabel("y")
plt.title("Hyperbolic Tangent Function and Maclaurin Approximation")

# Add legend
plt.legend()

# Add grid for better readability
plt.grid(True)

# Display the plot
plt.show()
```

<img src="image-22.png" width = '400px'>

#### 3.3.2. Áp dụng cho Sigmoid

<img src="image-23.png" width = '400px'>

```python
import numpy as np
import matplotlib.pyplot as plt

# Define the sigmoid function
def sigmoid(x):
    return 1 / (1 + np.exp(-x))


# Generate x values from -0.5 to 0.5
x = np.linspace(-0.5, 0.5, 100)

# Calculate sigmoid(x) and its Maclaurin approximation
y = sigmoid(x)
y_approx = 1 / 2 + x / 4  # Maclaurin approximation for sigmoid(x)

# Create the plot
plt.plot(x, y, label="sigmoid(x)", color="blue")  # Actual sigmoid function
plt.plot(x, y_approx, label="Maclaurin Approximation (1/2 + x/4)",
         linestyle="--", color="red")  # Approximation

# Add labels and title
plt.xlabel("x")
plt.ylabel("y")
plt.title("Sigmoid Function and Maclaurin Approximation")

# Add legend
plt.legend()

# Add grid for better readability
plt.grid(True)

# Display the plot
plt.show()
```
<img src="image-24.png" width = '400px'>

> [!NOTE]
> Phương sai của các layer bằng nhau qua các lớp : Điều này giúp đảm bảo rằng các giá trị kích hoạt (activations) trong mạng không bị lệch về phía dương hoặc âm quá nhiều, làm giảm hiệu suất của quá trình học.
> $$\text{Var}(a^{[l-1]}) = \text{Var}(a^{[l]})$$

> [!NOTE]
> Mean của các hàm kích hoạt nên bằng 0 : Điều này nhằm giữ cho tín hiệu truyền qua các layer không bị giảm dần (gradient vanish) hoặc tăng quá mức (gradient explosion).
> $$\mathbb{E}(a^{[l]}) = 0$$

<div style="display: flex">
  <img src="image-8.png" width="50%">
  <img src="image-9.png" width="50%">
</div>

**Ta sẽ bắt đầu chứng minh như sau**

{{< callout type="info" >}}
bạn có thể chuyển qua về giữa $ \mathbb{E}[x_i]$ và  $\mathbb{E}[a_i^{[l-1]}]$ trong suốt quá trình chứng minh nha {{< /callout >}}

$$z_i = \left(x_1 w_1 + \cdots + x_n w_n + b\right)$$

$$\text{Var}(z_i) = \text{Var}\left(x_1 w_1 + \cdots + x_n w_n + b\right)$$

$$\text{Var}(z_i) = \text{Var}(x_1 w_1) + \text{Var}(x_2 w_2) + \cdots + \text{Var}(x_n w_n) + Var(b)$$

Xét trường hợp $\text{Var}(x_1 w_1)$ 

$\text{Var}(x_1 w_1) = \text{Var}(x_1) \text{Var}(w_1) + \text{Var}(x_1) (\mathbb{E}[w_1])^2 + \text{Var}(w_1) (\mathbb{E}[x_1])^2$

1. Chúng ta có thể khởi tạo $\mathbb{E}[w_1]= 0$ bằng cách khởi tạo $w_1 $theo Uniform distribution đối xứng qua trục $y$  or có thể tự ý khởi tạo Mean = 0 cho Gaussian Distribution :  

$$U(-r, r)$$

$$\mathbb{E} = \frac{-r + r}{2} = 0$$

2. **Chúng ta có thể khởi tạo :** 
   - **$\mathbb{E}[x_1] = 0$  bằng cách chuẩn hóa các feature $x$ trong khoảng [-1; 1]**
   - **$\mathbb{E}[a_1^{[l-1]}] = 0$ bằng cách sử dụng Tanh hoặc nếu sài Sigmoid thành $\tanh(x) = -1 + 2 \cdot \text{sigmoid}(2x)$**. Tại vì trong các hàm chỉ có hàm Tanh mới có thể có Exception bằng 0 thôi.
   <img src="image-25.png" width="300px">

3. Chúng ta có thể khởi tạo b = 0

{{< callout >}}
  Vậy tóm lại $w_1, b, x_1$, $a_1^{[l-1]}$ là đều do chúng ta khởi tạo theo ý muốn để nó bằng 0
{{< /callout >}}

Do đó , ta có thể rút gọn lại thành 

$$ \text{var}(z_i) = \text{Var}(x_1) \text{Var}(w_1) + \text{Var}(x_2) \text{Var}(w_2) +...+ \text{Var}(x_i) \text{Var}(w_i) $$

giả sử tiếp  $Var(x_1)Var(w_1) = Var(x_2)Var(w_2) = Var(x_3)Var(w_3),....  $  ta rút gọn tiếp 

$$Var(z_i) = nVar(x_i).Var(w_i)$$

Các bạn sẽ thắc mắc tại sao rút gọn từ cộng thì phải có $\sum $ , tại vì giả sử các $Var $ bằng nhau rồi mình có thể rút gọn thành vậy được . Ví dụ $2 + 2 + 2 \to 3*2$

{{< callout >}}
$$Var(z_i) = nVar(x_i).Var(w_i)$$
{{< /callout >}}

## 3. Xavier Glorot Init.

### 3.1. Tanh activation

Theo maclaurin, vừa visualization ở trên ta có thể chứng minh được:

$$a_i = \tanh(z_i) \approx z_i $$
$$\Rightarrow a_i = z_i$$ 
$$\Rightarrow \text{Var}(x_i) = \text{Var}(z_i),$$ 
$$\text{mà } \text{Var}(x_i) = \text{Var}(a_i)$$ 
$$\Rightarrow \text{Var}(x_i) = \text{Var}(z_i). $$
$$\text{Thế vào:} \text{Var}(z_i) \to \text{Var}(z_i) = n \, \text{Var}(z_i) \cdot \text{Var}(w_i) $$
$$\Rightarrow \text{Var}(w_i) = \frac{1}{n}.$$

Với giải thiết $\text{Var}(w_i) = \frac{1}{n}$ khởi tạo $w_i$ như nào để $E(w_i) = 0$ 

<div style="display: flex">
  <div>
    <p>Với uniform distribution , thì khởi tạo trong khoảng đối xứng</p>
    <img src='image-28.png' width='80%'>
  </div>
  <div>
    <p>Với Gaussian distribution</p>
    <img src='image-30.png' width='80%'>
  </div>
</div>

{{< callout type="info" >}}
Kết luận:

![alt text](image-31.png)
{{< /callout >}}

### 3.2. Sigmoid activation

Theo Maclaurin: 

$$ a_i = \sigma(z_i) \approx \frac{1}{2} + \frac{z_i}{4} $$
$$\text{Var}\left(a\right) = \text{Var}\left(\frac{1}{2} + \frac{z}{u}\right) = Var\left(\frac{1}{2}\right)+\left(\frac{z}{u}\right) $$
$$\text{Do } \frac{1}{2} \text{ là hằng số: }
\text{Var}\left(a\right) = \text{Var}\left(\frac{z}{u}\right)$$
$$\text{Tương tự cho: } \mathbb{E}\left(\frac{1}{2} + \frac{z}{u}\right) = \mathbb{E}\left(\frac{z}{u}\right)$$

$$
\text{Chứng minh Kỳ vọng:} \\
\mathbb{E}\left(\frac{z}{u}\right) = \sum_i \frac{z}{u} p(z) = \frac{1}{u} \sum_i z p(z) = \frac{1}{u} \mathbb{E}(z) 
$$

$$
\text{Chứng minh phương sai:}
$$
$$
\text{Var}\left(\frac{z}{u}\right) = \mathbb{E}\left[\left(\frac{z}{u} - \mathbb{E}\left(\frac{z}{u}\right)\right)^2\right]
$$
$$
\text{Var}\left(\frac{z}{u}\right) = \sum_i \left(\frac{z}{u} - \frac{1}{u} \mathbb{E}(z)\right)^2 p(z) 
$$
$$
= \frac{1}{u^2} \sum_i \left(z - \mathbb{E}(z)\right)^2 p(z)
$$
$$
= \frac{1}{u^2} \text{Var}(z)
$$

$$
\text{Thay } u = 4: \\
\text{Var}\left(\frac{z}{4}\right) = \frac{1}{16} \text{Var}(z)
$$


<div style="display: flex">
  <div>
    <p>Với uniform distribution , thì khởi tạo trong khoảng đối xứng</p>
    <img src='image-32.png' width='80%'>
    <img src='image-33.png'width='80%'>
  </div>
  <div>
    <p>Với Gaussian distribution</p>
    <img src='image-34.png' width='80%'>
    <img src='image-35.png' width='80%'>
  </div>
</div>

## 4. He Initialization

$$
\text{Var}(a^{[l-1]} \cdot w) = \text{Var}(a^{[l-1]}) \cdot \text{Var}(w) $$

$$\text{+ } \text{Var}(a^{[l-1]}) \cdot (\mathbb{E}(w))^2 \text{+ } \text{Var}(w) \cdot (\mathbb{E}(a^{[l-1]}))^2 $$
$$
= \text{Var}(w) \left[ \text{Var}(a^{[l-1]}) + (\mathbb{E}(a^{[l-1]}))^2 \right]
$$
mà ta có : 

$$ \mathbb{E}(a^{2^{(l-1)}}) = (\mathbb{E}(a^{[l-1]}))^2 + \text{Var}(a^{[l-1]})$$

Thay vào:

$$\text{Var}(a \cdot w) = \text{Var}(w) \cdot \mathbb{E}(a^{2^{[l-1]}}) $$

Xét $\mathbb{E}(a^2)$ có thể coi là  $\mathbb{E}(a^{2^{[l-1]}})$ cũng được nha:

$$\mathbb{E}(a^2) = \int_{-\infty}^{\infty} a^2 f(a) \, da $$
$$
= \int_{-r}^{0} a^2 f(a) \, da + \int_{0}^{r} a^2 f(a) \, da $$
$$
= \int_{0}^{r} z^2 f(z) \, dz $$ $$
= \frac{1}{2} \int_{0}^{r} z^2 f(z) \, dz = \frac{1}{2} \text{Var}(z)$$

{{% details title="Các bạn sẽ thắc mắc tại sao từ $a$ thành $z$ , $\int_{-r}^{0} a^2 f(a) \, da = 0$" closed="true" %}}

![alt text](image-36.png)

{{% /details %}}

Thay vào:

$$\text{Var}(a \cdot w) = \text{Var}(w) \cdot \frac{1}{2} \text{Var}(z^{[l-1]})$$

$$
\text{Var}(z_i^{[l]}) = n \cdot \text{Var}(a_i \cdot w_i) 
$$
$$
= n \cdot \text{Var}(w_i) \cdot \frac{1}{2} \text{Var}(z^{[l-1]})
$$
$$
\text{Mà } \text{Var}(z_i^{[l]}) = \text{Var}(z_i^{[l - 1]})  \text{Như lí thuyết đề cập.}
$$
$$
=> \text{Var}(w_i) = \frac{2}{n}
$$

Thay vào uniform/Gaussian distribution tương tự Xavier

![alt text](image-37.png)

![alt text](image-38.png)

## 5. Câu hỏi ôn tập

{{% details title="Tại sao không thể khởi tạo tham số bằng giá trị cố định như 0 hoặc số âm?" closed="true" %}}

Vì tham số cố định không linh hoạt, gây Gradient Vanishing hoặc làm mạng không học được do thiếu tính ngẫu nhiên.

{{% /details %}}

{{% details title="Xavier Glorot Init và Kaiming He Init khác nhau ở điểm nào?" closed="true" %}}

- Xavier Init phù hợp với Sigmoid/Tanh, đảm bảo cân bằng;
- Kaiming He Init tối ưu cho ReLU, giúp tránh Gradient Vanishing hiệu quả hơn.

{{% /details %}}

{{% details title="Tại sao Gradient Vanishing xảy ra khi sử dụng hàm Sigmoid, đặc biệt với giá trị $z$ rất lớn hoặc rất nhỏ?" closed="true" %}}

Hàm Sigmoid có đạo hàm:  $\sigma'(z) = \sigma(z) \cdot (1 - \sigma(z))$. 
    
- Khi z lớn (z > 4), $\sigma(z) \to 1, \quad \sigma'(z) \to 0$,
- Tương tự, khi z nhỏ (z < -4),  $\sigma(z) \to 0, \quad \sigma'(z) \to 0$
    
Vì Gradient là tích của nhiều đạo hàm qua các lớp, các giá trị nhỏ này làm Gradient global gần bằng 0, gây ra hiện tượng Gradient Vanishing.

{{% /details %}}

{{% details title="Sử dụng hàm Sigmoid với $z = 14.15$ giá trị Gradient tại điểm này là bao nhiêu và ý nghĩa của nó?" closed="true" %}}

Với z = 14.15:

$$
\sigma'(z) = \sigma(z) \cdot (1 - \sigma(z))$$
$$
\sigma(14.15) \approx 0.9999992912,  $$
$$
\quad \sigma'(14.15) \approx 0.9999992912 \cdot (1 - 0.9999992912) \approx 7.08 \times 10^{-7}
$$

Gradient rất nhỏ, làm giảm khả năng cập nhật tham số trong mạng, dẫn đến khó học hiệu quả. Đây là minh chứng điển hình cho Gradient Vanishing.

{{% /details %}}

{{% details title="Tại sao gradient giảm qua từng layer trong mạng nơ-ron sâu?" closed="true" %}}

Gradient được tính bằng quy tắc chuỗi (chain rule):

$$
\frac{dy}{dx} = \frac{df^{(n)}}{df^{(n-1)}} \cdot \frac{df^{(n-1)}}{df^{(n-2)}} \cdots \frac{df^{(1)}}{dx}
$$

Nếu mỗi đạo hàm $\frac{df}{dx}$  có giá trị nhỏ $(<1)$, tích của chúng sẽ giảm dần, dẫn đến hiện tượng Gradient Vanishing.

{{% /details %}}

{{% details title="Giá trị đạo hàm lớn nhất của hàm kích hoạt Sigmoid là bao nhiêu, và tại điểm nào trên đồ thị đạo hàm?" closed="true" %}}

![alt text](image-39.png)

Giá trị đạo hàm lớn nhất của hàm kích hoạt Sigmoid là 0.25, xảy ra khi z = 0.

Điều này cho thấy khả năng truyền thông tin qua các lớp giảm mạnh khi giá trị z nằm ngoài khoảng gần 0, dẫn đến vấn đề Gradient Vanishing khi tích lũy qua nhiều lớp.

{{% /details %}}

{{% details title="Trong mạng 5 layers, tại sao giá trị  $\theta$  giảm nhưng không đáng kể khi backward?" closed="true" %}}

Gradient tại mỗi layer rất nhỏ, ví dụ:

$w_1 = w_1 - \eta L'_w \implies 0.919 - 0.01 \times (-0.0002) = 0.919002$

Sự thay đổi 0.000002 là không đáng kể, minh họa rõ vấn đề Gradient Vanishing.

{{% /details %}}

{{% details title="Exploding Gradient xảy ra khi nào và nó ảnh hưởng như thế nào đến quá trình training?" closed="true" %}}

Exploding Gradient xảy ra khi trọng số được khởi tạo quá lớn hoặc learning rate quá cao, làm Gradient tăng mạnh qua từng layer. Điều này khiến quá trình training không hội tụ do cập nhật trọng số không ổn định.

{{% /details %}}

{{% details title="Tại sao Exploding Gradient làm mô hình không hội tụ?" closed="true" %}}

Gradient quá lớn dẫn đến cập nhật trọng số vượt mức, khiến hàm mất mát dao động mạnh thay vì giảm dần, làm mô hình không thể học được hiệu quả.

![alt text](image-40.png)

{{% /details %}}

{{% details title="Công thức tính giá trị kỳ vọng $E(X)$ của một biến ngẫu nhiên rời rạc là gì?" closed="true" %}}

$$
E(X) = \sum_{i=1}^{N} X_i P_X(X_i)  
$$

{{% /details %}}

{{% details title="Làm thế nào để tính giá trị kỳ vọng của tích hai biến ngẫu nhiên độc lập $X \text{ và } Y$?" closed="true" %}}

Nếu $X \text{ và } Y$ độc lập, kỳ vọng của tích được tính như sau:

$$
E(XY) = E(X)E(Y)
$$

{{% /details %}}

{{% details title="Công thức tính giá trị kỳ vọng $E(X)$ khác nhau như thế nào giữa biến rời rạc và biến liên tục?" closed="true" %}}

- Với biến rời rạc:

$$
E(X) = \sum_{i} x_i P(x_i)
$$

- Với biến liên tục:

$$
E(X) = \int_{-\infty}^{\infty} x f(x) dx
$$

{{% /details %}}

{{% details title="Biến đổi công thức tính phương sai  $\text{var}(X)$  là gì?" closed="true" %}}

$$
\text{Var}(X) = E(X^2) - (E(X))^2
$$

{{% /details %}}

{{% details title="Công thức tính phương sai của hai biến ngẫu nhiên độc lập $X \text{ và } Y$ là gì?" closed="true" %}}

Nếu $X \text{ và } Y$ độc lập, phương sai được tính như sau:

$$
\text{var}(XY) = \text{var}(X) \cdot \text{var}(Y) + \text{var}(X) \cdot (E(Y))^2 + \text{var}(Y) \cdot (E(X))^2
$$

- **Chứng minh:**

![alt text](image-41.png)

https://www.cse.iitm.ac.in/~vplab/courses/LARP_2018/Tutorial/T-6_soution_2018.pdf

{{% /details %}}

{{% details title="Uniform Distribution được định nghĩa như thế nào, và dạng đồ thị của nó là gì?" closed="true" %}}

Uniform Distribution mô tả các sự kiện có khả năng xảy ra như nhau.

Đồ thị của nó có dạng hình chữ nhật với chiều cao:

$$
f(x) = \frac{1}{b - a}, \text{ với } a \leq x \leq b
$$

Tổng xác suất bằng 1.

{{% /details %}}

{{% details title="Xe buýt rời khỏi bến **mỗi giờ một lần** (every hour). Tuy nhiên, **không biết thời gian cụ thể mà chuyến xe buýt gần nhất đã rời đi.** Xác suất chờ trong khoảng thời gian bất kỳ được tính như thế nào?" closed="true" %}}

Với xe buýt rời bến mỗi giờ, thời gian chờ ngắn nhất là $a = 0$ phút và dài nhất là $b = 60$ phút, $a'$ là cận dưới khoảng mình muốn chờ , $b'$ là cận trên khoảng mình muốn chờ.

Theo Continuous Uniform Distribution, xác suất chờ trong mỗi phút được tính bằng công thức:

$$
f(x) = \frac{b'- a'}{b - a} = \frac{b'- a'}{60}, \text{ với } 0 \leq b'- a' \leq 60
$$

{{% /details %}}

{{% details title="Công thức tính phương sai $\text{var}(X)$ trong phân phối đều $X \sim U(a, b)$ là gì?" closed="true" %}}

Với phân phối đều:

$$
\text{var}(X) = \frac{(b - a)^2}{12}
$$

Phương sai thể hiện độ phân tán của giá trị $X$ trong khoảng đều từ $a$ đến $b$.

{{% /details %}}

{{% details title="Khai triển Maclaurin là gì và công thức tổng quát của nó như thế nào?" closed="true" %}}

Khai triển Maclaurin là một trường hợp đặc biệt của khai triển Taylor, dùng để xấp xỉ hàm $f(x) \text{ tại } x = 0$. Công thức:

$$
f(x) = \sum_{n=0}^{\infty} \frac{f^{(n)}(0)}{n!} x^n
$$

{{% /details %}}

{{% details title="Tại sao khai triển Maclaurin được sử dụng trong các ứng dụng thực tế?" closed="true" %}}

Khai triển Maclaurin thay thế hàm số phức tạp bằng một đa thức bậc thấp, dễ dàng tính toán hơn, tiết kiệm tài nguyên.

Nó đặc biệt hữu ích khi việc tính trực tiếp một hàm phức tạp là khó khăn hoặc tốn kém.

{{% /details %}}

{{% details title="Theo Xavier Initialization, khi sử dụng activation **Tanh**, khoảng giá trị nào là ổn định cho việc khởi tạo trọng số?" closed="true" %}}

![alt text](image-42.png)

{{% /details %}}

{{% details title="Theo Xavier Initialization, khi sử dụng activation **Sigmoid**, khoảng giá trị nào là ổn định cho việc khởi tạo trọng số?" closed="true" %}}

![alt text](image-43.png)

![alt text](image-44.png)

{{% /details %}}

{{% details title="Theo He Initialization, khi sử dụng activation ReLU, khoảng giá trị nào là ổn định cho việc khởi tạo trọng số?" closed="true" %}}

![alt text](image-45.png)

![alt text](image-46.png)

{{% /details %}}


