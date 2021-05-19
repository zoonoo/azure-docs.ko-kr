---
title: Azure HDInsight 가상 네트워크 아키텍처
description: Azure Virtual Network에서 HDInsight 클러스터를 만들 때 사용할 수 있는 리소스를 알아봅니다.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 43640a9de91b6b44a236090ff029a49313ee0247
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871676"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Azure HDInsight 가상 네트워크 아키텍처

이 문서에서는 사용자 지정 Azure Virtual Network에 HDInsight 클러스터를 배포할 때 존재하는 리소스에 대해 설명합니다. 이 정보의 도움을 통해 사용자는 온-프레미스 리소스를 Azure의 HDInsight 클러스터에 연결합니다. Azure Virtual Network에 대한 자세한 내용은 [Azure Virtual Network란?](../virtual-network/virtual-networks-overview.md)을 참조하세요.

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터의 리소스 유형

Azure HDInsight 클러스터에는 서로 다른 유형의 가상 머신 또는 노드가 있습니다. 각 노드 유형은 시스템에 대한 작업에서 역할을 수행합니다. 다음 표에는 클러스터의 이러한 노드 유형과 그 역할에 대한 설명이 요약되어 있습니다.

| 유형 | Description |
| --- | --- |
| 헤드 노드 |  Apache Storm를 제외한 모든 클러스터 유형의 경우, 헤드 노드는 분산 앱의 실행을 관리하는 프로세스를 호스트합니다. 헤드 노드는 또한 SSH 연결을 만들고, 클러스터 리소스에서 실행 되도록 조정된 애플리케이션을 실행할 수 있는 노드입니다. 모든 클러스터 유형의 헤드 노드 수는 2개로 고정됩니다. |
| Zookeeper 노드 | ZooKeeper가 데이터 처리를 수행하는 노드들 간의 작업을 조정합니다. 또한 이것은 헤드 노드의 선행부 선택을 수행하고, 특정 마스터 서비스를 실행 중인 헤드 노드를 추적합니다. ZooKeeper 노드 수는 3개로 고정됩니다. |
| 작업자 노드 | 데이터 처리 기능을 지원하는 노드를 나타냅니다. 작업자 노드를 클러스터에서 추가하거나 제거하여, 컴퓨팅 기능을 확장하고 비용을 관리할 수 있습니다. |
| R 서버 에지 노드 | R 서버 에지 노드는 SSH 연결을 만들고, 클러스터 리소스에서 실행 되도록 조정된 애플리케이션을 실행할 수 있는 노드입니다. 에지 노드는 클러스터 내에서 데이터 분석에 참가하지 않습니다. 이 노드는 또한 R Studio 서버를 호스트하여, 브라우저로 R 애플리케이션을 실행할 수 있도록 합니다. |
| 지역 노드 | HBase 클러스터 유형의 경우, 지역 노드(데이터 노드라고도 함)는 지역 서버를 실행합니다. 지역 서버는 HBase에서 관리하는 데이터의 일부를 제공하고 관리합니다. 지역 노드를 클러스터에서 추가하거나 제거하여, 컴퓨팅 기능을 확장하고 비용을 관리할 수 있습니다.|
| Nimbus 노드 | Storm 클러스터 유형의 경우 Nimbus 노드는 헤드 노드와 비슷한 기능을 제공합니다. Nimbus 노드는 Storm 토폴로지 실행을 조정하는 ZooKeeper를 통해 클러스터의 다른 노드에 작업을 할당합니다. |
| 감독자 노드 | Storm 클러스터 유형의 경우, 감독자 노드는 Nimbus 노드에서 제공하는 지침을 실행하여 처리를 수행합니다. |

## <a name="resource-naming-conventions"></a>리소스 명명 규칙

클러스터의 노드에 주소를 지정하는 경우 FQDN(정규화된 도메인 이름)을 사용합니다. [Ambari API](hdinsight-hadoop-manage-ambari-rest-api.md)를 사용하여 클러스터의 다양한 노드 형식에 대한 FQDN을 가져올 수 있습니다.

이러한 FQDN은 양식 `<node-type-prefix><instance-number>-<abbreviated-clustername>.<unique-identifier>.cx.internal.cloudapp.net`입니다.

`<node-type-prefix>`은 헤드 노드에 대한 *hn*, 작업자 노드에 대한 *w,n*, ZooKeeper 노드에 대한 *zn* 입니다.

호스트 이름만 필요한 경우 FQDN의 첫 번째 부분만 사용합니다(`<node-type-prefix><instance-number>-<abbreviated-clustername>`).

## <a name="basic-virtual-network-resources"></a>기본 가상 네트워크 리소스

다음 다이어그램에서는 Azure의 HDInsight 노드 및 네트워크 리소스를 배치하는 방법을 보여 줍니다.

:::image type="content" source="./media/hdinsight-virtual-network-architecture/hdinsight-vnet-diagram.png" alt-text="Azure 사용자 지정 VNET에서 만든 HDInsight 엔터티 다이어그램" border="false":::

Azure Virtual Network의 기본 리소스에는 이전의 표에 언급된 클러스터 노드 형식이 포함되어 있습니다. 그리고 가상 네트워크와 외부 네트워크 간의 통신을 지원하는 네트워크 디바이스입니다.

다음 표에는 HDInsight가 사용자 지정 Azure Virtual Network에 배포될 때 생성된 9개의 클러스터 노드가 요약되어 있습니다.

| 리소스 종류 | 표시 번호 | 세부 정보 |
| --- | --- | --- |
|헤드 노드 | two |    |
|Zookeeper 노드 | three | |
|작업자 노드 | two | 이 수는 클러스터 구성 및 크기 조정에 따라 달라질 수 있습니다. Apache Kafka에는 최소 3개의 작업자 노드가 필요합니다.  |
|게이트웨이 노드 | two | 게이트웨이 노드는 Azure에서 생성되지만 구독에는 표시되지 않는 Azure 가상 컴퓨터입니다. 이러한 노드를 다시 부팅해야 하는 경우 고객 지원팀에 문의 하세요. |

다음 네트워크 리소스는 HDInsight와 함께 사용되는 가상 네트워크 내에 자동으로 만들어집니다.

| 네트워킹 리소스 | 표시 번호 | 세부 정보 |
| --- | --- | --- |
|부하 분산 장치 | three | |
|네트워크 인터페이스 | nine | 이 값은 각 노드에 고유한 네트워크 인터페이스가 있는 일반 클러스터를 기반으로 합니다. 9개의 인터페이스에는 2개의 헤드 노드, 3개의 ZooKeeper 노드, 2개의 작업자 노드 및 이전의 표에 설명된 2개의 게이트웨이 노드가 있습니다. |
|공용 IP 주소 | two |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>HDInsight에 연결하기 위한 엔드포인트

HDInsight 클러스터에 다음 세 가지 방법으로 액세스할 수 있습니다.

- `CLUSTERNAME.azurehdinsight.net`의 가상 네트워크 외부에 있는 HTTPS 엔드포인트.
- `CLUSTERNAME-ssh.azurehdinsight.net`의 헤드 노드에 직접 연결하기 위한 SSH 엔드포인트.
- 가상 네트워크 `CLUSTERNAME-int.azurehdinsight.net` 내의 HTTPS 엔드포인트. 이 URL의 "`-int`"에 주목하세요. 이 엔드포인트는 해당 가상 네트워크의 개인 IP로 확인되지만, 공용 인터넷에서 액세스할 수 없습니다.

이러한 3개의 엔드포인트는 각각 부하 분산 장치에 할당됩니다.

또한 공용 IP 주소는 가상 네트워크 외부에서 연결할 수 있도록 하는 2개의 엔드포인트에 제공됩니다.

1. 인터넷 `CLUSTERNAME.azurehdinsight.net`에서 클러스터에 연결할 때 사용할 FQDN(정규화된 도메인 이름)에 대해 하나의 공용 IP가 부하 분산 장치에 할당됩니다.
1. 두 번째 공용 IP 주소는 SSH 전용 도메인 이름 `CLUSTERNAME-ssh.azurehdinsight.net`에 사용됩니다.

## <a name="next-steps"></a>다음 단계

- [프라이빗 엔드포인트를 사용하여 가상 네트워크에서 HDInsight 클러스터에 들어오는 트래픽 보안](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
