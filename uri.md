- ansible.builtin.uri
  - 和HTTP 以及HTTPS服务器进行交互，并支持digest 、basic 和  WSSE 三种基础的身份认证
  - 常见选项

| 选项                          | 作用                                              | 值                                                           |
| ----------------------------- | ------------------------------------------------- | ------------------------------------------------------------ |
| body                          | 指定给HTTP服务器的响应或者请求报文的主体部分      | any                                                          |
| body_format                   | 指定主体数据格式                                  | 枚举类型，raw（原生数据）；json  支持和解析相对较好； form-multipart  表单支持； format-urlencoded |
| dest                          | 指定地址                                          | 下载文件的保存路径                                           |
| return_content                | 是否将响应报文的主体，注册为一个名为content的变量 | 布尔值；false(默认) 不保存相应主体； true 使用变量保存响应主体数据 |
| status_code                   | 响应状态码列表                                    | 指定请求成功的状态码列表                                     |
| url                           | http/https的访问地址                              | 格式为 http/https://server域名[:通信端口]/资源路径           |
| url_pasword    / url_username | 提供web 服务器基础身份验证信息                    | 分别对应用户名和密码                                         |

```bash
[root@control-node ~]# ansible localhost -i real_hosts.ini  -m uri -a \
> 'url=https://www.baidu.com'
localhost | SUCCESS => {
    "accept_ranges": "bytes",
    "cache_control": "no-cache",
    "changed": false,
    "connection": "close",
    "content_length": "29506",
    "content_type": "text/html",
    "cookies": {
        "BAIDUID": "61F2CBE2E94C8C8D5A88F5A36C2E8A0E:FG=1",
        "BIDUPSID": "61F2CBE2E94C8C8DFF1AA2F713FCAE3D",
        "PSTM": "1749173415"
    },
    "cookies_string": "BAIDUID=61F2CBE2E94C8C8D5A88F5A36C2E8A0E:FG=1; BIDUPSID=61F2CBE2E94C8C8DFF1AA2F713FCAE3D; PSTM=1749173415",
    "date": "Fri, 06 Jun 2025 01:30:15 GMT",
    "elapsed": 0,
    "msg": "OK (29506 bytes)",
    "p3p": "CP=\" OTI DSP COR IVA OUR IND COM \", CP=\" OTI DSP COR IVA OUR IND COM \"",
    "pragma": "no-cache",
    "redirected": false,
    "server": "BWS/1.1",
    "set_cookie": "BAIDUID=61F2CBE2E94C8C8DFF1AA2F713FCAE3D:FG=1; expires=Thu, 31-Dec-37 23:55:55 GMT; max-age=2147483647; path=/; domain=.baidu.com, BIDUPSID=61F2CBE2E94C8C8DFF1AA2F713FCAE3D; expires=Thu, 31-Dec-37 23:55:55 GMT; max-age=2147483647; path=/; domain=.baidu.com, PSTM=1749173415; expires=Thu, 31-Dec-37 23:55:55 GMT; max-age=2147483647; path=/; domain=.baidu.com, BAIDUID=61F2CBE2E94C8C8D5A88F5A36C2E8A0E:FG=1; max-age=31536000; expires=Sat, 06-Jun-26 01:30:15 GMT; domain=.baidu.com; path=/; version=1; comment=bd",
    "status": 200,
    "traceid": "174917341528491701868725093484821294506",
    "url": "https://www.baidu.com",
    "vary": "Accept-Encoding",
    "x_ua_compatible": "IE=Edge,chrome=1",
    "x_xss_protection": "1;mode=block"

```
