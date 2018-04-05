---
title: Azure Mobile Engagement 웹 SDK API | Microsoft Docs
description: Azure Mobile Engagement용 웹 SDK의 최신 업데이트 및 절차
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 8a87d5ac-d8b7-4a0d-bdee-414dbcc561b2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 06/07/2016
ms.author: piyushjo
ms.openlocfilehash: 6d2ae75b384b60d0383c1682a00a4fc0d19d0f43
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="use-the-azure-mobile-engagement-api-in-a-web-application"></a>웹 응용 프로그램에서 Azure Mobile Engagement API 사용
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

이 문서는 [웹 응용 프로그램에서 Mobile Engagement를 통합](mobile-engagement-web-integrate-engagement.md)하는 방법을 안내하는 문서를 보완하는 추가 문서로, Azure Mobile Engagement API를 사용하여 응용 프로그램 통계를 보고하는 방법을 자세히 설명합니다.

Mobile Engagement API는 `engagement.agent` 개체를 통해 제공됩니다. 기본 Azure Mobile Engagement 웹 SDK 별칭은 `engagement`입니다. SDK 구성에서 이 별칭을 재정의할 수 있습니다.

## <a name="mobile-engagement-concepts"></a>Mobile Engagement 개념
다음 요소는 웹 플랫폼과 관련된 일반적인 [Mobile Engagement 개념](mobile-engagement-concepts.md) 을 구체화합니다.

### <a name="session-and-activity"></a>`Session` 및 `Activity`
두 활동 간에 몇 초 넘게 유휴 상태로 있는 경우 활동의 사용자 시퀀스가 두 개의 세션으로 분할됩니다. 이러한 몇 초를 세션 제한 시간이라고 합니다.

웹 응용 프로그램이 직접 사용자 활동 종료를 선언하지 않으면( `engagement.agent.endActivity` 함수 호출), Mobile Engagement 서버에서 응용 프로그램 페이지가 닫히고 3분 이내에 사용자 세션을 자동 만료시킵니다. 이를 서버 세션 시간 제한이라고 합니다.

### `Crash`
기본적으로 catch되지 않은 JavaScript 예외에 대한 자동화된 보고서는 생성되지 않습니다. 그러나 `sendCrash` 함수를 사용하면 수동으로 작동 중단을 보고할 수 있습니다(작동 중단 보고에 대한 섹션 참조).

## <a name="reporting-activities"></a>활동 보고
사용자 활동에 대한 보고에는 사용자가 새 활동을 시작한 시간 및 사용자가 현재 활동을 종료한 시간이 포함됩니다.

### <a name="user-starts-a-new-activity"></a>사용자가 새 활동을 시작함
    engagement.agent.startActivity("MyUserActivity");

사용자 활동이 변경될 때마다 `startActivity()` 을(를) 호출해야 합니다. 이 함수를 처음 호출하면 새 사용자 세션이 시작됩니다.

### <a name="user-ends-the-current-activity"></a>사용자가 현재 활동을 종료함
    engagement.agent.endActivity();

사용자가 마지막 활동을 완료하면 `endActivity()` 을(를) 한 번 이상 호출해야 합니다. 이 작업은 Mobile Engagement 웹 SDK에 사용자가 현재 유휴 상태이며, 세션 제한 시간이 만료되면 사용자 세션을 종료해야 한다는 것을 알립니다. 세션 제한 시간이 만료되기 전에 `startActivity()` 를 호출하는 경우 세션이 다시 시작됩니다.

탐색기 창이 닫혔을 때는 신뢰할 수 있는 호출 없으므로 웹 환경 내에서는 사용자가 활동을 종료한 것을 알아내기 어렵거나 불가능한 경우가 많습니다. 그래서 Mobile Engagement 서버에서 응용 프로그램 페이지가 닫히고 3분 이내에 사용자 세션을 자동 만료시키는 것입니다.

## <a name="reporting-events"></a>이벤트 보고
이벤트에 대한 보고에서는 세션 이벤트와 독립 실행형 이벤트를 다룹니다.

### <a name="session-events"></a>세션 이벤트
세션 이벤트는 일반적으로 사용자가 세션 중에 사용자가 수행하는 동작을 보고하는 데 사용됩니다.

**추가 데이터가 없는 예제:**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login');
      // [...]
    }

**추가 데이터가 있는 예제:**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login', {user: 'alice'});
      // [...]
    }

### <a name="standalone-events"></a>독립 실행형 이벤트
세션 이벤트와 달리 독립 실행형 이벤트는 세션의 컨텍스트 외부에서 발생할 수 있습니다.

이를 위해서는 ``engagement.agent.sendSessionEvent`` 대신 ``engagement.agent.sendEvent``를 사용합니다.

## <a name="reporting-errors"></a>오류 보고
오류에 대한 보고에서는 세션 오류와 독립 실행형 오류를 다룹니다.

### <a name="session-errors"></a>세션 오류
세션 오류는 일반적으로 사용자 세션 중에 사용자에게 영향을 주는 오류를 보고하는 데 사용됩니다.

**추가 데이터가 없는 예제:**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short');
      }
      // [...]
    }

**추가 데이터가 있는 예제:**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short', {length: 4});
      }
      // [...]
    }

### <a name="standalone-errors"></a>독립 실행형 오류
세션 오류와 달리 독립 실행형 오류는 세션의 컨텍스트 외부에서 발생할 수 있습니다.

이를 위해서는 `engagement.agent.sendSessionError` 대신 `engagement.agent.sendError`를 사용합니다.

## <a name="reporting-jobs"></a>작업 보고
작업에 대한 보고에서는 작업 중에 발생한 오류 및 이벤트 보고와 작동 중단 보고를 다룹니다.

**예제:**

AJAX 요청을 모니터링하려는 경우 다음을 사용합니다.

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
      // [...]
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-errors-during-a-job"></a>작업 중 오류 보고
오류는 현재 사용자 세션이 아닌 실행 중인 작업에 관련될 수 있습니다.

**예제:**

AJAX 요청이 실패하면 오류를 보고하고자 합니다.

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
        // [...]
        if (xhr.status == 0 || xhr.status >= 400) {
          engagement.agent.sendJobError('publish_xhr', 'publish', {status: xhr.status, statusText: xhr.statusText});
        }
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-events-during-a-job"></a>작업 중 이벤트 보고
`engagement.agent.sendJobEvent` 함수를 사용하면 이벤트를 현재 사용자 세션이 아니라 실행 중인 작업과 연결할 수 있습니다.

이 함수는 `engagement.agent.sendJobError`와 똑같이 동작합니다.

### <a name="reporting-crashes"></a>작동 중단 보고
`sendCrash` 함수는 수동으로 작동 중단을 보고할 때 사용합니다.

`crashid` 인수는 작동 중단 유형을 식별하는 문자열입니다.
`crash` 인수는 일반적으로 문자열로 된 작동 중단의 스택 추적입니다.

    engagement.agent.sendCrash(crashid, crash);

## <a name="extra-parameters"></a>extras 매개 변수
이벤트, 오류, 활동 또는 작업에 임의 데이터를 연결할 수 있습니다.

이 데이터는 배열 또는 기본 형식을 제외한 어떤 JSON 개체도 될 수 있습니다.

**예제:**

    var extras = {"video_id": 123, "ref_click": "http://foobar.com/blog"};
    engagement.agent.sendEvent("video_clicked", extras);

### <a name="limits"></a>제한
extras 매개 변수에 적용할 제한은 키, 값 형식 및 크기에 대한 정규식의 영역에 있습니다.

#### <a name="keys"></a>구성
개체의 각 키는 다음 정규식과 일치해야 합니다.

    ^[a-zA-Z][a-zA-Z_0-9]*

즉, 키는 하나 이상의 문자로 시작해야 하며 그 뒤에 문자, 숫자 또는 밑줄(\_)이 붙어야 합니다.

#### <a name="values"></a>값
값은 문자열, 숫자, 부울 형식으로 제한됩니다.

#### <a name="size"></a>크기
extras는 호출당 1,024자(Mobile Engagement 웹 SDK가 JSON에서 인코딩된 후)로 제한됩니다.

## <a name="reporting-application-information"></a>응용 프로그램 정보 보고
`sendAppInfo()` 함수를 사용하면 추적 정보 또는 기타 응용 프로그램 관련 정보를 수동으로 보고할 수 있습니다.

이 정보는 증분 방식으로 전송할 수 있습니다. 특정 장치에는 특정 키에 대한 최신 값만 유지됩니다.

이벤트 extras처럼 JSON 개체를 사용하여 응용 프로그램 정보를 추상화할 수 있습니다. 배열 또는 하위 개체는 플랫 문자열(JSON 직렬화 사용)로 처리됩니다.

**예제:**

사용자 성별 및 생년월일을 보내는 코드 샘플은 다음과 같습니다.

    var appInfos = {"birthdate":"1983-12-07","gender":"female"};
    engagement.agent.sendAppInfo(appInfos);

### <a name="limits"></a>제한
응용 프로그램에 적용할 제한은 키 및 크기에 대한 정규식의 영역에 있습니다.

#### <a name="keys"></a>구성
개체의 각 키는 다음 정규식과 일치해야 합니다.

    ^[a-zA-Z][a-zA-Z_0-9]*

즉, 키는 하나 이상의 문자로 시작해야 하며 그 뒤에 문자, 숫자 또는 밑줄(\_)이 붙어야 합니다.

#### <a name="size"></a>크기
응용 프로그램 정보는 호출당 1,024자(Mobile Engagement 웹 SDK가 JSON에서 인코딩된 후)로 제한됩니다.

이전 예제에서 서버로 전송된 JSON의 길이는 44자입니다.

    {"birthdate":"1983-12-07","gender":"female"}
