# Mysql和Mongo备份

一、mysql数据库备份

```
cat /data/db/backup.sh
#!/bin/sh
db_arr=(
    "web"
    "test"
)

for i in "${db_arr[@]}"
do
    echo "start to dump" $i
    docker exec -it docker-mysql[docker_container_id/docker_name] mysqldump -h 120.132.26.176 -uroot -p[password] --databases web > /data/backdb/`date "+%Y%m%d"`web.sql
    echo "end to dump ${db_arr[$i]}"
done
```

二、定时脚本

```
chmod +x /data/db/backup.sh
crontab -l
crontab -e
```

`40 22 * * * /data/db/backup.sh` //每天22点40执行脚本



一、Mongo 备份脚本

mongo数据库的备份是必须要做的，这里记录下自己备份数据的脚本

mongo备份脚本
`cat /data/backdb/back_up.sh`

```
## declare an array variable
declare -a db_arr=(
	"testA"
	"testB"
)
db_bk=/data/backdb/

## now loop through the above array
for i in "${db_arr[@]}"
do
   echo "start to dump " $i
   mongodump -h 192.168.0.1:20000 -d $i -o $db_bk
   echo "end"
   # or do whatever with individual element of the array
done
```

二、设置定时任务

```
chmod +x ./backup.sh
crontab -l
crontab -e
40 22 * * * /data/test/backup.sh //每天22点40执行脚本
```