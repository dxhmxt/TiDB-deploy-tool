1.TiDB部署助手工具介绍   
TiDB部署助手工具用于TiDB for OP离线部署，基于tiup和linux expect在部署节点（Tiup节点）执行文本菜单数字选项完成集群部署工作。   
TiDB部署助手工具对设置root用户和创建实例用户及其互信设置、初始化系统参数、挂载数据盘、编辑部署yaml文件、部署集群、设置数据库root密码、初始化数据库参数部署工作逻辑进行了封装。   
优化了重复执行命令、编辑yaml文件工作，可帮助大家提升部署效率，尤其是大规模、多套集群部署场景。    



2.TiDB部署助手工具实现功能   
1.编辑集群部署初始配置文件，对部署集群实例用户、版本等进行变量设置，程序读变量执行相关逻辑部署TiDB集群，支持TiDB社区版和企业部署，部署目标系统支持麒麟和centos系统；   
提供3个场景集群拓扑规划文件模板及生成host和role对应yaml格式文件展示，可以参考并快速编辑集群拓扑规划文件；   
2.读取集群拓扑规划文件结合linux expect完成免密设置系统root用户互信、创建集群实例用户和设置互信结果检查，避免手工设置互信；    
3.读取集群部署初始配置文件和集群拓扑规划文件快速完成整个集群部署系统参数初始化；   
4.读取集群拓扑规划文件对不同类型节点（tidb节点和非tidb节点）进行数据盘初始化和挂载；   
5.读取集群部署初始配置文件和集群集群拓扑规划文件，结合linux expect快速完成实例用户创建和互信设置及设置互信后结果检查；   
6.读取集群拓扑规划文件，实现两个功能：  
a.快速生成host和role对应yaml格式文件、遍历采集服务器CPU和MEM配置匹配集群拓扑规划文件生成动态参数值，最后生成部署yaml文件；  
b.快速生成相同架构规划yaml文件；（尤其同一个系统部署多套相关配置和规划的环境）；    
7.读取集群部署初始配置文件和yaml文件，解压安装介质包进行部署集群；   
8.安装mysql客户端命令包（mariadb），设置数据库root用户密码；  
9.编辑和读取数据库变量参数文件，对变量参数进行设置和对设置结果进行检查。  


3.TiDB部署助手工具目录结构     
[root@CentOS76_VM ~]# tree  /root/tidbdeploy/   
tidbdeploy/   
├── conf                            #root/tidbdeploy/conf 配置文件目录    
│   ├── iplist.txt                  #集群ip地址列表             1.7 编辑集群ip列表   
│   ├── a_and_b_iplist.txt          #新老集群ip地址列表         6.5.1 编辑两个集群ip列表   
│   ├── cluster_base_info.conf      #集群部署初始配置文件       1.1 编辑集群部署初始配置文件   
│   ├── cluster_global.conf         #yaml全局和服务配置文件     6.1 编辑yaml头部(全局和服务配置)文件    
│   ├── cluster_plan.conf           #集群拓扑规划文件           1.5 编辑集群拓扑规划文件   
│   ├── cluster_plan.conf.example1  #集群拓扑规划模板1-最小配置  1.2 集群拓扑规划模板1-最小配置   
│   ├── cluster_plan.conf.example2  #集群拓扑规划模板2-简单配置  1.3 集群拓扑规划模板2-简单配置   
│   ├── cluster_plan.conf.example3  #集群拓扑规划模板3-详细配置  1.4 集群拓扑规划模板3-详细配置   
│   └── tidbinitvar.txt             #初始化数据参数变量命令清单  9.1 编辑数据库变量参数文件   
├── log                             #root/tidbdeploy/log  日志文件目录                      
├── package                         #root/tidbdeploy/package 安装介质目录，需要server和toolkit同时下载   
│   ├── tidb-community-server-v6.1.1-linux-amd64.tar.gz   
│   ├── tidb-community-toolkit-v6.1.1-linux-amd64.tar.gz   
└── scripts                         #root/tidbdeploy/scripts 程序目录    
    ├── 00_tidb_deploy_text_menu.sh  #总菜单脚本   
...


4.TiDB部署助手工具部署和使用注意说明   
a.TiDB工具需要部署在tiup节点root用户的/root目录下；   
b.TiDB工具部署所在节点需要配置yum源，并安装linux expect，方便进行免交互操作，集群root用户密码需要一致；   
c.因合并镜像逻辑需要，安装介质server包和toolkit包需要同时下载；   
d.目前工具已测试和验证的操作系统是麒麟和centos系统，其他操作系统未进行部署测试和验证；   
e.目前工具部署测试TiDB版本是v6.1.1版本，建议部署TiDB版本也是v6以上版本；tiup版本应该在v1.6及以上，并行部署参数才能正常支持；   
f.挂载数据盘不同的客户规划可能不一致，不具有通用性（当前逻辑是xx客户小机下移部署场景使用，如使用请按需调整）；   
g.生成host和role对应yaml格式文件，仅限已有三个模板常用部署场景需求转换，其他多样性和复杂性部署场景yaml转换不支持；   
h.当前版本支持的动态计算参数如下：   
 tidb:
    token-limit: 40
    performance.max-procs: 6
    log.file.max-backups: 200
    tmp-storage-quota: 536870912000
  tikv:
    storage.block-cache.capacity: 3G
    readpool.unified.max-thread-count: 32
    server.end-point-max-concurrency: 8


部署和执行菜单命令：   
在tiup节点root用户/root/目录下，执行以下部署命令：   
tar zxf  tidbdeploy-v1.x.x.tar   
执行sh /root/tidbdeploy/scripts/00_tidb_deploy_text_menu.sh即可执行程序；   


5.版本说明   
Current Release Version: 1.0.4   
Script Editor: 马学涛   
2023-02-21.   

