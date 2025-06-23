**yum_repository**: 管理yum镜像仓库(常用选项注意和yum仓库配置文件的配置项对应)

- 功能接近于系统管理命令 yum-config-manager
- baseurl  仓库地址  【baseurl】
- enabled   仓库是否启用 ，值为布尔值  【enabled】
- gpgcheck     是否启用密钥检查，布尔值   【gpgcheck】
- gpgkey         设定gpgcheck地址  【gpgkey】
- mirrorlist      指定一个仓库地址列表
- name             指定的是仓库的ID 值为字符串         【 [id]  】
- description   仓库的描述信息                              【name】
- file                   仓库配置文件名                  file=a      配置会写入a.repo， 如果没有规定file，则常见name同名文件保存
- state              设置仓库配置文件是否保留 值为枚举类型：present（保留） | absent（不保留）

  
```yaml
- name: 添加yum 软件仓库源
  ansible.builtin.yum_repository:
    name: epel
    description: EPEL YUM repo
    baseurl: https://download.fedoraproject.org/pub/epel/$releasever/$basearch/

- name: 在同一个仓库配置文件中设置多个软件仓库源 (1/2)
  ansible.builtin.yum_repository:
    name: epel
    description: EPEL YUM repo
    file: external_repos
    baseurl: https://download.fedoraproject.org/pub/epel/$releasever/$basearch/
    gpgcheck: no

- name: 在同一个仓库配置文件中设置多个软件仓库源 (2/2)
  ansible.builtin.yum_repository:
    name: rpmforge
    description: RPMforge YUM repo
    file: external_repos
    baseurl: http://apt.sw.be/redhat/el7/en/$basearch/rpmforge
    mirrorlist: http://mirrorlist.repoforge.org/el7/mirrors-rpmforge
    enabled: no

# 使用处理程序清理仓库缓存
- name: yum-clean-metadata
  ansible.builtin.command: yum clean metadata

# Example removing a repository and cleaning up metadata cache
- name: 移除仓库，并使用处理程序清理缓存
  ansible.builtin.yum_repository:
    name: epel
    state: absent
  notify: yum-clean-metadata

- name: 从指定的配置文件移除仓库
  ansible.builtin.yum_repository:
    name: epel
    file: external_repos
    state: absent

```
