**dnf|dnf5|yum:**   rhel 相关发行版的操作系统的软件包管理

- 作用类似**dnf | yum** 
- 常用参数整理：
  - name: 指定需要安装的软件包
  - state:  指定对应的管理操作
    - adsent： 移除某个包
    - present:  安装某个包，如果包已经安装则不执行任何操作
    - installed:  和present 作用一致
    - removed： 和absent 作用一致
    - latest： 更新某个软件包，如果指定软件包未安装则安装最新版，如果已经安装但是不是最新版，则执行更新
```yaml
- name: 安装最新版httpd
  ansible.builtin.dnf:
    name: httpd
    state: latest


- name: 卸载httpd
  ansible.builtin.dnf:
    name: httpd
    state: absent



- name: 从testing仓库安装httpd
  ansible.builtin.dnf:
    name: httpd
    enablerepo: testing

```
