# Terraform

Nếu bạn cần lưu lại kết quả của câu lệnh plan, bạn sử dụng thêm -out option khi chạy. Ví dụ ta sẽ save lại kết quả của câu lệnh plan trong file json.
```bash
$ terraform plan -out plan.out
$ terraform show -json plan.out > plan.json
``` 

```bash
terraform apply -auto-approve
```

Ta có thể chạy câu lệnh plan trước, với -out option, để review resource, sau đó ta sẽ chạy câu lệnh apply với kết quả của plan trước đó, như sau:

Đầu tiên là sẽ chạy job để kiểm tra resource.
```bash
terraform plan -out plan.out
```

Nếu mọi thứ ok thì job trên sẽ pass và tiếp theo ta sẽ chạy job để tạo resource.
```bash
terraform apply "plan.out"
```

## Data block
Bên cạnh việc ta sử dụng resource block để tạo resource, thì terraform có cung cấp cho ta một block khác dùng để queries và tìm kiếm data trên AWS, block này sẽ giúp ta tạo resource một cách linh hoạt hơn là phải điền cứng giá trị của resource. Ví dụ như ở trên thì trường ami của EC2 ta fix giá trị là ami-09dd2e08d601bff67, để biết được giá trị này thì ta phải lên AWS để kiếm, với lại nếu ta dùng giá trị này thì người khác đọc cũng không biết được giá trị này là thuộc ami loại gì.

## Resource drift
Resource drift là vấn đề khi config resource của ta bị thay đổi bên ngoài terraform, với AWS thì có thể là do ai đó dùng Web Console của AWS để thay đổi config gì đó của resource mà được ta deploy bằng terraform

## Variable
Ta sẽ dùng variable block để khai báo variable, và theo sau nó là tên của variable đó. Ở ví dụ trên, ta tạo thêm một file nữa với tên là variable.tf (này bạn đặt tên gì cũng được nha) để khai báo biến của ta.
```bash
variable "instance_type" {
  type = string
  description = "Instance type of the EC2"
}
```
Thuộc tính là type để chỉ định type của biến đó, thuộc tính description dùng để ghi lại mô tả cho người đọc biến đó có ý nghĩa gì. Chỉ có thuộc tính type là bắt buộc phải khai báo. Trong terraform thì một biến sẽ có các type sau đây:

Gán giá trị cho variable
Để gán giá trị cho biến, ta sẽ tạo một file tên là terraform.tfvars
```bash
instance_type = "t2.micro"
```
Khi ta chạy terraform apply thì file terraform.tfvars sẽ được terraform sử dụng mặc định để load giá trị cho biến, nếu ta không muốn dùng mặc định, thì khi chạy câu lệnh apply ta thêm vào option là -var-file nữa. Tạo một file tên là production.tfvars

## Output
Thông thường khi tạo EC2 xong, ta sẽ muốn xem địa chỉ IP của nó, để làm được việc đó thì ta sử dụng output block.
Để in được giá trị public IP của EC2, ta thêm vào file main.tf đoạn code sau:
```bash
...

output "ec2" {
  value = {
    public_ip = aws_instance.hello.public_ip
  }
}
```

## Count parameter
Mọi thứ đều không có gì phức tạp hết, nhưng nếu giờ ta muốn 100 con EC2 thì sao? Ta có thể copy ra 100 resource block, nhưng không ai làm vậy 😂, mà ta sẽ sử dụng count parameter.

Count là một meta argument, là một thuộc tính trong terraform chứ không phải của resource type thuộc provider, ở bài 1 ta đã nói resource type block chỉ có chứa các thuộc tính mà provider cung cấp cho, còn meta argument là thuộc tính của terraform => nghĩa là ta có thể sử dụng nó ở bất kì resource block nào. Cập nhật lại file main.tf mà sẽ tạo ra 5 EC2 như sau:
```bash
provider "aws" {
  region = "us-west-2"
}

data "aws_ami" "ubuntu" {
  most_recent = true

  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-focal-20.04-amd64-server-*"]
  }

  owners = ["099720109477"]
}

resource "aws_instance" "hello" {
  count         = 5
  ami           = data.aws_ami.ubuntu.id
  instance_type = var.instance_type
}

output "ec2" {
  value = {
    public_ip1 = aws_instance.hello[0].public_ip
    public_ip2 = aws_instance.hello[1].public_ip
    public_ip3 = aws_instance.hello[2].public_ip
    public_ip4 = aws_instance.hello[3].public_ip
    public_ip5 = aws_instance.hello[4].public_ip
  }
}
```

Bây giờ ta đã giải quyết được vấn đề copy resource ra khi cần tạo nó với số lượng nhiều hơn, nhưng ở phần output, ta vẫn phải ghi ra từng resource riêng lẻ. Ta sẽ giải quyết nó bằng cách sử dụng for expressions.
## For expressions
```bash
output "ec2" {
  value = {
    public_ip = [ for v in aws_instance.hello : v.public_ip ]
  }
}
```

## Upload file to S3 aws
```bash
provider "aws" {
  region = "us-west-2"
}

resource "aws_s3_bucket" "static" {
  bucket = "terraform-series-bai3"
  acl    = "public-read"
  policy = file("s3_static_policy.json")

  website {
    index_document = "index.html"
    error_document = "error.html"
  }
}

locals {
  mime_types = {
    html  = "text/html"
    css   = "text/css"
    ttf   = "font/ttf"
    woff  = "font/woff"
    woff2 = "font/woff2"
    js    = "application/javascript"
    map   = "application/javascript"
    json  = "application/json"
    jpg   = "image/jpeg"
    png   = "image/png"
    svg   = "image/svg+xml"
    eot   = "application/vnd.ms-fontobject"
  }
}

resource "aws_s3_bucket_object" "object" {
  for_each = fileset(path.module, "static-web/**/*")
  bucket = aws_s3_bucket.static.id
  key    = replace(each.value, "static-web", "")
  source = each.value
  etag         = filemd5("${each.value}")
  content_type = lookup(local.mime_types, split(".", each.value)[length(split(".", each.value)) - 1])
}
```

## Local values
Đây là block giúp ta khai báo một giá trị local trong file terraform và có thể sử dụng lại được nhiều lần
Để truy cập giá trị local thì ta dùng cú pháp local.<KEY>

