---
title: Azure HDInsight 가상 네트워크 아키텍처
description: Azure 가상 네트워크에서 HDInsight 클러스터를 만들 때 사용할 수 있는 리소스에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: ad0e0250b32f2bdef4944e6e148be3215f3822f7
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390217"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Azure HDInsight 가상 네트워크 아키텍처

이 문서에서는 사용자 지정 Azure 가상 네트워크에 HDInsight 클러스터를 배포할 때 존재하는 리소스에 대해 설명합니다. 이 정보는 온-프레미스 리소스를 Azure의 HDInsight 클러스터에 연결하는 데 도움이 됩니다. Azure 가상 네트워크에 대한 자세한 내용은 [Azure 가상 네트워크란 무엇입니까?](../virtual-network/virtual-networks-overview.md)

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터의 리소스 유형

Azure HDInsight 클러스터에는 다양한 유형의 가상 시스템 또는 노드가 있습니다. 각 노드 유형은 시스템 작동에서 중요한 역할을 합니다. 다음 표에서는 이러한 노드 유형과 클러스터의 해당 역할을 요약합니다.

| Type | Description |
| --- | --- |
| 헤드 노드 |  아파치 스톰을 제외한 모든 클러스터 유형에 대해 헤드 노드는 분산 응용 프로그램의 실행을 관리하는 프로세스를 호스트합니다. 헤드 노드는 SSH가 클러스터 리소스에서 실행되도록 조정된 응용 프로그램을 입력하고 실행할 수 있는 노드이기도 합니다. 헤드 노드 수는 모든 클러스터 유형에 대해 두 개로 고정됩니다. |
| 사육사 노드 | 사육사는 데이터 처리를 수행하는 노드 간의 작업을 조정합니다. 또한 헤드 노드의 리더 선거를 수행하고 특정 마스터 서비스를 실행하는 헤드 노드를 추적합니다. ZooKeeper 노드의 수는 3개로 고정됩니다. |
| 작업자 노드 | 데이터 처리 기능을 지원하는 노드를 나타냅니다. 작업자 노드를 클러스터에서 추가하거나 제거하여 컴퓨팅 기능을 확장하고 비용을 관리할 수 있습니다. |
| R 서버 에지 노드 | R Server 에지 노드는 SSH가 클러스터 리소스에서 실행되도록 조정된 응용 프로그램을 SSH로 입력하고 실행할 수 있는 노드를 나타냅니다. 에지 노드는 클러스터 내의 데이터 분석에 참여하지 않습니다. 또한 이 노드는 R Studio Server를 호스팅하므로 브라우저를 사용하여 R 응용 프로그램을 실행할 수 있습니다. |
| 지역 노드 | HBase 클러스터 유형의 경우 지역 노드(데이터 노드라고도 함)는 지역 서버를 실행합니다. 지역 서버는 HBase에서 관리하는 데이터의 일부를 서비스하고 관리합니다. 리전 노드를 클러스터에서 추가하거나 제거하여 컴퓨팅 기능을 확장하고 비용을 관리할 수 있습니다.|
| 님버스 노드 | Storm 클러스터 유형의 경우 Nimbus 노드는 헤드 노드와 유사한 기능을 제공합니다. Nimbus 노드는 사육사를 통해 클러스터의 다른 노드에 작업을 할당하여 폭풍 토폴로지 실행을 조정합니다. |
| 수퍼바이저 노드 | Storm 클러스터 유형의 경우 감독자 노드는 Nimbus 노드에서 제공하는 명령을 실행하여 처리를 수행합니다. |

## <a name="resource-naming-conventions"></a>리소스 이름 지정 규칙

클러스터의 노드를 해결할 때 완전 인증된 도메인 이름(FQDN)을 사용합니다. [Ambari API를](hdinsight-hadoop-manage-ambari-rest-api.md)사용하여 클러스터의 다양한 노드 유형에 대한 FQDN을 얻을 수 있습니다.

이러한 FQDNs는 양식이 `<node-type-prefix><instance-number>-<abbreviated-clustername>.<unique-identifier>.cx.internal.cloudapp.net`됩니다.

헤드 `<node-type-prefix>` 노드에 대한 *hn,* 작업자 노드에 대한 *wn* 및 사육사 노드에 대한 *zn이* 될 것입니다.

호스트 이름만 필요한 경우 FQDN의 첫 번째 부분만 사용하십시오.`<node-type-prefix><instance-number>-<abbreviated-clustername>`

## <a name="basic-virtual-network-resources"></a>기본 가상 네트워크 리소스

다음 다이어그램은 Azure에서 HDInsight 노드 및 네트워크 리소스의 배치를 보여 주며 있습니다.

![Azure 사용자 지정 VNET에서 만든 HDInsight 엔터티 다이어그램](./media/hdinsight-virtual-network-architecture/hdinsight-vnet-diagram.png)

Azure 가상 네트워크의 기본 리소스에는 이전 테이블에 언급된 클러스터 노드 유형이 포함됩니다. 그리고 가상 네트워크와 외부 네트워크 간의 통신을 지원하는 네트워크 장치.

다음 표에는 HDInsight가 사용자 지정 Azure 가상 네트워크에 배포될 때 생성된 9개의 클러스터 노드가 요약되어 있습니다.

| 리소스 유형 | 현재 번호 | 세부 정보 |
| --- | --- | --- |
|헤드 노드 | two |    |
|Zookeeper 노드 | three | |
|작업자 노드 | two | 이 숫자는 클러스터 구성 및 크기 조정에 따라 달라질 수 있습니다. 아파치 카프카에는 최소 3개의 작업자 노드가 필요합니다.  |
|게이트웨이 노드 | two | 게이트웨이 노드는 Azure에서 만들어지지만 구독에 표시되지 않는 Azure 가상 시스템입니다. 이러한 노드를 재부팅해야 하는 경우 지원팀에 문의하십시오. |

HDInsight와 함께 사용되는 가상 네트워크 내에서 다음 네트워크 리소스가 자동으로 생성됩니다.

| 네트워킹 리소스 | 현재 번호 | 세부 정보 |
| --- | --- | --- |
|부하 분산 장치 | three | |
|네트워크 인터페이스 | 아홉 명 | 이 값은 각 노드에 고유한 네트워크 인터페이스가 있는 일반 클러스터를 기반으로 합니다. 9개의 인터페이스는 헤드 노드 2개, 사육사 노드 3개, 작업자 노드 2개 및 이전 표에 언급된 두 개의 게이트웨이 노드입니다. |
|공용 IP 주소 | two |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>HDInsight에 연결하기 위한 엔드포인트

다음 세 가지 방법으로 HDInsight 클러스터에 액세스할 수 있습니다.

- 에서 가상 네트워크 외부의 HTTPS `CLUSTERNAME.azurehdinsight.net`끝점입니다.
- 에서 `CLUSTERNAME-ssh.azurehdinsight.net`헤드노드에 직접 연결하기 위한 SSH 끝점입니다.
- 가상 네트워크 `CLUSTERNAME-int.azurehdinsight.net`내의 HTTPS 끝점. 이 URL에서 "`-int`" 를 확인합니다. 이 끝점은 해당 가상 네트워크의 개인 IP로 해결되며 공용 인터넷에서 액세스할 수 없습니다.

이 세 끝점은 각각 로드 밸런서에 할당됩니다.

공용 IP 주소는 가상 네트워크 외부에서 연결을 허용하는 두 끝점에도 제공됩니다.

1. 하나의 공용 IP는 인터넷에서 `CLUSTERNAME.azurehdinsight.net`클러스터에 연결할 때 사용할 완전 인증된 도메인 이름(FQDN)에 대한 로드 밸런서에 할당됩니다.
1. 두 번째 공용 IP 주소는 SSH `CLUSTERNAME-ssh.azurehdinsight.net`도메인 이름에만 사용됩니다.

## <a name="next-steps"></a>다음 단계

- [프라이빗 엔드포인트가 있는 가상 네트워크에서 HDInsight 클러스터로 들어오는 트래픽 보호](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
