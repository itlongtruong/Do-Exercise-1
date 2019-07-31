# Do-Exercise-1
DOCKER COMPOSER
Bài tập :

Sử dụng Vagrant tạo 3 máy ảo CI, WEB, DB. CI có thể ssh đến 2 server kia, CI được cài đặt sẵn Git, Ansible. Vì là môi trường local nên có thể syns source playbook với máy CI.

Chạy provision để cài đặt các thứ cần thiết cho cả 3 server như docker, v.v... 

WEB cần được triển khai 2 container là nginx và php-fpm, có thể thực hiện bằng docker-compose. DB cần triển khai mysql container, tiện nhất là bằng docker-compose.

Viết role để có thể copy nginx config đã được chuẩn bị sẵn/khi muốn thay đổi.

Chuẩn bị source ứng dụng (ở đây chỉ là 1 file php đơn giản có nội dung kết nối đến mysql, nếu thành công thì trả về message "Connection Successful!" ngược lại thì trả về "Connection Failure!") ở một git repository khác.
Viết role để triển khai source này vào container php-fpm.


1. clone mã nguồn từ gitlab của tôi
$ git clone https://github.com/itlongtruong/Do-Exercise-1/

- tạo máy ảo vm

2. Khởi động máy ảo

$ vagrant up

Kết quả của nó là chúng ta sẽ có ba VM: Ci, web, db

3. Tải xuống các vai trò như: docker...vv , tôi đã tạo tệp có tên requirements.yml, hãy cài đặt bằng cách thực hiện theo các lệnh sau
    $cd ansible-playbook/
    $ansible-galaxy install -p roles -r requirements.yml

4. Cấu hình môi trường cho web, máy chủ db. có hai tệp: local.host bao gồm địa chỉ IP của máy chủ và provision.yml nhằm mục đích cài đặt và cấu hình môi trường với các vai trò cho máy chủ: web, db. Hãy chạy lệnh:

$ vagrant ssh ci
$ cd ansible-playbook/
$ ansible-playbook -i local.host provision.yml

5.cài đặt file cấu hình dockerfile có chứ nginx , php và mysql
chạy lệnh : docker-compose -f docker-compose.yml up -d để tạo container từ docker-compose 

Tiếp theo, bạn cần đặt mã kết nối vào index.php chẳng hạn:
$ servername = "192.168.33.24";
$ tên người dùng = "user";
$ mật khẩu = "password";

// Create connection
$conn = new mysqli($servername, $username, $password);

// Check connection
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}
echo "Connected successfully";
?>


6. Kết nối với địa chỉ này: 192.168.33.24:9090/index.php và xem kết quả
