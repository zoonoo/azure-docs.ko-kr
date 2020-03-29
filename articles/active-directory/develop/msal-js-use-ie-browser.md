---
title: 인터넷 익스플로러 (MSAL.js)에 대한 문제 | Azure
titleSuffix: Microsoft identity platform
description: 인터넷 익스플로러 브라우저에서 자바 스크립트 (MSAL.js)에 대 한 마이크로소프트 인증 라이브러리를 사용 합니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76695860"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>인터넷 익스플로러와 마이크로 소프트 에지 브라우저에 알려진 문제 (MSAL.js)

자바 스크립트에 대한 마이크로 소프트 인증 라이브러리 (MSAL.js)는 인터넷 익스플로러에서 실행할 수 있도록 [자바 스크립트 ES5에](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) 대해 생성됩니다. 그러나 알아야 할 몇 가지 사항이 있습니다.

## <a name="run-an-app-in-internet-explorer"></a>인터넷 익스플로러에서 앱 실행
인터넷 익스플로러에서 실행할 수 있는 응용 프로그램에서 MSAL.js를 사용하려는 경우 MSAL.js 스크립트를 참조하기 전에 약속 폴리채우기에 대한 참조를 추가해야 합니다.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

이는 인터넷 익스플로러가 자바스크립트 약속을 기본적으로 지원하지 않기 때문입니다.

## <a name="debugging-an-application-running-in-internet-explorer"></a>인터넷 익스플로러에서 실행 중인 응용 프로그램 디버깅

### <a name="running-in-production"></a>프로덕션 환경에서 실행
최종 사용자가 팝업을 수락한 경우 응용 프로그램을 프로덕션(예: Azure Web 앱)에 배포하는 것이 일반적으로 정상적으로 작동합니다. 우리는 인터넷 익스플로러 11로 테스트했습니다.

### <a name="running-locally"></a>로컬로 실행
Internet Explorer에서 실행 중인 응용 프로그램을 로컬로 실행하고 디버깅하려면 다음 고려 사항을 알고 있어야 합니다(응용 *http://localhost:1234*프로그램을 다음과 같이 실행하려는 것으로 가정).

- Internet Explorer에는 MSAL.js가 올바르게 작동하지 않는 "보호 모드"라는 보안 메커니즘이 있습니다. 증상 중 로그인한 후 페이지를 http://localhost:1234/null로 리디렉션할 수 있습니다.

- 응용 프로그램을 로컬로 실행하고 디버깅하려면 이 "보호 모드"를 사용하지 않도록 설정해야 합니다. 이를 위해:

    1. 인터넷 익스플로러 도구(기어 아이콘)를 **클릭합니다.**
    1. **인터넷 옵션을** 선택한 다음 **보안** 탭을 선택합니다.
    1. **인터넷** 영역을 클릭하고 보호 모드 활성화를 선택 **취소(인터넷 탐색기 다시 시작 필요)**. Internet Explorer는 컴퓨터가 더 이상 보호되지 않음을 경고합니다. **확인**을 클릭합니다.
    1. 인터넷 익스플로러를 다시 시작합니다.
    1. 응용 프로그램을 실행하고 디버깅합니다.

완료되면 Internet Explorer 보안 설정을 복원합니다.  **설정** -> **인터넷 옵션** -> **보안** -> **재설정 모든 영역을 기본 수준으로 설정**합니다.

## <a name="next-steps"></a>다음 단계
[인터넷 익스플로러에서 MSAL.js를 사용할 때 알려진 문제에](msal-js-use-ie-browser.md)대해 자세히 알아보십시오.