### 一个用户属于哪个组，将用户加到组里，
```bash
mayaming@ubuntu:~$ groups mayaming
mayaming : mayaming adm cdrom sudo dip plugdev lpadmin sambashare docker

sudo usermod -g hdfs yaming.ma #修改initial login group为hdfs
sudo usermod -G hdfs,root,docker yaming.ma #把yaming.ma加到一个新的组hdfs里，注意原来的root,docker也要加上
```

### STDIN上使用sed
`echo "$fpath" | sed "s/\.zip$//g"`

### 无密scp和远程执行
```bash
sshpass -p '******' scp -o StrictHostKeyChecking=no target/${PROJECT}-${POM_VERSION}.deb user@hostname:~
sshpass -p '******' ssh -o StrictHostKeyChecking=no user@hostname sudo dpkg -i /home/chao.zhang/${PROJECT}-${POM_VERSION}.deb
```

### 列出所有服务的状态
`service --status-all`

### CentOS上查看服务启动level，禁用一个服务
```bash
......
hbase-master   	0:off	1:off	2:on	3:on	4:on	5:on	6:off
hbase-regionserver	0:off	1:off	2:on	3:on	4:on	5:on	6:off
hbase-thriftserver	0:off	1:off	2:on	3:on	4:on	5:on	6:off
hive-metastore 	0:off	1:off	2:on	3:on	4:on	5:on	6:off
hive-server2   	0:off	1:off	2:on	3:on	4:on	5:on	6:off
......
[root@uhadoop-0ywevt-master1 ~]# chkconfig hive-metastore off
```

### jps查看java进程参数（很可惜，没有classpath信息）
`jps -mlvV`

### 寻找父进程
```bash
[root@uhadoop-0ywevt-master2 hadoop]# ps -Ao pid,ppid | grep 13010
13010     1
```

### ps aux输出详解
`ps aux`
| USER | PID | %CPU | %MEM | VSZ | RSS | TTY | STAT | START | TIME | COMMAND |
| :- | :- | :- | :- | :- | :- | :- | :- | :- | :- | :- |
| ppipe | 31723 | 0.0 | 0.0 | 17960 | 1528 | ? | S | May20 | 0:03 | rsync -avzL 218.244.142.25::output_25 /datayes/pipeline/newsCrawler/data_25/ |
stat 中的参数意义如下：
   - D 不可中断 Uninterruptible（usually IO）
   - R 正在运行，或在队列中的进程
   - S 处于休眠状态
   - T 停止或被追踪
   - Z 僵尸进程
   - W 进入内存交换（从内核2.6开始无效）
   - X   死掉的进程

< 高优先级 
n   低优先级
s   包含子进程
\+   位于后台的进程组

### ssh无密码访问
假设A (10.XX.XX.XX) 要无密码ssh访问B (192.XX.XX.XX):
1. 在A上ssh-keygen，一路回车，生成~/.ssh/id_rsa.pub
2. 以abc用户登录到B，在abc的~/.ssh/authorized_keys里面，把步骤1里id_rsa.pub的内容追加到最后
3. 在A上，ssh abc@192.XX.XX.XX即可

### 从ps结果中找一个程序的当前工作目录
1. 从ps结果中看到运行一个start.sh，想知道这个start.sh是在哪个目录被执行的
`hadoop   30025  0.0  0.0  12032  1816 ?        S     2014  56:48 /bin/bash ./start.sh`
2. 进入该进程的proc目录/proc/30025
3. 查看environ文件，找到PWD=...

### 用rsync从多台机器同步文件过来
- 有讲怎么判断bash参数个数
- 在脚本里提供密码
```bash
#!/bin/bash

today=`date +"%Y%m%d"`
websiteOp=$1

if [[ $# -lt 1 ]]; then
    echo "请提供网站操作类型"
    exit 1
fi

while read ip
do
    mkdir -p ./$today/$websiteOp/$ip
    echo "准备同步 $ip:/datayes/pipeline/crawl/spider/log/$websiteOp/$today/ 和 ./$today/$websiteOp/$ip/ "
    sshpass -p "******" rsync -avzL --prune-empty-dirs root@$ip:/datayes/pipeline/crawl/spider/log/$websiteOp/$today/ ./$today/$websiteOp/$ip/
    if ! [ "$(ls -A ./$today/$websiteOp/$ip)" ]; then
        rm -rf ./$today/$websiteOp/$ip
    fi
done < ./addresses  #addresses文件中为
```

### Linux查看文件encoding
`enca <file name>`

### 打印某个目录下面所有pdf文件的大小
`find . -name "*2015_2015-0*.pdf" | xargs du -sm | grep -G '^[0-9]\+' -o | sort | uniq`

### 抽取命令行参数内容
[解释](http://forums.devshed.com/scripts-94/interpreting-bash-command-firstchar-1-0-1-a-153253.html)
```bash
#!/bin/bash

echo '$1 is ' $1
echo '${1:0:1} is' "${1:0:1}"
echo '$2 is ' $2
echo '${2:3:5} is' "${2:3:5}"
```

### 得到一个文件的全路径名
```bash
readlink -f <相对路径>
```

### 得到当前运行脚本所在目录
```bash
dirname $(readlink -f $0)
```

### grep显示命中之前之后多少行
-B 命中行之前多少行
-A 命中行之后多少行

### curl使用代理
`curl -x http://120.24.172.203:9001 -k -v -L http://www.sina.com.cn`

### 查看一个程序的打开文件列表
`lsof -p 22401`
`lsof -i TCP:22` # Find Processes running on Specific Port
```bash
[root@uhadoop-0ywevt-master2 bin]# lsof -i 4 | grep 15832
java    15832   root  182u  IPv4 692642099      0t0  TCP *:webcache (LISTEN)
```

### 查看网络连接
`lsof -i`
`root@iZ62hcmwc3eZ:/etc# netstat -tlunp`
```
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1304/sshd       
tcp        0      0 0.0.0.0:88              0.0.0.0:*               LISTEN      512/python      
tcp6       0      0 :::9001                 :::*                    LISTEN      3618/squid3     
udp        0      0 0.0.0.0:88              0.0.0.0:*                           512/python      
udp        0      0 47.90.5.227:123         0.0.0.0:*                           1360/ntpd       
udp        0      0 10.172.53.219:123       0.0.0.0:*                           1360/ntpd       
udp        0      0 127.0.0.1:123           0.0.0.0:*                           1360/ntpd       
udp        0      0 0.0.0.0:123             0.0.0.0:*                           1360/ntpd       
udp        0      0 0.0.0.0:53591           0.0.0.0:*                           3618/squid3     
udp        0      0 0.0.0.0:44035           0.0.0.0:*                           512/python      
udp6       0      0 :::123                  :::*                                1360/ntpd       
udp6       0      0 :::44487                :::*                                3618/squid3     
```

### 删除某个interface
`sudo ip link del docker0`

### 新添apt-get源时获得当前ubuntu版本
\$ echo "deb http://repo.mongodb.org/apt/ubuntu "$(lsb_release -sc)"/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb.list

### 让用户确认yes or no
```bash
read -p "检查通过，确认执行安装操作[y|n] :" yn
if [[ $yn != [Yy] ]];then
    echo -e "End With Nothing!"
    exit
fi
```

### 让任务超时杀掉
```bash
timeout 10 mysql -h ****** -P 3306 -u yaming.ma -p****** -D****** -e "SHOW TABLES;"
```

### 在bash里解析python
```bash 
echo ${RPT_MYSQL_CONFIG} | python -c "import sys, json; print json.load(sys.stdin)['host']"
```

### 通过sed从配置文件里去处某一处的内容
```
# The folder where your airflow pipelines live, most likely a
# subfolder in a code repository
# This path must be absolute
dags_folder = /home/mayaming/working/my-notebook/airflow/dags

# The folder where airflow should store its log files
# This path must be absolute
base_log_folder = /home/mayaming/working/my-notebook/airflow/logs

# Airflow can store logs remotely in AWS S3 or Google Cloud Storage. Users
# must supply an Airflow connection id that provides access to the storage
# location.
remote_log_conn_id =
encrypt_s3_logs = False
```
```
    log_dir_pat='^[ ]*base_log_folder[ ]*=\(.*\)$' 
    log_dir=${sed -e "/${log_dir_pat}/!d" -e 's//\1/' $airflow_conf}
```