---
layout: post
title:  "Các Bước Triển Khai Trang Web Tĩnh Bằng AWS S3"
date:   2023-11-28 15:00:00 +0700
categories: jekyll update
---
## Giới thiệu

- Triển khai trang web tĩnh ***(static web page)*** bằng Amazon Web Services S3 là một cách hiệu quả và tiết kiệm chi phí để đưa trang web lên internet

- Bài viết này sẽ hướng dẫn bạn từng bước để triển khai trang web tĩnh bằng AWS S3

## Bước 1: Chuẩn bị nội dung trang web

- Trước tiên, bạn cần chuẩn bị nội dung cho trang web tĩnh của mình. Đây có thể là các tệp HTML, CSS, JavaScript, hình ảnh hoặc bất kỳ tài liệu nào bạn muốn hiển thị trên trang web của mình

- Static website template source code: [https://github.com/thuc-pt/fe-zing-mp3](https://github.com/thuc-pt/fe-zing-mp3)

## Bước 2: Tạo một S3 bucket

- Đăng nhập vào AWS Management Console

- Chuyển đến dịch vụ **S3** bằng cách tìm kiếm trong thanh tìm kiếm hoặc nhấp vào nó trong danh sách các dịch vụ

![image-01](/assets/images/2023-11-28-deploy-static-page-with-s3/image-01.png)

- Click nút **Create bucket** để tạo mới một bucket

![image-02](/assets/images/2023-11-28-deploy-static-page-with-s3/image-02.png)

- AWS S3 quy định **bucket name** là duy nhất trên toàn cầu

- Nên đặt tên bucket dạng ***your-domain-name.com***

- Chọn khu vực AWS (Region) mà bạn muốn lưu trữ bucket

- Bỏ qua các cài đặt còn lại (hoặc tuỳ chỉnh theo nhu cầu của bạn) và nhấp vào nút **Create bucket** ở cuối trang để tạo bucket

![image-03](/assets/images/2023-11-28-deploy-static-page-with-s3/image-03.png)

## Bước 3: Upload nội dung trang web

- Chọn bucket bạn đã tạo trong danh sách S3 buckets

- Nhấp vào nút **Upload** và sau đó chọn files bạn muốn tải lên

![image-04](/assets/images/2023-11-28-deploy-static-page-with-s3/image-04.png)

- Sau khi bạn tải lên nội dung của trang web, hãy đảm bảo rằng tất cả các tệp và thư mục đều có quyền truy cập public để người khác có thể xem được trang web của bạn

- Có thể thực hiện theo các bước sau

![image-05](/assets/images/2023-11-28-deploy-static-page-with-s3/image-05.png)

![image-06](/assets/images/2023-11-28-deploy-static-page-with-s3/image-06.png)

![image-07](/assets/images/2023-11-28-deploy-static-page-with-s3/image-07.png)

![image-08](/assets/images/2023-11-28-deploy-static-page-with-s3/image-08.png)

![image-09](/assets/images/2023-11-28-deploy-static-page-with-s3/image-09.png)

- Sau khi **Make public** xong thì tất cả các files trong bucket đều có thể được truy cập từ internet

## Bước 4: Cấu hình trang web

- Chọn bucket của bạn và chọn **Properties**

![image-10](/assets/images/2023-11-28-deploy-static-page-with-s3/image-10.png)

![image-11](/assets/images/2023-11-28-deploy-static-page-with-s3/image-11.png)

- Sau khi click **Save changes** thì tại block ***Static website hosting*** sẽ hiển thị một URL. Đây là đường dẫn đến trang web của bạn

- Click vào URL để kiểm tra hoạt động

![image-12](/assets/images/2023-11-28-deploy-static-page-with-s3/image-12.png)

## Bước 5: Tùy chỉnh tên miền và SSL (optional)

- Nếu bạn muốn sử dụng tên miền tùy chỉnh và cài đặt SSL cho trang web của bạn, bạn có thể sử dụng dịch vụ như Amazon CloudFront hoặc AWS Amplify

- Bài viết này đã hướng dẫn cách triển khai một trang web tĩnh bằng AWS S3. Bằng cách làm theo các bước trên, bạn có thể đưa trang web của mình lên internet một cách dễ dàng và tiết kiệm chi phí

- Chúc các bạn thành công
