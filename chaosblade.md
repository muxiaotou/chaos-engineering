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
    1.network delay
    2.network dns
    3.network drop
    4.network loss
    5.network corrupt
    6.network reorder
    7.network duplicate
    8.network occupy
    
## PROCESS
    1.process kill
    2.process stop
    
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
        
        
   
          
        
        
    
