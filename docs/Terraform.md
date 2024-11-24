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