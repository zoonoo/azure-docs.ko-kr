---
title: Azure Service Bus 지리적 재해 복구 | Microsoft Docs
description: 지리적 지역을 사용하여 장애 조치(Failover)하고 Azure Service Bus에서 재해 복구를 수행하는 방법
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: a2b92b7673ed852e203ca0926421be6ee8cf977d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058182"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Azure Service Bus 지역 재해 복구

([가용성 영역](../availability-zones/az-overview.md)을 사용하지 않는 경우)전체 Azure 지역 또는 데이터 센터에서 가동 중지 시간이 발생하면 데이터 처리가 다른 지역 또는 데이터 센터에서 계속 작동되는 것이 중요합니다. 따라서 *지리적 재해 복구*는 모든 엔터프라이즈에서 중요한 기능입니다. Azure Service Bus는 네임스페이스 수준에서 지리적 재해 복구를 지원합니다.

지역 재해 복구 기능은 Service Bus 프리미엄 SKU에 전역적으로 사용할 수 있습니다. 

>[!NOTE]
> 지리적 재해 복구는 현재 쌍으로 연결되는 경우 메타데이터(큐, 토픽, 구독 및 필터)를 기본 네임스페이스에서 보조 네임스페이스로 복사하는 것만 보장합니다.

## <a name="outages-and-disasters"></a>중단 및 재해

"중단" 및 "재해" 간의 차이점을 참고하는 것이 중요합니다. 

*중단*은 Azure Service Bus를 일시적으로 사용할 수 없는 것으로, 메시징 저장소와 같은 서비스의 일부 구성 요소나 전체 데이터 센터에 영향을 줄 수 있습니다. 그렇지만 문제가 해결되면 Service Bus가 다시 사용할 수 있는 상태가 됩니다. 일반적으로 중단으로 인해 메시지나 기타 데이터가 손실되지는 않습니다. 이러한 중단의 예로 데이터 센터의 전원 장애를 들 수 있습니다. 일부 가동 중단은 일시적인 네트워크 문제로 인해 짧은 연결 오류가 발생합니다. 

*재해*는 Service Bus 클러스터, Azure 지역 또는 데이터 센터의 영구적이거나 장기적인 손실을 의미합니다. 지역 또는 데이터 센터는 다시 사용할 수 있게 될 수도, 그렇지 않을 수도 있고 몇 시간 또는 며칠 동안 중지될 수도 있습니다. 재해의 예로는 화재, 홍수 또는 지진이 있습니다. 영구적 재해는 일부 메시지, 이벤트 또는 기타 데이터의 손실을 발생시킬 수 있습니다. 그러나 대부분의 경우에는 데이터 손실이 없으며 메시지는 데이터 센터를 백업하면 복구할 수 있습니다.

Azure Service Bus의 지리적 재해 복구 기능은 재해 복구 솔루션입니다. 이 문서에 설명된 개념 및 워크플로는 재해 시나리오에 적용되며 일시적이거나 임시적인 중단이 아닙니다. Microsoft Azure의 재해 복구에 대해 자세히 알아보려면 [이 문서](/azure/architecture/resiliency/disaster-recovery-azure-applications)를 참조하세요.   

## <a name="basic-concepts-and-terms"></a>기본 개념 및 용어

재해 복구 기능은 메타데이터 재해 복구를 구현하며 주 및 보조 재해 복구 네임스페이스를 사용합니다. 지역 재해 복구 기능은 [프리미엄 SKU](service-bus-premium-messaging.md)에만 사용할 수 있습니다. 별칭을 통해 연결이 수행되므로 연결 문자열을 변경할 필요가 없습니다.

이 문서에서는 다음 용어가 사용됩니다.

-  *별칭*: 설정하는 재해 복구 구성의 이름입니다. 별칭은 하나의 안정적인 FQDN(정규화된 도메인 이름) 연결 문자열을 제공합니다. 애플리케이션은 이 별칭 연결 문자열을 사용하여 네임스페이스에 연결합니다. 별칭을 사용하면 장애 조치가 트리거될 때 연결 문자열이 변경되지 않습니다.

-  *주/보조 네임스페이스*: 별칭에 해당하는 네임스페이스입니다. 기본 네임스페이스는 "활성"이며 메시지를 수신합니다(기존 또는 새 네임스페이스일 수 있음). 보조 네임스페이스는 "수동"이며 메시지를 수신하지 않습니다. 둘 간의 메타데이터가 동기화되므로 둘 다 애플리케이션 코드 또는 연결 문자열을 변경하지 않고 메시지를 원활하게 수락할 수 있습니다. 활성 네임스페이스만 메시지를 수신하는지 확인하려면 별칭을 사용해야 합니다. 

-  *메타데이터*: 큐, 토픽 및 구독과 같은 엔터티 및 네임스페이스와 연결된 서비스의 해당 속성입니다. 엔터티 및 해당 설정만이 자동으로 복제됩니다. 메시지는 복제되지 않습니다.

-  *장애 조치(failover)* : 보조 네임스페이스를 활성화하는 프로세스입니다.

## <a name="setup"></a>설정

다음 섹션에서는 네임스페이스 간에 페어링을 설정하는 방법에 대해 간략히 설명합니다.

![1][]

설정 프로세스는 다음과 같습니다.

1. ***기본*** Service Bus 프리미엄 네임스페이스를 프로비전합니다.

2. *기본 네임스페이스가 프로비전된 위치와 다른 지역*에 ***보조*** Service Bus 프리미엄 네임스페이스를 프로비전합니다. 이렇게 하면 서로 다른 데이터 센터 지역 간에 오류를 격리할 수 있습니다.

3. 기본 네임스페이스와 보조 네임스페이스 간에 페어링을 만들어 ***별칭***을 가져옵니다.

4. 3단계에서 가져온 ***별칭***을 사용하여 클라이언트 애플리케이션을 Geo-DR(지리적 재해 복구) 사용 기본 네임스페이스에 연결합니다. 처음에는 별칭에서 기본 네임스페이스를 가리킵니다.

5. [선택 사항] 장애 조치가 필요한지 여부를 검색하기 위해 몇 가지 모니터링을 추가합니다.

## <a name="failover-flow"></a>흐름 장애 조치(Failover)

장애 조치는 고객이 명시적으로 명령 또는 명령을 트리거하는 클라이언트 소유 비즈니스 논리를 통해 수동으로 트리거되며, Azure에서는 트리거되지 않습니다. 이렇게 하면 고객이 Azure의 백본에서 가동 중단 해결에 대한 완전한 소유권과 가시성을 확보할 수 있습니다.

![4][]

장애 조치가 트리거되면,

1. ***별칭*** 연결 문자열이 보조 프리미엄 네임스페이스를 가리키도록 업데이트됩니다.

2. 클라이언트(발신자 및 수신자)가 보조 네임스페이스에 자동으로 연결됩니다.

3. 기본 및 보조 프리미엄 네임스페이스 간의 기존 페어링이 끊어집니다.

장애 조치가 시작되면,

1. 작동 중단이 발생하면 다시 장애 조치(Failover)할 수 있어야 합니다. 따라서 다른 수동 네임스페이스를 설정하고 페어링을 업데이트합니다. 

2. 사용할 수 있으면 이전 기본 네임스페이스에서 메시지를 끌어옵니다. 그 후에 지역 복구 설정의 외부에서 일반 메시징에 해당 네임스페이스를 사용하거나 이전 기본 네임스페이스를 삭제합니다.

> [!NOTE]
> 실패 전달 의미 체계만이 지원됩니다. 이 시나리오에서는 새 네임스페이스를 사용하여 장애 조치하고 다시 페어링합니다. 예를 들어 SQL 클러스터에서 장애 복구는 지원되지 않습니다. 

모니터링 시스템 또는 사용자 빌드 모니터링 솔루션을 사용하여 장애 조치를 자동화할 수 있습니다. 그러나 이러한 자동화에는 추가 계획 및 작업이 필요합니다. 이 부분은 이 문서에서 다루지 않습니다.

![2][]

## <a name="management"></a>관리

예를 들어 초기 설정 작업 중에 잘못된 지역을 페어링하는 실수가 발생한 경우 언제든지 두 네임스페이스의 페어링을 해제할 수 있습니다. 일반 네임스페이스와 페어링된 네임스페이스를 사용하려는 경우 별칭을 삭제합니다.

## <a name="use-existing-namespace-as-alias"></a>기존 네임스페이스를 별칭으로 사용

생산자와 소비자의 연결을 변경할 수 없는 시나리오를 사용하는 경우 네임스페이스 이름을 별칭 이름으로 다시 사용할 수 있습니다. [여기에서 GitHub의 샘플 코드](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name)를 참조하세요.

## <a name="samples"></a>샘플

[GitHub의 샘플](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/)에서는 장애 조치를 설정하고 시작하는 방법을 보여줍니다. 이러한 예제는 다음과 같은 개념을 보여줍니다.

- 지역 재해 복구를 설정하고 사용하기 위해 Service Bus에서 Azure Resource Manager를 사용하는 데 필요한 Azure Active Directory의 .NET 샘플 및 설정
- 샘플 코드를 실행하는 데 필요한 단계
- 기존 네임스페이스를 별칭으로 사용하는 방법
- PowerShell 또는 CLI를 통해 지역 재해 복구를 사용하는 단계
- 별칭을 사용하여 현재 기본 또는 보조 네임스페이스에서 [전달 및 수신](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1)

## <a name="considerations"></a>고려 사항

이 릴리스에서 고려할 다음과 같은 고려 사항에 유의하세요.

1. 장애 조치 계획에서 시간 요소를 고려해야 합니다. 예를 들어 15~20분이 넘게 연결이 손실된 경우 장애 조치를 시작하기로 결정할 수 있습니다.

2. 데이터가 복제되지 않으면 현재 활성 세션이 복제되지 않습니다. 또한 중복 검색 및 예약된 메시지가 작동하지 않을 수 있습니다. 새 세션, 새 예약된 메시지 및 새 중복이 작동합니다. 

3. 복잡한 분산 인프라를 장애 조치하려면 한 번 이상 [예행 연습](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan)을 수행해야 합니다.

4. 엔터티를 동기화하는 데 분당 약 50~100개의 엔터티를 처리하므로 다소 시간이 걸릴 수 있습니다. 구독 및 규칙은 엔터티로 계산합니다.

## <a name="availability-zones"></a>가용성 영역

Service Bus 프리미엄 SKU도 Azure 지역 내에서 오류가 없는 위치를 제공하는 [가용성 영역](../availability-zones/az-overview.md)을 지원합니다.

> [!NOTE]
> Azure Service Bus 프리미엄에 대한 가용성 영역 지원은 가용성 영역이 있는 [Azure 지역](../availability-zones/az-overview.md#services-support-by-region)에서만 사용할 수 있습니다.

Azure Portal을 사용하여 새로운 네임스페이스에서만 가용성 영역을 사용하도록 설정할 수 있습니다. Service Bus는 기존 네임스페이스의 마이그레이션을 지원하지 않습니다. 네임스페이스를 사용하도록 설정한 후에는 영역 중복성을 사용하지 않도록 설정할 수 없습니다.

![3][]

## <a name="next-steps"></a>다음 단계

- 지리적 재해 복구를 참조합니다[REST API 참조](/rest/api/servicebus/disasterrecoveryconfigs).
- 지리적 재해 복구를 실행합니다[GitHub의 샘플](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2).
- 지리적 재해 복구를 참조합니다[별칭으로 메시지를 전송하는 샘플](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1).

Service Bus 메시징에 대해 자세히 알아보려면 다음 문서를 참조하세요.

* [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus 토픽 및 구독을 사용하는 방법](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [Rest API](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geodr_setup_pairing.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png
[4]: ./media/service-bus-geo-dr/geodr_failover_alias_update.png