---
title: "Azure Application Insights에서 개발, 테스트 및 프로덕션 모니터링 | Microsoft Docs"
description: "개발의 여러 단계에서 응용 프로그램의 성능 및 사용 모니터링"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 578e30f0-31ed-4f39-baa8-01b4c2f310c9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 43fb1e764c929be14d42c3d214b051aeb5367d77
ms.lasthandoff: 03/15/2017


---
# <a name="separating-application-insights-resources"></a>Application Insights 리소스 구분
응용 프로그램의 다른 구성 요소 및 버전에서 원격 분석을 여러 Application Insights 리소스에 전송하거나 또는 하나로 결합해야 하나요? 이 문서에서는 모범 사례 및 필요한 기술을 살펴봅니다.

먼저, 질문을 살펴보겠습니다. 응용 프로그램에서 받은 데이터는 Microsoft Azure *리소스*의 Application Insights에 의해 저장되고 처리됩니다. 각 리소스는 해당 *계측 키* (iKey)로 식별됩니다. 응용 프로그램에서 올바른 리소스를 수집하는 데이터를 보낼 수 있도록 키 Application Insights SDK를 제공합니다. 코드 또는 ApplicationInsights.config에서 키를 제공 받을 수 있습니다. SDK에 있는 키를 변경하여 데이터를 다른 리소스로 디렉트할 수 있습니다. 

단순한 경우에 Application Insights를 이용하여 응용 프로그램을 등록하는 경우 Application Insights에서 새 리소스를 만들 수 있습니다. 이 작업은 Visual Studio에서 *Application Insights 구성* 또는 *Application Insights 추가* 대화 상자에서 수행할 수 있습니다.

고용량 웹 사이트인 경우 둘 이상의 서버 인스턴스에 배포될 수 있습니다.

더 복잡한 시나리오에는 웹 사이트 및 백 엔드 프로세서와 같은 여러 구성 요소로 이루어진 시스템이 있습니다. 

## <a name="when-to-use-separate-ikeys"></a>별도 iKey를 사용하는 경우
다음은 몇 가지 일반적인 지침입니다.

* 다른 구성 요소를 독립적으로 강화/축소할 수 있는 일련의 서버 인스턴스에서 실행되는 독립적으로 배포 가능한 응용 프로그램이 있는 경우 일반적으로 단일 리소스에 매핑합니다. 즉, 단일 계측 키(iKey)를 보유하게 됩니다.
* 반면, 별도 iKey를 사용하는 이유는 다음과 같습니다.
  * 별도 구성 요소에서 별도 메트릭을 쉽게 읽을 수 있습니다.
  * 저용량 원격 분석을 고용량과 분리되도록 유지하여 스트림에서 제한, 할당량 및 샘플링이 기타 항목에 영향을 주지 않도록 합니다.
  * 경고, 내보내기 및 작업 항목 구성 구분합니다.
  * 원격 분석 할당량, 제한 및 웹 테스트 수와 같은 [제한](app-insights-pricing.md#limits-summary)을 분산합니다.
  * 개발 및 테스트에 있는 코드는 프로덕션 스탬프와 다른 별도 iKey에 보내야 합니다.  

많은 Application Insights 포털 환경이 이 지침을 염두에 두고 설계되었습니다. 예를 들어 서버 인스턴스의 서버 보기 세그먼트의 경우 논리 구성 요소에 대한 원격 분석을 여러 서버 인스턴스에서 가져올 수 있다고 가정합니다.

## <a name="single-ikey"></a>단일 iKey
여러 구성 요소에서 단일 iKey에 원격 분석을 보내는 경우:

* 구성 요소 ID에 대한 세그먼트와 필터를 허용하는 모든 원격 분석에 속성을 추가합니다. 서버 역할 인스턴스에서 역할 ID가 원격 분석에 자동으로 추가되지만 다른 경우에 [원격 분석 이니셜라이저](app-insights-api-filtering-sampling.md#add-properties)를 사용하여 속성을 추가할 수 있습니다.
* 동시에 여러 구성 요소에서 Application Insights SDK를 업데이트합니다. 하나의 iKey에 대한 원격 분석은 동일한 버전의 SDK에서 시작해야 합니다.

## <a name="separate-ikeys"></a>별도 iKey
다른 응용 프로그램 구성 요소에 iKey가 여러 개 있는 경우:

* 논리 응용 프로그램에서 키 원격 분석의 보기에 [대시보드](app-insights-dashboards.md)를 만들어서 여러 응용 프로그램 구성 요소에서 결합합니다. 단일 논리 시스템 보기를 다른 팀에서 사용할 수 있도록 대시보드를 공유할 수 있습니다.
* 팀 수준에서 [리소스 그룹](app-insights-resources-roles-access-control.md)을 구성합니다. 액세스 권한은 리소스 그룹에서 할당되고 여기에 경고를 설정하는 권한을 포함합니다. 
* [Azure Resource Manager 템플릿 및 Powershell](app-insights-powershell.md)을 사용하여 경고 규칙 및 웹 테스트 등 아티팩트를 관리할 수 있습니다.

## <a name="separate-ikeys-for-devtest-and-production"></a>개발/테스트 및 프로덕션을 위한 별도 iKeys
앱이 릴리스될 경우 자동으로 키를 변경하기 쉽게 하려면 ApplicationInsights.config 대신 코드에서 iKey를 설정합니다.

시스템이 Azure 클라우드 서비스인 경우 [별도의 ikey를 설정하는 다른 방법](app-insights-cloudservices.md)이 있습니다.

### <a name="dynamic-ikey"></a> 동적 계측 키
ASP.NET 서비스의 global.aspx.cs 같은 초기화 메서드에서 키를 설정합니다.

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

이 예제에서는 서로 다른 리소스에 대한 ikeys는 다른 버전의 웹 구성 파일에 배치됩니다. 웹 구성 파일 교체는 릴리스 스크립트의 일부로 수행될 수 있고 대상 리소스를 교체합니다.

### <a name="web-pages"></a>웹 페이지
iKey는 [빠른 시작 블레이드에서 가져온 스크립트](app-insights-javascript.md)내의 응용 프로그램 웹페이지에서도 사용 가능합니다. 스크립트에 문자 그대로 코딩하는 대신, 서버 상태로부터 생성합니다. 예를 들어, ASP.NET 응용 프로그램에서:

*Razor에서 JavaScript*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      "@Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="creating-an-additional-application-insights-resource"></a>추가 Application Insights 리소스 만들기
동일한 구성 요소의 다른 응용 프로그램 구성 요소 또는 다른 스탬프(개발/테스트/프로덕션)에 대한 원격 분석을 분리하기로 결정한 경우 새 Application Insights 리소스를 만들어야 합니다.

[portal.azure.com](https://portal.azure.com)에서 Application Insights 리소스를 추가합니다.

![새로 만들기, Application Insights 클릭](./media/app-insights-separate-resources/01-new.png)

* **응용 프로그램 유형** 은 개요 블레이드에 표시되는 내용 및 [메트릭 탐색기](app-insights-metrics-explorer.md)에서 사용할 수 있는 속성에 영향을 줍니다. 앱 유형이 표시되지 않으면 웹 페이지에 대해 웹 유형 중 하나를 선택합니다.
* **리소스 그룹** 은 [액세스 제어](app-insights-resources-roles-access-control.md)와 같은 속성을 관리하기 위한 편의 기능입니다. 개발, 테스트 및 프로덕션 환경에 대 한 별도 리소스 그룹을 사용할 수 있습니다.
* **구독** 은 Azure의 지불 계정입니다.
* **위치** 는 데이터를 보관하는 곳입니다. 현재는 변경할 수 없습니다. 
* **대시보드에 추가** 는 Azure 홈 페이지에 리소스에 대한 빠른 액세스 타일을 넣습니다. 

리소스 생성 시 몇 초 정도 걸립니다. 완료되면 알림이 표시 됩니다.

(리소스를 자동으로 만드는 [PowerShell 스크립트](app-insights-powershell-script-create-resource.md) 를 작성할 수 있습니다).

## <a name="getting-the-instrumentation-key"></a>계측 키 가져오기
계측 키는 사용자가 만든 리소스를 식별합니다. 

![Essentials과 계측 키를 차례로 클릭하고, CTRL + C 누릅니다.](./media/app-insights-separate-resources/02-props.png)

사용자의 앱이 데이터를 보낼 모든 리소스의 계측 키가 필요합니다.


