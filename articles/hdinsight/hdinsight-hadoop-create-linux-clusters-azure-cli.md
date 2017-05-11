---
title: "명령줄을 사용하여 Azure HDInsight(Hadoop) 만들기 | Microsoft Docs"
description: "플랫폼 간 Azure CLI 1.0을 사용하여 HDInsight 클러스터를 만드는 방법을 알아봅니다."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 50b01483-455c-4d87-b754-2229005a8ab9
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/04/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: 269294f14dd4add5ab038f13b6818db345665c3b
ms.contentlocale: ko-kr
ms.lasthandoff: 05/03/2017


---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Azure CLI를 사용하여 HDInsight 클러스터 만들기

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

이 문서의 단계는 Azure CLI 1.0을 사용하여 HDInsight 3.5 클러스터 만들기 과정을 연습합니다.

> [!IMPORTANT]
> Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [HDInsight 3.2 및 3.3 사용 중단](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date)을 참조하세요.


## <a name="prerequisites"></a>필수 조건

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

* **Azure CLI**. 이 문서의 단계는 Azure CLI 버전 0.10.1로 마지막 테스트되었습니다.

    > [!IMPORTANT]
    > 이 문서의 단계는 Azure CLI 2.0에서 작동하지 않습니다. Azure CLI 2.0은 HDInsight 클러스터 만들기를 지원하지 않습니다.

## <a name="log-in-to-your-azure-subscription"></a>Azure 구독에 로그인합니다.

[Azure CLI(Azure 명령줄 인터페이스)에서 Azure 구독에 연결](../xplat-cli-connect.md) 에서 설명된 단계에 따라 **login** 메서드를 사용하여 구독에 연결합니다.

## <a name="create-a-cluster"></a>클러스터 만들기

다음 단계는 Azure CLI를 설치하고 구성한 후 명령 프롬프트, 셸 또는 터미널 세션에서 수행되어야 합니다.

1. 다음 명령을 사용하여 Azure 구독에 인증합니다.

        azure login

    사용자 이름 및 암호를 제공하라는 메시지가 표시됩니다. 여러 Azure 구독이 있는 경우 `azure account set <subscriptionname>` 을 사용하여 Azure CLI 명령이 사용할 구독을 설정합니다.

2. 다음 명령을 사용하여 Azure 리소스 관리자 모드로 전환합니다.

        azure config mode arm

3. 리소스 그룹을 만듭니다. 이 리소스 그룹에는 HDInsight 클러스터 및 연결된 저장소 계정이 포함됩니다.

        azure group create groupname location

    * **groupname** 을 그룹의 고유한 이름으로 바꿉니다.

    * **location** 을 그룹을 만들 지리적 지역으로 바꿉니다.

       유효한 위치 목록에 대해서는 `azure location list` 명령을 사용하고 **이름** 열의 위치 중 하나를 사용합니다.

4. 저장소 계정을 만듭니다. 이 저장소 계정은 HDInsight 클러스터에 대한 기본 저장소로 사용됩니다.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename

    * **groupname** 을 이전 단계에서 만든 그룹의 이름으로 바꿉니다.

    * **location** 을 이전 단계에서 사용된 동일한 위치로 바꿉니다.

    * **storagename** 을 저장소 계정의 고유한 이름으로 바꿉니다.

        > [!NOTE]
        > 이 명령에서 사용된 매개 변수에 대한 자세한 내용을 보려면 `azure storage account create -h`를 사용하여 이 명령에 대한 도움말을 표시합니다.

5. 저장소 계정에 액세스하는 데 사용된 키를 검색합니다.

        azure storage account keys list -g groupname storagename

    * **groupname** 을 리소스 그룹 이름으로 바꿉니다.
    * **storagename** 을 저장소 계정의 이름으로 바꿉니다.

     반환된 데이터에서 **key1**의 **키** 값을 저장합니다.

6. HDInsight 클러스터 만들기

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 2 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * **groupname** 을 리소스 그룹 이름으로 바꿉니다.

    * **Hadoop**을 만들려는 클러스터 유형으로 대체합니다. 예를 들면 Hadoop, HBase, Storm 또는 Spark가 있습니다.

     > [!IMPORTANT]
     > HDInsight 클러스터는 워크로드 또는 클러스터에 대한 튜닝 기술에 해당하는 다양한 형식을 제공합니다. 하나의 클러스터에서 Storm 및 HBase 등의 여러 유형을 결합하는 클러스터를 만들기 위해 지원되는 메서드가 없습니다.

    * **location** 을 이전 단계에서 사용된 동일한 위치로 바꿉니다.

    * **storagename** 을 저장소 계정 이름으로 바꿉니다.

    * **storagekey** 를 이전 단계에서 얻은 키로 바꿉니다.

    * `--defaultStorageContainer` 매개 변수의 경우 클러스터에 사용하는 것과 같은 이름을 사용합니다.

    * **admin** 및 **httppassword**를 HTTPS를 통해 클러스터에 액세스할 때 사용할 이름 및 암호로 바꿉니다.

    * **sshuser** 및 **sshuserpassword**를 SSH를 사용하여 클러스터에 액세스할 때 사용할 사용자 이름 및 암호로 바꿉니다.

    > [!IMPORTANT]
    > 이 예제에서는 2개의 작업자 노드를 사용하여 클러스터를 만듭니다. 클러스터를 만드는 동안 또는 클러스터를 확장하여 32개 이상의 작업자 노드를 계획하는 경우 최소한 코어 8개와 14GB RAM을 가진 헤드 노드 크기를 선택해야 합니다. `--headNodeSize` 매개 변수를 사용하여 헤드 노드 크기를 설정할 수 있습니다.
    >
    > 노드 크기 및 관련된 비용에 대한 자세한 내용은 [HDInsight 가격 책정](https://azure.microsoft.com/pricing/details/hdinsight/)을 참조하세요.

    클러스터 생성 프로세스를 완료하는 데 몇 분 정도 걸릴 수 있습니다. 일반적으로 약 15분이 걸립니다.

## <a name="troubleshoot"></a>문제 해결

HDInsight 클러스터를 만드는 동안 문제가 발생할 경우 [액세스 제어 요구 사항](hdinsight-administer-use-portal-linux.md#create-clusters)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure CLI를 사용하여 HDInsight 클러스터를 정상적으로 만들었으므로 다음을 사용하여 클러스터 작업을 수행하는 방법을 알아봅니다.

### <a name="hadoop-clusters"></a>Hadoop 클러스터

* [HDInsight에서 Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Pig 사용](hdinsight-use-pig.md)
* [HDInsight와 함께 MapReduce 사용](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>HBase 클러스터

* [HDInsight에서 HBase 시작](hdinsight-hbase-tutorial-get-started-linux.md)
* [HDInsight에서 HBase용 Java 응용 프로그램 개발](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm 클러스터

* [HDInsight에서 Storm용 Java 토폴로지 개발](hdinsight-storm-develop-java-topology.md)
* [HDInsight의 Storm에서 Python 구성 요소 사용](hdinsight-storm-develop-python-topology.md)
* [HDInsight에서 Storm을 사용하는 토폴로지 배포 및 모니터링](hdinsight-storm-deploy-monitor-topology-linux.md)

