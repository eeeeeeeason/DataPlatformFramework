### linux环境
- docker pull centos
- docker images 查看对应centos容器的imgId
- docker run -d -i -t imgId 后台运行
- docker attach imgId 进入对应容器
- yum install net-tools.x86_64 连ifconfig都没有，安装工具包
