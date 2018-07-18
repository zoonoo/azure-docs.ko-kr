---
title: Azure Application Insights에 저장된 개인 데이터에 대한 지침 | Microsoft Docs
description: 이 문서에서는 Azure Application Insights에 저장된 개인 데이터를 관리하는 방법 및 이를 식별하고 제거하는 메서드를 설명합니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.reviewer: Evgeny.Ternovsky
ms.author: mbullwin
ms.openlocfilehash: 95e421278b46015e761764792e11dec0351b9785
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294424"
---
# <a name="guidance-for-personal-data-stored-in-application-insights"></a>Application Insights에 저장된 개인 데이터에 대한 지침

Application Insights는 개인 데이터는 검색될 가능성이 있는 데이터 저장소입니다. 이 문서에서는 Application Insights에서 이 데이터가 보통 발견되는 위치 및 이 데이터를 처리할 수 있는 기능을 설명합니다.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>개인 데이터 처리를 위한 전략

개인 데이터(가능하면)를 처리할 전략을 결정하는 것은 궁극적으로 사용자 및 사용자 회사에 달려 있는 반면 몇 가지 가능한 방법은 다음과 같습니다. 기술적 관점에서 최우선 순서대로 나열됩니다.
* 가능한 경우 컬렉션을 중지, 난독 처리, 익명화하거나, 그렇지 않은 경우 수집 중인 데이터를 _개인_으로 고려하지 않도록 조정합니다. 이 메서드는 비용이 많이 들고 강력한 데이터 처리 전략을 만들 필요를 줄여주는 선호하는 방법입니다.
* 가능하지 않은 경우 데이터 플랫폼 및 성능에 미치는 영향을 줄이려면 데이터 정규화를 시도하십시오. 예를 들어 명시적 사용자 ID를 로깅하는 대신 사용자 이름과 상관 관계가 있는 데이터에 대한 조회 및 어디서든 로깅될 수 있는 내부 ID에 대한 상세 정보를 만듭니다. 이런 방식으로 사용자 중 한 명이 자신의 개인 정보를 삭제하도록 요청하는 경우 그 사용자에게 해당하는 조회 테이블에서 행을 삭제하는 것만으로 충분할 수 있습니다. 
* 마지막으로, 개인 데이터를 수집해야 하는 경우 사용자와 연결된 모든 개인 데이터를 내보내기 및 삭제와 관련해 있을 수 있는 모든 의무를 충족하려면 제거 API 경로 및 기존 쿼리 API 경로 주변에 프로세스를 빌드합니다.

## <a name="where-to-look-for-private-data-in-application-insights"></a>Application Insights에서 개인 데이터를 찾는 위치는?

Application Insights는 완전히 유연한 저장소로서, 데이터에 스키마를 규정하는 반면 사용자 지정 값으로 모든 필드를 재정의하게 합니다. 따라서 개인 데이터를 특정 응용 프로그램에서 찾을 위치를 정확하게 지적하기는 불가능합니다. 그러나 다음 위치는 인벤토리에서 좋은 시작점입니다.

* *IP 주소*: Application Insights는 기본적으로 모든 IP 주소 필드를 "0.0.0.0"으로 난독 처리하는 반면 세션 정보를 유지하기 위해 실제 사용자로 이 값을 재정의하는 것은 상당히 일반적인 패턴입니다. 아래의 Analytics 쿼리는 지난 24 시간 동안 "0.0.0.0"이 아닌 IP 주소 열의 값을 포함한 모든 테이블을 찾는 데 사용될 수 있습니다.
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *사용자 ID*: 기본적으로 Application Insights는 사용자 및 세션 추적을 위해 임의로 생성된 ID를 사용합니다. 그러나 응용 프로그램에 더 많은 관련이 있는 ID를 저장하려면 이러한 재정의된 필드를 확인하는 것이 일반적입니다. 예: 사용자 이름, AAD GUID 등. 이러한 ID는 자주 범위 내 개인 데이터로 간주되므로 적절하게 처리되어야 합니다. 이러한 ID는 항상 난독 처리하거나 익명화하는 것이 좋습니다. 이러한 값이 보통 발견되는 필드에는 session_Id, user_Id, user_AuthenticatedId, user_AccountId 및 customDimensions이 포함됩니다.
* *사용자 지정 데이터*: Application Insights를 사용하면 모든 데이터 형식에 사용자 지정 크기 집합을 추가할 수 있습니다. 이러한 차원은 *모든* 데이터일 수 있습니다. 다음 쿼리를 사용하여 지난 24시간 동안 수집된 모든 사용자 지정 크기를 식별합니다.
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *메모리 내 및 전송 중인 데이터*: Application Insights는 예외, 요청, 종속성 호출 및 추적을 추적합니다. 개인 데이터는 자주 코드 및 HTTP 호출 수준에서 수집될 수 있습니다. 이러한 데이터를 식별하려면 예외, 요청, 종속성 및 추적 테이블을 검토합니다. 이 데이터를 난독 처리할 수 있는 경우 [원격 분석 이니셜라이저](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling)를 사용합니다.
* *스냅숏 디버거 캡처*: Application Insights의 [스냅숏 디버거](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) 기능을 사용하면 예외가 응용 프로그램의 프로덕션 인스턴스에 catch될 때마다 디버그 스냅숏을 수집할 수 있습니다. 스냅숏은 예외 및 스택의 모든 단계에서 로컬 변수에 대한 값으로 이끄는 전체 스택 추적을 공개합니다. 그러나 이 기능은 맞춤 지점의 선택적 삭제 또는 스냅숏 내에서 데이터에 프로그래밍 방식의 액세스를 허용하지 않습니다. 따라서 기본 스냅숏 보존 속도가 규정 준수 요구 사항을 충족하지 못하는 경우 기능을 해제하는 것이 좋습니다.

## <a name="how-to-export-and-delete-private-data"></a>개인 데이터 내보내기 및 삭제 방법

개인 데이터를 난독 처리하거나 익명화하거나 아니면 “개인”으로 간주되지 않도록 수정하면서 개인 데이터 수집을 사용하지 않도록 설정하려면 가능하면 데이터 수집 정책을 재구성할 것을 __강력__ 권장합니다. 데이터를 수집한 후 처리하면 전략을 자동화 및 정의하고 고객이 해당 데이터와 상호 작용을 위한 인터페이스를 빌드하는 비용 및 지속적인 유지 관리 비용이 사용자와 사용자 팀에 발생합니다. 또한 Application Insights의 경우 비용이 많이 들며, 대량의 동시 쿼리 또는 제거 API 호출은 Application Insights 기능과 모든 상호 작용에 부정적인 영향을 미칠 가능성이 있습니다. 즉, 실제로 개인 데이터를 수집해야 하는 몇 가지 유효한 시나리오가 있습니다. 이러한 경우 이 섹션에 설명된 대로 데이터를 처리해야 합니다.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>보기 및 내보내기

데이터 요청 보기 및 내보내기의 경우 [쿼리 API](https://dev.applicationinsights.io/quickstart)를 사용해야 합니다. 사용자에게 전달될 데이터 형식을 적절한 형식으로 변환하는 논리의 구현은 사용자에게 달려 있습니다. [Azure Functions](https://azure.microsoft.com/services/functions/)은 이러한 논리를 호스트하기에 좋은 곳입니다.

### <a name="delete"></a>삭제

> [!WARNING]
> Application Insights에서 삭제는 파괴적이며 되돌릴 수 없습니다! 해당 실행에 각별히 주의하십시오.

"제거" API 경로를 개인 정보 처리 스토리의 일부로 사용할 수 있게 했습니다. 이 경로는 이러한 작업과 연결된 위험, 잠재적인 성능 영향 및 Application Insights 데이터의 총 집계, 측정 및 다른 측면을 왜곡시킬 가능성으로 인해 가끔 사용돼야 합니다. 개인 데이터를 처리하는 다른 방식은 [개인 데이터 처리를 위한 전략](#strategy-for-personal-data-handling) 섹션을 참조합니다.

제거는 Azure의 사용자나 앱(리소스 소유자 포함)이 Azure Resource Manager의 역할을 명시적으로 부여하지 않고서는 실행할 권한이 없는 높은 사용 권한이 필요한 작업입니다. 이 역할은 _데이터 제거자_이며 데이터 손실 가능성 때문에 신중하게 위임돼야 합니다.

Azure Resource Manager 역할이 할당되면 두 개의 새 API 경로는 사용 가능한 전체 개발자 설명서이며 연결된 셰이프를 호출합니다.

* [게시 제거](https://docs.microsoft.com/rest/api/application-insights/components/purge) - 삭제할 데이터의 매개 변수를 지정하는 개체를 사용하고 참조 GUID를 반환합니다.
* 제거 상태 가져오기 - 게시 제거 호출은 제거 API의 상태를 확인하려면 호출할 수 있는 URL을 포함하는 'x-ms-status-location' 헤더를 반환합니다. 예: 
   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

대부분의 제거 작업은 Application Insights에서 사용하는 데이터 플랫폼에 대한 많은 영향으로 인해 SLA보다 훨씬 빨리 완료할 수 있는 반면 제거 작업 완료를 위한 공식 SLA는 30일로 설정됩니다.

## <a name="next-steps"></a>다음 단계
데이터 수집, 처리 및 보안 방법에 대해 자세히 알려면 [Application Insights 데이터 보안](app-insights-data-retention-privacy.md)을 참조합니다.