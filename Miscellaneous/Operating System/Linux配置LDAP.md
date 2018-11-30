### 安装LDAP服务
* **安装OpenLDAP**

```
yum install -y openldap.x86_64 openldap-clients.x86_64 openldap-servers.x86_64 openldap-devel.x86_64 migrationtools.noarch compat-openldap.x86_64
```

* **配置LDAP Server**

```
vim /etc/openldap/slapd.d/cn\=config/olcDatabase\=\{2\}hdb.ldif
# 修改第8、9行，添加第10行
```

```
olcSuffix: dc=bloodzer0,dc=com
olcRootDN: cn=Manager,dc=bloodzer0,dc=com
olcRootPW: bloodzer0
```

![ldap-1](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Miscellaneous/Operating%20System/img/ldap-1.png)


* **配置Monitoring Database**

```
vim /etc/openldap/slapd.d/cn\=config/olcDatabase\=\{1\}monitor.ldif
# 修改第7行
```

```
olcAccess: {0}to * by dn.base="gidNumber=0+uidNumber=0,cn=peercred,cn=extern
 al,cn=auth" read by dn.base="cn=Manager,dc=bloodzer0,dc=com" read by * none
```

![ldap-2](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Miscellaneous/Operating%20System/img/ldap-2.png)

* **配置LDAP数据库**

```
cp /usr/share/openldap-servers/DB_CONFIG.example /var/lib/ldap/DB_CONFIG
chown -R ldap.ldap /var/lib/ldap/
```

* **测试并启动服务**

```
slaptest -u
```

![ldap-3](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Miscellaneous/Operating%20System/img/ldap-3.png)

```
systemctl enable slapd.service
systemctl start slapd.service
```

* **导入Schemas**


```
ldapadd -Y EXTERNAL -H ldapi:/// -D "cn=config" -f /etc/openldap/schema/cosine.ldif 
ldapadd -Y EXTERNAL -H ldapi:/// -D "cn=config" -f /etc/openldap/schema/nis.ldif 
ldapadd -Y EXTERNAL -H ldapi:/// -D "cn=config" -f /etc/openldap/schema/collective.ldif 
ldapadd -Y EXTERNAL -H ldapi:/// -D "cn=config" -f /etc/openldap/schema/corba.ldif 
ldapadd -Y EXTERNAL -H ldapi:/// -D "cn=config" -f /etc/openldap/schema/core.ldif 
ldapadd -Y EXTERNAL -H ldapi:/// -D "cn=config" -f /etc/openldap/schema/duaconf.ldif 
ldapadd -Y EXTERNAL -H ldapi:/// -D "cn=config" -f /etc/openldap/schema/dyngroup.ldif 
ldapadd -Y EXTERNAL -H ldapi:/// -D "cn=config" -f /etc/openldap/schema/inetorgperson.ldif 
ldapadd -Y EXTERNAL -H ldapi:/// -D "cn=config" -f /etc/openldap/schema/java.ldif 
ldapadd -Y EXTERNAL -H ldapi:/// -D "cn=config" -f /etc/openldap/schema/misc.ldif 
ldapadd -Y EXTERNAL -H ldapi:/// -D "cn=config" -f /etc/openldap/schema/openldap.ldif 
ldapadd -Y EXTERNAL -H ldapi:/// -D "cn=config" -f /etc/openldap/schema/pmi.ldif 
ldapadd -Y EXTERNAL -H ldapi:/// -D "cn=config" -f /etc/openldap/schema/ppolicy.ldif
```

* **配置basedomain文件**

```
vim /usr/share/migrationtools/migrate_common.ph
# 修改第71、74、90行
```

```
$DEFAULT_MAIL_DOMAIN = "bloodzer0.com";
$DEFAULT_BASE = "dc=bloodzer0,dc=com";
$EXTENDED_SCHEMA = 1;
```

![ldap-4](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Miscellaneous/Operating%20System/img/ldap-4.png)

```
/usr/share/migrationtools/migrate_base.pl > /etc/openldap/basedomain.ldif

# 导入文件
ldapadd -x -D cn=Manager,dc=bloodzer0,dc=com -W -f /etc/openldap/basedomain.ldif
# 输入之前配置olcRootPW密码
```

![ldap-5](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Miscellaneous/Operating%20System/img/ldap-5.png)

### 安装phpldapadmin
* **安装phpldapadmin**

```
yum install epel-release -y
yum install phpldapadmin.noarch -y

systemctl enable httpd.service
systemctl start httpd.service
```

* **修改配置**

```
# 修改httpd配置
vim /etc/httpd/conf.d/phpldapadmin.conf
# 添加Require all granted
# 重启服务
# 访问：http://192.168.199.10/phpldapadmin/cmd.php
# 此时是不能登录的
```

![ldap-6](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Miscellaneous/Operating%20System/img/ldap-6.png)

```
# 修改phpldapadmin配置
vim /etc/phpldapadmin/config.php
# 注释398，取消注释397
```

![ldap-7](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Miscellaneous/Operating%20System/img/ldap-7.png)

```
# 登录
# 用户名：cn=Manager,dc=bloodzer0,dc=com 密码：bloodzer0
```

![ldap-8](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Miscellaneous/Operating%20System/img/ldap-8.png)

### 配置用户
* **创建新条目**

```
# 需要先创建一个Generic: Posix Group

# 创建一个Generic: User Account
```

![ldap-9](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Miscellaneous/Operating%20System/img/ldap-9.png)


### 踩坑
* LDAP+phpldapadmin 是很麻烦的方式，推荐使用freeipa，下一篇文章将讲freeipa安装与使用。
