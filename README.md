## zabbix 4.0

#### 安装Zabbix

**准备yum源**

```
~]# rpm -ivh https://mirror.tuna.tsinghua.edu.cn/zabbix/zabbix/4.0/rhel/7/x86_64/zabbix-release-4.0-1.el7.noarch.rpm
~]# yum-config-manager --enable rhel-7-server-optional-rpms
```

**安装程序包**

```
~]# yum install zabbix-server-mysql zabbix-agent zabbix-sender zabbix-get zabbix-web-mysql
```

**初始化数据库**

```
~]# yum install -y mariadb-server
~]# vim /etc/my.cnf
skip_name_resolve=on
innodb_file_per_table=on
~]# systemctl  start mariadb
~]# mysql_secure_installation 
~]# mysql -u root -p'root';
>  create database zabbix character set utf8 collate utf8_bin;
> create user 'zabbix'@'localhost' identified by 'zabbix';
> grant all privileges on zabbix.* to 'zabbix'@'localhost';
> grant all privileges on zabbix.* to 'zabbix'@'127.0.0.1';
> flush privileges;
```
```
~]# zcat /usr/share/doc/zabbix-server-mysql*/create.sql.gz | mysql -uzabbix -p'zabbix' zabbix
```

**配置zabbix Server**

```
~]# vim /etc/zabbix/zabbix_server.conf
DBHost=localhost
DBName=zabbix
DBUser=zabbix
DBPassword=zabbix
~]#  service zabbix-server start && systemctl  enable zabbix-server
```

**配置Zabbix前端**

```
~]# vim /etc/httpd/conf.d/zabbix.conf
  ...
    <IfModule mod_php5.c>
        ...
        php_value date.timezone Asia/Shanghai
    </IfModule>
  ...
~]# systemctl  start httpd
```

访问：`http://192.168.40.128//zabbix/setup.php`

**配置Zabbix-agent**

```
~]# vim /etc/zabbix/zabbix_agentd.conf 
Server=92.168.60.128
~]# systemctl  start zabbix-agent && systemctl enable zabbix-agent
```

