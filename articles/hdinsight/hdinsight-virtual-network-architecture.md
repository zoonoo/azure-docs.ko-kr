---
title: Azure HDInsight 가상 네트워크 아키텍처
description: Azure Virtual Network에서 HDInsight 클러스터를 만들 때 사용할 수 있는 리소스에 알아봅니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: hrasheed
ms.openlocfilehash: 41420497bffd0abdc598e4c86b2dbda1466b2ce1
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66252859"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Azure HDInsight 가상 네트워크 아키텍처

이 문서에서는 사용자 지정 Azure Virtual Network에 HDInsight 클러스터를 배포할 때 존재 하는 리소스를 설명 합니다. 이 정보는 Azure에서 HDInsight 클러스터에 온-프레미스 리소스를 연결 하는 데 도움이 됩니다. Azure Virtual Network에 대 한 자세한 내용은 참조 하세요. [Azure Virtual Network 란?](../virtual-network/virtual-networks-overview.md)합니다.

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터에서 리소스 종류

Azure HDInsight 클러스터에 다른 유형의 virtual machines 또는 노드가 있습니다. 각 노드 형식 시스템의 작업에서 역할을 담당 합니다. 다음 표에서 이러한 노드 형식 및 클러스터의 해당 역할에 요약 되어 있습니다.

| Type | 설명 |
| --- | --- |
| 헤드 노드 |  Apache Storm을 제외 하 고 모든 클러스터 유형의 경우 헤드 노드 배포 응용 프로그램의 실행을 관리 하는 프로세스를 호스트 합니다. 헤드 노드 이기도에 SSH 할 수 있는 노드 및 클러스터 리소스를 실행 하기 위해 다음 조정 되는 응용 프로그램을 실행 합니다. 모든 클러스터 유형에 대해 두 개의 헤드 노드 수가 고정 됩니다. |
| ZooKeeper 노드 | Zookeeper는 데이터 처리를 수행 하는 노드 간에 작업을 조정 합니다. 또한 헤드 노드의 리더 않으며 추적 헤드 노드는 특정 마스터 서비스를 실행 합니다. ZooKeeper 노드 수가 3 개에 고정 됩니다. |
| 작업자 노드 | 데이터 처리 기능을 지 원하는 노드를 나타냅니다. 작업자 노드를 추가 하거나 컴퓨팅 기능을 확장 하 고 비용을 관리 하려면 클러스터에서 제거 될 수 있습니다. |
| R Server에 지 노드 | R 서버 가장자리 노드에 SSH를 실행할 수 있습니다 노드를 나타내고 클러스터 리소스를 실행 하기 위해 다음 조정 되는 응용 프로그램을 실행 합니다. 에 지 노드는 클러스터 내에서 데이터 분석에 참여 하지 않습니다. 이 노드에 또한 브라우저를 사용 하 여 R 응용 프로그램을 실행할 수 인 R Studio Server를 호스트 합니다. |
| 영역 노드 | HBase 클러스터 유형에 대해 (데이터 노드를 라고도 함) 지역 노드 지역 서버를 실행 합니다. 지역 서버는 제공 하 고 HBase에서 관리 하는 데이터의 부분을 관리 합니다. 영역 노드를 추가 또는 컴퓨팅 기능을 확장 하 고 비용을 관리 하려면 클러스터에서 제거할 수 있습니다.|
| Nimbus 노드 | Storm 클러스터 유형에 대해 Nimbus 노드는 헤드 노드 비슷한 기능을 제공 합니다. Nimbus 노드는 Storm 토폴로지를 실행 하는 조정 Zookeeper 통해 클러스터의 다른 노드로 작업을 할당 합니다. |
| 감독자 노드 | Storm 클러스터 유형에 대해 감독자 노드 Nimbus 노드에 원하는 처리를 수행 하 여 제공 된 지침을 실행 합니다. |

## <a name="basic-virtual-network-resources"></a>기본 가상 네트워크 리소스

다음 다이어그램은 Azure에서 HDInsight 노드 및 네트워크 리소스의 배치를 보여줍니다.

![사용자 지정 Azure VNET에서 만든 HDInsight 엔터티 다이어그램](./media/hdinsight-virtual-network-architecture/vnet-diagram.png)

HDInsight는 Azure Virtual Network에 배포 된 경우 기본 리소스 네트워크 외부 가상 네트워크 간 통신을 지 원하는 네트워크 장치 뿐만 아니라 이전 테이블에서 언급 한 클러스터 노드 형식을 포함 합니다.

다음 표에서 사용자 지정 Azure Virtual Network에 HDInsight를 배포할 때 만들어진 9 개의 클러스터 노드를 보여 줍니다.

| 리소스 종류 | 수 있는 | 세부 정보 |
| --- | --- | --- |
|헤드 노드 | two |    |
|Zookeeper 노드 | three | |
|작업자 노드 | two | 이 번호는 클러스터 구성 및 크기 조정에 따라 달라질 수 있습니다. Apache Kafka에 대 한 최소 3 개의 작업자 노드가 필요 합니다.  |
|게이트웨이 노드 | two | 게이트웨이 노드는 Azure 가상 컴퓨터를 Azure에서 생성 되지만 구독에는 표시 되지 않습니다. 이러한 노드를 다시 부팅 해야 할 경우 지원에 문의 합니다. |

HDInsight를 사용 하 여 가상 네트워크 내에서 다음 네트워크 있는 리소스를 자동으로 만들어집니다.

| 네트워킹 리소스 | 수 있는 | 세부 정보 |
| --- | --- | --- |
|부하 분산 장치 | three | |
|네트워크 인터페이스 | 9 | 이 값은 각 노드에 자체 네트워크 인터페이스에 있는 일반 클러스터를 기반으로 합니다. 9 인터페이스는 두 헤드 노드, zookeeper 노드 3 개, 2 개의 작업자 노드 및 두 개의 게이트웨이 노드가 이전 표에서 언급 합니다. |
|공용 IP 주소 | two |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>HDInsight에 연결 하기 위한 끝점

세 가지 방법으로 HDInsight 클러스터에 액세스할 수 있습니다.

- 가상 네트워크 외부에서 HTTPS 끝점 `CLUSTERNAME.azurehdinsight.net`합니다.
- 헤드 노드에 직접 연결 하는 것에 대 한 SSH 끝점 `CLUSTERNAME-ssh.azurehdinsight.net`합니다.
- 가상 네트워크 내에서 HTTPS 끝점 `CLUSTERNAME-int.azurehdinsight.net`합니다. 통지를 "-int"이이 URL에 있습니다. 이 끝점 해당 가상 네트워크의 개인 IP로 확인 되 고 공용 인터넷에서 액세스할 수 없습니다.

이러한 세 끝점 각각 부하 분산 장치를 할당 됩니다.

공용 IP 주소는 가상 네트워크 외부에서 연결을 허용 하는 두 끝점에도 제공 됩니다.

1. 하나의 공용 IP는 인터넷에서 클러스터에 연결할 때 사용할 정규화 된 도메인 이름 (FQDN)에 대 한 부하 분산 장치에 할당 된 `CLUSTERNAME.azurehdinsight.net`합니다.
1. SSH 도메인 이름에 대 한 두 번째 공용 IP 주소는 `CLUSTERNAME-ssh.azurehdinsight.net`합니다.

## <a name="next-steps"></a>다음 단계

* [개인 끝점을 사용 하 여 가상 네트워크에 HDInsight 클러스터에 들어오는 트래픽 보호](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
