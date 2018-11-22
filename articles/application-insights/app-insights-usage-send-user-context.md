---
title: Azure Application Insights에서 사용 현황 환경을 활성화하도록 사용자 컨텍스트 ID 보내기 | Microsoft Docs
description: Application Insights에서 각각에 고유하고 영구적인 ID 문자열을 할당하여 사용자가 서비스를 통해 이동하는 방식을 추적합니다.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/02/2017
ms.reviewer: abgreg;mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 5a224d7a78e35776b36b162228f6ba4c72677069
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636206"
---
# <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Azure Application Insights에서 사용 환경을 활성화하도록 사용자 컨텍스트 ID 보내기

## <a name="tracking-users"></a>사용자 추적

Application Insights를 사용하면 제품 사용 현황 도구 집합을 통해 사용자를 모니터링하고 추적할 수 있습니다.

- [사용자, 세션, 이벤트](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
- [깔때기](https://docs.microsoft.com/azure/application-insights/usage-funnels)
- [보존](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention) 코호트
- [통합 문서](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)

시간이 지남에 따른 사용자 동작을 추적하기 위해 Application Insights에는 각 사용자 또는 세션에 대한 ID가 필요합니다. 모든 사용자 지정 이벤트 또는 페이지 보기에 다음 ID를 포함합니다.

- 사용자, 깔때기, 재방문 주기 및 코호트: 사용자 ID를 포함합니다.
- 세션: 세션 ID를 포함합니다.

앱이 [JavaScript SDK](https://docs.microsoft.com/azure/application-insights/app-insights-javascript#set-up-application-insights-for-your-web-page)와 통합된 경우 사용자 ID는 자동으로 추적됩니다.

## <a name="choosing-user-ids"></a>사용자 ID 선택

사용자 ID는 시간에 따라 사용자가 동작하는 방법을 추적하기 위해 사용자 세션에서 유지되어야 합니다. ID를 유지하기 위한 다양한 방법이 있습니다.

- 서비스에 이미 있는 사용자의 정의.
- 서비스에 브라우저에 대한 액세스가 있는 경우 ID로 브라우저에 쿠키를 전달할 수 있습니다. ID는 쿠키가 사용자의 브라우저에 유지되는 동안 유지됩니다.
- 필요한 경우 각 세션에 새 ID를 사용할 수 있지만 사용자에 대한 결과가 제한됩니다. 예를 들어 시간이 지남에 따라 사용자의 동작이 변경하는 방식을 확인할 수 없습니다.

ID는 각 사용자를 고유하게 식별하는 데 충분히 복잡한 Guid 또는 다른 문자열이어야 합니다. 예를 들어 긴 임의의 수일 수 있습니다.

ID가 사용자에 대한 개인 식별 정보를 포함하는 경우 사용자 ID로 Application Insights에 보낼 적절한 값이 아닙니다. [인증된 사용자 ID](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#authenticated-users)로 이러한 ID로 보낼 수 있지만 사용 현황 시나리오에 대한 사용자 ID 요구 사항을 만족하지 않습니다.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>ASP.NET 앱: ITelemetryInitializer에 사용자 컨텍스트 설정

[여기](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#add-properties-itelemetryinitializer)에 설명된 대로 원격 분석 이니셜라이저를 만듭니다. 요청 원격 분석을 통해 세션 ID를 전달하고 Context.User.Id 및 Context.Session.Id를 설정합니다.

이 예제에서는 세션 후에 만료되는 식별자에 대한 사용자 ID를 설정합니다. 가능하면 세션 간에 유지되는 사용자 ID를 사용합니다.

### <a name="telemetry-initializer"></a>원격 분석 이니셜라이저

```csharp
using System;
using System.Web;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that sets the user ID.
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var ctx = HttpContext.Current;

        // If telemetry initializer is called as part of request execution and not from some async thread
        if (ctx != null)
        {
            var requestTelemetry = ctx.GetRequestTelemetry();
 
            // Set the user and session ids from requestTelemetry.Context.User.Id, which is populated in Application_PostAcquireRequestState in Global.asax.cs.
            if (requestTelemetry != null && !string.IsNullOrEmpty(requestTelemetry.Context.User.Id) &&
                (string.IsNullOrEmpty(telemetry.Context.User.Id) || string.IsNullOrEmpty(telemetry.Context.Session.Id)))
            {
                // Set the user id on the Application Insights telemetry item.
                telemetry.Context.User.Id = requestTelemetry.Context.User.Id;
 
                // Set the session id on the Application Insights telemetry item.
                telemetry.Context.Session.Id = requestTelemetry.Context.User.Id;
            }
        }
    }
  }
}
```

### <a name="globalasaxcs"></a>Global.asax.cs

```csharp
using System.Web;
using System.Web.Http;
using System.Web.Mvc;
using System.Web.Optimization;
using System.Web.Routing;

namespace MvcWebRole.Telemetry
{
    public class MvcApplication : HttpApplication
    {
        protected void Application_Start()
        {
            AreaRegistration.RegisterAllAreas();
            GlobalConfiguration.Configure(WebApiConfig.Register);
            FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
            RouteConfig.RegisterRoutes(RouteTable.Routes);
            BundleConfig.RegisterBundles(BundleTable.Bundles);
        }
 
        protected void Application_PostAcquireRequestState()
        {
            var requestTelemetry = Context.GetRequestTelemetry();
 
            if (HttpContext.Current.Session != null && requestTelemetry != null && string.IsNullOrEmpty(requestTelemetry.Context.User.Id))
            {
                requestTelemetry.Context.User.Id = Session.SessionID;
            }
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

- 사용 현황 환경을 활성화하려면 [사용자 지정 이벤트](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) 또는 [페이지 보기](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views) 보내기를 시작합니다.
- 사용자 지정 이벤트 또는 페이지 보기를 이미 보낸 경우 사용자가 서비스를 사용하는 방법에 대해 알아보려면 사용 현황 도구를 살펴봅니다.
    - [사용 현황 개요](app-insights-usage-overview.md)
    - [사용자, 세션 및 이벤트](app-insights-usage-segmentation.md)
    - [깔때기](usage-funnels.md)
    - [보존](app-insights-usage-retention.md)
    - [통합 문서](app-insights-usage-workbooks.md)
