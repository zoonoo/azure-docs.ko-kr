---
title: Azure Monitor 로그에서 논리 앱에 대한 쿼리 보기 및 생성
description: Azure 논리 앱에 대한 Azure 모니터 로그에서 쿼리 보기 및 생성
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 46989ed2468469443d4e91a1834bc20b12c25a1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908073"
---
# <a name="view-and-create-queries-for-monitoring-and-tracking-in-azure-monitor-logs-for-azure-logic-apps"></a>Azure 논리 앱에 대한 Azure Monitor 로그에서 모니터링 및 추적을 위한 쿼리 보기 및 생성

[Azure Monitor 로그에서](../log-analytics/log-analytics-overview.md) 결과를 생성하는 기본 쿼리를 보고 특정 기준을 기반으로 결과를 필터링하는 쿼리를 만들 수 있습니다. 예를 들어 특정 교환 컨트롤 번호에 따라 메시지를 찾을 수 있습니다. 쿼리는 [Kusto 쿼리 언어를](https://aka.ms/LogAnalyticsLanguageReference)사용하며, 다른 결과를 보려면 편집할 수 있습니다. 자세한 내용은 [Azure Monitor 로그 쿼리를](../azure-monitor/log-query/query-language.md)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

* Log Analytics 작업 영역. Log Analytics 작업 영역이 없는 경우 [Log Analytics 작업 영역을 만드는 방법](../azure-monitor/learn/quick-create-workspace.md)을 알아봅니다.

* Azure Monitor 로깅으로 설정되어 해당 정보를 로그 분석 작업 영역으로 전송하는 논리 앱입니다. [논리 앱에 대한 Azure 모니터 로그를 설정하는 방법에](../logic-apps/monitor-logic-apps.md)대해 알아봅니다.

* 통합 계정을 사용하는 경우 해당 정보를 Log Analytics 작업 영역으로 전송하도록 Azure Monitor 로깅을 사용하여 계정을 설정했는지 확인합니다. [통합 계정에 대한 Azure 모니터 로깅을 설정하는](../logic-apps/monitor-b2b-messages-log-analytics.md)방법에 대해 알아봅니다.

## <a name="view-queries-behind-results"></a>결과 뒤에 있는 쿼리 보기

작업 영역 요약에서 결과를 생성하는 쿼리를 보거나 편집하려면 다음 단계를 따르십시오.

1. 모든 결과 페이지에서 하단에서 **모두 보기를**선택합니다.

   ![모든 결과 보기](./media/create-monitoring-tracking-queries/logic-app-see-all.png)

   로그 페이지가 열리고 이전 결과 페이지 뒤에 있는 쿼리를 표시합니다.

   ![로그 페이지 - 쿼리 보기](./media/create-monitoring-tracking-queries/view-query-behind-results.png)

1. **로그** 페이지에서 다음 옵션을 선택할 수 있습니다.

   * 쿼리 결과를 테이블로 보려면 쿼리 편집기에서 **표를**선택합니다.

   * 쿼리를 변경하려면 쿼리 문자열을 업데이트하고 **실행을** 선택하여 테이블에서 결과를 봅니다.

## <a name="create-your-own-query"></a>나만의 쿼리 만들기

특정 속성 이나 값에 따라 결과 찾거나 필터링 하려면 빈 쿼리에서 시작 하거나 기존 쿼리를 사용 하 여 사용자 고유의 쿼리를 만들 수 있습니다. 자세한 내용은 [Azure Monitor의 로그 쿼리 시작 을](../azure-monitor/log-query/get-started-queries.md)참조하십시오.

1. Azure [포털에서](https://portal.azure.com)로그 분석 작업 영역을 찾아 선택합니다.

1. 작업 영역 메뉴에서 **일반**에서 **로그를 선택합니다.**

1. 빈 쿼리 또는 사용 가능한 기존 쿼리에서 시작합니다.

   * 기존 쿼리를 사용할 수 있는지 확인하려면 쿼리 도구 모음에서 이전 쿼리 실행의 쿼리를 표시하는 **샘플 쿼리** > **기록**중 하나를 선택하거나 미리 빌드된 쿼리를 표시하는 **쿼리 탐색기를**선택합니다.

     예를 들어 논리 앱 B2B 솔루션은 미리 빌드된 다음과 같은 쿼리를 제공합니다.

     !["논리 앱 B2B" 솔루션 미리 빌드된 쿼리로 시작](./media/create-monitoring-tracking-queries/b2b-prebuilt-queries.png)

   * 빈 쿼리에서 시작하려면 쿼리 편집기에서 쿼리에 대한 [Kusto 쿼리 언어를](../azure-monitor/log-query/query-language.md) 입력하십시오.

     ![빈 쿼리로 시작](./media/create-monitoring-tracking-queries/create-query-from-blank.png)

## <a name="next-steps"></a>다음 단계

* [AS2 추적 스키마](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 추적 스키마](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [사용자 지정 추적 스키마](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)