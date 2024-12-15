FROM ubuntu:20.04

# 安装 SSH 服务
RUN apt-get update && apt-get install -y openssh-server && mkdir -p /run/sshd

# 添加一个测试用户
RUN useradd -m -s /bin/bash testuser && echo "testuser:testpassword" | chpasswd

# 启动 SSH 服务
CMD ["/usr/sbin/sshd", "-D"]