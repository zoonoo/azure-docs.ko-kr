---
title: Internet Explorer에서 발생 하는 문제 (MSAL.js) | Microsoft
titleSuffix: Microsoft identity platform
description: Internet Explorer 브라우저에서 JavaScript 용 Microsoft 인증 라이브러리 (MSAL.js)를 사용 합니다.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: c4f3c4153e1404a5576427be7ef218f5a669387e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "76695860"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Internet Explorer 및 Microsoft Edge 브라우저의 알려진 문제(MSAL.js)

Javascript 용 Microsoft Authentication Library (MSAL.js)가 Internet Explorer에서 실행 될 수 있도록 [JAVASCRIPT ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) 생성 됩니다. 그러나 알아야 할 몇 가지 사항이 있습니다.

## <a name="run-an-app-in-internet-explorer"></a>Internet Explorer에서 앱 실행
Internet Explorer에서 실행할 수 있는 응용 프로그램에서 MSAL.js를 사용 하려는 경우 MSAL.js 스크립트를 참조 하기 전에 약속 safehtml에 대 한 참조를 추가 해야 합니다.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Internet Explorer에서는 JavaScript 약속을 기본적으로 지원 하지 않기 때문입니다.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Internet Explorer에서 실행 되는 응용 프로그램 디버깅

### <a name="running-in-production"></a>프로덕션 환경에서 실행
응용 프로그램을 프로덕션에 배포 하는 경우 (예를 들어 Azure Web apps의 경우) 최종 사용자가 팝업을 수락한 경우 일반적으로 정상적으로 작동 합니다. Internet Explorer 11을 사용 하 여 테스트 했습니다.

### <a name="running-locally"></a>로컬에서 실행
Internet Explorer에서 실행 되는 응용 프로그램을 로컬로 실행 하 고 디버깅 하려는 경우 다음 사항을 고려해 야 합니다 (응용 프로그램을로 실행 한다고 가정 *http://localhost:1234* ).

- Internet Explorer에는 MSAL.js 올바르게 작동 하지 않도록 하는 "보호 모드" 라는 보안 메커니즘이 있습니다. 증상 중에서 로그인 한 후 페이지를로 리디렉션할 수 있습니다 http://localhost:1234/null .

- 응용 프로그램을 로컬로 실행 하 고 디버깅 하려면 "보호 모드"를 사용 하지 않도록 설정 해야 합니다. 이렇게 하려면 다음을 수행 합니다.

    1. Internet Explorer **도구** (기어 아이콘)를 클릭 합니다.
    1. **인터넷 옵션** 을 선택한 다음 **보안** 탭을 선택 합니다.
    1. **인터넷** 영역을 클릭 하 고 보호 모드 사용을 선택 취소 합니다 **(internet Explorer를 다시 시작 해야 함)**. Internet Explorer에서 컴퓨터가 더 이상 보호 되지 않는다는 경고를 표시 합니다. **확인**을 클릭합니다.
    1. Internet Explorer를 다시 시작 합니다.
    1. 응용 프로그램을 실행 하 고 디버깅 합니다.

완료 되 면 Internet Explorer 보안 설정을 복원 합니다.  **설정**  ->  **인터넷 옵션**  ->  **보안**  ->  **모든 영역을 기본 수준으로 다시 설정을**선택 합니다.

## <a name="next-steps"></a>다음 단계
[Internet Explorer에서 MSAL.js를 사용 하는 경우 알려진 문제](msal-js-use-ie-browser.md)에 대해 자세히 알아보세요.