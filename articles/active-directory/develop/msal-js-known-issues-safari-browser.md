---
title: 알려진 Safari 브라우저 문제 (MSAL .js) | Microsoft
titleSuffix: Microsoft identity platform
description: Safari 브라우저에서 JavaScript 용 Microsoft 인증 라이브러리 (MSAL)를 사용 하는 경우 문제를 파악 하는 방법에 대해 알아봅니다.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76696115"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>MSAL를 사용한 Safari 브라우저의 알려진 문제 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Safari 12 및 ITP 2.0의 자동 토큰 갱신

Apple iOS 12 및 MacOS 10.14 운영 체제는 [Safari 12 브라우저](https://developer.apple.com/safari/whats-new/)의 릴리스를 포함 합니다. 보안 및 개인 정보 보호를 위해 Safari 12에는 [지능형 추적 방지 2.0](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/)이 포함 되어 있습니다. 이렇게 하면 기본적으로 브라우저는 설정 되는 타사 쿠키를 삭제 합니다. ITP 2.0는 또한 id 공급자에 의해 설정 된 쿠키를 타사 쿠키로 처리 합니다.

### <a name="impact-on-msaljs"></a>MSAL에 미치는 영향

MSAL는 숨겨진 Iframe을 사용 하 여 `acquireTokenSilent` 호출의 일부로 자동 토큰 획득 및 갱신을 수행 합니다. 자동 토큰 요청은 Azure AD에서 설정 된 쿠키가 나타내는 인증 된 사용자 세션에 액세스할 수 있는 Iframe을 사용 합니다. ITP 2.0를 사용 하 여 이러한 쿠키에 대 한 액세스를 차단 하는 경우 MSAL는 토큰을 자동으로 `acquireTokenSilent` 획득 하 고 갱신할 수 없으므로 오류가 발생 합니다.

지금은이 문제에 대 한 솔루션이 없으며 표준 커뮤니티를 사용 하 여 옵션을 평가 하 고 있습니다.

### <a name="work-around"></a>해결 방법

기본적으로 ITP 설정은 Safari 브라우저에서 사용 하도록 설정 됩니다. **기본 설정** -> **개인 정보** 로 이동 하 고 **교차 사이트 추적 방지** 옵션의 선택을 취소 하 여이 설정을 사용 하지 않도록 설정할 수 있습니다.

![safari 설정](./media/msal-js-known-issue-safari-browser/safari.png)

사용자에 게 로그인 하 라는 `acquireTokenSilent` 메시지를 표시 하는 대화형 획득 토큰 호출로 오류를 처리 해야 합니다.
반복 해 서 로그인을 방지 하기 위해 구현할 수 있는 방법은 오류를 `acquireTokenSilent` 처리 하 고 사용자에 게 Safari에서 itp 설정을 사용 하지 않도록 설정 하는 옵션을 제공 하 여 대화형 호출을 진행 하는 것입니다. 설정을 사용 하지 않도록 설정 하면 후속 자동 토큰 갱신에 성공 해야 합니다.
