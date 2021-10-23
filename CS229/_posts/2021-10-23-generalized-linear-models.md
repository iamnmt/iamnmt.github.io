---
author_profile: true
toc: true
toc_label: "Mục lục"
toc_icon: "bars"
---

# Tóm tắt

# Tổng quan

Mô hình học có giám sát khi được đặt dưới những giả thiết nhất định thì trở thành Generalized Linear Model (**GLM**). Đối với một **GLM**, ta sẽ dễ dàng xây dựng được hàm giả thiết, công thức cập nhật tham số và biện luận về tính lồi của hàm Negative Log-Likelihood. Từ đó, ta có thể đưa ra một quy trình tổng quát để thiết kế các mô hình học có giám sát trên.

# Mô hình học có giám sát
Xét tập dữ liệu: $$S= \{(x^{(1)}, y^{(1)}), (x^{(2)}, y^{(2)}),...,(x^{(n)}, y^{(n)}) \}$$. Trong đó, mỗi $$x^{(i)} \in \mathcal{X}$$ được gọi là một điểm dữ liệu và giá trị $$y^{(i)} \in \mathcal{Y}$$ tướng ứng được gọi là nhãn của điểm dữ liệu đó.

Khi này, ta muốn tìm một hàm giả thiết được tham số bởi $$\theta \in \Theta$$:


$$
\begin{equation}
\begin{split}
h:\mathcal{X} &\to \mathcal{Y} \\
x &\mapsto h(x; \theta)
\end{split}
\end{equation}
$$


Sao cho $$h(x^{(i)}; \theta)$$ là một xấp xỉ đủ tốt của $$y^{(i)}$$.

Để xây dựng được hàm giả thiết như mong đợi ta cần:

- Chọn dạng của hàm số $$h$$. Việc làm này sẽ phụ thuộc vào đánh giá của người thiết kế dựa trên không gian $$\mathcal{X},\mathcal{Y}$$  và mục tiêu của mô hình.
- Chọn tham số $$\theta$$. Được thực hiện bằng cách tối ưu hàm hợp lí phụ thuộc vào $$h(x^{(i)}; \theta)$$ và $$y^{(i)}$$.

| ![](../../assets/post_img/glm/h1.png) |
| :---: |
| *Hình 1: Mô phỏng quá trình cập nhật tham số $$\theta$$.* |


# Exponential family

Trước khi bước vào xây dựng Generalized Linear Model (**GLM**), ta sẽ đề cập đến một thành phần quan trọng trong **GLM** là *exponential family*. Ta sẽ định nghĩa khi nào một phân phối thuộc *exponential family* và chỉ ra một vài phân phối thường gặp như: phân phối chuẩn, phân phối Bernoulli cũng thuộc vào *exponential family*.

## Định nghĩa

Một phân phối thuộc *exponential family* là khi hàm xác suất của nó có thể biểu diễn dưới dạng


$$
\begin{equation}
p(y; \eta) = b(y) \exp (\eta^T T(y) - a(\eta))
\end{equation}
$$


Trong đó:

- $$\eta$$ được gọi là *natural parameter* (hoặc *canonical parameter*) của phân phối.
- $$T(y)$$ được gọi là *sufficient statistic*.
- $$a(\eta)$$ được gọi là *log partition function*.

Với những hàm $$T, a, b$$ cố định, ta có họ phân phối được tham số bởi $$\eta$$. Khi thay đổi $$\eta$$, ta có được một phân phối cụ thể trong họ phân phối đó.

Ví du: Với những hàm $$T, a, b$$ cố định, ta có họ phân phối chuẩn $$\mathcal{N}(\mu, \sigma)$$. Với một giá trị $$\eta_0$$ cụ thể, ta được một phân phối chuẩn $$\mathcal{N}(\mu_0, \sigma_0)$$.

Ngoài ra, giá trị $$\exp(-a(\eta))$$ đóng vai trò là hằng số chuẩn hóa khiến hàm xác suất khi lấy tích phân luôn bằng 1.

## Ví dụ

### Phân phối Bernoulli

Xét một phân phối $$\text{Bernoulli}(\phi)$$ có hàm khối xác suất là:


$$
\begin{aligned}
p(y; \phi) &= \phi^y(1-\phi)^{1-y} \\
&= \exp(y \log \phi + (1-y)\log(1 - \phi)) \\
&= \exp \left( \left( \log \left( \frac{\phi}{1-\phi} \right) \right)y + \log(1 - \phi) \right)
\end{aligned}
$$


Khi này, ta chọn:


$$
\begin{equation}
\begin{split}
\eta &= \log \left( \frac{\phi}{1 - \phi} \right) \\
T(y) &= y \\
a(\eta) &= - \log(1 - \phi) \\
&= \log(1 + e^{\eta}) \\
b(y) &= 1
\end{split}
\end{equation}
$$


Vậy phân phối Bernoulli thuộc exponential family.

### Phân phối chuẩn

Xét một phân phối chuẩn $$\mathcal{N}(\mu, \sigma)$$ có hàm mật độ xác suất là:


$$
\begin{aligned}
f(y;\mu,\sigma) &= \frac{1}{\sigma \sqrt{2\pi}}\exp\left(-\frac{(y- \mu)^2}{2\sigma^2} \right) \\
&=\frac{1}{\sqrt{2\pi}}\exp \left(\frac{\mu}{\sigma^2}y - \frac{1}{2\sigma^2}y^2 - \left( \frac{\mu^2}{2\sigma^2} + \log \sigma\right) \right)
\end{aligned}
$$


Khi đó, ta chọn:


$$
\begin{equation}
\begin{split}
\eta &= \left[\frac{\mu}{\sigma^2}, -\frac{1}{2\sigma^2} \right]^T \\
T(Y) &= [y, -y^2]^T \\
a(\eta) &= \frac{\mu^2}{2\sigma^2} + \log \sigma \\
&= -\frac{\eta_1^2}{4\eta_2} + \frac{1}{2} \log \left \vert \frac{1}{2 \eta_2} \right \vert \\
b(y)&= \frac{1}{\sqrt{2\pi}}
\end{split}
\end{equation}
$$


Vậy phân phối chuẩn thuộc exponential family.

Ngoài ra, ta có thể biến đổi hàm mật độ của phân phối chuẩn $$\mathcal{N}(\mu, 1)$$ như sau:


$$
\begin{aligned}
f(y;\mu,1) &= \frac{1}{\sqrt{2\pi}}\exp\left(-\frac{1}{2}(y- \mu)^2 \right) \\
&=\frac{1}{\sqrt{2\pi}} \exp \left( - \frac{1}{2}y^2 \right) \exp\left(\mu y - \frac{1}{2}\mu^2 \right)
\end{aligned}
$$


Khi đó, ta chọn:


$$
\begin{aligned}
\eta &= \mu \\
T(y) &= y \\
a(\eta) &= \frac{\mu^2}{2} \\
&= \frac{\eta^2}{2} \\
b(y) &= \frac{1}{\sqrt{2\pi}}\exp \left(- \frac{y^2}{2} \right)
\end{aligned}
$$


> Trong thực tế, ta sẽ thường mô hình dựa trên phân phối chuẩn $$\mathcal{N}(\mu, 1)$$ nhiều hơn là trên phân phối chuẩn tổng quát $$\mathcal{N}(\mu, \sigma)$$. Do vậy, cách chọn tham số $$\eta$$ và các hàm $$T, a, b$$ trong trường hợp 2 sẽ thường gặp hơn.

# Xây dựng GLM

Xét một điểm điểm dữ liệu $$x \in \mathcal{X}$$ là giá trị xác định của vector ngẫu nhiên $$X$$. Nhãn của điểm dữ liệu $$x$$ là $$y \in \mathcal{Y}$$, với $$y$$ là giá trị xác định của vector ngẫu nhiên $$Y$$. Ở đây, ta xét trường hợp rộng là $$X$$ và $$Y$$ đều là vector ngẫu nhiên nhưng sẽ hoàn toàn tương tự trong trường hợp một trong hai hoặc cả hai là biến ngẫu nhiên.

Khi này, để mô hình học có giám sát trở thành một **GLM** thì ta cần đưa ra những giả thiết sau:

1. $$Y \vert X \sim \text{ExponentialFamily}(\eta)$$. Nói cách khác, khi biết $$x$$, giá trị của $$y$$ sẽ tuân theo một phân phối thuộc *exponential family*.
2. Giá trị ta muốn dự đoán là kỳ vọng của $$T(Y) \vert X$$. Nói cách khác, hàm giả thiết sẽ có dạng $$h(x) = \mathbb{E}[T(Y) \vert X]$$   Trong những trường hợp phổ biến thì $$T(Y) = Y$$, lúc này ta đang muốn dự đoán $$Y \vert X$$, hay hàm giả thiết sẽ có dạng $$h(x) = \mathbb{E}[Y \vert X]$$.
3. Tham số $$\eta$$ có mối quan hệ tuyến tính với $$x$$, hay $$\eta = \theta^T x$$. Trong đó, $$\theta \in \Theta$$.

Giả thiết 2 là một giả thiết rất tự nhiên để tìm dạng của hàm giả thiết $$h$$.

Giả thiết 3 phần nào khó giả thích khi đưa ra mối quan hệ tuyến tính trên, nhưng nhờ vậy ta đưa mô hình ban đầu thành **GLM** từ đó có những tính chất rất hữu ích.

| ![](../../assets/post_img/glm/h2.png) |
| :---: |
| *Hình 2: Mô phỏng quá trình tạo ra nhãn của điểm dữ liệu.* |



# Tính chất của GLM

Từ giả thiết của phần trước, ta có $$Y \vert X \sim \text{ExponentialFamily}(\eta)$$ nên $$Y|X$$ sẽ có hàm xác suất là:


$$
p(y ; \eta) = b(y) \exp (\eta^T T(y) - a(\eta))
$$


Vì $$\eta = \theta^T x$$ nên ta thường viết $$\mathbb{E}[T(y) \vert X]$$ thành $$\mathbb{E}[T(y); \eta]$$; viết $$\text{Var}(T(Y) \vert X)$$ thành $$\text{Var}(T(Y); \eta)$$ nhằm nhấn mạnh hàm số được tham số bởi $$\eta$$.

## $$\mathbb{E}[T(Y) ; \eta] = \frac{\partial}{\partial \eta} a(\eta)$$

Với tính chất này, ta sẽ có một cơ sở chặt chẽ để chọn hàm giả thiết cho mô hình vì: $$h(x) = \mathbb{E}[T(Y) ; \eta] = \frac{\partial}{\partial \eta} a(\eta)$$. Trong đó, hàm số $$a$$ đã xác định khi ta chọn phân phối cho $$Y \vert X$$. Ngoài ra, việc tính $$\frac{\partial}{\partial \eta}a(\eta)$$ tương đối dễ.

Ta sẽ đi chứng minh tính chất trên.

Vì $$p(y; \eta)$$ là hàm xác suất nên:


$$
\begin{aligned}
\int_{\mathcal{Y}}p(y; \eta)dy &= 1 \\
\implies \int_{\mathcal{Y}}b(y) \exp(\eta \; T(y)) \frac{1}{\exp(a(\eta))}dy&= 1 \\
\implies \int_{\mathcal{Y}}b(y) \exp(\eta \; T(y))dy &= \exp(a(\eta)) \\
\implies \frac{\partial}{\partial \eta} \int_{\mathcal{Y}} b(y) \exp(\eta \; T(y))dy &= \frac{\partial}{\partial \eta}\exp(a(\eta)) \\
\implies \int_{\mathcal{Y}} b(y) \frac{\partial}{\partial \eta} \exp(\eta \; T(y))dy &=\exp(a(\eta)) \frac{\partial}{\partial \eta} a(\eta) \\
\implies \int_{\mathcal{Y}} T(y) b(y)  \exp(\eta \; T(y)) \frac{1}{\exp(a(\eta))}dy &= \frac{\partial}{\partial \eta} a(\eta) \\
\implies \int_{\mathcal{Y}} T(y) b(y)  \exp(\eta \; T(y) - a(\eta))dy &= \frac{\partial}{\partial \eta} a(\eta) \\
\implies \mathbb{E}[T(y); \eta] &= \frac{\partial}{\partial \eta} a(\eta)
\end{aligned}
$$


Vậy ta đã chứng minh được tính chất trên.

## $$\text{Var}(T(Y); \eta) = \frac{\partial^2}{\partial \eta^2} a(\eta)$$

Chứng minh tính chất này sẽ giúp ta lập luận về tính lồi của hàm Negative Log-Likelihood.

Ta có:


$$
\begin{aligned}
\frac{\partial}{\partial \eta} a(\eta) &= \int_{\mathcal{Y}} T(y) b(y)  \exp(\eta \; T(y) - a(\eta))dy \\
\implies \frac{\partial^2}{\partial \eta^2} a(\eta) &= \frac{\partial}{\partial \eta} \int_{\mathcal{Y}} T(y) b(y)  \exp(\eta \; T(y) - a(\eta)) dy \\
&=\int_{\mathcal{Y}} T(y) b(y)  \exp(\eta \; T(y) - a(\eta)) \left(T(y) - \frac{\partial}{\partial \eta} a(\eta)\right)dy \\ 
&=\int_{\mathcal{Y}} T(y)^2 b(y)  \exp(\eta \; T(y) - a(\eta)) - \frac{\partial}{\partial \eta} a(\eta) \int_{\mathcal{Y}} T(y) b(y)  \exp(\eta \; T(y) - a(\eta)) \\
&= \mathbb{E}[T(Y)^2; \eta] - \frac{\partial}{\partial \eta} a(\eta) \mathbb{E}[T(Y); \eta] \\
&= \mathbb{E}[T(Y)^2; \eta] - \mathbb{E}[T(Y); \eta]^2 \\
&= \text{Var}(T(y); \eta)
\end{aligned}
$$



# Kết luận

# Thuật ngữ sử dụng



