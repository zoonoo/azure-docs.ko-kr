<properties 
	pageTitle="연결된 서비스 계산 | Microsoft Azure" 
	description="데이터의 변환/처리를 위해 Azure 데이터 팩터리 파이프라인에서 사용할 수 있는 계산 환경을 알아봅니다." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="spelluru"/>

# 연결된 서비스 계산

이 문서는 프로세스 또는 변환 데이터에 사용할 수 있는 다양한 계산 환경을 설명합니다. 또한 이러한 계산 환경을 Azure 데이터 팩터리에 연결하는 연결된 서비스를 구성하는 경우 데이터 팩터리에서 지원하는 다른 구성(주문형 vs. 사용자 고유)에 대한 자세한 내용을 제공합니다.

## 주문형 계산 환경

이 구성의 형식에서는 컴퓨팅 환경은 Azure 데이터 팩터리 서비스에 의해 완벽하게 관리됩니다. 데이터를 처리하기 위한 작업을 제출하기 전에 데이터 팩터리 서비스에서 자동으로 컴퓨팅 환경을 만들고 작업이 완료되면 제거합니다. 사용자는 주문형 계산 환경에 대한 연결된 서비스를 만들고 구성하며 작업 실행, 클러스터 관리, 부트스트래핑 작업에 대한 세부적인 설정을 제어할 수 있습니다.

> [AZURE.NOTE]주문형 구성은 현재 Azure HDInsight 클러스터에 대해서만 지원됩니다.

## Azure HDInsight 주문형 연결된 서비스

Azure 데이터 팩터리 서비스에서 데이터를 처리하도록 주문형 HDInsight 클러스터가 자동으로 작성됩니다. 클러스터는 클러스터와 연결된 저장소 계정(JSON에서 linkedServiceName 속성)과 동일한 지역에 만들어집니다.

주문형 HDInsight 연결된 서비스에 대해 다음 **중요한** 점에 유의하십시오.

- Azure 구독에서 만든 주문형 HDInsight 클러스터는 표시되지 않습니다. Azure 데이터 팩터리 서비스는 사용자를 대신해 주문형 HDInsight 클러스터를 관리합니다.
- 주문형 HDInsight 클러스터에서 실행하는 작업에 대한 로그는 HDInsight 클러스터와 연결된 저장소 계정에 복사됩니다. **작업 실행 세부 정보** 블레이드의 Azure 포털에서 이러한 로그에 액세스할 수 있습니다. 세부 정보는 [파이프라인 모니터링 및 관리](data-factory-monitor-manage-pipelines.md) 문서를 참조하십시오. 
- HDInsight 클러스터가 작업을 실행 중인 경우에 대해서만 청구됩니다.

> [AZURE.IMPORTANT]일반적으로 **15분**이 더 걸려서 필요한 Azure HDInsight 클러스터를 프로비전합니다.

### 예

	{
	  "name": "HDInsightOnDemandLinkedService",
	  "properties": {
	    "type": "HDInsightOnDemand",
	    "typeProperties": {
	      "clusterSize": 4,
	      "jobsContainer": "adfjobs",
	      "timeToLive": "00:05:00",
	      "version": "3.1",
	      "linkedServiceName": "MyBlobStore"
	      "additionalLinkedServiceNames": [
	        "otherLinkedServiceName1",
	        "otherLinkedServiceName2"
	      ]
	    }
	  }
	}

### 속성

속성 | 설명 | 필수
-------- | ----------- | --------
type | 형식 속성은 **HDInsightOnDemand**로 설정해야 합니다. | 예
clusterSize | 주문형 클러스터의 크기입니다. 이 주문형 클러스터에 포함하려는 노드 수를 지정합니다. | 예 
jobscontainer | Pig/hive/패키지 작업에서 사용하는 데이터를 보유하고 클러스터 로그를 저장할 blob 컨테이너입니다. | 예
timetolive | <p>주문형 HDInsight 클러스터에 대한 허용된 유휴 시간입니다. 클러스터에 다른 활성 작업이 없는 경우 활동이 완료된 후에 주문형 HDInsight 클러스터가 유지될 기간을 지정합니다.</p><p>예를 들어 활동 실행에 6분이 걸리고 timetolive이 5분으로 설정된 경우 클러스터는 활동을 처리하는 6분 동안 실행된 후에 5분 동안 유지됩니다. 다른 활동 실행이 6분 창을 실행하는 경우 동일한 클러스터에 의해 처리됩니다.</p><p>주문형 HDInsight 클러스터를 만드는 것은 비용이 많이 드는 작업이므로(시간이 걸림) 주문형 HDInsight 클러스터를 다시 사용하여 데이터 팩터리의 성능 향상을 위해 필요한 만큼 이 설정을 사용합니다.</p><p>timetolive 값을 0으로 설정한 경우 클러스터는 활동이 처리되는 즉시 삭제됩니다. 반면 높은 값을 설정하는 경우 클러스터는 불필요하게 많은 비용이 발생하는 유휴 상태에 머무를 수 있습니다. 따라서 필요에 따라 적절한 값을 설정하는 것이 중요합니다.</p><p>Timetolive 속성 값이 적절하게 설정되는 경우 여러 파이프라인은 주문형 HDInsight 클러스터의 동일한 인스턴스를 공유할 수 있습니다.</p> | 예
버전 | HDInsight 클러스터의 버전 | 아니요
linkedServiceName | 데이터를 저장 및 처리하기 위해 주문형 클러스터에서 사용하는 blob 저장소입니다. | 예
additionalLinkedServiceNames | HDInsight 연결된 서비스에 대한 추가 저장소 계정을 지정하므로 데이터 팩터리 서비스가 사용자를 대신해 계정을 등록할 수 있습니다. | 아니요

### 고급 속성

또한 주문형 HDInsight 클러스터의 세부적인 구성에 다음 속성을 지정할 수 있습니다.

속성 | 설명 | 필수
-------- | ----------- | --------
coreConfiguration | 만들어지는 HDInsight 클러스터에 대한 핵심 구성 매개 변수(core-site.xml에서 처럼)를 지정합니다. | 아니요
hBaseConfiguration | HDInsight 클러스터에 대한 HBase 구성 매개 변수(hbase-site.xml)를 지정합니다. | 아니요
hdfsConfiguration | HDInsight 클러스터에 대한 HDFS 구성 매개 변수(hdfs-site.xml)를 지정합니다. | 아니요
hiveConfiguration | HDInsight 클러스터에 대한 hive 구성 매개 변수(hive-site.xml)를 지정합니다. | 아니요
mapReduceConfiguration | HDInsight 클러스터에 대한 MapReduce 구성 매개 변수(mapred-site.xml)를 지정합니다. | 아니요
oozieConfiguration | HDInsight 클러스터에 대한 Oozie 구성 매개 변수(oozie-site.xml)를 지정합니다. | 아니요
stormConfiguration | HDInsight 클러스터에 대한 Storm 구성 매개 변수(storm-site.xml)를 지정합니다. | 아니요
yarnConfiguration | HDInsight 클러스터에 대한 Yarn 구성 매개 변수(yarn-site.xml)를 지정합니다. | 아니요

#### 예제 - 고급 속성을 포함하는 주문형 HDInsight 클러스터 구성

	{
	  "name": " HDInsightOnDemandLinkedService",
	  "properties": {
	    "type": "HDInsightOnDemand",
	    "typeProperties": {
	      "clusterSize": 16,
	      "jobsContainer": "adfjobs",
	      "timeToLive": "01:30:00",
	      "version": "3.1",
	      "linkedServiceName": "adfods1",
	      "coreConfiguration": {
	        "templeton.mapper.memory.mb": "5000"
	      },
	      "hiveConfiguration": {
	        "templeton.mapper.memory.mb": "5000"
	      },
	      "mapReduceConfiguration": {
	        "mapreduce.reduce.java.opts": "-Xmx8000m",
	        "mapreduce.map.java.opts": "-Xmx8000m",
	        "mapreduce.map.memory.mb": "5000",
	        "mapreduce.reduce.memory.mb": "5000",
	        "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
	      },
	      "yarnConfiguration": {
	        "yarn.app.mapreduce.am.resource.mb": "5000"
	      },
	      "additionalLinkedServiceNames": [
	        "datafeeds",
	        "adobedatafeed"
	      ]
	    }
	  }
	}

## 사용자 고유의 계산 환경 가져오기 

이 구성의 형식에서는 사용자가 이미 기존 컴퓨팅 환경을 데이터 팩터리에서 연결된 서비스로 등록할 수 있습니다. 컴퓨팅 환경은 이를 사용하여 작업을 실행하는 데이터 팩터리 서비스와 사용자에 의해 관리됩니다.
 
이 구성의 형식은 다음의 계산 환경에 대해 지원됩니다.

- Azure HDInsight
- Azure 배치 
- Azure 기계 학습입니다.

## Azure HDInsight 연결된 서비스

Azure HDInsight 연결된 서비스를 만들어서 데이터 팩터리를 사용하는 사용자 고유의 HDInsight 클러스터를 등록할 수 있습니다.

### 예

	{
	  "name": "HDInsightLinkedService",
	  "properties": {
	    "type": "HDInsight",
	    "typeProperties": {
	      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
	      "userName": "admin",
	      "password": "<password>",
	      "location": "WestUS",
	      "linkedServiceName": "MyHDInsightStoragelinkedService"
	    }
	  }
	}

### 속성

속성 | 설명 | 필수
-------- | ----------- | --------
type | 형식 속성은 **HDInsight**로 설정해야 합니다. | 예
clusterUri | HDInsight 클러스터의 URI입니다. | 예
username | 기존 HDInsight 클러스터에 연결하는데 사용할 사용자의 이름을 지정합니다. | 예
password | 사용자 계정으로 password를 지정합니다. | 예
location | HDInsight 클러스터의 위치를 지정합니다.(예: WestUS). | 예
linkedServiceName | 이 HDInsight 클러스터에서 사용하는 blob 저장소에 대한 연결된 서비스의 이름입니다. | 예

## Azure 일괄 처리 연결된 서비스

Azure 일괄 처리 연결된 서비스를 만들어 데이터 팩터리에 가상 컴퓨터(VM)의 일괄 처리 풀을 등록합니다. Azure 일괄 처리 또는 Azure HDInsight를 사용하여 .NET 사용자 지정 활동을 실행할 수 있습니다.

Azure 일괄 처리 서비스가 처음이라면 다음 항목을 참조하십시오.
 

- Azure 일괄 처리 서비스의 개요는 [Azure 일괄 처리 기술 개요](../batch/batch-technical-overview.md)입니다.
- Azure Batch 계정을 만드는 [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) cmdlet (또는) Azure 관리 포털을 사용하여 Azure Batch 계정을 만드는 [Azure 관리 포털](../batch/batch-technical-overview.md)입니다. 이 cmdlet 사용에 관한 자세한 지침은 [PowerShell을 사용하여 Azure Batch 계정 관리](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) 항목를 참조하십시오.
- [New-AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) cmdlet을 사용하여 Azure 배치 풀을 만듭니다.

### 예

	{
	  "name": "AzureBatchLinkedService",
	  "properties": {
	    "type": "AzureBatch",
	    "typeProperties": {
	      "accountName": "<Azure Batch account name>",
	      "accessKey": "<Azure Batch account key>",
	      "poolName": "<Azure Batch pool name>",
	      "linkedServiceName": "<Specify associated storage linked service reference here>"
	    }
	  }
	}

Batch 계정의 이름에 **accountName** 속성으로 "**.<지역 이름**"을 추가합니다. 예제:
	
			"accountName": "mybatchaccount.eastus" 

다른 옵션은 아래와 같이 batchUri 끝점을 제공합니다.

			accountName: "adfteam",
			batchUri: "https://eastus.batch.azure.com",

### 속성

속성 | 설명 | 필수
-------- | ----------- | --------
type | 형식 속성은 **AzureBatch**로 설정해야 합니다. | 예
accountName | Azure Batch 계정의 이름 | 예
accessKey | Azure Batch 계정에 대한 선택키 | 예
poolName | 가상 컴퓨터의 풀 이름입니다. | 예
linkedServiceName | Azure 일괄 처리 연결된 서비스와 관련된 Azure 저장소 연결된 서비스의 이름입니다. 이 연결된 서비스는 활동을 실행하는 데 필요한 파일을 스테이징하고 활동 실행 로그를 저장하는 데 사용됩니다. | 예


## Azure 기계 학습 연결된 서비스

Azure 컴퓨터 학습 연결된 서비스를 만들어 데이터 팩토리에 끝점을 매기는 기계 학습 일괄 처리를 등록합니다.

### 예

	{
	  "name": "AzureMLLinkedService",
	  "properties": {
	    "type": "AzureML",
	    "typeProperties": {
	      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
	      "apiKey": "<apikey>"
	    }
	  }
	}

### 속성

속성 | 설명 | 필수
-------- | ----------- | --------
형식 | 형식 속성은 **AzureML**로 설정해야 합니다. | 예
mlEndpoint | 일괄 처리 점수 매기기 URL입니다. | 예
apiKey | 게시된 작업 영역 모델의 API입니다. | 예


## Azure SQL 연결된 서비스

Azure SQL 연결된 서비스를 만들고 [저장 프로시저 활동](data-factory-stored-proc-activity.md)에서 사용하여 Data Factory 파이프라인에서 저장 프로시저를 호출합니다. 이 연결된 서비스에 대한 자세한 내용은 [Azure SQL 커넥터](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) 문서를 참조하세요.


  



     
 
   

<!---HONumber=August15_HO7-->