---
title: Azure Service Bus 지리적 재해 복구 | Microsoft Docs
description: 지리적 지역을 사용 하 여 장애 조치 (failover) 및 재해 복구 Azure Service Bus
ms.topic: article
ms.date: 02/10/2021
ms.openlocfilehash: 3e8050cdaaae7e16a0f5125292df4b89b3690ed3
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102035397"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Azure Service Bus 지역 재해 복구

데이터 처리 리소스의 치명적인 가동 중단에 대 한 복원 력을 많은 기업에서 요구 하는 것 이며, 경우에 따라 업계 규정에도 필요 합니다. 

Azure Service Bus은 (는) 이미 개별 컴퓨터의 치명적인 오류에 대 한 위험을 분산 하거나 데이터 센터 내의 여러 오류 도메인에 걸쳐 있는 클러스터 간에 완전 한 랙을 분산 하 고 있으며, 서비스가 계속 해 서 보장 되는 서비스 수준 내에서 작업을 계속 수행 하 고 이러한 오류가 발생 하는 경우에는 눈에 띄게 중단 되지 않도록 투명 한 오류 감지 및 장애 조치 [가용성 영역](../availability-zones/az-overview.md)에 대해 enabled 옵션을 사용 하 여 Service Bus 네임 스페이스를 만든 경우 위험 중단 위험은 물리적으로 분리 된 세 가지 기능에 추가로 분산 되 고, 서비스에는 전체 기능의 완전 하 고 심각한 손실을 신속 하 게 처리할 수 있는 충분 한 용량 예약이 있습니다. 

가용성 영역을 지 원하는 모든 활성 Azure Service Bus 클러스터 모델은 억음 하드웨어 오류에 대 한 복원 력 측면에서 온-프레미스 메시지 broker 제품 보다 우수 하며 전체 데이터 센터 시설의 심각한 손실을 발생 합니다. 그러나 이러한 측정값이 충분히 방어 될 수 없는 광범위 한 물리적 소멸이 있는 경우에도 매우 많은 상황이 발생할 수 있습니다. 

Service Bus 지역 재해 복구 기능은 이러한 크기의 재해 로부터 쉽게 복구 하 고, 응용 프로그램 구성을 변경 하지 않고도 실패 한 Azure 지역을 중단 하는 데 사용할 수 있도록 설계 되었습니다. Azure 지역을 포기 하는 것은 일반적으로 여러 서비스를 포함 하며이 기능은 주로 복합 응용 프로그램 구성의 무결성 유지를 목표로 합니다. 이 기능은 Service Bus 프리미엄 SKU에서 전역적으로 사용할 수 있습니다. 

Geo-Disaster 복구 기능을 사용 하면 네임 스페이스의 전체 구성 (큐, 토픽, 구독, 필터)이 기본 네임 스페이스에서 보조 네임 스페이스로 지속적으로 복제 되 고, 언제 든 지 주 데이터베이스에서 보조 네임 스페이스로의 한 번만 장애 조치 (failover)를 시작할 수 있습니다. 장애 조치 (failover) 이동은 네임 스페이스의 선택한 별칭 이름을 보조 네임 스페이스로 다시 지정한 다음 페어링을 중단 합니다. 장애 조치 (failover)가 시작 된 후에는 거의 즉시 이루어집니다. 

> [!IMPORTANT]
> 이 기능을 사용 하면 동일한 구성을 사용 하 여 작업을 즉시 연속성 할 수 있지만 **큐 또는 토픽 구독 또는 배달 못 한 편지 큐에 저장 된 메시지는 복제 하지 않습니다**. 큐 의미 체계를 유지 하기 위해 이러한 복제는 메시지 데이터의 복제 뿐만 아니라 broker의 모든 상태 변경 내용에 대해서만 필요 합니다. 대부분의 Service Bus 네임 스페이스에 대해 필요한 복제 트래픽이 응용 프로그램 트래픽을 초과 하 고 처리량이 높은 큐를 사용 하 게 될 것입니다. 대부분의 메시지는 여전히 주 복제본에서 삭제 되는 동안 보조 복제본에 복제 되지만 과도 하 게 불필요 한 트래픽이 발생 합니다. 지역 재해 복구에 대해 선택 하는 많은 쌍에 적용 되는 대기 시간이 긴 복제 경로의 경우에는 지연 시간 제한 효과로 인해 응용 프로그램 트래픽을 sustainably 하는 복제 트래픽이 불가능 한 경우도 있습니다.
 
> [!TIP]
> 활성/활성 구성에서 큐 및 토픽 구독의 내용과 운영 체제의 해당 네임 스페이스를 복제 하 여 중단 및 재해를 처리할 수 있습니다. 이러한 지역 재해 복구 기능 집합에 대해서는 설명 하지 말고 [복제 지침](service-bus-federation-overview.md)을 따르세요.  

## <a name="outages-and-disasters"></a>중단 및 재해

"중단" 및 "재해" 간의 차이점을 참고하는 것이 중요합니다. 

*중단* 은 Azure Service Bus를 일시적으로 사용할 수 없는 것으로, 메시징 저장소와 같은 서비스의 일부 구성 요소나 전체 데이터 센터에 영향을 줄 수 있습니다. 그렇지만 문제가 해결되면 Service Bus가 다시 사용할 수 있는 상태가 됩니다. 일반적으로 중단으로 인해 메시지나 기타 데이터가 손실되지는 않습니다. 이러한 중단의 예로 데이터 센터의 전원 장애를 들 수 있습니다. 일부 가동 중단은 일시적인 네트워크 문제로 인해 짧은 연결 오류가 발생합니다. 

*재해* 는 Service Bus 클러스터, Azure 지역 또는 데이터 센터의 영구적이거나 장기적인 손실을 의미합니다. 지역 또는 데이터 센터는 다시 사용할 수 있게 될 수도, 그렇지 않을 수도 있고 몇 시간 또는 며칠 동안 중지될 수도 있습니다. 재해의 예로는 화재, 홍수 또는 지진이 있습니다. 영구적 재해는 일부 메시지, 이벤트 또는 기타 데이터의 손실을 발생시킬 수 있습니다. 그러나 대부분의 경우에는 데이터 손실이 없으며 메시지는 데이터 센터를 백업하면 복구할 수 있습니다.

Azure Service Bus의 지리적 재해 복구 기능은 재해 복구 솔루션입니다. 이 문서에 설명된 개념 및 워크플로는 재해 시나리오에 적용되며 일시적이거나 임시적인 중단이 아닙니다. Microsoft Azure의 재해 복구에 대해 자세히 알아보려면 [이 문서](/azure/architecture/resiliency/disaster-recovery-azure-applications)를 참조하세요.   

## <a name="basic-concepts-and-terms"></a>기본 개념 및 용어

재해 복구 기능은 메타데이터 재해 복구를 구현하며 주 및 보조 재해 복구 네임스페이스를 사용합니다. 지역 재해 복구 기능은 [PREMIUM SKU](service-bus-premium-messaging.md) 에 대해서만 사용할 수 있습니다. 별칭을 통해 연결이 수행되므로 연결 문자열을 변경할 필요가 없습니다.

이 문서에서는 다음 용어가 사용됩니다.

-  *별칭*: 설정하는 재해 복구 구성의 이름입니다. 별칭은 하나의 안정적인 FQDN(정규화된 도메인 이름) 연결 문자열을 제공합니다. 애플리케이션은 이 별칭 연결 문자열을 사용하여 네임스페이스에 연결합니다. 별칭을 사용하면 장애 조치가 트리거될 때 연결 문자열이 변경되지 않습니다.

-  *주/보조 네임스페이스*: 별칭에 해당하는 네임스페이스입니다. 기본 네임스페이스는 "활성"이며 메시지를 수신합니다(기존 또는 새 네임스페이스일 수 있음). 보조 네임스페이스는 “수동”이며 메시지를 수신하지 않습니다. 둘 간의 메타데이터가 동기화되므로 둘 다 애플리케이션 코드 또는 연결 문자열을 변경하지 않고 메시지를 원활하게 수락할 수 있습니다. 활성 네임스페이스만 메시지를 수신하는지 확인하려면 별칭을 사용해야 합니다. 
-  *메타데이터*: 큐, 토픽 및 구독과 같은 엔터티 및 네임스페이스와 연결된 서비스의 해당 속성입니다. 엔터티 및 해당 설정만이 자동으로 복제됩니다. 메시지는 복제 되지 않습니다.
-  *장애 조치(Failover)* : 보조 네임스페이스를 활성화하는 프로세스입니다.

## <a name="setup"></a>설치 프로그램

다음 섹션에서는 네임 스페이스 간의 페어링을 설정 하는 방법을 간략하게 설명 합니다.

![1][]

먼저 기존의 기본 네임스페이스 및 새로운 보조 네임스페이스를 만들거나 사용한 다음 둘을 쌍으로 연결합니다. 이 페어링은 연결하는 데 사용할 수 있는 별칭을 제공합니다. 별칭을 사용하므로 연결 문자열을 변경할 필요가 없습니다. 새 네임스페이스에만 장애 조치(Failover) 페어링에 추가할 수 있습니다. 

1. 기본 네임 스페이스를 만듭니다.
1. 다른 지역에 보조 네임 스페이스를 만듭니다. 이 단계는 선택 사항입니다. 다음 단계에서 페어링을 만드는 동안 보조 네임 스페이스를 만들 수 있습니다. 
1. Azure Portal에서 주 네임 스페이스로 이동 합니다.
1. 왼쪽 메뉴에서 **지역에서 복구** 를 선택 하 고 도구 모음에서 **페어링 시작** 을 선택 합니다. 

    :::image type="content" source="./media/service-bus-geo-dr/primary-namspace-initiate-pairing-button.png" alt-text="기본 네임 스페이스에서 페어링 시작":::    
1. **페어링 시작** 페이지에서 다음 단계를 수행 합니다.
    1. 기존 보조 네임 스페이스를 선택 하거나 다른 지역에서 하나를 만듭니다. 이 예제에서는 기존 네임 스페이스를 보조 네임 스페이스로 사용 합니다.  
    1. **별칭** 에 대해 지역 dr 쌍의 별칭을 입력 합니다. 
    1. 그런 다음 **만들기** 를 선택합니다. 

        :::image type="content" source="./media/service-bus-geo-dr/initiate-pairing-page.png" alt-text="보조 네임 스페이스 선택":::        
1. 다음 그림에 표시 된 것 처럼 **Service Bus 지역 DR 별칭** 페이지가 표시 됩니다. 왼쪽 메뉴에서 **지역 복구** 를 선택 하 여 기본 네임 스페이스 페이지에서 **지역 DR 별칭** 페이지로 이동할 수도 있습니다. 

    :::image type="content" source="./media/service-bus-geo-dr/service-bus-geo-dr-alias-page.png" alt-text="Service Bus 지역 DR 별칭 페이지":::
1. **지리적 DR 별칭** 페이지의 왼쪽 메뉴에서 **공유 액세스 정책** 을 선택 하 여 별칭의 기본 연결 문자열에 액세스 합니다. 기본/보조 네임 스페이스에 대 한 연결 문자열을 직접 사용 하는 대신이 연결 문자열을 사용 합니다. 처음에는 별칭에서 기본 네임스페이스를 가리킵니다.
1. **개요** 페이지로 전환합니다. 다음 작업을 수행할 수 있습니다. 
    1. 기본 네임 스페이스와 보조 네임 스페이스 간의 페어링을 해제 합니다. 도구 모음에서 연결 **끊기** 를 선택 합니다. 
    1. 보조 네임 스페이스로 수동으로 장애 조치 (failover) 합니다. 
        1. 도구 모음에서 **장애 조치 (Failover)** 를 선택 합니다. 
        1. 별칭에를 입력 하 여 보조 네임 스페이스로 장애 조치 (failover)를 수행 하려는 경우를 확인 합니다. 
        1. **안전 장애 조치 (Failover)** 옵션을 설정 하 여 보조 네임 스페이스로 안전 하 게 장애 조치 (failover) 합니다. 이 기능은 보조로 전환 하기 전에 보류 중인 지역 DR 복제가 완료 되었는지 확인할 수 있습니다. 
        1. 그런 다음 **장애 조치 (Failover)** 를 선택 합니다. 
        
            :::image type="content" source="./media/service-bus-geo-dr/failover-page.png" alt-text="{대체 텍스트}":::
    
            > [!IMPORTANT]
            > 장애 조치 (failover)는 보조 네임 스페이스를 활성화 하 고 Geo-Disaster Recovery 페어링에서 기본 네임 스페이스를 제거 합니다. 새 지역 재해 복구 쌍을 포함 하는 다른 네임 스페이스를 만듭니다. 

1. 마지막으로 장애 조치가 필요한 경우 감지할 몇 가지 모니터링을 추가해야 합니다. 대부분의 경우 서비스는 대규모 에코시스템의 일부입니다. 따라서 장애 조치가 주로 나머지 하위 시스템 또는 인프라와 동기화되어 수행되어야 할 때가 많으므로 자동 장애 조치는 거의 불가능합니다.

### <a name="service-bus-standard-to-premium"></a>Standard에서 premium으로 Service Bus
[Azure Service Bus 표준 네임 스페이스를 Azure Service Bus Premium으로 마이그레이션한](service-bus-migrate-standard-premium.md)경우 기존 별칭 (즉, Service Bus 표준 네임 스페이스 연결 문자열)을 사용 하 여 **PS/CLI** 또는 **REST API** 를 통해 재해 복구 구성을 만들어야 합니다.

마이그레이션 중에 Azure Service Bus 표준 네임 스페이스 연결 문자열/DNS 이름 자체가 Azure Service Bus Premium 네임 스페이스에 대 한 별칭이 되기 때문입니다.

클라이언트 응용 프로그램은이 별칭 (즉, 표준 네임 스페이스 연결 문자열 Azure Service Bus)을 사용 하 여 재해 복구 페어링이 설정 된 프리미엄 네임 스페이스에 연결 해야 합니다.

포털을 사용 하 여 재해 복구 구성을 설정 하면 포털에서이 주의 사항을 추상화할 수 있습니다.


## <a name="failover-flow"></a>흐름 장애 조치(Failover)

장애 조치는 고객이 명시적으로 명령 또는 명령을 트리거하는 클라이언트 소유 비즈니스 논리를 통해 수동으로 트리거되며, Azure에서는 트리거되지 않습니다. Azure의 백본에서 중단 해결에 대 한 고객의 전체 소유권 및 표시 여부를 제공 합니다.

![4][]

장애 조치가 트리거되면,

1. ***별칭*** 연결 문자열이 보조 프리미엄 네임스페이스를 가리키도록 업데이트됩니다.

2. 클라이언트(발신자 및 수신자)가 보조 네임스페이스에 자동으로 연결됩니다.

3. 기본 및 보조 프리미엄 네임스페이스 간의 기존 페어링이 끊어집니다.

장애 조치가 시작되면,

1. 작동 중단이 발생하면 다시 장애 조치(Failover)할 수 있어야 합니다. 따라서 다른 수동 네임 스페이스를 설정 하 고 페어링을 업데이트 합니다. 

2. 사용할 수 있으면 이전 기본 네임스페이스에서 메시지를 끌어옵니다. 그 후에 지역 복구 설정의 외부에서 일반 메시징에 해당 네임스페이스를 사용하거나 이전 기본 네임스페이스를 삭제합니다.

> [!NOTE]
> 실패 전달 의미 체계만이 지원됩니다. 이 시나리오에서는 새 네임스페이스를 사용하여 장애 조치하고 다시 페어링합니다. 예를 들어 SQL 클러스터에서 장애 복구는 지원되지 않습니다. 

모니터링 시스템 또는 사용자 빌드 모니터링 솔루션을 사용하여 장애 조치를 자동화할 수 있습니다. 그러나 이러한 자동화에는 추가 계획 및 작업이 필요합니다. 이 부분은 이 문서에서 다루지 않습니다.

![2][]

## <a name="management"></a>관리

예를 들어 초기 설정 작업 중에 잘못된 지역을 페어링하는 실수가 발생한 경우 언제든지 두 네임스페이스의 페어링을 해제할 수 있습니다. 일반 네임스페이스와 페어링된 네임스페이스를 사용하려는 경우 별칭을 삭제합니다.

## <a name="use-existing-namespace-as-alias"></a>기존 네임스페이스를 별칭으로 사용

생산자와 소비자의 연결을 변경할 수 없는 시나리오가 있는 경우 별칭 이름으로 네임 스페이스 이름을 다시 사용할 수 있습니다. [여기에서 GitHub의 샘플 코드](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name)를 참조하세요.

## <a name="samples"></a>샘플

[GitHub의 샘플](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/)에서는 장애 조치를 설정하고 시작하는 방법을 보여줍니다. 이러한 예제는 다음과 같은 개념을 보여줍니다.

- Azure Active Directory에서 Service Bus Azure Resource Manager를 사용 하 여 지역 재해 복구를 설정 하 고 사용 하는 데 필요한 .NET 샘플 및 설정입니다.
- 샘플 코드를 실행하는 데 필요한 단계
- 기존 네임스페이스를 별칭으로 사용하는 방법
- PowerShell 또는 CLI를 통해 지역 재해 복구를 사용하는 단계
- 별칭을 사용하여 현재 기본 또는 보조 네임스페이스에서 [전달 및 수신](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1)

## <a name="considerations"></a>고려 사항

이 릴리스에서 고려할 다음과 같은 고려 사항에 유의하세요.

1. 장애 조치 계획에서 시간 요소를 고려해야 합니다. 예를 들어 15~20분이 넘게 연결이 손실된 경우 장애 조치를 시작하기로 결정할 수 있습니다.

2. 데이터가 복제되지 않으면 현재 활성 세션이 복제되지 않습니다. 또한 중복 검색 및 예약된 메시지가 작동하지 않을 수 있습니다. 새 세션, 새 예약 된 메시지 및 새 중복이 작동 합니다. 

3. 복잡한 분산 인프라를 장애 조치하려면 한 번 이상 [예행 연습](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan)을 수행해야 합니다.

4. 엔터티를 동기화하는 데 분당 약 50~100개의 엔터티를 처리하므로 다소 시간이 걸릴 수 있습니다. 구독 및 규칙은 엔터티로 계산합니다.

### <a name="availability-zones"></a>가용성 영역

Service Bus 프리미엄 SKU는 동일한 Azure 지역 내에서 오류가 없는 위치를 제공하는 [가용성 영역](../availability-zones/az-overview.md)을 지원합니다. Service Bus은 3 개의 메시징 저장소 복사본 (1 주 및 2 차)을 관리 합니다. Service Bus는 데이터 및 관리 작업에 대해 3 개의 복사본을 모두 동기화 된 상태로 유지 합니다. 주 복사본에 오류가 발생 하는 경우 보조 복사본 중 하나가 가동 중지 시간 없이 주 복제본으로 승격 됩니다. 응용 프로그램의 Service Bus에서 일시적으로 연결을 해제 하는 경우 SDK의 재시도 논리는 Service Bus에 자동으로 다시 연결 됩니다. 

가용성 영역을 사용 하는 경우 메타 데이터와 데이터 (메시지)는 모두 가용성 영역의 데이터 센터에 복제 됩니다. 

> [!NOTE]
> Azure Service Bus 프리미엄에 대한 가용성 영역 지원은 가용성 영역이 있는 [Azure 지역](../availability-zones/az-region.md)에서만 사용할 수 있습니다.

Azure Portal을 사용하여 새로운 네임스페이스에서만 가용성 영역을 사용하도록 설정할 수 있습니다. Service Bus는 기존 네임스페이스의 마이그레이션을 지원하지 않습니다. 네임스페이스를 사용하도록 설정한 후에는 영역 중복성을 사용하지 않도록 설정할 수 없습니다.

![3][]

## <a name="private-endpoints"></a>프라이빗 엔드포인트
이 섹션에서는 개인 끝점을 사용 하는 네임 스페이스에서 지역 재해 복구를 사용할 때 추가로 고려해 야 할 사항에 대해 설명 합니다. 일반적으로 Service Bus에서 프라이빗 엔드포인트를 사용하는 방법에 대한 자세한 내용은 [Azure Private Link와 Azure Service Bus 통합](private-link-service.md)을 참조하세요.

### <a name="new-pairings"></a>새 페어링
프라이빗 엔드포인트가 없는 보조 네임스페이스와 프라이빗 엔드포인트가 있는 기본 네임스페이스 간에 페어링을 만들려고 시도하면 페어링이 실패합니다. 기본 및 보조 네임스페이스에 모두 프라이빗 엔드포인트가 있는 경우에만 페어링이 성공합니다. 기본 및 보조 네임스페이스와 프라이빗 엔드포인트가 생성되는 가상 네트워크에서 동일한 구성을 사용하는 것이 좋습니다. 

> [!NOTE]
> 프라이빗 엔드포인트가 있는 기본 네임스페이스를 보조 네임스페이스와 페어링하려고 시도하면 유효성 검사 프로세스에서는 보조 네임스페이스에 프라이빗 엔드포인트가 있는지만 검사합니다. 엔드포인트가 장애 조치(failover) 후 작동하는지 또는 작동할지는 검사하지 않습니다. 장애 조치(failover) 후 프라이빗 엔드포인트가 있는 보조 네임스페이스가 예상대로 작동하는지 검사하는 것은 사용자의 책임입니다.
>
> 프라이빗 엔드포인트 구성이 동일한지 테스트하려면 가상 네트워크 외부에서 보조 네임스페이스로 [큐 가져오기](/rest/api/servicebus/stable/queues/get) 요청을 보내고 서비스에서 오류 메시지를 수신하는지 확인합니다.

### <a name="existing-pairings"></a>기존 페어링
기본 네임스페이스와 보조 네임스페이스 간의 페어링이 이미 있는 경우에는 기본 네임스페이스에서 프라이빗 엔드포인트 만들기가 실패합니다. 이 문제를 해결하려면 먼저 보조 네임스페이스에 프라이빗 엔드포인트를 만든 다음, 기본 네임스페이스에 프라이빗 엔드포인트를 만듭니다.

> [!NOTE]
> 보조 네임스페이스에는 읽기 전용 액세스가 허용되며 프라이빗 엔드포인트 구성에 대한 업데이트가 허용됩니다. 

### <a name="recommended-configuration"></a>권장 구성
애플리케이션 및 Service Bus에 대한 재해 복구 구성을 만들 때 애플리케이션의 기본 및 보조 인스턴스를 모두 호스팅하는 가상 네트워크에 대하여 기본 및 보조 Service Bus 네임스페이스 둘 다에 대한 프라이빗 엔드포인트를 만들어야 합니다.

두 개의 가상 네트워크인 VNET-1, VNET-2 및 이러한 기본 및 보조 네임스페이스인 ServiceBus-Namespace1-Primary, ServiceBus-Namespace2-Secondary가 있다고 가정해 보겠습니다. 다음 단계를 수행해야 합니다. 

- ServiceBus-Namespace1-Primary에서 VNET-1과 VNET-2의 서브넷을 사용하는 두 개의 프라이빗 엔드포인트를 만듭니다.
- ServiceBus-Namespace2-Secondary에서 VNET-1과 VNET-2의 동일한 서브넷을 사용하는 두 개의 프라이빗 엔드포인트를 만듭니다. 

![프라이빗 엔드포인트 및 가상 네트워크](./media/service-bus-geo-dr/private-endpoints-virtual-networks.png)


이 방법의 장점은 장애 조치(failover)가 Service Bus 네임스페이스와 무관한 애플리케이션 계층에서 발생할 수 있다는 것입니다. 다음과 같은 시나리오를 고려해 보세요. 

**애플리케이션 전용 장애 조치:** 여기서 애플리케이션은 VNET-1에는 없지만 VNET-2로 이동합니다. 두 프라이빗 엔드포인트 모두 기본 및 보조 네임스페이스에 대해 VNET-1과 VNET-2 모두에 구성되어 있으므로 애플리케이션은 작동합니다. 

**Service Bus 네임스페이스 전용 장애 조치(failover)** : 여기서도 역시 두 프라이빗 엔드포인트는 기본 및 보조 네임스페이스에 대해 두 가상 네트워크 모두에서 구성되므로 애플리케이션은 작동합니다. 

> [!NOTE]
> 가상 네트워크의 지리적 재해 복구에 대한 지침은 [Virtual Network - 비즈니스 연속성](../virtual-network/virtual-network-disaster-recovery-guidance.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- 지리적 재해 복구를 참조합니다[REST API 참조](/rest/api/servicebus/stable/disasterrecoveryconfigs).
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
