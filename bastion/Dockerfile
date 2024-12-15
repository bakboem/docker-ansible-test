FROM ubuntu:20.04

# 安装必要工具和 SSH 服务
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y \
    openssh-server sshpass python3 python3-pip sudo software-properties-common && \
    mkdir -p /run/sshd && apt-get clean

# 添加 Ansible 的官方 PPA 并安装 Ansible
RUN apt-add-repository --yes --update ppa:ansible/ansible && \
    apt-get install -y ansible

# 创建 SSH 用户
RUN useradd -m -d /home/instance_user -s /bin/bash instance_user && \
    echo "instance_user:instance_password" | chpasswd && \
    mkdir -p /home/instance_user/.ssh && \
    touch /home/instance_user/.ssh/authorized_keys && \
    chmod 700 /home/instance_user/.ssh && \
    chmod 600 /home/instance_user/.ssh/authorized_keys && \
    chown -R instance_user:instance_user /home/instance_user/.ssh

# 允许密码登录
RUN sed -i 's/^#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config && \
    sed -i 's/^PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config

# 生成 SSH 主机密钥
RUN ssh-keygen -A

# 生成 SSH 密钥对（私钥和公钥）
RUN mkdir -p /root/.ssh && \
    ssh-keygen -t rsa -b 2048 -f /root/.ssh/id_rsa -q -N "" && \
    chmod 600 /root/.ssh/id_rsa && \
    chmod 644 /root/.ssh/id_rsa.pub

# 将公钥保存到一个共享目录，方便其他容器使用
RUN mkdir -p /shared && cp /root/.ssh/id_rsa.pub /shared/id_rsa.pub

# 写入 Ansible Inventory 文件
RUN mkdir -p /etc/ansible && \
    echo "[auth_instances]" > /etc/ansible/hosts && \
    echo "auth_instance_01 ansible_host=auth_instance_01 ansible_user=instance_user" >> /etc/ansible/hosts && \
    echo "auth_instance_02 ansible_host=auth_instance_02 ansible_user=instance_user" >> /etc/ansible/hosts && \
    echo "" >> /etc/ansible/hosts && \
    echo "[api_instances]" >> /etc/ansible/hosts && \
    echo "api_instance_01 ansible_host=api_instance_01 ansible_user=instance_user" >> /etc/ansible/hosts && \
    echo "api_instance_02 ansible_host=api_instance_02 ansible_user=instance_user" >> /etc/ansible/hosts

# 启动 SSH 服务
EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]
