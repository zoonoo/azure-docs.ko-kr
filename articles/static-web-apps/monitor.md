---
title: Azure Static Web Apps 모니터링
description: Azure Static Web Apps에서 요청, 오류 및 추적 정보 모니터링
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 4/23/2021
ms.author: cshoe
ms.openlocfilehash: 8c97c3c008dda4269b282e89af7badda889588fe
ms.sourcegitcommit: bb9a6c6e9e07e6011bb6c386003573db5c1a4810
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110497576"
---
# <a name="monitor-azure-static-web-apps"></a>Azure Static Web Apps 모니터링

[Application Insights](../azure-monitor/app/app-insights-overview.md)를 사용하여 API 요청, 오류 및 추적 정보를 모니터링할 수 있습니다.

> [!IMPORTANT]
> Application Insights에는 Azure Static Web Apps의 [독립적인 가격 책정 모델](https://azure.microsoft.com/pricing/details/monitor)이 있습니다.

> [!NOTE]
> Azure Static Web Apps에서 Application Insights를 사용하려면 [API](./add-api.md)를 사용하는 애플리케이션이 필요합니다.

## <a name="add-monitoring"></a>모니터링 추가

다음 단계를 사용하여 정적 웹앱에 Application Insights 모니터링을 추가합니다.

1. Azure Portal에서 Static Web Apps 인스턴스를 엽니다.

1. 메뉴에서 **Application Insights** 를 선택합니다.

1. _Application Insights 사용_ 옆에서 **예** 를 선택합니다.

1. **저장** 을 선택합니다.

:::image type="content" source="media/monitoring/azure-static-web-apps-application-insights-add.png" alt-text="Azure Static Web Apps에 Application Insights 추가":::

> [!NOTE]
> Application Insights 인스턴스를 만들면 서비스를 함께 연결하는 데 사용되는 Azure Static Web Apps 인스턴스에 연결된 애플리케이션 설정이 만들어집니다.

## <a name="access-data"></a>데이터 액세스

1. 정적 웹앱의 _개요_ 창에서 _리소스 그룹_ 옆에 있는 링크를 선택합니다.

1. 목록에서 정적 웹앱과 같은 이름의 접두사가 있는 Application Insights 인스턴스를 선택합니다.

다음에는 애플리케이션 API 엔드포인트의 측면을 검사하는 데 사용되는 포털의 몇 군데 위치가 강조 표시되어 있습니다.

> [!NOTE]
> Application Insights 사용에 대한 자세한 내용은 [내 원격 분석을 어디에서 볼 수 있나요?](../azure-monitor/app/app-insights-overview.md#where-do-i-see-my-telemetry)를 참조하세요.

| 형식 | 메뉴 위치 | Description |
|--- | --- | --- |
| [실패](../azure-monitor/app/asp-net-exceptions.md) | _조사 > 오류_ | 실패한 요청을 검토합니다. |
| [서버 요청](../azure-monitor/app/tutorial-performance.md) | _조사 > 성능_ | 개별 API 요청을 검토합니다.  |
| [로그](../azure-monitor/app/diagnostic-search.md) | _모니터링 > 로그_ | 편집기와 상호 작용하여 트랜잭션 로그를 쿼리합니다. |
| [메트릭](../azure-monitor/essentials/app-insights-metrics.md) | _모니터링 > 메트릭_ | 디자이너와 상호 작용하여 다양한 메트릭을 사용하여 사용자 지정 차트를 만들 수 있습니다. |

### <a name="traces"></a>추적

다음 단계를 사용하여 애플리케이션에서 추적을 봅니다.

1. _모니터링_ 아래에서 **로그** 를 선택합니다.

1. _쿼리_ 창에서 카드 위로 마우스를 가져갑니다.

1. **로드 편집기** 단추를 선택합니다.

1. 생성된 쿼리를 `traces` 단어로 바꿉니다.

1. **실행** 단추를 선택합니다.

:::image type="content" source="media/monitoring/azure-static-web-apps-application-insights-traces.png" alt-text="Application Insights 추적 보기":::

## <a name="limit-logging"></a>로깅 제한

경우에 따라 Azure Functions 앱의 _host.json_ 파일을 다음과 같이 변경하여 오류 및 경고에 대한 세부 정보를 캡처하면서 로깅을 제한할 수 있습니다.

```json
{
    "version": "2.0",
    "logging": {
        "applicationInsights": {
            "samplingSettings": {
              "isEnabled": true
            },
            "enableDependencyTracking": false
        },
        "logLevels": {
            "default": "Warning"
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [인증 및 권한 부여 설정](authentication-authorization.md)
