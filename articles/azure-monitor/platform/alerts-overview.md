---
title: Azure의 경고 및 알림 모니터링 개요
description: Azure의 경고에 대한 개요입니다. 경고, 클래식 경고 및 경고 인터페이스가 있습니다.
ms.subservice: alerts
ms.topic: conceptual
ms.date: 01/28/2018
ms.openlocfilehash: b8d24eab79bdf16e4d883549be00431bd5372af8
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95536050"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Microsoft Azure의 경고 개요 

이 문서에서는 경고에 대해 설명하고 그 이점과 사용 방법을 소개합니다.  

## <a name="what-are-alerts-in-microsoft-azure"></a>Microsoft Azure의 경고란?

경고는 Azure Monitor의 모니터링 데이터를 사용 하 여 인프라 또는 응용 프로그램에서 문제가 발견 될 때 사용자에 게 사전에 알립니다. 시스템 사용자가 문제를 알아채기 전에 경고를 통해 문제를 식별하여 해결할 수 있습니다. 

## <a name="overview"></a>개요

아래 다이어그램은 경고의 흐름을 나타냅니다. 

![경고 흐름 다이어그램](media/alerts-overview/Azure-Monitor-Alerts.svg)

경고 규칙은 경고와 경고 발생 시 수행 되는 작업으로 구분 됩니다. 경고 규칙은 경고에 대 한 대상 및 조건을 캡처합니다. 경고 규칙은 사용 또는 사용 안 함 상태입니다. 경고 규칙이 사용인 경우에만 경고가 발생합니다. 

경고 규칙의 주요 특성은 다음과 같습니다.

**대상 리소스** - 경고에 사용할 수 있는 범위 및 신호를 정의합니다. 대상은 Azure 리소스일 수 있습니다. 예제 대상:

- 가상 머신
- 저장소 계정.
- Log Analytics 작업 영역.
- Application Insights. 

특정 리소스 (예: 가상 머신)의 경우 여러 리소스를 경고 규칙의 대상으로 지정할 수 있습니다.

대상 리소스에서 나가는 **신호** 입니다. 신호는 메트릭, 활동 로그, Application Insights 및 로그 유형 중 하나일 수 있습니다.

**조건** -대상 리소스에 적용 되는 신호 및 논리의 조합입니다. 예: 

- 백분율 CPU > 70%
- 서버 응답 시간 > 4 ms 
- 로그 쿼리의 결과 수 > 100

**경고 이름** -사용자가 구성한 경고 규칙의 특정 이름입니다.

**경고 설명** -사용자가 구성한 경고 규칙에 대 한 설명입니다.

**심각도** -경고 규칙에 지정 된 조건이 충족 되 면 발생 하는 경고의 심각도입니다. 심각도 범위는 0~4입니다.

- 심각도 0 = 위험
- 심각도 1 = 오류
- 심각도 2 = 경고
- 심각도 3 = 정보
- 심각도 4 = 세부 정보 표시 

**작업** - 경고가 발생할 때 수행되는 특정 작업입니다. 자세한 내용은 [작업 그룹](./action-groups.md)을 참조 하세요.

## <a name="what-you-can-alert-on"></a>경고 가능한 내용

[데이터 원본 모니터링](./data-sources.md)에 설명 된 대로 메트릭과 로그에 대해 경고할 수 있습니다. 신호에는 다음이 포함 되지만이에 제한 되지 않습니다.

- 메트릭 값
- 로그 검색 쿼리
- 활동 로그 이벤트
- 기본 Azure 플랫폼의 상태
- 웹 사이트 가용성 테스트

## <a name="manage-alerts"></a>경고 관리

경고가 해결 과정에 있는지 지정하는 경고 상태를 설정할 수 있습니다. 경고 규칙에 지정 된 조건이 충족 되 면 경고가 만들어지거나 실행 되며 *새* 상태를 가집니다. 경고를 확인할 때 및 경고를 닫을 때 상태를 변경할 수 있습니다. 모든 상태 변경은 경고 기록에 저장됩니다.

다음은 지원되는 경고 상태입니다.

| 주 | 설명 |
|:---|:---|
| 새로 만들기 | 문제가 검색 되었으며 아직 검토 되지 않았습니다. |
| 승인됨 | 관리자가 경고를 검토하고 작업을 시작했습니다. |
| 해결됨 | 문제가 해결되었습니다. 경고가 닫힌 후 다른 상태로 변경하면 경고를 다시 열 수 있습니다. |

*경고 상태* 는 *모니터 조건* 과 다르며 독립적입니다. 경고 상태는 사용자가 설정합니다. 모니터 조건은 시스템에 의해 설정됩니다. 경고가 발생 하면 경고의 모니터 조건이 ' 실행 됨 *'* 으로 설정 되 고 경고를 발생 시킨 기본 조건이 지워집니다. 그러면 모니터 조건이 *' 해결 됨 '* 으로 설정 됩니다. 

경고 상태는 사용자가 변경할 때까지 변경되지 않습니다. [경고 및 스마트 그룹의 상태를 변경하는 방법](./alerts-managing-alert-states.md?toc=%252fazure%252fazure-monitor%252ftoc.json)을 알아보세요.

## <a name="alerts-experience"></a>경고 환경 
기본 경고 페이지에는 특정 시간 범위 내에 생성 된 경고에 대 한 요약이 제공 됩니다. 각 심각도에 대 한 총 경고 수를 표시 하 고 각 심각도의 각 상태에 대 한 총 경고 수를 식별 하는 열을 표시 합니다. 아무 심각도나 선택하면 해당 심각도를 기준으로 필터링된 [모든 경고](#all-alerts-page) 페이지가 열립니다.

대신 [REST api를 사용 하 여 구독에서 생성 된 경고 인스턴스를 프로그래밍 방식으로 열거할](#manage-your-alert-instances-programmatically)수 있습니다.

> [!NOTE]
   >  최근 30 일 동안 생성 된 경고에만 액세스할 수 있습니다.

클래식 경고를 표시 하거나 추적 하지 않습니다. 구독을 변경하거나 매개 변수를 필터링하여 페이지를 업데이트할 수 있습니다. 

![경고 페이지의 스크린샷](media/alerts-overview/alerts-page.png)

페이지 위쪽의 드롭다운 메뉴에서 값을 선택 하 여이 뷰를 필터링 할 수 있습니다.

| 열 | 설명 |
|:---|:---|
| 구독 | 경고를 확인 하려는 Azure 구독을 선택 합니다. 필요에 따라 모든 구독을 선택 하도록 선택할 수 있습니다. 선택한 구독에서 액세스 권한이 있는 경고만 보기에 포함 됩니다. |
| Resource group | 단일 리소스 그룹을 선택합니다. 선택한 리소스 그룹의 대상이 있는 경고만 보기에 포함됩니다. |
| 시간 범위 | 선택한 시간 범위 내에서 발생 한 경고만 보기에 포함 됩니다. 지원되는 값은 지난 1시간, 지난 24시간, 지난 7일 및 지난 30일입니다. |

경고 페이지 위쪽에서 다음 값을 선택 하 여 다른 페이지를 엽니다.

| 값 | 설명 |
|:---|:---|
| 총 경고 수 | 선택한 조건과 일치하는 총 경고 수입니다. 이 값을 선택하면 필터 없이 [모든 경고] 보기가 열립니다. |
| 스마트 그룹 | 선택한 조건과 일치하는 경고에서 만들어진 스마트 그룹의 총수입니다. 이 값을 선택하면 [모든 경고] 보기에 스마트 그룹 목록이 열립니다.
| 총 경고 규칙 수 | 선택한 구독 및 리소스 그룹에 있는 총 경고 규칙 수입니다. 이 값을 선택하면 선택한 구독 및 리소스 그룹을 기준으로 필터링된 규칙 보기가 열립니다.


## <a name="manage-alert-rules"></a>경고 규칙 관리
**규칙** 페이지를 표시 하려면 **경고 규칙 관리** 를 선택 합니다. 규칙 페이지는 Azure 구독에서 모든 경고 규칙을 관리 하기 위한 단일 장소입니다. 모든 경고 규칙을 나열하며 대상 리소스, 리소스 그룹, 규칙 이름 또는 상태에 따라 정렬할 수 있습니다. 또한이 페이지에서 경고 규칙을 편집, 사용 또는 사용 하지 않도록 설정할 수 있습니다.  

 ![규칙 페이지의 스크린샷](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>경고 규칙 만들기
모니터링 서비스 또는 신호 형식에 관계 없이 일관 된 방식으로 경고 규칙을 작성할 수 있습니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tflw]

 
새 경고 규칙을 만드는 방법은 다음과 같습니다.
1. 경고의 _대상_ 을 선택합니다.
1. 대상에 사용 가능한 신호 중에서 _신호_ 를 선택합니다.
1. 신호의 데이터에 적용할 _논리_ 를 지정합니다.

이 간소화된 작성 프로세스에서는 Azure 리소스를 선택하기 전에 지원되는 모니터링 원본 또는 신호를 더 이상 알 필요가 없습니다. 사용 가능한 신호 목록은 선택한 대상 리소스를 기준으로 자동 필터링됩니다. 또한 해당 대상을 기반으로 경고 규칙의 논리를 자동으로 정의 하는 과정을 안내 합니다.  

경고 규칙을 만드는 방법에 대한 자세한 내용은 [Azure Monitor를 사용하여 경고 만들기, 보기 및 관리](./alerts-metric.md)를 참조하세요.

경고는 여러 Azure 모니터링 서비스 전체에서 제공됩니다. 각 서비스를 사용하는 방법 및 시기에 대한 자세한 내용은 [Azure 애플리케이션 및 리소스 모니터링](../overview.md)을 참조하세요. 


## <a name="all-alerts-page"></a>[모든 경고] 페이지 
**모든 경고** 페이지를 표시 하려면 **총 경고** 를 선택 합니다. 여기에서 선택한 시간 내에 생성 된 경고의 목록을 볼 수 있습니다. 개별 경고 목록 또는 경고가 포함된 스마트 그룹 목록을 볼 수 있습니다. 페이지 맨 위에 있는 배너를 선택하면 보기가 전환됩니다.

![모든 경고 페이지의 스크린샷](media/alerts-overview/all-alerts-page.png)

페이지 맨 위에 있는 드롭다운 메뉴에서 다음 값을 선택 하 여 뷰를 필터링 할 수 있습니다.

| 열 | 설명 |
|:---|:---|
| 구독 | 경고를 확인 하려는 Azure 구독을 선택 합니다. 필요에 따라 모든 구독을 선택 하도록 선택할 수 있습니다. 선택한 구독에서 액세스 권한이 있는 경고만 보기에 포함 됩니다. |
| Resource group | 단일 리소스 그룹을 선택합니다. 선택한 리소스 그룹의 대상이 있는 경고만 보기에 포함됩니다. |
| 리소스 종류 | 리소스 종류를 하나 이상 선택합니다. 선택한 형식의 대상이 있는 경고만 보기에 포함됩니다. 이 열은 리소스 그룹을 지정한 후에만 사용할 수 있습니다. |
| 리소스 | 리소스를 선택합니다. 해당 리소스가 대상으로 지정된 경고만 보기에 포함됩니다. 이 열은 리소스 종류를 지정한 후에만 사용할 수 있습니다. |
| 심각도 | 경고 심각도를 선택 하거나 모두를 **선택 하 여** 모든 심각도의 경고를 포함 합니다. |
| 조건 모니터링 | 모니터 조건을 선택 하거나 **모두를 선택 하 여** 모든 조건의 경고를 포함 합니다. |
| 경고 상태 | 모든 상태에 대 한 경고를 포함 하려면 경고 상태를 선택 하거나 **모두** 를 선택 합니다. |
| 서비스 모니터링 | 서비스를 선택 하거나 **모두를 선택 하 여** 모든 서비스를 포함 합니다. 서비스를 대상으로 사용하는 규칙에서 생성된 경고만 포함됩니다. |
| 시간 범위 | 선택한 시간 범위 내에서 발생 한 경고만 보기에 포함 됩니다. 지원되는 값은 지난 1시간, 지난 24시간, 지난 7일 및 지난 30일입니다. |

페이지 맨 위에 있는 **열** 을 선택 하 여 표시할 열을 선택 합니다. 

## <a name="alert-details-page"></a>경고 정보 페이지
경고를 선택 하면이 페이지에서 경고에 대 한 세부 정보를 제공 하 고 해당 상태를 변경할 수 있습니다.

![경고 정보 페이지의 스크린샷](media/alerts-overview/alert-detail2.png)

경고 정보 페이지에는 다음과 같은 섹션이 포함 되어 있습니다.

| 섹션 | Description |
|:---|:---|
| 요약 | 경고에 대한 속성과 기타 중요한 정보를 표시합니다. |
| 기록 | 경고에서 수행한 각 작업과 경고의 변경 내용을 나열합니다. 현재는 상태 변경으로 제한되어 있습니다. |
| 진단 | 경고가 포함 된 스마트 그룹에 대 한 정보입니다. *경고 수* 는 스마트 그룹에 포함된 경고 수를 나타냅니다. 이전 30 일 동안 생성 된 것과 동일한 스마트 그룹의 다른 경고, 경고 목록 페이지의 시간 필터를 포함 합니다. 경고를 선택하면 세부 정보를 볼 수 있습니다. |

## <a name="azure-role-based-access-control-azure-rbac-for-your-alert-instances"></a>경고 인스턴스에 대 한 azure RBAC (역할 기반 액세스 제어)

경고 인스턴스의 사용 및 관리를 위해서는 사용자에게 [모니터링 기여자](../../role-based-access-control/built-in-roles.md#monitoring-contributor) 또는 [모니터링 읽기 권한자](../../role-based-access-control/built-in-roles.md#monitoring-reader)의 Azure 기본 제공 역할이 있어야 합니다. 이러한 역할은 구독 수준에서 리소스 수준의 세부적인 할당까지의 모든 Azure Resource Manager 범위에서 지원됩니다. 예를 들어 사용자가 가상 컴퓨터에 대 한 참가자 액세스를 모니터링 하는 경우 `ContosoVM1` 해당 사용자는에 생성 된 경고만 사용 하 고 관리할 수 있습니다 `ContosoVM1` .

## <a name="manage-your-alert-instances-programmatically"></a>프로그래밍 방식으로 경고 인스턴스 관리

구독에 대해 생성 된 경고를 프로그래밍 방식으로 쿼리 하는 것이 좋습니다. 쿼리는 Azure Portal 외부에서 사용자 지정 보기를 만들거나 패턴 및 추세를 식별 하기 위해 경고를 분석 하는 것일 수 있습니다.

[경고 관리 REST API](/rest/api/monitor/alertsmanagement/alerts) 사용 하거나 [Azure 리소스 그래프](../../governance/resource-graph/overview.md) 와 [리소스에 대 한 REST API](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources)사용 하 여 구독에 대해 생성 된 경고를 쿼리할 수 있습니다.

리소스에 대 한 리소스 그래프 REST API를 사용 하 여 대규모 경고 인스턴스를 쿼리할 수 있습니다. 여러 구독에서 생성 된 경고를 관리 해야 하는 경우에는 리소스 그래프가 권장 됩니다. 

리소스 그래프에 대 한 다음 샘플 요청은 한 구독 내의 경고 수를 반환 REST API 합니다.

```json
{
  "subscriptions": [
    <subscriptionId>
  ],
  "query": "AlertsManagementResources | where type =~ 'Microsoft.AlertsManagement/alerts' | summarize count()"
}
```

Azure 리소스 그래프 탐색기: [portal.azure.com](https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/AlertsManagementResources%20%7C%20where%20type%20%3D~%20%27Microsoft.AlertsManagement%2Falerts%27%20%7C%20summarize%20count()) 를 사용 하 여 포털에서이 리소스 그래프 쿼리 결과를 볼 수도 있습니다.

해당 [필수](alerts-common-schema-definitions.md#essentials) 필드에 대 한 경고를 쿼리할 수 있습니다.

[경고 관리 REST API](/rest/api/monitor/alertsmanagement/alerts) 를 사용 하 여 [경고 컨텍스트](alerts-common-schema-definitions.md#alert-context) 필드를 포함 하 여 특정 경고에 대 한 자세한 정보를 얻을 수 있습니다.

## <a name="smart-groups"></a>스마트 그룹

스마트 그룹은 기계 학습 알고리즘을 기반으로 하는 경고의 집계로, 경고 노이즈를 줄이고 문제를 해결 하는 데 도움이 될 수 있습니다. [스마트 그룹](./alerts-smartgroups-overview.md?toc=%252fazure%252fazure-monitor%252ftoc.json) 및 [스마트 그룹을 관리하는 방법](./alerts-managing-smart-groups.md?toc=%252fazure%252fazure-monitor%252ftoc.json)을 알아보세요.

## <a name="next-steps"></a>다음 단계

- [스마트 그룹에 대해 자세히 알아보기](./alerts-smartgroups-overview.md?toc=%252fazure%252fazure-monitor%252ftoc.json)
- [작업 그룹](./action-groups.md)에 대해 자세히 알아보기
- [Azure에서 경고 인스턴스 관리](./alerts-managing-alert-instances.md?toc=%252fazure%252fazure-monitor%252ftoc.json)
- [스마트 그룹 관리](./alerts-managing-smart-groups.md?toc=%252fazure%252fazure-monitor%252ftoc.json)
- [Azure alerts 가격에 대 한 자세한 정보](https://azure.microsoft.com/pricing/details/monitor/)