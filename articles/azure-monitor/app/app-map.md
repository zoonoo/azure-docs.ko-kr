---
title: Azure Application Insights의 애플리케이션 맵 | Microsoft Docs
description: 애플리케이션 맵을 사용하여 복잡한 애플리케이션 토폴로지 모니터링
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: a42eb7b57319df7de4c5277cdcdd93eb777f376c
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58622113"
---
# <a name="application-map-triage-distributed-applications"></a>애플리케이션 맵: 분산 애플리케이션 심사

애플리케이션 맵을 사용하면 분산된 애플리케이션의 모든 구성 요소에서 성능 병목 상태 또는 실패 핫스폿을 찾는 데 도움이 됩니다. 맵의 각 노드는 애플리케이션 구성 요소 또는 해당 종속성을 나타내며 상태 KPI 및 경고 상태를 나타냅니다. 구성 요소부터 Application Insights 이벤트와 같은 보다 자세한 진단까지 클릭하면서 살펴볼 수 있습니다. 앱에서 Azure 서비스를 사용하는 경우 SQL Database Advisor 권장 사항과 같은 Azure 진단도 살펴볼 수 있습니다.

## <a name="what-is-a-component"></a>구성 요소란?

구성 요소는 독립적으로 배포할 수 있는 분산/마이크로 서비스 애플리케이션의 부분입니다. 개발자 및 운영 팀은 이러한 애플리케이션 구성 요소에서 생성된 원격 분석에 대한 코드 수준의 가시성 또는 액세스 권한을 갖습니다. 

* 구성 요소는 팀/조직에서 액세스할 수 없는 SQL, EventHub 등과 같은 "관찰된" 외부 종속성(코드 또는 원격 분석)과 다릅니다.
* 구성 요소는 서버/역할/컨테이너 인스턴스의 수에 관계없이 실행됩니다.
* 구성 요소는 별도의 Application Insights 계측 키(구독이 다른 경우에도) 또는 단일 Application Insights 계측 키에 보고하는 다른 역할일 수 있습니다. 맵 미리 보기 환경은 구성 요소를 설정 방식에 관계없이 표시합니다.

## <a name="composite-application-map"></a>복합 애플리케이션 맵

여러 수준의 관련된 애플리케이션 구성 요소의 전체 애플리케이션 토폴로지를 볼 수 있습니다. 구성 요소는 다른 Application Insights 리소스이거나 단일 리소스 내의 다른 역할일 수 있습니다. 앱은 Application Insights SDK가 설치된 서버 간에 수행된 HTTP 종속성 호출에 따라 구성 요소를 찾습니다. 

이러한 환경은 구성 요소를 점진적으로 검색으로 시작됩니다. 애플리케이션 맵을 처음 로드할 때 이 구성 요소와 관련된 구성 요소를 검색하기 위해 쿼리 집합이 트리거됩니다. 왼쪽 위 구석에 있는 단추는 검색된 애플리케이션의 구성 요소 수로 업데이트됩니다. 

"맵 구성 요소 업데이트"를 클릭하면 맵이 해당 시점까지 검색된 모든 구성 요소로 새로 고쳐집니다. 애플리케이션의 복잡성에 따라, 로드하는 데 다소 시간이 걸릴 수 있습니다.

모든 구성 요소가 단일 Application Insights 리소스 내의 역할인 경우 이 검색 단계가 필요하지 않습니다. 이러한 애플리케이션에 대한 초기 로드 시에는 모든 구성 요소가 포함됩니다.

![애플리케이션 맵 스크린샷](media/app-map/app-map-001.png)

수백 가지 구성 요소가 있는 복잡한 토폴로지를 시각화하는 것이 이 환경의 주요 목표 중 하나입니다.

모든 구성 요소를 클릭하여 관련 정보를 확인하고, 해당 구성 요소에 대한 성능 및 실패 심사 환경으로 이동합니다.

![플라이아웃](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>오류 조사

**오류 조사**를 선택하여 오류 창을 실행합니다.

![오류 조사 단추 스크린샷](media/app-map/investigate-failures.png)

![오류 환경 스크린샷](media/app-map/failures.png)

### <a name="investigate-performance"></a>성능 조사

성능 문제를 해결하려면 **성능 조사**를 선택합니다.

![성능 조사 단추 스크린샷](media/app-map/investigate-performance.png)

![성능 환경 스크린샷](media/app-map/performance.png)

### <a name="go-to-details"></a>세부 정보로 이동

**세부 정보로 이동**을 선택하여 호출 스택 수준으로 수행된 보기를 제공할 수 있는 엔드 투 엔드 트랜잭션 환경을 탐색합니다.

![세부 정보로 이동 단추 스크린샷](media/app-map/go-to-details.png)

![종단 간 트랜잭션 세부 정보 스크린샷](media/app-map/end-to-end-transaction.png)

### <a name="view-in-analytics"></a>Analytics에서 보기

애플리케이션 데이터를 더 쿼리하고 조사하려면 **Analytics에서 보기**를 클릭합니다.

![Analytics에서 보기 단추 스크린샷](media/app-map/view-in-analytics.png)

![Analytics 환경 스크린샷](media/app-map/analytics.png)

### <a name="alerts"></a>경고

경고가 트리거될 수 있는 활성 경고 및 기본 규칙을 보려면 **경고**를 선택합니다.

![경고 단추 스크린샷](media/app-map/alerts.png)

![Analytics 환경 스크린샷](media/app-map/alerts-view.png)

## <a name="set-cloudrolename"></a>cloud_RoleName 설정

Application Map에서는 `cloud_RoleName` 속성을 사용하여 맵에 있는 구성 요소를 식별합니다. Application Insights SDK는 구성 요소가 내보낸 원격 분석에 `cloud_RoleName` 속성을 자동으로 추가합니다. 예를 들어 이 SDK는 `cloud_RoleName` 속성에 웹 사이트 이름 또는 서비스 역할 이름을 추가합니다. 그러나 기본값을 대체할 수 있는 경우가 있습니다. cloud_RoleName을 재정의하고 Application Map에 표시되는 내용을 변경하려면 다음을 수행합니다.

### <a name="net"></a>.NET

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here
                telemetry.Context.Cloud.RoleName = "RoleName";
            }
        }
    }
}
```

**이니셜라이저 로드**

ApplicationInsights.config에서:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

또는 코드에서 이니셜라이저를 인스턴스화할 수 있습니다(예: Global.aspx.cs).

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Node.js에 대한 대체 방법

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

### <a name="java"></a>자바

Application Insights Spring Boot 스타터에서 Spring Boot를 사용하는 경우 application.properties 파일에서 애플리케이션에 대한 사용자 지정 이름을 설정하도록 변경하기만 하면 됩니다.

`spring.application.name=<name-of-app>`

Spring Boot 스타터는 spring.application.name 속성에 입력한 값에 자동으로 cloudRoleName을 할당합니다.

Java 상관 관계 및 비 SpringBoot 애플리케이션에 대해 cloudRoleName을 구성하는 방법에 대한 자세한 내용은 상관 관계에 대한 이 [섹션](https://docs.microsoft.com/azure/application-insights/application-insights-correlation#role-name)을 참조하세요.

### <a name="clientbrowser-side-javascript"></a>클라이언트/브라우저 쪽 JavaScript

```javascript
appInsights.queue.push(() => {
appInsights.context.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

원격 분석 이니셜라이저를 사용하여 cloud_RoleName 속성을 재정의하는 방법에 대한 자세한 내용은 [속성: ITelemetryInitializer 추가](api-filtering-sampling.md#add-properties-itelemetryinitializer)를 참조하세요.

## <a name="troubleshooting"></a>문제 해결

Application Map이 예상대로 작동하지 않는 경우 다음 단계를 수행하세요.

1. 공식적으로 지원되는 SDK를 사용하고 있는지 확인합니다. 비지원/커뮤니티 SDK는 상관 관계를 지원하지 않을 수 있습니다.

    지원되는 SDK 목록에 대해서는 이 [문서](https://docs.microsoft.com/azure/application-insights/app-insights-platforms)를 참조하세요.

2. 모든 구성 요소를 최신 SDK 버전으로 업그레이드합니다.

3. C#으로 Azure Functions를 사용하는 경우 [Functions V2](https://docs.microsoft.com/azure/azure-functions/functions-versions)로 업그레이드합니다.

4. [cloud_RoleName](#set-cloud_rolename)이 올바르게 구성되었는지 확인합니다.

5. 종속성이 누락된 경우 [자동 수집 종속성](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies) 목록에 들어 있는지 확인합니다. 이 목록에 없어도 [종속성 호출 추적](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency)을 사용하여 수동으로 계속 추적할 수 있습니다.

## <a name="portal-feedback"></a>포털 사용자 의견

사용자 의견을 제공하려면 사용자 의견 옵션을 사용하세요.

![MapLink-1 이미지](./media/app-map/14-updated.png)

## <a name="next-steps"></a>다음 단계

* [상관 관계 이해](https://docs.microsoft.com/azure/application-insights/application-insights-correlation)