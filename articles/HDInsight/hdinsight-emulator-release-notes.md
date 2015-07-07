<properties 
	pageTitle="릴리스 정보: Microsoft HDInsight Emulator for Azure | Microsoft Azure" 
	description="HDInsight Hadoop Emulator의 최신 릴리스, Hadoop 샌드박스 환경에 대한 새로운 정보를 제공합니다." 
	editor="cgronlun" 
	manager="paulettm" 
	services="hdinsight" 
	authors="mumian" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="jgao"/>




# 릴리스 정보: Microsoft HDInsight Emulator for Hadoop 



> [AZURE.NOTE]버전 번호를 확인하는 가장 쉬운 방법은 **Microsoft HDInsight Emulator for Azure**(버전 1.0.0.0 이상의 경우) 또는 **Microsoft HDInsight Developer Preview**(버전 1.0.0.0 이전의 경우) 항목에서 프로그램 추가/제거를 확인하는 것입니다.

## 버전 2.0.0.0, 2014년 8월 29일 출시

* 이 릴리스에서는 현재 버전 3.1의 서비스에서 라이브 상태인 동일 Hadoop 프로젝트 집합을 대상으로 하도록 HDInsight Emulator가 업데이트되었습니다.    

* 이 제품의 미리 보기 릴리스와 마찬가지로 이 릴리스는 개발자 시나리오를 대상으로 하기 때문에 단일 노드 배포만 지원합니다.

### 새로운 기능 
 
* 서비스의 3.1 버전에 해당하는 [업데이트된 Hadoop 구성 요소 버전](hdinsight-component-versioning.md). 여기에는 Hive 0.13 및 Tez 지원이 포함됩니다.

## 버전 1.0.0.0, 2013년 10월 28일 출시

* 이 버전은 이전의 Microsoft HDInsight Developer Preview였던 Microsoft HDInsight Emulator for Azure의 일반적으로 사용 가능한(GA) 릴리스입니다. 

* 이 제품의 미리 보기 릴리스와 마찬가지로 이 릴리스는 개발자 시나리오를 대상으로 하기 때문에 단일 노드 배포만 지원합니다.

### 새로운 기능 
 
* 모든 Apache Hadoop 서비스의 자동 시작 또는 수동 시작 설정을 간소화하는 스크립트가 추가되었습니다. 기본값은 이전처럼 자동이지만 이제 C:\Hadoop에 설치된 set-onebox-manualstart.cmd 또는 set-onebox-autostart.cmd 스크립트를 사용하여 모든 서비스를 변경할 수 있습니다. 

* 필요한 설치 종속성 수는 크게 줄었으며 설치 속도는 더욱 빨라졌습니다.

* 이 버전에는 GettingStarted 폴더에 설치된 RunSamples.ps1 스크립트에서 Pig 샘플을 실행하는 데 사용한 명령의 버그 수정이 포함되어 있습니다.

* 이 버전에는 Azure HDInsight 클러스터 버전 1.6에서 사용할 수 있는 HDP(Hortonworks Data Platform) 서비스에 맞는 Hortonworks Data Platform 버전 1.1에 대한 업데이트가 포함되어 있습니다.

## 버전 0.11.0.0, 2013년 9월 30일 출시

### 새로운 기능 
		
* HDInsight 대시보드가 제거되었습니다. 

* 이 버전에는 Azure HDInsight의 Hortonworks Data Platform Preview와 대등한 Hortonworks Data Platform Developer Preview에 대한 업데이트가 포함되어 있습니다.

## 버전 0.10.0.0, 2013년 8월 9일 출시

### 새로운 기능 
	
* 이 버전에는 Azure HDInsight의 Hortonworks Data Platform Preview와 대등한 Hortonworks Data Platform Developer Preview에 대한 업데이트가 포함되어 있습니다.

## 버전 0.8.0.0, 2013년 6월 7일 출시

### 새로운 기능 

* 이 버전에는 Azure HDInsight의 Hortonworks Data Platform Preview와 대등한 Hortonworks Data Platform Developer Preview에 대한 업데이트가 포함되어 있습니다.

## 버전 0.6.0.0, 2013년 5월 13일 출시

### 새로운 기능 

* Hive Server 2가 지금 설치 중입니다. 이 업데이트와 동시에 출시된 Microsoft ODBC Driver for Hive의 버전 0.9.2.0에 필요합니다. 

* 모든 서비스가 자동 시작으로 설정되어 있으므로 컴퓨터를 다시 부팅한 후 모두 다시 시작하도록 할 필요가 없습니다.

## 버전 0.4.0.0, 2013년 3월 25일 출시

### 새로운 기능 

* Microsoft HDInsight Developer Preview의 새 릴리스 및 Hortonworks Data Platform for Windows Developer Preview 

* Apache Hadoop, Hive, Pig, Sqoop, Oozie, HCatalog, Templeton을 포함합니다.

* 다음 기능이 포함된 새 HDInsight 대시보드
 
	* Azure HDInsight 서비스를 사용하여 원격으로 실행 중인 클러스터 및 로컬 설치 등 여러 클러스터에 연결합니다. HDInsight 서비스에 대한 자세한 내용은 [http://azure.microsoft.com/documentation/services/hdinsight/](http://azure.microsoft.com/documentation/services/hdinsight/)를 참조하세요.
 
	* 로컬 클러스터에서 Azure Blob 저장소를 구성합니다. 자세한 지침은 아래를 참조하세요.

	* 새 대화형 Hive 콘솔에서 Hive 쿼리를 작성 및 편집합니다.

	* 작업 기록 및 결과를 보고 다운로드합니다.

### 릴리스 정보 

* 로컬 HDInsight 설치 및 Azure HDInsight 서비스의 REST API 끝점은 다음과 같이 동일한 서비스의 다른 포트 번호를 통해 액세스됩니다. 

	로컬: Oozie - http://localhost:11000/oozie/v1/admin/status Templeton - http://localhost:50111/templeton/v1/status ODBC - DSN 구성 또는 연결 문자열에서 10000 포트 사용

	HDInsight 서비스: Oozie - http://ServerFQDN:563/oozie/v1/admin/status Templeton - http://ServerFQDN:563/templeton/v1/status ODBC - DSN 구성 또는 연결 문자열에서 563 포트 사용


* 다음과 같이 로컬 클러스터에 Azure Blob 저장소를 구성합니다.

	대시보드에 "local(hdfs)"이라는 기본 로컬 클러스터가 표시됩니다. Azure Blob 저장소를 로컬 설치를 위한 저장소로 사용하려면 다음을 수행합니다.

	1. C:\Hadoop\hadoop-1.1.0-SNAPSHOT\conf에 있는 core-site.xml에 account 태그를 추가합니다.       

			<property>
        		<name>fs.azure.account.key.{AccountName}</name>
        		<value>{Key}</value>
     		</property>

			<property>
        		<name>fs.default.name</name>
       			<!-- cluster variant -->
       	 		<value>asv://ASVContainerName@ASVAccountName</value>
        		<description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
        		<final>true</final>
      		</property>

     		<property>
        		<name>dfs.namenode.rpc-address</name>
        		<value>hdfs://localhost:8020</value>
        		<description>A base for other temporary directories.</description>
      		</property>
      
		예:

			<property>
    			<name>fs.azure.account.key.MyHadoopOnAzureAccountName</name>
				<value>8T45df897/a5fSox1yMjYkX66CriiGgA5zptSXpPdG4o1Qw2mnbbWQN1E9i/i7kihk6FWNhvLlqe02NXEVw6rP==</value>
    		</property>

			<property>
				<name>fs.default.name</name>
				<!-- cluster variant -->
				<value>asv://MyASVContainer@MyASVAccount</value>
				<description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
				<final>true</final>
			</property>
        

	2. 바탕 화면에서 관리자 모드로 Hadoop 명령 셸을 열고 다음 명령을 실행합니다.
	 
	 		%HADOOP_NODE%\stop-onebox.cmd && %HADOOP_NODE%\start-onebox.cmd

	3. 다음과 같은 전체 URI를 사용하여 해당 계정에 있는 모든 파일에 액세스합니다. asv://{container}@{account}/{path}(또는 데이터에 액세스하는 데 HTTPS를 사용하려는 경우 asvs://). 예:
	 
	 		hadoop fs -lsr 
			asvs://MyHadoopOnAzureContainerName@MyHadoopOnAzureAccountName/example/data/

	4. 현재 등록된 로컬 클러스터를 삭제하고 새 Azure Blob 저장소 자격 증명으로 로컬 클러스터를 다시 등록합니다.


## 버전 0.3.0.0, 2012년 12월 13일 출시 

* 대시보드 웹 사이트가 Windows 자격 증명을 사용하지 않고 익명 인증으로 변경되었습니다. 이렇게 하면 이전 버전의 릴리스 정보에서 언급된 로그인 프롬프트 문제가 없어집니다. 

* Sqoop에서 내보내기 및 일부 형식의 가져오기 버그가 수정되었습니다.

### 릴리스 문제 

* JavaScript 콘솔을 로드하지 못했습니다. 자세한 내용은 버전 0.2.0.0의 릴리스 정보를 참조하세요. 
* Sqoop 명령줄은 아래와 같이 경고를 표시합니다. 이러한 경고는 향후 업데이트에서 해결되므로 무시해도 괜찮습니다. 
	
		c:\Hadoop\sqoop-1.4.2\bin>sqoop version 
		Setting HBASE_HOME to 
		Warning: HBASE_HOME [c:\hadoop\hadoop-1.1.0-SNAPSHOT\hbase-0.94.2] does not exist HBase imports will fail. 
		Please set HBASE_HOME to the root of your HBase installation. 
		Setting ZOOKEEPER_HOME to 
		Warning: ZOOKEEPER_HOME [c:\hadoop\hadoop-1.1.0-SNAPSHOT\zookeeper-3.4.3] does not exist 
		Please set $ZOOKEEPER_HOME to the root of your Zookeeper installation. 
		Sqoop 1.4.2 
		git commit id 3befda0a456124684768348bd652b0542b002895 
		Compiled by  on Thu 11/29/2012- 3:26:26.10

## 버전 0.2.0.0, 2012년 12월 3일 출시

* Windows Installer에 의미 체계 버전 관리 도입 

* MSDN 포럼에 보고된 다양한 설치 버그에 대한 수정, 특히 HDInsight 대시보드 설치와 관련한 버그 수정

* 향상된 검색 기능을 위해 추가된 **시작** 메뉴 항목

* Hive 콘솔 여러 줄 입력에 대한 수정

* 시작 콘텐츠에 대한 업데이트

### 릴리스 문제 

* JavaScript 콘솔을 로드하지 못했습니다. 

	* 일부 설치의 경우 페이지에 HTTP 404 오류를 표시하며 JavaScript 콘솔이 실패합니다. 이 문제를 해결하려면 http://localhost:8080으로 바로 이동하여 콘솔을 사용합니다. 

* HDInsight 대시보드를 검색하면 로그인 프롬프트가 발생합니다.

	* HDInsight 대시보드를 검색할 때 로그인 대화 상자가 나타난다는 일부 보고가 있었습니다. 이런 경우 현재 사용자의 로그인 정보를 제공할 수 있으며 대시보드를 검색할 수 있어야 합니다. 


## 버전 1.0.0.0, 2012년 10월 23일 출시

* 최초 릴리스 

### 릴리스 문제 

* Hive 콘솔 

	* 제출된 Hive 명령에 새 줄이 포함되어 있으면 구문 오류가 표시됩니다. 새 줄을 제거하고 의도한 대로 쿼리를 실행해야 합니다. 



## 일반적으로 알려진 문제

* Hadoop 사용자 암호 만료 

	컴퓨터에 적용된 Active Directory 정책에 따라 Hadoop 사용자의 암호가 만료되었을 수 있습니다. 다음 Windows PowerShell 스크립트는 암호가 만료되지 않도록 설정하고 관리 명령 프롬프트에서 실행할 수 있습니다.

		$username = "hadoop"
		$ADS_UF_DONT_EXPIRE_PASSWD = 0x10000 # (65536, from ADS_USER_FLAG_ENUM enumeration)

		$computer = [ADSI]("WinNT://$ENV:COMPUTERNAME,computer")
		$users = $computer.psbase.children | where { $_.psbase.schemaclassname -eq "User" }

		foreach($user in $users)
		{
			if($user.Name -eq $username)
			{
				$user.UserFlags = $ADS_UF_DONT_EXPIRE_PASSWD
        		$user.SetInfo()
 
        		$user.PasswordExpired = 0
        		$user.SetInfo()
 
        		Write-Host "$username user maintenance completed. "
    		}
		}


* 임시 디렉터리
	
	hadoop.tmp.dir 파일이 잘못된 위치를 나타냅니다. C:\hadoop\hdfs를 가리키지 않고 잘못된 위치인 C:\hdfs를 가리킵니다. 이 버그는 HDP 비트의 다음 업데이트에서 수정됩니다.

* OS 제한

	HDInsight 서버는 64비트 OS에 설치되어야 합니다.

* Active Directory (웹 플랫폼 설치 관리자) 검색 결과

	WebPI 검색 결과에서 HDInsight를 찾을 수 없습니다. 이 문제는 대개 OS 제한으로 인해 발생합니다. HDInsight는 Windows 7 서비스 팩 1, Windows Server 2008 R2 서비스 팩 1, Windows 8 또는 Windows Server 2012의 최소 버전을 실행하는 64비트 운영 체제가 필요합니다.

* 관리 명령 프롬프트 설명서

	* **hadoop mradmin** 또는 **hadoop defadmin**과 같은 명령을 실행하려면 hadoop 사용자로 실행해야 합니다. 

	* 해당 사용자로 실행하는 셸을 쉽게 만들려면 Hadoop 명령 프롬프트를 열고 다음을 실행합니다.
	 
	 		start-hadoopadminshell

	* 이 코드는 Hadoop 관리자 권한으로 실행하는 새 명령 셸을 엽니다.

##<a name="nextsteps"></a> 다음 단계

- [HDInsight Emulator 시작][hdinsight-hadoop-emulator-get-started]




[hdinsight-hadoop-emulator-get-started]: ../hdinsight-get-started-emulator.md

 

<!---HONumber=62-->