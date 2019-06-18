---
title: 알려진 문제 브라우저 (Microsoft Authentication Library for JavaScript) | Azure
description: 에 대해 알아봅니다 (MSAL.js) JavaScript 용 Microsoft Authentication Library를 사용 하는 경우에 문제를 알고 있는 Safari 브라우저를 사용 하 여.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb89b1ef4dbbef234fba3152d7f85bbadfbdc64a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65873887"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>MSAL.js 사용 하 여 Safari 브라우저에서 알려진된 문제 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Safari 12에 ITP 2.0 자동 토큰 갱신

12 Apple iOS 및 MacOS 10.14 운영 체제 릴리스를 포함 합니다 [Safari 12 브라우저](https://developer.apple.com/safari/whats-new/)합니다. Safari 12 보안 및 개인 정보 보호를 위해 포함 된 [지능형 추적 방지 2.0](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/)합니다. 기본적으로이 위치를 선택 하면 해당 브라우저 설정에서 타사 쿠키를 삭제 하려면. 또한 ITP 2.0 id 공급자에서 타사 쿠키로 설정한 쿠키를 처리 합니다.

### <a name="impact-on-msaljs"></a>MSAL.js에 미치는 영향

MSAL.js 숨겨진된 Iframe을 사용 하 여의 일부로 자동 토큰 획득 및 갱신을 수행 하 여 `acquireTokenSilent` 호출 합니다. 자동 토큰 요청을 Azure AD에서 설정 하는 쿠키를 나타내는 인증 된 사용자 세션에 대 한 액세스를 가진 Iframe에 의존 합니다. 이러한 쿠키에 대 한 액세스를 방지 ITP 2.0을 사용 하 여 MSAL.js 자동으로 획득 하 고 토큰을 갱신에 실패 하 고이 인해 `acquireTokenSilent` 실패 합니다.

이 시점에서이 문제에 대 한 솔루션은 및 표준 커뮤니티를 사용 하 여 옵션을 평가 하는 것입니다.

### <a name="work-around"></a>해결

기본적으로는 ITP 설정이 Safari 브라우저에서 사용 됩니다. 로 이동 하 여이 설정을 사용 하지 않도록 설정할 수 있습니다 **기본 설정** -> **개인 정보 취급** 선택을 취소 하 고는 **사이트 간 추적 방지** 옵션입니다.

![safari 설정](./media/msal-js-known-issue-safari-browser/safari.png)

처리 해야 합니다는 `acquireTokenSilent` 대화형를 사용 하 여 오류 토큰 호출에 로그인 하 라는 메시지를 획득 합니다.
반복 된 로그인을 방지 하려면 구현할 수 있습니다 하는 방법을 처리 하는 것을 `acquireTokenSilent` 실패 사용자 대화형 호출을 진행 하기 전에 ITP 설정에서 Safari 사용 하지 않도록 설정 하는 옵션을 제공 합니다. 설정이 해제 되 면 후속 자동 토큰 갱신 성공 해야 합니다.
