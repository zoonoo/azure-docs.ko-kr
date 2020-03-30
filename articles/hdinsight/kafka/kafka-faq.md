---
title: Azure HDInsight의 아파치 카프카에 대한 FAQ
description: 관리되는 Hadoop 클라우드 서비스인 Azure HDInsight에서 아파치 카프카에 대한 일반적인 질문에 대한 답변을 얻을 수 있습니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: ff4079263fd7afb02e132a798997687fad7e9961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206982"
---
# <a name="frequently-asked-questions-about-apache-kafka-in-azure-hdinsight"></a>Azure HDInsight의 Apache Kafka에 대한 질문과 대답

이 문서에서는 Azure HDInsight에서 아파치 카프카를 사용하는 방법에 대한 몇 가지 일반적인 질문을 다룹니다.

## <a name="what-kafka-versions-are-supported-by-hdinsight"></a>HDInsight에서 지원하는 카프카 버전은 무엇입니까?

HDInsight에서 공식적으로 지원되는 구성 요소 버전에 대한 자세한 내용은 [HDInsight에서 사용할 수 있는 아파치 하두롭 구성 요소 및 버전은 무엇입니까?](../hdinsight-component-versioning.md#supported-hdinsight-versions). 최상의 성능과 사용자 환경을 보장하기 위해 항상 최신 버전을 사용하는 것이 좋습니다.

## <a name="what-resources-are-provided-in-an-hdinsight-kafka-cluster-and-what-resources-am-i-charged-for"></a>HDInsight Kafka 클러스터에는 어떤 리소스가 제공되며 어떤 리소스에 대해 요금이 부과됩니까?

HDInsight 카프카 클러스터에는 다음 리소스가 포함됩니다.

* 헤드 노드
* Zookeeper 노드
* 브로커(작업자) 노드 
* 브로커 노드에 연결된 Azure 관리 디스크
* 게이트웨이 노드

이러한 모든 리소스는 게이트웨이 노드를 제외한 [HDInsight 가격 책정 모델에](https://azure.microsoft.com/pricing/details/hdinsight/)따라 요금이 부과됩니다. 게이트웨이 노드에 대한 요금은 청구되지 않습니다.

다양한 노드 유형에 대한 자세한 설명은 [Azure HDInsight 가상 네트워크 아키텍처를](../hdinsight-virtual-network-architecture.md)참조하십시오. 가격은 분별 노드 사용량을 기준으로 합니다. 가격은 노드 크기, 노드 수, 사용된 관리 디스크 유형 및 지역에 따라 다릅니다.

## <a name="do-apache-kafka-apis-work-with-hdinsight"></a>아파치 카프카 API는 HDInsight와 함께 작동합니까?

예, HDInsight는 네이티브 카프카 API를 사용합니다. 클라이언트 응용 프로그램 코드를 변경할 필요가 없습니다. [자습서 참조: 아파치 카프카 생산자 및 소비자 API를 사용하여](./apache-kafka-producer-consumer-api.md) 클러스터에서 Java 기반 생산자/소비자 API를 사용하는 방법을 확인합니다.

## <a name="can-i-change-cluster-configurations"></a>클러스터 구성을 변경할 수 있습니까?

예, Ambari 포털을 통해. 포털의 각 구성 요소에는 구성 요소 구성을 변경하는 데 사용할 수 있는 **구성** 섹션이 있습니다. 일부 변경을 위해 브로커를 다시 시작해야 할 수 있습니다.

## <a name="what-type-of-authentication-does-hdinsight-support-for-apache-kafka"></a>HDInsight는 아파치 카프카에 어떤 유형의 인증을 지원합니까?

[ESP(엔터프라이즈 보안 패키지)를](../domain-joined/apache-domain-joined-architecture.md)사용하면 Kafka 클러스터에 대한 토픽 수준 보안을 얻을 수 있습니다. 자습서: 자세한 내용은 [엔터프라이즈 보안 패키지(미리 보기)를 통해 HDInsight에서 아파치 카프카 정책을 구성합니다.](../domain-joined/apache-domain-joined-run-kafka.md)

## <a name="is-my-data-encrypted-can-i-use-my-own-keys"></a>내 데이터가 암호화되어 있습니까? 내 키를 사용할 수 있습니까?

관리 되는 디스크에 모든 Kafka 메시지는 [Azure 저장소 서비스 암호화 (SSE)](../../storage/common/storage-service-encryption.md)암호화 로 암호화 됩니다. 전송 중인 데이터(예: 클라이언트에서 브로커로 전송되는 데이터 및 다른 방법으로 전송되는 데이터)는 기본적으로 암호화되지 않습니다. [직접 SSL을 설정하여](./apache-kafka-ssl-encryption-authentication.md)이러한 트래픽을 암호화할 수 있습니다. 또한 HDInsight를 사용하면 자신의 키를 관리하여 미사용 데이터를 암호화할 수 있습니다. 자세한 내용은 [고객 관리 키 디스크 암호화를](../disk-encryption.md)참조하십시오.

## <a name="how-do-i-connect-clients-to-my-cluster"></a>클러스터에 클라이언트를 연결하려면 어떻게 해야 합니까?

Kafka 클라이언트가 Kafka 브로커와 통신하려면 네트워크를 통해 브로커에게 연락 할 수 있어야합니다. HDInsight 클러스터의 경우 VNet(가상 네트워크)이 보안 경계입니다. 따라서 클라이언트를 HDInsight 클러스터에 연결하는 가장 쉬운 방법은 클러스터와 동일한 VNet 내에서 클라이언트를 만드는 것입니다. 다른 시나리오는 다음과 같습니다.

* 다른 Azure VNet에서 클라이언트 연결 – 클러스터 VNet 및 클라이언트 VNet을 피어로 연결하고 [IP 광고에](apache-kafka-connect-vpn-gateway.md#configure-kafka-for-ip-advertising)대한 클러스터를 구성합니다. IP 광고를 사용하는 경우 Kafka 클라이언트는 브로커 IP 주소를 사용하여 완전 자격을 갖춘 도메인 이름(FQDN) 대신 브로커와 연결해야 합니다.

* 온-프레미스 클라이언트 연결 - VPN 네트워크를 사용하고 [Azure HDInsight에 대한 가상 네트워크 계획에](../hdinsight-plan-virtual-network-deployment.md)설명된 대로 사용자 지정 DNS 서버를 설정합니다.

* Kafka 서비스에 대한 공용 끝점 만들기 - 엔터프라이즈 보안 요구 사항이 허용하는 경우 Kafka 브로커를 위한 공용 끝점 또는 공용 끝점을 통해 자체 관리되는 오픈 소스 REST 엔드포인트를 배포할 수 있습니다.

## <a name="can-i-add-more-disk-space-on-an-existing-cluster"></a>기존 클러스터에 디스크 공간을 더 추가할 수 있습니까?

Kafka 메시지에 사용할 수 있는 공간을 늘리려면 노드 수를 늘릴 수 있습니다. 현재 기존 클러스터에 디스크를 더 추가하는 것은 지원되지 않습니다.

## <a name="can-a-kafka-cluster-work-with-databricks"></a>Kafka 클러스터가 데이터브릭과 함께 작동할 수 있나요? 

예. Kafka 클러스터는 동일한 VNet에 있는 한 Databrick에서 작업할 수 있습니다. Databricks가 있는 Kafka 클러스터를 사용하려면 HDInsight Kafka 클러스터가 있는 VNet을 만든 다음 데이터브릭 작업 영역을 만들 때 해당 VNet을 지정하고 VNet 주입을 사용합니다. 자세한 내용은 [Azure Virtual Network(VNet 삽입)에서 Azure Databricks 배포](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)를 참조하세요. Databricks 작업 영역을 만들 때 Kafka 클러스터의 부트스트랩 브로커 이름을 제공해야 합니다. Kafka 브로커 이름 검색에 대한 자세한 내용은 [아파치 사육사 및 브로커 호스트 정보 받기를](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started#getkafkainfo)참조하십시오.

## <a name="how-can-i-have-maximum-data-durability"></a>최대 데이터 내구성을 어떻게 가질 수 있습니까?

데이터 내구성을 사용하면 메시지 손실 위험을 가장 낮게 달성할 수 있습니다. 데이터 내구성을 극대화하려면 다음 설정을 권장합니다.

* 대부분의 지역에서 최소 복제 계수 3사용
* 장애 도메인이 두 개만 있는 리전에서 최소 복제 계수 4를 사용합니다.
* 부정한 지도자 선거를 비활성화
* **min.insync.replicas를** 2 개 이상으로 설정 - 쓰기를 진행하기 전에 리더와 완전히 동기화되어야하는 복제본 의 수를 변경합니다.
* **acks** 속성을 **모두로** 설정 - 이 속성은 모든 복제본이 모든 메시지를 승인해야 합니다.

데이터 일관성을 높이기 위해 Kafka를 구성하는 것은 브로커가 요청을 생성할 수 있는 가용성에 영향을 줍니다.

## <a name="can-i-replicate-my-data-to-multiple-clusters"></a>데이터를 여러 클러스터에 복제할 수 있습니까?

예. Kafka MirrorMaker를 사용하여 데이터를 여러 클러스터로 복제할 수 있습니다. 미러 메이커 설정에 대한 자세한 내용은 [거울 아파치 카프카 항목에서](apache-kafka-mirroring.md)찾을 수 있습니다. 또한 [Brooklin과](https://github.com/linkedin/Brooklin/)같은 여러 클러스터에 복제를 달성하는 데 도움이 되는 다른 자체 관리형 오픈 소스 기술 및 공급업체도 있습니다.

## <a name="can-i-upgrade-my-cluster-how-should-i-upgrade-my-cluster"></a>클러스터를 업그레이드할 수 있습니까? 클러스터를 업그레이드하는 방법은 무엇입니까?

현재 는 현재 클러스터 버전 업그레이드를 지원하지 않습니다. 클러스터를 더 높은 Kafka 버전으로 업데이트하려면 원하는 버전으로 새 클러스터를 만들고 Kafka 클라이언트를 마이그레이션하여 새 클러스터를 사용합니다.

## <a name="how-do-i-monitor-my-kafka-cluster"></a>Kafka 클러스터를 모니터링하려면 어떻게 해야 합니까?

Azure 모니터를 사용하여 [Kafka 로그를 분석합니다.](./apache-kafka-log-analytics-operations-management.md)

## <a name="next-steps"></a>다음 단계

* [Azure HDInsight에서 아파치 카프카에 대한 보안 소켓 계층(SSL) 암호화 및 인증 설정](./apache-kafka-ssl-encryption-authentication.md)
* [MirrorMaker를 사용하여 HDInsight에서 Kafka와 함께 Apache Kafka 토픽 복제](./apache-kafka-mirroring.md)
