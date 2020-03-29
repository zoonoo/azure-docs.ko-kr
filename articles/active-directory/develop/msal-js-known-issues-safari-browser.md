---
title: 알려진 사파리 브라우저 문제 (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: 사파리 브라우저에서 자바스크립트(MSAL.js)용 Microsoft 인증 라이브러리를 사용할 때 문제에 대해 알아봅니다.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: edb995e31c2872c1541e29fee09dd66aafc8f9e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696115"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>MSAL.js와 사파리 브라우저에 알려진 문제 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>사파리 12 및 ITP 2.0에서 자동 토큰 갱신

애플 아이폰 OS 12 및 맥 OS 10.14 운영 [체제는 사파리의](https://developer.apple.com/safari/whats-new/)릴리스를 포함 12 브라우저 . 보안 및 개인 정보 보호를 위해 Safari 12에는 [지능형 추적 방지 2.0이](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/)포함되어 있습니다. 이로 인해 브라우저가 설정되는 타사 쿠키를 삭제합니다. 또한 ITP 2.0은 ID 공급자가 설정한 쿠키를 타사 쿠키로 취급합니다.

### <a name="impact-on-msaljs"></a>MSAL.js에 미치는 영향

MSAL.js는 숨겨진 Iframe을 사용하여 통화의 일부로 자동 `acquireTokenSilent` 토큰 수집 및 갱신을 수행합니다. 자동 토큰 요청은 Azure AD에서 설정한 쿠키로 표시되는 인증된 사용자 세션에 액세스할 수 있는 Iframe에 의존합니다. ITP 2.0이 이러한 쿠키에 대한 액세스를 차단하면 MSAL.js는 자동으로 토큰을 `acquireTokenSilent` 획득하고 갱신하지 못하므로 오류가 발생합니다.

이 시점에서이 문제에 대 한 해결책이 없습니다 그리고 우리는 표준 커뮤니티와 옵션을 평가 하 고 있습니다.

### <a name="work-around"></a>해결

기본적으로 ITP 설정은 Safari 브라우저에서 활성화되어 있습니다. 기본 설정**개인 정보** 로 이동 하 고 **사이트 간 추적 방지** 옵션을 선택 취소 하 여이 설정을 사용 하지 않도록 설정할 수 **있습니다.** -> 

![사파리 설정](./media/msal-js-known-issue-safari-browser/safari.png)

사용자가 로그인하라는 `acquireTokenSilent` 메시지를 표시하는 대화형 획득 토큰 호출을 사용하여 오류를 처리해야 합니다.
반복되는 로그인을 방지하려면 `acquireTokenSilent` 오류 처리및 대화형 호출을 진행하기 전에 Safari에서 ITP 설정을 비활성화할 수 있는 옵션을 사용자에게 제공하는 방법을 구현할 수 있습니다. 설정이 비활성화되면 후속 자동 토큰 갱신이 성공해야 합니다.
