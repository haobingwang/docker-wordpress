# Docker-WordPress

docker-compose.yml 从 https://docs.docker.com/compose/ 获取

启动：

```shell
docker-compose up -d
```

访问：http://localhost:8000

关闭：

```shell
docker-compose down
```

The command `docker-compose down` removes the containers and default network, but preserves your WordPress database.

The command `docker-compose down --volumes` removes the containers, default network, and the WordPress database.

## 准备共享目录

将容器中的目录映射到本地已经存在的目录。

If you’re on Linux, you should change the permissions of the wordpress folder (the local folder) to writable. That’s because the containers are created by the Docker daemon, a process that starts when the system boots (by the sudo user). To fix this execute:

```shell
mkdir wordpress
sudo chmod -R 777 wordpress
```

>改一下 wordpress 目录的权限，让容器可以将配置信息写入这个目录（容器内部写入的/var/www/html目录，会映射到这个目录）。

## Include pre-installed themes / plugins

Mount the volume containing your themes or plugins to the proper directory; and then apply them through the wp-admin webui. Ensure read/write/execute permissions are in place for the user.

- Themes go in a subdirectory in `/var/www/html/wp-content/themes/`
- Plugins go in a subdirectory in `/var/www/html/wp-content/plugins/`

As we’ve discussed previously, to get access to the WordPress files on our local filesystem, we need to do something like `-v ${PWD}/:/var/www/html`. You should add a working directory and map two folders (one on your container and one on your local filesystem). To do this, we just add the following two lines:

```
working_dir: /var/www/html
volumes:
 - ${PWD}/wordpress/wp-content/:/var/www/html/wp-content
```

这里要在本地目录前追加 `${PWD}/`，否则会报错：Named volume "wordpress/wp-content:/var/www/html/wp-content:rw" is used in service "wordpress" but no declaration was found in the volumes section.

The `working_dir` tells the WordPress image to install WordPress in that directory. volumes: behaves like `-v "$PWD/wordpress":/var/www/html/wp-content`. The WordPress files live under `/var/www/html` folder. We’re really only interested in the wp-content folder, since that’s where our plugins and themes reside. After all, most developers only deal with this folder.

## 安装插件：JWT Authentication for the WP REST API

文档：https://github.com/Tmeister/wp-api-jwt-auth

When the plugin is activated, a new namespace is added.


```
/jwt-auth/v1
```


Also, two new endpoints are added to this namespace.


| Endpoint                              | HTTP Verb |
| ------------------------------------- | --------- |
| */wp-json/jwt-auth/v1/token*          | POST      |
| */wp-json/jwt-auth/v1/token/validate* | POST      |

token 类型为 Bearer