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

- ansible.builtin.replace 
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
