# chaosblade
    阿里云开源的混沌工程工具 https://github.com/chaosblade-io/chaosblade
    chasblade支持丰富的场景：[中文文档：https://github.com/chaosblade-io/chaosblade/blob/master/README_CN.md]
        基础资源：CPU、内存、网络、磁盘、进程等
        Docker容器：杀容器、容器内CPU、内存、网络、磁盘、进程等场景
        云原生平台：kubernetes平台节点上的CPU、内存、网络、磁盘、进程实验场景，Pod网络和Pod本身的场景等
        ......
# install(centos7.6)
    获取最新release包：wget https://chaosblade.oss-cn-hangzhou.aliyuncs.com/agent/github/1.2.0/chaosblade-1.2.0-linux-amd64.tar.gz
    下载完成后解压即可，无需编译：tar -zxvf chaosblade-1.2.0-linux-amd64.tar.gz -C /
        [root@chaos1 ~]# cd /chaosblade-1.2.0/
        [root@chaos1 chaosblade-1.2.0]# ./blade -h
        An easy to use and powerful chaos engineering experiment toolkit
        
        Usage:
          blade [command]
        
        Available Commands:

    支持CLI和HTTP两种调用方式，以下demo通过CLI调用，http的后续再学习调用
    
# demo(基础资源)
## CPU
    1.cpu满负荷实验：
        ./blade create cpu fullload   或./blade create cpu load
            {"code":200,"success":true,"result":"c024dfc46005d55a"}
        top命令，输入1，查看各cpu使用率
            Tasks: 134 total,   2 running, 132 sleeping,   0 stopped,   0 zombie
            %Cpu0  :100.0 us,  0.0 sy,  0.0 ni,  0.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
            %Cpu1  :100.0 us,  0.0 sy,  0.0 ni,  0.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
            %Cpu2  :100.0 us,  0.0 sy,  0.0 ni,  0.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
            %Cpu3  :100.0 us,  0.0 sy,  0.0 ni,  0.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
            %Cpu4  :100.0 us,  0.0 sy,  0.0 ni,  0.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
            %Cpu5  :100.0 us,  0.0 sy,  0.0 ni,  0.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
            %Cpu6  :100.0 us,  0.0 sy,  0.0 ni,  0.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
            %Cpu7  :100.0 us,  0.0 sy,  0.0 ni,  0.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
        查询实验状态
            ./blade status --type create
            {
            	"code": 200,
            	"success": true,
            	"result": [
            		{
            			"Uid": "c024dfc46005d55a",
            			"Command": "cpu",
            			"SubCommand": "fullload",
            			"Flag": "",
            			"Status": "Success",
            			"Error": "",
            			"CreateTime": "2021-06-05T14:39:31.949395783+08:00",
            			"UpdateTime": "2021-06-05T14:39:33.144649702+08:00"
            		}
            	]
            }
            ./blade status c024dfc46005d55a
            {
            	"code": 200,
            	"success": true,
            	"result": {
            		"Uid": "c024dfc46005d55a",
            		"Command": "cpu",
            		"SubCommand": "fullload",
            		"Flag": "",
            		"Status": "Success",
            		"Error": "",
            		"CreateTime": "2021-06-05T14:39:31.949395783+08:00",
            		"UpdateTime": "2021-06-05T14:39:33.144649702+08:00"
            	}
            }
        停止实验
            ./blade destroy c024dfc46005d55a
            {"code":200,"success":true,"result":{"target":"cpu","action":"fullload"}}
        再次查看实验状态
            ./blade status c024dfc46005d55a
            {
            	"code": 200,
            	"success": true,
            	"result": {
            		"Uid": "c024dfc46005d55a",
            		"Command": "cpu",
            		"SubCommand": "fullload",
            		"Flag": "",
            		"Status": "Destroyed",
            		"Error": "",
            		"CreateTime": "2021-06-05T14:39:31.949395783+08:00",
            		"UpdateTime": "2021-06-05T15:02:27.731808996+08:00"
            	}
            }
    2.指定满载内核数或者指定内核满载
        ./blade  create cpu load  --cpu-list 1,2  或./blade  create cpu load  --cpu-list 1-2
        Tasks: 135 total,   3 running, 132 sleeping,   0 stopped,   0 zombie
        %Cpu0  :  0.0 us,  0.0 sy,  0.0 ni,100.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
        %Cpu1  :100.0 us,  0.0 sy,  0.0 ni,  0.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
        %Cpu2  :100.0 us,  0.0 sy,  0.0 ni,  0.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
        %Cpu3  :  0.0 us,  0.0 sy,  0.0 ni,100.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
        
        ./blade  create cpu load  --cpu-count 3    //总共cpu使用率300%左右
        Tasks: 134 total,   1 running, 133 sleeping,   0 stopped,   0 zombie
        %Cpu0  :  0.3 us,  0.0 sy,  0.0 ni, 99.7 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
        %Cpu1  : 70.7 us,  0.0 sy,  0.0 ni, 29.3 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
        %Cpu2  : 62.8 us,  0.0 sy,  0.0 ni, 37.2 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
        %Cpu3  :  0.3 us,  0.0 sy,  0.0 ni, 99.7 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
        %Cpu4  :100.0 us,  0.0 sy,  0.0 ni,  0.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
        %Cpu5  :  0.0 us,  0.0 sy,  0.0 ni,100.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
        %Cpu6  : 66.4 us,  0.3 sy,  0.0 ni, 33.2 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
        %Cpu7  :  0.0 us,  0.0 sy,  0.0 ni,100.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
        KiB Mem : 32779816 total, 31443396 free,   379588 used,   956832 buff/cache
        KiB Swap:        0 total,        0 free,        0 used. 32018788 avail Mem 
        
          PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND                                                                                               
        30697 root      20   0  110636  12496      4 S 299.7  0.0   3:14.38 chaos_burncpu
    3.CPU 亲和性设置(讲一个进程"绑定"到一个或一组CPU上
        taskset -pc 32559   //显示32559 pid亲和性列表
        pid 32559's current affinity list: 0-7
        taskset -pc 1,2 32559  //设置亲和性
        pid 32559's current affinity list: 0-7
        pid 32559's new affinity list: 1,2
        taskset -pc 32559 //设置亲和性成果
        pid 32559's current affinity list: 1,2
        再次可以使用./blade  create cpu load  --cpu-list 1,2对1,2 CPU和进行负载模拟
    4.cpu  load -h当中的一些例子
        ./blade create cpu load --cpu-percent 60 --cpu-count 2   //随机两个核60%负载
        ./blade create cpu load --cpu-percent 60  --timeout 100  //全CPU 60%负荷，运行时长100s
    
## DISK
    1.disk burn
        ./blade create disk burn --write   //通过dd工具创建一个size*100大小的文件写，默认路径/，destroy时删除被写文件
        ./blade create disk burn --read --write --path /home //同时进行读、写测试，默认路径/,可以指定，默认创建600M的文件进行读取操作
        ./blade create disk burn --read --write --size 20 --path /home --timeout 120 //写2000M文件，读600M文件，总共运行120s
    2.disk fill
        ./blade create disk fill --percent 99  --reserve 10 --size 100 --timeout 120 //默认在根目录下创建一个大文件进行容量填充，percent指定使用率，reserve指定预留size(MB)，size指定填充的大小(MB)，
        三个参数同时存在时，按照percent > reserve > size优先级进行取值，可以通过--path指定目标目录

## MEM
    1.mem load(如果内存满载无法恢复，重启机器进行恢复)
        ./blade create mem load --mode ram --mem-percent 50   //采用ram方式，覆盖50%内存，此方法使用程序申请内存【内存打满后chaos_burnmem进行就自己退了，觉得模拟打满还是用cache方式】
        ./blade create mem load --mode cache --mem-percent 50  //采用cache方式，覆盖50%内存，此方法mount tmpfs后使用dd进行文件填充
        top ，输入m进行查看
        KiB Mem : 48.0/32779816 [||||||||||||||||||||||||||||||||||||||||||||||||                                                    ]
        ./blade create mem load --mode ram --mem-percent 50 --reserve 100 --rate 10 --timeout 120 //reserve预留100M内存，当mem-percent存在会被优先采用，rate指定在ram mode时内存占用速率10MB/s
        
## NETWORK
    1.network delay(可以添加timeout指定运行时间，自动停止销毁实验)
        ./blade create network delay --interface eth0 --time 30    // 指定eth0 网卡延迟30ms，对整个网卡延迟(interface和time是必须参数)，通过ping 114.67.124.166 目标地址进行验证，对比延迟3ms和30ms的表现
        64 bytes from 114.67.124.166: icmp_seq=26 ttl=54 time=3.00 ms
        64 bytes from 114.67.124.166: icmp_seq=27 ttl=54 time=2.98 ms
        64 bytes from 114.67.124.166: icmp_seq=28 ttl=54 time=26.1 ms
        64 bytes from 114.67.124.166: icmp_seq=29 ttl=54 time=34.2 ms
        
        ./blade create network delay --time 3000 --offset 1000 --interface eth0 --local-port 80,8000-8081 // 指定eth0网卡延迟3s，且浮动时间1s，实际延迟时间[2~4s]，且仅对本地80,8000-8081端口生效
        通过telnet IP port的返回时间来验证，且仅对限制了的port有延迟，非限制port无延迟
        
        ./blade create network delay --time 3000 --interface eth0 --remote-port 80 --destination-ip 10.0.0.20 // 指定eth0网卡延迟3s，仅对目标IP的目标port有效
        通过telnet IP port的返回时间来验证，且仅对限制了的port有延迟，非限制port无延迟
        
        ./blade create network delay --time 5000 --interface eth0 --exclude-port 22,8000-8080 // 指定eth0网卡延迟5s，排除22,8000-8080端口，对本地及远端端口均生效
        
    2.network dns
        ./blade create network dns --domain www.baidu.com --ip 10.0.0.0 //通过修改本地hosts文件，达到篡改域名地址映射的目的，通过ping来进行验证
        
    3.network drop(只能100%丢包，对所有网卡有效，可以使用network loss替代)
        ./blade create network drop --source-ip 10.0.0.20 --network-traffic in  //将源IP是10.0.0.20，进入本机的网络包全部丢弃，可以在源节点通过telnet 来验证
        ./blade create network drop --destination-ip 10.0.0.21  --network-traffic in --timeout 10 //将目标IP是10.0.0.21进入本机的网络包全部丢弃，可以在其他非destination节点通过telent来验证,如果本机就是10.0.0.21，切记一定勿忘timeout参数
        ./blade create network drop --source-port 20051 --network-traffic in // 将源port是80，进入本机的包全部丢掉
        ./blade create network drop --destination-port 80,81 --network-traffic in // 将目标port 80,81，进去本机的包全部丢掉
        ./blade create network drop --destination-port 20051 --network-traffic out // 将目标port是20051，从本机出去的包全部丢掉
        
    4.network loss
        ./blade create network loss --percent 70 --interface eth0 --local-port 80 // 通过eth0访问本机80端口的70%的包丢掉
        ./blade create network loss --percent 100 --interface eth0 --remote-port 20051 --destination-ip 10.0.0.20 // 本机访问10.0.0.20 80端口的包全部丢掉
        ./blade create network loss --percent 60 --interface eth0 --exclude-port 22,8000-8080 // 对eth0整个网卡60%丢包，排除特定端口
        ./blade create network loss --percent 100 --interface eth0 --timeout 20  // 实现整个网卡不可访问，20s后恢复
        
    5.network corrupt(模拟网络包损坏，可以指定port，ip等参数，类似delay用法)
        ./blade create network corrupt --percent 80 --destination-ip 10.0.0.20 --interface eth0 // 模拟到10.0.0.20的ip 网络包损坏80%，可以通过ping验证
        
    6.network reorder(模拟网络包重新排序，可以指定port、ip等参数，类似delay用法，可以模拟部分包的延迟)
        ./blade create  network reorder --correlation 80 --percent 50  --time 500 --interface eth0 --destination-ip 10.0.0.20 // 模拟到10.0.0.20的ip请求包乱序，网络包
        延迟500ms，立即发送50%的包，通过ping IP -A观察效果
        
    7.network duplicate(模拟网络包重复，可以指定ip、port等参数，类似delay用法)
        ./blade create network duplicate --percent=10 --interface=eth0 --destination-ip 10.0.0.20 // 模拟到10.0.0.20的ip 网络包有10%的重发
        
    8.network occupy
        ./blade create  network occupy --port 80 --force // 强制占用本地port
    
## PROCESS
    1.process kill(使用kill -9 或kill -15杀死进程)
        ./blade create process kill --process server   // 使用ps -ef |grep KEY查找进程并kill -9 PIDS
        ./blade create process kill --process-cmd python // 使用pgrep 查找出python进行并kill -9 PIDS
        ./blade c process kill --local-port 8080 --signal 15   // 指定信号量，kill -15 8080端口的进程，-9 内核级强制杀死一个进程，-15 操作系统发送一个通知告诉应用主动关闭
        
    2.process stop(使用kill -19 模拟进行hang，可以使用kill -18 恢复进程)
        ./blade create process stop --process server  --timeout 20
        
## STATUS
    ./blade status --type create --status Success //查询混沌实验状态
    ./blade status 8828df65fa8d75c6
    
## SERVER
    ./blade server start --port 8080
    success, listening on :8080
    ./blade server status
    {"code":200,"success":true,"result":{"port":"8080","status":"up"}}
    ./blade server stop
    {"code":200,"success":true,"result":"pid is 5798"}
    ./blade server status
    {"code":500,"success":false,"error":"down"}
    
    server启动后，可以调用curl命令进行混沌实验，%20用来做了一个间隔符号
    curl "http://114.67.124.166:8080/chaosblade?cmd=create%20cpu%20load%20--cpu-percent%2080"
    {"code":200,"success":true,"result":"642c29ede6d6edb1"}
    
    curl "http://114.67.124.166:8080/chaosblade?cmd=status%20642c29ede6d6edb1" |python -m json.tool
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   263  100   263    0     0    974      0 --:--:-- --:--:-- --:--:--   977
    {
        "code": 200,
        "result": {
            "Command": "cpu",
            "CreateTime": "2021-06-05T18:29:42.740593426+08:00",
            "Error": "",
            "Flag": " --cpu-percent=80",
            "Status": "Success",
            "SubCommand": "fullload",
            "Uid": "642c29ede6d6edb1",
            "UpdateTime": "2021-06-05T18:29:43.930256519+08:00"
        },
        "success": true
    }
    
## SCRIPT
## DOCKER
## K8S

## 模拟端口占用自己写的小脚本
    [root@chaos1 ~]# cat occupy_port.py 
    #!/usr/bin/python
    #-*- coding:utf-8 -*-
    
    import socket
    
    s = socket.socket()
    host = socket.gethostname()
    port = 80
    s.bind((host, port))
    
    s.listen(5)
    
    while True:
    	c, addr = s.accept()
    	print 'connect addr: ', addr
    	c.send("welcome !")
    	c.close()
    	
    [root@chaos2 ~]# cat client.py 
    #!/usr/bin/python
    #-*- coding:utf-8 -*-
    
    import socket
    import sys
    import time
    
    host = sys.argv[1]
    port = int(sys.argv[2])
    
    while True:
    	s = socket.socket()
    	s.bind(('10.0.0.20', 20051))
    	s.connect((host, port))
    	print s.recv(1024)
    	s.close()
    	time.sleep(1)
    	
## chaosblade实现方法小结(后续再深入代码学习)
    cpu load： 利用消耗CPU时间片来实现，增加所有核或者指定核的负载
    disk burn：使用dd命令实现，对特定目录进行读、写文件，触发目录所在磁盘的util增加
    disk fill：按照特定的百分比、大小，创建文件填充测试目录
    mem load：内存占用
        ram 模式采用代码申请内存实现
        cache 模式采用 dd、mount 命令实现，挂载 tmpfs 并且进行文件填充
        ***如果执行了内存满载，无法恢复，如何处理 A：重启机器恢复
    network delay：通过tc命令， 指定网卡、本地端口、远程端口、目标 IP 延迟
    network drop：iptable实现， 仅针对端口，不针对整个网口，有局限性，可以使用network loss替代
    network loss：tc实现，指定网卡、本地端口、远程端口、目标 IP 丢包
    network corrupt：tc实现，模拟网络包损坏
    network record：tc实现，模拟网络包的顺序重新调整
    network duplicate：tc实现，模拟网络包重发
    network occupy：指定特定port启动server，模拟端口占用
    process kill:通过 ps -ef | grep KEY、pgrep找到pid，后续通过kill -9杀掉，杀掉进程后不能恢复
    process stop： 通过 ps -ef | grep KEY、pgrep找到pid，使用 kill -STOP PIDS 暂停进程，使用 kill -CONT PIDS 恢复进程