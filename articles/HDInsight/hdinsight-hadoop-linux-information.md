<properties
   pageTitle="Linux 기반 HDInsight에서 Hadoop을 사용 하기 위한 팁 | Microsoft Azure"
   description="zure 클라우드에서 실행되는 친숙한 Linux 환경에서 Linux 기반 HDInsight(Hadoop) 클러스터를 사용하기 위한 구현 팁을 제공합니다."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/27/2015"
   ms.author="larryfr"/>

# Linux에서 HDInsight 사용에 관한 정보(미리 보기)

Linux 기반 Azure HDInsight 클러스터는 Azure 클라우드에서 실행되는 친숙한 Linux 환경에서 Hadoop을 제공합니다. 대부분의 작업에 대해 Linux 설치에서 모든 다른 Hadoop으로 정확하게 작동해야 합니다. 이 문서를 알고 있어야 하는 특정 차이점을 호출합니다.

## 도메인 이름

클러스터에 연결할 때 사용하려는 FQDN(정규화된 도메인 이름)은 **&lt;clustername>.azurehdinsight.net** 또는 **&lt;clustername-ssh>.azurehdinsight.net**(SSH인 경우만)입니다.

## 서비스에 대한 원격 액세스

* **Ambari(웹)** - https://&lt;clustername>. azurehdinsight.net

	> [AZURE.NOTE]클러스터 관리자 계정 및 암호를 사용하여 인증하고 Ambari에 로그인합니다. 여기에서도 클러스터 관리자 계정 및 암호를 사용합니다.
	>
	> 인증은 일반 텍스트입니다. 항상 HTTPS를 사용하여 연결의 보안을 유지합니다.

	인터넷을 통해 직접 액세스할 수 있는 클러스터의 Ambari의 경우, 일부 기능은 클러스터에서 사용하는 내부 도메인 이름으로 노드 액세스해야 합니다. 공용이 아닌 내부 도메인 이름이어야 하므로 인터넷을 통해 일부 기능에 액세스하면 “서버를 찾을 수 없음" 오류가 발생합니다.

	이 문제를 해결하려면 SSH 터널을 프록시 웹 트래픽에서 클러스터 헤드 노드에 사용합니다. 다음 문서의 **SSH 터널링** 섹션을 사용하여 로컬 컴퓨터의 포트에서 클러스터로 SSH 터널을 만듭니다.

	* [Linux, Unix 또는 OS X에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md): `ssh` 명령을 사용하여 SSH 터널을 만드는 단계

	* [Windows에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows): PuTTY를 사용하여 SSH 터널을 만드는 단계

* **Ambari(REST)** - https://&lt;clustername>.azurehdinsight.net/ambari

	> [AZURE.NOTE]클러스터 관리자 계정 및 암호를 사용하여 인증합니다.
	>
	> 인증은 일반 텍스트입니다. 항상 HTTPS를 사용하여 연결의 보안을 유지합니다.

* **WebHCat(Templeton)** - https://&lt;clustername>.azurehdinsight.net/templeton

	> [AZURE.NOTE]클러스터 관리자 계정 및 암호를 사용하여 인증합니다.
	>
	> 인증은 일반 텍스트입니다. 항상 HTTPS를 사용하여 연결의 보안을 유지합니다.

* **SSH** - &lt;clustername>-ssh.azurehdinsight.net(포트 22)

	> [AZURE.NOTE]클라이언트 컴퓨터에서 SSH를 통해 클러스터 헤드 노드에 액세스할 수 있습니다. 연결한 후 헤드 노드에서 SSH를 사용하여 작업자 노드를 액세스할 수 있습니다.

## 파일 위치

Hadoop 관련 파일은 `/usr/hdp/current`의 클러스터 노드에서 찾을 수 있습니다.

예제 데이터 및 JAR 파일은 HDFS(Hadoop Distributed File System) 또는 Azure Blob 저장소('/example' 또는 'wasb:///example')에서 찾을 수 있습니다.

## HDFS, Azure Blob 저장소 및 저장소 모범 사례

대부분의 Hadoop 배포판에서 HDFS는 클러스터의 컴퓨터에서 로컬 저장소에 의해 되돌아갑니다. 효율적이지만 계산 리소스에 시간당 비용이 부과되는 클라우드 기반 솔루션에 대한 비용이 많이 들 수 있습니다.

HDInsight는 다음과 같은 이점을 제공하는 기본 저장소로 Azure Blob 저장소를 사용합니다.

* 저렴한 장기 저장소

* 웹 사이트, 파일 업로드/다운로드 유틸리티, 다양한 언어 SDK 및 웹 브라우저와 같은 외부 서비스에 액세스할 수 있음

HDInsight에 대한 기본 저장소가 된 이후 일반적으로 사용하기 위해 아무런 동작도 수행할 필요가 없습니다. 예를 들어 다음 명령은 Azure Blob 저장소에 저장된 **/example/data** 폴더의 파일을 나열합니다.

	hadoop fs -ls /example/data

일부 명령은 Blob 저장소를 사용할지 지정해야 할 수 있습니다. 이러한 경우 **WASB://**.를 명령 앞에 붙일 수 있습니다.

HDInsight은 클러스터와 여러 개의 Blob 저장소 계정을 연결할 수도 있습니다. 기본이 아닌 Blob 저장소 계정 데이터에 액세스하려면 **WASB://&lt;container-name>@&lt;account-name>.blob.core.windows.net/** 형식을 사용할 수 있습니다. 예를 들어 다음은 지정된 컨테이너 및 Blob 저장소 계정에 대한 **/example/data** 디렉터리 콘텐츠를 나열합니다.

	hadoop fs -ls wasb://mycontainer@mystorage.blob.core.windows.net/example/data

### Blob 저장소는 어떤 클러스터를 사용하나요?

클러스터를 만드는 동안 기존 Azure 저장소 계정 및 컨테이너를 사용할지 아니면 새로 만들지를 선택했습니다. 그런 다음 잊어버릴 수 있습니다. 다음 메서드를 사용하여 저장소 계정 및 컨테이너를 찾을 수 있습니다.

**Ambari API**

1. 다음 명령을 사용하여 HDFS 구성 정보를 검색할 수 있습니다.

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1"

2. 반환되는 JSON 데이터에서 `fs.defaultFS` 항목을 찾습니다. 이 항목은 다음과 유사한 형식으로 기본 컨테이너 및 저장소 계정 이름을 포함합니다.

        wasb://CONTAINTERNAME@STORAGEACCOUNTNAME.blob.core.windows.net

	> [AZURE.TIP][jq](http://stedolan.github.io/jq/)를 설치한 경우 다음을 사용하여 `fs.defaultFS` 항목만 반환할 수 있습니다.
	>
	> `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'`
	
3. 클러스터와 연결된 모든 보조 저장소 계정을 찾거나 저장소 계정을 인증하는 데 사용되는 키를 찾으려면 다음을 사용합니다.

		curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1"
		
4. 반환되는 JSON 데이터에서 `fs.azure.account.key`로 시작하는 항목을 찾습니다. 항목 이름의 나머지 부분은 저장소 계정 이름입니다. 예: `fs.azure.account.key.mystorage.blob.core.windows.net` 이 항목에 저장된 값은 저장소 계정 인증에 사용되는 키입니다.

	> [AZURE.TIP][jq](http://stedolan.github.io/jq/)를 설치한 경우, 다음을 사용하여 키 및 값의 목록을 반환할 수 있습니다.
	>
	> `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties as $in | $in | keys[] | select(. | contains("fs.azure.account.key.")) as $item | $item | ltrimstr("fs.azure.account.key.") | { storage_account: ., storage_account_key: $in[$item] }'`


**Azure 포털**

1. [Azure 포털](https://manage.windowsazure.com/)에서 HDInsight 클러스터를 선택합니다.

2. 페이지 상단에서 **대시보드**를 선택합니다.

3. 페이지의 **연결된 리소스** 섹션에 저장소 계정 및 컨테이너가 나열됩니다.

	![연결된 리소스](./media/hdinsight-hadoop-linux-information/storageportal.png)

### Blob 저장소에 액세스하려면 어떻게 해야 합니까?

클러스터에서 Hadoop 명령을 통하는 것 외에 blob에 액세스할 수 있는 다양한 방법이 있습니다.

* [Mac, Linux 및 Windows용 Azure CLI](../xplat-cli.md): Azure로 작업하기 위한 명령줄 인터페이스 명령입니다. 설치 후 저장소 사용에 대한 도움말은 `azure storage`를 참조하고 Blob 관련 명령에 대한 도움말은 `azure blob`을 참조하세요.

* [blobxfer.py](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): Azure 저장소의 Blob 작업을 위한 python 스크립트입니다.

* 다양한 SDK:

	* [Java](https://github.com/Azure/azure-sdk-for-java)

	* [Node.JS](https://github.com/Azure/azure-sdk-for-node)

	* [PHP](https://github.com/Azure/azure-sdk-for-php)

	* [Python](https://github.com/Azure/azure-sdk-for-python)

	* [Ruby](https://github.com/Azure/azure-sdk-for-ruby)

	* [.NET](https://github.com/Azure/azure-sdk-for-net)

* [저장소 REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx)

## 다음 단계

* [HDInsight에서 Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Pig 사용](hdinsight-use-pig.md)
* [HDInsight에서 MapReduce 작업 사용](hdinsight-use-mapreduce.md)
 

<!---HONumber=62-->