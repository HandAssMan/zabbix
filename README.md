# Zabbix cluster installation (CentOS_8_Stream, Zabbix_6, MySQL)

## MYSQL server
1. Update server, disable SELinux and install "nano" (optional)
  ```
  dnf update -y
  sed -i 's/^SELINUX=.*/SELINUX=disabled/g' /etc/selinux/config
  dnf install nano
  ```
2. Install MySQL-server and start service
  ```
  dnf install mysql-server
  systemctl start mysqld.service
  ```
