---
title: Azure HDInsight 가상 네트워크 아키텍처
description: Azure Virtual Network에서 HDInsight 클러스터를 만들 때 사용할 수 있는 리소스를 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: ad0e0250b32f2bdef4944e6e148be3215f3822f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81390217"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Azure HDInsight 가상 네트워크 아키텍처

이 문서에서는 사용자 지정 Azure Virtual Network에 HDInsight 클러스터를 배포할 때 표시 되는 리소스에 대해 설명 합니다. 이 정보는 온-프레미스 리소스를 Azure의 HDInsight 클러스터에 연결 하는 데 도움이 됩니다. Azure Virtual Network에 대 한 자세한 내용은 [azure Virtual Network 란?](../virtual-network/virtual-networks-overview.md)을 참조 하세요.

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터의 리소스 종류

Azure HDInsight 클러스터에는 서로 다른 유형의 가상 머신 또는 노드가 있습니다. 각 노드 유형은 시스템 작업에서 역할을 수행 합니다. 다음 표에는 클러스터의 노드 유형과 해당 역할에 대 한 설명이 요약 되어 있습니다.

| Type | Description |
| --- | --- |
| 헤드 노드 |  Apache Storm를 제외한 모든 클러스터 유형에 대해 헤드 노드는 배포 응용 프로그램의 실행을 관리 하는 프로세스를 호스팅합니다. 헤드 노드는 또한 SSH로 이동 하 여 클러스터 리소스에서 실행 되도록 조정 된 응용 프로그램을 실행할 수 있는 노드입니다. 모든 클러스터 유형에 대해 헤드 노드 수는 2로 고정 됩니다. |
| 사육 아웃 노드 | 사육 사가 데이터 처리를 수행 하는 노드 간에 작업을 조정 합니다. 또한 헤드 노드의 지도자 선택을 수행 하 고 특정 마스터 서비스를 실행 중인 헤드 노드를 추적 합니다. 사육 사 노드 수는 3에서 고정 됩니다. |
| 작업자 노드 | 데이터 처리 기능을 지 원하는 노드를 나타냅니다. 작업자 노드를 클러스터에서 추가 하거나 제거 하 여 컴퓨팅 기능을 확장 하 고 비용을 관리할 수 있습니다. |
| R 서버에 지 노드 | R 서버에 지 노드는 SSH 할 수 있는 노드를 나타내며,이를 통해 클러스터 리소스에서 실행 되도록 조정 된 응용 프로그램을 실행할 수 있습니다. 에 지 노드는 클러스터 내에서 데이터 분석에 참여 하지 않습니다. 이 노드는 또한 R Studio 서버를 호스트 하 여 브라우저를 사용 하 여 R 응용 프로그램을 실행할 수 있도록 합니다. |
| 영역 노드 | HBase 클러스터 유형의 경우 지역 노드 (데이터 노드 라고도 함)는 지역 서버를 실행 합니다. 지역 서버는 HBase에서 관리 하는 데이터의 일부를 제공 하 고 관리 합니다. 클러스터에서 지역 노드를 추가 하거나 제거 하 여 컴퓨팅 기능을 확장 하 고 비용을 관리할 수 있습니다.|
| Nimbus 노드 | 스톰 클러스터 유형의 경우 Nimbus 노드는 헤드 노드와 비슷한 기능을 제공 합니다. Nimbus 노드는 스톰 토폴로지 실행을 조정 하는 사육 광고를 통해 클러스터의 다른 노드에 작업을 할당 합니다. |
| 감독자 노드 | 스톰 클러스터 유형의 경우 감독자 노드는 Nimbus 노드에서 제공 하는 지침을 실행 하 여 처리를 수행 합니다. |

## <a name="resource-naming-conventions"></a>리소스 명명 규칙

클러스터의 노드에 주소를 지정 하는 경우 Fqdn (정규화 된 도메인 이름)을 사용 합니다. [AMBARI API](hdinsight-hadoop-manage-ambari-rest-api.md)를 사용 하 여 클러스터의 다양 한 노드 형식에 대 한 fqdn을 가져올 수 있습니다.

이러한 Fqdn은 형식 `<node-type-prefix><instance-number>-<abbreviated-clustername>.<unique-identifier>.cx.internal.cloudapp.net`입니다.

는 `<node-type-prefix>` 헤드 노드에 대 한 *hn* , 작업자 노드에 대 한 *w)* 및 아웃 청구 노드에 대 한 *zn* 입니다.

호스트 이름만 필요한 경우 FQDN의 첫 번째 부분만 사용 합니다.`<node-type-prefix><instance-number>-<abbreviated-clustername>`

## <a name="basic-virtual-network-resources"></a>기본 가상 네트워크 리소스

다음 다이어그램에서는 Azure의 HDInsight 노드 및 네트워크 리소스를 배치 하는 방법을 보여 줍니다.

![Azure 사용자 지정 VNET에서 만든 HDInsight 엔터티 다이어그램](./media/hdinsight-virtual-network-architecture/hdinsight-vnet-diagram.png)

Azure Virtual Network의 기본 리소스에는 위의 표에 언급 된 클러스터 노드 형식이 포함 되어 있습니다. 그리고 가상 네트워크와 외부 네트워크 간의 통신을 지 원하는 네트워크 장치.

다음 표에는 HDInsight가 사용자 지정 Azure Virtual Network에 배포 될 때 생성 된 9 개의 클러스터 노드가 요약 되어 있습니다.

| 리소스 유형 | 표시 번호 | 세부 정보 |
| --- | --- | --- |
|헤드 노드 | two |    |
|Zookeeper 노드 | three | |
|작업자 노드 | two | 이 수는 클러스터 구성 및 크기 조정에 따라 달라질 수 있습니다. Apache Kafka에는 최소 3 개의 작업자 노드가 필요 합니다.  |
|게이트웨이 노드 | two | 게이트웨이 노드는 Azure에서 생성 되지만 구독에는 표시 되지 않는 Azure 가상 컴퓨터입니다. 이러한 노드를 다시 부팅 해야 하는 경우 지원 담당자에 게 문의 하세요. |

다음 네트워크 리소스는 HDInsight에서 사용 되는 가상 네트워크 내에 자동으로 만들어집니다.

| 네트워킹 리소스 | 표시 번호 | 세부 정보 |
| --- | --- | --- |
|부하 분산 장치 | three | |
|네트워크 인터페이스 | 커서나 | 이 값은 각 노드에 고유한 네트워크 인터페이스가 있는 일반 클러스터를 기반으로 합니다. 9 개의 인터페이스에는 두 개의 헤드 노드, 3 개의 사육 아웃 노드, 두 개의 작업자 노드 및 위의 표에 설명 된 두 개의 게이트웨이 노드가 있습니다. |
|공용 IP 주소 | two |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>HDInsight에 연결 하기 위한 끝점

다음 세 가지 방법으로 HDInsight 클러스터에 액세스할 수 있습니다.

- 의 가상 네트워크 외부에 있는 HTTPS 끝점 `CLUSTERNAME.azurehdinsight.net`입니다.
- 에서 `CLUSTERNAME-ssh.azurehdinsight.net`헤드 노드에 직접 연결 하기 위한 SSH 끝점입니다.
- 가상 네트워크 `CLUSTERNAME-int.azurehdinsight.net`내의 HTTPS 끝점입니다. 이 URL의`-int`""를 확인 합니다. 이 끝점은 해당 가상 네트워크의 개인 IP로 확인 되 고 공용 인터넷에서 액세스할 수 없습니다.

이러한 세 끝점은 각각 부하 분산 장치에 할당 됩니다.

또한 공용 IP 주소는 가상 네트워크 외부에서 연결할 수 있도록 하는 두 끝점에 제공 됩니다.

1. 인터넷 `CLUSTERNAME.azurehdinsight.net`에서 클러스터에 연결할 때 사용할 FQDN (정규화 된 도메인 이름)에 대해 하나의 공용 IP가 부하 분산 장치에 할당 됩니다.
1. 두 번째 공용 IP 주소는 SSH 전용 도메인 이름 `CLUSTERNAME-ssh.azurehdinsight.net`에 사용 됩니다.

## <a name="next-steps"></a>다음 단계

- [개인 끝점을 사용 하 여 가상 네트워크에서 HDInsight 클러스터에 들어오는 트래픽 보안](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
