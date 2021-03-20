---
title: Azure API Management에서 API analytics 사용 | Microsoft Docs
description: Azure API Management에서 분석을 사용 하 여 Api 및 API 성능의 사용을 이해 하 고 범주화 할 수 있습니다.
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 11/24/2020
ms.author: apimpm
ms.openlocfilehash: ca7bd70bbf99a6d0079717a7a02328b11528d2e0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96841514"
---
# <a name="get-api-analytics-in-azure-api-management"></a>Azure API Management에서 API 분석 가져오기

Azure API Management는 Api에 대 한 기본 제공 분석을 제공 합니다. 다음을 포함 하 여 여러 차원에서 API Management 인스턴스의 Api 사용량 및 성능을 분석 합니다.

* 시간
* Geography
* API
* API 작업
* 제품
* Subscriptions
* 사용자
* 요청

:::image type="content" source="media/howto-use-analytics/analytics-report-portal.png" alt-text="포털의 타임 라인 분석":::

Api에 대 한 개략적인 모니터링 및 문제 해결을 위해 분석을 사용 합니다. 진단 및 감사에 대 한 거의 실시간 메트릭 및 리소스 로그를 포함 한 추가 모니터링 기능은 [자습서: 게시 된 Api 모니터링](api-management-howto-use-azure-monitor.md)을 참조 하세요.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="analytics---portal"></a>분석-포털

Azure Portal를 사용 하 여 API Management 인스턴스의 한 눈에 분석 데이터를 검토할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 API Management 인스턴스로 이동합니다. 
1. 왼쪽 메뉴의 **모니터링** 에서 **분석** 을 선택 합니다.

    :::image type="content" source="media/howto-use-analytics/monitoring-menu-analytics.png" alt-text="포털에서 API Management 인스턴스에 대 한 분석 선택":::  
1. 데이터의 시간 범위를 선택 하거나 사용자 지정 시간 범위를 입력 하십시오.
1. **타임 라인**, **지리** 등의 분석 데이터에 대 한 보고서 범주를 선택 합니다.
1. 필요에 따라 하나 이상의 추가 범주를 기준으로 보고서를 필터링 합니다.

## <a name="analytics---rest-api"></a>분석-REST API

API Management REST API에서 [보고서](/rest/api/apimanagement/2019-12-01/reports) 작업을 사용 하 여 API Management 인스턴스의 분석 데이터를 검색 하 고 필터링 할 수 있습니다.

사용 가능한 작업은 API, geography, API 작업, 제품, 요청, 구독, 시간 또는 사용자 별로 보고서 레코드를 반환 합니다.

## <a name="next-steps"></a>다음 단계

* API Management의 Azure Monitor 기능에 대 한 소개는 [자습서: 게시 된 Api 모니터링](api-management-howto-use-azure-monitor.md) 을 참조 하세요.
* HTTP 로깅 및 모니터링에 대 한 자세한 내용은 [Azure API Management, Event Hubs 및 Moesif를 사용 하 여 Api 모니터링](api-management-log-to-eventhub-sample.md)을 참조 하세요.
* [Azure 애플리케이션 Insights와 Azure API Management를 통합 하는](api-management-howto-app-insights.md)방법에 대해 알아봅니다.