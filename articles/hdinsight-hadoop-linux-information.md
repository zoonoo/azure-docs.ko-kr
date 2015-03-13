<properties
   pageTitle="Linux 기반 HDInsight에서 Hadoop에 대해 알아야 할 정보 | Azure"
   description="Linux 기반 HDInsight 클러스터는 Azure 클라우드에서 실행되는 친숙한 Linux 환경에서 Hadoop을 제공합니다."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

# Linux의 HDInsight에서 작업(미리 보기)

Linux 기반 HDInsight 클러스터는 Azure 클라우드에서 실행되는 친숙한 Linux 환경에서 Hadoop을 제공합니다. 대부분의 작업에 대해 Linux 설치에서 모든 다른 Hadoop으로 정확하게 작동해야 합니다. 이 문서를 알고 있어야 하는 특정 차이점을 호출합니다.

## 도메인 이름

클러스터에 연결할 때 사용하려는 FQDN(완전히 정규화된 도메인 이름)은 **&lt;clustername >.azurehdinsight.net** 또는 (SSH의 경우) **&lt;clustername >.aurehdinsight.net**입니다.

## 원격으로 액세스할 수 있는 서비스

* **Ambari(웹)** - https://&lt;clustername>.azurehdinsight.net

	> [AZURE.NOTE] 클러스터 관리자 계정 및 암호를 사용하여 인증하고 Ambari에 로그인합니다. 또한 클러스터 관리자 계정 및 암호도 사용합니다.
	> 
	> 인증은 일반 텍스트 - 항상 HTTPS를 사용하여 연결의 보안을 유지합니다.

	클러스터에 대한 Ambari가 인터넷을 통해 직접 액세스할 수 있는 동안 일부 기능이 클러스터에서 사용하는 내부 도메인 이름으로 노드를 액세스하는 데 의존합니다. 내부 도메인 이름이 되고 공개되지 않은 이후부터 인터넷을 통해 일부 기능에 액세스 하려고 할 때 서버에서 오류를 받지 않게 됩니다.

	이 문제를 해결하려면 SSH 터널을 프록시 웹 트래픽에서 클러스터 헤드 노드에 사용합니다. 다음 문서를 사용하여 로컬 컴퓨터의 포트에서 클러스터에 SSH 터널을 만듭니다.

	* <a href="../hdinsight-hadoop-linux-use-ssh-unix/#tunnel" target="_blank">Linux, Unix 또는 OS X에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용</a> - `ssh` 명령을 사용하여 SSH 터널을 만드는 단계

	* <a href="../hdinsight-hadoop-linux-use-ssh-windows/#tunnel" target="_blank">Windows에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용</a> - Putty를 사용하여 SSH 터널을 만드는 단계

* **Ambari(REST)** - https://&lt;clustername>.azurehdinsight.net/ambari

	> [AZURE.NOTE] 클러스터 관리자 계정 및 암호를 사용하여 인증합니다.
	> 
	> 인증은 일반 텍스트 - 항상 HTTPS를 사용하여 연결의 보안을 유지합니다.

* **WebHCat(Templeton)** - https://&lt;clustername>.azurehdinsight.net/templeton

	> [AZURE.NOTE] 클러스터 관리자 계정 및 암호를 사용하여 인증합니다.
	> 
	> 인증은 일반 텍스트 - 항상 HTTPS를 사용하여 연결의 보안을 유지합니다.

* **SSH** - &lt;clustername>-ssh.azurehdinsight.net, 포트 22

	> [AZURE.NOTE] 클라이언트 컴퓨터에서 SSH를 통해 클러스터 헤드 노드에 액세스할 수 있습니다. 연결한 후 헤드 노드에서 SSH를 사용하여 작업자 노드를 액세스할 수 있습니다.

## 파일 위치

 `/usr/hdp/current`의 클러스터 노드에서 Hadoop 관련 파일을 찾을 수 있습니다.

예제 데이터 및 jar는 '/예제' 또는  'wasb:///example'의 HDFS(WASB)에서 찾을 수 있습니다.

## HDFS, WASB 및 저장소 모범 사례

대부분의 Hadoop 배포판에서 HDFS(Hadoop 분산 파일 시스템)는 클러스터의 컴퓨터에서 로컬 저장소에 의해 되돌아갑니다. 효율적이지만 계산 리소스에 시간당 비용이 부과되는 클라우드 기반 솔루션에 대한 비용이 많이 들 수 있습니다.

HDInsight는 다음과 같은 이점을 제공하는 기본 저장소로 Azure Blob 저장소를 사용합니다.

* 저렴한 장기 저장소

* 웹 사이트, 파일 업로드/다운로드 유틸리티, 다양한 언어 SDK 및 웹 브라우저와 같은 외부 서비스에 액세스할 수 있음

HDInsight에 대한 기본 저장소가 된 이후 일반적으로 사용하기 위해 아무런 동작도 수행할 필요가 없습니다. 예를 들어 다음 명령은 Azure Blob 저장소에 저장된 **/예제/데이터** 폴더의 파일을 나열합니다.

	hadoop fs -ls /example/data

일부 명령은 Blob 저장소를 사용할지 지정해야 할 수 있습니다. 이러한 경우 **WASB://**와 함께 명령을 앞에 붙일 수 있습니다.

HDInsight은 클러스터와 여러 개의 Blob 저장소 계정을 연결할 수도 있습니다. 기본이 아닌 Blob 저장소 계정 데이터에 액세스하려면 **WASB://&lt;컨테이너 이름>@&lt;계정 이름 >.blob.core.windows.net/** 형식을 사용할 수 있습니다. 예를 들어 다음은 특정 컨테이너 및 저장소 계정에 대한 **/예제/데이터** 디렉토리 콘텐츠를 나열합니다.

	hadoop fs -ls wasb://mycontainer@mystorage.blob.core.windows.net/example/data

### blob 저장소는 어떤 클러스터를 사용합니까?

클러스터를 만드는 동안 각 기존 저장소 계정 및 컨테이너를 사용하거나 새로 만들 것인지 선택합니다. 그런 다음 잊어버릴 수 있습니다. 다음 메서드를 사용하여 저장소 계정 및 컨테이너를 찾을 수 있습니다.

**Azure 포털**

1. 그런 다음 <a href="https://manage.windowsazure.com/" target="_blank">Azure 관리 포털에서</a>HDInsight 클러스터를 선택합니다.

2. 페이지 상단에서 **대시보드**를 선택합니다.

3. 저장소 계정 및 컨테이너가 페이지의 **연결된 리소스** 섹션에 나열되어 있습니다.

	![linked resources](./media/hdinsight-hadoop-linux-information/storageportal.png)

**Azure 플랫폼간 명령줄 인터페이스**

*서비스 예정!*

### Blob 저장소에 액세스하려면 어떻게 해야 합니까?

클러스터에서 Hadoop 명령을 통하는 것 외에 blob에 액세스할 수 있는 다양한 방법이 있습니다.

* <a href="http://azure.microsoft.com/ documentation/articles/xplat-cli/" target="_blank">Azure 플랫폼간 명령줄 인터페이스</a> - 설치한 후 저장소 사용에 대한 도움말은 `azure storage`를 참조하고 blob 특정 명령에 대한 도움말은 `azure blob` 참조하세요.

* 다양한 SDK:

	* <a href="https://github.com/Azure/azure-sdk-for-java" target="_blank">Java</a>

	* <a href="https://github.com/Azure/azure-sdk-for-node" target="_blank">Node.js</a>

	* <a href="https://github.com/Azure/azure-sdk-for-php" target="_blank">PHP</a>

	* <a href="https://github.com/Azure/azure-sdk-for-python" target="_blank">Python</a>

	* <a href="https://github.com/Azure/azure-sdk-for-ruby" target="_blank">Ruby</a>

	* <a href="https://github.com/Azure/azure-sdk-for-net" target="_blank">.NET</a>

* <a href="https://msdn.microsoft.com/ko-kr/library/azure/dd135733.aspx" target="_blank">저장소 REST API</a>


## 다음 단계

* [HDInsight에서 Hive 사용](../hdinsight-use-hive/)
* [HDInsight에서 Pig 사용](../hdinsight-use-pig/)
* [HDInsight에서 MapReduce 작업 사용](../hdinsight-use-mapreduce)


<!--HONumber=45--> 
