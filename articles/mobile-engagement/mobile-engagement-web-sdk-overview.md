<properties
	pageTitle="Azure Mobile Engagement 웹 SDK 개요 | Microsoft Azure"
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
	ms.date="06/07/2016"
	ms.author="piyushjo" />


# Azure Mobile Engagement용 웹 SDK

이 문서에서는 웹앱에 Azure Mobile Engagement를 통합하는 방법에 대한 모든 세부 사항을 확인할 수 있습니다. 먼저 통합을 연습해 보려면 [15분 자습서](mobile-engagement-web-app-get-started.md)의 단계를 진행하세요

## 통합 절차
1. 시작: [웹앱에서 Mobile Engagement를 통합하는 방법](mobile-engagement-web-integrate-engagement.md)

2. 태그 계획 구현: [웹앱에서 고급 Mobile Engagement API 태깅을 사용하는 방법](mobile-engagement-web-use-engagement-api.md)

## 릴리스 정보

### 2\.0.1 (06/10/2016)

-   IE8 및 IE9에서는 SDK를 사용할 수 없습니다.
-   Opera 웹 브라우저 감지 문제가 수정되었습니다.

모든 버전에 대한 내용은 [전체 릴리스 정보](mobile-engagement-web-release-notes.md)를 참조하세요.

## 업그레이드 절차

### 1\.2.1에서 2.0.0으로

아래에서는 SDK 통합을 Capptain SAS 제공 Capptain 서비스에서 Azure Mobile Engagement 구동 앱으로 마이그레이션하는 방법을 설명합니다. 이전 버전에서 마이그레이션하는 경우 Capptain 웹 사이트를 참조하여 먼저 1.2.1으로 마이그레이션한 후 다음 절차를 적용하세요.

이 버전의 Engagement 웹 SDK는 삼성 TV, OperaTV, webOS 및 도달률 기능을 지원하지 않습니다.

>[AZURE.IMPORTANT] Capptain과 Mobile Engagement는 같은 서비스가 아니며, 아래에서 제공하는 절차에서는 클라이언트 앱을 마이그레이션하는 방법만 중점적으로 설명합니다. 앱에서 SDK를 마이그레이션해도 데이터가 Capptain 서버에서 Mobile Engagement 서버로 마이그레이션되지는 않습니다.

#### JavaScript 파일

`capptain-sdk.js` 파일을 `azure-engagement.js` 파일로 교체하고 그에 따라 스크립트 가져오기를 업데이트합니다.

#### Capptain Reach 제거

이 Engagement 웹 SDK 버전은 도달률 기능을 지원하지 않습니다. 응용 프로그램에 Capptain Reach를 통합했다면 제거해야 합니다.

페이지에서 Reach css 가져오기를 제거하고 관련 css 파일(기본: capptain-reach.css)을 삭제합니다.

Reach 리소스 삭제: 닫기 이미지(기본: capptain-close.png) 및 브랜드 아이콘(기본: capptain-notification-icon).

앱 내 알림용 Reach UI를 제거합니다. 기본 레이아웃은 다음과 같습니다.

	<!-- capptain notification -->
	<div id="capptain_notification_area" class="capptain_category_default">
	  <div class="icon">
	    <img src="capptain-notification-icon.png" alt="icon" />
	  </div>
	  <div class="content">
	    <div class="title" id="capptain_notification_title"></div>
	    <div class="message" id="capptain_notification_message"></div>
	  </div>
	  <div id="capptain_notification_image"></div>
	  <div>
	    <button id="capptain_notification_close">Close</button>
	  </div>
	</div>

text\\web 알림 및 설문 조사용 Reach UI를 제거합니다. 기본 레이아웃은 다음과 같습니다.

	<div id="capptain_overlay" class="capptain_category_default">
	  <button id="capptain_overlay_close">x</button>
	  <div id="capptain_overlay_title"></div>
	  <div id="capptain_overlay_body"></div>
	  <div id="capptain_overlay_poll"></div>
	  <div id="capptain_overlay_buttons">
	    <button id="capptain_overlay_exit"></button>
	    <button id="capptain_overlay_action"></button>
	  </div>
	</div>

구성에 `reach` 개체가 있다면 제거합니다. 이 개체는 다음과 같이 표시됩니다.

	window.capptain = {
	  [...]
	  reach: {
	    [...]
	  }
	}

범주 등의 다른 Reach 사용자 지정을 제거합니다.

#### 사용이 중단된 API 제거

Capptain의 일부 API는 SDK의 Engagement 버전에서 사용이 중단되었습니다.

다음 API 호출을 제거합니다. `agent.connect`, `agent.disconnect`, `agent.pause`, `agent.sendMessageToDevice`.

Capptain 구성에서 다음 콜백이 있다면 제거합니다. `onConnected`, `onDisconnected`, `onDeviceMessageReceived`, `onPushMessageReceived`.

#### 구성

이제 Engagement에서 연결 문자열을 사용하여 응용 프로그램 식별자와 같은 SDK 식별자를 구성합니다.

응용 프로그램 ID를 연결 문자열로 교체합니다. SDK 구성의 전역 개체도 `capptain`에서 `azureEngagement`(으)로 이동합니다.

마이그레이션 전:

	window.capptain = {
	  appId: ...,
	  [...]
	};

마이그레이션 후:

	window.azureEngagement = {
	  connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
	  [...]
	};

응용 프로그램의 연결 문자열은 Azure 포털에 표시됩니다.

#### JavaScript API

전역 JavaScript 개체 `window.capptain`은(는) 이름이 `window.azureEngagement`(으)로 변경되었지만 API 호출에 `window.engagement` 별칭을 사용할 수 있습니다(SDK 구성 정의에는 별칭을 사용할 수 없습니다).

예: `capptain.deviceId`이(가) `engagement.deviceId`(으)로 변경, `capptain.agent.startActivity`이(가) `engagement.agent.startActivity`(으)로 변경 등...

이전 버전의 SDK를 응용 프로그램에 이미 통합한 경우에는 [업그레이드 절차](mobile-engagement-web-upgrade-procedure.md)를 참조하세요.

<!---HONumber=AcomDC_0615_2016-->