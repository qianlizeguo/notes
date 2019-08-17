###所有安装包全部在 /usr/local/lib目录下

1.安装http2

 a.下载  [nghttp2](https://github.com/redis/hiredis/archive/v0.13.3.tar.gz), 解压 sudo tar -zxvf  nghttp2-1.32.0.tar.xz

 b.进入目录  nghttp2-1.32.0

    sudo ./configure

    sudo make && make install
    
 2.安装hiredis
 
 a.下载[hiredis](https://github.com/redis/hiredis/archive/v0.13.3.tar.gz) 解压 
     
     sudo tar -zxvf hiredis-0.13.3.tar.gz
 
 b.进入目录hiredis-0.13.3，执行 
 
    sudo make -j && sudo make install
 
 3.安装swoole4.0
 
 a.下载[swoole 4.0](https://github.com/swoole/swoole-src/releases/tag/v4.0.0)   
 
 b.进入swoole目录，执行命令 
 
     /usr/local/Cellar/php/7.2.6/bin/phpize
 
 c.编译 
    
     sudo ./configure \
             --with-php-config=/usr/local/Cellar/php/7.2.6/bin/php-config \
             --enable-coroutine \
             --enable-openssl  \
             --enable-http2  \
             --enable-async-redis \
             --enable-sockets \
             --enable-mysqlnd
             
    注意：在b这一步，哪里出错就安装那里，假如openssl没有就安装openssl，然后查看
    ls /usr/local/include/openssl 有无，
    没有就
    ln -s /usr/local/Cellar/openssl/1.0.2o_1/include/openssl /usr/local/include/  （建立软连接过去就可以了）
    
 d.安装：
 
     sudo make && make install
     
 e.查看
 
    php -m  (查看php扩展)