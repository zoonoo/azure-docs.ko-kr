---
title: Azure Mobile Engagement 웹 SDK 통합 | Microsoft Docs
description: Azure Mobile Engagement 웹 SDK의 최신 업데이트 및 절차
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: b5daa2a2-942b-489d-aa1d-568c3b25e56f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 02/29/2016
ms.author: piyushjo
ms.openlocfilehash: bccfbdfe9d99900f58d5dbfa44183146c79b9c88
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="integrate-azure-mobile-engagement-in-a-web-application"></a>웹 응용 프로그램에 Azure Mobile Engagement 통합
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

> [!div class="op_single_selector"]
> * [Windows 범용](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

이 문서의 절차에서는 웹 응용 프로그램에서 Azure Mobile Engagement의 분석 및 모니터링 기능을 활성화하는 가장 간단한 방법을 설명합니다.

다음 단계를 수행하여 사용자, 세션, 활동, 작동 중단 및 기술과 관련된 모든 통계를 계산하는 데 필요한 로그 보고를 활성화합니다. 이벤트, 오류, 작업 등의 응용 프로그램 종속 통계의 경우 Azure Mobile Engagement API를 사용하여 로그 보고를 수동으로 활성화해야 합니다. 자세한 내용은 [웹 응용 프로그램에서 고급 Mobile Engagement 태깅 API를 사용하는 방법](mobile-engagement-web-use-engagement-api.md)을 참조하세요.

## <a name="introduction"></a>소개
[Azure Mobile Engagement 웹 SDK 다운로드](http://aka.ms/P7b453)Mobile Engagement 웹 SDK는 단일 JavaScript 파일인 azure-engagement.js로 제공됩니다.
이 파일은 사이트 또는 웹 응용 프로그램의 각 페이지에 포함해야 합니다.

> [!IMPORTANT]
> 이 스크립트를 실행하려면 먼저 응용 프로그램의 Mobile Engagement를 구성하기 위해 작성한 스크립트 또는 코드 조각을 실행해야 합니다.
> 
> 

## <a name="browser-compatibility"></a>브라우저 호환성
Mobile Engagement 웹 SDK는 크로스 도메인 AJAX 요청 외에 네이티브 JSON 인코딩 및 디코딩을 사용합니다(W3C CORS 사양 사용). 다음 브라우저와 호환됩니다.

* Microsoft Edge 12+
* Internet Explorer 10+
* Firefox 3.5+
* Chrome 4+
* Safari 6+
* Opera 12+

## <a name="configure-mobile-engagement"></a>Mobile Engagement 구성
다음 예제와 같은 전역 `azureEngagement` JavaScript 개체를 만드는 스크립트를 작성합니다. 사이트에 여러 개의 페이지가 있을 수 있으므로 이 예제에서는 이 스크립트가 모든 페이지에 포함되어 있다고 가정합니다. 이 예제에서 JavaScript 개체 이름은 `azure-engagement-conf.js`로 지정됩니다.

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
    };

응용 프로그램에 대한 `connectionString` 값이 Azure 포털에 표시됩니다.

> [!NOTE]
> `appVersionName` 및 `appVersionCode`는 선택 사항입니다. 그러나 분석할 때 프로세스 버전 정보가 처리될 수 있도록 구성하는 것이 좋습니다.
> 
> 

## <a name="include-mobile-engagement-scripts-in-your-pages"></a>페이지에 Mobile Engagement 스크립트 포함
다음 방법 중 하나를 사용하여 페이지에 Mobile Engagement 스크립트를 추가합니다.

    <head>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </head>

또는 다음을 사용하세요.

    <body>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </body>

## <a name="alias"></a>Alias
Mobile Engagement 웹 SDK 스크립트는 로드되면 SDK API에 액세스하기 위한 **engagement** 별칭을 만듭니다. SDK 구성을 정의하는 데는 이 별칭을 사용할 수 없습니다. 이 별칭은 이 설명서에서 참조로 사용됩니다.

기본 별칭이 페이지의 다른 전역 변수와 충돌할 경우 다음과 같이 Mobile Engagement 웹 SDK를 로드하기 전에 구성에서 재정의할 수 있습니다.

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
      alias:'anotherAlias'
    };

## <a name="basic-reporting"></a>기본 보고
Mobile Engagement의 기본 보고 기능에는 사용자, 세션, 활동 및 작동 중단에 대한 통계와 같은 세션 수준 통계가 포함됩니다.

### <a name="session-tracking"></a>세션 추적
Mobile Engagement 세션은 일련의 활동으로 나뉘며 각각이 이름으로 식별됩니다.

기존 웹 사이트는 사이트의 각 페이지에서 서로 다른 활동을 선언하는 것이 좋습니다. 현재 페이지가 절대 변경되지 않는 웹 사이트 또는 웹 응용 프로그램에서는 페이지 내부와 같이 좀 더 작은 규모로 활동을 추적하는 것이 좋습니다.

현재 사용자 활동을 시작하든 변경하든 `engagement.agent.startActivity` 함수를 호출합니다. 예: 

    <body onload="yourOnload()">

    <!-- -->

    yourOnload = function() {
      [...]
      engagement.agent.startActivity('welcome');
    };

Mobile Engagement 서버는 응용 프로그램 페이지가 닫히고 3분 이내에 열린 세션을 자동으로 종료합니다.

또는 `engagement.agent.endActivity`를 호출하여 세션을 수동으로 종료할 수 있습니다. 이렇게 하면 현재 사용자 활동이 'Idle'로 설정됩니다.  `engagement.agent.startActivity` 의 새 호출이 세션을 다시 시작하지 않는 한, 세션은 10초 후에 종료됩니다.

다음과 같이 전역 Engagement 개체에서 10초의 지연을 구성할 수 있습니다.

    engagement.sessionTimeout = 2000; // 2 seconds
    // or
    engagement.sessionTimeout = 0; // end the session as soon as endActivity is called

> [!NOTE]
> 이 단계에서는 AJAX를 호출할 수 없으므로 `onunload` 콜백에서 `engagement.agent.endActivity`를 사용할 수 없습니다.
> 
> 

## <a name="advanced-reporting"></a>고급 보고
필요에 따라 응용 프로그램 관련 이벤트, 오류 및 작업을 보고하려는 경우 Mobile Engagement API를 사용해야 합니다. `engagement.agent` 개체를 통해 Mobile Engagement API에 액세스합니다.

Mobile Engagement API의 Mobile Engagement에서 모든 고급 기능에 액세스할 수 있습니다. 이 API는 [웹 응용 프로그램에서 고급 Mobile Engagement 태깅 API를 사용하는 방법](mobile-engagement-web-use-engagement-api.md)문서에 자세히 설명되어 있습니다.

## <a name="customize-the-urls-used-for-ajax-calls"></a>AJAX 호출에 사용되는 URL 사용자 지정
Mobile Engagement 웹 SDK에서 사용되는 URL을 사용자 지정할 수 있습니다. 예를 들어 로그 URL(로깅을 위한 SDK 끝점)을 다시 정의하기 위해 다음과 같이 구성을 재정의할 수 있습니다.

    window.azureEngagement = {
      ...
      urls: {
        ...        
        getLoggerUrl: function() {
        return 'someProxy/log';
        }
      }
    };

URL 함수가 `/`, `//`, `http://` 또는 `https://`로 시작하는 문자열을 반환한다면 기본 구성표가 사용되지 않은 것입니다. 기본적으로 해당 URL에는 `https://` 구성표가 사용됩니다. 기본 구성표를 사용자 지정하고 싶다면 다음과 같이 구성을 재정의합니다.

    window.azureEngagement = {
      ...
      urls: {
        ...         
        scheme: '//'
      }
    };
