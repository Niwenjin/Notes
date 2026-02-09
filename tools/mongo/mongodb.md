# mongodb

## 本地部署mongodb

使用docker拉取mongo镜像并启动容器

```sh
# 需要配置镜像站写入 /etc/docker/daemon.json 并重启服务
docker pull mongo:4.4
# 将需要恢复的数据目录挂载到容器
docker run -d -p 27017:27017 --name mongodb -v ~/mongo/data:/data/db -v ~/mongo/backup:/data/backup mongo:4.4
# 进入容器
docker exec -it mongodb bash
# 使用mongorestore恢复数据表
mongorestore -dSmartCareLP --gzip /data/backup/
# 使用mongodump备份数据
mongodump --dSmartCareLP --out /data/backup/ --gzip
--db：指定导出的数据库
--collection：指定导出的集合
--excludeCollection：指定不导出的集合
--host ：远程ip
--username：开启身份验证后，用户的登录名
--password：用户的密码
--out（指定输出目录）：如果不使用这个参数，mongodump将输出文件保存在当前工作目录中名为dump的目录中
--archive：导出归档文件，最后只会生成一个文件
--gzip：压缩归档的数据库文件，文件的后缀名为.gz
```



