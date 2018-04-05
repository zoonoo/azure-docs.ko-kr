---
title: Azure Mobile Engagement 웹 SDK 개요 | Microsoft Docs
description: Azure Mobile Engagement용 웹 SDK의 최신 업데이트 및 절차
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 5bbc2fda-0f3f-43d0-a73d-0f2c0f8dc25b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 10/18/2016
ms.author: piyushjo
ms.openlocfilehash: ad0a83e05c6a60953c2f8a0036f823e7ed497f45
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement-web-sdk"></a>Azure Mobile Engagement 웹 SDK
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

이 문서에서는 웹앱에 Azure Mobile Engagement를 통합하는 방법에 대한 모든 세부 사항을 확인할 수 있습니다. 자신의 고유한 웹앱으로 시작하기 전에 연습해 보려면 [15분 자습서](mobile-engagement-web-app-get-started.md)를 참조하세요.

## <a name="integration-procedures"></a>통합 절차
1. [웹앱에서 Mobile Engagement를 통합하는 방법](mobile-engagement-web-integrate-engagement.md)에 대해 알아보세요.
2. 태그 계획 구현의 경우 [웹앱에서 고급 Mobile Engagement 태깅 API를 사용하는 방법](mobile-engagement-web-use-engagement-api.md)을 알아보세요.

## <a name="release-notes"></a>릴리스 정보
### <a name="202-10182016"></a>2.0.2 (10/18/2016)
* 개인 브라우징(Safari)의 작동 중단 문제가 해결되었습니다.
* 쿠키를 사용하지 않는 브라우저의 작동 중단 문제가 해결되었습니다.

모든 버전에 대한 내용은 [전체 릴리스 정보](mobile-engagement-web-release-notes.md)를 참조하세요.

## <a name="upgrade-procedures"></a>업그레이드 절차
### <a name="upgrade-from-121-to-200"></a>1.2.1에서 2.0.0으로 업그레이드
다음 섹션에서는 Capptain SAS가 제공하는 Capptain 서비스에서 Azure Mobile Engagement 앱으로 Mobile Engagement 웹 SDK 통합을 마이그레이션하는 방법을 설명합니다. 1.2.1 이전 버전에서 마이그레이션하는 경우 Capptain 웹 사이트를 참조하여 먼저 1.2.1으로 마이그레이션한 후 다음 절차를 적용하세요.

이 버전의 Mobile Engagement 웹 SDK는 삼성 스마트 TV, OperaTV, webOS 또는 도달률 기능을 지원하지 않습니다.

> [!IMPORTANT]
> Capptain과 Azure Mobile Engagement는 같은 서비스가 아니며, 다음 절차에서는 클라이언트 앱을 마이그레이션하는 방법만 중점적으로 설명합니다. 앱의 Mobile Engagement 웹 SDK를 마이그레이션해도 데이터가 Capptain 서버에서 Mobile Engagement 서버로 마이그레이션되지는 않습니다.
> 
> 

#### <a name="javascript-files"></a>JavaScript 파일
파일 capptain-sdk.js를 파일 azure-engagement.js로 바꾸고 그에 맞게 스크립트 가져오기를 업데이트합니다.

#### <a name="remove-capptain-reach"></a>Capptain Reach 제거
이 버전의 Mobile Engagement 웹 SDK는 Reach 기능을 지원하지 않습니다. Capptain Reach를 응용 프로그램에 통합한 경우는 제거해야 합니다.

페이지에서 Reach CSS 가져오기를 제거하고 관련 .css 파일(기본: capptain-reach.css)을 삭제합니다.

다음 Reach 리소스 삭제: 닫기 이미지(기본: capptain-close.png) 및 브랜드 아이콘(기본: capptain-notification-icon).

앱 내 알림에 대한 Reach UI를 제거합니다. 기본 레이아웃은 다음과 같습니다.

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

텍스트, 웹 알림 및 설문 조사에 대한 Reach UI를 제거합니다. 기본 레이아웃은 다음과 같습니다.

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

구성에 `reach` 개체가 있으면 제거합니다. 다음과 같이 표시됩니다.

    window.capptain = {
      [...]
      reach: {
        [...]
      }
    }

범주 등의 다른 Reach 사용자 지정을 제거합니다.

#### <a name="remove-deprecated-apis"></a>사용이 중단된 API 제거
Capptain의 일부 API는 Mobile Engagement 웹 SDK에서 사용이 중단되었습니다.

다음 API 호출을 제거합니다. `agent.connect`, `agent.disconnect`, `agent.pause` 및 `agent.sendMessageToDevice`

Capptain 구성에서 다음 콜백을 모두 제거합니다. `onConnected`, `onDisconnected`, `onDeviceMessageReceived` 및 `onPushMessageReceived`

#### <a name="configuration"></a>구성
Mobile Engagement에서는 연결 문자열을 사용하여 응용 프로그램 식별자와 같은 SDK 식별자를 구성합니다.

응용 프로그램 ID를 연결 문자열로 바꿉니다. SDK 구성에 대한 전역 개체가 `capptain`에서 `azureEngagement`로 변경됩니다.

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

#### <a name="javascript-apis"></a>JavaScript API
전역 JavaScript 개체 `window.capptain`은 이름이 `window.azureEngagement`로 변경되었지만 API 호출에 `window.engagement` 별칭을 사용할 수 있습니다. SDK 구성 정의에는 별칭을 사용할 수 없습니다.

예: `capptain.deviceId`가 `engagement.deviceId`로 변경, `capptain.agent.startActivity`가 `engagement.agent.startActivity`로 변경 등...

이전 버전의 Azure Mobile Engagement 웹 SDK를 응용 프로그램에 이미 통합한 경우 [업그레이드 절차](mobile-engagement-web-upgrade-procedure.md)에 대해 알아보세요.

