#使用satis自建私有镜像

为了不让代码公开，能够让项目基于gitlab快速自动化部署，简化单个composer.json文件配置内容

## 安装
###使用composer
```
composer create-project composer/satis --keep-vcs

```
安装完之后与github中的目录结构一致
#### 配置satis.json
接着在项目根目录下创建satis.json文件

```
{
    "name": "satis packages",
    "homepage": "http://satis.example.com",
    "repositories": [
        { "type": "git", "url": "ssh://git@gitlab.example.com/tim/test.git" }
    ],
    "require":{
        "tim/test":"dev-master"
    }
}
```

接下来对镜像中的packages创建索引
php bin/satis build satis.json ./web -v #-v参数可以看到被索引的包
![avatar](https://segmentfault.com/img/bVJSnP?w=595&h=105)

###使用docker
pull image

```
git pull composer/satis

```
在build目录下创建satis.json文件之后运行
```
docker run --rm -it -v /build:/build composer/satis
```
上面这条命令的作用与bin/satis命令一样，都是创建索引

```
注意：
1,可能会遇到ssh的问题：把本地的ssh挂载到docker中
docker run --rm -it -v /root/satis:/build -v /root/ssh:/root composer/satis
2,确保挂载的/root/satis/下面有satis.json文件
3, 多个项目satis.json示例
[root@localhost satis]# cat satis.json
{
    "name": "box-sdk",
    "homepage": "http://od23101284.51mypc.cn:57990",
    "repositories": [
        {
            "type": "vcs",
            "url": "ssh://git@mall.boxfantasia.com.cn:1022/box-sdk/hello-world.git"
        },
        {
            "type": "vcs",
            "url": "ssh://git@mall.boxfantasia.com.cn:1022/box-sdk/hello.git"
        }
    ],
    "require-all": true
}
```





