<properties
	pageTitle="Azure Mobile Engagement 웹 SDK 통합 | Microsoft Azure"
	description="Azure Mobile Engagement용 웹 SDK의 최신 업데이트 및 절차"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="web"
	ms.devlang="js"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="piyushjo" />

#웹 응용 프로그램에서 Engagement를 통합하는 방법

> [AZURE.SELECTOR]
- [Windows 범용](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

이 절차에서는 웹 응용 프로그램에서 Engagement의 분석 및 모니터링 기능을 활성화하는 가장 간단한 방법을 설명합니다.

다음 단계를 통해 사용자, 세션, 작업, 크래시 및 기술과 관련된 모든 통계를 계산하는 데 필요한 로그의 보고서를 활성화할 수 있습니다. 이벤트, 오류, 작업 등의 기타 통계는 응용 프로그램별로 다르므로, 해당 통계를 계산하는 데 필요한 로그 보고는 Engagement API를 사용하여 수동으로 수행해야 합니다. 관련 설명은 [웹 응용 프로그램에서 고급 Mobile Engagement 태깅 API를 사용하는 방법](mobile-engagement-web-use-engagement-api.md)을 참조하세요.

## 소개

[여기](http://aka.ms/P7b453)에서 웹 SDK를 다운로드합니다. SDK는 **azure-engagement.js**라는 단일 JavaScript 파일로 출시됩니다. 이 파일은 사이트 또는 웹 응용 프로그램의 각 페이지에 포함해야 합니다.

이 스크립트는 **반드시** 응용 프로그램의 Engagement를 구성하기 위해 작성한 스크립트 또는 코드 조각 **이후**에 실행되어야 합니다.

## 브라우저 호환성

Engagement 웹 SDK는 네이티브 JSON 인코딩/디코딩과 크로스 도메인 AJAX 요청을 사용합니다(W3C CORS 사양 사용).

* Edge 12+
* IE 10+
* Firefox 3.5+
* Chrome 4+
* Safari 6+
* Opera 12+

## Engagement 구성

다음과 같은 전역 **azureEngagement** JavaScript 개체를 만드는 스크립트를 작성합니다.
 
사이트에 여러 페이지가 포함될 수 있으므로 이 예제에서는 이 스크립트가 모든 페이지에 포함된다고 가정합니다. 이 과정에서는 스크립트 이름을 `azure-engagement-conf.js`(으)로 지정합니다.

	window.azureEngagement = {
	  connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
	  appVersionName: '1.0.0',
	  appVersionCode: 1
	};

Azure 포털에 응용 프로그램용 `connectionString`이(가) 표시됩니다.

> [AZURE.NOTE] `appVersionName` 및 `appVersionCode`은(는) 선택 사항입니다. 분석에서 버전 정보를 처리하게 하려면 구성하는 것이 좋습니다.

## 페이지에 Engagement 스크립트 포함

	<head>
	  ...
	  <script type="text/javascript" src="azure-engagement-conf.js"></script>
	  <script type="text/javascript" src="azure-engagement.js"></script>
	  ...
	</head>

또는

	<body>
	  ...
	  <script type="text/javascript" src="azure-engagement-conf.js"></script>
	  <script type="text/javascript" src="azure-engagement.js"></script>
	  ...
	</body>

## Alias

SDK 스크립트가 로드되면 SDK API에 액세스할 **engagement** 별칭을 만듭니다(SDK 구성을 정의하는 데 사용할 수 없습니다). 이 별칭은 이 문서에서 참조로 사용됩니다.

참고로 기본 별칭이 페이지의 다른 전역 변수와 충돌을 일으킬 경우, 다음과 같이 SDK를 로드하기 전에 구성에서 다시 정의할 수 있습니다.

	window.azureEngagement = {
	  connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
	  appVersionName: '1.0.0',
	  appVersionCode: 1
	  alias:'anotherAlias'
	};

## 기본 보고

### 세션 추적

Engagement 세션은 이름으로 식별되는 일련의 활동으로 나뉩니다.

기존 웹 사이트는 사이트의 각 페이지에서 서로 다른 활동을 선언하는 것이 좋습니다. 현재 페이지가 바뀌지 않는 웹 사이트나 웹 응용 프로그램은 더욱 세밀하게 활동을 추적하는 것이 좋습니다.

현재 사용자 활동을 시작하든 변경하든, 이 예제에서와 같이 `engagement.agent.startActivity` 함수를 호출합니다.

	<body onload="yourOnload()">

	<!-- -->

	yourOnload = function() {
      [...]
      engagement.agent.startActivity('welcome');
	};

열린 세션은 응용 프로그램 페이지가 닫히고 3분 이내에 Engagement 서버가 자동 종료합니다.

또는, `engagement.agent.endActivity`을(를) 호출해서 세션을 수동 종료할 수도 있습니다. 이 방법은 현재 사용자 활동을 ‘유휴'로 설정하고 새로 `engagement.agent.startActivity`을(를) 호출하여 세션이 다시 시작되지 않으면 설정 후 10초 후에 세션을 자동 종료합니다.
 
10초 지연 시간은 전역 **engagement** 개체에서 구성할 수 있습니다.

	engagement.sessionTimeout = 2000; // 2 seconds
	// or
	engagement.sessionTimeout = 0; // end the session as soon as endActivity is called

> [AZURE.NOTE] 이 단계에서는 Ajax 호출이 불가능하므로 `onunload` 콜백에서 `engagement.agent.endActivity`을(를) 사용할 수 없습니다.

## 고급 보고

필요에 따라 응용 프로그램별로 `events`, `errors` 및 `jobs`을(를) 보고하고자 할 경우, `engagement.agent` 개체를 통해 Engagement API를 사용해야 합니다.

Engagement API는 Engagement의 모든 고급 기능을 사용할 수 있으며, [웹 응용 프로그램에서 고급 Mobile Engagement 태깅 API를 사용하는 방법](mobile-engagement-web-use-engagement-api.md)에 상세히 안내되어 있습니다.

## AJAX 호출에 사용되는 URL 사용자 지정

SDK가 사용한 URL을 사용자 지정할 수 있습니다. 예를 들어, 로그 URL(로깅을 위한 SDK 끝점)을 다시 정의하기 위해 다음과 같이 구성을 재정의할 수 있습니다.

	window.azureEngagement = {
	  ...
	  urls: {
	    ...        
	    getLoggerUrl: function() {
	    return 'someProxy/log';
	    }
	  }
	};

URL 함수가 `/`, `//`, `http://` 또는 `https://`(으)로 시작하는 문자열을 반환한다면 기본 구성표가 사용되지 않은 것입니다.

기본적으로 해당 URL에는 `https://` 구성표가 사용됩니다. 기본 구성표를 사용자 지정하고 싶다면 다음과 같이 구성을 재정의합니다.

	window.azureEngagement = {
	  ...
	  urls: {
        ...	     
	    scheme: '//'
	  }
	};

<!---HONumber=AcomDC_0615_2016-->