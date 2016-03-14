<properties
   	pageTitle="플랫폼 간 Azure CLI를 사용하여 HDInsight에서 Linux에 Hadoop, HBase 또는 Storm 클러스터 만들기 | Microsoft Azure"
   	description="플랫폼 간 Azure CLI, Azure 리소스 관리자 템플릿 및 Azure REST API를 사용하여 Linux 기반 HDInsight 클러스터를 만드는 방법을 알아봅니다. 클러스터 유형(Hadoop, HBase 또는 Storm)을 지정하거나 스크립트를 사용하여 사용자 지정 구성 요소를 설치할 수 있습니다."
   	services="hdinsight"
   	documentationCenter=""
   	authors="Blackmist"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="02/29/2016"
   	ms.author="larryfr"/>

#Azure CLI를 사용하여 HDInsight에서 Linux 기반 클러스터 만들기

[AZURE.INCLUDE [선택기](../../includes/hdinsight-selector-create-clusters.md)]

Azure CLI는 Azure 서비스를 관리할 수 있도록 하는 크로스 플랫폼 명령줄 유틸리티입니다. Azure 리소스 관리 템플릿과 함께 사용하여 HDInsight 클러스터를 연결된 저장소 계정 및 기타 서비스와 함께 만들 수 있습니다.

Azure 리소스 관리 템플릿은 __리소스 그룹__ 및 그 안에 모든 리소스를 설명하는 JSON 문서입니다.(예: HDInsight) 이 템플릿 기반 접근 방식을 사용하면 HDInsight에 필요한 모든 리소스를 하나의 템플릿에 정의하고 그룹에 변경 내용을 적용하는 __배포__를 통해 그룹에 대한 변경 내용을 전체적으로 관리할 수 있습니다.

이 문서의 단계는 Azure CLI 및 템플릿을 사용하여 새 HDInsight 클러스터를 만드는 과정을 안내합니다.

> [AZURE.IMPORTANT] 이 문서의 단계는 HDInsight 클러스터에 대해 작업자 노드 (4)의 기본 갯수를 사용합니다. 클러스터 만들기에서 또는 클러스터를 만든 후 확장하여 32개 이상의 작업자 노드를 계획하는 경우 최소한 코어 8개와 14GB RAM을 가진 헤드 노드 크기를 선택해야 합니다.
>
> 노드 크기 및 관련된 비용에 대한 자세한 내용은 [HDInsight 가격 책정](https://azure.microsoft.com/pricing/details/hdinsight/)을 참조하세요.

##필수 조건

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

- __Azure CLI__. CLI를 설치하는 방법에 대한 자세한 내용은 [Azure CLI 설치](../xplat-cli-install.md)를 참조하세요.

##Azure 구독에 로그인

[Azure CLI(Azure 명령줄 인터페이스)에서 Azure 구독에 연결](../xplat-cli-connect.md)에서 설명된 단계를 따라 __로그인__ 방법을 사용하여 구독에 연결합니다.

##클러스터 만들기

다음 단계는 Azure CLI를 설치하고 구성한 후 명령 프롬프트, 셸 또는 터미널 세션에서 수행되어야 합니다.

1. 다음 명령을 사용하여 Azure 구독에 인증합니다.

        azure login

    사용자 이름 및 암호를 제공하라는 메시지가 표시됩니다. 여러 Azure 구독이 있는 경우 `azure account set <subscriptionname>`을 사용하여 Azure CLI 명령을 사용하는 구독을 설정할 수 있습니다.

3. 다음 명령을 사용하여 Azure 리소스 관리자 모드로 전환합니다.

        azure config mode arm

4. HDInsight 클러스터에 대한 템플릿을 만듭니다. 다음은 기본 예제 템플릿 일부입니다.

    * [SSH 공개 키를 사용하는 Linux 기반 클러스터](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-publickey)
    * [SSH 계정에 암호를 사용하는 Linux 기반 클러스터](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password)

    또한 이러한 템플릿은 모두 HDInsight에서 사용되는 기본 Azure 저장소 계정을 만듭니다.

    필요한 파일은 __azuredeploy.json__ 및 __azuredeploy.parameters.json__입니다. 계속하려면 이러한 파일을 로컬로 복사합니다.

5. 편집기에서 __azuredeploy.parameters.json__를 열고 `parameters` 섹션에서 항목에 대한 값을 제공합니다.

    * __위치__: 리소스가 생성된 데이터 센터입니다. 허용되는 위치의 목록에 __azuredeploy.json__ 파일에서 `location` 섹션을 확인할 수 있습니다.
    * __clusterName__: HDInsight 클러스터의 이름입니다. 이 이름은 고유해야 합니다. 아니면 배포가 실패합니다.
    * __clusterStorageAccountName__: HDInsight 클러스터에 생성되는 Azure 저장소 계정의 이름입니다. 이 이름은 고유해야 합니다. 아니면 배포가 실패합니다.
    * __clusterLoginPassword__: 클러스터 관리자 사용자의 암호입니다. 클러스터에서 웹 사이트 및 REST 서비스에 액세스하는 데 사용되기 때문에 안전한 암호여야 합니다.
    * __sshUserName__: 클러스터에 만들 첫 번째 SSH 사용자의 이름입니다. 이 계정을 사용하여 클러스터에 원격으로 액세스하는 데 SSH를 사용합니다.
    * __sshPublicKey__: SSH 공개 키를 필요로 하는 템플릿을 사용하는 경우 이 줄에서 공용 키를 추가해야 합니다. 공용 키로 생성하고 작업하는 자세한 내용은 다음 문서를 참조하세요.

        * [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)
        * [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)

    * __sshPassword__: SSH 암호를 필요로 하는 템플릿을 사용하는 경우 이 줄에 암호를 추가해야 합니다.

    작업을 완료하면 파일을 저장하고 닫습니다.

5. 다음을 사용하여 빈 리소스 그룹을 만듭니다. __RESOURCEGROUPNAME__을 이 그룹에 사용하려는 이름으로 바꿉니다. __LOCATION__을 HDInsight 클러스터를 만들려는 데이터센터로 바꿉니다.

        azure group create RESOURCEGROUPNAME LOCATION

    > [AZURE.NOTE] 위치 이름이 공백을 포함하는 경우 이중 따옴표로 묶습니다. 예를 들어 "미국 중남부"입니다.

6. 다음 명령을 사용하여 이 리소스 그룹에 초기 배포를 만듭니다. __PATHTOTEMPLATE__를 __azuredeploy.json__ 템플릿 파일에 대한 경로로 바꿉니다. __PATHTOPARAMETERSFILE__를 __azuredeploy.parameters.json__ 파일에 대한 경로로 바꿉니다. __RESOURCEGROUPNAME__를 이전 단계에서 만든 그룹의 이름으로 바꿉니다.

        azure group deployment create -f PATHTOTEMPLATE -e PATHTOPARAMETERSFILE -g RESOURCEGROUPNAME -n InitialDeployment

    배포가 수락되면 `group deployment create command ok`과 유사한 메시지가 나타납니다.

7. 배포를 완료하려면 약 15분 정도의 시간이 걸릴 수 있습니다. 다음 명령을 사용하여 배포에 대한 정보를 볼 수 있습니다. __RESOURCEGROUPNAME__를 이전 단계에서 사용한 리소스 그룹의 이름으로 바꿉니다.

        azure group log show -l RESOURCEGROUPNAME

    배포가 완료되면 __상태__ 필드에 __성공__ 값이 포함됩니다. 배포 중에 오류가 발생하는 경우 다음 명령을 사용하여 오류에 대한 자세한 정보를 얻을 수 있습니다.

        azure group log show -l -v RESOURCEGROUPNAME

##다음 단계

HDInsight 클러스터를 성공적으로 만들었으므로 다음을 사용하여 클러스터 작업을 수행하는 방법을 알아봅니다.

###Hadoop 클러스터

* [HDInsight에서 Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Pig 사용](hdinsight-use-pig.md)
* [HDInsight와 함께 MapReduce 사용](hdinsight-use-mapreduce.md)

###HBase 클러스터

* [HDInsight에서 HBase 시작](hdinsight-hbase-tutorial-get-started-linux.md)
* [HDInsight에서 HBase용 Java 응용 프로그램 개발](hdinsight-hbase-build-java-maven-linux.md)

###Storm 클러스터

* [HDInsight에서 Storm용 Java 토폴로지 개발](hdinsight-storm-develop-java-topology.md)
* [HDInsight의 Storm에서 Python 구성 요소 사용](hdinsight-storm-develop-python-topology.md)
* [HDInsight에서 Storm을 사용하는 토폴로지 배포 및 모니터링](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---HONumber=AcomDC_0302_2016-->