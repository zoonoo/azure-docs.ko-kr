---
title: 웹후크를 통해 Azure Service Health 알림 보내기
description: 기존 문제 관리 시스템에 서비스 상태 이벤트에 대한 개인 설정 알림을 보냅니다.
ms.topic: conceptual
ms.service: service-health
ms.date: 3/27/2018
ms.openlocfilehash: a70c4fcd01a1a95c598d980004ee60292a6cf24b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594609"
---
# <a name="use-a-webhook-to-configure-health-notifications-for-problem-management-systems"></a>웹후크를 사용하여 문제 관리 시스템에 대한 상태 알림 구성

이 문서에서는 웹후크를 통해 기존 알림 시스템에 데이터를 보내도록 Azure Service Health 경고를 구성하는 방법을 보여 줍니다.

Azure 서비스 인시던트가 영향을 미치는 경우 문자 메시지 또는 이메일을 통해 알리도록 Service Health 경고를 구성할 수 있습니다.

그러나 선호하는 기존 외부 알림 시스템이 있을 수 있습니다. 이 문서에서는 웹후크 페이로드의 가장 중요한 부분을 식별합니다. 또한 관련 서비스 문제가 발생할 때 알리도록 사용자 지정 경고를 만드는 방법을 설명합니다.

미리 구성된 통합을 사용하려면 다음을 참조하세요.
* [ServiceNow를 사용하여 경고 구성](service-health-alert-webhook-servicenow.md)
* [PagerDuty를 사용하여 경고 구성](service-health-alert-webhook-pagerduty.md)
* [OpsGenie를 사용하여 경고 구성](service-health-alert-webhook-opsgenie.md)

**소개 비디오 보기:**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUV]

## <a name="configure-a-custom-notification-by-using-the-service-health-webhook-payload"></a>Service Health 웹후크 페이로드를 사용하여 사용자 지정 알림 구성
고유한 사용자 지정 웹후크 통합을 설정하려면 Service Health 알림을 통해 전송되는 JSON 페이로드를 구문 분석해야 합니다.

`ServiceHealth` 웹후크 페이로드 [예제](../azure-monitor/alerts/activity-log-alerts-webhook.md)를 참조하세요.

`context.eventSource == "ServiceHealth"`를 보면 Service Health 경고인지 확인할 수 있습니다. 가장 관련성이 높은 속성은 다음과 같습니다.
- **data.context.activityLog.status**
- **data.context.activityLog.level**
- **data.context.activityLog.subscriptionId**
- **data.context.activityLog.properties.title**
- **data.context.activityLog.properties.impactStartTime**
- **data.context.activityLog.properties.communication**
- **data.context.activityLog.properties.impactedServices**
- **data.context.activityLog.properties.trackingId**

## <a name="create-a-link-to-the-service-health-dashboard-for-an-incident"></a>인시던트의 Service Health 대시보드에 대한 링크 만들기
특수 URL을 생성하여 데스크톱 또는 모바일 디바이스에서 Service Health 대시보드에 대한 직접 링크를 만들 수 있습니다. *trackingId* 및 *subscriptionId* 의 처음 3자리와 마지막 3자리를 다음 형식으로 사용합니다.

https <i></i>://app.azure.com/h/ *&lt;trackingId&gt;* / *&lt;subscriptionId의 처음 3자리 및 마지막 3자리&gt;*

예를 들어 *subscriptionId* 가 bba14129-e895-429b-8809-278e836ecdb3이고 *trackingId* 가 0DET-URB인 경우 Service Health URL은 다음과 같습니다.

https<i></i>://app.azure.com/h/0DET-URB/bbadb3

## <a name="use-the-level-to-detect-the-severity-of-the-issue"></a>수준을 사용하여 문제의 심각도 검색
페이로드의 **수준** 속성은 낮은 심각도에서 높은 심각도 순으로 '정보', '경고', '오류' 또는 '위험'일 수 있습니다.

## <a name="parse-the-impacted-services-to-determine-the-incident-scope"></a>영향을 받는 서비스를 구문 분석하여 인시던트 범위 결정
Service Health 경고는 여러 지역 및 서비스에서 문제를 알릴 수 있습니다. 전체 세부 정보를 얻으려면 `impactedServices` 값을 구문 분석해야 합니다.

내부 콘텐츠는 이스케이프된 [JSON](https://json.org/) 문자열이며, 이스케이프되지 않으면 정기적으로 구문 분석할 수 있는 다른 JSON 개체를 포함합니다. 예를 들면 다음과 같습니다.

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

다음과 같이 사용하십시오.

```json
[
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia East"
         },
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"Alerts & Metrics"
   },
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"App Service"
   }
]
```

이 예제에서는 다음 문제를 보여 줍니다.
- 오스트레일리아 동부 및 오스트레일리아 남동부의 '경고 및 메트릭'.
- 오스트레일리아 남동부의 'App Service'.

## <a name="test-your-webhook-integration-via-an-http-post-request"></a>HTTP POST 요청을 통해 웹후크 통합 테스트

다음 단계를 수행합니다.

1. 보낼 Service Health 페이로드를 만듭니다. [Azure 활동 로그 경고에 대한 웹후크](../azure-monitor/alerts/activity-log-alerts-webhook.md)에서 Service Health 웹후크 페이로드 예제를 참조하세요.

1. 다음과 같이 HTTP POST 요청을 만듭니다.

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
   '2XX - Successful' 응답을 받게 됩니다.

1. [PagerDuty](https://www.pagerduty.com/)로 이동하여 통합이 성공적으로 설정되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계
- [활동 로그 경고 웹후크 스키마](../azure-monitor/alerts/activity-log-alerts-webhook.md)를 검토하세요. 
- [서비스 상태 알림](./service-notifications.md)에 대해 자세히 알아보세요.
- [작업 그룹](../azure-monitor/alerts/action-groups.md)에 대해 자세히 알아보세요.
