# Cài đặt Ironic trong OpenStack. ([Visit website](https://hunter1202.github.io/ironic-controller/))

## Yêu cầu

- Debian 12 Server
- Truy cập vào một môi trường OpenStack
- Hiểu biết cơ bản về các khái niệm trong OpenStack

## Bước 1: Cài đặt Các thành phần của OpenStack

Trước khi cài đặt Ironic, bạn cần cài đặt các thành phần cơ bản của OpenStack như Keystone, Nova, Glance và Neutron. Dưới đây là hướng dẫn chi tiết:

### Sử dụng DevStack

[DevStack](https://docs.openstack.org/devstack/latest/) là một công cụ triển khai OpenStack nhanh chóng và dễ dàng cho môi trường phát triển và thử nghiệm. Bạn có thể cài đặt DevStack trên một máy chủ hoặc máy ảo với Ubuntu Server. Sau khi cài đặt DevStack, các thành phần OpenStack sẽ được cài đặt tự động.

1. Tải xuống mã nguồn của DevStack từ kho lưu trữ chính thức:

    ```bash
    git clone https://opendev.org/openstack/devstack
    ```

2. Chuyển vào thư mục devstack và sao chép file cấu hình mẫu:

    ```bash
    cd devstack
    cp samples/local.conf .
    ```

3. Chỉnh sửa file `local.conf` để cấu hình DevStack theo yêu cầu của bạn. Bạn cần xác định các cài đặt như IP address, mật khẩu, và các thành phần OpenStack cần cài đặt.

4. Chạy script cài đặt DevStack:

    ```bash
    ./stack.sh
    ```

5. Sau khi cài đặt hoàn tất, bạn sẽ có một môi trường OpenStack hoạt động để tiến hành cài đặt Ironic.

### Sử dụng OpenStack-Ansible

[OpenStack-Ansible](https://docs.openstack.org/openstack-ansible/latest/) là một khung cài đặt OpenStack mạnh mẽ và linh hoạt, dựa trên Ansible. Nó cho phép bạn cài đặt OpenStack trên một hoặc nhiều máy chủ với kiến trúc phân tán.

1. Tải xuống mã nguồn của OpenStack-Ansible từ kho lưu trữ chính thức:

    ```bash
    git clone https://opendev.org/openstack/openstack-ansible
    ```

2. Chuyển vào thư mục openstack-ansible và tải các roles cần thiết:

    ```bash
    cd openstack-ansible
    scripts/bootstrap-ansible.sh
    ```

3. Tạo file `user_variables.yml` để cấu hình các biến cho cài đặt của bạn, ví dụ:

    ```yaml
    ---
    nova_compute_virt_type: qemu
    lxc_container_backing_store: dir
    glance_default_store: file
    ```

4. Chỉnh sửa file `openstack_user_config.yml` để cấu hình các biến cụ thể cho môi trường của bạn.

5. Chạy playbook cài đặt OpenStack-Ansible:

    ```bash
    openstack-ansible setup-hosts.yml
    openstack-ansible setup-infrastructure.yml
    openstack-ansible setup-openstack.yml
    ```

6. Sau khi cài đặt hoàn tất, bạn sẽ có một môi trường OpenStack hoạt động để tiến hành cài đặt Ironic.

Đảm bảo rằng bạn đã cấu hình DevStack hoặc OpenStack-Ansible đúng cách trước khi tiến hành cài đặt Ironic.


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

Chi tiết [click here](https://hunter1202.github.io/ironic-controller/ironic-controller.pdf).
