---
layout: post
title:  "Upload File Lên Amazon S3: Dùng Multipart Upload Vs AWS CLI"
date:   2024-06-24 21:55:00 +0700
categories: jekyll update
---
- Việc upload file lên Amazon S3 sẽ trở nên mạnh mẽ và hiệu quả hơn với Multipart Upload và AWS CLI

- Trong bài viết này, chúng ta sẽ khám phá cách sử dụng công cụ dòng lệnh mạnh mẽ của AWS để tối ưu hóa quá trình upload và đảm bảo tính toàn vẹn của dữ liệu

## AWS CLI Configuration

- Việc đầu tiên cần kiểm tra xem máy của bạn đã được cấu hình AWS CLI chưa

```shell
aws configure list
```

![image-01](/assets/images/2024-06-24-multipart-upload-to-s3-with-aws-cli/image-01.png)

- Bạn có thể cấu hình một profile mới như sau

```shell
aws configure
```

![image-02](/assets/images/2024-06-24-multipart-upload-to-s3-with-aws-cli/image-02.png)

- Tạo một bucket trên S3

![image-03](/assets/images/2024-06-24-multipart-upload-to-s3-with-aws-cli/image-03.png)

- Chuẩn bị file muốn upload

![image-04](/assets/images/2024-06-24-multipart-upload-to-s3-with-aws-cli/image-04.png)

## Use high-level aws s3 commands

- Để dùng high-level command cho multipart upload, có thể chạy lệnh sau

```shell
aws s3 cp large_test_file s3://DOC-EXAMPLE-BUCKET/
```

![image-05](/assets/images/2024-06-24-multipart-upload-to-s3-with-aws-cli/image-05.png)

- Khi bạn sử dụng lệnh aws s3 cp để tải lên một file vào Amazon S3, AWS CLI có thể tự quyết định xem có nên dùng multipart upload hay không, dựa vào kích thước của file và các cấu hình liên quan

- Tốc độ upload còn phụ thuộc vào tốc độ mạng nhưng nhìn chung là upload khá nhanh

- Để mã hoá file khi upload, có thể dùng lệnh sau

```shell
aws s3 cp large_test_file s3://DOC-EXAMPLE-BUCKET/ --metadata md5="examplemd5value1234/4Q"
```

- Để config max concurrent request, có thể chạy lệnh sau (mặc định là 10)

```shell
aws configure set default.s3.max_concurrent_requests 20
```

- **Lưu ý:** max_concurrent_requests càng nhiều thì trong quá trình upload sẽ tiêu hao nhiều băng thông và tài nguyên của máy chủ hơn

- Để biết thêm thông tin về cách định cấu hình AWS CLI với Amazon S3 thì hãy xem [**AWS CLI S3 configuration**](https://awscli.amazonaws.com/v2/documentation/api/latest/topic/s3-config.html){:target="_blank"}

- Ngoài lệnh copy, bạn cũng có thể sử dụng các lệnh AWS S3 khác liên quan đến việc tải object lên S3 như `aws s3 sync` hoặc `aws s3 mv`

- Để xem thêm các options khi copy, có thể dùng lệnh sau

```shell
aws s3 cp help
```

## Use low-level aws s3api commands

**Chia file bạn muốn tải lên thành nhiều phần**

- Ubuntu: https://askubuntu.com/questions/54579/how-to-split-larger-files-into-smaller-parts

- MacOS

```shell
split -b fileSize path/to/filename
```

- Mình thử split một file hơn 20MB thì nó sẽ tự động sinh ra 5 files con với tên random như hình minh hoạ

![image-06](/assets/images/2024-06-24-multipart-upload-to-s3-with-aws-cli/image-06.png)

**Chạy lệnh sau để khởi tạo multipart upload và truy xuất liên kết với một UploadID, kết quả phản hồi có chứa UploadID value**

```shell
aws s3api create-multipart-upload --bucket DOC-EXAMPLE-BUCKET --key large-test-file
```

![image-07](/assets/images/2024-06-24-multipart-upload-to-s3-with-aws-cli/image-07.png)

**Copy và lưu lại UploadID value để dùng cho các bước sau**

**Chạy lệnh sau để upload các files part đã split ở trên, kết quả phản hồi có chứa ETag value**

```shell
aws s3api upload-part --bucket DOC-EXAMPLE-BUCKET --key large-test-file --part-number 1 --body large-test-file.001 --upload-id UploadID
```

**Copy ETag value để dùng cho bước sau**

**Lặp lại bước 4 & 5 đến khi upload hết các files part**

![image-08](/assets/images/2024-06-24-multipart-upload-to-s3-with-aws-cli/image-08.png)

**Tạo một file fileparts.json với nội dung là ETag value của các part đã upload ở trên**

```json
{
  "Parts": [
    {
      "ETag": "example8be9a0268ebfb8b115d4c1fd3",
      "PartNumber": 1
    },
    {
      "ETag": "example246e31ab807da6f62802c1ae8",
      "PartNumber": 2
    }
  ]
}
```

![image-09](/assets/images/2024-06-24-multipart-upload-to-s3-with-aws-cli/image-09.png)

**Chạy lệnh sau để hoàn thành quá trình upload**

```shell
aws s3api complete-multipart-upload --multipart-upload file://path/to/fileparts.json --bucket DOC-EXAMPLE-BUCKET --key large-test-file --upload-id UploadID
```

- Kết quả phản hồi sẽ có chứa **Location** value là url dẫn đến file trên S3 của bạn

```json
{
  "ETag": "exampleae01633ff0af167d925cad279-2",
  "Bucket": "DOC-EXAMPLE-BUCKET",
  "Location": "https://DOC-EXAMPLE BUCKET.s3.amazonaws.com/large-test-file",
  "Key": "large-test-file"
}
```

![image-10](/assets/images/2024-06-24-multipart-upload-to-s3-with-aws-cli/image-10.png)

## Resolve upload failures

**Chạy lệnh sau để xem tất cả multipart-upload chưa hoàn thành**

```shell
aws s3api list-multipart-uploads --bucket DOC-EXAMPLE-BUCKET
```

```json
{
  "Uploads": [
    {
      "Initiator": {
        "DisplayName": "multipartmessage",
        "ID": "290xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
      },
      "Initiated": "2016-03-31T06:13:15.000Z",
      "UploadId": "examplevQpHp7eHc_J5s9U.kzM3GAHeOJh1P8wVTmRqEVojxxu...",
      "StorageClass": "STANDARD",
      "Key": "",
      "Owner": {
        "DisplayName": "multipartmessage",
        "ID": "290xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
      }
    }
  ]
}
```

**Chạy lệnh sau để remove UploadID chưa hoàn thành**

```shell
aws s3api abort-multipart-upload --bucket DOC-EXAMPLE-BUCKET --key Key --upload-id UploadID
```

[**Tài liệu tham khảo**](https://repost.aws/knowledge-center/s3-multipart-upload-cli){:target="_blank"}
