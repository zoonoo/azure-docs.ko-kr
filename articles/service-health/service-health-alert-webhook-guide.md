---
title: Webhook를 사용 하 여 Azure Service Health 알림 보내기
description: 서비스 상태 이벤트에 대 한 개인 설정 알림을 기존 문제 관리 시스템에 보냅니다.
ms.topic: conceptual
ms.service: service-health
ms.date: 3/27/2018
ms.openlocfilehash: 95926185057d9fc1177b974fe76b2da18ebfc124
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551678"
---
# <a name="use-a-webhook-to-configure-health-notifications-for-problem-management-systems"></a>Webhook를 사용 하 여 문제 관리 시스템에 대 한 상태 알림 구성

이 문서에서는 웹 후크를 통해 기존 알림 시스템에 데이터를 보내도록 Azure Service Health 경고를 구성 하는 방법을 보여 줍니다.

Azure 서비스 인시던트가 영향을 받는 경우 텍스트 메시지나 전자 메일을 통해 알리도록 Service Health 경고를 구성할 수 있습니다.

그러나 사용 하려는 기존 외부 알림 시스템이 이미 있을 수 있습니다. 이 문서에서는 webhook 페이로드의 가장 중요 한 부분을 식별 합니다. 또한 관련 서비스 문제가 발생할 때 알리도록 사용자 지정 경고를 만드는 방법을 설명 합니다.

미리 구성 된 통합을 사용 하려면 다음을 참조 하세요.
* [ServiceNow를 사용하여 경고 구성](service-health-alert-webhook-servicenow.md)
* [PagerDuty를 사용하여 경고 구성](service-health-alert-webhook-pagerduty.md)
* [OpsGenie를 사용하여 경고 구성](service-health-alert-webhook-opsgenie.md)

**소개 비디오를 시청 하세요.**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUV]

## <a name="configure-a-custom-notification-by-using-the-service-health-webhook-payload"></a>Service Health webhook 페이로드를 사용 하 여 사용자 지정 알림 구성
사용자 고유의 사용자 지정 웹 후크 통합을 설정 하려면 Service Health 알림을 통해 전송 되는 JSON 페이로드를 구문 분석 해야 합니다.

[예제](../azure-monitor/platform/activity-log-alerts-webhook.md) `ServiceHealth` webhook 페이로드를 참조 하세요.

`context.eventSource == "ServiceHealth"`를 살펴보면 서비스 상태 경고 인지 확인할 수 있습니다. 가장 관련성이 높은 속성은 다음과 같습니다.
- **데이터. 컨텍스트-activityLog. 상태**
- **data. activityLog. 수준**
- **data. activityLog. subscriptionId**
- **data. activityLog. properties. 제목**
- **data. activityLog. 속성.**
- **data. activityLog. 속성. 통신**
- **impactedServices. 속성.**
- **trackingId. 속성.**

## <a name="create-a-link-to-the-service-health-dashboard-for-an-incident"></a>인시던트의 Service Health 대시보드에 대 한 링크 만들기
특수 URL을 생성 하 여 데스크톱 또는 모바일 장치에서 Service Health 대시보드에 대 한 직접 링크를 만들 수 있습니다. *TrackingId* 및 *subscriptionId* 의 처음 3 자리 및 마지막 3 자리를이 형식으로 사용 합니다.

https<i></i>://App.azure.com/h/ *&lt;trackingId&gt;* / *&lt;처음 3 자리부터&gt;subscriptionId의 마지막 3 자리까지*

예를 들어 *subscriptionId* 가 bba14129-e895-429b-8809-278e836ecdb3이 고 *trackingId* 가 0DET-URB 인 경우 Service Health URL은 다음과 같습니다.

https<i></i>://app.azure.com/h/0DET-URB/bbadb3

## <a name="use-the-level-to-detect-the-severity-of-the-issue"></a>수준을 사용 하 여 문제의 심각도를 검색 합니다.
가장 낮은 심각도에서 가장 높은 심각도부터 페이로드의 **수준** 속성은 *정보*, *경고*, *오류*또는 *중요*일 수 있습니다.

## <a name="parse-the-impacted-services-to-determine-the-incident-scope"></a>영향을 받는 서비스를 구문 분석 하 여 인시던트 범위 결정
Service Health 경고는 여러 지역 및 서비스에서 발생 한 문제를 알려 줍니다. 전체 세부 정보를 얻으려면 `impactedServices`의 값을 구문 분석 해야 합니다.

내부 콘텐츠는 이스케이프 되지 않은 경우 정기적으로 구문 분석할 수 있는 다른 JSON 개체를 포함 하는 이스케이프 된 [json](https://json.org/) 문자열입니다. 예:

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

다음 예에서는에 대 한 문제를 보여 줍니다.
- 오스트레일리아 동부 및 오스트레일리아 남동쪽의 "경고 & 메트릭"
- 오스트레일리아 남동쪽의 "App Service"입니다.

## <a name="test-your-webhook-integration-via-an-http-post-request"></a>HTTP POST 요청을 통해 webhook 통합 테스트

다음 단계를 수행하세요.

1. 보내려는 서비스 상태 페이로드를 만듭니다. [Azure 활동 로그 경고에 대 한 웹 후크에](../azure-monitor/platform/activity-log-alerts-webhook.md)서비스 상태 webhook 페이로드의 예를 참조 하세요.

1. 다음과 같이 HTTP POST 요청을 만듭니다.

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
   "2XX-성공" 응답을 받게 됩니다.

1. [PagerDuty](https://www.pagerduty.com/)로 이동하여 통합이 성공적으로 설정되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계
- [활동 로그 경고 웹후크 스키마](../azure-monitor/platform/activity-log-alerts-webhook.md)를 검토하세요. 
- [서비스 상태 알림](../azure-monitor/platform/service-notifications.md)에 대해 자세히 알아보세요.
- [작업 그룹](../azure-monitor/platform/action-groups.md)에 대해 자세히 알아보세요.