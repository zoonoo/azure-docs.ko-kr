---
title: 비즈니스 연속성 및 재해 복구
description: 데이터를 보호하고, 파괴적인 이벤트에서 신속하게 복구하고, 중요한 비즈니스 기능에 필요한 리소스를 복원하고, Azure Logic Apps에 대한 비즈니스 연속성을 유지하기 위한 전략을 설계합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 7bf71ce7c44229ccf19022e9cfb0162f9d77cd97
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437711"
---
# <a name="business-continuity-and-disaster-recovery-for-azure-logic-apps"></a>Azure 논리 앱에 대한 비즈니스 연속성 및 재해 복구

예측할 수 없는 이벤트가 비즈니스와 고객에게 미치는 영향과 영향을 줄이려면 데이터를 보호하고, 중요한 비즈니스 기능을 지원하는 리소스를 신속하게 복원하고, [ *BC(비즈니스 연속성)를* ](https://en.wikipedia.org/wiki/Business_continuity_planning)유지하기 위해 운영을 계속할 수 있도록 [ *DR(재해 복구)* ](https://en.wikipedia.org/wiki/Disaster_recovery) 솔루션을 마련해야 합니다. 예를 들어 중단에는 중단, 기본 인프라 또는 저장소, 네트워크 또는 컴퓨팅 리소스와 같은 구성 요소의 손실, 복구할 수 없는 응용 프로그램 오류 또는 전체 데이터 센터 손실이 포함될 수 있습니다. 기업 또는 조직은 비즈니스 연속성 및 재해 복구(BCDR) 솔루션을 준비하여 중단, 계획 또는 계획되지 않은 중단에 보다 신속하게 대응하고 고객의 가동 중지 시간을 줄일 수 있습니다.

이 문서에서는 [Azure Logic Apps를](../logic-apps/logic-apps-overview.md)사용하여 자동화된 워크플로를 빌드할 때 적용할 수 있는 BCDR 지침 및 전략을 제공합니다. 논리 앱 워크플로를 사용하면 작성해야 하는 코드의 양을 줄여 앱, 클라우드 서비스 및 온-프레미스 시스템 간에 데이터를 보다 쉽게 통합하고 오케스트레이션할 수 있습니다. BCDR을 계획할 때논리 앱뿐만 아니라 논리 앱과 함께 사용하는 이러한 Azure 리소스도 고려해야 합니다.

* 논리 앱에서 다른 앱, 서비스 및 시스템으로 만드는 [연결입니다.](../connectors/apis-list.md) 자세한 내용은 이 항목의 후반부 [리소스 연결을](#resource-connections) 참조하십시오.

* 논리 앱에서 만들고 온-프레미스 시스템의 데이터에 액세스하는 데 사용하는 Azure 리소스인 [온-프레미스 데이터 게이트웨이입니다.](../logic-apps/logic-apps-gateway-connection.md) 각 게이트웨이 리소스는 로컬 컴퓨터에 별도의 [데이터 게이트웨이 설치를](../logic-apps/logic-apps-gateway-install.md) 나타냅니다. 자세한 내용은 이 항목의 [후반부온-프레미스 데이터 게이트웨이를](#on-premises-data-gateways) 참조하십시오.

* [B2B(기업 간) 엔터프라이즈 통합](../logic-apps/logic-apps-enterprise-integration-overview.md) 시나리오에 논리 앱이 사용하는 아티팩트를 정의하고 저장하는 [통합 계정입니다.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 예를 들어 [통합 계정에 대해 지역 간 재해 복구를 설정할](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)수 있습니다.

* Azure 가상 네트워크 내에서 격리된 논리 앱 런타임 인스턴스에서 실행되는 논리 앱을 만드는 [통합 서비스 환경(ISEs)입니다.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 그런 다음 이러한 논리 앱은 해당 가상 네트워크의 방화벽 뒤에 보호되는 리소스에 액세스할 수 있습니다.

<a name="primary-secondary-locations"></a>

## <a name="primary-and-secondary-locations"></a>기본 및 보조 위치

각 논리 앱은 배포에 사용할 위치를 지정해야 합니다. 이 위치는 "Us West"와 같은 글로벌 다중 테넌트 Azure의 공용 지역이거나 이전에 Azure 가상 네트워크에 만들고 배포한 ISE(통합 서비스 환경)입니다. ISE에서 논리 앱을 실행하는 것은 전역 Azure 지역에서 논리 앱을 실행하는 것과 유사하므로 재해 복구 전략이 두 시나리오모두에 적용할 수 있습니다. 그러나 ISEs에는 ISEs에서만 사용할 수 있는 리소스에 대한 액세스를 구성하는 등의 다른 고려 사항이 있습니다.

> [!NOTE]
> 로직 앱이 거래 파트너, 계약, 스키마, 맵 및 통합 계정에 저장된 인증서와 같은 B2B 아티팩트와도 함께 작동하는 경우 통합 계정과 논리 앱 모두 동일한 위치를 지정해야 합니다.

이 재해 복구 전략은 Azure Logic Apps를 사용할 수 있는 대체 위치에서 대기 또는 백업 논리 앱에 [*장애 조치(failover)하도록*](https://en.wikipedia.org/wiki/Failover) 기본 논리 앱을 설정하는 데 중점을 둡니다. 이렇게 하면 기본 이 손실, 중단 또는 실패를 겪는 경우 보조 데이터베이스가 작업을 수행 할 수 있습니다. 이 전략을 사용하려면 보조 논리 앱과 종속 리소스가 이미 배포되어 대체 위치에 준비되어 있어야 합니다.

좋은 DevOps 사례를 따르는 경우 이미 [Azure 리소스 관리자 템플릿을](../azure-resource-manager/management/overview.md) 사용하여 논리 앱과 해당 종속 리소스를 정의하고 배포합니다. 리소스 관리자 템플릿을 사용하면 단일 배포 정의를 사용한 다음 매개 변수 파일을 사용하여 각 배포 대상에 사용할 구성 값을 제공할 수 있습니다. 이 기능은 개발, 테스트 및 프로덕션과 같은 다양한 환경에 동일한 논리 앱을 배포할 수 있음을 의미합니다. [페어링된](../best-practices-availability-paired-regions.md)지역을 사용하는 재해 복구 전략을 지원하는 다른 Azure 지역 또는 ISEs에 동일한 논리 앱을 배포할 수도 있습니다.

장애 조치 전략의 경우 논리 앱 및 위치가 다음 요구 사항을 충족해야 합니다.

* 보조 논리 앱 인스턴스는 기본 논리 앱 인스턴스와 동일한 앱, 서비스 및 시스템에 액세스할 수 있습니다.

* 두 논리 앱 인스턴스모두 동일한 호스트 유형을 갖습니다. 따라서 두 인스턴스가 전역 다중 테넌트 Azure의 지역에 배포되거나 두 인스턴스가 모두 ISEs에 배포되어 논리 앱이 Azure 가상 네트워크의 리소스에 직접 액세스할 수 있습니다. BCDR에 대해 페어링된 지역에 대한 모범 사례 및 자세한 내용은 [비즈니스 연속성 및 재해 복구(BCDR): Azure 쌍을 이루는 지역](../best-practices-availability-paired-regions.md)참조)

  예를 들어 기본 논리 앱이 ISE에서 실행되고 [ISE 버전 커넥터,](../connectors/apis-list.md#ise-connectors)HTTP 작업을 사용하여 Azure 가상 네트워크의 리소스를 호출하거나 둘 다사용할 때 기본 위치와 보조 위치 모두 ISE여야 합니다. 이 시나리오에서는 보조 논리 앱도 보조 위치에서 기본 논리 앱과 유사한 설정이 있어야 합니다.

  > [!NOTE]
  > 고급 시나리오의 경우 다중 테넌트 Azure와 ISE를 모두 위치로 혼합할 수 있습니다. 그러나 [논리 앱이 ISE에서 실행되는 방식과 다중 테넌트 Azure 의 차이점을](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)고려하고 이해해야 합니다.

* ISEs를 사용하는 경우 [크기 조정이 수행되었는지 또는](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) 부하를 처리할 수 있는 충분한 용량이 있는지 확인합니다.

#### <a name="example-multi-tenant-azure"></a>예: 다중 테넌트 Azure

이 예제에서는 이 시나리오에 대한 전역 다중 테넌트 Azure의 별도 지역에 배포되는 기본 및 보조 논리 앱 인스턴스를 보여 주입니다. 단일 [리소스 관리자 템플릿은](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) 논리 앱 인스턴스와 해당 논리 앱에 필요한 종속 리소스를 모두 정의합니다. 별도의 매개 변수 파일은 각 배포 위치에 사용할 구성 값을 지정합니다.

![별도의 위치에서 기본 및 보조 논리 앱 인스턴스](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations.png)

#### <a name="example-integration-service-environment"></a>예: 통합 서비스 환경

이 예제에서는 이전 기본 및 보조 논리 앱 인스턴스를 보여 주지만 ISE를 분리하기 위해 배포됩니다. 단일 리소스 관리자 템플릿은 논리 앱 인스턴스, 해당 논리 앱에 필요한 종속 리소스 및 ISEs를 배포 위치로 정의합니다. 별도의 매개 변수 파일은 각 위치에서 배포에 사용할 구성 값을 정의합니다.

![서로 다른 위치에 있는 기본 및 보조 논리 앱](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations-ise.png)

<a name="resource-connections"></a>

## <a name="connections-to-resources"></a>리소스에 대한 연결

Azure Logic Apps는 기본 제공 트리거 및 작업과 논리 앱이 Azure Storage 계정, SQL Server 데이터베이스, Office 365 Outlook 전자 메일 계정 등과 같은 다른 앱, 서비스, 시스템 및 기타 리소스와 함께 작업하는 데 사용할 수 있는 수백 개의 [관리형 커넥터를](../connectors/apis-list.md) 제공합니다. 논리 앱에서 이러한 리소스에 대한 액세스가 필요한 경우 이러한 리소스에 대한 액세스를 인증하는 연결을 만듭니다. 각 연결은 특정 위치에 존재하며 다른 위치의 리소스에서 사용할 수 없는 별도의 Azure 리소스입니다.

재해 복구 전략의 경우 논리 앱 인스턴스를 기준으로 종속 리소스가 있는 위치를 고려하십시오.

* 기본 인스턴스와 종속 리소스가 서로 다른 위치에 있습니다. 이 경우 보조 인스턴스는 동일한 종속 리소스 또는 끝점에 연결할 수 있습니다. 그러나 보조 인스턴스에 대 한 연결을 만들어야 합니다. 이렇게 하면 기본 위치를 사용할 수 없게 되면 보조 사용자의 연결에는 영향을 미치지 않습니다.

  예를 들어 기본 논리 앱이 Salesforce와 같은 외부 서비스에 연결한다고 가정합니다. 일반적으로 외부 서비스의 가용성 및 위치는 논리 앱의 가용성과 독립적입니다. 이 경우 보조 인스턴스는 동일한 서비스에 연결할 수 있지만 자체 연결이 있어야 합니다.

* 기본 인스턴스와 종속 리소스가 모두 동일한 위치에 있습니다. 이 경우 보조 인스턴스가 해당 리소스에 계속 액세스할 수 있도록 종속 리소스에 다른 위치에 백업 또는 복제된 버전이 있어야 합니다.

  예를 들어 기본 논리 앱이 동일한 위치 또는 지역에 있는 서비스(예: Azure SQL Database)에 연결한다고 가정합니다. 이 전체 리전을 사용할 수 없게 되면 해당 리전의 Azure SQL Database 서비스도 사용할 수 없습니다. 이 경우 보조 인스턴스에서 복제된 데이터베이스 또는 백업 데이터베이스를 해당 데이터베이스에 대한 별도의 연결과 함께 사용하도록 할 수 있습니다.

<a name="on-premises-data-gateways"></a>

## <a name="on-premises-data-gateways"></a>온-프레미스 데이터 게이트웨이

논리 앱이 다중 테넌트 Azure에서 실행되고 SQL Server 데이터베이스와 같은 온-프레미스 리소스에 액세스해야 하는 경우 로컬 컴퓨터에 [온-프레미스 데이터 게이트웨이를](../logic-apps/logic-apps-gateway-install.md) 설치해야 합니다. 그런 다음 리소스에 대한 연결을 만들 때 논리 앱이 게이트웨이를 사용할 수 있도록 Azure Portal에서 데이터 게이트웨이 리소스를 만들 수 있습니다.

데이터 게이트웨이 리소스는 논리 앱 리소스와 마찬가지로 위치 또는 Azure 지역과 연결됩니다. 재해 복구 전략에서 논리 앱에서 사용할 수 있는 데이터 게이트웨이를 유지해야 합니다. 게이트웨이 설치가 여러 개인 경우 [게이트웨이에 대해 고가용성을 활성화할](../logic-apps/logic-apps-gateway-install.md#high-availability) 수 있습니다.

> [!NOTE]
> 논리 앱이 ISE(통합 서비스 환경)에서 실행되고 온-프레미스 데이터 원본에 대해 ISE 버전 커넥터만 사용하는 경우 ISE 커넥터가 온-프레미스 리소스에 직접 액세스하기 때문에 데이터 게이트웨이가 필요하지 않습니다.
>
> 원하는 온-프레미스 리소스에 ISE 버전 커넥터를 사용할 수 없는 경우 논리 앱은 ISE가 아닌 전역 다중 테넌트 Azure에서 실행되는 비 ISE 커넥터를 사용하여 연결을 만들 수 있습니다. 그러나 이 연결에는 온-프레미스 데이터 게이트웨이가 필요합니다.

<a name="roles"></a>

## <a name="active-active-versus-active-passive-roles"></a>능동-액티브 역할 대 능동-수동 역할

이러한 위치의 논리 앱 인스턴스가 이러한 역할을 수행할 수 있도록 기본 및 보조 위치를 설정할 수 있습니다.

| 기본 보조 역할 | Description |
|------------------------|-------------|
| *활성 활성* | 두 위치의 기본 논리 앱 인스턴스는 다음 패턴 중 하나를 수행하여 요청을 적극적으로 처리합니다. <p><p>- *로드 밸런스*: 두 인스턴스가 엔드포인트를 수신및 로드 밸런스 트래픽을 필요에 따라 각 인스턴스에 수신하도록 할 수 있습니다. <p>- *경쟁 소비자*: 두 인스턴스가 큐에서 메시지를 위해 경쟁하도록 두 인스턴스가 경쟁 소비자 역할을 하도록 할 수 있습니다. 한 인스턴스가 실패하면 다른 인스턴스가 워크로드를 대신합니다. |
| *액티브 패시브* | 기본 논리 앱 인스턴스는 전체 워크로드를 적극적으로 처리하는 반면 보조 인스턴스는 수동(비활성화 또는 비활성)입니다. 보조 는 중단 또는 오류로 인해 기본 을 사용할 수 없거나 작동하지 않는다는 신호를 기다리며 워크로드를 활성 인스턴스로 인계합니다. |
| 결합 | 일부 논리 앱은 활성-활성 역할을 하는 반면 다른 논리 앱은 능동 수동 역할을 합니다. |
|||

<a name="active-active-examples"></a>

### <a name="active-active-examples"></a>활성 활성 예제

다음 예제에서는 두 논리 앱 인스턴스가 요청 또는 메시지를 적극적으로 처리하는 활성-활성 설정을 보여 준다. 일부 다른 시스템 또는 서비스는 다음과 같은 옵션 중 하나와 같은 요청 또는 메시지를 인스턴스 간에 배포합니다.

* 트래픽을 라우팅하는 하드웨어와 같은 "물리적" 로드 밸러블러

* [Azure 로드 밸러버](../load-balancer/load-balancer-overview.md) 또는 Azure API [관리와](../api-management/api-management-key-concepts.md)같은 "소프트" 로드 밸러버. API 관리를 사용하면 들어오는 트래픽을 로드하는 방법을 결정하는 정책을 지정할 수 있습니다. 또는 상태 추적을 지원하는 서비스(예: [Azure Service Bus)를](../service-bus-messaging/service-bus-messaging-overview.md)사용할 수 있습니다.

  이 예제에서는 주로 Azure Load Balancer를 보여 주지만 시나리오의 요구 사항에 가장 적합한 옵션을 사용할 수 있습니다.

  ![로드 밸러블러 또는 상태 조정 서비스를 사용하는 "활성 활성" 설정](./media/business-continuity-disaster-recovery-guidance/active-active-setup-load-balancer.png)

* 각 논리 앱 인스턴스는 소비자 역할을 하며 두 인스턴스가 큐의 메시지에 대해 경쟁하도록 합니다.

  !["경쟁 소비자"를 사용하는 "활성" 설정](./media/business-continuity-disaster-recovery-guidance/active-active-competing-consumers-pattern.png)

<a name="active-passive-examples"></a>

### <a name="active-passive-examples"></a>능동 수동 예제

이 예제에서는 기본 논리 앱 인스턴스가 한 위치에서 활성 상태인 반면 보조 인스턴스는 다른 위치에서 비활성 상태로 유지되는 활성 수동 설정을 보여 주며, 기본 작업중단 또는 오류가 발생하는 경우 운영자가 보조 스크립트를 활성화하여 워크로드를 수행하도록 하는 스크립트를 실행하도록 할 수 있습니다.

!["경쟁 소비자"를 사용하는 "능동 수동" 설정](./media/business-continuity-disaster-recovery-guidance/active-passive-setup.png)

<a name="active-active-active-passive-examples"></a>

### <a name="combination-with-active-active-and-active-passive"></a>액티브-액티브 및 액티브 패시브와의 조합

이 예제에서는 기본 위치에 활성 논리 앱 인스턴스가 모두 있는 반면 보조 위치에는 능동 수동 논리 앱 인스턴스가 있는 결합된 설정이 표시됩니다. 기본 위치에 중단 또는 오류가 발생하면 이미 부분 워크로드를 처리하고 있는 보조 위치의 활성 논리 앱이 전체 워크로드를 대신할 수 있습니다.

* 기본 위치에서 활성 논리 앱은 메시지에 대한 Azure Service Bus 큐를 수신 대기하고 다른 활성 논리 앱은 Office 365 Outlook 폴링 트리거를 사용하여 전자 메일을 확인합니다.

* 보조 위치에서 활성 논리 앱은 동일한 Service Bus 큐의 메시지를 듣고 경쟁하여 기본 위치에 있는 논리 앱과 함께 작동합니다. 한편 수동 비활성 논리 앱은 기본 위치를 사용할 수 없게 되었지만 전자 메일을 다시 읽지 않도록 *비활성화된* 경우 전자 메일을 확인하기 위해 대기 대기 를 기다립니다.

![되풀이 트리거를 사용하는 "능동-패시브" 및 "능동-패시브" 조합](./media/business-continuity-disaster-recovery-guidance/combo-active-active-active-passive-setup.png)

<a name="state-history"></a>

## <a name="logic-app-state-and-history"></a>논리 앱 상태 및 기록

논리 앱이 트리거되고 실행되기 시작하면 앱의 상태가 앱이 시작된 동일한 위치에 저장되고 다른 위치로 전송할 수 없습니다. 오류 또는 중단이 발생하면 진행 중인 워크플로 인스턴스가 중단됩니다. 기본 및 보조 위치를 설정하면 보조 위치에서 새 워크플로 인스턴스가 실행됩니다.

<a name="reduce-abandoned-in-progress"></a>

### <a name="reduce-abandoned-in-progress-instances"></a>중단된 진행 중인 인스턴스 감소

중단된 진행 중인 워크플로 인스턴스 수를 최소화하려면 다음과 같은 다양한 메시지 패턴 중에서 선택할 수 있습니다.

* [고정 라우팅 슬립 패턴](https://docs.microsoft.com/biztalk/esb-toolkit/message-routing-patterns#routing-slip)

  비즈니스 프로세스를 더 작은 단계로 분할하는 이 엔터프라이즈 메시지 패턴입니다. 각 단계에 대해 해당 단계에 대한 워크로드를 처리하는 논리 앱을 설정합니다. 서로 통신하기 위해 논리 앱은 Azure Service Bus 큐 또는 토픽과 같은 비동기 메시징 프로토콜을 사용합니다. 프로세스를 더 작은 단계로 나누면 실패한 논리 앱 인스턴스에 갇일 수 있는 비즈니스 프로세스의 수가 줄어듭니다. 이 패턴에 대한 자세한 내용은 [엔터프라이즈 통합 패턴 - 라우팅 슬립을](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html)참조하십시오.

  이 예제에서는 각 논리 앱이 스테이지를 나타내고 Service Bus 큐를 사용하여 프로세스의 다음 논리 앱과 통신하는 라우팅 슬립 패턴을 보여 주습니다.

  ![비즈니스 프로세스를 Azure Service Bus 큐를 사용하여 서로 통신하는 논리 앱으로 표시되는 단계로 분할](./media/business-continuity-disaster-recovery-guidance/fixed-routing-slip-pattern.png)

  기본 논리 앱과 보조 논리 앱 인스턴스가 해당 위치에서 동일한 라우팅 슬립 패턴을 따르는 경우 해당 인스턴스에 대해 [활성 활성 역할을](#roles) 설정하여 경쟁 소비자 [패턴을](https://docs.microsoft.com/azure/architecture/patterns/competing-consumers) 구현할 수 있습니다.

* [프로세스 관리자(브로커) 패턴](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html)

* [시간 시간 패턴이 없는 픽 잠금](https://social.technet.microsoft.com/wiki/contents/articles/50022.azure-service-bus-how-to-peek-lock-a-message-from-queue-using-azure-logic-apps.aspx)

<a name="access-trigger-runs-history"></a>

### <a name="access-to-trigger-and-runs-history"></a>기록을 트리거하고 실행하기 위한 액세스

논리 앱의 과거 워크플로 실행에 대한 자세한 정보를 얻으려면 앱의 트리거를 검토하고 기록을 실행할 수 있습니다. 논리 앱의 기록 실행 기록은 해당 논리 앱이 실행된 동일한 위치 또는 지역에 저장되므로 이 기록을 다른 위치로 마이그레이션할 수 없습니다. 기본 인스턴스가 보조 인스턴스로 장애 발생하면 각 인스턴스의 트리거에만 액세스하고 해당 인스턴스가 실행된 각 위치에서 의 기록을 실행합니다. 그러나 Azure Log Analytics 작업 영역으로 진단 이벤트를 보내도록 논리 앱을 설정하여 논리 앱의 기록에 대한 위치 관련 정보를 얻을 수 있습니다. 그런 다음 여러 위치에서 실행되는 논리 앱에서 상태 및 기록을 검토할 수 있습니다.

<a name="trigger-types-guidance"></a>

## <a name="trigger-type-guidance"></a>트리거 유형 지침

논리 앱에서 사용하는 트리거 유형에 따라 재해 복구 전략의 위치에 논리 앱을 설정하는 방법에 대한 옵션이 결정됩니다. 논리 앱에서 사용할 수 있는 사용 가능한 트리거 유형은 다음과 같습니다.

* [되풀이 트리거](#recurrence-trigger)
* [폴링 트리거](#polling-trigger)
* [요청 트리거](#request-trigger)
* [웹후크 트리거](#webhook-trigger)

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>되풀이 트리거

**되풀이** 트리거는 특정 서비스 또는 끝점과 독립적이며 지정된 일정만 을 기반으로 하며 다른 조건(예:

* 10분마다 와 같은 고정 된 주파수 및 간격
* 매월 마지막 월요일 오후 5:00와 같은 고급 일정

논리 앱이 되풀이 트리거로 시작하면 [활성 수동 역할을](#roles)사용하여 기본 및 보조 논리 앱 인스턴스를 설정해야 합니다. 중단 또는 재해 후 비즈니스 프로세스를 복원하기 위한 대상 기간을 나타내는 *RTO(복구 시간 목표)를* 줄이려면 [능동적 역할과](#roles) [수동-활성 역할의](#roles)조합으로 논리 앱 인스턴스를 설정할 수 있습니다. 이 설정에서는 여러 위치에서 일정을 분할합니다.

예를 들어 10분마다 실행해야 하는 논리 앱이 있다고 가정합니다. 기본 위치를 사용할 수 없게 되면 보조 위치가 작업을 대신할 수 있도록 논리 앱 및 위치를 설정할 수 있습니다.

![되풀이 트리거를 사용하는 "능동-패시브" 및 "패시브-액티브" 조합](./media/business-continuity-disaster-recovery-guidance/combo-active-passive-passive-active-setup.png)

* 기본 위치에서 이러한 논리 앱에 대해 [능동 수동 역할을](#roles) 설정합니다.

  * *활성* 사용 논리 앱의 경우 되풀이 트리거를 시간 의 맨 위에서 시작하여 20분마다 반복(예: 오전 9:00, 오전 9:20 등)을 반복합니다.

  * *수동* 비활성화 논리 앱의 경우 되풀이 트리거를 동일한 일정으로 설정하지만 10분 후 10분 간격으로 시작하여 20분간격(예: 오전 9:10, 오전 9시 30분 등)을 반복합니다.

* 보조 위치에서 이러한 논리 앱에 대해 [수동 활성을](#roles) 설정합니다.

  * *수동* 비활성화 논리 앱의 경우 되풀이 트리거를 기본 위치의 활성 논리 앱과 동일한 일정으로 설정하고 시간 의 맨 위에 있고 20분마다 반복됩니다(예: 오전 9:00, 오전 9:10 등).

  * *활성* 사용 논리 앱의 경우 되풀이 트리거를 기본 위치의 수동 논리 앱과 동일한 일정으로 설정하여 10분 후 10분 간격으로 시작하여 20분마다 반복됩니다(예: 오전 9:10, 오전 9:20 등).

이제 기본 위치에서 중단 이벤트가 발생하면 대체 위치에서 수동 논리 앱을 활성화합니다. 이렇게 하면 오류를 찾는 데 시간이 걸리면 이 설정은 지연 중에 누락된 되풀이 횟수를 제한합니다.

<a name="polling-trigger"></a>

### <a name="polling-trigger"></a>폴링 트리거

특정 서비스 또는 끝점에서 새 데이터를 사용할 수 있는지 정기적으로 확인하려면 논리 앱에서 고정 된 되풀이 일정에 따라 서비스 또는 끝점을 반복적으로 호출하는 *폴링* 트리거를 사용할 수 있습니다. 서비스 또는 끝점이 제공하는 데이터에는 다음 형식 중 하나가 있을 수 있습니다.

* 항상 읽을 수 있는 데이터를 설명하는 정적 데이터
* 읽기 후 더 이상 사용할 수 없는 데이터를 설명하는 휘발성 데이터

동일한 데이터를 반복적으로 읽지 않으려면 논리 앱에서 클라이언트 측또는 서버, 서비스 또는 시스템 측에서 상태를 유지 관리하여 이전에 읽은 데이터를 기억해야 합니다.

* 클라이언트 측 상태와 함께 작동하는 논리 앱은 상태를 유지할 수 있는 트리거를 사용합니다.

  예를 들어 전자 메일 받은 편지함에서 새 메시지를 읽는 트리거에는 트리거가 가장 최근에 읽은 메시지를 기억할 수 있어야 합니다. 이렇게 하면 트리거는 다음 읽지 않은 메시지가 도착할 때만 논리 앱을 시작합니다.

* 서버, 서비스 또는 시스템 측 상태와 함께 작동하는 논리 앱은 서버, 서비스 또는 시스템 측에 있는 속성 값 또는 설정을 사용합니다.

  예를 들어 데이터베이스에서 행을 읽는 쿼리 기반 트리거에는 행에 `isRead` 로 설정된 열이 있어야 합니다. `FALSE` 트리거가 행을 읽을 때마다 논리 앱은 열을 에서 `isRead` `FALSE` 로 `TRUE`변경하여 해당 행을 업데이트합니다.

  이 서버 측 접근 방식은 논리 앱이 메시지를 처리하는 동안 트리거가 메시지를 읽고 잠글 수 있는 대기 의미 체계가 있는 Service Bus 큐 또는 토픽에서도 유사하게 작동합니다. 논리 앱이 처리를 완료하면 트리거가 큐 또는 토픽에서 메시지를 삭제합니다.

재해 복구 관점에서 논리 앱의 기본 및 보조 인스턴스를 설정할 때 논리 앱이 클라이언트 측또는 서버 측에서 상태를 추적하는지 여부에 따라 이러한 동작을 고려해야 합니다.

* 클라이언트 쪽 상태에서 작동하는 논리 앱의 경우 논리 앱이 동일한 메시지를 두 번 이상 읽지 않도록 합니다. 한 위치에서만 특정 시간에 활성 논리 앱 인스턴스를 가질 수 있습니다. 대체 위치의 논리 앱 인스턴스가 기본 인스턴스가 대체 위치로 장애 토사될 때까지 비활성 상태이거나 비활성화되어 있는지 확인합니다.

  예를 들어 Office 365 Outlook 트리거는 클라이언트 쪽 상태를 유지하고 중복 을 읽지 않도록 가장 최근에 읽은 전자 메일의 타임스탬프를 추적합니다.

* 서버 쪽 상태에서 작동하는 논리 앱의 경우 논리 앱 인스턴스를 설정하여 경쟁 소비자로 작동하는 [활성-활성 역할](#roles) 또는 대체 인스턴스가 대체 위치로 장애 처리될 때까지 대체 인스턴스가 대기하는 [능동 수동 역할을](#roles) 수행할 수 있습니다.

  예를 들어 Azure Service Bus 큐와 같은 메시지 큐에서 읽는 것은 대기 서비스가 메시지에 대한 잠금을 유지관리하여 다른 클라이언트가 동일한 메시지를 읽지 못하도록 하기 때문에 서버 측 상태를 사용합니다.

  > [!NOTE]
  > 논리 앱이 서비스 버스 큐에서 특정 순서로 메시지를 읽어야 하는 경우 경쟁 소비자 패턴을 사용할 수 있지만 [ *순차 호위* 패턴이라고도](https://docs.microsoft.com/azure/architecture/patterns/sequential-convoy)하는 Service Bus 세션과 결합될 때만 사용할 수 있습니다. 그렇지 않으면 능동 수동 역할을 사용 하 고 논리 앱 인스턴스를 설정 해야 합니다.

<a name="request-trigger"></a>

### <a name="request-trigger"></a>요청 트리거

**요청** 트리거는 다른 앱, 서비스 및 시스템에서 논리 앱을 호출할 수 있게 하며 일반적으로 이러한 기능을 제공하는 데 사용됩니다.

* 다른 사용자가 호출할 수 있는 논리 앱에 대한 직접 REST API

  예를 들어 요청 트리거를 사용하여 논리 앱을 시작하여 다른 논리 앱이 **호출 워크플로 - 논리 앱** 작업을 사용하여 트리거를 호출할 수 있습니다.

* 논리 앱에 대한 [웹후크](#webhook-trigger) 또는 콜백 메커니즘

* 특정 작업을 수행하는 PowerShell 스크립트를 사용하여 논리 앱을 호출하기 위해 사용자 작업 또는 루틴을 수동으로 실행할 수 있는 방법

재해 복구 관점에서 요청 트리거는 논리 앱이 아무 작업도 하지 않고 다른 서비스 또는 시스템이 명시적으로 트리거를 호출할 때까지 대기하기 때문에 수동 수신기입니다. 수동 끝점으로 다음과 같은 방법으로 기본 및 보조 인스턴스를 설정할 수 있습니다.

* [활성 상태](#roles): 두 인스턴스 모두 요청 또는 호출을 적극적으로 처리합니다. 호출자 또는 라우터는 이러한 인스턴스 간에 트래픽의 균형을 맞추거나 분산합니다.

* [활성 수동](#roles): 기본 인스턴스만 활성 상태이며 모든 작업을 처리하지만 보조 인스턴스는 기본 인스턴스가 중단 또는 실패를 경험할 때까지 기다립니다. 호출자 또는 라우터는 보조 인스턴스를 호출할 시기를 결정합니다.

권장 아키텍처로 Azure API Management를 요청 트리거를 사용하는 논리 앱의 프록시로 사용할 수 있습니다. API Management는 [기본 제공 지역 간 복원력과 여러 엔드포인트에서 트래픽을 라우팅할 수 있는 기능을](https://docs.microsoft.com/azure/api-management/api-management-howto-deploy-multi-region)제공합니다.

<a name="webhook-trigger"></a>

### <a name="webhook-trigger"></a>웹후크 트리거

*웹후크* 트리거는 로직 앱이 해당 서비스에 *콜백 URL을* 전달하여 서비스를 구독할 수 있는 기능을 제공합니다. 그런 다음 논리 앱은 해당 서비스 끝점에서 특정 이벤트가 발생할 때까지 듣고 기다릴 수 있습니다. 이벤트가 발생하면 서비스는 콜백 URL을 사용하여 웹후크 트리거를 호출한 다음 논리 앱을 실행합니다. 활성화하면 웹후크 트리거가 서비스를 구독합니다. 비활성화하면 트리거가 서비스의 구독을 취소합니다.

재해 복구 관점에서 웹후크 트리거를 사용하는 기본 및 보조 인스턴스를 설정하여 하나의 인스턴스만 구독된 끝점에서 이벤트 또는 메시지를 수신해야 하므로 능동적 수동 역할을 수행합니다.

## <a name="assess-primary-instance-health"></a>1차 인스턴스 상태 평가

재해 복구 전략이 작동하려면 솔루션에 다음 작업을 수행할 수 있는 방법이 필요합니다.

* [기본 인스턴스의 가용성 확인](#check-primary-availability)
* [기본 인스턴스의 상태 모니터링](#monitor-primary-health)
* [보조 인스턴스 활성화](#activate-secondary)

이 섹션에서는 완전히 사용할 수 있는 솔루션 중 하나또는 사용자 고유의 디자인의 기초로 사용할 수 있는 솔루션에 대해 설명합니다. 다음은 이 솔루션에 대한 상위 수준 시각적 개요입니다.

![기본 위치에서 상태 확인 논리 앱을 모니터링하는 Watchdog 논리 앱 만들기](./media/business-continuity-disaster-recovery-guidance/check-location-health-watchdog.png)

<a name="check-primary-availability"></a>

### <a name="check-primary-instance-availability"></a>기본 인스턴스 가용성 확인

기본 인스턴스를 사용할 수 있는지, 실행 중이며, 작업할 수 있는지 확인하려면 기본 인스턴스와 동일한 위치에 있는 "상태 확인" 논리 앱을 만들 수 있습니다. 그런 다음 대체 위치에서 이 상태 확인 앱을 호출할 수 있습니다. 상태 확인 앱이 성공적으로 응답하는 경우 해당 지역의 Azure Logic Apps 서비스에 대한 기본 인프라를 사용할 수 있고 작동합니다. 상태 확인 앱이 응답하지 않으면 위치가 더 이상 정상이 아니라고 가정할 수 있습니다.

이 작업의 경우 다음 작업을 수행하는 기본 상태 확인 논리 앱을 만듭니다.

1. 요청 트리거를 사용하여 Watchdog 앱에서 호출을 받습니다.

1. 확인된 논리 앱이 응답 작업을 사용하여 작동하는지 여부를 나타내는 상태로 응답합니다.

   > [!IMPORTANT]
   > 상태 확인 논리 앱은 앱이 비동기적이지 않고 동기적으로 응답되도록 응답 작업을 사용해야 합니다.

1. 선택적으로 기본 위치가 정상인지 여부를 추가로 확인하려면 이 위치에서 대상 논리 앱과 상호 작용하는 다른 서비스의 상태를 고려할 수 있습니다. 상태 확인 논리 앱을 확장하여 이러한 다른 서비스에 대한 상태도 평가합니다.

<a name="monitor-primary-health"></a>

### <a name="create-a-watchdog-logic-app"></a>감시 논리 앱 만들기

기본 인스턴스의 상태를 모니터링하고 상태 확인 논리 앱을 호출하려면 *대체 위치에*"watchdog" 논리 앱을 만듭니다. 예를 들어 상태 확인 논리호출에 실패할 경우 Watchdog가 작업 팀에 경고를 보내 오류를 조사하고 기본 인스턴스가 응답하지 않는 이유를 조사할 수 있도록 Watchdog 논리 앱을 설정할 수 있습니다.

> [!IMPORTANT]
> Watchdog 논리 앱이 *기본 위치와 다른 위치에*있는지 확인합니다. 기본 위치에서 Logic Apps 서비스에 문제가 발생하면 Watchdog 논리 앱이 실행되지 않을 수 있습니다.

이 작업의 경우 보조 위치에서 다음 작업을 수행하는 Watchdog 논리 앱을 만듭니다.

1. 되풀이 트리거를 사용하여 고정 또는 예약된 되풀이에 따라 실행합니다.

   되풀이는 RTO(복구 시간 목표)에 대한 허용 오차 수준 보다 낮은 값으로 설정할 수 있습니다.

1. HTTP 작업을 사용하여 기본 위치에서 상태 확인 논리 앱을 호출합니다.

<a name="activate-secondary"></a>

### <a name="activate-your-secondary-instance"></a>보조 인스턴스 활성화

보조 인스턴스를 자동으로 활성화하려면 [Azure Resource Manager 커넥터와](https://docs.microsoft.com/connectors/arm/) 같은 관리 API를 호출하는 논리 앱을 만들어 보조 위치에서 적절한 논리 앱을 활성화할 수 있습니다. 특정 수의 오류가 발생한 후 Watchdog 앱을 확장하여 이 활성화 논리 앱을 호출할 수 있습니다.

<a name="collect-diagnostic-data"></a>

## <a name="collect-diagnostic-data"></a>진단 데이터 수집

논리 앱 실행에 대한 로깅을 설정하고 추가 처리 및 처리를 위해 Azure 저장소, Azure 이벤트 허브 및 Azure Log Analytics와 같은 서비스에 결과 진단 데이터를 보낼 수 있습니다.

* Azure Log Analytics에서 이 데이터를 사용하려는 경우 논리 앱의 **진단 설정을** 설정하고 데이터를 여러 Log Analytics 작업 영역으로 전송하여 기본 및 보조 위치 모두에 대해 데이터를 사용할 수 있도록 할 수 있습니다. 자세한 내용은 [Azure 모니터 로그 설정 및 Azure 논리 앱](../logic-apps/monitor-logic-apps-log-analytics.md)에 대한 진단 데이터 수집을 참조하세요.

* Azure 저장소 또는 Azure 이벤트 허브로 데이터를 보내려면 지리적 중복성을 설정하여 기본 및 보조 위치 모두에 대해 데이터를 사용할 수 있도록 설정할 수 있습니다. 자세한 내용은 다음 문서를 참조하세요.<p>

  * [Azure Blob 저장소 재해 복구 및 계정 장애 조치](../storage/common/storage-disaster-recovery-guidance.md)
  * [Azure 이벤트 허브 지리적 재해 복구](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>다음 단계

* [Azure에 대한 복원력 개요](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)
* [특정 Azure 서비스에 대한 복원력 검사 목록](https://docs.microsoft.com/azure/architecture/checklist/resiliency-per-service)
* [Azure의 복원력을 위한 데이터 관리](https://docs.microsoft.com/azure/architecture/framework/resiliency/data-management)
* [Azure 응용 프로그램에 대한 백업 및 재해 복구](https://docs.microsoft.com/azure/architecture/framework/resiliency/backup-and-recovery)
* [지역 전체의 서비스 중단으로부터 복구](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)
* [Azure 서비스에 대한 Microsoft 서비스 수준 계약(SLA)](https://azure.microsoft.com/support/legal/sla/)
