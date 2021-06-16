---
title: Internet Explore의 문제(MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Internet Explorer 브라우저에서 JavaScript용 Microsoft 인증 라이브러리(MSAL.js)를 사용합니다.
services: active-directory
author: mtillman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/16/2019
ms.author: mtillman
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: eda2d61d7af1b56a2e0069e8726aa1e29800e19a
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112077125"
---
# <a name="known-issues-on-internet-explorer-browsers-msaljs"></a>Internet Explorer 브라우저(MSAL.js)의 알려진 문제

JavaScript용 Microsoft 인증 라이브러리(MSAL.js)는 Internet Explorer에서 실행할 수 있도록 [JavaScript ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29)용으로 생성됩니다. 그러나 알아야 할 몇 가지 사항이 있습니다.

## <a name="run-an-app-in-internet-explorer"></a>Internet Explorer에서 앱 실행
Internet Explorer에서 실행할 수 있는 애플리케이션에서 MSAL.js를 사용하려는 경우 MSAL.js 스크립트를 참조하기 전에 프라미스 polyfill에 대한 참조를 추가해야 합니다.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

이는 Internet Explorer에서는 기본적으로 JavaScript 프라미스를 지원하지 않기 때문입니다.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Internet Explorer에서 실행 중인 애플리케이션 디버깅

### <a name="running-in-production"></a>프로덕션에서 실행
최종 사용자가 팝업을 수락한 경우 애플리케이션을 프로덕션(예: Azure 웹앱)에 배포하면 정상적으로 작동합니다. Internet Explorer 11로 테스트했습니다.

### <a name="running-locally"></a>로컬로 실행
Internet Explorer에서 실행되는 애플리케이션을 로컬로 실행하고 디버깅하려는 경우 다음 사항을 고려해야 합니다(애플리케이션을 *http://localhost:1234* 로 실행한다고 가정).

- Internet Explorer에는 MSAL.js가 올바르게 작동하지 못하게 하는 "보호 모드"라는 보안 메커니즘이 있습니다. 증상 중 로그인 후 페이지를 http://localhost:1234/null 로 리디렉션할 수 있습니다.

- 애플리케이션을 로컬로 실행하고 디버그하려면 "보호 모드"를 사용하지 않도록 설정해야 합니다. 대상:

    1. Internet Explorer **도구**(기어 아이콘)를 클릭합니다.
    1. **인터넷 옵션** 을 선택한 다음, **보안** 탭을 선택합니다.
    1. **인터넷** 영역을 클릭하고 **보호 모드 사용(internet Explorer를 다시 시작해야 함)** 을 선택 취소합니다. Internet Explorer에서 컴퓨터가 더 이상 보호되지 않는다는 경고를 표시합니다. **확인** 을 클릭합니다.
    1. Internet Explorer를 다시 시작합니다.
    1. 애플리케이션을 실행하고 디버그합니다.

완료되면 Internet Explorer 보안 설정을 복원합니다.  **설정** -> **인터넷 옵션** -> **보안** -> **모든 영역을 기본 수준으로 다시 설정** 을 선택합니다.

## <a name="next-steps"></a>다음 단계
[Internet Explorer에서 MSAL.js를 사용할 때 알려진 문제](msal-js-use-ie-browser.md)에 대해 자세히 알아보세요.