---
title: 웹후크를 통해 Azure 서비스 상태 알림 보내기
description: 기존 문제 관리 시스템에 서비스 상태 이벤트에 대한 개인 설정된 알림을 보냅니다.
ms.topic: conceptual
ms.service: service-health
ms.date: 3/27/2018
ms.openlocfilehash: 2609a267bd151354f83482ab16c4b9345aa88cc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062849"
---
# <a name="use-a-webhook-to-configure-health-notifications-for-problem-management-systems"></a>웹후크를 사용하여 문제 관리 시스템에 대한 상태 알림 구성

이 문서에서는 웹후크를 통해 기존 알림 시스템으로 데이터를 보내도록 Azure Service 상태 경고를 구성하는 방법을 보여 줍니다.

Azure 서비스 인시던트에 영향을 미치는 경우 문자 메시지 또는 전자 메일로 알림을 하도록 서비스 상태 경고를 구성할 수 있습니다.

그러나 이미 사용하려는 기존 외부 알림 시스템이 있을 수 있습니다. 이 문서에서는 웹후크 페이로드의 가장 중요한 부분을 식별합니다. 또한 관련 서비스 문제가 발생할 때 이를 알리는 사용자 지정 경고를 만드는 방법을 설명합니다.

미리 구성된 통합을 사용하려면 다음을 참조하십시오.
* [ServiceNow를 사용하여 경고 구성](service-health-alert-webhook-servicenow.md)
* [PagerDuty를 사용하여 경고 구성](service-health-alert-webhook-pagerduty.md)
* [OpsGenie를 사용하여 경고 구성](service-health-alert-webhook-opsgenie.md)

**소개 동영상 보기:**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUV]

## <a name="configure-a-custom-notification-by-using-the-service-health-webhook-payload"></a>서비스 상태 웹후크 페이로드를 사용하여 사용자 지정 알림 구성
사용자 지정 웹후크 통합을 설정하려면 서비스 상태 알림을 통해 전송되는 JSON 페이로드를 구문 분석해야 합니다.

[예제](../azure-monitor/platform/activity-log-alerts-webhook.md) `ServiceHealth` 웹후크 페이로드를 참조하십시오.

`context.eventSource == "ServiceHealth"`을 보고 서비스 상태 경고인지 확인할 수 있습니다. 다음 속성이 가장 관련성이 높습니다.
- **데이터.컨텍스트.활동로그.상태**
- **데이터.컨텍스트.활동로그.수준**
- **데이터.컨텍스트.활동Log.구독ID**
- **데이터.컨텍스트.활동Log.properties.title**
- **데이터.컨텍스트.활동로그.속성.영향시작 시간**
- **데이터.컨텍스트.활동Log.properties.communication**
- **데이터.컨텍스트.활동Log.properties.영향 서비스**
- **데이터.컨텍스트.활동로그.속성.추적ID**

## <a name="create-a-link-to-the-service-health-dashboard-for-an-incident"></a>인시던트에 대한 서비스 상태 대시보드에 대한 링크 만들기
특수 URL을 생성하여 데스크톱 또는 모바일 장치에서 서비스 상태 대시보드에 대한 직접 링크를 만들 수 있습니다. 이 형식의 *trackingId* 및 *구독Id의* 처음 세 자리와 마지막 세 자리 를 사용합니다.

<i></i>https://app.azure.com/h/*&lt;trackingId&gt;*/*&lt;처음 세 자리와 마지막&gt; 세 자리 구독Id*

예를 들어 *구독Id가* bba14129-e895-429b-8809-278e836ecdb3이고 *trackingId가* 0DET-URB인 경우 서비스 상태 URL은 다음과 같습니다.

https://app.azure.com/h/0DET-URB/bbadb3<i></i>

## <a name="use-the-level-to-detect-the-severity-of-the-issue"></a>수준을 사용하여 문제의 심각도를 검색합니다.
가장 낮은 심각도에서 가장 높은 심각도까지 페이로드의 **수준** 속성은 *정보,* *경고,* *오류*또는 *중요*할 수 있습니다.

## <a name="parse-the-impacted-services-to-determine-the-incident-scope"></a>영향을 받는 서비스를 구문 분석하여 인시던트 범위를 결정합니다.
서비스 상태 경고는 여러 지역 및 서비스의 문제에 대해 알려줄 수 있습니다. 전체 세부 정보를 얻으려면 `impactedServices`의 값을 구문 분석해야 합니다.

내부에 있는 콘텐츠는 이스케이프된 [JSON](https://json.org/) 문자열로, 이스케이프되지 않은 경우 정기적으로 구문 분석할 수 있는 다른 JSON 개체가 포함되어 있습니다. 예를 들어:

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

이 예제에서는 다음에 대한 문제를 보여 주며 다음과 같은 문제를 보여 주며 다음과 같은 문제를 보여 주
- 호주 동부 및 호주 남동부의 "& 지표 경고".
- 호주 남동부의 "앱 서비스".

## <a name="test-your-webhook-integration-via-an-http-post-request"></a>HTTP POST 요청을 통해 웹후크 통합 테스트

다음 단계를 수행하세요.

1. 보낼 서비스 상태 페이로드를 만듭니다. [Azure 활동 로그 경고에 대한 Webhooks의](../azure-monitor/platform/activity-log-alerts-webhook.md)서비스 상태 웹후크 페이로드 예제를 참조하십시오.

1. 다음과 같이 HTTP POST 요청을 만듭니다.

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
   "2XX - 성공" 응답을 받게 됩니다.

1. [PagerDuty](https://www.pagerduty.com/)로 이동하여 통합이 성공적으로 설정되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계
- 활동 [로그 경고 webhook 스키마](../azure-monitor/platform/activity-log-alerts-webhook.md)를 검토합니다. 
- 서비스 [상태 알림에](../azure-monitor/platform/service-notifications.md)대해 자세히 알아봅니다.
- [작업 그룹에](../azure-monitor/platform/action-groups.md)대해 자세히 알아보세요.