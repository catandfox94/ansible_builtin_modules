### ansible.builtin

- ansible.builtin.get_url    （wget）
  - 从http/https/ftp 的服务节点下载文件：
  - 常用选项：

| 选项                     | 作用                                          | 值                                                           |
| ------------------------ | --------------------------------------------- | ------------------------------------------------------------ |
| cipher                   | 指定SSL/TLS 请求使用的加密方式                | 不同的加密方法使用： 分隔     目前节点支持的加密方式以节点的ssl版本信息为准 |
| client_cert 、client_key | 指定客户端证书/密钥                           | 文件路径                                                     |
| decompresss              | 对于使用gzip压缩的响应主题是否解压            | 布尔值，false 不进行解压； true(默认) 进行解压               |
| dest【必需】             | 下载文件的保存路径                            | 文件路径                                                     |
| force_basic_auth         | 是否强制执行http协议支持的basic auth 身份认证 | 布尔值，false(默认)不进行身份认证，true 进行身份认证         |
| headers                  | 规定请求头部信息                              | 使用hash/dict 传递请求头信息                                 |
| timeout                  | 超时时间                                      | URL 请求的超时时间                                           |
| url 【必需】             | 数据下载地址                                  | url地址                                                      |
| validate_certs           | 是否进行SSL证书的校验                         | 布尔值，false不进行校验，即忽略风险提示；true(默认)进行校验  |

用法示例： 下载nginx安装包
```
[root@control-node ~]# ansible localhost -i real_hosts.ini -m get_url  -a 'url=https://nginx.org/download/nginx-1.27.5.tar.gz dest=/usr/src'
localhost | CHANGED => {
    "changed": true,
    "checksum_dest": null,
    "checksum_src": "8aa96ecdc54f74ecdc2cee88331da47b16e3b9cd",
    "dest": "/usr/src/nginx-1.27.5.tar.gz",
    "elapsed": 29,
    "gid": 0,
    "group": "root",
    "md5sum": "ecb21eeb9a1586add94931389bc224bd",
    "mode": "0644",
    "msg": "OK (1279891 bytes)",
    "owner": "root",
    "secontext": "system_u:object_r:usr_t:s0",
    "size": 1279891,
    "src": "/root/.ansible/tmp/ansible-tmp-1749171177.422976-2271-107957676353621/tmp2xq4u6t5",
    "state": "file",
    "status_code": 200,
    "uid": 0,
    "url": "https://nginx.org/download/nginx-1.27.5.tar.gz"
}
[root@control-node ~]# ls /usr/src/nginx-1.27.5.tar.gz 
/usr/src/nginx-1.27.5.tar.gz

```

