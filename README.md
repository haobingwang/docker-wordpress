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
