##环境

服务端：centos7

客户端：window

#### 一、centos7上安装docker和docker-compose
安装docker：

1、Docker 要求 CentOS 系统的内核版本高于 3.10 ，查看本页面的前提条件来验证你的CentOS 版本是否支持 Docker 。

通过 uname -r 命令查看你当前的内核版本

```
 $ uname -r
 ```
 
2、使用 root 权限登录 Centos。确保 yum 包更新到最新。

```
$ sudo yum update
 ```
3、卸载旧版本(如果安装过旧版本的话)

 ```
$ sudo yum remove docker  docker-common docker-selinux docker-engine
 ```
4、安装需要的软件包， yum-util 提供yum-config-manager功能，另外两个是devicemapper驱动依赖的

 ```
$ sudo yum install -y yum-utils device-mapper-persistent-data lvm2
 ```
5、设置yum源
 ```
$ sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
 ```
 ![avatar](https://images2017.cnblogs.com/blog/1107037/201801/1107037-20180128094640209-1433322312.png)
 
 6、可以查看所有仓库中所有docker版本，并选择特定版本安装
 
 ```
 $ yum list docker-ce --showduplicates | sort -r
 ```
 ![avatar](https://images2017.cnblogs.com/blog/1107037/201801/1107037-20180128095038600-772177322.png)

7、安装docker

 ```
$ sudo yum install docker-ce  #由于repo中默认只开启stable仓库，故这里安装的是最新稳定版17.12.0
$ sudo yum install <FQPN>  # 例如：sudo yum install docker-ce-17.12.0.ce
 ```
 ![avatar](https://images2017.cnblogs.com/blog/1107037/201801/1107037-20180128103448287-493824081.png)
 
 8、启动并加入开机启动
 
 ```
 $ sudo systemctl start docker
 $ sudo systemctl enable docker
 ```
 9、验证安装是否成功(有client和service两部分表示docker安装启动都成功了)
 
 ```
 $ docker version
 ```
 ![avatar](https://images2017.cnblogs.com/blog/1107037/201801/1107037-20180128104046600-1053107877.png)

安装docker-compose：

参考官网：https://docs.docker.com/compose/install/#install-compose

1、Run this command to download the latest version of Docker Compose:

 ```
sudo curl -L "https://github.com/docker/compose/releases/download/1.23.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
 ```
2、Apply executable permissions to the binary:

 ```
sudo chmod +x /usr/local/bin/docker-compose
 ```
 
 ###二、拉取gitlab中文版镜像
 地址：https://hub.docker.com/r/twang2218/gitlab-ce-zh
 
 1、利用xshell+xftp在centos7中创建存储日志、配置、数据的文件夹 文件夹结构如下：
 
 ```
 gitlab
     -config
     -logs
     -data
 docker-compose.yml
 ```
 2、利用docker-compose.yml启动
 ```
 version: '3'
 services:
     gitlab:
       image: 'twang2218/gitlab-ce-zh:11.1.4'
       restart: unless-stopped
       hostname: 'gitlab.domain.com'
       container_name: gitlab
       environment:
         TZ: 'Asia/Shanghai'
         GITLAB_OMNIBUS_CONFIG: |
           external_url 'http://gitlab.domain.com/'
           registry_external_url 'https://gitlab.domain.com'
           gitlab_rails['gitlab_shell_ssh_port'] = 1022
           gitlab_rails['time_zone'] = 'Asia/Shanghai'
           # gitlab_rails['smtp_enable'] = true
           # gitlab_rails['smtp_address'] = "smtp.exmail.qq.com"
           # gitlab_rails['smtp_port'] = 465
           # gitlab_rails['smtp_user_name'] = "xxxx@xx.com"
           # gitlab_rails['smtp_password'] = "password"
           # gitlab_rails['smtp_authentication'] = "login"
           # gitlab_rails['smtp_enable_starttls_auto'] = true
           # gitlab_rails['smtp_tls'] = true
           # gitlab_rails['gitlab_email_from'] = 'xxxx@xx.com'
       ports:
         - '80:80'
         - '443:443'
         - '1022:22'
       volumes:
         - ./data:/var/opt/gitlab
         - ./config:/etc/gitlab
         - ./logs:/var/log/gitlab
 ```
 
 如果你的服务器有域名，将上面的 gitlab.domain.com 替换为实际域名。
 
 实验时，也可以直接修改 /etc/hosts 方便测试。比如：
 
 ```
 127.0.0.1   gitlab.example.com
 ```
 3、cd到gitlab目录下执行docker-compose up -d启动，稍作等待。。
 
 4、启动完毕后打开浏览器访问gitlab.domain.com
 
 ![avatar](https://img2018.cnblogs.com/blog/1070088/201902/1070088-20190220104333116-1144735199.png)
 
5、SSH 连接GitLab
首先我们需要得到一个SSH Key，输入

 ```
cat ~/.ssh/id_rsa.pub
 ```
如果出现 ssh-rsa打头的字符，说明本地已经有了SSH Key那我们可以直接拿来用，如果没有则需要我们自己创建一个SSH Key。 
![avatar](https://img2018.cnblogs.com/blog/1070088/201902/1070088-20190220115510876-1501015371.png)

6、创建SSH Key
输入以下命令创建一个SSH Key。

 ```
ssh-keygen -t rsa -C "your.email@example.com" -b 4096
 ```
完成之后可以再输入 上面的cat命令 或者pbcopy直接复制生成的ssh key。

 ```
pbcopy < ~/.ssh/id_rsa.pub
 ```
在GitLab中账号-》设置中找到 SSH Keys中添加这个Key即可。

![avatar](https://img2018.cnblogs.com/blog/1070088/201902/1070088-20190220115744589-1496700000.png)

7、SSH 连接 1022 端口
正常情况下你已经可以通过git命令来连接我们部署的gitlab，但这里不一样的是我们把容器的 22端口映射到了 host的 1022上，我们需要指定端口来进连接。

ssh -p 1022 git@gitlab.domain.com
同时还需要更改GitLab的配置文件中的 ssh端口（如果上面配置过就不用了），否则GitLab中为我们生成的项目连接地址会连接不上。/gitlab/config/gitlab.rb

gitlab_rails['gitlab_shell_ssh_port'] = 1022
更改完ssh端口之后重启gitlab容器，之后我们的项目 ssh连接址会变为：

ssh://git@gitlab.domain.com:1022/test/projectname.git
到这里我们已经完成了GitLab的部署。

###三、拉取gitlab-runner镜像
地址：https://hub.docker.com/r/gitlab/gitlab-runner

1、利用xshell+xftp在centos7中创建存储日志、配置、数据的文件夹 文件夹结构如下：
 ```
 gitlab-runner
     -config
 docker-compose.yml
 ```
 
 2、利用docker-compose.yml启动

 ```
 version: '3'
 services:
     runner:
       image: 'gitlab/gitlab-runner:v11.4.2'
       container_name: gitlab-runner
       restart: always 
       networks:
         - gitlab_default
       volumes: 
         - ./config:/etc/gitlab-runner
         - /var/run/docker.sock:/var/run/docker.sock
 networks:
   gitlab_default:
     external: true
 ```
 
 其中gitlab_default为上面gitlab的网络
 
 注意：gitlab-runner的版本要和gitlab尽量对应不然会出现连不上gitlab
 
 3、cd到gitlab目录下执行docker-compose up -d启动
 
 #### 四、配置gitlab-runner连上gitlab
 
 new:建议如下命令注册：
 ```
 docker exec -it gitlab-runner gitlab-runner register -n \
   --url http://mall.boxfantasia.com.cn:8081/ \
   --registration-token QGAKwipD-9vBrdYv-yhV \
   --executor docker \
   --description "deploy" \
   --docker-image "docker:stable" \
     --docker-privileged \
    --docker-volumes /var/run/docker.sock:/var/run/docker.sock
 ```
 
 
 1、注册gitlab-runner
 
 ```
 docker exec -it gitlab-runner gitlab-runner register
 ```
 2. 我们会输入 http://gitlab.domain.com或http://ip:port 也就是我们安装在本地的GitLab
 
 3. Please enter the gitlab-ci token for this runner 要求输入 gitlab-ci token
 
 在项目的 管理区域->runners中可以找到(这里注册的是share类型runner)
 
![avatar](https://img2018.cnblogs.com/blog/1070088/201902/1070088-20190220105428020-342837083.png)  
 
 4. 输入描述,如：test
 
 5. 输入tag（留空也可以，之后可以进行编辑 ）
 
 6. 选择当遇到没有打标签的提交时是否会执行，我们选 true
 
 7. 是否锁定此runner 到当前项目， 我们选 false
 
 8. 选一个执行者 executor
 
 这一步比较重要 (ssh, docker+machine, docker-ssh+machine, kubernetes, docker, parallels, virtualbox, docker-ssh, shell)
 
 我们选docker
 
 9. 选择默认使用的镜像： docker:stable
 
 在注册完之后，我们可以在GitLab获取gitlab-ci token 的页面看到我们刚刚注册的这个 runner
 
![avatar](https://img2018.cnblogs.com/blog/1070088/201902/1070088-20190220105653474-1431778075.png)

同时可以在gitlab-runner/config下可以看到生成一个config.toml的文件，文件内容如下：

 ```
 concurrent = 1
 check_interval = 0
 
 [[runners]]
   name = "test"
   url = "http://192.168.1.157/"
   token = "69c0ff735a76c0bb3cce977a361661"#这个token是gitlab-runner根据第三步骤的token生成的
   executor = "docker"
   [runners.docker]
     extra_hosts = ["gitlab.domain.com:192.168.1.157"] #如果在执行上面步骤中碰到连接不上gitlab，是因为gitlab-runner在docker内部解析不了gitlab.domain.com,通过添加配置这个可以解决问题
     tls_verify = false
     image = "docker:stable" #这里之所以这样是因为基于这个镜像它包含了docker等工具，可以在gitlab-runner执行的.gitlab-ci.yml中有docker指令时而无需再安装docker 参考地址：https://docs.gitlab.com/ee/ci/docker/using_docker_build.html
     privileged = false #使用docker-in-docker时通常为true
     disable_cache = false
     volumes = ["/var/run/docker.sock:/var/run/docker.sock", "/cache"]
     shm_size = 0
    [runners.cache]
 ```
 
 ####五、提交项目代码完成CI/CD
 在项目根目录下添加.gitlab-ci.yml，提交项目代码后会自动运行该文件打包项目
 
 ```
 image: docker:stable
 
 image:
   name: docker/compose:1.23.2 # update tag to whatever version you want to use. 这个是因为我下面脚本用到docker-compose
   entrypoint: ["/bin/sh", "-c"]
 
 before_script:
   - docker version
   - docker-compose version
   
 build:
   script: #下面脚本根据自己情况写
     - COMPOSE_HTTP_TIMEOUT=200 docker-compose -f docker-compose-efk.yml up -d   #这是因为我的项目已经用docker-compose编排好了
     - COMPOSE_HTTP_TIMEOUT=200 docker-compose up -d --build --force-recreate
 ```

