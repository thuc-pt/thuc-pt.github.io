---
layout: post
title:  "Proc và Lambda trong Ruby: Sự Khác Biệt Và Cách Sử Dụng"
date:   2023-12-18 20:30:00 +0700
categories: jekyll update
---
- Proc và Lambda trong ngôn ngữ lập trình Ruby đóng một vai trò quan trọng cho việc xử lý hàm và thực hiện các tác vụ khác nhau

- Trong bài viết này, mình sẽ giới thiệu sự khác biệt giữa Proc và Lambda, cách chúng được tích hợp làm cho mã nguồn của bạn trở nên mạnh mẽ, linh hoạt và dễ đọc hơn

- Cùng tìm hiểu Proc và Lambda để hiểu rõ hơn và nâng cao kĩ năng lập trình của bạn

## 1. Proc

#### 1.1 Khái niệm

- Proc là một class trong Ruby

- Đối tượng Proc là sự đóng gói của một khối mã, có thể được lưu trữ trong một biến, được truyền vào một method hoặc Proc khác và được gọi khi cần dùng

#### 1.2 Khai báo và sử dụng Proc

- Proc có 2 cách khai báo và nhiều cách gọi

```ruby
# Lambda-like syntax
sample_proc = Proc.new { |x| puts x }
# Or short syntax
sample_proc = proc { |x| puts x }

# Call proc (Output: 10)
sample_proc.call(10)
# Or
sample_proc[10]
# Or
sample_proc.10
# Or
sample_proc.yield(10)
```

## 2. Lambda

#### 2.1 Khái niệm

- Lambda là một đối tượng đặc biệt của Proc, mang lại cú pháp đơn giản hơn cho việc định nghĩa hàm

#### 2.2 Khai báo và sử dụng Lambda

- Lambda có 2 cách khai báo. Cách gọi Lambda cũng giống như gọi Proc

```ruby
sample_lambda = lambda { |x| puts x }
# Or
sample_lambda = ->(x) { puts x }
```

## 3. Sự khác biệt

| Ngoài syntax khai báo thì Proc và Lambda còn có những điểm khác biệt sau

#### 3.1 Parameters

![image-01](/assets/images/2023-12-18-proc-and-lambda-in-ruby/image-01.png)

- Proc không cần truyền đúng số lượng arguments vẫn hoạt động tốt

- Lambda bắt buộc truyền đúng số lượng arguments nếu không sẽ raise errors

![image-02](/assets/images/2023-12-18-proc-and-lambda-in-ruby/image-02.png)

- Proc có khả năng giải cấu trúc mảng để tương ứng với arguments

- Lambda thì không

#### 3.2 Return

![image-03](/assets/images/2023-12-18-proc-and-lambda-in-ruby/image-03.png)

- Khi gặp lệnh **return** trong method

  - Proc lập tức thoát hàm và không xử lý gì thêm

  - Lambda vẫn xử lý như một method thông thường

![image-04](/assets/images/2023-12-18-proc-and-lambda-in-ruby/image-04.png)

- Khi **return** hiện diện trực tiếp trong block

  - Proc sẽ báo lỗi khi gọi

  - Lambda vẫn hoạt động tốt

| Đây là lý do vì sao trong Rails, bạn sẽ hiếm khi thấy ai dùng Proc để viết scope, vì Proc kém linh hoạt hơn Lambda ở cú pháp return này

## 4. Một vài method khác

#### 4.1 lambda?

- Để kiểm tra một đối tượng Proc có phải là lambda hay không

![image-05](/assets/images/2023-12-18-proc-and-lambda-in-ruby/image-05.png)

#### 4.2 source_location

- Trả về tên file và số dòng chứa đối tượng Proc này

![image-06](/assets/images/2023-12-18-proc-and-lambda-in-ruby/image-06.png)

#### 4.3 Others

- Còn rất nhiều method hữu ích dành cho proc và lambda, bạn có thể tham khảo trực tiếp trên docs của Ruby

- [https://ruby-doc.org/3.1.2/Proc.html](https://ruby-doc.org/3.1.2/Proc.html){:target="_blank"}

## 5. Lời kết

- Chúng ta đã khám phá xong sự khác biệt giữa Proc và Lambda trong Ruby

- Dù cả hai đều là đối tượng của Proc nhưng Lambda mang lại sự linh hoạt hơn với cú pháp khai báo, xử lý tham số nghiêm túc, và kiểm soát lệnh **return**

- Proc và Lambda đều có vai trò quan trọng trong việc làm cho mã nguồn Ruby trở nên mạnh mẽ và dễ đọc hơn, tùy thuộc vào ngữ cảnh sử dụng
