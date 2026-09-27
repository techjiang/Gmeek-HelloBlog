按功能分类整理，覆盖从镜像管理到容器编排的日常操作。

---

## 一、环境信息与帮助

```bash
docker version              # 查看 Docker 版本（客户端 + 服务端）
docker info                 # 查看 Docker 系统信息（镜像数、容器数、存储驱动等）
docker help                 # 查看帮助
docker --help               # 等价写法
docker run --help           # 查看子命令帮助
```

---

## 二、镜像管理

```bash
docker images                         # 列出本地镜像
docker images -a                      # 列出所有镜像（含中间层）
docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}"  # 自定义格式

docker pull nginx:1.24                # 拉取指定版本镜像
docker pull nginx                     # 拉取最新版（latest）
docker push myrepo/myapp:v1           # 推送镜像到仓库

docker rmi nginx:1.24                 # 删除镜像
docker rmi -f $(docker images -q)     # 强制删除所有镜像
docker image prune                    # 清理无标签的悬空镜像
docker image prune -a                 # 清理所有未被容器使用的镜像

docker tag myapp:v1 myrepo/myapp:v1   # 给镜像打标签
docker save -o nginx.tar nginx:1.24   # 导出镜像为 tar 文件
docker load -i nginx.tar              # 从 tar 文件导入镜像
docker history nginx:1.24             # 查看镜像构建历史
```

**搜索镜像：**
```bash
docker search nginx                   # 在 Docker Hub 搜索
```

---

## 三、容器生命周期管理

```bash
# 创建并启动容器（最常用）
docker run -d --name mynginx -p 8080:80 nginx:1.24

# 常用参数
docker run -d \                  # -d 后台运行
  --name mynginx \               # 指定容器名
  -p 8080:80 \                   # 端口映射 宿主机:容器
  -v /data:/usr/share/nginx/html \  # 目录挂载
  -e TZ=Asia/Shanghai \          # 设置环境变量
  --restart=always \             # 开机自启/自动重启
  --network mynet \              # 指定网络
  --memory 512m \                # 内存限制
  --cpus 1 \                     # CPU 限制
  nginx:1.24

docker start mynginx             # 启动已停止的容器
docker stop mynginx              # 停止容器
docker restart mynginx           # 重启容器
docker pause mynginx             # 暂停容器
docker unpause mynginx           # 恢复暂停
docker rm mynginx                # 删除容器（需先停止）
docker rm -f mynginx             # 强制删除（运行中也可删）
docker rm $(docker ps -aq)       # 删除所有容器
```

---

## 四、容器查看与操作

```bash
docker ps                          # 查看运行中的容器
docker ps -a                       # 查看所有容器（含已停止）
docker ps -q                       # 只输出容器 ID
docker ps -s                       # 显示容器大小
docker ps --filter "status=exited" # 按状态过滤

docker inspect mynginx             # 查看容器详细信息（JSON 格式）
docker logs mynginx                # 查看容器日志
docker logs -f mynginx             # 实时追踪日志
docker logs --tail 100 mynginx     # 查看最后 100 行
docker logs --since 1h mynginx     # 查看最近 1 小时日志

docker exec -it mynginx bash       # 进入容器（交互式终端）
docker exec -it mynginx sh         # 进入容器（无 bash 时用 sh）
docker exec mynginx cat /etc/nginx/nginx.conf  # 在容器内执行命令
docker attach mynginx              # 附着到容器主进程（不常用）

docker top mynginx                 # 查看容器内进程
docker stats                       # 实时查看所有容器资源占用
docker stats mynginx               # 查看单个容器
docker port mynginx                # 查看端口映射
docker diff mynginx                # 查看容器文件系统变化
docker cp mynginx:/etc/nginx/nginx.conf ./   # 从容器复制文件出来
docker cp ./index.html mynginx:/usr/share/nginx/html/  # 复制文件进容器
docker rename mynginx newname      # 重命名容器
docker update --restart=always mynginx   # 修改容器配置
docker wait mynginx                # 阻塞等待容器退出并返回退出码
```

---

## 五、镜像构建（Dockerfile）

```bash
docker build -t myapp:v1 .                     # 使用当前目录 Dockerfile 构建
docker build -t myapp:v1 -f Dockerfile.dev .   # 指定 Dockerfile
docker build --no-cache -t myapp:v1 .          # 不使用缓存构建
docker build -t myapp:v1 --build-arg VERSION=1.0 .   # 传入构建参数
```

**Dockerfile 常用指令：**
```dockerfile
FROM node:18-alpine            # 基础镜像
WORKDIR /app                   # 工作目录
COPY package.json ./           # 复制文件
RUN npm install                # 执行命令
COPY . .                       # 复制源码
ENV NODE_ENV=production        # 环境变量
EXPOSE 3000                    # 声明端口
VOLUME /data                   # 声明数据卷
USER node                      # 切换用户
HEALTHCHECK --interval=30s CMD curl -f http://localhost:3000/   # 健康检查
CMD ["node", "server.js"]      # 启动命令（可被覆盖）
ENTRYPOINT ["node"]            # 入口点（不易被覆盖）
```

---

## 六、数据卷与挂载

```bash
# 数据卷（Volume，推荐方式）
docker volume create mydata               # 创建数据卷
docker volume ls                          # 列出数据卷
docker volume inspect mydata              # 查看数据卷详情
docker volume rm mydata                   # 删除数据卷
docker volume prune                       # 清理无用数据卷

# 使用数据卷
docker run -d -v mydata:/var/lib/mysql mysql:8.0

# 绑定宿主机目录（Bind Mount）
docker run -d -v /host/path:/container/path nginx

# 只读挂载
docker run -d -v /host/path:/container/path:ro nginx
```

---

## 七、网络管理

```bash
docker network ls                           # 列出网络
docker network create mynet                 # 创建网络
docker network create --subnet=172.20.0.0/16 mynet   # 指定子网
docker network inspect mynet                # 查看网络详情
docker network connect mynet mynginx        # 将容器接入网络
docker network disconnect mynet mynginx     # 将容器断开网络
docker network rm mynet                     # 删除网络
docker network prune                        # 清理无用网络

# 指定网络运行容器（容器间可用名称互通）
docker run -d --name app --network mynet myapp:v1
```

**默认网络模式：**
| 模式 | 参数 | 说明 |
|------|------|------|
| bridge | `--network bridge` | 默认，容器有独立 IP |
| host | `--network host` | 直接使用宿主机网络 |
| none | `--network none` | 无网络 |
| 自定义 | `--network mynet` | 用户创建的网络 |

---

## 八、仓库管理（Registry）

```bash
docker login                          # 登录 Docker Hub
docker login myregistry.com:5000      # 登录私有仓库
docker logout                         # 退出登录

# 私有仓库
docker run -d -p 5000:5000 --name registry registry:2   # 搭建私有仓库
docker tag myapp:v1 localhost:5000/myapp:v1
docker push localhost:5000/myapp:v1
```

> 如果使用国内镜像加速，编辑 `/etc/docker/daemon.json`：
> ```json
> {
>   "registry-mirrors": ["https://mirror.ccs.tencentyun.com"]
> }
> ```
> 然后执行 `systemctl restart docker` 生效。

---

## 九、Docker Compose

**docker-compose.yml 示例：**
```yaml
version: "3.8"

services:
  web:
    image: nginx:1.24
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html
    depends_on:
      - app
    restart: always

  app:
    build: .
    environment:
      - DB_HOST=db
    restart: always

  db:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: 123456
      MYSQL_DATABASE: mydb
    volumes:
      - dbdata:/var/lib/mysql

volumes:
  dbdata:
```

**常用命令：**
```bash
docker compose up -d              # 后台启动所有服务（新版）
docker compose down               # 停止并删除容器、网络
docker compose down -v            # 同时删除数据卷
docker compose ps                 # 查看服务状态
docker compose logs -f            # 查看日志
docker compose restart            # 重启服务
docker compose pull               # 拉取最新镜像
docker compose build              # 重新构建镜像
docker compose exec app bash      # 进入服务容器
docker compose config             # 校验并查看最终配置

# 旧版命令（docker-compose 带横杠）
docker-compose up -d
```

---

## 十、系统清理与维护

```bash
docker system df                  # 查看 Docker 磁盘占用
docker system prune               # 清理停止的容器、悬空镜像、无用网络
docker system prune -a            # 连同未使用的镜像一起清理
docker system prune --volumes     # 连同数据卷一起清理（慎用）

docker container prune            # 清理所有停止的容器
docker image prune -a             # 清理未使用的镜像
docker volume prune               # 清理未使用的数据卷
docker network prune              # 清理未使用的网络

# 批量清理示例
docker rm -f $(docker ps -aq)          # 删除所有容器
docker rmi $(docker images -q)         # 删除所有镜像
```

---

## 十一、镜像导入导出与迁移

```bash
# 导出镜像
docker save -o nginx.tar nginx:1.24
docker save nginx:1.24 mysql:8.0 > images.tar   # 导出多个镜像

# 导入镜像
docker load -i nginx.tar

# 导出容器（含运行时数据）
docker export mynginx > mynginx.tar

# 导入为镜像
docker import mynginx.tar mynginx:v1
```

---

## 十二、常用组合场景速查

```bash
# 1. 快速启动一个 MySQL
docker run -d --name mysql \
  -e MYSQL_ROOT_PASSWORD=123456 \
  -p 3306:3306 \
  -v mysql_data:/var/lib/mysql \
  --restart=always \
  mysql:8.0

# 2. 快速启动一个 Redis
docker run -d --name redis \
  -p 6379:6379 \
  --restart=always \
  redis:7 redis-server --appendonly yes

# 3. 查看容器占用资源排行
docker stats --no-stream --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}"

# 4. 批量停止所有运行中的容器
docker stop $(docker ps -q)

# 5. 查看容器日志并过滤关键字
docker logs mynginx 2>&1 | grep "error"

# 6. 修改镜像 tag 并推送
docker tag myapp:v1 registry.example.com/myapp:v1
docker push registry.example.com/myapp:v1

# 7. 容器开机自启
docker update --restart=always mynginx
```

---

## 十三、故障排查常用命令

```bash
docker logs --tail 200 -f mynginx     # 查看日志
docker exec -it mynginx sh            # 进入容器排查
docker inspect mynginx | grep -i error  # 查看容器配置信息
docker inspect -f '{{.State.ExitCode}}' mynginx   # 查看退出码
docker events                         # 实时监听 Docker 事件
docker diff mynginx                   # 查看容器文件改动
docker stats mynginx                  # 查看资源占用
docker system events --since 1h       # 查看最近 1 小时事件
```