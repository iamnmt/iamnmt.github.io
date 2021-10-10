---
author_profile: true
toc: true
toc_label: "Mục lục"
toc_icon: "bars"
---

# Tóm tắt

Mở đầu bài viết, ta sẽ giới thiệu Naive Bayes classifier (**NB**) là một thuật toán để lọc tin nhắn spam và cách để biểu diễn tin nhắn dưới dạng các điểm dữ liệu.

Tiếp đến, ta sẽ xây dựng mô hình toán học cho **NB**, đề cập đến giải thiết Naive Bayes và kỹ thuật **Laplace smoothing**.

Cuối cùng, ta sẽ xây dụng **NB** trong python và triển khai trên tập dữ liệu *SMS spam dataset* được phát triển bởi Tiago A. Almedia và Jose Marıa Gomez Hidalgo [^1]

# Tổng quan

Naive Bayes classifier (**NB**) là thuật toán học có giám sát được dùng để gắn nhẵn (nhị phân) cho những điểm dữ liệu rời rạc. Cụ thể hơn, **NB** thường được dùng để phân loại tin nhắn là spam hoặc không. Do đó, ở đây ta sẽ xây dựng **NB** dùng để phân loại tin nhắn.

# Biểu diễn tin nhắn thành điểm dữ liệu

Ở đây, ta sẽ làm việc trên tập dữ liệu *SMS spam dataset* được phát triển bởi Tiago A. Almedia và Jose Marıa Gomez Hidalgo [^1]. Do đó, những tin nhắn được đề cập ở đây sẽ bằng tiếng Anh và những ký tự đặc biệt.

Một tin nhắn bao gồm:

- Nội dung tin nhắn
- Tin nhắn là spam hoặc không

Trước nhất, ta sẽ liệt kê những chữ và ký tự đặc biệt xuất hiện trong tập dữ liệu (*training*) thành một danh sách có số thứ tự:

1. a
2. about
3. above
4. ...

Ta gọi danh sách này là **vocabulary** của tập dữ liệu, giả sử danh sách này bao gồm $$d$$ chữ.

> Khi này, nội dung một tin nhắn sẽ được biểu diễn bằng một vector $$x \in \{0;1\}^d$$. Nếu chữ thứ $$j$$ xuất hiện trong tin nhắn thì $$x_j = 1$$ ; ngược lại $$x_j=0$$.

Ví dụ: Xét một tin nhắn có nội dung:

```
Almost there, see u in a sec
```

Nội dung tin nhắn trên sẽ được biểu diễn bằng:


$$
\begin{equation}
x = 
\begin{bmatrix}
\quad 1 \quad \\
\quad 0 \quad \\
\vdots \\
\quad 1 \quad \\
\vdots \\
\quad 0 \quad
\end{bmatrix} 
\quad
\begin{array}{c}
\text{a} \\
\text{above}\\
\vdots \\
\text{tree}\\
\vdots\\
\\
\end{array}
\end{equation}
$$


> Khi này, tập dữ liệu được xác định là: $$ \left \{(x^{(1)}, \, y^{(1)}), \, (x^{(2)}, \, y^{(2)}), \,..., (x^{(n)}, \, y^{(n)}) \right \}$$ với  $$x^{(i)} \in \{0;1\}^d, \; y^{(i)} \in \{0,1\}$$
>
> Trong đó:
>
> - Tin nhắn không spam: $$y^{(i)}=0$$
> - Tin nhắn spam: $$y^{(i)}=1$$

# Mô hình toán học

Ta có tập dữ liệu $$ \left \{(x^{(1)}, \, y^{(1)}), \, (x^{(2)}, \, y^{(2)}), \,..., (x^{(n)}, \, y^{(n)}) \right \}$$, với mỗi $$x^{(i)} \in \{0,1\}^d$$ là một giá trị xác định của vector ngẫu nhiên $$X^{(i)}$$. Các vector ngẫu nhiên $$X^{(i)}$$ độc lập thống kê với nhau. 

Đồng thời ta có $$n$$ biến ngẫu nhiên $$Y^{(1)}, Y^{(2)},..., Y^{(n)}$$ với $$Y^{(i)} : \Omega \rightarrow \{0,1\}$$, mỗi giá trị xác định $$y^{(i)}$$ của biến ngẫu nhiên $$Y^{(i)}$$ thể hiện tin nhắn là spam hoặc không.

Ta giả sử $$Y^{(i)} \sim \text{Bernoulli}( \phi)$$. Khi đó, hàm khối xác suất của vector ngẫu nhiên $$Y^{(i)}$$ là:


$$
\begin{equation}
\begin{split}
p_{Y^{(i)}}(y^{(i)} = q; \phi) &= 
\begin{cases}
\phi & \text{khi} \; q = 1 \\
1-\phi & \text{khi} \; q = 0 \\
\end{cases} \\
&= \phi^q (1-\phi)^{1-q} 
\end{split}
\end{equation}
$$


Ta có giả sử tiếp theo, $$X^{(i)}_j \vert  Y^{(i)} =q \sim \text{Bernoulli}(\phi_{j \vert  Y=q})$$. Khi đó, hàm khối xác suất của biến ngẫu nhiên $$ X^{(i)}_j \vert  Y^{(i)} =q $$ là:


$$
\begin{equation}
\begin{split}
p_{X^{(i)}_j | Y^{(i)} =q}(x_j^{(i)} = r|y^{(i)} = q; \phi_{j|Y=q}) &= 
\begin{cases}
\phi_{j|Y=q} & \text{khi } \; r = 1 \\
1-\phi_{j|Y=q} & \text{khi } \; r = 0 
\end{cases} \\
&= \phi_{j|Y=q}^r (1 - \phi_{j|Y=q})^{1-r}
\end{split} 
\end{equation}
$$


Để ngắn gọi trong việc trình bày, ta sẽ viết lại các hàm khối xác suất trên như sau:


$$
\begin{equation}
\begin{split}
p_{Y^{(i)}}(y^{(i)} = q; \phi) &\triangleq p(y^{(i)}) \\
p_{X^{(i)}_j | Y =q}(x_j^{(i)} = r| y^{(i)} = q; \phi_{j|Y=q}) &\triangleq p (x_j^{(i)} | y^{(i)})
\end{split}
\end{equation}
$$


Ngoài ra, ta sẽ kí hiệu cho hàm khối của vector ngẫu nhiên $$X^{(i)} \vert  Y^{(i)} = q$$ là:


$$
\begin{equation}
p_{X^{(i)} | Y^{(i)} = q}(x^{(i)} = r | y^{(i)} = q) \triangleq p(x^{(i)}|y^{(i)}) \triangleq p(x_1^{(i)}, x_2^{(i)},...,x_d^{(i)}| y^{(i)})
\end{equation}
$$


> Tóm lại, những tham số có trong **NB** là:
>
> - $$\phi$$ với $$\phi \in [0,1]$$
> - $$\phi_{1 \vert  Y = 1}, \phi_{2 \vert  Y = 1},...,\phi_{d \vert  Y = 1}$$ với $$\phi_{j \vert  Y = 1} \in [0,1]$$
> - $$\phi_{1 \vert  Y = 0}, \phi_{2 \vert  Y = 0},...,\phi_{d \vert  Y = 0}$$ với $$\phi_{j \vert  Y = 0} \in [0,1]$$

Một ví dụ về các tham số có trong **NB**:

Phân phối: $$\text{Bernoulli}( \phi)$$


$$
\def\arraystretch{1.5}
\begin{array}{c|c}
   & Spam \\
   \hline
   \phi & 0.2 \\
   1- \phi & 0.8
\end{array}
$$


Phân phối: $$\text{Bernoulli}(\phi_{j\vert Y=q})$$


$$
\def\arraystretch{1.5}
\begin{array}{c|c|c|c}
   & \text{a}  & \text{about} & \dots & \text{tree} & \dots\\
   \hline
   \phi_{j|Y=q} & 0.85 & 0.66  & \dots & 0.55 & \dots\\
   1- \phi_{j|Y=q} & 0.15 & 0.34 & \dots & 0.45 & \dots
\end{array}
$$


> Khi này, một cách toán học, điểm dữ liệu $$x^{(i)}$$ sẽ được hình thành từ hai bước:
>
> 1. $$y^{(i)}$$ được lấy ngẫu nhiên từ phân phối $$Y^{(i)} \sim \text{Bernoulli}(\phi)$$
> 2. Giả sử $$y^{(i)}=q$$, điểm dữ liệu $$x^{(i)}$$ được lấy ngẫu nhiên từ phân phối $$p(x^{(i)}\vert y^{(i)})$$.

# Giả thiết Naive Bayes

Ở phần trước, ta đề cập đến phân phối $$p(x\vert y)$$ nhưng vẫn chưa đưa ra được hàm xác suất. Do đó, ở phần này ta sẽ tìm cách mô hình phân phối $$p(x\vert y)$$.

Vì $$x \in \{0;1\}^d$$ nên để mô hình một cách chính xác phân phối $$p(x\vert y)$$ ta sẽ cần đến $$2^d-1$$ tham số. 


$$
\begin{aligned}
p(x|y) &= p(x_1, x_2,..x_d; y) \\
&= p(x_1|y) p(x_2|y, x_1) p(x_3|y, x_1, x_2) \dots p(x_d|y,x_1,...,x_{d-1})
\end{aligned}
$$


Điều này không khả thi vì thông thường sẽ có rất nhiều chữ trong **vocabulary**, nói cách khác $$d$$ sẽ rất lớn. Do đó, mô hình theo cách này sẽ cần quá nhiều tham số.

Vì lẽ đó, ta sẽ đặt ra một giả thiết nhằm giảm số lượng tham số, được gọi là **Giả thiết Naive Bayes**

> Các biến ngẫu nhiên $$X_1, X_2,...,X_d$$ sẽ độc lập với nhau khi biết $$Y$$.
>
> Một cách toán học:
> 
>
> $$
> \begin{equation}
> \begin{split}
> p(x|y) &= p(x_1, x_2,...,x_d; y) \\
> &=p(x_1|y) p(x_2|y) \dots p(x_d|y) \\
> &=\prod_{j=1}^d p(x_j|y) \\
> &=\prod_{j=1}^d \phi_{j|Y}^{x_j} (1 - \phi_{j|Y})^{1-x_j}
> \end{split}
> \end{equation}
> $$
> 
> 

Sử dụng giả thiết trên, ta đã giảm từ $$2^{d}$$ về $$2d$$ tham số của các phân phối $$\text{Bernoulli}(.)$$ 

Giả thiết này được gọi là *Naive* (ngây thơ) vì ta sẽ bỏ qua thứ tự của các thuộc tính trong một điểm dữ liệu, điều này sẽ không đúng với thực tế. Ví dụ, ta có hai tin nhắn không phải là spam có nội dung như sau:

```
Almost there, see u in a sec
```

```
see in a Almost sec u there,
```

Thì theo giả thiết trên, ta sẽ xem hai tin nhắn đó là một.

Ngoài ra, do cách ta *xây dựng điểm dữ liệu* nên còn hạn chế nữa là, ta chỉ cân nhắc **chữ thứ $$j$$ trong vocabulary có xuất hiện trong nội dung tin nhắn không?** Mà bỏ qua số lượng của chữ đó. Ví dụ, ta có hai tin nhắn không phải là spam có nội dung như sau:

```
hey what's up
```

```
hey hey hey hey hey what's up hey hey
```

Theo cách ta *xây dựng điểm dữ liệu* thì hai tin nhắn trên là một.

> Tóm lại, một điểu dữ liệu $$x$$ được hình thành từ $$d+1$$ bước:
>
> - $$y$$ được lấy ngẫu nhiên từ phân phối $$Y \sim \text{Bernoulli}(\phi)$$ 
> - Chữ thứ $$1$$ trong **vocabulary** có xuất hiện trong nội dung tin nhắn không? Điều này được quyết định bởi phân phối $$\text{Bernoulli}(\phi_{1\vert Y})$$
> - Chữ thứ $$2$$ trong **vocabulary** có xuất hiện trong nội dung tin nhắn không? Điều này được quyết định bởi phân phối $$\text{Bernoulli}(\phi_{2\vert Y})$$
> - ...
> - Chữ thứ $$d$$ trong **vocabulary** có xuất hiện trong nội dung tin nhắn không? Điều này được quyết định bởi phân phối $$\text{Bernoulli}(\phi_{d\vert Y})$$

# Xây dựng Naive Bayes classifier (NB)

Ở phần này, ta sẽ tìm công thức của các tham số $$ \phi, \phi_{j\vert Y=1}, \phi_{j\vert Y=0}$$ bằng phương pháp ước lượng hợp lí cực đại.

## Xây dựng hàm Log-likelihood

Trước nhất, ta sẽ xây dựng hàm Log-likelihood của tập dữ liệu:


$$
\begin{equation}
\begin{split}
&\quad \ell(\phi, \phi_{j|Y=1}, \phi_{j|Y=1}) \\
&= \sum_{i=1}^n \log p(x^{(i)}, y^{(i)}) \\
&= \sum_{i=1}^n \log \left[ p(y^{(i)}) p(x^{(i)} | y^{(i)})\right] \\
&= \sum_{i=1}^n \log p(y^{(i)}) + \sum_{i=1}^n \log p(x^{(i)}|y^{(i)}) \\
&= \sum_{i=1}^n \log \left[ \phi^{y^{(i)}} (1-\phi)^{1- y^{(i)}}\right] + \sum_{i=1}^n \log \left[ \prod_{k=1}^d \phi_{k|Y=y^{(i)}}^{x_k^{(i)}} (1 - \phi_{k|Y=y^{(i)}})^{1-x_k^{(i)}} \right] \\
&= \sum_{i=1}^n \left[ y^{(i)} \log\phi + (1-y^{(i)}) \log(1-\phi)\right] + \sum_{i=1}^n \sum_{k=1}^d \left[ x_k^{(i)} \log \phi_{k|Y=y^{(i)}} + (1-x_k^{(i)}) \log(1-\phi_{k|Y=y^{(i)}})\right]
\end{split}
\end{equation}
$$


Ta sẽ tìm các tham số bằng lấy giải đạo hàm riêng bằng không. Việc làm này tương đối dễ vì các biến riêng biệt với nhau.

## Công thức của $$\phi$$

Ta có:


$$
\begin{aligned}
\frac{\partial \ell}{\partial \textcolor{yellow}{\phi}} &= 0\\
\frac{\partial}{\partial \textcolor{yellow}{\phi}} \sum_{i=1}^n \left[ y^{(i)} \log \textcolor{yellow}{\phi} + (1-y^{(i)}) \log(1-\textcolor{yellow}{\phi})\right] &= 0 \\
\sum_{i=1}^n \left[ \frac{y^{(i)}}{\textcolor{yellow}{\phi}}  - \frac{1- y^{(i)}}{1-\textcolor{yellow}{\phi}} \right] &= 0 \\
\sum_{i=1}^n \left[y^{(i)}(1- \textcolor{yellow}{\phi}) - (1-y^{(i)}) \textcolor{yellow}{\phi} \right] &= 0 \\
\sum_{i=1}^n \left[y^{(i)} - \textcolor{yellow}{\phi} \right] &= 0 \\
\textcolor{yellow}{\phi} &= \frac{\sum_{i=1}^n y^{(i)}}{n} \\
\textcolor{yellow}{\phi}&= \frac{\sum_{i=1}^n \mathbb{1}\{y^{(i)}=1\}}{n}
\end{aligned}
$$


## Công thức của $$\phi_{j\vert Y=1}$$

Ta có:


$$
\begin{aligned}
\frac{\partial \ell}{\partial \textcolor{yellow}{\phi_{j \vert Y=1}}} &= 0 \\
\frac{\partial}{\partial \textcolor{yellow}{\phi_{j \vert Y=1}}} \sum_{i=1}^n \sum_{k=1}^d \left[ x_k^{(i)} \log \phi_{k \vert Y=y^{(i)}} + (1-x_k^{(i)}) \log(1-\phi_{k \vert Y=y^{(i)}})\right] &= 0 \\
\sum_{i=1}^n \left\{ \mathbb{1} \{y^{(i)} = 1\} \left[\frac{x_{\textcolor{yellow}{j}}^{(i)}}{\textcolor{yellow}{\phi_{j \vert Y=1}}}- \frac{1-x_{\textcolor{yellow}{j}}^{(i)}}{1-\textcolor{yellow}{\phi_{j \vert Y=1}}} \right] \right\} &= 0 \\
\sum_{i=1}^n \left\{\mathbb{1}\{y^{(i)} = 1 \} \left[x_{\textcolor{yellow}{j}}^{(i)} (1- \textcolor{yellow}{\phi_{j \vert Y=1}} )- \textcolor{yellow}{\phi_{j \vert Y=1}}(1-x_{\textcolor{yellow}{j}}^{(i)}) \right] \right\} &= 0 \\
\sum_{i=1}^n \left\{ \mathbb{1} \{y^{(i)} = 1\} (x_{\textcolor{yellow}{j}}^{(i)} - \textcolor{yellow}{\phi_{j \vert Y=1}}) \right\} &= 0 \\
\textcolor{yellow}{\phi_{j \vert Y=1}} &= \frac{\sum_{i=1}^n \left[ \mathbb{1} \{y^{(i)} = 1\}  \; x_{\textcolor{yellow}{j}}^{(i)}\right] }{\sum_{i=1}^n \mathbb{1} \{y^{(i)} = 1\}}  \\
\textcolor{yellow}{\phi_{j \vert Y=1}} &= \frac{\sum_{i=1}^n \mathbb{1} \{y^{(i)} = 1 \wedge x_{\textcolor{yellow}{j}}^{(i)} = 1\}}{\sum_{i=1}^n \mathbb{1} \{y^{(i)} = 1\}}
\end{aligned}
$$


## Công thức của $$\phi_{j \vert  Y= 0}$$

Tương tự như phía trên, ta sẽ tìm được:


$$
\begin{aligned}
\textcolor{yellow}{\phi_{j \vert Y = 0}} = \frac{\sum_{i=1}^n \mathbb{1} \{y^{(i)} = 0 \wedge x_{\textcolor{yellow}{j}}^{(i)} = 1\}}{\sum_{i=1}^n \mathbb{1} \{y^{(i)} = 0\}}
\end{aligned}
$$


## Laplace smoothing

Các công thức của $$\phi, \phi_{j \vert Y = 0}, \phi_{j \vert Y=1}$$ xây dựng phía trên đã tương đối tốt nhưng ta có thể thực hiện một vài thay đổi nhỏ để cải thiện thuật toán tốt hơn.

Để hiểu rõ hơn về thay đổi này, ta sẽ xét bài toán thống kê sau. 

> Ta thực hiện tung một đồng xu đồng chất. Khi này, sự xuất hiện của mặt sấp và ngửa sẽ theo phân phối $$\text{Bernoulli}(p = 0.5)$$. Dựa vào thông tin các mặt có được sau mỗi lần tung, ta muốn ước lượng tham số $$p$$.

Giả sử, ta tung đồng xu $$4$$ lần, nhưng vì khá may mắn nên ta nhận được $$4$$ mặt sấp và $$0$$ mặt ngửa.

Từ đó, ta kết luận xác suất ra mặt sấp (tham số $$p$$) là: $$\frac{4}{4} = 1$$ 

Đây là một kết luận không sai tại thời điểm nói. Nhưng vì số lần tung đồng xu quá ít nên kết luận trên phần nào quá "lạc quan".

Để khắc phục vấn đề này, ta sẽ sử dụng kỹ thuật **Laplace smoothing**. Trước khi tung đồng xu, ta giả sử mỗi mặt của đồng xu đã xuất hiện $$1$$ lần. 

Sau đó, khi quan sát thấy sau $$4$$ lần tung ta thu được $$4$$ mặt sấp thì ta có thể kết luận xác suất ra mặt sấp (tham số $$p$$) là: $$\frac{5}{6} = 0.834$$. 

Kết luận này, phần nào hợp lí hơn kết luận xác suất ra mặt sấp là $$1$$. Do vậy, kỹ thuật **Laplace smoothing** thường được sử dụng để giảm sự quá "lạc quan" thấy ở trường hợp trước.

>  Nói cách khác, kỹ thuật này được sử dụng để giảm *phương sai* của kết quả ước lượng khi *số lượng mẫu* quá ít.

> Tóm lại, kĩ thuật **Laplace smoothing** là việc ta giả sử *mọi* kết quả khả thi của dữ liệu đã xuất hiện $$n$$ lần. (thường là $$1$$ lần)

## Dự đoán

Sau khi đã có các tham số $$\phi, \phi_{j \vert Y = 0}, \phi_{j \vert Y=1}$$, ta có thể đưa ra dự đoán bằng cách tính xác suất hậu nghiệm và chọn một ngưỡng để gắn nhãn cho dữ liệu.

Ta tính xác suất hậu nghiệm bằng định lí Bayes:


$$
\begin{equation}
\begin{split}
p(y=1 \vert x) &= \frac{p(x \vert y = 1) p(y=1)}{p(x)} \\
&= \frac{\left( \prod_{j=1}^d p(x_j \vert y = 1)\right) p(y=1)}{\left( \prod_{j=1}^d p(x_j \vert y = 1)\right) p(y=1) +\left( \prod_{j=1}^d p(x_j \vert y = 0)\right) p(y=0)}
\end{split}
\end{equation}
$$


Thông thường ta sẽ lựa ngưỡng cao hơn $$0.5$$ như $$0.6$$, $$0.7$$,... vì trong quá trình phân loại tin nhắn ta không muốn những tin nhắn quan trọng bị cho vào mục spam. Nói cách khác, ta mong muốn số lượng **False Negative** cao hơn **False Positive**.

Từ đây, ta có thể thấy được tại sao thực hiện kỹ thuật **Laplace smoothing** là cần thiết khi dự đoán dữ liệu mới.

Giả sử, trong khi dự đoán, ta gặp phải những chữ mới chưa từng xuất hiện trong quá trình *training* thì khi đó:


$$
\begin{aligned}
p(x_j \vert y=0) &= 0 \\
p(x_j \vert y = 1) &= 0
\end{aligned}
$$


Dẫn đến


$$
p(y=1 \vert x) = \frac{0}{0+0} = \frac{0}{0}
$$


Để tránh những phiền phức này, việc thực hiện kỹ thuật **Laplace smoothing** là cần thiết.

## Tiểu kết

Ta có thể trình bày Navie Bayes classifier có sử dụng **Laplace smoothing** một cách hoàn thiện như sau:

>**Input**: Tập dữ liệu $$ \{ (x^{(1)}, y^{(1)}), (x^{(2)}, y^{(2)}),..., (x^{(n)}, y^{(n)}) \}$$ với $$ x^{(i)} \in \{0,1\}^d, \; y^{(i)} \in \{0,1\}$$. 
>
>Tính các tham số:
>
>
>$$
>\begin{aligned}
>\phi&= \frac{1+\sum_{i=1}^n \mathbb{1}\{y^{(i)}=1\}}{2+n} \\
>\phi_{j \vert Y=1} &= \frac{1+\sum_{i=1}^n \mathbb{1} \{y^{(i)} = 1 \wedge x_j^{(i)} = 1\}}{2+\sum_{i=1}^n \mathbb{1} \{y^{(i)} = 1\}} \\
>\phi_{j \vert Y=0} &= \frac{1+\sum_{i=1}^n \mathbb{1} \{y^{(i)} = 0 \wedge x_j^{(i)} = 1\}}{2+\sum_{i=1}^n \mathbb{1} \{y^{(i)} = 0\}}
>\end{aligned}
>$$
>
>
>**Output**: Các tham số $$\phi, \phi_{j \vert Y=0}, \phi_{j \vert Y=1}$$
>
>**Dự đoán**: Tính xác suất hậu nghiệm $$p(y=1 \vert x)$$ và chọn một ngưỡng để gắn nhẵn cho dữ liệu mới.

# Triển khai Naive Bayes classifier trong python

Trong phần này, ta sẽ xây dựng Naive Bayes classifier (**NB**) trong python từ những công thức có được phía trên. Trong quá trình, ta sẽ phải xây dựng thêm hàm để biến đổi từ dữ liệu text sang vector. Cuối cùng, ta thêm vào tính năng tìm những từ thường được sử dụng trong tin nhắn spam.

## Tập dữ liệu

Ở đây ta sử dụng tập dữ liệu *SMS spam dataset* được phát triển bởi Tiago A. Almedia và Jose Marıa Gomez Hidalgo [^1]. Để quá trình đánh giá mô hình chính xác. Ta cần chia tập dữ liệu trên thành 3 phần: train, validation, test.

| ![image-center](../../assets/post_img/nbc/h1.png) |
| :-----------------------------------------------: |
|   *Hình 1: Tập dữ liệu được chia thành 3 phần*    |

Ngoài ra, trong tập dữ liệu các tin nhắn được gắn nhẵn như sau:

- Tin nhắn spam: **spam**
- Tin nhắn không spam: **ham**

|  ![image-center](../../assets/post_img/nbc/h2.png)  |
| :-------------------------------------------------: |
| *Hình 2: Một vài tin nhắn trong tập dữ liệu train.* |

## Thư viện cần thiết

```python
import numpy as np
import pandas as pd
import collections
```

## Hàm cần thiết

```python
def read_data(path):
    '''
    Input: 
        path: str
              data path
    Output:
        X: list, shape=(n,)
           list of messages
        Y: ndarray, shape=(n,)
           labels
    '''
    data = pd.read_csv(path, sep='\t', header=None)
    
    X = data[1].str.lower().tolist()
    Y = (data[0] == 'spam').to_numpy() * 1
    
    return X, Y
```

Hàm *read_data(path)* giúp ta đọc dữ liệu và chuyển các nhãn của dữ liệu từ **ham** và **spam** về **0** và **1**.

Ngoài ra, ta sẽ đưa mọi chữ về lowercase.

## Kiến trúc của class

- Tên class: NB.
- Attributes:
  - phi
  - phiy1
  - phiy0
  - vocabulary
- Methods:
  - \_\_init\_\_()
  - make_vocabulary(X)
  - to_vector(X, vocabulary)
  - fit(X, Y)
  - predict(X, threshold)
  - evaluate(Y_predict, Y_true)
  - top_spam_words(k)

## Method \_\_init\_\_()

```python
 def __init__(self):
        '''
        Setting up parameters
        '''
        self.phi = None
        self.phiy1 = None
        self.phiy0 = None
        self.vocabulary = None
```

Thiết lập những Attributes.

## Method make_vocabulary(X)

```python
def make_vocabulary(self, X):
        '''
        Input: 
            X: list, shape(n,)
               list of messages
        Output: 
            vocabulary: dictionary, shape(d,2)
                        vocabulary of data
        '''
        n = len(X_train)
        dict_pos = 0
        vocabulary_word_count = {}
        vocabulary = {}

        for message in X:
            message_word_count = collections.Counter(message.split(' '))
            for word in message_word_count:
                if word in vocabulary_word_count:
                    vocabulary_word_count[word] += 1
                else:
                    vocabulary_word_count[word] = 1

        for word in vocabulary_word_count:
            if vocabulary_word_count[word] >= 5:
                vocabulary[word] = dict_pos
                dict_pos += 1
    
        return dict(sorted(vocabulary.items(), key = lambda x: x[1]))
```

Xây dựng **vocabulary** cho tập dữ liệu.

Lưu ý, ở đây ta không lấy **mọi** chữ như đã đề cập ở phần trước mà lấy những chữ xuất hiện ít nhất trong 5 tin nhắn (nếu chữ xuất hiện nhiền lần trong 1 tin nhắn thì chỉ tính 1 lần). Việc làm này sẽ giúp ta bỏ qua những chữ hiếm gặp, từ đó **vocabulary** sẽ nhỏ hơn.

## Method to_vector(X, vocabulary)

```python
def to_vector(self, X, vocabulary):
        '''
        Input: 
            X: list, shape(n,)
               list of messages
            vocabulary: dictionary, shape(d,2)
                        vocabulary of data
        Output:
            X_transformed: ndarray, shape(n,d)
                           messages represented in vector form
        '''
        n = len(X)
        d = len(vocabulary)
        X_transformed = np.zeros((n,d))

        for i, message in enumerate(X):
            word_dict = collections.Counter(message.split(' '))
            for word in word_dict:
                if word in vocabulary:
                    X_transformed[i, vocabulary[word]] = 1

        return X_transformed
```

Chuyển tin nhắn về vector $$x \in \{0, 1\}^d$$. Cụ thể hơn, ở đây ta chuyển list tin nhắn về một ma trận $$X \in \{0, 1\}^{d \times d}$$.

## Method fit(X, Y)

```
def fit(self, X, Y):
        '''
        Input: 
            X: list, shape(n,)
               list of messages
            Y: ndarray, shape(n,)
               labels
        Output: self
        '''
        
        vocabulary = self.make_vocabulary(X)
        X_transformed = self.to_vector(X, vocabulary)
        
        n, d = X_transformed.shape
        
        spam_count = np.sum(Y)
        
        self.phi = spam_count / n
        self.phiy1 = (1 + np.sum(X_transformed[Y == 1], axis=0)) / (2 + spam_count)
        self.phiy0 = (1 + np.sum(X_transformed[Y == 0], axis=0)) / (2 + n - spam_count)
        self.vocabulary = vocabulary
        
        return self
```

Tính các tham số $$ \phi, \phi_{j \vert Y=1}, \phi_{j \vert Y=0}$$ theo các công thức đã thiết lập và cập nhật **vocabulary** cho class.

## Method predict(X, threshold)

```python
def predict(self, X, threshold):
        '''
        Input:
            X: list, shape(n,)
               list of messages
            threshold: float, 0 <= threshold <= 1
        Output:
            Y: ndarray, shape(n,)
               prediction, p(y=1|x)
        '''
        if type(X) != type([]):
            X_transformed = to_vector([X], self.vocabulary)
        else:
            X_transformed = to_vector(X, self.vocabulary)
            
        n, d = X_transformed.shape
        
    
        p_x_given_y1_matrix = np.outer(np.ones(n), nb.phiy1)
        p_x_given_y0_matrix = np.outer(np.ones(n), nb.phiy0)
        
        p_x_given_y1_matrix[X_transformed == 0] = (1 - np.outer(np.ones(n), nb.phiy1))[X_transformed == 0]
        p_x_given_y0_matrix[X_transformed == 0] = (1 - np.outer(np.ones(n), nb.phiy0))[X_transformed == 0]
        
        p_x_and_y1 = np.sum(np.log(p_x_given_y1_matrix), axis=1) + np.log(nb.phi)
        p_x_and_y0 = np.sum(np.log(p_x_given_y0_matrix), axis=1) + np.log(1 - nb.phi)
        
        px = np.exp(p_x_and_y1) + np.exp(p_x_and_y0)
        
        Y = np.exp(p_x_and_y1) / px
    
        spam_index = np.array(Y > threshold)
        Y[spam_index] = 1
        Y[~spam_index] = 0
        
        return Y
```

Tính xác suất hậu nghiệm $$p(y=1 \vert x)$$ và sử dụng *threshold* để đưa ra dự đoán.

> **Lưu ý**
>
> Vì quá trình tính xác suất hậu nghiệm ta sẽ nhân rất nhiều số nhỏ hơn $$1$$ lại với nhau nên để hạn chế tình trạng *underflow* làm mất thông tin, ta sử dụng hàm $$\log$$ để cộng các xác suất lại sau đó dùng hàm $$\text{exp}$$ để trả dữ liệu về ban đầu.
>
> Ngoài ra, để giảm thời gian tính toán, ta sử dụng rất nhiều ma trận của `Numpy` để thực hiện tính toán.

## Method evaluate(Y_predict, Y_true)

```python
def evaluate(self, Y_predict, Y_true):
        '''
        Input: 
            Y_predict: ndarray, shape(n,)
            Y_true: ndarray, shape(n,)
        Output:
            score: float, 0 <= score <= 1
        '''
        return np.sum(Y_predict == Y_true) / len(Y_true)
```

Evaluate mô hình bằng cách so sánh nhãn dự đoán và nhãn thật của dữ liệu.

## Method top_spam_words(k)

```python
 def top_spam_words(self, k):
        '''
        Input:
            k: int, 0 <= k
        Output:
            Top k spam words: list
        '''
        arg_spam_word_sorted = np.argsort(np.log(nb.phiy1/nb.phiy0))[::-1]
        inv_vocabulary = {value: key for key, value in nb.vocabulary.items()}
        
        return [inv_vocabulary[arg_spam_word_sorted[i]] for i in range(k)]
```

Để tìm những chữ thường xuất hiện trong tin nhắn spam, ta không thể chỉ so sánh các chứ với nhau dựa vào $$\phi_{j \vert Y=1} = p(x_j \vert y=1)$$ được. Vì có một vài từ hay xuất hiện nhiều trong ngôn ngữ cho dù có phải spam hay không, ví dụ: "i", "you", "what",...

> Do vậy, một chữ được xem là xuất hiện nhiều trong tin nhắn spam là khi:
>
> - $$\phi_{j \vert Y=1} = p(x_j \vert y=1)$$ **lớn**.
>
> - $$\phi_{j \vert Y=0} = p(x_j \vert y=0)$$ **nhỏ**.
>
> Vậy ta sẽ dùng tỉ số sau để đánh giá một chữ
>
>
> $$
> \begin{equation}
> \log \frac{p(x_j \vert y = 1)}{p(x_j \vert y = 0)} = \log \frac{\phi_{j \vert Y = 1}}{ \phi_{j \vert Y = 0}}
> \end{equation}
> $$
>
>
> Ở đây ta sử dụng hàm $$\log$$ để tránh tình trạng *underflow*

## Kết quả

Thêm vào các tập dữ liệu

```python
X_train, Y_train = read_data('spam_train.tsv')
X_val, Y_val = read_data('spam_val.tsv')
X_test, Y_test = read_data('spam_test.tsv')
```

Khởi tạo một instance của **NB** và fit dữ liệu

```python
nb = NB().fit(X_train, Y_train)
```

Đánh giá độ chính xác của mô hình trên tập *validation*

```python
prediction = nb.predict(X_val, .6)
score = nb.evaluate(prediction, Y_val)
print(score)
```

```python
0.9820466786355476
```

Sau khi chỉnh sửa và hài lòng với mô hình, ta chạy lần cuối trên tập *test*

```python
prediction = nb.predict(X_test, .6)
score = nb.evaluate(prediction, Y_test)
print(score)
```

```python
0.982078853046595
```

Ta có thể in ra những chữ hay xuất hiện trong tin nhắn spam

```python
nb.top_spam_words(10)
```

```python
['claim',
 'won',
 'prize',
 'urgent!',
 'awarded',
 'tone',
 '£1000',
 'guaranteed',
 '4*',
 '150ppm']
```

## Nhận xét

> Hai methods: *make_vocabulary(X)* và *to_vector(X, vocabulary)* có thể xem như hai private methods, được thiết kế để phục vụ những chức năng trong class chứ không được gọi trực tiếp từ người dùng.
>
> Việc *validation score* và *test score* đều khá cao chưa hẳn là một việc tốt. Vì ở tập *train* có khoảng 4500 samples, tập *valid* và tập *test* mỗi tập có khoảng 600 samples. Kích thước của tập dữ liệu vẫn còn khá nhỏ để đánh giá được *generalization error*.

## Code link

https://github.com/iamnmt/blog-code/tree/main/nbc

# Kết luận

Naive Bayes classifier là một mô hình dùng để phân loại tin nhắn khá cũ. Ngày nay, việc phân loại tin nhắn spam thường được sử dùng bằng những thuật toán phức tạp hơn.

Ngoài ra, ta có thể phát triển cho thuật toán gắn nhẵn nhiều hơn hai loại dữ liệu. Việc xây dựng công thức cũng sẽ tương tự trong trường hợp có hai nhãn.

Biết viết này, được viết dựa trên kiến thức khóa học CS229 của Stanford [^2] [^3]

# Thuật ngữ đã sử dụng

| Tiếng Việt        | Tiếng Anh                           | Tiếng Việt               | Tiếng Anh                   |
| ----------------- | ----------------------------------- | ------------------------ | --------------------------- |
| Biến ngẫu nhiên   | Random variable                     | Nhãn/gắn nhãn            | Label                       |
| Điểm dữ liệu      | Data point                          | Nhị phân                 | Binary                      |
| Độc lập thống kê  | Independent identically distributed | Phân phối                | Distribution                |
| Dự đoán           | Predict                             | Tập dữ liệu              | Dataset                     |
| Giả thiết         | Assumption                          | Thuộc tính               | Feature                     |
| Hàm khối xác suất | Probability mass function           | Ước lượng hợp lí cực đại | Maximum likelihood estimate |
| Học có giám sát   | Supervised learning                 | Vector ngẫu nhiên        | Random vector               |
| Mẫu               | Sample                              | Xác suất hậu nghiệm      | Posterior probability       |
| Ngưỡng            | Threshold                           |                          |                             |


[^1]: http://www.dt.fee.unicamp.br/~tiago/smsspamcollection
[^2]: Stanford. CS229. Summer 2019. Lecture 7 - GDA, Naive Bayes & Laplace Smoothing.
[^3]: Stanford. CS229. Summer 2019. Problem set 2

