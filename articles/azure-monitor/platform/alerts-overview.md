---
title: Azure의 경고 및 알림 모니터링 개요
description: Azure의 경고에 대한 개요입니다. 경고, 클래식 경고 및 경고 인터페이스.
ms.subservice: alerts
ms.topic: conceptual
ms.date: 01/28/2018
ms.openlocfilehash: e02c23623062f5cb3e4c597b0bb257b30aa9f44a
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769779"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Microsoft Azure의 경고 개요 

이 문서에서는 경고에 대해 설명하고 그 이점과 사용 방법을 소개합니다.  

## <a name="what-are-alerts-in-microsoft-azure"></a>Microsoft Azure의 경고란?
경고는 모니터링 데이터에서 중요한 조건이 발견되면 사전에 알려줍니다. 시스템 사용자가 문제를 알아채기 전에 경고를 통해 문제를 식별하여 해결할 수 있습니다. 

이 문서에서는 이전에 Log Analytics 및 응용 프로그램 인사이트에서 관리하던 경고를 포함하는 Azure Monitor의 통합 경고 환경을 설명합니다. [이전 경고 환경](alerts-classic.overview.md) 및 경고 유형을 *클래식 경고*라고 합니다. 경고 페이지 상단에서 클래식 경고 보기를 선택하여 이 이전 환경 및 이전 경고 유형을 볼 수 **있습니다.** 

## <a name="overview"></a>개요

아래 다이어그램은 경고의 흐름을 나타냅니다. 

![경고 흐름 다이어그램](media/alerts-overview/Azure-Monitor-Alerts.svg)

경고 규칙은 경고및 경고가 발생했을 때 수행되는 작업과 구분됩니다. 경고 규칙은 경고대상과 기준을 캡처합니다. 경고 규칙은 사용 또는 사용 안 함 상태입니다. 경고 규칙이 사용인 경우에만 경고가 발생합니다. 

다음은 경고 규칙의 주요 특성입니다.

**대상 리소스**: 경고에 사용할 수 있는 범위와 신호를 정의합니다. 대상은 Azure 리소스일 수 있습니다. 예제 대상: 가상 머신, 스토리지 계정, 가상 머신 확장 집합, Log Analytics 작업 영역 또는 Application Insights 리소스 가상 컴퓨터와 같은 특정 리소스의 경우 여러 리소스를 경고 규칙의 대상으로 지정할 수 있습니다.

**신호**: 대상 리소스에서 내보내입니다. 신호는 메트릭, 활동 로그, 응용 프로그램 인사이트 및 로그와 같은 유형중일 수 있습니다.

**기준**: 대상 리소스에 적용된 신호와 논리의 조합입니다. 예제: 

- 백분율 CPU > 70%
- 서버 응답 시간 > 4 ms 
- 로그 쿼리의 결과 수 > 100

**경고 이름**: 사용자가 구성한 경고 규칙의 특정 이름입니다.

**경고 설명**: 사용자가 구성한 경고 규칙에 대한 설명입니다.

**심각도:** 경고 규칙에 지정된 조건이 충족된 후 경고의 심각도입니다. 심각도 범위는 0~4입니다.

- Sev 0 = 임계값
- Sev 1 = 오류
- Sev 2 = 경고
- 세브 3 = 정보 정보
- 세브 4 = 자세한 내용 

**작업**: 경고가 실행될 때 수행되는 특정 작업입니다. 자세한 내용은 [작업 그룹](../../azure-monitor/platform/action-groups.md)을 참조하십시오.

## <a name="what-you-can-alert-on"></a>경고 가능한 내용

[데이터 원본 모니터링에](../../azure-monitor/platform/data-sources.md)설명된 대로 메트릭 및 로그에 대해 경고할 수 있습니다. 포함하지만 다음과 같이 제한되지 않습니다.

- 메트릭 값
- 로그 검색 쿼리
- 활동 로그 이벤트
- 기본 Azure 플랫폼의 상태
- 웹 사이트 가용성 테스트

이전에는 Azure Monitor 메트릭, Application Insights, Log Analytics 및 Service Health에 별도의 경고 기능이 있었습니다. 시간이 지나면서 Azure가 개선되고 사용자 인터페이스가 다른 여러 경고 방법과 결합되었습니다. 이 통합은 여전히 진행 중입니다. 결과적으로 일부 경고 기능은 아직도 새 경고 시스템에는 없습니다.  

| **모니터 소스** | **신호 유형**  | **설명** |
|-------------|----------------|-------------|
| 서비스 상태 | 활동 로그  | 지원되지 않습니다. [서비스 알림에 대한 활동 로그 경고 만들기](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)를 참조하세요.  |
| Application Insights | 웹 가용성 테스트 | 지원되지 않습니다. [웹 테스트 경고](../../azure-monitor/app/monitor-web-app-availability.md)를 참조하세요. Application Insights에 데이터를 보내도록 계측되는 모든 웹 사이트에서 사용할 수 있습니다. 웹 사이트의 가용성이나 응답성이 기대 이하이면 알림을 받습니다. |

## <a name="manage-alerts"></a>경고 관리
경고가 해결 과정에 있는지 지정하는 경고 상태를 설정할 수 있습니다. 경고 규칙에 지정된 조건이 충족되면 경고가 만들어지거나 발생하며 *새로*이됨 의 상태가 있습니다. 경고를 확인할 때 및 경고를 닫을 때 상태를 변경할 수 있습니다. 모든 상태 변경은 경고 기록에 저장됩니다.

다음은 지원되는 경고 상태입니다.

| 시스템 상태 | 설명 |
|:---|:---|
| 단추를 사용하여 새 | 문제가 방금 검색되었으며 아직 검토되지 않았습니다. |
| 확인됨 | 관리자가 경고를 검토하고 작업을 시작했습니다. |
| 닫힘 | 문제가 해결되었습니다. 경고가 닫힌 후 다른 상태로 변경하면 경고를 다시 열 수 있습니다. |

*경고 상태*는 *모니터 조건*과 다르며 독립적입니다. 경고 상태는 사용자가 설정합니다. 모니터 조건은 시스템에 의해 설정됩니다. 경고가 발생할 때 경고의 모니터 조건은 *실행됨*으로 설정됩니다. 경고를 발생한 기본 조건이 사라지면 모니터 조건이 *해결됨*으로 설정됩니다. 경고 상태는 사용자가 변경할 때까지 변경되지 않습니다. [경고 및 스마트 그룹의 상태를 변경하는 방법](https://aka.ms/managing-alert-smart-group-states)을 알아보세요.

## <a name="smart-groups"></a>스마트 그룹 

스마트 그룹은 기계 학습 알고리즘을 기반으로 하는 경고 집계로, 경고 노이즈를 줄이고 문제 해결에 도움을 줄 수 있습니다. [스마트 그룹](https://aka.ms/smart-groups) 및 [스마트 그룹을 관리하는 방법](https://aka.ms/managing-smart-groups)을 알아보세요.


## <a name="alerts-experience"></a>경고 환경 
기본 경고 페이지는 특정 시간 범위 내에서 생성된 경고에 대한 요약을 제공합니다. 각 심각도에 대한 각 상태의 총 경고 수를 식별하는 열과 함께 각 심각도에 대한 총 경고를 표시합니다. 아무 심각도나 선택하면 해당 심각도를 기준으로 필터링된 [모든 경고](#all-alerts-page) 페이지가 열립니다.

또는 [REST API를 사용하여 구독에서 생성된 경고 인스턴스를 프로그래밍 방식으로 열거할 수 있습니다.](#manage-your-alert-instances-programmatically)

> [!NOTE]
   >  지난 30일 동안생성된 경고에만 액세스할 수 있습니다.

클래식 경고를 표시하거나 추적하지 않습니다. 구독을 변경하거나 매개 변수를 필터링하여 페이지를 업데이트할 수 있습니다. 

![경고 페이지의 스크린샷](media/alerts-overview/alerts-page.png)

페이지 상단의 드롭다운 메뉴에서 값을 선택하여 이 보기를 필터링할 수 있습니다.

| 열 | Description |
|:---|:---|
| Subscription | 경고를 보려는 Azure 구독을 선택합니다. 선택적으로 모든 구독을 선택하도록 선택할 수 있습니다. 선택한 구독에 액세스할 수 있는 경고만 보기에 포함됩니다. |
| Resource group | 단일 리소스 그룹을 선택합니다. 선택한 리소스 그룹의 대상이 있는 경고만 보기에 포함됩니다. |
| 시간 범위 | 선택한 시간 범위 내에서 발생된 경고만 보기에 포함됩니다. 지원되는 값은 지난 1시간, 지난 24시간, 지난 7일 및 지난 30일입니다. |

경고 페이지 상단에서 다음 값을 선택하여 다른 페이지를 엽니다.

| 값 | 설명 |
|:---|:---|
| 총 경고 수 | 선택한 조건과 일치하는 총 경고 수입니다. 이 값을 선택하면 필터 없이 [모든 경고] 보기가 열립니다. |
| 스마트 그룹 | 선택한 조건과 일치하는 경고에서 만들어진 스마트 그룹의 총수입니다. 이 값을 선택하면 [모든 경고] 보기에 스마트 그룹 목록이 열립니다.
| 총 경고 규칙 수 | 선택한 구독 및 리소스 그룹에 있는 총 경고 규칙 수입니다. 이 값을 선택하면 선택한 구독 및 리소스 그룹을 기준으로 필터링된 규칙 보기가 열립니다.


## <a name="manage-alert-rules"></a>경고 규칙 관리
**규칙** 페이지를 표시하려면 **경고 규칙 관리를**선택합니다. 규칙 페이지는 Azure 구독에서 모든 경고 규칙을 관리하기 위한 단일 장소입니다. 모든 경고 규칙을 나열하며 대상 리소스, 리소스 그룹, 규칙 이름 또는 상태에 따라 정렬할 수 있습니다. 이 페이지에서 경고 규칙을 편집, 사용 또는 비활성화할 수도 있습니다.  

 ![규칙 화면 의 스크린 샷 페이지](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>경고 규칙 만들기
모니터링 서비스 또는 신호 유형에 관계없이 일관된 방식으로 경고 규칙을 작성할 수 있습니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tflw]

 
새 경고 규칙을 만드는 방법은 다음과 같습니다.
1. 경고의 _대상_을 선택합니다.
1. 대상에 사용 가능한 신호 중에서 _신호_를 선택합니다.
1. 신호의 데이터에 적용할 _논리_를 지정합니다.
 
이 간소화된 작성 프로세스에서는 Azure 리소스를 선택하기 전에 지원되는 모니터링 원본 또는 신호를 더 이상 알 필요가 없습니다. 사용 가능한 신호 목록은 선택한 대상 리소스를 기준으로 자동 필터링됩니다. 또한 해당 대상에 따라 경고 규칙의 논리를 정의하는 과정을 자동으로 안내합니다.  

경고 규칙을 만드는 방법에 대한 자세한 내용은 [Azure Monitor를 사용하여 경고 만들기, 보기 및 관리](../../azure-monitor/platform/alerts-metric.md)를 참조하세요.

경고는 여러 Azure 모니터링 서비스 전체에서 제공됩니다. 각 서비스를 사용하는 방법 및 시기에 대한 자세한 내용은 [Azure 애플리케이션 및 리소스 모니터링](../../azure-monitor/overview.md)을 참조하세요. 


## <a name="all-alerts-page"></a>[모든 경고] 페이지 
**모든 경고** 페이지를 보려면 총 **경고를 선택합니다.** 여기에서 선택한 시간 내에 생성된 경고 목록을 볼 수 있습니다. 개별 경고 목록 또는 경고가 포함된 스마트 그룹 목록을 볼 수 있습니다. 페이지 맨 위에 있는 배너를 선택하면 보기가 전환됩니다.

![모든 경고 페이지의 스크린샷](media/alerts-overview/all-alerts-page.png)

페이지 상단의 드롭다운 메뉴에서 다음 값을 선택하여 보기를 필터링할 수 있습니다.

| 열 | Description |
|:---|:---|
| Subscription | 경고를 보려는 Azure 구독을 선택합니다. 선택적으로 모든 구독을 선택하도록 선택할 수 있습니다. 선택한 구독에 액세스할 수 있는 경고만 보기에 포함됩니다. |
| Resource group | 단일 리소스 그룹을 선택합니다. 선택한 리소스 그룹의 대상이 있는 경고만 보기에 포함됩니다. |
| 리소스 유형 | 리소스 종류를 하나 이상 선택합니다. 선택한 형식의 대상이 있는 경고만 보기에 포함됩니다. 이 열은 리소스 그룹을 지정한 후에만 사용할 수 있습니다. |
| 리소스 | 리소스를 선택합니다. 해당 리소스가 대상으로 지정된 경고만 보기에 포함됩니다. 이 열은 리소스 종류를 지정한 후에만 사용할 수 있습니다. |
| 심각도 | 경고 심각도를 선택하거나 **모두를** 선택하여 모든 심각도의 경고를 포함합니다. |
| 조건 모니터링 | 모니터 조건을 선택하거나 **All을** 선택하여 모든 조건에 대한 경고를 포함합니다. |
| 경고 상태 | 경고 상태를 선택하거나 **All을** 선택하여 모든 상태의 경고를 포함합니다. |
| 서비스 모니터링 | 서비스를 선택하거나 모든 서비스를 포함하려면 **모두를** 선택합니다. 서비스를 대상으로 사용하는 규칙에서 생성된 경고만 포함됩니다. |
| 시간 범위 | 선택한 시간 범위 내에서 발생된 경고만 보기에 포함됩니다. 지원되는 값은 지난 1시간, 지난 24시간, 지난 7일 및 지난 30일입니다. |

페이지 상단의 **열을** 선택하여 표시할 열을 선택합니다. 

## <a name="alert-details-page"></a>경고 세부 정보 페이지
경고를 선택하면 이 페이지에서 경고에 대한 세부 정보를 제공하고 해당 상태를 변경할 수 있습니다.

![경고 세부 정보 페이지의 스크린샷](media/alerts-overview/alert-detail2.png)

경고 세부 정보 페이지에는 다음 섹션이 포함되어 있습니다.

| 섹션 | 설명 |
|:---|:---|
| 요약 | 경고에 대한 속성과 기타 중요한 정보를 표시합니다. |
| 기록 | 경고에서 수행한 각 작업과 경고의 변경 내용을 나열합니다. 현재는 상태 변경으로 제한되어 있습니다. |
| 진단 | 경고가 포함된 스마트 그룹에 대한 정보입니다. *경고 수*는 스마트 그룹에 포함된 경고 수를 나타냅니다. 경고 목록 페이지의 시간 필터에 관계없이 지난 30일 동안 생성된 동일한 스마트 그룹에 다른 경고를 포함합니다. 경고를 선택하면 세부 정보를 볼 수 있습니다. |

## <a name="role-based-access-control-rbac-for-your-alert-instances"></a>경고 인스턴스에 대한 역할 기반 액세스 제어(RBAC)

경고 인스턴스를 사용 및 관리하려면 사용자가 [기여자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) 또는 [모니터링 판독기의](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader)기본 제공 RBAC 역할을 갖도록 해야 합니다. 이러한 역할은 구독 수준에서 리소스 수준에서 세분화된 할당에 이르기까지 모든 Azure 리소스 관리자 범위에서 지원됩니다. 예를 들어 사용자가 가상 시스템에 `ContosoVM1`대한 모니터링 기여자 액세스 만 있는 경우 해당 `ContosoVM1`사용자는 에서 생성된 경고만 사용하고 관리할 수 있습니다.

## <a name="manage-your-alert-instances-programmatically"></a>프로그래밍 방식으로 경고 인스턴스 관리

구독에 대해 생성된 경고를 프로그래밍 방식으로 쿼리할 수 있습니다. Azure 포털 외부에서 사용자 지정 보기를 만들거나 경고를 분석하여 패턴 및 추세를 식별하는 것일 수 있습니다.

[경고 관리 REST API를](https://aka.ms/alert-management-api) 사용하거나 [Azure 리소스 그래프](../../governance/resource-graph/overview.md) 및 [리소스에 대한 REST API를](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources)사용하여 구독에 대해 생성된 경고를 쿼리할 수 있습니다.

리소스에 대한 리소스 그래프 REST API를 사용하면 대규모경고 인스턴스를 쿼리할 수 있습니다. 많은 구독에서 생성된 경고를 관리해야 하는 경우에 권장됩니다. 

리소스 그래프 REST API에 대한 다음 샘플 요청은 하나의 구독 내에서 경고 수를 반환합니다.

```json
{
  "subscriptions": [
    <subscriptionId>
  ],
  "query": "AlertsManagementResources | where type =~ 'Microsoft.AlertsManagement/alerts' | summarize count()"
}
```

또한 Azure 리소스 그래프 탐색기를 사용 하 고 포털에서이 리소스 그래프 쿼리의 결과 볼 수 있습니다.: [portal.azure.com](https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/AlertsManagementResources%20%7C%20where%20type%20%3D~%20%27Microsoft.AlertsManagement%2Falerts%27%20%7C%20summarize%20count())

[필수](alerts-common-schema-definitions.md#essentials) 필드에 대한 경고를 쿼리할 수 있습니다.

경고 [관리 REST API를](https://aka.ms/alert-management-api) 사용하여 [경고 컨텍스트](alerts-common-schema-definitions.md#alert-context) 필드를 비롯한 특정 경고에 대한 자세한 정보를 얻을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [스마트 그룹에 대해 자세히 알아보기](https://aka.ms/smart-groups)
- [작업 그룹](../../azure-monitor/platform/action-groups.md)에 대해 자세히 알아보기
- [Azure에서 경고 인스턴스 관리](https://aka.ms/managing-alert-instances)
- [스마트 그룹 관리](https://aka.ms/managing-smart-groups)
- [Azure 경고 가격 책정에 대해 자세히 알아보기](https://azure.microsoft.com/pricing/details/monitor/)






