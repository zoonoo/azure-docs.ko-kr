---
title: Azure Monitor 로그에서 논리 앱에 대한 쿼리 보기 및 만들기
description: Azure Logic Apps에 대한 Azure Monitor 로그에서 쿼리 보기 및 만들기
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 5de795811923c965a1473f8d91e3fd654aeef266
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574543"
---
# <a name="view-and-create-queries-for-monitoring-and-tracking-in-azure-monitor-logs-for-azure-logic-apps"></a>Azure Logic Apps에 대한 Azure Monitor 로그에서 모니터링 및 추적을 위한 쿼리 보기 및 만들기

[Azure Monitor 로그](../azure-monitor/logs/log-query-overview.md)에서 결과를 생성하는 기본 쿼리를 확인하고 특정 조건에 따라 결과를 필터링하는 쿼리를 만들 수 있습니다. 예를 들어 특정 교환 컨트롤 번호에 따라 메시지를 찾을 수 있습니다. 쿼리는 [Kusto 쿼리 언어](/azure/data-explorer/kusto/query/)를 사용하여 서로 다른 결과를 보려는 경우 편집할 수 있습니다. 자세한 내용은 [Azure Monitor 로그 쿼리](/azure/data-explorer/kusto/query/)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Log Analytics 작업 영역. Log Analytics 작업 영역이 없는 경우 [Log Analytics 작업 영역을 만드는 방법](../azure-monitor/logs/quick-create-workspace.md)을 알아봅니다.

* Azure Monitor 로깅에 설정되어 해당 정보를 Log Analytics 작업 영역으로 전송하는 논리 앱. [논리 앱에 대한 Azure Monitor 로그를 설정하는 방법](../logic-apps/monitor-logic-apps.md)을 알아봅니다.

* 통합 계정을 사용하는 경우 해당 정보를 Log Analytics 작업 영역으로 보내도록 Azure Monitor 로깅을 사용하여 계정을 설정했는지 확인합니다. [통합 계정에 대한 Azure Monitor 로깅 설정](../logic-apps/monitor-b2b-messages-log-analytics.md) 방법을 알아봅니다.

## <a name="view-queries-behind-results"></a>결과 뒤에 있는 쿼리 보기

작업 영역 요약에서 결과를 생성하는 쿼리를 보거나 편집하려면 다음 단계를 수행합니다.

1. 결과 페이지 하단에서 **모두 보기** 를 선택합니다.

   ![전체 결과 보기](./media/create-monitoring-tracking-queries/logic-app-see-all.png)

   로그 페이지가 열리고 이전 결과 페이지 뒤에 쿼리를 표시합니다.

   ![로그 페이지 - 쿼리 보기](./media/create-monitoring-tracking-queries/view-query-behind-results.png)

1. **로그** 페이지에서 다음 옵션을 선택할 수 있습니다.

   * 쿼리 결과를 테이블로 보려면 쿼리 편집기에서 **테이블** 을 선택합니다.

   * 쿼리를 변경하려면 쿼리 문자열을 업데이트하고 **실행** 을 선택하여 테이블의 결과를 확인합니다.

## <a name="create-your-own-query"></a>사용자 고유의 쿼리 만들기

특정 속성이나 값에 따라 결과를 찾거나 필터링하려면 빈 쿼리에서 시작하거나 기존 쿼리를 사용하여 사용자 고유의 쿼리를 만들 수 있습니다. 자세한 내용은 [Azure Monitor에서 로그 쿼리 시작](../azure-monitor/logs/get-started-queries.md)을 참조하세요.

1. [Azure Portal](https://portal.azure.com)에서 Log Analytics 작업 영역을 찾아서 선택합니다.

1. 작업 영역 메뉴의 **일반** 에서 **로그** 를 선택합니다.

1. 빈 쿼리 또는 사용 가능한 기존 쿼리에서 시작합니다.

   * 기존 쿼리를 사용할 수 있는지 확인하려면 쿼리 도구 모음에서 이전 쿼리 실행의 쿼리를 표시하는 **샘플 쿼리** > **내역** 을 선택하거나 이전에 빌드한 쿼리를 보여주는 **쿼리 탐색기** 를 선택합니다.

     예를 들어 Logic Apps B2B 솔루션은 다음과 같은 미리 빌드된 쿼리를 제공합니다.

     !["Logic Apps B2B" 솔루션 미리 빌드된 쿼리 시작](./media/create-monitoring-tracking-queries/b2b-prebuilt-queries.png)

   * 빈 쿼리에서 시작하려면 쿼리 편집기에서 쿼리에 대한 [Kusto 쿼리 언어](/azure/data-explorer/kusto/query/) 입력을 시작합니다.

     ![빈 쿼리로 시작](./media/create-monitoring-tracking-queries/create-query-from-blank.png)

## <a name="next-steps"></a>다음 단계

* [AS2 추적 스키마](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 추적 스키마](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [사용자 지정 추적 스키마](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)