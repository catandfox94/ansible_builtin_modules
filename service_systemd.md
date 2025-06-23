### 控制系统服务的状态  

- ansible.builtin.service
  - 选项：
    - name： 服务名
    - state:    服务的状态
      - started
      - stopped
      - restarted
      - reloaded
    - enabled： 是否设置服务开机自启
      - false: 不开机自启 默认值
      - true： 启用开机自启

```yaml
- name: 启动httpd服务
  ansible.builtin.service:
    name: httpd
    state: started

- name: 停止httpd 服务
  ansible.builtin.service:
    name: httpd
    state: stopped

- name: 任何情况下重启httpd 服务
  ansible.builtin.service:
    name: httpd
    state: restarted

- name: 任何情况下httpd 服务
  ansible.builtin.service:
    name: httpd
    state: reloaded

- name: httpd 添加开机自启，但是管理服务的启动状态
  ansible.builtin.service:
    name: httpd
    enabled: yes

```

- **ansible.builtin.systemd_service**
  - 基于systemd管理系统进程与服务
  - 从传统的systemd 模块重命名
  - 选项： name state  enabled 和service 模块用法一致
    - daemon_reload：
      - false: 不加载单元变换
      - true : 加载单元变化
    - maksed:  屏蔽手动管理
      - false
      - true

```yaml
- name: 确定指定服务运行
  ansible.builtin.systemd_service:
    state: started
    name: httpd

- name: 确保指定服务停止运行
  ansible.builtin.systemd_service:
    name: cron
    state: stopped

- name: 重启 cronded 服务， 并重新加载单元配置变更   
  ansible.builtin.systemd_service:
    state: restarted
    daemon_reload: true
    name: crond

- name: 重新加载systemd服务配置变更
  ansible.builtin.systemd_service:
    name: httpd.service
    state: reloaded

```
