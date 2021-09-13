# Lab insecure deserialization on Portswigger

### 1. Modifying object attributes

Trong trang web này sử dụng một đối tượng được serialized làm giá trị của cookie, sau đó sử dụng giá trị của một trường để xác thực quyền truy cập vào một số tà nguyên và chức năng giới hạn.

Sau khi đăng nhập, ta nhận được giá trị của cookie. Đem đi decode URL và base64

![image](https://user-images.githubusercontent.com/83699106/133071314-429ab3ba-7dcd-4564-a5ab-5773245da793.png)

Sau khi giải mã ta được giá trị

```
O:4:"User":2:{s:8:"username";s:6:"wiener";s:5:"admin";b:0;}
```
Ta quan sát thấy đối tượng này có tên là __User__ và có 2 thuộc tính là __username__ và __admin__

Thay đổi giá trị của __admin__ thành 1 và encode base64 ta được 1 giá trị token mới:

```
Tzo0OiJVc2VyIjoyOntzOjg6InVzZXJuYW1lIjtzOjY6IndpZW5lciI7czo1OiJhZG1pbiI7YjoxO30=

```
Thay thế nó vào giá trị token hiện tại và load lại trang.

![image](https://user-images.githubusercontent.com/83699106/133072048-6872f9a3-3d31-42c7-b1a4-15cf2e7bc4ec.png)

Trang admin hiện ra, xóa tk carlos để hoàn thành.


### 2. Modifying data types

Cách hoạt động của trang web này tương tự như trang web trên, sẽ sử dụng giá trị của một trường acccess_token để kiểm tra.

Sau khi giải mã cookie bằng URL và base64

```
O:4:"User":2:{s:8:"username";s:6:"wiener";s:12:"access_token";s:32:"nj3rb4d0wq8vgndzx2lr39dhvc0ij8qk";}

```

Trong PHP, toán tử `==` chỉ so sánh giá trị của 2 tham số mà bỏ qua kiểu dữ liệu. Vì vậy nếu so sánh 1 số với một chuỗi thì nó sẽ cống gắng ép kiểu từ string sang số
Trong trường hợp này, ta thấy mã token là `nj3rb4d0wq8vgndzx2lr39dhvc0ij8qk` bắt đầu bằng một chữ, khi ép kiểu sang số sẽ có giá trị bằng 0

Do đó ta sẽ thay đổi giá trị của trường `access_token` thành 0 và thay đổi các thành phần khác liên quan.

```
O:4:"User":2:{s:8:"username";s:6:"wiener";s:12:"access_token";i:0;}
```
Sau đó encode bằng base64

```
Tzo0OiJVc2VyIjoyOntzOjg6InVzZXJuYW1lIjtzOjY6IndpZW5lciI7czoxMjoiYWNjZXNzX3Rva2VuIjtpOjA7fQ==
```
Thay thế giá trị cookie hiện tại và trang quản trị se hiện ra.

![image](https://user-images.githubusercontent.com/83699106/133073562-f809f7ae-c963-4e26-a5a5-9446261face7.png)



### 3. Using application functionality

Khi đăng nhập, trang web có một chức năng xóa tài khoản.

Giải mã cookie, giá trị của cookie là một đối tượng có chứa 1 thuộc tính là __avatar_link__ chứa một đường dẫn tới ảnh đại diện của người dùng.
Khi xóa tài khoản, server sẽ xóa avatar của người dùng thông qua đường dẫn này. Do đó, ta sẽ lợi dùng điều này để thay đường dẫn tới file __morale.txt__ của carlos, thay đổi độ dài của giá trị.


Encode lại và thay thế cookie hiện tại, sau đó sử dụng chức năng __delete account__ .

Thay vì xóa avatar của người dùng hiện tại thì phía máy chủ sẽ xóa file theo đường dẫn mà chúng ta đã cung cấp.

### 4. Arbitrary object injection in PHP

### 5. Exploiting Java deserialization with Apache Commons

### 6. Exploiting PHP deserialization with a pre-built gadget chain

### 7. Exploiting Ruby deserialization using a documented gadget chain

### 8. Developing a custom gadget chain for Java deserialization

### 9. Developing a custom gadget chain for PHP deserialization

### 10. Using PHAR deserialization to deploy a custom gadget chain

