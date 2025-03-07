---
author_profile: true
toc: true
toc_label: "Mục lục"
toc_icon: "bars"
title: "PiMA 2021: Bài làm ứng tuyển BTC"
---

# Cách trình bày trên Facebook

Phần này sẽ không có trong bài viết chính thức trên Facebook. Ở phần này, em sẽ nói rõ hơn cách bài post này được trình bày thế nào khi đưa lên trang fanpage Facebook.

Những Heading ở đây bỏ phần dấu "()" là phần được viết trong "[]" trên bài viết Facebook.

Trang bìa là trang đầu tiên của bài post trên fanpage.

![image-center](../assets/post_img/pima_submission/h01.png)

Trang có số là những trang sau đó.

![image-center](../assets/post_img/pima_submission/h02.png)


# Tóm tắt (trang bìa)

Trong bài viết hôm nay chúng ta sẽ tìm hiểu một cách trực quan về phân phối thường xuất hiện rất nhiều trong khoa học cũng như đời sống hằng ngày, đó là phân phối chuẩn/phân phối Gaussian.

Mở đầu bài viết, ta tìm hiểu về bài khảo sát chiều cao các học sinh và nhận xét rút ra được từ phân phối đó.

Tiếp đến, ta sẽ tìm hiểu về hàm mật độ xác suất của phân phối chuẩn và đồ thị của hàm số thay đổi như thế nào khi ta thay đổi các tham số.

Cuối cùng, ta sẽ mở rộng phân phối chuẩn trong không gian 2-chiều và nhiều chiều hơn.

# Một số khái niệm thống kê cơ bản (trang 1)

Trước tiên, ta sẽ tóm tắt qua những kiến thức xác suất thống kê được sử dụng trong bài viết:

- Biến ngẫu nhiên là một hàm số đi từ không gian sự kiện đến tập hợp số thực. Hàm số này sẽ gán giá trị cho kết quả của một phép thử ngẫu nhiên.
- Kỳ vọng của một biến ngẫu nhiên là trung bình có trọng số của tất cả các giá trị có thể nhận được của biến ngẫu nhiên đó.
- Phương sai của một biến ngẫu nhiên thể hiện mức độ phân tán của biến ngẫu nhiên đó. Ngoài ra, phương sai còn thể hiện các giá trị cụ thể của biến ngẫu nhiên cách kỳ vọng bao xa.
- Hàm mật độ xác suất thể hiện mức độ tập trung xác suất xung quanh một giá trị cụ thể của biến ngẫu nhiên liên tục.



![image-center](../assets/post_img/pima_submission/h1.png)

# Khảo sát chiều cao (trang 2)

Phân phối chuẩn xuất hiện trong những dữ liệu có số lượng tập trung nhiều ở giá trị trung bình và giảm dần khi ra xa. Một trong những đặc điểm của con người thường tuân theo phân phối chuẩn là chiều cao. Để hiểu rõ hơn, ta thực hiện khảo sát chiều cao của 200 bạn học sinh.

<iframe width="960" height="540" src="../assets/post_video/pima_submission/v1.mp4" frameborder="0" allowfullscreen></iframe>

# Nhận xét về phân phối của chiều cao (trang 3)

Qua khảo sát trên, ta có nhận xét:

- Phân phối của chiều cao các bạn có dạng hình cong, đường cong này thường được gọi là đường cong chuông.
- Chiều cao trung bình của các bạn khoảng: 171cm.
- Độ lệch chuẩn của chiều cao khoảng: 4cm.
- Nhiều bạn học sinh có chiều cao trong khoảng từ 166cm đến 174cm (Hình chữ nhật màu xanh).
- Ít bạn học sinh có chiều cao ngoài khoảng từ 166cm đến 174cm (Hình chữ nhật màu đỏ).


<iframe width="960" height="540" src="../assets/post_video/pima_submission/v2.mp4" frameborder="0" allowfullscreen></iframe>

# Hàm mật độ xác suất của biến ngẫu nhiên (trang 4)

Ta có thể phát biểu một cách toán học như sau, gọi chiều cao của mỗi bạn trong khảo sát trên là một giá trị xác định của biến ngẫu nhiên $$X$$. Nếu biến ngẫu nhiên $$X$$ được lấy mẫu từ phân phối chuẩn thì ta sẽ có được hàm mật độ xác suất của $$X$$ như sau:

<iframe width="960" height="540" src="../assets/post_video/pima_submission/v3.mp4" frameborder="0" allowfullscreen></iframe>

# Thay đổi giá trị kỳ vọng (trang 5)

Để hiểu rõ hơn về hàm mật độ xác suất trên, ta sẽ xem đồ thị hàm số sẽ thay đổi như thế nào khi $$\mu$$ thay đổi.

Ta thấy được đồ thị hàm số sẽ bị dịch sang trái hoặc dịch sang phải khi $$\mu$$ thay đổi.

<iframe width="960" height="540" src="../assets/post_video/pima_submission/v4.mp4" frameborder="0" allowfullscreen></iframe>

# Thay đổi giá trị phương sai (trang 6)

Để hiểu rõ hơn về hàm mật độ xác suất trên, ta sẽ xem đồ thị hàm số sẽ thay đổi như thế nào khi $$\sigma^2$$ thay đổi.

Ta thấy được đồ thị hàm số sẽ rộng ra hoặc hẹp lại khi $$\sigma^2$$ thay đổi.

<iframe width="960" height="540" src="../assets/post_video/pima_submission/v5.mp4" frameborder="0" allowfullscreen></iframe>

# Mở rộng trong không gian 2-chiều (trang 7)

Ngoài ra, hàm mật độ xác suất trên có thể mở rộng lên trong không gian 2-chiều. Đồ thị của hàm số có dạng như sau:

<iframe width="960" height="540" src="../assets/post_video/pima_submission/v6.mp4" frameborder="0" allowfullscreen></iframe>

# Mở rộng trong không gian nhiều chiều (trang 8)

Hàm mật độ xác suất trong không gian nhiều chiều có dạng tương tự trong không gian 1-chiều. Bên cạnh đó, ta sử dụng thêm những khái niệm trong đại số tuyến tính như: vector, ma trận; để biểu diễn hàm số.

<iframe width="960" height="540" src="../assets/post_video/pima_submission/v7.mp4" frameborder="0" allowfullscreen></iframe>

# Kết luận (trang 9)

Hy vọng qua bài viết này, các bạn có được cái nhìn trực quan về phân phối chuẩn.

Phân phối chuẩn thường có những tính chất toán học đẹp, do đó việc giả sử dữ liệu tuân theo phân phối chuẩn thường được thấy trong lĩnh vực như máy học và thống kê. Bằng cách này, ta có thể khai thác những tính chất toán học đó để việc tính toán trở nên dễ dàng hơn. 

Cảm ơn các bạn đã đọc bài viết.

<iframe width="960" height="540" src="../assets/post_video/pima_submission/v8.mp4" frameborder="0" allowfullscreen></iframe>

