---
layout: post
title:  "htop: Đọc hiểu hiệu suất Server"
date:   2024-06-26 20:50:00 +0700
categories: jekyll update
---
- htop là một tiện ích quản lý tiến trình dựa trên dòng lệnh, cung cấp một cái nhìn trực quan về tình trạng hiệu suất của server

- Trong bài viết này, mình sẽ chia sẻ cách đọc và hiểu thông tin từ htop, cũng như cách tùy chỉnh nó để phản ánh cho nhu cầu cụ thể của bạn

## Đọc hiểu thông tin từ htop

![image-01](/assets/images/2024-06-26-read-and-understand-htop/image-01.png)

- Hình trên là giao diện mặc định của htop

- Layout hiển thị gồm ba thành phần chính

#### Top left

Từ trên xuống
- CPU
  - 1 và 2 là hai thanh tiến trình của CPU
  - Vì sao CPU lại có hai thanh tiến trình? vì server được cấu hình hai lõi CPU, nếu server của bạn có bốn lõi thì chỗ này có thể sẽ được hiển thị bốn thanh tiến trình
  - Để xem thông tin chi tiết về CPU trên ubuntu server, bạn có thể chạy lệnh sau: **lscpu**
  - CPU Bar có thể giúp bạn nhanh chóng đánh giá tình trạng hiệu suất của server. Nếu bạn thấy thanh CPU màu đỏ, có thể là dấu hiệu server đang tải công việc nặng
- Mem
  - Memory (RAM)
  - Các màu sắc thường thể hiện mức độ sử dụng bộ nhớ
    - Màu xanh lá cây: mức độ sử dụng bộ nhớ thấp hoặc không sử dụng
    - Màu vàng: mức độ sử dụng bộ nhớ trung bình
    - Màu đỏ: mức độ sử dụng bộ nhớ cao
  - Memory Bar giúp bạn đánh giá tình trạng sử dụng bộ nhớ của server và xác định xem liệu có cần thực hiện các biện pháp như giải phóng bộ nhớ, tối ưu hóa các tiến trình hoặc nâng cấp bộ nhớ hay không
- Swp
  - Swap Usage
  - Là một phần không gian của ổ cứng được OS sử dụng như bộ nhớ ảo
  - Khi đầy RAM thì OS sẽ tự động chuyển một số dữ liệu không sử dụng sang vùng Swap để tạo không gian cho các tiến trình khác
  - Khi bạn quan sát thấy thanh Swp này thường xuyên đầy đặn là dấu hiệu server đang sử dụng nhiều swap, có thể ảnh hưởng đến performance vì truy cập dữ liệu từ ổ cứng thường sẽ chậm hơn RAM

#### Top right

Từ trên xuống
- Tasks
  - Số liệu về tổng số tiến trình đang chạy trên server
  - Tasks: 39, 51 thr; 1 running có ý nghĩa
    - Tổng số tiến trình là 39
    - Tổng số luồng (bao gồm cả đang chạy và không chạy) là 51
    - Có 1 tiến trình đang chạy
- Load average
  - Chỉ số này thể hiện tải trung bình của server trong một khoảng thời gian nhất định, lần lượt theo thứ tự 1 -> 5 -> 15 phút
  - Load average: 0.5 0.6 0.11 có ý nghĩa
    - Load average trong 1 phút gần nhất là 0.5
    - Load average trong 5 phút gần nhất là 0.6
    - Load average trong 15 phút gần nhất là 0.11
  - Các con số này đại diện cho tỉ lệ của công việc đang chờ (đang chạy hoặc đợi) so với tổng số lõi CPU
  - Nếu số này thấp (gần 0) nghĩa là server đang chạy nhẹ và có nhiều tài nguyên còn trống
  - Nếu số này cao (lớn hơn số lõi CPU) thì đó có thể là dấu hiệu của tải nặng và server phải xử lý nhiều công việc
- Uptime
  - Là khoảng thời gian hoạt động liên tục kể từ thời điểm gần nhất mà server được start/restart
  - uptime = DateTime.now - start/restart time

#### Bảng danh sách các tiến trình

- PID (Process ID) là một số nguyên duy nhất đại diện cho ID của tiến trình
- USER là tên người dùng chạy tiến trình. Cột này hiển thị thông tin về người dùng sở hữu tiến trình
- PRI (Priority) là độ ưu tiên của tiến trình. Số càng lớn thì độ ưu tiên càng thấp
- NI (Nice value) cũng là một số chỉ đánh giá sự ưu tiên của tiến trình. Nice value âm thường có độ ưu tiên cao hơn
- VIRT (Virtual memory) là lượng bộ nhớ ảo mà tiến trình đang sử dụng
- RES (Resident memory) là lượng bộ nhớ thực tế đang được sử dụng bởi tiến trình
- SHR (Shared memory) là lượng bộ nhớ được sử dụng chung giữa các tiến trình
- S (Status) là trạng thái của tiến trình
  - R (Running) là tiến trình đang thực thi và đang chạy hoặc sẵn sàng chạy
  - S (Sleeping) là tiến trình đang ở trạng thái chờ đợi mà không chiếm tài nguyên CPU. Có thể là đang chờ sự kiện nào đó để xảy ra
  - D (Disk sleep) là tiến trình đang chờ đợi dữ liệu từ ổ đĩa hoặc thiết bị lưu trữ khác
  - Thông thường, trạng thái “D” không phải là điều tốt đẹp vì nó có thể làm tăng tải cho server nếu có quá nhiều tiến trình chờ đợi I/O
  - Nếu bạn gặp phải trạng thái “D” liên tục cho một tiến trình cụ thể thì bạn nên kiểm tra và giải quyết vấn đề liên quan đến I/O như sự cố với ổ đĩa hoặc dữ liệu lưu trữ
- CPU% là phần trăm của CPU mà tiến trình đang sử dụng
- MEM% là phần trăm của bộ nhớ RAM mà tiến trình đang sử dụng
- TIME+ là thời gian tiêu thụ CPU của bởi tiến trình kể từ khi nó được bắt đầu
- Command là dòng lệnh hoặc tên của tiến trình
- Các tiến trình được sắp xếp mặc định theo CPU% giảm dần

## Tuỳ chỉnh hiển thị

- Bạn chỉ cần bấm F2 và tuỳ chỉnh những gì mình mong muốn

![image-02](/assets/images/2024-06-26-read-and-understand-htop/image-02.png)

- Phần Setup này mô tả rất chi tiết về công dụng của mỗi thuộc tính. Do đó, để điều chỉnh giao diện của htop phản ánh đúng nhu cầu thì bạn nên đọc kỹ hướng dẫn sử dụng trước khi dùng
- Tuỳ chỉnh sẽ được hiển thị realtime ngay khi bạn lưu lại
- Hình dưới là một ví dụ tuỳ chỉnh htop mà mình tự chế cho dễ nhìn hơn

![image-03](/assets/images/2024-06-26-read-and-understand-htop/image-03.png)

## Lời kết

- Hi vọng bài viết của mình sẽ giúp bạn thoải mái sử dụng htop để theo dõi hiệu suất server và giải quyết các vấn đề liên quan, ví dụ như các câu hỏi của sếp: mày có hiểu cái này là gì không? còn cái này và cái này 🤣
- Chúc bạn có những trải nghiệm tốt với htop
