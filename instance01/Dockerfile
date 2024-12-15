FROM ubuntu:20.04

# 无提示安装 SSH 服务和必要工具
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y \
    openssh-server python3 sudo && \
    mkdir -p /run/sshd && \
    apt-get clean

# 创建 SSH 用户和密码
RUN useradd -m -s /bin/bash instance_user && echo "instance_user:instance_password" | chpasswd

# 初始化 SSH 并设置权限
RUN mkdir -p /home/instance_user/.ssh && \
    chmod 700 /home/instance_user/.ssh && \
    touch /home/instance_user/.ssh/authorized_keys && \
    chmod 600 /home/instance_user/.ssh/authorized_keys && \
    chown -R instance_user:instance_user /home/instance_user/.ssh

RUN echo "instance_user ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/instance_user && \
    chmod 440 /etc/sudoers.d/instance_user
    
# 允许 Root 和普通用户 SSH 登录
RUN sed -i 's/^#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config && \
    sed -i 's/^#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config && \
    sed -i 's/^PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config

# 生成 SSH 主机密钥
RUN ssh-keygen -A

# 启用 SSH 服务
EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]
