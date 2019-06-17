# centos 7+ (阿里云环境)

1. 安装依赖包

    `yum install wget curl curl-devel zlib-devel openssl-devel perl perl-devel cpio expat-devel gettext-devel git autoconf libtool gcc swig python-devel`

    _setuptools（如果python需要此依赖）_

    `cd /usr/local/src/`

    `wget --no-check-certificate  https://pypi.python.org/packages/source/s/setuptools/setuptools-19.6.tar.gz#md5=c607dd118eae682c44ed146367a17e26`

    `tar -zvxf setuptools-19.6.tar.gz`

    `python2.7 setup.py build`

    _zlib-devel（如果失败，提示说需要zlib-devel依赖，RuntimeError: Compression requires the (missing) zlib module）_

    `yum install -y zlib-devel`

    `python2.7 setup.py build`

    `python2.7  setup.py install`

2. 更新pip

    `pip install --upgrade pip`

    _一般cent7自带pip，但版本不一定是最新，可重新安装一次_

    `yum -y install epel-release`

    `yum -y install pip python-pip`
    
    _如果epel安装成功但pip依然安装失败, 则说明epel工具没有开启, 再重新执行pip的安装过程_
    
    `yum install -y yum-utils`

    `yum-config-manager --enable epel`

3. 安装ss

    `pip install shadowsocks`

    _安装加密的依赖包_

    `pip install M2Crypto`

4. 创建配置文件

    `nano /etc/shadowsocks.json`

    ```
    {
    "server": "172.21.166.194",
    "server_port": 8388,
    "password": "AS3e123jK",
    "timeout": 30,
    "method": "aes-256-cfb"
    }
    ```

    `server` : 专用网络下，这里只能写私网ip，不能写外网ip，否则启动失败！或者写0.0.0.0

    `server_port` : ss服务器的端口,默认是8388端口

    `password` : ss服务器密码

    `method` : ss服务器配置的加密方式

    _也可以进行多用户设置_

    ```
    {
    "server": "172.21.166.194",
    "port_password": {
    "8388": "AS3e123jK",
    "8389": "wang2",
    "8390": "zhang3"
    },
    "timeout":30,
    "method":"aes-256-cfb",
    "fast_open": false
    }
    ```

5. 创建服务文件

    `nano /etc/systemd/system/ssserver.service`

    ```
    [Unit]
    Description=ssserver
    [Service]
    TimeoutStartSec=0
    ExecStart=/usr/bin/ssserver -c /etc/shadowsocks.json
    [Install]
    WantedBy=multi-user.target
    ```

6. 启动服务

    `systemctl enable ssserver`

    `systemctl start ssserver`

7. 查看服务

    `systemctl status ssserver -l`

    `lsof -i:8388`

8. 安装客户端

    [GIT地址](https://github.com/shadowsocks/shadowsocks-windows)

    [下载地址](https://github.com/shadowsocks/shadowsocks-windows/releases)

    [中文说明文档](https://github.com/shadowsocks/shadowsocks-windows/wiki/Shadowsocks-Windows-%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E)
