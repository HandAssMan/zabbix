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
3. Create database and users for backend and frontend servers authorization 
  - Enter the MySQL
    ```
    mysql
    ```
  - Create database and users
    ```
    create database zabbix character set utf8mb4 collate utf8mb4_bin;
    create user zabbix_node1@ip_node1 identified by 'password';
    grant all privileges on zabbix.* to zabbix@ip_node1;
    create user zabbix_node2@ip_node2 identified by 'password';
    grant all privileges on zabbix.* to zabbix@ip_node2;
    create user zabbix_web@ip_web identified by 'password';
    grant all privileges on zabbix.* to zabbix@ip_web;
    ```
  - Exit from MySQL
    ```
    exit
    ```
4. Install zabbix-sql-scripts and zabbix-agent2
  ```
  rpm -Uvh https://repo.zabbix.com/zabbix/6.0/rhel/8/x86_64/zabbix-release-6.0-1.el8.noarch.rpm
  dnf clean all
  yum makecache
  dnf install zabbix-sql-scripts zabbix-agent2
  zcat /usr/share/doc/zabbix-sql-scripts/mysql/server.sql.gz | mysql zabbix
  systemctl restart mysqld.service
  ```
5. Add rules for firewall-cmd
  ```
  firewall-cmd --zone=public --add-service=mysql --permanent
  firewall-cmd --zone=public --add-port=3306/tcp --permanent
  firewall-cmd --zone=public --add-port=4567/tcp --permanent
  firewall-cmd --zone=public --add-port=4568/tcp --permanent
  firewall-cmd --zone=public --add-port=4444/tcp --permanent
  firewall-cmd --add-service={http,https} --permanent
  firewall-cmd --add-port={10051/tcp,10050/tcp} --permanent
  firewall-cmd --reload
  ```
6. Edit zabbix_agent2 config
    - open config file
      ```
      nano /etc/zabbix/zabbix_agent2.conf
      ```
    - edit next lines
      ```
      Server=**ip_node1,ip_node1**
      ServerActive=**ip_node1;ip_node1**
      Hostname=**hostname_database_server**
      ```
7. Restart service and reboot server
  ```
  systemctl restart zabbix-agent2
  reboot
  ```
