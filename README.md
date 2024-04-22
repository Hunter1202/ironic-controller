# OpenStack-Ironic-Bare-Metal

# Cài đặt Ironic trong OpenStack

## Yêu cầu

- Ubuntu Server 20.04 LTS
- Truy cập vào một môi trường OpenStack
- Hiểu biết cơ bản về các khái niệm trong OpenStack

## Bước 1: Cài đặt Các thành phần của OpenStack

1. Cài đặt các gói OpenStack Keystone, Nova, Glance, Neutron và các thành phần cần thiết khác theo tài liệu của OpenStack hoặc sử dụng công cụ triển khai tự động như DevStack hoặc OpenStack-Ansible.

## Bước 2: Cài đặt Ironic

1. Cài đặt các gói Ironic:

    ```bash
    sudo apt-get update
    sudo apt-get install ironic-api ironic-conductor
    ```

2. Cài đặt Ironic client:

    ```bash
    sudo apt-get install python3-ironicclient
    ```

## Bước 3: Cấu hình Ironic

1. Chỉnh sửa tệp cấu hình của Ironic `/etc/ironic/ironic.conf`:

    ```bash
    sudo nano /etc/ironic/ironic.conf
    ```

2. Cấu hình phần `[keystone_authtoken]` với các chi tiết xác thực Keystone:

    ```ini
    [keystone_authtoken]
    auth_url = http://keystone-server:5000/v3
    username = ironic
    password = MẬT_KHẨU_CỦA_BẠN
    project_name = service
    user_domain_name = Default
    project_domain_name = Default
    ```

    Thay `keystone-server` bằng tên máy chủ Keystone thực tế hoặc địa chỉ IP, và thay `MẬT_KHẨU_CỦA_BẠN` bằng mật khẩu phù hợp.

3. Cấu hình các tùy chọn khác trong tệp cấu hình tùy theo môi trường và yêu cầu của bạn.

## Bước 4: Khởi tạo Cơ sở dữ liệu của Ironic

1. Khởi tạo cơ sở dữ liệu của Ironic:

    ```bash
    sudo ironic-dbsync --config-file /etc/ironic/ironic.conf create_schema
    ```

## Bước 5: Khởi động Dịch vụ Ironic

1. Khởi động dịch vụ Ironic API và conductor:

    ```bash
    sudo systemctl start openstack-ironic-api openstack-ironic-conductor
    ```

2. Bật các dịch vụ để khởi động cùng với hệ thống:

    ```bash
    sudo systemctl enable openstack-ironic-api openstack-ironic-conductor
    ```

## Bước 6: Xác nhận Cài đặt

1. Kiểm tra xem các dịch vụ Ironic có đang chạy không:

    ```bash
    sudo systemctl status openstack-ironic-api openstack-ironic-conductor
    ```

2. Xác nhận rằng các điểm cuối Ironic đã được đăng ký với Keystone:

    ```bash
    openstack endpoint list | grep ironic
    ```

3. Sử dụng Ironic client để tương tác với API Ironic:

    ```bash
    openstack baremetal driver list
    openstack baremetal node list
    ```

## Kết luận

Bạn đã cài đặt và cấu hình Ironic thành công trong môi trường OpenStack của mình. Bây giờ bạn có thể triển khai và quản lý các máy chủ bare metal bằng dịch vụ Ironic.
