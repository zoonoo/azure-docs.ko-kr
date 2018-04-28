---
title: Azure 가상 네트워크에서 HBase 클러스터 복제 설정 | Microsoft Docs
description: 부하 분산, 고가용성, 무중단 마이그레이션/업데이트 및 재해 복구를 위해 한 HDInsight 버전에서 다른 HDInsight 버전으로 HBase 복제를 설정하는 방법을 알아봅니다.
services: hdinsight,virtual-network
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/03/2018
ms.author: jgao
ms.openlocfilehash: c28c48b5842deec9d9c3898c5742c3d4d473094e
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="set-up-hbase-cluster-replication-in-azure-virtual-networks"></a>Azure 가상 네트워크에서 HBase 클러스터 복제 설정

Azure에서 한 가상 네트워크 내에 또는 두 가상 네트워크 간에 HBase 복제를 설정하는 방법에 대해 알아봅니다.

클러스터 복제에서는 원본-푸시 방법을 사용합니다. HBase 클러스터는 원본 또는 대상이 될 수도 있고, 한 번에 두 가지 역할을 모두 수행할 수도 있습니다. 복제는 비동기적이며, 그 목표는 결과적으로 일관성에 맞춰져 있습니다. 복제를 사용하도록 설정된 경우 원본에서 열 패밀리에 대한 편집 내용을 받으면 해당 편집 내용이 모든 대상 클러스터로 전파됩니다. 한 클러스터에서 다른 클러스터로 데이터를 복제하는 경우 복제 루프를 방지하기 위해 데이터를 이미 사용한 원본 클러스터 및 모든 클러스터가 추적됩니다.

이 자습서에서는 원본-대상 복제를 설정합니다. 다른 클러스터 토폴로지는 [Apache HBase 참조 가이드(영문)](http://hbase.apache.org/book.html#_cluster_replication)를 참조하세요.

단일 가상 네트워크에 대한 HBase 복제 사용 사례는 다음과 같습니다.

* 부하 분산 - 예를 들어 대상 클러스터에서 검색 또는 MapReduce 작업을 실행하고 원본 클러스터에서 데이터를 수집할 수 있습니다.
* 고가용성 추가
* 하나의 HBase 클러스터에서 다른 HBase 클러스터로 데이터 마이그레이션
* 한 버전에서 다른 버전으로 Azure HDInsight 클러스터 업그레이드

두 가상 네트워크에 대한 HBase 복제 사용 사례는 다음과 같습니다.

* 재해 복구 설정
* 응용 프로그램 부하 분산 및 분할
* 고가용성 추가

[GitHub](https://github.com/Azure/hbase-utils/tree/master/replication)에 있는 [스크립트 동작](../hdinsight-hadoop-customize-cluster-linux.md) 스크립트를 사용하여 클러스터를 복제할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에 Azure 구독이 있어야 합니다. [Azure 평가판 받기](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)를 참조하세요.

## <a name="set-up-the-environments"></a>환경 설정

세 가지 구성 옵션이 있습니다.

- 1개 Azure 가상 네트워크에 2개 HBase 클러스터 구성
- 동일한 지역에 있는 별도의 2개 가상 네트워크에 2개 HBase 클러스터 구성
- 별도의 2개 지역에 있는 별도의 2개 가상 네트워크에 2개 HBase 클러스터 구성(지역에서 복제)

환경 설정을 지원하기 위해 몇 가지 [Azure Resource Manager 템플릿](../../azure-resource-manager/resource-group-overview.md)을 만들었습니다. 다른 방법을 사용하여 환경을 설정하려면 다음을 참조하세요.

- [HDInsight에서 Hadoop 클러스터 만들기](../hdinsight-hadoop-provision-linux-clusters.md)
- [Azure Virtual Network에 HBase 클러스터 만들기](apache-hbase-provision-vnet.md)

### <a name="set-up-one-virtual-network"></a>단일 가상 네트워크 설정

동일한 가상 네트워크에서 두 개의 HBase 클러스터를 만들려면 다음 이미지를 클릭하세요. 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-one-vnet/)에 저장됩니다.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-one-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

### <a name="set-up-two-virtual-networks-in-the-same-region"></a>동일한 지역에 2개 가상 네트워크 설정

동일한 지역에 가상 네트워크 피어링과 두 개의 HBase 클러스터가 있는 두 개의 가상 네트워크를 만들려면 다음 이미지를 클릭하세요. 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-two-vnets-same-region/)에 저장됩니다.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-two-vnets-same-region%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>



이 시나리오에는 [가상 네트워크 피어링](../../virtual-network/virtual-network-peering-overview.md)이 필요합니다. 템플릿을 사용하면 가상 네트워크 피어링을 사용할 수 있습니다.   

HBase 복제는 ZooKeeper VM의 IP 주소를 사용합니다. 대상 HBase ZooKeeper 노드에 대한 고정 IP 주소를 설정해야 합니다.

**고정 IP 주소를 구성 하려면**

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
3. HBase 대상 클러스터가 포함된 리소스 그룹을 선택합니다. 이 그룹은 Resource Manager 템플릿을 사용하여 환경을 만들 때 지정한 리소스 그룹입니다. 필터를 사용하여 목록의 범위를 좁힐 수 있습니다. 두 가상 네트워크가 포함된 리소스 목록을 볼 수 있습니다.
4. HBase 대상 클러스터가 포함된 가상 네트워크를 선택합니다. 예를 들어 **xxxx-vnet2**를 선택합니다. **nic-zookeepermode-** 로 시작하는 이름을 가진 세 개의 장치가 나열됩니다. 이러한 장치는 세 개의 ZooKeeper VM입니다.
5. ZooKeeper VM 중 하나를 선택합니다.
6. **IP 구성**을 선택합니다.
7. 목록에서 **ipConfig1**을 선택합니다.
8. **고정**을 선택하고 실제 IP 주소를 복사하거나 적어 둡니다. 스크립트 동작을 실행하여 복제를 사용하도록 설정할 때 IP 주소가 필요합니다.

  ![HDInsight HBase 복제 ZooKeeper 고정 IP 주소](./media/apache-hbase-replication/hdinsight-hbase-replication-zookeeper-static-ip.png)

9. 6단계를 반복하여 다른 두 ZooKeeper 노드의 고정 IP 주소를 설정합니다.

가상 네트워크 간 시나리오에서는 `hdi_enable_replication.sh` 스크립트 동작을 호출할 때 **-ip** 스위치를 사용해야 합니다.

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>별도의 2개 지역에 2개 가상 네트워크 설정

두 개의 다른 지역에 두 개의 가상 네트워크를 만들고 VNet 간에 VPN 연결을 만들려면 다음 이미지를 클릭합니다. 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/)에 저장됩니다.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-geo%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

템플릿의 하드 코드된 일부 값:

**VNet 1**

| 자산 | 값 |
|----------|-------|
| 위치 | 미국 서부 |
| VNet 이름 | &lt;ClusterNamePrevix>-vnet1 |
| 주소 공간 접두사 | 10.1.0.0/16 |
| 서브넷 이름 | 서브넷 1 |
| 서브넷 접두사 | 10.1.0.0/24 |
| 서브넷(게이트웨이) 이름 | GatewaySubnet(변경할 수 없음) |
| 서브넷(게이트웨이) 접두사 | 10.1.255.0/27 |
| 게이트웨이 이름 | vnet1gw |
| 게이트웨이 유형 | Vpn |
| 게이트웨이 VPN 유형 | 경로 기반 |
| 게이트웨이 SKU | Basic |
| 게이트웨이 IP | vnet1gwip |
| 클러스터 이름 | &lt;ClusterNamePrefix>1 |
| 클러스터 버전 | 3.6 |
| 클러스터 종류 | HBase: |
| 클러스터 작업자 노드 수 | 2 |


**VNet 2**

| 자산 | 값 |
|----------|-------|
| 위치 | 미국 동부 |
| VNet 이름 | &lt;ClusterNamePrevix>-vnet2 |
| 주소 공간 접두사 | 10.2.0.0/16 |
| 서브넷 이름 | 서브넷 1 |
| 서브넷 접두사 | 10.2.0.0/24 |
| 서브넷(게이트웨이) 이름 | GatewaySubnet(변경할 수 없음) |
| 서브넷(게이트웨이) 접두사 | 10.2.255.0/27 |
| 게이트웨이 이름 | vnet2gw |
| 게이트웨이 유형 | Vpn |
| 게이트웨이 VPN 유형 | 경로 기반 |
| 게이트웨이 SKU | Basic |
| 게이트웨이 IP | vnet1gwip |
| 클러스터 이름 | &lt;ClusterNamePrefix>2 |
| 클러스터 버전 | 3.6 |
| 클러스터 종류 | HBase: |
| 클러스터 작업자 노드 수 | 2 |

HBase 복제는 ZooKeeper VM의 IP 주소를 사용합니다. 대상 HBase ZooKeeper 노드에 대한 고정 IP 주소를 설정해야 합니다. 고정 IP를 설정하려면 이 문서의 [동일한 지역에 2개 가상 네트워크 설정](#set-up-two-virtual-networks-in-the-same-region) 섹션을 참조하세요.

가상 네트워크 간 시나리오에서는 `hdi_enable_replication.sh` 스크립트 동작을 호출할 때 **-ip** 스위치를 사용해야 합니다.

## <a name="load-test-data"></a>테스트 데이터 로드

클러스터를 복제할 때 복제하려는 테이블을 지정해야 합니다. 이 섹션에서는 일부 데이터를 원본 클러스터에 로드합니다. 다음 섹션에서는 두 클러스터 간에 복제를 사용하도록 설정합니다.

[Contacts(연락처)](apache-hbase-tutorial-get-started-linux.md) 테이블을 만들고 이 테이블에 데이터를 삽입하려면 **HBase 자습서: HDInsight의 Apache HBase 사용 시작**의 지침을 따릅니다.

## <a name="enable-replication"></a>복제 사용

다음 단계에서는 Azure Portal에서 스크립트 동작 스크립트를 호출하는 방법을 설명합니다. Azure PowerShell과 Azure CLI(명령줄 도구)를 사용하여 스크립트 동작을 실행하는 방법에 대한 자세한 내용은 [스크립트 동작을 사용하여 HDInsight 클러스터 사용자 지정](../hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

**Azure Portal에서 HBase 복제를 사용하도록 설정하려면**

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 원본 HBase 클러스터를 엽니다.
3. 클러스터 메뉴에서 **스크립트 동작**을 선택합니다.
4. 페이지의 위쪽에서 **새로운 항목 제출**을 선택합니다.
5. 다음 정보를 선택하거나 입력합니다.

  1. **이름**: **복제 사용**을 입력합니다.
  2. **Bash 스크립트 URL**: **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**를 입력합니다.
  3.  **헤드**: 이 URL이 선택되어 있는지 확인합니다. 다른 노드 형식은 선택 취소합니다.
  4. **매개 변수**: 다음 샘플 매개 변수를 사용하면 기존의 모든 테이블을 복제하도록 설정한 다음, 모든 데이터를 원본 클러스터에서 대상 클러스터로 복사합니다.

          -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata
    
    >[!note]
    >
    > 원본 및 대상 클러스터 DNS 이름에 대한 FQDN 대신 호스트 이름을 사용합니다.

6. **만들기**를 선택합니다. 특히 **-copydata** 인수를 사용하는 경우 스크립트를 실행하는 데 약간의 시간이 걸릴 수 있습니다.

필수 인수:

|Name|설명|
|----|-----------|
|-s, --src-cluster | HBase 원본 클러스터의 DNS 이름을 지정합니다. 예: -s hbsrccluster, --src-cluster=hbsrccluster |
|-d, --dst-cluster | HBase 대상(복제본) 클러스터의 DNS 이름을 지정합니다. 예: -s dsthbcluster, --src-cluster=dsthbcluster |
|-sp, --src-ambari-password | HBase 원본 클러스터에서 Ambari의 관리자 암호를 지정합니다. |
|-dp, --dst-ambari-password | HBase 대상 클러스터에서 Ambari의 관리자 암호를 지정합니다.|

선택적 인수:

|Name|설명|
|----|-----------|
|-su, --src-ambari-user | HBase 원본 클러스터에서 Ambari에 대한 관리 사용자 이름을 지정합니다. 기본값은 **admin**입니다. |
|-du, --dst-ambari-user | HBase 대상 클러스터에서 Ambari에 대한 관리 사용자 이름을 지정합니다. 기본값은 **admin**입니다. |
|-t, --table-list | 복제할 테이블을 지정합니다. 예: --table-list="table1;table2;table3". 테이블을 지정하지 않으면 기존의 모든 HBase 테이블을 복제합니다.|
|-m, --machine | 스크립트 동작이 실행되는 헤드 노드를 지정합니다. 값은 **hn1** 또는 **hn0**입니다. 일반적으로 **hn0** 헤드 노드의 사용량이 많기 때문에 **hn1**을 사용하는 것이 좋습니다. HDInsight 포털 또는 Azure PowerShell에서 $0 스크립트를 스크립트 동작으로 실행하는 경우 이 옵션을 사용합니다.|
|-ip | 이 인수는 두 개의 가상 네트워크 간에 복제를 사용할 때 필요하며, FQDN 이름 대신 복제본 클러스터에서 ZooKeeper 노드의 고정 IP 주소를 사용하는 스위치 역할을 합니다. 복제를 사용하도록 설정하기 전에 고정 IP 주소를 미리 구성해야 합니다. |
|-cp, -copydata | 복제가 사용되는 테이블에서 기존 데이터의 마이그레이션을 사용하도록 설정합니다. |
|-rpm, -replicate-phoenix-meta | Phoenix 시스템 테이블에서 복제를 사용하도록 설정합니다. <br><br>*이 옵션은 주의해서 사용해야 합니다.* 이 스크립트를 사용하기 전에 복제본 클러스터에서 Phoenix 테이블을 다시 만드는 것이 좋습니다. |
|-h, --help | 사용 정보를 표시합니다. |

[스크립트](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh)의 `print_usage()` 섹션에는 매개 변수에 대한 자세한 설명이 있습니다.

스크립트 동작이 성공적으로 배포된 후에 SSH를 사용하여 HBase 대상 클러스터에 연결한 다음 데이터가 복제되었는지 확인할 수 있습니다.

### <a name="replication-scenarios"></a>복제 시나리오

다음 목록에서는 일반적인 사용 사례 및 해당 매개 변수 설정을 보여 줍니다.

- **두 클러스터 간의 모든 테이블에서 복제를 사용하도록 설정** - 이 시나리오에서는 테이블의 기존 데이터를 복사하거나 마이그레이션할 필요가 없으며 Phoenix 테이블을 사용하지 않습니다. 다음 매개 변수를 사용합니다.

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **특정 테이블에서 복제를 사용하도록 설정** - table1, table2 및 table3에서 복제를 사용하도록 설정하려면 다음 매개 변수를 사용합니다.

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **특정 테이블에서 복제를 사용하도록 설정하고 기존 데이터 복사** - table1, table2 및 table3에서 복제를 사용하도록 설정하려면 다음 매개 변수를 사용합니다.

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **모든 테이블에서 복제를 사용하도록 설정하고 원본에서 대상으로 Phoenix 메타데이터 복제** - Phoenix 메타데이터 복제는 완벽하지 않으므로 주의해서 사용하세요. 다음 매개 변수를 사용합니다.

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>데이터 복사 및 마이그레이션

복제를 사용하도록 설정한 후에 데이터를 복사하거나 마이그레이션하는 데 사용할 수 있는 별도의 두 가지 스크립트 동작 스크립트가 있습니다.

- [소형 테이블용 스크립트](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh)(크기가 수 GB이고, 전체 복사를 완료하는 데 1시간 미만이 걸릴 것으로 예상되는 테이블)

- [대형 테이블용 스크립트](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh)(복사하는 데 1시간 이상 걸릴 것으로 예상되는 테이블)

[복제 사용](#enable-replication)에서 설명한 것과 동일한 절차에 따라 스크립트 동작을 호출할 수 있습니다. 다음 매개 변수를 사용합니다.

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

[스크립트](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh)의 `print_usage()` 섹션에는 매개 변수에 대한 자세한 설명이 있습니다.

### <a name="scenarios"></a>시나리오

- **지금(현재 타임스탬프)까지 편집된 모든 행에 대한 특정 테이블(test1, test2 및 test3) 복사**:

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  또는

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **지정된 시간 범위의 특정 테이블 복사**:

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>복제 사용 안 함

복제를 사용하지 않도록 설정하려면 [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh)에 있는 다른 스크립트 동작 스크립트를 사용합니다. [복제 사용](#enable-replication)에서 설명한 것과 동일한 절차에 따라 스크립트 동작을 호출할 수 있습니다. 다음 매개 변수를 사용합니다.

    -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

[스크립트](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh)의 `print_usage()` 섹션에는 매개 변수에 대한 자세한 설명이 있습니다.

### <a name="scenarios"></a>시나리오

- **모든 테이블에서 복제를 사용하지 않도록 설정**:

        -m hn1 -s <source cluster DNS name> -sp Mypassword\!789 -all
  또는

        --src-cluster=<source cluster DNS name> --dst-cluster=<destination cluster DNS name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **지정된 테이블(table1, table2 및 table3)에서 복제를 사용하지 않도록 설정**:

        -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>다음 단계

이 자습서에서는 한 가상 네트워크 내에 또는 두 가상 네트워크 간에 HBase 복제를 설정하는 방법을 알아보았습니다. HDInsight 및 HBase에 대한 자세한 내용은 다음 문서를 참조하세요.

* [HDInsight에서 Apache HBase 시작](./apache-hbase-tutorial-get-started-linux.md)
* [HDInsight HBase 개요](./apache-hbase-overview.md)
* [Azure Virtual Network에 HBase 클러스터 만들기](./apache-hbase-provision-vnet.md)

