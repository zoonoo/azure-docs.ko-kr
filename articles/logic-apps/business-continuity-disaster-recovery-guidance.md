---
title: 비즈니스 연속성 및 재해 복구
description: 데이터를 보호 하 고, 중단 이벤트 로부터 신속히 복구 하 고, 중요 한 비즈니스 기능에 필요한 리소스를 복원 하 고, Azure Logic Apps에 대 한 비즈니스 연속성을 유지 하는 전략을 설계
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 0a36cb468ebcb77c0614bffd0afc392df3655c20
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89658208"
---
# <a name="business-continuity-and-disaster-recovery-for-azure-logic-apps"></a>Azure Logic Apps에 대 한 비즈니스 연속성 및 재해 복구

비즈니스 및 고객에 게 예측할 수 없는 이벤트의 영향과 영향을 줄이려면 데이터를 보호 하 고 중요 한 비즈니스 기능을 지 원하는 리소스를 신속 하 게 복원 하 고 [ *비즈니스 연속성* (BC)](https://en.wikipedia.org/wiki/Business_continuity_planning)을 유지 하기 위해 작업을 계속 실행할 수 있도록 DR ( [ *재해 복구* )](https://en.wikipedia.org/wiki/Disaster_recovery) 솔루션이 준비 되어 있는지 확인 합니다. 예를 들어 중단에는 중단, 기본 인프라의 손실 또는 저장소, 네트워크 또는 계산 리소스와 같은 구성 요소, 복구할 수 없는 응용 프로그램 오류 또는 전체 데이터 센터 손실이 포함 될 수 있습니다. 기업 또는 조직은 비즈니스 연속성 및 재해 복구 (BCDR) 솔루션을 준비 하 여 더 신속 하 게 중단, 계획 또는 계획 되지 않은 상태로 신속 하 게 대응 하 고 고객의 가동 중지 시간을 줄일 수 있습니다.

이 문서에서는 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)를 사용 하 여 자동화 된 워크플로를 빌드할 때 적용할 수 있는 지침 및 전략을 BCDR 합니다. 논리 앱 워크플로를 통해 응용 프로그램, 클라우드 서비스 및 온-프레미스 시스템 간에 데이터를 보다 쉽게 통합 하 고 오케스트레이션 하 여 작성 해야 하는 코드의 양을 줄일 수 있습니다. BCDR를 계획 하는 경우 논리 앱 뿐만 아니라 논리 앱에서 사용 하는 다음과 같은 Azure 리소스도 고려해 야 합니다.

* 논리 앱에서 다른 앱, 서비스 및 시스템으로 만드는 [연결](../connectors/apis-list.md) 자세한 내용은이 항목의 뒷부분에 있는 [리소스에 연결](#resource-connections) 을 참조 하세요.

* 온 [-프레미스 데이터 게이트웨이](../logic-apps/logic-apps-gateway-connection.md) 는 논리 앱에서 만들고 사용 하 여 온-프레미스 시스템의 데이터에 액세스 하는 Azure 리소스입니다. 각 게이트웨이 리소스는 로컬 컴퓨터에 별도의 [데이터 게이트웨이 설치](../logic-apps/logic-apps-gateway-install.md) 를 나타냅니다. 자세한 내용은이 항목의 뒷부분에 나오는 [온-프레미스 데이터 게이트웨이](#on-premises-data-gateways) 를 참조 하세요.

* 논리 앱이 [b2b (기업 간) 엔터프라이즈 통합](../logic-apps/logic-apps-enterprise-integration-overview.md) 시나리오에 사용 하는 아티팩트를 정의 하 고 저장 하는 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 입니다. 예를 들어 [통합 계정에 대 한 지역 간 재해 복구를 설정할](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)수 있습니다.

* [ISEs (Integration service environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) -Azure virtual network 내에서 격리 된 Logic Apps 런타임 인스턴스에서 실행 되는 논리 앱을 만듭니다. 그런 다음 이러한 논리 앱은 해당 가상 네트워크의 방화벽 뒤에 보호 되는 리소스에 액세스할 수 있습니다.

<a name="primary-secondary-locations"></a>

## <a name="primary-and-secondary-locations"></a>기본 및 보조 위치

각 논리 앱은 배포에 사용할 위치를 지정 해야 합니다. 이 위치는 글로벌 다중 테 넌 트 Azure의 공용 지역 (예: "미국 서 부") 또는 이전에 Azure virtual network에 만들어 배포한 integration service environment (ISE) 중 하나입니다. ISE에서 논리 앱을 실행 하는 것은 글로벌 Azure 지역에서 논리 앱을 실행 하는 것과 유사 합니다. 즉, 재해 복구 전략이 두 시나리오에 모두 적용 될 수 있습니다. 그러나 ISEs는 ISEs만 사용할 수 있는 리소스에 대 한 액세스를 구성 하는 등의 다른 고려 사항도 있습니다.

> [!NOTE]
> 논리 앱이 통합 계정에 저장 된 거래 업체, 규약, 스키마, 맵 및 인증서와 같은 B2B 아티팩트와도 연동 되는 경우 통합 계정 및 논리 앱 모두 동일한 위치를 지정 해야 합니다.

이 재해 복구 전략은 Azure Logic Apps도 사용할 수 있는 대체 위치에서 대기 또는 백업 논리 앱으로 [*장애 조치 (failover)*](https://en.wikipedia.org/wiki/Failover) 하도록 기본 논리 앱을 설정 하는 데 중점을 둔 것입니다. 이렇게 하면 주 복제본이 손실, 중단 또는 실패 하는 경우 보조 데이터베이스에서 작업을 수행할 수 있습니다. 이 전략을 사용 하려면 보조 논리 앱 및 종속 리소스를 대체 위치에 이미 배포 하 고 준비 해야 합니다.

좋은 DevOps 사례를 따르는 경우 이미 [Azure Resource Manager 템플릿을](../azure-resource-manager/management/overview.md) 사용 하 여 논리 앱 및 해당 종속 리소스를 정의 하 고 배포 합니다. 리소스 관리자 템플릿은 단일 배포 정의를 사용 하 고 매개 변수 파일을 사용 하 여 각 배포 대상에 사용할 구성 값을 제공 하는 기능을 제공 합니다. 이 기능은 동일한 논리 앱을 개발, 테스트, 프로덕션 등의 다른 환경에 배포할 수 있음을 의미 합니다. [쌍을 이루는 지역을](../best-practices-availability-paired-regions.md)사용 하는 재해 복구 전략을 지 원하는 다른 Azure 지역 또는 ISEs에 동일한 논리 앱을 배포할 수도 있습니다.

장애 조치 (failover) 전략의 경우 논리 앱 및 위치는 다음 요구 사항을 충족 해야 합니다.

* 보조 논리 앱 인스턴스는 기본 논리 앱 인스턴스와 동일한 앱, 서비스 및 시스템에 액세스할 수 있습니다.

* 두 논리 앱 인스턴스는 동일한 호스트 유형을 갖습니다. 따라서 두 인스턴스 모두 글로벌 다중 테 넌 트 Azure의 지역에 배포 되거나 두 인스턴스가 모두 ISEs에 배포 되므로 논리 앱이 Azure 가상 네트워크의 리소스에 직접 액세스할 수 있습니다. BCDR에 대 한 쌍을 이루는 지역에 대 한 모범 사례 및 자세한 내용은 [BCDR (비즈니스 연속성 및 재해 복구): Azure 쌍을 이루는 지역](../best-practices-availability-paired-regions.md)을 참조 하세요.

  예를 들어 기본 논리 앱이 ISE에서 실행 되 고 [ise 버전의 커넥터](../connectors/apis-list.md#ise-connectors), Azure 가상 네트워크에서 리소스를 호출 하는 HTTP 작업 또는 둘 다를 사용 하는 경우 기본 및 보조 위치 모두 ISEs 되어야 합니다. 이 시나리오에서 보조 논리 앱은 보조 위치에서 기본 논리 앱과 유사한 설정도 있어야 합니다.

  > [!NOTE]
  > 고급 시나리오의 경우 다중 테 넌 트 Azure와 ISE를 위치로 혼합할 수 있습니다. 그러나 [ISE와 다중 테 넌 트 Azure에서 논리 앱이 실행 되는 방법 간의 차이점](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)을 고려 하 고 이해 해야 합니다.

* ISEs를 사용 하는 경우 확장 하거나 로드를 처리 하기에 [충분 한 용량이 있는지 확인](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) 합니다.

#### <a name="example-multi-tenant-azure"></a>예: 다중 테 넌 트 Azure

이 예제에서는이 시나리오에 대해 전역 다중 테 넌 트 Azure의 개별 지역에 배포 되는 기본 및 보조 논리 앱 인스턴스를 보여 줍니다. 단일 [리소스 관리자 템플릿은](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) 논리 앱 인스턴스와 해당 논리 앱에 필요한 종속 리소스를 모두 정의 합니다. 별도의 매개 변수 파일은 각 배포 위치에 사용할 구성 값을 지정 합니다.

![별도의 위치에 있는 기본 및 보조 논리 앱 인스턴스](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations.png)

#### <a name="example-integration-service-environment"></a>예: Integration service environment

이 예에서는 이전 주 및 보조 논리 앱 인스턴스를 보여 주지만 별도의 ISEs에 배포 했습니다. 단일 리소스 관리자 템플릿은 논리 앱 인스턴스, 해당 논리 앱에 필요한 종속 리소스 및 배포 위치로 ISEs를 정의 합니다. 별도의 매개 변수 파일은 각 위치의 배포에 사용할 구성 값을 정의 합니다.

![다른 위치에 있는 기본 및 보조 논리 앱](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations-ise.png)

<a name="resource-connections"></a>

## <a name="connections-to-resources"></a>리소스에 대 한 연결

Azure Logic Apps는 논리 앱이 다른 앱, 서비스, 시스템 및 기타 리소스 (예: Azure Storage 계정, SQL Server 데이터베이스, 회사 또는 학교 전자 메일 계정 등)와 함께 작동 하는 데 사용할 수 있는 [수백 개의 관리 되는 커넥터](../connectors/apis-list.md) 를 포함 하는 기본 제공 트리거와 작업을 제공 합니다. 논리 앱에서 이러한 리소스에 액세스 해야 하는 경우 이러한 리소스에 대 한 액세스를 인증 하는 연결을 만듭니다. 각 연결은 특정 위치에 있는 별도의 Azure 리소스 이며 다른 위치의 리소스에서 사용할 수 없습니다.

재해 복구 전략을 위해 논리 앱 인스턴스를 기준으로 종속 리소스가 존재 하는 위치를 고려 합니다.

* 주 인스턴스와 종속 리소스가 다른 위치에 있습니다. 이 경우 보조 인스턴스는 동일한 종속 리소스 또는 끝점에 연결할 수 있습니다. 그러나 보조 인스턴스에 대해서만 연결을 만들어야 합니다. 이렇게 하면 기본 위치를 사용할 수 없게 되 면 보조의 연결이 영향을 받지 않습니다.

  예를 들어 기본 논리 앱이 Salesforce와 같은 외부 서비스에 연결 된다고 가정 합니다. 일반적으로 외부 서비스의 가용성과 위치는 논리 앱의 가용성과 독립적입니다. 이 경우 보조 인스턴스는 동일한 서비스에 연결할 수 있지만 자체 연결이 있어야 합니다.

* 주 인스턴스와 종속 리소스가 모두 같은 위치에 있습니다. 이 경우 종속 리소스의 다른 위치에 백업이 나 복제 된 버전이 있어야 보조 인스턴스에서 해당 리소스에 계속 액세스할 수 있습니다.

  예를 들어 기본 논리 앱이 동일한 위치 또는 지역에 있는 서비스 (예: Azure SQL Database)에 연결 된다고 가정 합니다. 이 전체 지역을 사용할 수 없게 되 면 해당 지역의 Azure SQL Database 서비스를 사용할 수 없게 될 수도 있습니다. 이 경우 보조 인스턴스는 해당 데이터베이스에 대 한 별도의 연결과 함께 복제 또는 백업 데이터베이스를 사용 하려고 합니다.

<a name="on-premises-data-gateways"></a>

## <a name="on-premises-data-gateways"></a>온-프레미스 데이터 게이트웨이

논리 앱이 다중 테 넌 트 Azure에서 실행 되 고 SQL Server 데이터베이스와 같은 온-프레미스 리소스에 액세스 해야 하는 경우에는 로컬 컴퓨터에 [온-프레미스 데이터 게이트웨이](../logic-apps/logic-apps-gateway-install.md) 를 설치 해야 합니다. 그런 다음 해당 리소스에 대 한 연결을 만들 때 논리 앱에서 게이트웨이를 사용할 수 있도록 Azure Portal에서 데이터 게이트웨이 리소스를 만들 수 있습니다.

데이터 게이트웨이 리소스는 논리 앱 리소스와 마찬가지로 위치나 Azure 지역에 연결 됩니다. 재해 복구 전략에서 논리 앱이 사용할 수 있도록 데이터 게이트웨이를 사용할 수 있는지 확인 합니다. 게이트웨이 설치가 여러 개 있는 경우 [게이트웨이에 대해 고가용성을 사용 하도록 설정할](../logic-apps/logic-apps-gateway-install.md#high-availability) 수 있습니다.

> [!NOTE]
> 논리 앱이 ISE (integration service environment)에서 실행 되 고 온-프레미스 데이터 원본에 ISE 버전의 커넥터만 사용 하는 경우 ISE 커넥터는 온-프레미스 리소스에 직접 액세스를 제공 하기 때문에 데이터 게이트웨이가 필요 하지 않습니다.
>
> 원하는 온-프레미스 리소스에 대해 ISE 버전의 커넥터를 사용할 수 없는 경우에도 논리 앱은 ise가 아닌 전역 다중 테 넌 트 Azure에서 실행 되는 ISE가 아닌 커넥터를 사용 하 여 연결을 만들 수 있습니다. 그러나이 연결에는 온-프레미스 데이터 게이트웨이가 필요 합니다.

<a name="roles"></a>

## <a name="active-active-versus-active-passive-roles"></a>활성-활성 및 활성-수동 역할

기본 및 보조 위치를 설정 하 여 이러한 위치의 논리 앱 인스턴스가 이러한 역할을 수행할 수 있도록 할 수 있습니다.

| 주-보조 역할 | 설명 |
|------------------------|-------------|
| *활성-활성* | 두 위치의 기본 및 보조 논리 앱 인스턴스는 다음 패턴 중 하나를 수행 하 여 요청을 적극적으로 처리 합니다. <p><p>- *부하 분산*: 두 인스턴스가 끝점을 수신 대기 하 고 필요에 따라 각 인스턴스에 대 한 트래픽 부하를 분산할 수 있습니다. <p>- *경쟁 소비자*: 인스턴스가 큐의 메시지를 경합 하도록 두 인스턴스를 경쟁 소비자로 작동 시킬 수 있습니다. 한 인스턴스가 실패 하면 다른 인스턴스는 작업을 수행 합니다. |
| *활성-수동* | 주 논리 앱 인스턴스는 전체 워크 로드를 적극적으로 처리 하는 반면, 보조 인스턴스는 수동 (비활성화 또는 비활성)입니다. 보조는 중단 또는 실패로 인해 주 복제본을 사용할 수 없거나 작동 하지 않는 신호를 대기 하 고 작업을 활성 인스턴스로 사용 합니다. |
| 결합 | 일부 논리 앱은 활성-활성 역할을 수행 하지만 다른 논리 앱은 활성-수동 역할을 수행 합니다. |
|||

<a name="active-active-examples"></a>

### <a name="active-active-examples"></a>활성-활성 예

다음 예에서는 두 논리 앱 인스턴스가 요청 또는 메시지를 적극적으로 처리 하는 활성-활성 설정을 보여 줍니다. 다른 시스템 또는 서비스는 인스턴스 간에 요청 또는 메시지를 배포 합니다. 예를 들어 다음 옵션 중 하나입니다.

* 트래픽을 라우팅하는 하드웨어와 같은 "실제" 부하 분산 장치

* [Azure Load Balancer](../load-balancer/load-balancer-overview.md) 또는 [Azure API Management](../api-management/api-management-key-concepts.md)와 같은 "소프트" 부하 분산 장치입니다. API Management를 사용 하 여 들어오는 트래픽의 부하를 분산 하는 방법을 결정 하는 정책을 지정할 수 있습니다. 또는 상태 추적을 지 원하는 서비스 (예: [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md))를 사용할 수 있습니다.

  이 예제에서는 Azure Load Balancer를 주로 보여 주지만 시나리오 요구 사항에 가장 적합 한 옵션을 사용할 수 있습니다.

  ![부하 분산 장치 또는 상태 저장 서비스를 사용 하는 "활성-활성" 설치](./media/business-continuity-disaster-recovery-guidance/active-active-setup-load-balancer.png)

* 각 논리 앱 인스턴스는 소비자 역할을 하며 두 인스턴스가 모두 큐의 메시지에 대해 경합 합니다.

  !["경쟁 소비자"를 사용 하는 "활성-활성" 설치](./media/business-continuity-disaster-recovery-guidance/active-active-competing-consumers-pattern.png)

<a name="active-passive-examples"></a>

### <a name="active-passive-examples"></a>활성-수동 예

이 예에서는 기본 논리 앱 인스턴스가 한 위치에서 활성 상태이 고 보조 인스턴스가 다른 위치에서 비활성 상태로 유지 되는 활성-수동 설정을 보여 줍니다. 주 데이터베이스에서 중단 또는 실패를 경험 하는 경우 작업을 수행 하기 위해 보조 복제본을 활성화 하는 스크립트를 운영자가 실행할 수 있습니다.

!["경쟁 소비자"를 사용 하는 "능동-수동" 설정](./media/business-continuity-disaster-recovery-guidance/active-passive-setup.png)

<a name="active-active-active-passive-examples"></a>

### <a name="combination-with-active-active-and-active-passive"></a>활성-활성 및 능동-수동과의 조합

이 예에서는 기본 위치에 활성 논리 앱 인스턴스가 모두 있고 보조 위치에 활성-수동 논리 앱 인스턴스가 있는 경우 결합 된 설정을 보여 줍니다. 기본 위치에서 중단이 발생 하거나 오류가 발생 하는 경우 보조 워크 로드를 이미 처리 하 고 있는 보조 위치의 활성 논리 앱은 전체 작업을 수행할 수 있습니다.

* 기본 위치에서 활성 논리 앱은 메시지에 대 한 Azure Service Bus 큐를 수신 하는 반면, 다른 활성 논리 앱은 Office 365 Outlook 폴링 트리거를 사용 하 여 전자 메일을 확인 합니다.

* 보조 위치에서 활성 논리 앱은 동일한 Service Bus 큐의 메시지를 수신 하 고 경합 하 여 기본 위치의 논리 앱과 함께 작동 합니다. 한편, 수동 비활성 논리 앱은 기본 위치를 사용할 수 없게 되 고 다시 읽는 메일을 방지 하기 위해 *사용 하지 않도록 설정* 된 경우 대기 상태에서 전자 메일을 확인 하도록 대기 합니다.

![되풀이 트리거를 사용 하는 "능동-수동" 및 "능동-수동" 조합](./media/business-continuity-disaster-recovery-guidance/combo-active-active-active-passive-setup.png)

<a name="state-history"></a>

## <a name="logic-app-state-and-history"></a>논리 앱 상태 및 기록

논리 앱이 트리거되고 실행을 시작 하면 앱의 상태가 앱이 시작 된 위치와 동일한 위치에 저장 되 고 다른 위치로 이전할 수 없습니다. 오류가 발생 하거나 중단이 발생 하면 진행 중인 워크플로 인스턴스가 중단 됩니다. 기본 및 보조 위치를 설정 하면 새 워크플로 인스턴스가 보조 위치에서 실행 되기 시작 합니다.

<a name="reduce-abandoned-in-progress"></a>

### <a name="reduce-abandoned-in-progress-instances"></a>중단 중인 진행 중인 인스턴스 줄이기

중단 된 진행 중인 워크플로 인스턴스의 수를 최소화 하기 위해 구현할 수 있는 다양 한 메시지 패턴 중에서 선택할 수 있습니다. 예를 들면 다음과 같습니다.

* [고정 라우팅 쪽지 패턴](/biztalk/esb-toolkit/message-routing-patterns#routing-slip)

  이 엔터프라이즈 메시지 패턴은 비즈니스 프로세스를 더 작은 단계로 분할 합니다. 각 단계에 대해 해당 단계에 대 한 작업을 처리 하는 논리 앱을 설정 합니다. 서로 통신 하기 위해 논리 앱은 큐 또는 토픽 Azure Service Bus 같은 비동기 메시징 프로토콜을 사용 합니다. 프로세스를 더 작은 단계로 나누면 실패 한 논리 앱 인스턴스에서 중단 될 수 있는 비즈니스 프로세스의 수를 줄일 수 있습니다. 이 패턴에 대 한 일반적인 정보는 [엔터프라이즈 통합 패턴-라우팅 쪽지](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html)를 참조 하세요.

  이 예에서는 각 논리 앱에서 단계를 나타내고 Service Bus 큐를 사용 하 여 프로세스의 다음 논리 앱과 통신 하는 라우팅 쪽지 패턴을 보여 줍니다.

  ![Azure Service Bus 큐를 사용 하 여 서로 통신 하는 논리 앱이 나타내는 단계로 비즈니스 프로세스 분할](./media/business-continuity-disaster-recovery-guidance/fixed-routing-slip-pattern.png)

  기본 및 보조 논리 앱 인스턴스가 해당 위치에서 동일한 라우팅 쪽지 패턴을 따르는 경우 해당 인스턴스에 대 한 [활성-활성 역할](#roles) 을 설정 하 여 [경쟁 소비자 패턴](/azure/architecture/patterns/competing-consumers) 을 구현할 수 있습니다.

* [프로세스 관리자 (broker) 패턴](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html)

* [Peek-시간 제한 패턴 없이 잠금](https://social.technet.microsoft.com/wiki/contents/articles/50022.azure-service-bus-how-to-peek-lock-a-message-from-queue-using-azure-logic-apps.aspx)

<a name="access-trigger-runs-history"></a>

### <a name="access-to-trigger-and-runs-history"></a>트리거 및 실행 기록에 대 한 액세스

논리 앱의 이전 워크플로 실행에 대 한 자세한 정보를 얻기 위해 앱의 트리거 및 실행 기록을 검토할 수 있습니다. 논리 앱의 기록 실행 기록은 논리 앱이 실행 되는 위치 또는 지역에 저장 됩니다. 즉,이 기록을 다른 위치로 마이그레이션할 수 없습니다. 주 인스턴스가 보조 인스턴스로 장애 조치 (failover) 되는 경우 각 인스턴스의 트리거를 액세스 하 고 해당 인스턴스가 실행 되는 각 위치에서 기록을 실행할 수 있습니다. 그러나 Azure Log Analytics 작업 영역에 진단 이벤트를 보내도록 논리 앱을 설정 하 여 논리 앱의 기록에 대 한 위치 관련 없는 정보를 가져올 수 있습니다. 그런 다음 여러 위치에서 실행 되는 논리 앱에서 상태 및 기록을 검토할 수 있습니다.

<a name="trigger-types-guidance"></a>

## <a name="trigger-type-guidance"></a>트리거 형식 지침

논리 앱에서 사용 하는 트리거 유형은 재해 복구 전략의 여러 위치에서 논리 앱을 설정 하는 방법에 대 한 옵션을 결정 합니다. 논리 앱에서 사용할 수 있는 트리거 형식은 다음과 같습니다.

* [되풀이 트리거](#recurrence-trigger)
* [폴링 트리거](#polling-trigger)
* [요청 트리거](#request-trigger)
* [웹후크 트리거](#webhook-trigger)

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>되풀이 트리거

**되풀이** 트리거는 특정 서비스 또는 끝점과 독립적 이며, 지정 된 일정을 기반으로 하 고 다른 조건은 사용 하지 않을 경우에만 발생 합니다. 예를 들면 다음과 같습니다.

* 10 분 간격의 고정 된 빈도 및 간격
* 매월 마지막 월요일부터 오후 5:00 시 까지의 고급 일정

논리 앱이 되풀이 트리거로 시작 하는 경우 [활성-수동 역할](#roles)을 사용 하 여 기본 및 보조 논리 앱 인스턴스를 설정 해야 합니다. 중단 또는 재해 발생 후 비즈니스 프로세스를 복원 하는 대상 기간을 나타내는 RTO ( *복구 시간 목표* )를 줄이기 위해 [활성-수동 역할](#roles) 및 [수동-활성 역할](#roles)의 조합을 사용 하 여 논리 앱 인스턴스를 설정할 수 있습니다. 이 설정에서는 여러 위치에서 일정을 분할 합니다.

예를 들어 10 분 마다 실행 해야 하는 논리 앱이 있다고 가정 합니다. 기본 위치를 사용할 수 없게 되 면 보조 위치에서 작업을 수행할 수 있도록 논리 앱 및 위치를 설정할 수 있습니다.

![되풀이 트리거를 사용 하는 "능동-수동" 및 "수동 활성" 조합](./media/business-continuity-disaster-recovery-guidance/combo-active-passive-passive-active-setup.png)

* 기본 위치에서 다음 논리 앱에 대 한 [활성-수동 역할](#roles) 을 설정 합니다.

  * *활성* 으로 설정 된 논리 앱의 경우 되풀이 트리거를 시간 맨 위에서 시작 하도록 설정 하 고 20 분 마다 반복 합니다 (예: 오전 9:00, 9:20 am 등).

  * *수동* 사용 안 함 논리 앱의 경우 되풀이 트리거를 동일한 일정으로 설정 하지만 10 분에 시작 하 고 오전 10 시, 9:30 오전 9:10 등의 시간 마다 반복 합니다.

* 보조 위치에서 다음 논리 앱에 대해 [수동 활성화](#roles) 를 설정 합니다.

  * *수동* 사용 안 함 논리 앱의 경우 되풀이 트리거를 기본 위치의 활성 논리 앱과 동일한 일정으로 설정 합니다 .이 일정은 시간 맨 위에 있으며 오전 9:00 시, 9:10 am 등의 20 분 마다 반복 됩니다.

  * *활성* 으로 설정 된 논리 앱의 경우 되풀이 트리거를 기본 위치의 수동 논리 앱과 동일한 일정으로 설정 합니다. 즉, 10 분 마다 시작 하 고 오전 9:10, 9:20 am 등과 같이 20 분 마다 반복 됩니다.

이제 기본 위치에서 중단 이벤트가 발생 하는 경우 대체 위치에서 수동 논리 앱을 활성화 합니다. 이렇게 하면 오류를 찾는 데 시간이 소요 되는 경우이 설치 프로그램은 해당 지연 시간 동안 누락 된 되풀이 횟수를 제한 합니다.

<a name="polling-trigger"></a>

### <a name="polling-trigger"></a>폴링 트리거

특정 서비스 또는 끝점에서 처리를 위한 새 데이터를 사용할 수 있는지 정기적으로 확인 하기 위해 논리 앱은 고정 된 되풀이 일정에 따라 서비스 또는 끝점을 반복적으로 호출 하는 *폴링* 트리거를 사용할 수 있습니다. 서비스 또는 끝점에서 제공 하는 데이터에는 다음 형식 중 하나를 사용할 수 있습니다.

* 정적 데이터-항상 읽을 수 있는 데이터를 설명 합니다.
* Volatile 데이터-읽은 후 더 이상 사용할 수 없는 데이터를 설명 합니다.

동일한 데이터를 반복 해 서 읽을 수 없도록 하기 위해 논리 앱은 클라이언트 쪽 이나 서버, 서비스 또는 시스템 쪽에서 상태를 유지 관리 하 여 이전에 읽은 데이터를 기억할 수 있어야 합니다.

* 클라이언트 쪽 상태에서 작동 하는 논리 앱은 상태를 유지할 수 있는 트리거를 사용 합니다.

  예를 들어 전자 메일 받은 편지함에서 새 메시지를 읽는 트리거는 트리거가 가장 최근에 읽은 메시지를 기억할 수 있어야 합니다. 이렇게 하면 트리거가 다음에 읽지 않은 메시지가 도착할 때만 논리 앱을 시작 합니다.

* 서버, 서비스 또는 시스템 측 상태에서 작동 하는 논리 앱은 서버, 서비스 또는 시스템 측에 있는 속성 값 또는 설정을 사용 합니다.

  예를 들어 데이터베이스에서 행을 읽는 쿼리 기반 트리거를 사용 하려면 행에가로 설정 된 열이 있어야 합니다 `isRead` `FALSE` . 트리거가 행을 읽을 때마다 논리 앱은에서로 열을 변경 하 여 해당 행을 업데이트 합니다 `isRead` `FALSE` `TRUE` .

  이 서버 쪽 접근 방식은 논리 앱이 메시지를 처리 하는 동안 트리거를 통해 메시지를 읽고 잠글 수 있는 큐 의미 체계를 포함 하는 Service Bus 큐 또는 항목과 유사 하 게 작동 합니다. 논리 앱의 처리가 완료 되 면 트리거는 큐 또는 토픽에서 메시지를 삭제 합니다.

재해 복구 관점에서 논리 앱의 기본 및 보조 인스턴스를 설정 하는 경우 논리 앱이 클라이언트 쪽 또는 서버 쪽에서 상태를 추적 하는지 여부에 따라 이러한 동작을 고려해 야 합니다.

* 클라이언트 쪽 상태에서 작동 하는 논리 앱의 경우 논리 앱이 동일한 메시지를 두 번 이상 읽지 않는지 확인 합니다. 특정 시간에 한 위치 에서만 활성 논리 앱 인스턴스를 사용할 수 있습니다. 기본 인스턴스가 대체 위치로 장애 조치 (failover) 될 때까지 대체 위치의 논리 앱 인스턴스가 비활성화 또는 비활성화 되어 있는지 확인 합니다.

  예를 들어 Office 365 Outlook 트리거는 클라이언트 쪽 상태를 유지 관리 하 고, 중복 된 항목을 읽지 않도록 가장 최근에 읽은 전자 메일에 대 한 타임 스탬프를 추적 합니다.

* 서버 쪽 상태에서 작동 하는 논리 앱의 경우 기본 인스턴스가 대체 위치로 장애 조치 (failover) 될 때까지 대체 인스턴스가 대기 하는 경쟁 소비자 또는 [활성-수동 역할](#roles) 의 역할을 하는 [활성-활성 역할](#roles) 을 수행 하도록 논리 앱 인스턴스를 설정할 수 있습니다.

  예를 들어, 큐 서비스는 메시지에 대 한 잠금을 유지 하 여 다른 클라이언트가 동일한 메시지를 읽지 못하도록 하기 때문에 Azure Service Bus 큐와 같은 메시지 큐에서 읽기는 서버측 상태를 사용 합니다.

  > [!NOTE]
  > 논리 앱이 특정 순서로 메시지를 읽어야 하는 경우 (예: Service Bus 큐에서) 경쟁 하는 소비자 패턴을 사용할 수 있지만 Service Bus 세션 ( [ *순차 호위 (convoy* ) 패턴](/azure/architecture/patterns/sequential-convoy)이 라고도 함)과 함께 사용할 수 있습니다. 그렇지 않으면 활성-수동 역할을 사용 하 여 논리 앱 인스턴스를 설정 해야 합니다.

<a name="request-trigger"></a>

### <a name="request-trigger"></a>요청 트리거

**요청** 트리거는 다른 앱, 서비스 및 시스템에서 논리 앱을 호출할 수 있도록 하며 일반적으로 이러한 기능을 제공 하는 데 사용 됩니다.

* 다른 사용자가 호출할 수 있는 논리 앱에 대 한 직접 REST API

  예를 들어 요청 트리거를 사용 하 여 논리 앱을 시작 하면 다른 논리 앱이 **호출 워크플로-Logic Apps** 작업을 사용 하 여 트리거를 호출할 수 있습니다.

* 논리 앱에 [대 한 웹 후크 또는 콜백](#webhook-trigger) 메커니즘

* 특정 작업을 수행 하는 PowerShell 스크립트를 사용 하는 등의 방법으로 사용자 작업 또는 루틴을 수동으로 실행 하 여 논리 앱을 호출할 수 있는 방법

재해 복구 측면에서, 논리 앱은 작업을 수행 하지 않고 다른 서비스 또는 시스템이 명시적으로 트리거를 호출할 때까지 대기 하므로 요청 트리거는 수동 수신기입니다. 수동 끝점으로 다음과 같은 방법으로 기본 및 보조 인스턴스를 설정할 수 있습니다.

* [활성-활성](#roles): 두 인스턴스가 요청 또는 호출을 적극적으로 처리 합니다. 호출자 또는 라우터는 이러한 인스턴스 간에 트래픽을 분산 하거나 분산 합니다.

* [활성-수동](#roles): 주 인스턴스만 활성 상태 이며 모든 작업을 처리 하는 반면 보조 인스턴스는 주 복제본이 중단 또는 실패할 때까지 대기 합니다. 호출자 또는 라우터는 보조 인스턴스 호출 시기를 결정 합니다.

권장 되는 아키텍처는 Azure API Management를 요청 트리거를 사용 하는 논리 앱에 대 한 프록시로 사용할 수 있습니다. API Management은 [기본 제공 지역 간 복원 력 및 여러 끝점 간에 트래픽을 라우팅하는 기능](../api-management/api-management-howto-deploy-multi-region.md)을 제공 합니다.

<a name="webhook-trigger"></a>

### <a name="webhook-trigger"></a>웹후크 트리거

웹 *후크 트리거는* 논리 앱이 서비스에 *콜백 URL* 을 전달 하 여 서비스를 구독할 수 있는 기능을 제공 합니다. 그런 다음 논리 앱은 해당 서비스 끝점에서 특정 이벤트가 발생할 때까지 수신 대기 하 고 대기할 수 있습니다. 이벤트가 발생할 때 서비스는 콜백 URL을 사용 하 여 웹 후크 트리거를 호출한 다음 논리 앱을 실행 합니다. 사용 하도록 설정 하면 웹 후크 트리거가 서비스를 구독 합니다. 사용 하지 않도록 설정 된 경우 트리거는 서비스에서 구독을 취소 합니다.

재해 복구 관점에서, 하나의 인스턴스만 구독 된 끝점에서 이벤트 또는 메시지를 수신 해야 하므로 webhook 트리거를 사용 하 여 활성-수동 역할을 재생 하는 기본 및 보조 인스턴스를 설정 합니다.

## <a name="assess-primary-instance-health"></a>주 인스턴스 상태 평가

재해 복구 전략이 작동 하려면 솔루션에 다음 작업을 수행 하는 방법이 필요 합니다.

* [주 인스턴스의 가용성 확인](#check-primary-availability)
* [주 인스턴스의 상태를 모니터링 합니다.](#monitor-primary-health)
* [보조 인스턴스 활성화](#activate-secondary)

이 섹션에서는 사용자가 디자인 하는 데 사용할 수 있는 한 가지 솔루션에 대해 설명 합니다. 이 솔루션에 대 한 개략적인 시각적 개요는 다음과 같습니다.

![기본 위치의 상태 검사 논리 앱을 모니터링 하는 감시 논리 앱 만들기](./media/business-continuity-disaster-recovery-guidance/check-location-health-watchdog.png)

<a name="check-primary-availability"></a>

### <a name="check-primary-instance-availability"></a>주 인스턴스 가용성 확인

주 인스턴스를 사용할 수 있는지, 실행 중이 고, 작동할 수 있는지 확인 하기 위해 주 인스턴스와 동일한 위치에 있는 "상태 검사" 논리 앱을 만들 수 있습니다. 그런 다음 대체 위치에서이 상태 검사 앱을 호출할 수 있습니다. 상태 검사 앱이 성공적으로 응답 하면 해당 지역의 Azure Logic Apps 서비스에 대 한 기본 인프라가 사용 가능 하 고 작동 합니다. 상태 검사 앱이 응답 하지 않으면 위치가 더 이상 정상이 아닌 것으로 간주할 수 있습니다.

이 작업의 경우 다음 작업을 수행 하는 기본 상태 검사 논리 앱을 만듭니다.

1. 요청 트리거를 사용 하 여 watchdog 앱에서 호출을 받습니다.

1. 확인 된 논리 앱이 응답 작업을 사용 하 여 계속 작동 하는지 여부를 나타내는 상태로 응답 합니다.

   > [!IMPORTANT]
   > 상태 검사 논리 앱은 앱이 비동기적이 아닌 동기적으로 응답 하도록 응답 작업을 사용 해야 합니다.

1. 필요에 따라 기본 위치가 정상 인지 확인 하기 위해이 위치에서 대상 논리 앱과 상호 작용 하는 다른 서비스의 상태를 파악할 수 있습니다. 상태 확인 논리 앱을 확장 하 여 이러한 다른 서비스에 대 한 상태를 평가 하면 됩니다.

<a name="monitor-primary-health"></a>

### <a name="create-a-watchdog-logic-app"></a>Watchdog 논리 앱 만들기

주 인스턴스의 상태를 모니터링 하 고 상태 검사 논리 앱을 호출 하려면 *대체 위치*에 "watchdog" 논리 앱을 만듭니다. 예를 들어 감시 논리 앱을 설정 하 여 상태 검사 논리를 호출할 수 없는 경우 watchdog은 작업 팀에 경고를 보내 오류를 조사 하 고 주 인스턴스가 응답 하지 않는 이유를 확인할 수 있습니다.

> [!IMPORTANT]
> Watchdog 논리 앱이 *기본 위치와 다른 위치*에 있는지 확인 합니다. 기본 위치의 Logic Apps 서비스에서 문제가 발생 하는 경우 watchdog 논리 앱이 실행 되지 않을 수 있습니다.

이 작업의 경우 보조 위치에서 다음 작업을 수행 하는 감시 논리 앱을 만듭니다.

1. 되풀이 트리거를 사용 하 여 고정 또는 예약 된 되풀이에 따라 실행 합니다.

   RTO (복구 시간 목표)에 대 한 허용 오차 수준 보다 낮은 값으로 되풀이를 설정할 수 있습니다.

1. HTTP 작업을 사용 하 여 기본 위치에서 상태 검사 논리 앱을 호출 합니다. 예를 들면 다음과 같습니다.

<a name="activate-secondary"></a>

### <a name="activate-your-secondary-instance"></a>보조 인스턴스 활성화

보조 인스턴스를 자동으로 활성화 하려면 [Azure Resource Manager 커넥터](/connectors/arm/) 와 같은 관리 API를 호출 하 여 보조 위치에서 적절 한 논리 앱을 활성화 하는 논리 앱을 만들 수 있습니다. 특정 수의 실패가 발생 한 후에는이 활성화 논리 앱을 호출 하도록 watchdog 앱을 확장할 수 있습니다.

<a name="collect-diagnostic-data"></a>

## <a name="collect-diagnostic-data"></a>진단 데이터 수집

논리 앱 실행에 대 한 로깅을 설정 하 고 결과 진단 데이터를 Azure Storage, Azure Event Hubs, Azure Log Analytics 등의 서비스에 전송 하 여 추가 처리 및 처리할 수 있습니다.

* Azure Log Analytics와 함께이 데이터를 사용 하려는 경우 논리 앱의 **진단 설정을** 설정 하 고 여러 Log Analytics 작업 영역으로 데이터를 전송 하 여 기본 및 보조 위치 모두에 대해 데이터를 사용할 수 있도록 설정할 수 있습니다. 자세한 내용은 [Azure Monitor 로그 설정 및 Azure Logic Apps에 대 한 진단 데이터 수집](../logic-apps/monitor-logic-apps-log-analytics.md)을 참조 하세요.

* Azure Storage 또는 Azure Event Hubs에 데이터를 전송 하려는 경우 지역 중복을 설정 하 여 기본 및 보조 위치 모두에 대해 데이터를 사용할 수 있도록 설정할 수 있습니다. 자세한 내용은 다음 문서를 참조하세요.<p>

  * [Azure Blob Storage 재해 복구 및 계정 장애 조치 (failover)](../storage/common/storage-disaster-recovery-guidance.md)
  * [Azure Event Hubs 지역 재해 복구](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>다음 단계

* [Azure에 대 한 복원 력 개요](/azure/architecture/framework/resiliency/overview)
* [특정 Azure 서비스에 대한 복원력 검사 목록](/azure/architecture/checklist/resiliency-per-service)
* [Azure의 복원 력을 위한 데이터 관리](/azure/architecture/framework/resiliency/data-management)
* [Azure 응용 프로그램에 대 한 백업 및 재해 복구](/azure/architecture/framework/resiliency/backup-and-recovery)
* [지역 전체의 서비스 중단으로부터 복구](/azure/architecture/resiliency/recovery-loss-azure-region)
* [Azure 서비스에 대 한 Microsoft Sla (서비스 수준 계약)](https://azure.microsoft.com/support/legal/sla/)
