#openstack 
[[ubuntu 레포지터리]]

유저 생성 및 권한 부여
```/bin/bash
CREATE USER 'keystone'@'localhost' IDENTIFIED BY 'It1';
GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'localhost';
```

비밀번호 변경

```/bin/bash
SET PASSWORD FOR 'root'@'localhost' = '1234';
```
