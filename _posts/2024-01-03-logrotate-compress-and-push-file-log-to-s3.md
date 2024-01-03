---
layout: post
title:  "Logrotate: Compress và push file log lên S3"
date:   2024-01-03 13:45:00 +0700
categories: jekyll update
---
- Log files giúp bạn hiểu rõ về lịch sử hoạt động của máy chủ nhưng chúng có thể trở nên nặng nề và khó quản lý

- Logrotate không chỉ giúp giảm kích thước file log một cách tự động mà còn có thể nén file để tiết kiệm không gian lưu trữ

- Trong bài viết này, chúng ta sẽ tìm hiểu cách Logrotate được cấu hình để tự động nén và đẩy file lên S3, giúp bạn duy trì hệ thống được sạch sẽ và dễ quản lý hơn

## Config

```
cd /etc/logrotate.d
touch rails_log_rotation
```

```
/home/ubuntu/path-to-deploy/current/log/production.log {
  daily
  dateext
  missingok
  rotate 90
  compress
  copytruncate
  lastaction
    aws s3 sync /home/ubuntu/path-to-deploy/current/log/ s3://path-to-bucket-name --exclude "*" --include "production.log*.gz" 2>&1
  endscript
  create 0644 root root
  su root root
}
```

**home/ubuntu/path-to-deploy/current/log/production.log**

- Link đến file log cần được quản lý bởi logrotate

**daily**

- Thực hiện xoay vòng log hàng ngày (được thực hiện bằng cron-job)

- Có nghĩa là file */home/ubuntu/path-to-deploy/current/log/production.log* sẽ được làm mới mỗi ngày

- Check cron configuration

```
cd /etc/
ls | grep cron
cron.d
cron.daily
cron.hourly
cron.monthly
cron.weekly
crontab
```

**dateext**

- Thêm ngày vào filename của file log được xoay vòng để đảm bảo không bị trùng tên

**missingok**

- Cho phép logrotate bỏ qua nếu không tìm thấy file

**rotate 90**

- Giữ lại tối đa 90 file logs đã quay vòng

- Trong trường hợp này, có thể hiểu là cho phép lưu trữ file logs của 90 ngày gần nhất

**compress**

- Nén file log đã xoay vòng để tiết kiệm không gian lưu trữ

**copytruncate**

- Sao chép nội dung từ file cũ vào file mới, sau đó quay lại đầu file cũ và cắt bớt nó. Điều này giúp logrotate có thể xoay vòng mà không làm ảnh hưởng đến ứng dụng đang chạy

**lastaction và endscript**

- Bọc một đoạn code hoặc command được thực thi sau khi xoay vòng

- Trong trường hợp này, sử dụng AWS CLI để đồng bộ hóa các file logs đã xoay vòng với Amazon S3

- Loại trừ tất cả các files, chỉ bao gồm các file logs đã nén (production.log*.gz)

- Lưu ý

  - Bạn cần thay thế path-to-bucket-name bằng đường dẫn thích hợp đến S3 Bucket của bạn

  - EC2 phải tương tác được với S3 Bucket của bạn (có thể dùng aws configure hoặc attached IAM role đều được)

**create 0644 root root**

- Tạo một file log mới với quyền truy cập 0644 và chủ sở hữu là root

**su root root**

- Chuyển quyền sở hữu của file sang root sau khi tạo mới để đảm bảo an toàn và bảo mật

**Tóm lại**

- Config này giúp bạn quản lý và tự động xoay vòng file production.log mỗi ngày

- Nén file để tiết kiệm không gian và đồng thời sao chép lên Amazon S3 để lưu trữ

## Manual test config

```
sudo logrotate -f /etc/logrotate.conf
```
