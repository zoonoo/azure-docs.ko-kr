<properties 
	pageTitle="Azure에서 MariaDB(MySQL) 클러스터 실행" 
	description="Azure 가상 컴퓨터에서 MariaDB + Galera MySQL 클러스터 만들기" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="sabbour" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.tgt_pltfrm="vm-linux" 
	ms.workload="infrastructure-services" 
	ms.date="12/3/2014" 
	ms.author="v-ahsab" 
	ms.prod="azure"/>

<!--The next line, with one pound sign at the beginning, is the page title--> 
# MariaDB(MySQL) 클러스터 - Azure 자습서

<p>Azure 가상 컴퓨터의 고가용성 환경에서 작업하기 위해 강력하고 확장성이 크며 신뢰할 수 있는 MySQL의 드롭인 대체 기능인 [MariaDB](https://mariadb.org/en/about/)의 다중 마스터 [Galera](http://galeracluster.com/products/) 클러스터를 만드는 중입니다.</p>

## 아키텍처 개요

이 항목에서는 다음 단계를 수행합니다.

1. 3 노드 클러스터를 만듭니다.
2. OS 디스크에서 데이터 디스크를 분리합니다.
3. RAID-0/스트라이프 설정으로 데이터 디스크를 만들어 IOPS를 늘립니다.
4. Azure 부하 분산 장치를 사용하여 3개 노드의 부하를 분산시킵니다.
5. 반복 작업을 최소화하기 위해 MariaDB+Galera가 포함된 VM 이미지를 만든 후 이 이미지를 사용하여 다른 클러스터 VM을 만듭니다.

![Architecture](./media/virtual-machines-mariadb-cluster/Setup.png)

> [AZURE.NOTE]  이 항목에서는 [Azure CLI] 도구를 사용하므로 도구를 다운로드하고 지침에 따라 Azure 구독에 연결해야 합니다. Azure CLI에서 사용 가능한 명령에 대한 참조가 필요한 경우 이 [Azure CLI 명령 참조] 링크를 확인합니다. 또한 [인증에 사용할 SSH 키를 만들고] **.pem 파일 위치**를 적어 두어야 합니다.


## 템플릿 만들기

### 인프라

1. 리소스를 포함할 선호도 그룹을 만듭니다.

		azure account affinity-group create mariadbcluster --location "North Europe" --label "MariaDB Cluster"
        
2. 가상 네트워크 만들기

		azure network vnet create --address-space 10.0.0.0 --cidr 8 --subnet-name mariadb --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --affinity-group mariadbcluster mariadbvnet

3. 모든 디스크를 호스트할 저장소 계정을 만듭니다. 20,000 IOPS 저장소 계정 한도에 도달하지 않도록 동일한 저장소 계정에 자주 사용하는 디스크가 40개 이상 배치되지 않도록 해야 합니다. 여기서는 이 수치보다 훨씬 작으므로 단순한 구현을 위해 모든 항목을 동일한 계정에 저장하겠습니다.

		azure storage account create mariadbstorage --label mariadbstorage --affinity-group mariadbcluster

3. CentOS 7 가상 컴퓨터 이미지의 이름을 찾습니다.

		azure vm image list | findstr CentOS        
`5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926`과 같이 출력됩니다. 다음 단계에 이름을 사용하세요.

4. 생성된 .pem SSH 키를 저장한 경로로 **/path/to/key.pem**을 바꾸어 VM 템플릿을 만듭니다.

		azure vm create --virtual-network-name mariadbvnet --subnet-names mariadb --blob-url "http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-os.vhd"  --vm-size Medium --ssh 22 --ssh-cert "/path/to/key.pem" --no-ssh-password mariadbtemplate 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926 azureuser
        
5. RAID 구성에서 사용할 VM에 4x500GB 데이터 디스크를 연결합니다.

		FOR /L %d IN (1,1,4) DO azure vm disk attach-new mariadbhatemplate 512 http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-data-%d.vhd        

6. **mariadbhatemplate.cloudapp.net:22**에 만든 템플릿 VM에 SSH하고 개인 키를 사용하여 연결합니다.

### 소프트웨어

1. 루트를 가져옵니다.

        sudo su
        
2. RAID 지원을 설치합니다.

     - mdadm을 설치합니다.
        
        		yum install mdadm
                
     - EXT4 파일 시스템으로 RAID0/스트라이프 구성을 만듭니다.
        
				mdadm --create --verbose /dev/md0 --level=stripe --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf
				mdadm --detail --scan >> /etc/mdadm.conf
				mkfs -t ext4 /dev/md0
         
     - 탑재 지점 디렉터리를 만듭니다.
         
				mkdir /mnt/data
                
     - 새로 만든 RAID 장치의 UUID를 검색합니다.
        
				blkid | grep /dev/md0
    
     - /etc/fstab을 편집합니다.
        
        		vi /etc/fstab
        
     - 여기에서 UUID를 이전에 **blkid** 명령에서 가져온 값으로 바꿔 재부팅 시 자동 탑재를 사용하도록 장치를 추가합니다.
        
        		UUID=<UUID FROM PREVIOUS>   /mnt/data ext4   defaults,noatime   1 2
        	
     - 새 파티션을 탑재합니다.
        
        		mount /mnt/data
            
3. IMariaDB를 설치합니다.    
		
     - MariaDB.repo 파일을 만듭니다. 
        
              	vi /etc/yum.repos.d/MariaDB.repo
        
     - 아래 내용으로 파일을 채웁니다.      
        
				[mariadb]
				name = MariaDB
				baseurl = http://yum.mariadb.org/10.0/centos7-amd64
				gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
				gpgcheck=1
    
     - 충돌을 방지하기 위해 기존 후위와 mariadb-libs를 제거합니다.
    
    		yum remove postfix mariadb-libs-*
            
     - Galera와 함께 MariaDB를 설치합니다.
    
    		yum install MariaDB-Galera-server MariaDB-client galera
   
4. MySQL 데이터 디렉터리를 RAID 블록 장치로 이동합니다.

     - 현재 MySQL 디렉터리를 새 위치에 복사하고 이전 디렉터리를 제거합니다.
    
    		cp -avr /var/lib/mysql /mnt/data  
    		rm -rf /var/lib/mysql
      
     - 새 디렉터리에 대한 사용 권한을 설정합니다.
        
        	chown -R mysql:mysql /mnt/data && chmod -R 755 /mnt/data/  
            
     - 이전 디렉터리를 RAID 파티션의 새 위치로 안내하는 symlink를 만듭니다.
    
    		ln -s /mnt/data/mysql /var/lib/mysql   

5. [SELinux는 클러스터 작업을 방해](http://galeracluster.com/documentation-webpages/configuration.html#selinux)하므로 호환되는 버전이 나타날 때까지 현재 세션에 대해 사용하지 않도록 설정해야 합니다. `/etc/selinux/config`를 편집하여 이후의 다시 시작에서 사용하지 않도록 설정합니다.
    	
	        setenforce 0
    
       그런 다음 `/etc/selinux/config`를  `SELINUX=permissive`로 설정합니다.
       
6. MySQL 실행의 유효성을 확인합니다.

    - MySQL을 시작합니다.
    
    		service mysql start
            
    - MySQL 설치 보안을 유지하고, 루트 암호를 설정한 다음, 익명 사용자를 제거하여 원격 루트 로그인을 사용하지 않도록 설정하고 테스트 데이터베이스를 제거합니다.
    		
            mysql_secure_installation
            
    - 클러스터 작업 및 응용 프로그램(선택 사항)에 대한 데이터베이스에서 사용자를 만듭니다.
   
			mysql -u root -p
			GRANT ALL PRIVILEGES ON *.* TO 'cluster'@'%' IDENTIFIED BY 'p@ssw0rd' WITH GRANT OPTION; FLUSH PRIVILEGES;
            exit
   
   - MySQL을 중지합니다.
   
			service mysql stop
            
7. 구성 자리 표시자를 만듭니다.

	- MySQL 구성을 편집하여 클러스터 설정에 대한 자리 표시자를 만듭니다. 지금은 **`<Vairables>`**를 바꾸거나 주석을 제거하지 마세요. 이 템플릿에서 VM을 만들면 해당 작업이 수행됩니다.
	
			vi /etc/my.cnf.d/server.cnf
			
	- **[galera]** 섹션을 편집하여 지웁니다.
	
	- **[mariadb]** 섹션을 편집합니다.
	
			wsrep_provider=/usr/lib64/galera/libgalera_smm.so 
            binlog_format=ROW
            wsrep_sst_method=rsync
			bind-address=0.0.0.0 # When set to 0.0.0.0, the server listens to remote connections
			default_storage_engine=InnoDB
            innodb_autoinc_lock_mode=2
			
            wsrep_sst_auth=cluster:p@ssw0rd # CHANGE: Username and password you created for the SST cluster MySQL user
            #wsrep_cluster_name='mariadbcluster' # CHANGE: Uncomment and set your desired cluster name
            #wsrep_cluster_address="gcomm://mariadb1,mariadb2,mariadb3" # CHANGE: Uncomment and Add all your servers
            #wsrep_node_address='<ServerIP>' # CHANGE: Uncomment and set IP address of this server
			#wsrep_node_name='<NodeName>' # CHANGE: Uncomment and set the node name of this server

8. 방화벽에서 필요한 포트를 엽니다(CentOS 7의 FirewallD 사용).
	
	- MySQL: `firewall-cmd --zone=public --add-port=3306/tcp --permanent`
    - GALERA: `firewall-cmd --zone=public --add-port=4567/tcp --permanent`
    - GALERA IST: `firewall-cmd --zone=public --add-port=4568/tcp --permanent`
    - RSYNC: `firewall-cmd --zone=public --add-port=4444/tcp --permanent`
    - 방화벽 다시 로드: `firewall-cmd --reload`
	
9.  성능에 대해 시스템을 최적화합니다. 자세한 내용은 [성능 조정 전략]에 대한 이 문서를 참조하세요.

	- MySQL 구성 파일을 다시 편집합니다.
	
			vi /etc/my.cnf.d/server.cnf
		
	- **[mariadb]** 섹션을 편집하고 아래 내용을 추가합니다.
	
	> [AZURE.NOTE] **innodb_buffer_pool_size**를 VM 메모리의 70%로 설정하는 것이 좋습니다. 여기서는 RAM이 3.5GB인 Medium Azure VM에 대해 2.45GB로 설정되었습니다.
	
	        innodb_buffer_pool_size = 2508M # The buffer pool contains buffered data and the index. This is usually set to 70% of physical memory.
            innodb_log_file_size = 512M #  Redo logs ensure that write operations are fast, reliable, and recoverable after a crash
            max_connections = 5000 # A larger value will give the server more time to recycle idled connections
            innodb_file_per_table = 1 # Speed up the table space transmission and optimize the debris management performance
            innodb_log_buffer_size = 128M # The log buffer allows transactions to run without having to flush the log to disk before the transactions commit
            innodb_flush_log_at_trx_commit = 2 # The setting of 2 enables the most data integrity and is suitable for Master in MySQL cluster
			query_cache_size = 0
			
10. MySQL을 중지하고, 새 노드를 추가할 때 클러스터가 복잡해지지 않도록 시작 시 MySQL 서비스가 실행되지 않도록 하고, 컴퓨터 프로비전을 해제합니다.

		service mysql stop
        chkconfig mysql off
		waagent -deprovision	
		
11. 포털을 통해 VM을 캡처합니다. (현재 [Azure CLI 도구의 문제 #1268]은 xplat-cli 도구에서 캡처된 이미지가 연결된 데이터 디스크를 캡처하지 않는다는 사실을 설명합니다.)

	- 포털을 통해 시스템을 종료합니다.
    - 캡처를 클릭하고 이미지 이름을 **mariadb-galera-image**로 지정한 다음 설명을 입력하고 "I have run waagent(waagent를 실행했습니다.)"를 선택합니다.
	![Capture the Virtual Machine](./media/virtual-machines-mariadb-cluster/Capture.png)
	![Capture the Virtual Machine](./media/virtual-machines-mariadb-cluster/Capture2.PNG)
	
## 클러스터 만들기
 
방금 만든 템플릿에서 VM 3개를 만든 후 클러스터를 구성하고 시작합니다.

1. 가상 네트워크 이름 **mariadbvnet**, 서브넷 **mariadb**, 컴퓨터 크기 **Medium**을 제공하고 클라우드 서비스 이름을 **mariadbha**(또는 mariadbha.cloudapp.net을 통해 액세스하려는 이름)으로 전달하고, 이 컴퓨터의 이름을 **mariadb1**, 사용자 이름을 **azureuser**로 설정하고, SSH 액세스를 사용하도록 설정하고, SSH 인증서 .pem 파일을 전달하고, 생성된 .pem SSH 키를 저장한 경로로 **/path/to/key.pem**을 바꾸어 직접 만든 **mariadb-galera-image** 이미지에서 첫 번째 CentOS 7 VM을 만듭니다.

	> [AZURE.NOTE] 아래 명령은 읽기 쉽도록 여러 줄로 나누어져 있지만 각각 한 줄로 입력해야 합니다.

		azure vm create 
        --virtual-network-name mariadbvnet
        --subnet-names mariadb 
        --availability-set clusteravset
		--vm-size Medium 
		--ssh-cert "/path/to/key.pem" 
		--no-ssh-password 
		--ssh 22 
		--vm-name mariadb1 
		mariadbha mariadb-galera-image azureuser 

2. 현재 만들어진 **mariadbha** 클라우드 서비스에 _연결_하고 **VM 이름** 및 **SSH 포트**를 동일한 클라우드 서비스의 다른 VM과 충돌하지 않는 고유한 포트로 변경하여 가상 컴퓨터를 2개 더 만듭니다.

		azure vm create		
        --virtual-network-name mariadbvnet
        --subnet-names mariadb 
        --availability-set clusteravset
		--vm-size Medium
		--ssh-cert "/path/to/key.pem"
		--no-ssh-password
		--ssh 23
		--vm-name mariadb2
        --connect mariadbha mariadb-galera-image azureuser
and for MariaDB3

		azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb 
        --availability-set clusteravset
		--vm-size Medium
		--ssh-cert "/path/to/key.pem"
		--no-ssh-password
		--ssh 24
		--vm-name mariadb3
        --connect mariadbha mariadb-galera-image azureuser
		
3. 다음 단계를 위해 VM 3개의 내부 IP 주소를 각각 가져와야 합니다.

	![Getting IP address](./media/virtual-machines-mariadb-cluster/IP.png)

4. VM 3개에 SSH하고 각 VM에서 구성 파일을 편집합니다.

		sudo vi /etc/my.cnf.d/server.cnf
		
	시작 부분에 있는 **#**을 제거하여 **`wsrep_cluster_name`** 및 **`wsrep_cluster_address`** 주석을 제거하고 원하는 내용이 맞는지 확인합니다.
    또한 **`wsrep_node_address`**의 **`<ServerIP>`** 및 **`wsrep_node_name`**의 **`<NodeName>`**을 VM의 IP 주소와 이름으로 각각 바꾸고 해당 줄의 주석도 제거합니다.
	
5. MariaDB1에서 클러스터를 시작하고 시작 시 실행되도록 합니다.

		sudo service mysql bootstrap
        chkconfig mysql on
	
6. MariaDB2 및 MariaDB3에서 MySQL을 시작하고 시작 시 실행되도록 합니다.

		sudo service mysql start
        chkconfig mysql on
		
## 클러스터 부하 분산
클러스터된 VM을 만들 때 **clusteravset**라는 가용성 집합에 추가하여 다른 장애 및 업데이트 도메인에 배치되도록 했으며 Azure가 동시에 모든 컴퓨터에서 유지 관리를 수행하지 않도록 했습니다. 이 구성은 해당 Azure SLA(서비스 수준 계약)에서 지원할 요구 사항을 충족합니다.

이제 Azure 부하 분산 장치를 사용하여 3개 노드 간에 요청을 분산시킵니다.

Azure CLI를 사용하여 컴퓨터에서 아래 명령을 실행합니다.
명령 매개 변수 구조는 다음과 같습니다. `azure vm endpoint create-multiple <MachineName> <PublicPort>:<VMPort>:<Protocol>:<EnableDirectServerReturn>:<Load Balanced Set Name>:<ProbeProtocol>:<ProbePort>`

	azure vm endpoint create-multiple mariadb1 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb2 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb3 3306:3306:tcp:false:MySQL:tcp:3306
	
마지막으로, CLI에서 부하 분산 장치 검색 간격을 조금 길 수 있는 15초로 설정하므로 VM 중 하나에 대한 포털의 **끝점**에서 변경합니다.

![Edit endpoint](./media/virtual-machines-mariadb-cluster/Endpoint.PNG)

부하 분산 집합 다시 구성을 클릭하고 다음으로 이동합니다.

![Reconfigure Load Balanced Set](./media/virtual-machines-mariadb-cluster/Endpoint2.PNG)

검색 간격을 5초로 변경하고 저장합니다.

![Change Probe Interval](./media/virtual-machines-mariadb-cluster/Endpoint3.PNG)

## 클러스터 유효성 검사

하드 작업이 완료되었습니다. 이제 부하 분산 장치에 도달하고 3개의 VM 간에 요청을 매끄럽고 효율적으로 라우팅하는 `mariadbha.cloudapp.net:3306`에서 클러스터에 액세스할 수 있습니다.

원하는 MySQL 클라이언트를 사용하여 VM 중 하나에 연결하거나 연결을 끊어 이 클러스터가 작동하는지 확인합니다.

	 mysql -u cluster -h mariadbha.cloudapp.net -p
	 
새 데이터베이스를 만들고 일부 데이터를 채웁니다.

	CREATE DATABASE TestDB;
    USE TestDB;
    CREATE TABLE TestTable (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, value VARCHAR(255));
	INSERT INTO TestTable (value)  VALUES ('Value1');
	INSERT INTO TestTable (value)  VALUES ('Value2');
    SELECT * FROM TestTable;
	
아래 표와 같은 결과가 생성됩니다.

	+----+--------+
	| id | value  |
	+----+--------+
	|  1 | Value1 |
	|  4 | Value2 |
	+----+--------+
	2 rows in set (0.00 sec)	
	
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계

이 문서에서는 CentOS 7을 실행하는 Azure 가상 컴퓨터에 3 노드 MariaDB+Galera 고가용성 클러스터를 만들었습니다. Azure 부하 분산 장치를 사용하여 VM에 부하가 분산되었습니다.

[Linux에서 MySQL을 클러스터링하는 다른 방법] 및 [Azure Linux VM에서 MySQL 성능 최적화 및 테스트]를 살펴보는 것이 좋습니다.

<!--Anchors-->
[아키텍처 개요]: #architecture-overview
[템플릿 만들기]: #creating-the-template
[클러스터 만들기]: #creating-the-cluster
[클러스터 부하 분산]: #load-balancing-the-cluster
[클러스터 유효성 검사]: #validating-the-cluster
[다음 단계]: #next-steps

<!--Image references-->

<!--Link references-->
[Azure CLI]: http://azure.microsoft.com/documentation/articles/xplat-cli/
[Azure CLI 명령 참조]: http://azure.microsoft.com/documentation/articles/virtual-machines-command-line-tools/
[인증에 사용할 SSH 키를 만들고]:http://www.jeff.wilcox.name/2013/06/secure-linux-vms-with-ssh-certificates/
[성능 조정 전략]: http://azure.microsoft.com/documentation/articles/virtual-machines-linux-optimize-mysql-perf/
[Azure Linux VM에서 MySQL 성능 최적화 및 테스트]:http://azure.microsoft.com/documentation/articles/virtual-machines-linux-optimize-mysql-perf/
[Azure CLI 도구의 문제 #1268]:https://github.com/Azure/azure-xplat-cli/issues/1268
[Linux에서 MySQL을 클러스터링하는 다른 방법]: http://azure.microsoft.com/documentation/articles/virtual-machines-linux-mysql-cluster/

<!--HONumber=47-->
 