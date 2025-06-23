## 管理文件内容：
- **ansible.builtin.blockinfile|lineinfile**
  - 管理指定的文本块
  - ansible.builtin.lineinfile模块功能与blockinfile类似，区别是lineinfile是单行文件，而blockinfile 文件块（多行文本）
- 常用选项如下：lineinfile基本与blockinfile选项一致

| 选项                                      | 作用                                                         | 值                                                           |
| ----------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| path                                      | 指定文件                                                     | 文件路径                                                     |
| create                                    | 在指定文件不存在的情况下，是否创建文件                       | 布尔值 false 不创建（默认） true 创建                        |
| mode/user/group                           | 指定文件的权限、属主属组                                     | 值为字符串，设置属主属组、权限的规则与chmod、chown的要求一致 |
| block（blockinfile） \| line (lineinfile) | 指定的文本内容                                               | 字符床                                                       |
| state                                     | 指定文本的移除和添加                                         | present（默认值）: 添加指定内容   absent:删除指定内容        |
| insterafter                               | 指定文件添加位置                                             | 默认值为EOF ，在指定文件末尾添加指定的行，或者根据输入的文件，进行匹配，如果有多处匹配，默认添加到最后一次匹配的行后面 |
| insertbefore                              | 指定文件添加位置                                             | 默认值为BOF ，在指定文件开头添加指定的行，或者根据输入的文件，进行匹配，如果有多处匹配，默认添加到第一次匹配的行前面 |
| marker（lineinfile不支持）                | 指定文本边界                                                 | 在文本前后添加的标记                                         |
| appendnewline（lineinfile不支持）         | 在文本内容后面添加一个空行，                                 | 布尔值,false 不添加（默认）  true添加                        |
| prependnewline（lineinfile不支持）        | 在文本内容前面添加一个空行，如果插入到文件的第一行，则不会添加新行 | 布尔值,false 不添加（默认）  true添加                        |



```bash
[root@control-node ~]# ansible localhost -i real_hosts.ini -m ansible.builtin.blockinfile -a 'path=/root/blockfile  block="block1" '
localhost | CHANGED => {
    "changed": true,
    "msg": "Block inserted"
}
[root@control-node ~]# cat blockfile 
# BEGIN ANSIBLE MANAGED BLOCK
block1
# END ANSIBLE MANAGED BLOCK
[root@control-node ~]# ansible localhost -i real_hosts.ini -m ansible.builtin.blockinfile -a 'path=/root/blockfile  block="block1" marker="END OF FILE" insertafter=EOF'
localhost | CHANGED => {
    "changed": true,
    "msg": "Block inserted"
}
[root@control-node ~]# cat blockfile 
# BEGIN ANSIBLE MANAGED BLOCK
block1
# END ANSIBLE MANAGED BLOCK
END OF FILE
block1
END OF FILE
[root@control-node ~]# ansible localhost -i real_hosts.ini -m ansible.builtin.blockinfile -a 'path=/root/blockfile  block="block2" marker="BEGINNING OF FILE" insertbefore=BOF'
localhost | CHANGED => {
    "changed": true,
    "msg": "Block inserted"
}
[root@control-node ~]# cat blockfile 
BEGINNING OF FILE
block2
BEGINNING OF FILE
# BEGIN ANSIBLE MANAGED BLOCK
block1
# END ANSIBLE MANAGED BLOCK
END OF FILE
block1
END OF FILE
[root@control-node ~]# ansible localhost -i real_hosts.ini -m ansible.builtin.blockinfile -a 'path=/root/blockfile  block="block3" marker="# append after a line" insertafter="blcok1"'
localhost | CHANGED => {
    "changed": true,
    "msg": "Block inserted"
}
[root@control-node ~]# cat blockfile 
BEGINNING OF FILE
block2
BEGINNING OF FILE
# BEGIN ANSIBLE MANAGED BLOCK
block1
# END ANSIBLE MANAGED BLOCK
END OF FILE
block1
END OF FILE
# append after a line
block3
# append after a line
[root@control-node ~]# ansible localhost -i real_hosts.ini -m ansible.builtin.blockinfile -a 'path=/root/blockfile  block="block3" marker="# append after a line" insertafter="block1"'
localhost | CHANGED => {
    "changed": true,
    "msg": "Block inserted"
}
[root@control-node ~]# cat blockfile 
BEGINNING OF FILE
block2
BEGINNING OF FILE
# BEGIN ANSIBLE MANAGED BLOCK
block1
# END ANSIBLE MANAGED BLOCK
END OF FILE
block1
END OF FILE
# append after a line
block3
# append after a line
block3
# append after a line
[root@control-node ~]# vim blockfile 
[root@control-node ~]# ansible localhost -i real_hosts.ini -m ansible.builtin.blockinfile -a 'path=/root/blockfile  block="block3" marker="# append after a line" insertafter="block2"'
localhost | CHANGED => {
    "changed": true,
    "msg": "Block inserted"
}
[root@control-node ~]# vim blockfile 
[root@control-node ~]# cat blockfile 
BEGINNING OF FILE
block2
# append after a line
block3
# append after a line
BEGINNING OF FILE
# BEGIN ANSIBLE MANAGED BLOCK
block1
# END ANSIBLE MANAGED BLOCK
END OF FILE
block1
END OF FILE
[root@control-node ~]# ansible localhost -i real_hosts.ini -m ansible.builtin.blockinfile -a 'path=/root/blockfile  block="block4" marker="# insert before a line" insertbefore="block2"'
localhost | CHANGED => {
    "changed": true,
    "msg": "Block inserted"
}
[root@control-node ~]# cat blockfile 
BEGINNING OF FILE
# insert before a line
block4
# insert before a line
block2
# append after a line
block3
# append after a line
BEGINNING OF FILE
# BEGIN ANSIBLE MANAGED BLOCK
block1
# END ANSIBLE MANAGED BLOCK
END OF FILE
block1
END OF FILE

## 相同操作替换为lineinfile
[root@control-node ~]# ansible localhost -i real_hosts.ini -m ansible.builtin.lineinfile -a 'path=/root/blockfile  line="block5" insertbefore="block2"'
localhost | CHANGED => {
    "backup": "",
    "changed": true,
    "msg": "line added"
}
[root@control-node ~]# cat blockfile 
BEGINNING OF FILE
# insert before a line
block4
# insert before a line
block5
block2
# append after a line
block3
# append after a line
BEGINNING OF FILE
# BEGIN ANSIBLE MANAGED BLOCK
block1
# END ANSIBLE MANAGED BLOCK
END OF FILE
block1
END OF FILE

```

- **ansible.builtin.replace** 
  - 文本替换
  - 常用选项
    - path: 指定文件路径
    - regexp: 使用正则匹配要替换的行      /old string 
    - replace: 要替换的内容             / new string

```bash
[root@control-node ~]# cat blockfile 
BEGINNING OF FILE
# insert before a line
block4
# insert before a line
block5
block2
# append after a line
block3
# append after a line
BEGINNING OF FILE
# BEGIN ANSIBLE MANAGED BLOCK
block1
# END ANSIBLE MANAGED BLOCK
END OF FILE
block1
END OF FILE
[root@control-node ~]# ansible localhost -i real_hosts.ini -m ansible.builtin.replace \
> -a "path=/root/blockfile regexp=block  replace=new_replace"
localhost | CHANGED => {
    "changed": true,
    "msg": "6 replacements made",
    "rc": 0
}
[root@control-node ~]# cat blockfile 
BEGINNING OF FILE
# insert before a line
new_replace4
# insert before a line
new_replace5
new_replace2
# append after a line
new_replace3
# append after a line
BEGINNING OF FILE
# BEGIN ANSIBLE MANAGED BLOCK
new_replace1
# END ANSIBLE MANAGED BLOCK
END OF FILE
new_replace1
END OF FILE

```



## 管理文件
- **ansible.builtin.copy **
  - 文件的复制，把指定文件从控制节点复制到受控节点

| 选项                                                | 作用                           | 值                                                           |
| --------------------------------------------------- | ------------------------------ | ------------------------------------------------------------ |
| dest/path【必需】                                   | 指定目标文件，位置在受控节点上 | 文件路径                                                     |
| src                                                 | 指定源文件                     | 文件路径                                                     |
| remote_src                                          | 源文件是否在被控节点上         | 布尔值，false（默认）源文件在控制节点，不在被控节点， true 源文件在被控节点，不在控制节点 |
| content                                             | 规定目标文件文本内容           | 文件字符串，可以使用 \|保持文件原始格式  >折叠文件换行等格式设置 |
| mode, owner, group, setype, seuser, serole, selevel | 文件权限                       | 字符串                                                       |
| backup                                              | 目标文件是否备份               | 布尔值，false(默认)不备份，true 备份                         |
| force                                               | 是否强制覆盖文件               | 布尔值，false只有在目标文件不存在的情况下才复制，true（默认）只要目标文件内容与源文件或者content规定的值不一样，就覆盖目标文件 |
| follow                                              | 跟随符号链接                   | 布尔值 false 不跟随目标文件的符号链接，true(默认值)跟随目标文件的符号链接 |
| checksum                                            | 获取源文件的hash值             | 需要计算源文件的hash值来确定                                 |

```bash
[root@control-node ~]# ansible localhost -i real_hosts.ini -m copy \
> -a "src=/root/blockfile dest=/tmp/destfile"
localhost | CHANGED => {
    "changed": true,
    "checksum": "5be110afd49257c0980920c79b7b46e9a3fb5aba",
    "dest": "/tmp/destfile",
    "gid": 0,
    "group": "root",
    "md5sum": "928fe462179aa252805c04def6ebc484",
    "mode": "0644",
    "owner": "root",
    "secontext": "unconfined_u:object_r:admin_home_t:s0",
    "size": 286,
    "src": "/root/.ansible/tmp/ansible-tmp-1749026330.0848007-42949-276181020313140/.source",
    "state": "file",
    "uid": 0
}
[root@control-node ~]# ls /tmp/destfile 
/tmp/destfile
[root@control-node ~]# ansible localhost -i real_hosts.ini -m copy -a "content='example content' dest=/tmp/destfile"
localhost | CHANGED => {
    "changed": true,
    "checksum": "0ff30941ca5acd879fd809e8c937d9f9e6dd1615",
    "dest": "/tmp/destfile",
    "gid": 0,
    "group": "root",
    "md5sum": "ef06f76f53a4386cea89de53db991bea",
    "mode": "0644",
    "owner": "root",
    "secontext": "unconfined_u:object_r:admin_home_t:s0",
    "size": 15,
    "src": "/root/.ansible/tmp/ansible-tmp-1749026365.3969333-42985-272456312970787/.source",
    "state": "file",
    "uid": 0
}
[root@control-node ~]# ansible localhost -i real_hosts.ini -m copy -a "content='example content' dest=/tmp/destfile"
localhost | SUCCESS => {
    "changed": false,
    "checksum": "0ff30941ca5acd879fd809e8c937d9f9e6dd1615",
    "dest": "/tmp/destfile",
    "gid": 0,
    "group": "root",
    "mode": "0644",
    "owner": "root",
    "path": "/tmp/destfile",
    "secontext": "unconfined_u:object_r:admin_home_t:s0",
    "size": 15,
    "state": "file",
    "uid": 0
}
[root@control-node ~]# ansible localhost -i real_hosts.ini -m copy -a "content='example content 1' dest=/tmp/destfile"
localhost | CHANGED => {
    "changed": true,
    "checksum": "fb51fdd844a4a6078c784fa041d635c8e8bbb0e0",
    "dest": "/tmp/destfile",
    "gid": 0,
    "group": "root",
    "md5sum": "1b14fe6739b4279b160c185ff4172a7f",
    "mode": "0644",
    "owner": "root",
    "secontext": "unconfined_u:object_r:admin_home_t:s0",
    "size": 17,
    "src": "/root/.ansible/tmp/ansible-tmp-1749026407.1880226-43036-64085488947257/.source",
    "state": "file",
    "uid": 0
}
[root@control-node ~]# ansible localhost -i real_hosts.ini -m copy -a "content='example content 1' dest=/tmp/destfile force=false"
localhost | SUCCESS => {
    "changed": false,
    "dest": "/tmp/destfile",
    "src": "/root/.ansible/tmp/ansible-local-43060stucm9hf/.aow_m5cz"
}
[root@control-node ~]# ansible localhost -i real_hosts.ini -m copy -a "content='example content 2' dest=/tmp/destfile force=false"
localhost | SUCCESS => {
    "changed": false,
    "dest": "/tmp/destfile",
    "src": "/root/.ansible/tmp/ansible-local-430800aym92z2/.ihfr6ysn"
}
```



- **ansible.builtin.file**
  - 管理文件以及属性
  - 常用选项

| 选项                                                | 作用                 | 值                                                           |
| --------------------------------------------------- | -------------------- | ------------------------------------------------------------ |
| path/dest/name                                      | 指定文件路径         | 文件路径                                                     |
| mode, owner, group, setype, seuser, serole, selevel | 文件权限             | 字符串                                                       |
| state                                               | 声明文件管理操作类型 | 枚举   **absent 删除文件**，类似与rm ; **directory 目录管理** 类似 mkdir ； **file 文件管理**，如果path指定的文件不存在，这个文件不会被创建，如果文件存在，且没有规定其他选项，返回文件基本信息，如果规定了其他选项，比如说文件权限，那么会修改文件的权限；**touch 新建文件**，如果文件存在，更新文件时间戳，如果文件不存在，则新建文件。**hard 创建硬链接文件**；**link 符号链接文件的创建** |

```bash
[root@control-node ~]# ansible localhost -i real_hosts.ini -m file -a 'path=/tmp/ansible                     src=/root/blockfile state=hard'
localhost | CHANGED => {
    "changed": true,
    "dest": "/tmp/ansible",
    "gid": 0,
    "group": "root",
    "mode": "0644",
    "owner": "root",
    "secontext": "unconfined_u:object_r:admin_home_t:s0",
    "size": 286,
    "src": "/root/blockfile",
    "state": "hard",
    "uid": 0
}
[root@control-node ~]# ls /tmp/ansible 
/tmp/ansible
```
