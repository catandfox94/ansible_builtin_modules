- 系统账号的管理

  - ansible.builtin.user
    - 用户账号的管理
    - 功能上对应系统命令： useradd  userdel  usermod  passwd
    - 常用的选项：

  | 选项                                                         | 作用                             | 值                                                           |
  | ------------------------------------------------------------ | -------------------------------- | ------------------------------------------------------------ |
  | name[必需]                                                   | 指定用户名                       | 字符串                                                       |
  | append                                                       | 是否给追加用户的新的附加组       | 布尔值： false【默认】 覆盖用户原本的附加组信息  true  给用户添加新的附加组 |
  | comment                                                      | 用户全名【用户的描述信息】       | 字符串                                                       |
  | create_home                                                  | 是否创建用户宿主目录             | 布尔值，默认值为true; true 代表创建用户宿主目录。 false 代表不创建用户宿主目录 |
  | expires                                                      | 指定用户账号过期时间             | 给在epoch期间的用户账号指定新的过期时间，而不是默认配置，使用浮点数设置，支持使用负数 |
  | force                                                        | 是否强制移除用户      | 布尔值；默认值为false; 效果等同于userdel --force |
  | group                                                        | 设置用户的基本组                 | 字符串                                                       |
  | groups                                                       | 设置用户的附加组                 | 使用列表来一次性设置多个附加组，对于用户账号是新增还是覆盖取决于append的值 |
  | home                                                         | 设置用户宿主目录的路径           | 宿主目录路径                                                 |
  | move_home                                                    | 是否移动用户的宿主目录路径       | 布尔值，false【默认值】不移动用户宿主目录，true 移动用户的宿主目录 |
  | password                                                     | 规定用户口令                     | 设置用户的口令，注意事项：password 不会给提供的口令进行加密处理，因此需要结合插件或者其他用户进行加密 |
  | shell                                                        | 指定用户登录shell                | shell 路径                                                   |
  | skeleton                                                     | 宿主目录结构参考路径             | skeleton 目录路径                                            |
  | generate_ssh_key                                             | 是否生成ssh密钥对                | 布尔值<br />false【默认值】 不创建密钥<br />true 创建密钥    |
  | ssh_key_bits / ssh_key_file \| ssh_key_passphrase / shh_key_type | 用户ssh密钥                      | 分别指定ssh密钥的长度、私钥路径、私钥的保护密码、ssh密钥类型 |
  | state                                                        | 指定的用户账号是否在受控节点存在 | present  用户账号在受控节点存在<br />absent 用户账号在受控节点不存在 |
  | uid                                                          | 用户账号id                       | 数值                                                         |
  | update_password                                              | 设置用户密码的时间               | on_create: 在用户账号创建的时候设置 <br />always： 任何情况吸下都更新并设置用户密码 |
  | system                                                       | 是否为系统账号                   | 布尔值<br />false【默认】不是系统账号<br />true： 是系统账号 |
  | remove                                                       | 是否移除用户宿主目录             | 布尔值；false【默认值】不移除宿主目录；true 移除宿主目录    <br /> 效果等同于 userdel -r |

  

  - ansible.builtin.group 
    - 组账号的管理
    - 功能和系统管理命令： groupadd groupdel   groupmod
    - 常用的选项：

  | 选项                | 作用                           | 值                                                |
  | ------------------- | ------------------------------ | ------------------------------------------------- |
  | gid                 | 指定组ID                       | 数值                                              |
  | gid_max \|  gid_min | 指定GID 的范围                 | 数值                                              |
  | name[必需]          | 指定组账号账号名               | 字符串                                            |
  | state               | 指定的组账号是否在受控节点存在 | present  组账号存在   absent 组账号不存在         |
  | system              | 账号是否为系统账号             | 布尔值“ false[默认] 不是系统账号  true 是系统账号 |

  ```
  # 创建用户账号
  [root@control-node ~]# ansible localhost -i real_hosts.ini  -m user -a \
  > 'name=user01 comment="user managed bu ansible" uid=1003 '
  localhost | CHANGED => {
      "changed": true,
      "comment": "user managed bu ansible",
      "create_home": true,
      "group": 1003,
      "home": "/home/user01",
      "name": "user01",
      "shell": "/bin/bash",
      "state": "present",
      "system": false,
      "uid": 1003
  }
  [root@control-node ~]# id user01
  uid=1003(user01) gid=1003(user01) groups=1003(user01)
  [root@control-node ~]# getent passwd user01
  user01:x:1003:1003:user managed bu ansible:/home/user01:/bin/bash
  
  # 创建组账号
  [root@control-node ~]# ansible localhost -i real_hosts.ini  -m group -a \
  > 'name=group01 state=present gid=1004'
  localhost | CHANGED => {
      "changed": true,
      "gid": 1004,
      "name": "group01",
      "state": "present",
      "system": false
  }
  [root@control-node ~]# getent group group01
  group01:x:1004:
  
  
  # 调整用户账号信息
  [root@control-node ~]# ansible localhost -i real_hosts.ini  -m user -a 'name=user01 comment="User 01" '
  localhost | CHANGED => {
      "append": false,
      "changed": true,
      "comment": "User 01",
      "group": 1003,
      "home": "/home/user01",
      "move_home": false,
      "name": "user01",
      "shell": "/bin/bash",
      "state": "present",
      "uid": 1003
  }
  [root@control-node ~]# getent passwd user01
  user01:x:1003:1003:User 01:/home/user01:/bin/bash
  [root@control-node ~]# ansible localhost -i real_hosts.ini  -m user -a 'name=user01 uid=1005'
  localhost | CHANGED => {
      "append": false,
      "changed": true,
      "comment": "User 01",
      "group": 1003,
      "home": "/home/user01",
      "move_home": false,
      "name": "user01",
      "shell": "/bin/bash",
      "state": "present",
      "uid": 1005
  }
  [root@control-node ~]# getent passwd user01
  user01:x:1005:1003:User 01:/home/user01:/bin/bash
  [root@control-node ~]# ls /home/user01/
  [root@control-node ~]# ansible localhost -i real_hosts.ini  -m user -a 'name=user01 home=/var/user01 move_home=yes'
  localhost | CHANGED => {
      "append": false,
      "changed": true,
      "comment": "User 01",
      "group": 1003,
      "home": "/var/user01",
      "move_home": true,
      "name": "user01",
      "shell": "/bin/bash",
      "state": "present",
      "uid": 1005
  }
  [root@control-node ~]# ls /home/user01
  ls: cannot access '/home/user01': No such file or directory
  [root@control-node ~]# ls /var/user01/
  [root@control-node ~]# id user01
  uid=1005(user01) gid=1003(user01) groups=1003(user01)
  [root@control-node ~]# ansible localhost -i real_hosts.ini  -m user -a 'name=user01 groups=group01 append=true'
  localhost | CHANGED => {
      "append": true,
      "changed": true,
      "comment": "User 01",
      "group": 1003,
      "groups": "group01",
      "home": "/var/user01",
      "move_home": false,
      "name": "user01",
      "shell": "/bin/bash",
      "state": "present",
      "uid": 1005
  }
  [root@control-node ~]# id user01
  uid=1005(user01) gid=1003(user01) groups=1003(user01),1004(group01)
  [root@control-node ~]# ansible localhost -i real_hosts.ini  -m user -a 'name=user01 groups=wheel append=true'
  localhost | CHANGED => {
      "append": true,
      "changed": true,
      "comment": "User 01",
      "group": 1003,
      "groups": "wheel",
      "home": "/var/user01",
      "move_home": false,
      "name": "user01",
      "shell": "/bin/bash",
      "state": "present",
      "uid": 1005
  }
  [root@control-node ~]# id user01
  uid=1005(user01) gid=1003(user01) groups=1003(user01),10(wheel),1004(group01)
  [root@control-node ~]# ansible localhost -i real_hosts.ini  -m user -a 'name=user01 groups=wheel append=false'
  localhost | CHANGED => {
      "append": false,
      "changed": true,
      "comment": "User 01",
      "group": 1003,
      "groups": "wheel",
      "home": "/var/user01",
      "move_home": false,
      "name": "user01",
      "shell": "/bin/bash",
      "state": "present",
      "uid": 1005
  }
  [root@control-node ~]# id user01
  uid=1005(user01) gid=1003(user01) groups=1003(user01),10(wheel)
  [root@control-node ~]# ansible localhost -i real_hosts.ini  -m user -a 'name=user01 ssh_key_bits=4096 ssh_key_file=cloud_id_rsa ssh_key_type=rsa generate_ssh_key=true'
  localhost | CHANGED => {
      "append": false,
      "changed": true,
      "comment": "User 01",
      "group": 1003,
      "home": "/var/user01",
      "move_home": false,
      "name": "user01",
      "shell": "/bin/bash",
      "ssh_fingerprint": "4096 SHA256:LWMZ0z8e9/utob2qOA0fXe7lOZhtxRlqKvi3CEu4DtY ansible-generated on control-node (RSA)",
      "ssh_key_file": "/var/user01/cloud_id_rsa",
      "ssh_public_key": "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDiotGItOzfcYary/wr5Vuqc+nwU/0RWgb0yru0WQbIbF6iphHS7S9vbTD06N9ZWBWaYFi+osjgRy42n+WmRBcGkGB+I/rvsK2VqhI5mjfiy4NEEDQe8Oot9nCgY6dBpUIfyLwcqkBVlV4mTM1gmXRZiDZG3rZdXsn1byVLSmhUifI3oeR7P3/iu26FxB4wku/Z0L/W2vdvc4tAez6UT4bKGqZq7E2s0n8aRw6O+b8VkJS1S8dsnoAj35Fk4jEeKECZYV8dasOkYGuNF7ZOp0uD6tEeWc7Tw3XhvDGtHL8CNruHs/dJ2oYUHJgDqZrmehfhwBzWV/imJUfxVc0sXeW1t2m6nF/EB/nk21/nMdEzoacKKlvi0ODnSvpGbKlturNxQzX2hCeSUjEtl2scSGUxbUx4YEIwQtozs1ZS/jjrqguhNfOH4f4vaIOVzcfwKHCamb6XgZylShxHY4BbaWiMofZu+x0xmySq3sAqZujZRc+QBJ7U1z2hXmXH6kkmR7Q4GEmI9Dy0+kzZajr7yi3Mzxymtp8NlRqKbzsFw0xBF7r+COvEu3Ow7mqSay7QdaZfO3Dab6IyLfNXN5eJabi+jzdwFz/38XITKh6nnEqX4/Y8loezpEX2qW848PLPdIok2vyvnrpKt47XRFIPqLMZzJLrJ6YO99vF7ivTm375yw== ansible-generated on control-node",
      "state": "present",
      "uid": 1005
  }
  [root@control-node ~]# 
  [root@control-node ~]# ls /var/user01/
  cloud_id_rsa  cloud_id_rsa.pub
  
  
  # 移除账号
  [root@control-node ~]# ansible localhost -i real_hosts.ini  -m user -a 'name=user01 state=absent remove=false'
  localhost | CHANGED => {
      "changed": true,
      "force": false,
      "name": "user01",
      "remove": false,
      "state": "absent"
  }
  [root@control-node ~]# ansible localhost -i real_hosts.ini  -m group -a 'name=group01 state=absent'
  localhost | CHANGED => {
      "changed": true,
      "name": "group01",
      "state": "absent"
  }
  [root@control-node ~]# getent group group01 
  
  ```
