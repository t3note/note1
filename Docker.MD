### docker-compose启动项

```
docker rm -f `docker ps -aq`
docker-compose -f ~/compose.yml up -d
```

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


#### analyze_docker_json.py
```
#coding:utf-8
import json,sys
import os


def analyze_docker_json(eachinfo):
    j=json.loads(eachinfo)
    print(j[0]['State']['StartedAt'])
    print(j[0]['State']['Status'])
    print("name: %s"%j[0]['Name'].replace(r"/",""))
    d=j[0]["NetworkSettings"]["Ports"]
    dd=d.keys()
    port=""
    for each in dd:
        if d[each]!=None:
            #print(d[each][0]['HostPort'])
            port+=str(d[each][0]['HostPort'])+","
    print("port: %s"%port)
            



os.system("docker ps -q >/tmp/s.txt")

allpc=[]

with open("/tmp/s.txt","r") as f:
	for each in f:
		allpc.append(each.strip())
print("All live %s PC"%len(allpc))
allinfo=[]
for i in allpc:
    allinfo.append(os.popen('docker inspect '+i).read())
os.system("rm -r /tmp/s.txt")

for h in allinfo:
    analyze_docker_json(h)
```
