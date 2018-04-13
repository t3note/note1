### docker的持久化数据存储
```
docker exec -it test_1 /bin/bash
mysqldump --all-databases >data.sql
vi /处理mysql的sh
添加：
mysql </data.sql
```

```
docker cp test_1:/data.sql ./
docker commit test_1 images1:v2

docker run -td --name test_2 image1:v2 run.sh
```

##### 时间与宿主机同步问题

启动容器时候加一下 
```
-v /etc/localtime:/etc/localtime
```
容器里面就会跟host一个时区了,也可以在Dockerfile里面指定时区。
