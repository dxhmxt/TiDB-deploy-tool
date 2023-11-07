1.Introduction to TiDB Deployment Assistant Tool
The TiDB deployment assistant tool is used for offline deployment of TiDB for OP, and performs text menu numerical options on the deployment node (Tiup node) based on Tiup and Linux Expect to complete cluster deployment work.
The TiDB deployment assistant tool encapsulates the deployment logic for setting root users and creating instance users, as well as their mutual trust settings, initializing system parameters, mounting data disks, editing and deploying yaml files, deploying clusters, setting database root passwords, and initializing database parameters.
Optimized the repetitive execution of commands and editing of yaml files, which can help improve deployment efficiency, especially in large-scale and multi cluster deployment scenarios.



2.TiDB Deployment Assistant Tool Implementation Function
1. Edit the initial configuration file for cluster deployment, set variables for deploying cluster instance users, versions, etc., read variables and execute related logic to deploy TiDB clusters, support TiDB community version and enterprise deployment, and deploy target systems that support Kirin and Centos systems;
Provide three scenario cluster topology planning file templates and display the corresponding yaml format files for generating hosts and roles, which can be referenced and quickly edited for cluster topology planning files;
2. Read the cluster topology planning file and combine it with Linux Expect to complete the system root user mutual trust, create cluster instance users, and check the mutual trust results, avoiding manual setting of mutual trust;
3. Quickly complete the initialization of the entire cluster deployment system parameters by reading the initial configuration file and cluster topology planning file of the cluster deployment;
4. Read the cluster topology planning file to initialize and mount data disks for different types of nodes (tidb nodes and non tidb nodes);
5. Read the initial configuration file and cluster topology planning file for cluster deployment, and quickly complete instance user creation and mutual trust settings in conjunction with Linux Expect. Check the results after setting mutual trust;
6. Read the cluster topology planning file to achieve two functions:
a. Quickly generate yaml format files corresponding to hosts and roles, traverse and collect server CPU and MEM configurations to match cluster topology planning files, generate dynamic parameter values, and finally generate deployment yaml files;
b. Quickly generate yaml files for the same architecture plan; (Especially in environments where multiple related configurations and plans are deployed for the same system);
7. Read the initial configuration file and yaml file for cluster deployment, extract the installation media package, and deploy the cluster;
8. Install the MySQL client command package (mariadb) and set the database root user password;
9. Edit and read database variable parameter files, set variable parameters, and check the setting results.

3.TiDB-deploy-tool directory    
[root@CentOS76_VM ~]# tree  /root/tidbdeploy/   
tidbdeploy/   
├── conf                            #root/tidbdeploy/conf    
│   ├── iplist.txt                     
│   ├── a_and_b_iplist.txt            
│   ├── cluster_base_info.conf        
│   ├── cluster_global.conf             
│   ├── cluster_plan.conf             
│   ├── cluster_plan.conf.example1    
│   ├── cluster_plan.conf.example2    
│   ├── cluster_plan.conf.example3    
│   └── tidbinitvar.txt                
├── log                             #root/tidbdeploy/log                        
├── package                         #root/tidbdeploy/package    
│   ├── tidb-community-server-v6.1.1-linux-amd64.tar.gz   
│   ├── tidb-community-toolkit-v6.1.1-linux-amd64.tar.gz   
└── scripts                         #root/tidbdeploy/scripts  
    ├── 00_tidb_deploy_text_menu.sh    
...


4. TiDB Deployment Assistant Tool Deployment and Usage Notes
a. The TiDB tool needs to be deployed in the/root directory of the root user of the Tiup node;
b. The node where the TiDB tool is deployed needs to be configured with yum source and installed with Linux expect for easy interactive operation. The password of the cluster root user needs to be consistent;
c. Due to the need for merging image logic, both the installation media server package and toolkit package need to be downloaded simultaneously;
d. At present, the operating systems that have been tested and validated by the tool are the Kirin and Centos systems, while other operating systems have not been deployed, tested or validated;
e. At present, the TiDB version for tool deployment testing is v6.1.1 version. It is recommended to deploy TiDB version at least v6 version; The Tiup version should be v1.6 or above for parallel deployment parameters to be supported properly;
f. Different customer plans for mounting data disks may be inconsistent and not universal (the current logic is for xx customer small machine deployment scenarios, please adjust as needed if used);
g. Generate yaml format files corresponding to host and role, limited to three commonly used deployment scenarios that require conversion. Other diverse and complex deployment scenarios do not support yaml conversion;
h. The current version supports the following dynamic calculation parameters:
 tidb:  
    token-limit: 40  
    performance.max-procs: 6  
    log.file.max-backups: 200  
    tmp-storage-quota: 536870912000  
  tikv:  
    storage.block-cache.capacity: 3G  
    readpool.unified.max-thread-count: 32  
    server.end-point-max-concurrency: 8  


Install：   
user root in this directory /root/   
tar zxf  tidbdeploy-v1.x.x.tar   
sh /root/tidbdeploy/scripts/00_tidb_deploy_text_menu.sh；   


5.version  
Current Release Version: 1.0.4   
Script Editor: 马学涛  
2023-02-21.   

