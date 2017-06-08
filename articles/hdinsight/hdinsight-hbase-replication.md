---
title: "HBase 복제 구성 | Microsoft Docs"
description: "부하 분산, 고가용성, 한 버전의 HDInsight에서 다른 버전으로의 무중단 마이그레이션/업데이트 및 재해 복구를 위해 HBase 복제를 구성하는 방법에 대해 알아봅니다."
services: hdinsight,virtual-network
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/25/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: a62cd616f0abd59c83c834bf13b4ba8549a9c73e
ms.openlocfilehash: 83aabe0e5161cd3f94caa996dec29bc020e5308b
ms.contentlocale: ko-kr
ms.lasthandoff: 02/23/2017


---
# <a name="configure-hbase-replication"></a>HBase 복제 구성

한 VNet(가상 네트워크) 내에 또는 두 VNet 간에 HBase 복제를 구성하는 방법에 대해 알아봅니다.

클러스터 복제에서는 원본-푸시 방법을 사용합니다. HBase 클러스터는 원본 또는 대상이 될 수도 있고, 한 번에 두 가지 역할을 모두 수행할 수도 있습니다. 복제는 비동기이며 복제의 목적은 일관성을 유지하는 것입니다. 원본에서 복제를 사용할 수 있는 열 패밀리에 대한 편집 내용을 받은 경우 해당 편집 내용은 모든 대상 클러스터로 전파됩니다. 클러스터 간에 데이터를 복제하는 경우 복제 루프를 방지하기 위해 데이터를 이미 사용한 원본 클러스터 및 모든 클러스터가 추적됩니다.

이 자습서에서는 원본-대상 복제를 구성합니다. 다른 클러스터 토폴로지는 [Apache HBase 참조 가이드](http://hbase.apache.org/book.html#_cluster_replication)를 참조하세요.

단일 가상 네트워크에 대한 HBase 복제 사용 사례:

* 부하 분산 - 예를 들어 대상 클러스터에서 스캔 또는 MapReduce 작업을 실행하고 원본 클러스터에서 데이터를 수집합니다.
* 고가용성
* 하나의 HBase 클러스터에서 다른 HBase 클러스터로 데이터 마이그레이션
* 한 버전에서 다른 버전으로 Azure HDInsight 클러스터 업그레이드

두 가상 네트워크에 대한 HBase 복제 사용 사례:

* 재해 복구
* 응용 프로그램 부하 분산 및 분할
* 고가용성

[GitHub](https://github.com/Azure/hbase-utils/tree/master/replication)에 있는 [스크립트 동작](hdinsight-hadoop-customize-cluster-linux.md) 스크립트를 사용하여 클러스터를 복제합니다.

## <a name="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에 Azure 구독이 있어야 합니다. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

## <a name="configure-the-environments"></a>환경 구성

다음과 같이 세 가지 가능한 구성이 있습니다.

- 1개 Azure 가상 네트워크에 2개 HBase 클러스터 구성
- 동일한 지역에 있는 별도의 2개 가상 네트워크에 2개 HBase 클러스터 구성
- 별도의 2개 지역에 있는 2개 다른 가상 네트워크에 2개 HBase 클러스터 구성(지리적 복제)

환경을 쉽게 구성할 수 있도록 몇 가지 [Azure Resource Manager 템플릿](../azure-resource-manager/resource-group-overview.md)을 만들었습니다. 다른 방법을 사용하여 환경을 구성하려면 다음을 참조하세요.

- [HDInsight에서 Linux 기반 Hadoop 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)
- [Azure Virtual Network에 HBase 클러스터 만들기](hdinsight-hbase-provision-vnet.md)

### <a name="configure-one-virtual-network"></a>1개 가상 네트워크 구성

다음 이미지를 클릭하면 동일한 가상 네트워크에서 두 개의 HBase 클러스터를 만들 수 있습니다. 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-one-vnet/)에 저장됩니다.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-one-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

### <a name="configure-two-virtual-networks-in-the-same-region"></a>동일한 지역에 2개 가상 네트워크 구성

다음 이미지를 클릭하면 동일한 지역에 VNet 피어링과 두 개의 HBase 클러스터가 있는 두 개의 가상 네트워크를 만들 수 있습니다. 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-two-vnets-same-region/)에 저장됩니다.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-two-vnets-same-region%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>



이 시나리오에는 [VNet 피어링](../virtual-network/virtual-network-peering-overview.md)이 필요하며, 템플릿은 VNet 피어링을 사용하도록 설정합니다.   

HBase 복제는 ZooKeeper VM의 IP 주소를 사용합니다. 대상 HBase ZooKeeper 노드에 대해 고정 IP 주소를 구성해야 합니다.

**고정 IP 주소를 구성 하려면**

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **리소스 그룹**을 클릭합니다.
3. 대상 HBase 클러스터가 포함된 리소스 그룹을 클릭합니다. 이 그룹은 Resource Manager 템플릿을 사용하여 환경을 만들 때 지정한 리소스 그룹입니다. 필터를 사용하여 목록의 범위를 좁힐 수 있습니다. 두 개의 가상 네트워크가 포함된 리소스 목록을 볼 수 있습니다.
4. 대상 HBase 클러스터가 포함된 가상 네트워크를 클릭합니다. 예를 들어 **xxxx-vnet2**를 클릭합니다. **nic-zookeepermode-**로 시작하는 이름을 가진 세 개의 장치를 볼 수 있습니다. 이러한 장치는 세 개의 ZooKeeper VM입니다.
5. ZooKeeper VM 중 하나를 클릭합니다.
6. **IP 구성**을 클릭합니다.
7. 목록에서 **ipConfig1**을 클릭합니다.
8. **고정**을 클릭하고 실제 IP 주소를 적어 둡니다. 스크립트 동작을 실행하여 복제를 사용하도록 설정할 때 IP 주소가 필요합니다.

  ![HDInsight HBase 복제 ZooKeeper 고정 IP](./media/hdinsight-hbase-replication/hdinsight-hbase-replication-zookeeper-static-ip.png)

9. 6단계를 반복하여 다른 두 ZooKeeper 노드의 고정 IP 주소를 설정합니다.

VNet 간 시나리오의 경우 **hdi_enable_replication.sh** 스크립트 작업 호출 시 **-ip** 스위치를 사용해야 합니다.

### <a name="configure-two-virtual-networks-in-two-different-regions"></a>별도의 2개 지역에 2개 가상 네트워크 구성

다음 이미지를 클릭하면 두 개의 다른 지역에 두 개의 가상 네트워크를 만들 수 있습니다. 템플릿은 공용 Azure Blob 컨테이너에 저장됩니다.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fdeploy-hbase-geo-replication.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

두 개의 가상 네트워크 간에 VPN 게이트웨이를 만듭니다. 지침은 [사이트 간 연결로 VNet 만들기](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)를 참조하세요.

HBase 복제는 ZooKeeper VM의 IP 주소를 사용합니다. 대상 HBase ZooKeeper 노드에 대해 고정 IP 주소를 구성해야 합니다. 고정 IP를 구성하려면 이 문서의 "동일한 지역에 2개 가상 네트워크 구성" 섹션을 참조하세요.

VNet 간 시나리오의 경우 **hdi_enable_replication.sh** 스크립트 작업 호출 시 **-ip** 스위치를 사용해야 합니다.

## <a name="load-test-data"></a>테스트 데이터 로드

클러스터를 복제할 때 복제할 테이블을 지정해야 합니다. 이 섹션에서는 일부 데이터를 원본 클러스터에 로드합니다. 다음 섹션에서는 두 클러스터 간에 복제를 사용하도록 설정합니다.

[HBase 자습서: HDInsight에서 Linux 기반 Hadoop을 통해 Apache HBase 사용 시작](hdinsight-hbase-tutorial-get-started-linux.md)의 지침에 따라 **연락처** 테이블을 만들고 이 테이블에 데이터를 삽입합니다.

## <a name="enable-replication"></a>복제 사용

다음 단계에서는 Azure Portal에서 스크립트 동작 스크립트를 호출하는 방법을 보여 줍니다. Azure PowerShell과 Azure CLI(명령줄 인터페이스)를 사용하여 스크립트 동작을 실행하려면 [스크립트 동작을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

**Azure Portal에서 HBase 복제를 사용하도록 설정하려면**

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 원본 HBase 클러스터를 엽니다.
3. 클러스터 메뉴에서 **스크립트 동작**을 클릭합니다.
4. 블레이드 위쪽에서 **새로운 항목 제출**을 클릭합니다.
5. 다음 정보를 선택하거나 입력합니다.

  - **이름**: **복제 사용**을 입력합니다.
  - **Bash 스크립트 URL**: **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**를 입력합니다.
  - **헤드**: 선택합니다. 다른 노드 형식은 선택 취소합니다.
  - **매개 변수**: 다음 샘플 매개 변수를 사용하면 기존의 모든 테이블을 복제하도록 설정하고 모든 데이터를 원본 클러스터에서 대상 클러스터로 복사합니다.

            -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata

6. **만들기**를 클릭합니다. 특히 -copydata 인수를 사용하는 경우 스크립트 실행에서 시간이 좀 걸릴 수 있습니다.

필수 인수:

|이름|설명|
|----|-----------|
|-s, --src-cluster | 원본 HBase 클러스터의 DNS 이름을 지정합니다. 예: -s hbsrccluster, --src-cluster=hbsrccluster |
|-d, --dst-cluster | 대상(복제본) HBase 클러스터의 DNS 이름을 지정합니다. 예: -s dsthbcluster, --src-cluster=dsthbcluster |
|-sp, --src-ambari-password | 원본 HBase 클러스터에서 Ambari의 관리자 암호를 지정합니다. |
|-dp, --dst-ambari-password | 대상 HBase 클러스터에서 Ambari의 관리자 암호를 지정합니다.|

선택적 인수:

|이름|설명|
|----|-----------|
|-su, --src-ambari-user | 원본 HBase 클러스터에서 Ambari의 관리자 사용자 이름을 지정합니다. 기본값은 **admin**입니다. |
|-du, --dst-ambari-user | 대상 HBase 클러스터에서 Ambari의 관리자 사용자 이름을 지정합니다. 기본값은 **admin**입니다. |
|-t, --table-list | 복제할 테이블을 지정합니다. 예: --table-list="table1;table2;table3". 테이블을 지정하지 않으면 기존의 모든 HBase 테이블을 복제합니다.|
|-m, --machine | 스크립트 동작이 실행될 헤드 노드를 지정합니다. 값은 hn1 또는 hn0입니다. 대개 hn0이 더 바쁘기 때문에 hn1을 사용하는 것이 좋습니다. HDInsight 포털 또는 Azure PowerShell에서 $0 스크립트를 스크립트 동작으로 실행하는 경우 이 옵션을 사용합니다.|
|-ip | 이 인수는 두 개의 가상 네트워크 간에 복제를 사용할 때 필요하며, FQDN 이름 대신 복제본 클러스터에서 ZooKeeper 노드의 고정 IP를 사용하는 스위치 역할을 합니다. 고정 IP는 복제를 사용하도록 설정하기 전에 미리 구성해야 합니다. |
|-cp, -copydata | 복제가 사용되는 테이블에서 기존 데이터의 마이그레이션을 사용하도록 설정합니다. |
|-rpm, -replicate-phoenix-meta | Phoenix 시스템 테이블에서 복제를 사용하도록 설정합니다. <br><br>*이 옵션은 주의해서 사용해야 합니다.* 이 스크립트를 사용하기 전에 복제본 클러스터에서 Phoenix 테이블을 다시 만드는 것이 좋습니다. |
|-h, --help | 사용 정보를 표시합니다. |

[스크립트](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh)의 print_usage() 섹션은 매개 변수에 대한 자세한 설명을 제공합니다.

스크립트 동작이 성공적으로 배포된 후에 SSH를 사용하면 대상 HBase 클러스터에 연결하고 데이터가 복제되었는지 확인할 수 있습니다.

### <a name="replication-scenarios"></a>복제 시나리오

다음 목록에서는 일반적인 사용 사례 및 해당 매개 변수 설정을 보여 줍니다.

- **두 클러스터 간의 모든 테이블에서 복제를 사용하도록 설정** - 이 시나리오에서는 기존 데이터를 테이블에 복사/마이그레이션할 필요가 없으며 Phoenix 테이블을 사용하지 않습니다. 다음 매개 변수를 사용합니다.

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **특정 테이블에서 복제를 사용하도록 설정** - table1, table2 및 table3에서 복제를 사용하도록 설정하려면 다음 매개 변수를 사용합니다.

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **특정 테이블에서 복제를 사용하도록 설정 및 기존 데이터 복사** - table1, table2 및 table3에서 복제를 사용하도록 설정하려면 다음 매개 변수를 사용합니다.

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **원본에서 대상으로 phoenix 메타데이터를 복제하여 모든 테이블에서 복제를 사용하도록 설정** - Phoenix 메타데이터 복제는 완벽하지 않으므로 주의해서 사용해야 합니다.

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>데이터 복사 및 마이그레이션

복제를 사용하도록 설정한 후에 데이터를 복사/마이그레이션하기 위한 두 개의 스크립트 동작 스크립트가 별도로 있습니다.

- [소형 테이블용 스크립트](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) - 수 GB 크기, 전체 복사 완료에 1시간 이내 소요

- [대형 테이블용 스크립트](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) - 복사 완료에 1시간 이상 소요

[복제 사용](#enable-replication)과 동일한 절차에 따라 다음 매개 변수를 사용하여 스크립트 동작을 호출할 수 있습니다.

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

[스크립트](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh)의 print_usage() 섹션은 매개 변수에 대한 자세한 설명을 제공합니다.

### <a name="scenarios"></a>시나리오

- **지금(현재 타임스탬프)까지 편집된 모든 행에 대한 특정 테이블(test1, test2 및 test3) 복사**:

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  또는

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **지정된 시간 범위의 특정 테이블 복사**:

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>복제 사용 안 함

복제를 사용하지 않도록 설정하려면 [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh)에 있는 다른 스크립트 동작 스크립트를 사용합니다. [복제 사용](#enable-replication)과 동일한 절차에 따라 다음 매개 변수를 사용하여 스크립트 동작을 호출할 수 있습니다.

    -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari Password> <-all|-t "table1;table2;...">  

[스크립트](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh)의 print_usage() 섹션은 매개 변수에 대한 자세한 설명을 제공합니다.

### <a name="scenarios"></a>시나리오

- **모든 테이블에서 복제를 사용하지 않도록 설정**:

        -m hn1 -s <source cluster DNS name> -sp Mypassword\!789 -all
  또는

        --src-cluster=<source cluster DNS name> --dst-cluster=<destination cluster DNS name> --src-ambari-user=<source cluster Ambari username> --src-ambari-password=<source cluster Ambari password>

- **지정된 테이블(table1, table2 및 table3)에서 복제를 사용하지 않도록 설정**:

        -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>다음 단계

이 자습서에서는 두 데이터 센터에서 HBase 복제를 구성하는 방법을 살펴보았습니다. HDInsight 및 HBase에 대한 자세한 내용은 다음을 참조하세요.

* [HDInsight에서 Apache HBase 시작][hdinsight-hbase-get-started]
* [HDInsight HBase 개요][hdinsight-hbase-overview]
* [Azure Virtual Network에 HBase 클러스터 만들기][hdinsight-hbase-provision-vnet]
* [HBase를 사용하여 Twitter 데이터 실시간 분석][hdinsight-hbase-twitter-sentiment]
* [HDInsight(Hadoop)에서 Apache Storm 및 HBase를 사용하여 센서 데이터 분석][hdinsight-sensor-data]

[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[hdinsight-hbase-geo-replication-dns]: ../hdinsight-hbase-geo-replication-configure-VNet.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication/HDInsight.HBase.Replication.Network.diagram.png

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started-linux.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[hdinsight-sensor-data]: hdinsight-storm-sensor-data-analysis.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

