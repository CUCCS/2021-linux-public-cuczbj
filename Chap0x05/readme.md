# 第五章：WEB服务器实验
## 一、软件环境
* 宿主机：
	* 操作系统：Windows 10
	* IP地址：192.168.1.104
* 服务器虚拟机：
	* 操作系统：Ubuntu 20.04 Server AMD64
	* IP地址：192.168.1.130
* 客户端虚拟机：
	* 操作系统：Red Hat Enterprise Linux X64
	* IP地址：192.168.1.140
* 实验软件：
	* Nginx
	* VeryNginx
	* WordPress 4.7
	* Damn Vulnerable Web Application

## 二、实验过程
### 1. 基本要求
####1.1. 在一台主机上同时配置Nginx和VeryNginx
* Nginx的安装直接使用apt-get完成

`sudo apt-get install nginx`

* VeryNginx的安装通过源码编译的方式完成
```
# 安装编译环境
sudo apt-get install gcc make zlib1g-dev
# 安装必要的依赖库
sudo apt-get install libssl-dev libpcre-dev
# 克隆VeryNginx仓库到本地
git clone https://github.com/alexazhou/VeryNginx.git
# 停用Nginx避免端口冲突
sudo nginx -s stop
# 修改nginx配置，改用www-data用户，监听端口改成8080端口
cd VeryNginx
vi nginx.conf
# 执行安装程序，开始编译安装
sudo python install.py install
# 创建软链接
sudo ln -s /opt/verynginx/openresty/nginx/sbin/nginx /usr/sbin/verynginx
# 启动VeryNginx
sudo verynginx
```

* 通过宿主机浏览器访问页面，测试安装是否成功

![OpenResty欢迎页面](images/OpenResty_welcome.jpg)

![VeryNginx登录页面](images/verynginx_login.jpg)

* 使用默认用户登录VeryNginx

![VeryNginx首页](images/verynginx_index.jpg)

#### 1.2. 使用Wordpress搭建站点
* Wordpress需要与PHP和MySQL搭配使用，首先安装PHP和MySQL
```
# 安装PHP和MySQL
sudo apt-get install php mysql-server php-mysql
# 下载Wordpress源码
sudo wget https://wordpress.org/wordpress-4.7.tar.gz
# 解压源码
tar xvf wordpress-4.7.tar.gz
# 在/var/www/下创建站点文件夹
sudo mkdir -p /var/www/wordpress/pub
# 将Wordpress的文件复制到站点文件夹下
sudo cp -r wordpress/* /var/www/wordpress/pub/
```
* 为Wordpress配置MySQL数据库
```
CREATE USER 'Wordpress'@'localhost' IDENTIFIED BY 'Wordpress';
CREATE DATABASE wp_database;
GRANT ALL ON wp_database.* TO 'Wordpress'@'localhost';
```
* 配置Wordpress连接MySQL的设置
```
cd /var/www/wordpress/pub
sudo cp wp-config-sample.php wp-config.php
sudo vi wp-config.php
```
![WordPress连接设置](images/Wordpress_config.jpg)

* 配置Wordpress连接Nginx的设置
```
sudo chown -R www-data /var/www/wordpress/
cd /etc/nginx/sites-available
# 使用默认模板来修改
sudo mv default wp.sec.cuc.edu.cn
sudo vi wp.sec.cuc.edu.cn
sudo ln -s /etc/nginx/sites-available/wp.sec.cuc.edu.cn /etc/nginx/sites-enabled/
sudo rm -rf ../sites-enabled/dafault
```
![Ngnix配置](images/wp_ngnix_config.jpg)
* 配置VeryNginx，将监听端口恢复为80，添加访问匹配规则

![VeryNgnix配置](images/verynginx_matcher.jpg)

* 在VeryNginx上配置反向代理

![VeryNgnix上行数据流](images/verynginx_upstream.jpg)

![VeryNgnix反向代理](images/verynginx_proxy_pass.jpg)

* 在Windows系统配置hosts文件
`192.168.1.130 wp.sec.cuc.edu.cn`

* 使用浏览器通过域名测试访问

![域名访问测试](images/wp_access_test.jpg)

#### 1.3. 使用Damn Vulnerable Web Application搭建站点
* DVWA也是基于PHP和MySQL环境，首先配置数据库
```
CREATE USER 'dvwa'@'localhost' IDENTIFIED BY 'dvwa';
CREATE DATABASE dvwa;
GRANT ALL ON dvwa.* TO 'dvwa'@'localhost';
```
* 从github下载源码安装
```
# 下载源码
git clone https://github.com/digininja/DVWA.git
# 下载完毕后修改数据库连接设置
mv DVWA/config/config.inc.php.dist DVWA/config/config.inc.php
vi DVWA/config/config.inc.php
```

![配置DVWA的MySQL设置](images/dvwa_config.jpg)

* 将DVWA部署到/var/www/下
```
sudo mkdir -p /var/www/dvwa/pub
sudo cp -r ./DVWA/* /var/www/dvwa/pub
sudo chown -R www-data /var/www/dvwa/
# 配置Nginx
cd /etc/nginx/sites-available
sudo cp wp.sec.cuc.edu.cn dvwa.sec.cuc.edu.cn
# 修改配置文件
sudo vi dvwa.sec.cuc.edu.cn
sudo ln -s /etc/nginx/sites-available/dvwa.sec.cuc.edu.cn /etc/nginx/sites-enabled/
sudo nginx -s reload
```

![配置DVWA的Nginx设置](images/dvwa_nginx.jpg)

* 在VeryNginx上配置监听规则

![配置DVWA的Matcher设置](images/dvwa_matcher.jpg)

* 在VeryNginx上配置反向代理

![配置DVWA的上行数据流设置](images/dvwa_upstream.jpg)

![配置DVWA的反向代理设置](images/dvwa_pass_proxy.jpg)

* 在Windows系统hosts文件添加条目
`192.168.1.130 dvwa.sec.cuc.edu.cn`

* 使用浏览器通过域名测试访问

![DVWA域名访问测试](images/dvwa_access_test.jpg)

### 2. 安全加固要求
#### 2.1 禁止使用IP地址的方式访问站点
* 首先设置Matcher监听目标地址为IP的请求

![IP Matcher](images/ip_matcher.jpg)

* 建立友好的错误提示信息页面-1

![错误页面1](images/ip_response.jpg)

* 创建filter拦截访问请求并返回提示信息

![IP拦截](images/ip_filter.jpg)

* 测试使用IP直接访问

![IP访问](images/ip_access_test.jpg)


#### 2.2 DVWA只允许白名单IP访问
* 首先设置Matcher监听访问DVWA且不在白名单的请求

![white matcher](images/not_white_matcher.jpg)

* 建立友好的错误提示信息页面-2

![错误页面2](images/deny_dvwa_access.jpg)

* 创建filter拦截不在白名单的请求并返回提示信息

![非白名单拦截](images/deny_dvwa_filter.jpg)

* 测试白名单IP访问

![白名单访问](images/white_access.jpg)

* 测试非白名单IP访问

![非白名单访问](images/not_white_access.jpg)

#### 2.3 热修复WordPress < 4.7.1 - Username Enumeration
* Username Enumeration漏洞是通过REST API实现的，需要通过禁用该API修复漏洞

![Username matcher](images/username_enum.jpg)

* 创建filter拦截请求并返回错误提示

![Username filter](images/username_enum_filter.jpg)

* 将攻击脚本写入另一主机，尝试利用该漏洞获取信息

![attack again](images/attack_failed.jpg)

#### 2.4 实现对DVWA的SQL注入实验在低安全等级条件下进行防护
* 首先将DVWA的安全等级调到最低

![low secure](images/dvwa_low.jpg)

* 在DVWA上进行SQL注入测试

![sql inject](images/sql_inject.jpg)

* 设置对SQL注入敏感词的监听规则

![sql matcher](images/sql_matcher.jpg)

* 设置对SQL注入的拦截规则

![sql filter](images/sql_filter.jpg)

* 测试SQL注入(使用包含union、select等敏感词的注入语句)

![sql test](images/sql_inject_test.jpg)

### 3.VeryNginx配置要求
#### 3.1. VeryNginx仅允许白名单ip访问
* 设置对来自非白名单的VeryNginx访问请求的监听规则

![VN matcher](images/vn_white_matcher.jpg)

* 创建友好的错误信息提示页面-3

![VN response](images/deny_vn_access.jpg)

* 创建拦截规则

![VN filter](images/vn_access_filter.jpg)

* 白名单访问测试

![VN white access](images/white_access_vn_test.jpg)

* 非白名单访问测试

![VN not white access](images/not_white_vn_access_test.jpg)

#### 3.4. 访问控制策略规则实现
* 在限制前对服务器进行压力测试

![before limit](images/before_limit.jpg)

* 尝试用curl爬取Wordpress页面

![curl before](images/curl_before.jpg)

* 建立友好的错误提示页面-4

![dont fre](images/fre_limit.jpg)

* 建立访问频率限制规则

![fre rules](images/fre_limit_rules.jpg)

* 建立监听curl请求的规则

![curl matcher](images/curl_matcher.jpg)

* 建立拦截curl请求的规则

![curl filter](images/curl_filter.jpg)

* 再次对服务器进行压力测试，发现大量访问被拒绝

![after limit](images/after_limit.jpg)

* 再次尝试用curl爬取Wordpress页面，只获得了拒绝页面

![curl test](images/curl_test.jpg)

## 三、遇到的问题和解决方法
### 1. 安装Nginx时遇到错误
* 问题描述：按照用户手册安装Nginx依然遇到多个错误，主要是编译环境和依赖库缺失
* 问题分析：用户手册的排障指南是不全面的，除了列出的依赖库外，还需要自行将其他缺失的依赖环境补全
* 解决方法：补充安装make、zlib1g-dev等环境后继续安装Nginx

### 2. 客户端访问Wordpress和DVWA都失败
* 问题描述：使用域名访问Wordpress和DVWA都提示错误，但是使用IP:端口的方式可以正常访问。
* 问题原因：为了避免Nginx和VeryNginx的冲突，安装Nginx和VeryNginx后都更改了默认端口。使用域名访问时，访问的是80端口，此时Nginx和VeryNginx的监听端口都不是80，导致访问请求没有得到响应。
* 解决方法：将VeryNginx的监听端口更改为80端口后重启服务。