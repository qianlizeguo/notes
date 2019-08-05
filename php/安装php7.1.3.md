##安装php7.1.3

下载好后解压 php-7.1.3.tar.gz 压缩包，并进入解压后的目录
````
wget -c http://cn2.php.net/distributions/php-7.1.3.tar.gz
tar -xzvf php-7.1.3.tar.gz
cd php-7.1.3

````

安装php7需要的一些依赖库包 libxml2和一些其他依赖的扩展库

````
yum -y install libxml2 
yum -y install libxml2-devel 
yum -y install openssl 
yum -y install openssl-devel 
yum -y install curl-devel 
yum -y install libjpeg-devel 
yum -y install libpng-devel 
yum -y install freetype-devel
yum -y install bzip2-devel
yum -y install libmcrypt libmcrypt-devel
yum -y install postgresql-devel
yum -y install aspell-devel
yum -y install readline-devel
yum -y install libxslt-devel
yum -y install net-snmp-devel
yum -y install unixODBC-devel
yum -y install libicu-devel
yum -y install libc-client-devel
yum -y install libXpm-devel
yum -y install libvpx-devel
yum -y install enchant-devel
yum -y install openldap
yum -y install openldap-devel
yum -y install db4-devel
yum -y install gmp-devel
yum -y install sqlite-devel
yum -y install mysql-devel
````

安装前的环境配置检查，php7的一些依赖包的检查和php扩展的启动，这个过程如果缺少php依赖的库包会有报错提示。

添加用户和组 
````
groupadd -r www && useradd -r -g www -s /sbin/nologin
````
编译时不要安装  --enable-gd-jis-conv, 会导致gd中文乱码
````
./configure      --prefix=/usr/local/php    --with-config-file-path=/usr/local/php/etc    --enable-fpm    --with-fpm-user=www    --with-fpm-group=www    --enable-inline-optimization    --disable-debug    --disable-rpath    --enable-shared    --enable-soap    --with-xmlrpc    --with-openssl    --with-mcrypt    --with-pcre-regex    --with-sqlite3    --with-zlib    --enable-bcmath    --with-iconv    --with-bz2    --enable-calendar    --with-curl    --with-cdb    --enable-dom    --enable-exif    --enable-fileinfo    --enable-filter     --with-pcre-dir    --enable-ftp    --with-gd    --with-openssl-dir    --with-jpeg-dir    --with-png-dir    --with-freetype-dir    --enable-gd-native-ttf      --with-gettext    --with-gmp    --with-mhash    --enable-json    --enable-mbstring    --enable-mbregex    --enable-mbregex-backtrack    --with-libmbfl    --with-onig    --enable-pdo    --with-mysqli=mysqlnd    --with-pdo-mysql=mysqlnd    --with-zlib-dir    --with-pdo-sqlite    --with-readline    --enable-session    --enable-shmop    --enable-simplexml    --enable-sockets    --enable-sysvmsg    --enable-sysvsem    --enable-sysvshm    --enable-wddx    --with-libxml-dir    --with-xsl    --enable-zip    --enable-mysqlnd-compression-support    --with-pear    --enable-opcache
````
````
make && make install
````
( 如果出现 make: *** [sapi/cli/php] Error 1 的错误，通过继续执行 make ZEND_EXTRA_LIBS='-liconv' 命令解决

运行
````
/usr/local/php/sbin/php-fpm
````