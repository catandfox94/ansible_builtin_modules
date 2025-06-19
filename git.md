ansible.builtin.git
  - 从git仓库中管理git 的检出（checkout），从而实现文件和软件的部署
  - 常用选项

| 选项      | 作用                  | 值                                                           |
| --------- | --------------------- | ------------------------------------------------------------ |
| archive   | 将指定的资源打包      | 压缩文件（归档文件包）的路径                                 |
| bare      | 是否创建空仓库        | 布尔值 false(默认)创建仓库的基本结构，true 仅创建空目录，不需要创建基本git仓库结构 |
| clone     | 是否进行仓库的克隆    | 布尔值； false 不进行仓库数据的同步，true【默认】 进行从仓库数据的同步 |
| dest/path | 本地文件保存路径      | 文件路径                                                     |
| remote    | 远程仓库的分支指定    | 默认为origin                                                 |
| repo/name | 指定远程仓库名称/地址 | 基于ssh或者http协议的git仓库地址                             |
| version   | 指定检出版本          | 字符串， 根据实际仓库的版本支持                              |

演示如下：
```bash
[root@control-node ~]# ansible localhost -i real_hosts.ini  -m git -a 'repo=https://github.com/ansible/ansible.git dest=/usr/src/ansible'
localhost | CHANGED => {
    "after": "43c0132caa1859fec830f26812cb895dfa96609b",
    "before": null,
    "changed": true
}
[root@control-node ~]# ls /usr/src/ansible/
bin  changelogs  COPYING  hacking  lib  licenses  MANIFEST.in  packaging  pyproject.toml  README.md  requirements.txt  test
