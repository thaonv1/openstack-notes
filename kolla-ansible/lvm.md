# Cấu hình LVM làm backend cho cinder volume

Một số lưu ý:

- Khai báo storage node trong file multinode nếu có 1 máy riêng chạy LVM

```
[storage]
192.168.20.131 ansible_connection=ssh     ansible_user=root
```

**Lưu ý:** Có thể khai báo từng interface nếu các interface khác nhau trên từng node

```
os02     ansible_ssh_host=192.168.15.45 network_interface=br0 neutron_external_interface=veth2 ansible_python_interpreter=/usr/bin/python3
```
 

- Tạo vg trên storage node

```
pvcreate /dev/vdb
vgcreate cinder-volumes /dev/vdb
```

- Khai báo trong file global

```
enable_cinder: "yes"
enable_cinder_backend_lvm: "yes"
```

- Mặc định node storage sẽ listen các connection iscsi trên network mgnt. Để thay đổi, có thể chỉnh option sau trong file `cinder.conf` ở container `cinder-volume`

```
[lvm-1]
target_ip_address = 192.168.50.211
```

và file `config.json` ở container `tgtd`

```
{
    "command": "tgtd -d 1 -f --iscsi portal=192.168.50.211:3260",
    "config_files": []
}
```

Trong đó `192.168.50.211` là IP dải riêng mà bạn muốn compute truy cập iscsi trên storage node.

