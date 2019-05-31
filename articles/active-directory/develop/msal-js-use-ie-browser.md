---
title: Internet explorer (Microsoft Authentication Library for JavaScript) | Azure
description: JavaScript (MSAL.js) 용 Microsoft Authentication Library를 사용 하 여 Internet Explorer 브라우저에 대해 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cf8c84120f4c90d3943cfc31ffbf9aafcec0ba3
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873917"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>MSAL.js 사용 하 여 Internet Explorer와 Microsoft Edge 브라우저에서 알려진된 문제

JavaScript (MSAL.js) 용 Microsoft 인증 라이브러리에 대해 생성 됩니다 [JavaScript ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) Internet Explorer에서 실행할 수 있도록 합니다. 그러나 있습니다, 몇 가지 알아야 합니다.

## <a name="run-an-app-in-internet-explorer"></a>Internet Explorer에서 앱 실행
MSAL.js Internet Explorer에서 실행할 수 있는 응용 프로그램에서 사용 하려는 경우 MSAL.js 스크립트 참조 하기 전에 약속 polyfill에 대 한 참조를 추가 해야 합니다.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

즉, Internet Explorer JavaScript 프라미스를 고유 하 게 지원 하지 않습니다.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Internet Explorer에서 실행 중인 응용 프로그램 디버깅

### <a name="running-in-production"></a>프로덕션 환경에서 실행
일반적으로 응용 프로그램 (예를 들어 Azure 웹 앱)에서 프로덕션으로 배포 정상적으로, 최종 사용자가 팝업을 수락을 제공 합니다. Internet Explorer 11을 사용 하 여 테스트 했습니다.

### <a name="running-locally"></a>로컬로 실행
다음 고려 사항에 유의 해야 하는 로컬로 실행 하면서 디버깅할 응용 프로그램을 Internet Explorer에서 실행 하려는 경우 (으로 응용 프로그램을 실행 한다고 가정해 보십시오 *http://localhost:1234*):

- Internet Explorer의 "보호 모드" MSAL.js 제대로 작동 하지 못하게 하는 명명 된 보안 메커니즘입니다. 증상, 중, 로그인 한 후 페이지 리디렉션될 수 http://localhost:1234/null입니다.

- 를 실행 하 고 응용 프로그램을 로컬로 디버그이 "보호 모드"를 사용 하지 않도록 설정 해야 합니다. 이 방법을 사용 합니다.

    1. Internet Explorer를 클릭 **도구** (기어 아이콘).
    1. 선택 **인터넷 옵션** 차례로 합니다 **보안** 탭 합니다.
    1. 클릭 합니다 **Internet** 영역을 마우스 선택 취소 **보호 모드 사용 (Internet Explorer를 다시 시작 필요)** 합니다. Internet Explorer 컴퓨터를 더 이상 보호 경고를 표시 합니다. **확인**을 클릭합니다.
    1. Internet Explorer를 다시 시작 합니다.
    1. 실행 하 고 응용 프로그램을 디버그 합니다.

완료 되 면 Internet Explorer 보안 설정을 복원 합니다.  선택 **설정을** -> **인터넷 옵션** -> **Security** -> **기본 수준로모든영역을다시설정**.

## <a name="next-steps"></a>다음 단계
에 대해 자세히 알아보세요 [MSAL.js를 사용 하 여 Internet Explorer에서 때의 알려진 문제](msal-js-use-ie-browser.md)합니다.