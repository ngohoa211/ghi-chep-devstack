## Giới thiệu

Devstack giúp bạn triển khai nhanh một hệ thống Openstack bằng 1 script.

## Chuẩn bị

Bạn cần một máy chủ chạy Ubuntu 16.04 64bits.

Cấu hình phụ thuộc vào số lượng project bạn muốn cài. Trong trường hợp của tôi, cấu hình như sau:
```sh
CPU: 4 vCPU
RAM: 8096 GB
Disk: 60GB
Interface: eth0 ra internet với IP 123.30.212.235
```

## Cài đặt

Trước tiên cần chắc chắn hệ điều hành đã được cập nhật mới nhất
```sh
apt update && apt dist-upgrade -y
```

Devstack nên được chạy với user khác root, vì thế, ta sẽ tạo một username `stack` để chạy Devstack
```sh
sudo useradd -s /bin/bash -d /opt/stack -m stack
```

User `stack` sẽ thao tác nhiều với quyền root nên ta thiết lập cấu hình để không cần nhập password root khi chạy lệnh sudo
```sh
echo "stack ALL=(ALL) NOPASSWD: ALL" | sudo tee /etc/sudoers.d/stack
sudo su - stack
```

Bạn thay branch của devstack phù hợp với branch trong file localrc để cài đặt.

Thực hiện tải một bản devstack về
```sh
git clone https://git.openstack.org/openstack-dev/devstack --branch stable/queens
cd devstack
```

Kiểm tra nhánh của devstack
```sh
git status
```

Nếu là nhánh master thì ta chuyển sang nhánh queens như sau (do tôi cài stable/queens)
```sh
git checkout stable/queens
```

Ta tạo một tập tin `local.conf` để thiết lập cấu hình cho devstack cài đặt

Nội dung của file `local.conf` lấy từ file sau:

- [Queens](/local-stable-queens.conf)

Sử dụng lệnh để lấy file cấu hình về:
```sh
wget https://raw.githubusercontent.com/TrongTan124/ghi-chep-devstack/master/local-stable-queens.conf -O ./local.conf
```

Bạn sử dụng lệnh `vim local.conf` để chỉnh sửa lại tham số `HOST_IP` trong tập tin thành IP của interface ra ngoài internet của máy bạn.

Ở đây, tôi chỉ định cài đặt toàn bộ project trong Openstack là stable/queens

Chạy script sau để bắt đầu cài đặt
```sh
./stack.sh
```

Quá trình cài đặt sẽ diễn ra khoảng 1h30p. Thông tin sau khi cài đặt xong
```sh
=========================
DevStack Component Timing
 (times are in seconds)  
=========================
run_process           23
test_with_retry        3
apt-get-update        10
pip_install          637
osc                  184
wait_for_service      21
git_timed            550
dbsync               164
apt-get              1165
-------------------------
Unaccounted time     594
=========================
Total runtime        3351



This is your host IP address: 123.30.212.235
This is your host IPv6 address: ::1
Horizon is now available at http://123.30.212.235/dashboard
Keystone is serving at http://123.30.212.235/identity/
The default users are: admin and demo
The password: secretadmin
```

Thiết lập biến môi trường cho user `stack` như sau:
```sh
echo 'source /opt/stack/devstack/openrc admin admin' >> /opt/stack/.bashrc
```

Sau khi cài xong, bạn chuyển sang user stack để bắt đầu thao tác. hoặc đăng nhập vào horizon.

**NOTE**: Sau khi cài đặt xong, đừng khởi động hay tắt máy. vì sẽ bị mất cấu hình của `cinder, openvswitch`

**NOTE**: Cài đặt thêm gói `sudo pip install python-octaviaclient` với user `stack` để sử dụng được tập lệnh `openstack loadbalancer`

## Tham khảo

- [https://docs.openstack.org/devstack/latest/](https://docs.openstack.org/devstack/latest/)