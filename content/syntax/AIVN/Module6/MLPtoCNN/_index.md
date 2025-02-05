---
title: MLP to CNN
math: true
---

## CNN-OnlyConv

Trước khi vào bài code thì chúng ta sẽ xem nhược điểm của các mô hình như MLP trên một số tập dữ liệu như FashionMNIST, Cifar10. Tôi sẽ áp dụng Conv để so sánh độ hiệu quả.

{{% details title="MLP_FashionMNIST_ReLU_He_Adam" closed="true" %}}

{{% jupyter "1.CNN-OnlyConv/0.MLP_FashionMNIST_ReLU_He_Adam.ipynb" %}}

{{% /details %}}
{{% details title="MLP_Cifar10_ReLU_He_Adam" closed="true" %}}

{{% jupyter "1.CNN-OnlyConv/1.MLP_Cifar10_ReLU_He_Adam.ipynb" %}}

{{% /details %}}


{{% details title="MLP_Cifar10_ReLU_He_Adam_2H" closed="true" %}}

{{% jupyter "1.CNN-OnlyConv/2.MLP_Cifar10_ReLU_He_Adam_2H.ipynb" %}}

{{% /details %}}

{{% details title="MLP_Cifar10_ReLU_He_Adam_3H" closed="true" %}}

{{% jupyter "1.CNN-OnlyConv/3.MLP_Cifar10_ReLU_He_Adam_3H.ipynb" %}}

{{% /details %}}

{{% details title="FashionMNIST_CNN_onlyConv2d" closed="true" %}}

{{% jupyter "1.CNN-OnlyConv/4.FashionMNIST_CNN_onlyConv2d.ipynb" %}}

{{% /details %}}

{{% details title="Cifar10_CNN_onlyConv2d" closed="true" %}}

{{% jupyter "1.CNN-OnlyConv/5.Cifar10_CNN_onlyConv2d.ipynb" %}}

{{% /details %}}

## Downsampling

Trong đoạn code dươi sẽ downsampling bằng 3 cách:
+ MaxPooling
+ AveragePooling
+ Interpolate

{{% details title="FashionMNIST_mp" closed="true" %}}

{{% jupyter "2.downsampling/1.FashionMNIST_mp.ipynb" %}}

{{% /details %}}

{{% details title="FashionMNIST_ap" closed="true" %}}

{{% jupyter "2.downsampling/2.FashionMNIST_ap.ipynb" %}}

{{% /details %}}

{{% details title="FashionMNIST_resize" closed="true" %}}

{{% jupyter "2.downsampling/3.FashionMNIST_resize.ipynb" %}}

{{% /details %}}

## Transition_CNN

{{% details title="Tradition" closed="true" %}}

{{% jupyter "3.Trandition_CNN/Tradition.ipynb" %}}

{{% /details %}}

{{% details title="CNN" closed="true" %}}

{{% jupyter "3.Trandition_CNN/CNN.ipynb" %}}

{{% /details %}}