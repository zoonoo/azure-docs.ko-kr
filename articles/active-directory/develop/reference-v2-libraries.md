---
title: Microsoft id 플랫폼 인증 라이브러리 | Microsoft
description: Microsoft id 플랫폼과 호환 되는 클라이언트 라이브러리 및 미들웨어 목록입니다. 이러한 라이브러리를 사용 하 여 사용자 로그인 (인증) 및 보호 된 웹 API 액세스 (권한 부여)에 대 한 지원을 응용 프로그램에 추가할 수 있습니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 01/29/2021
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 590e57d587c8e6e254811892b5c5e740b511c302
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104690656"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Microsoft id 플랫폼 인증 라이브러리

다음 표에서는 여러 응용 프로그램 유형에 대 한 Microsoft 인증 라이브러리 지원을 보여 줍니다. 여기에는 라이브러리 소스 코드에 대 한 링크, 앱 프로젝트에 대 한 패키지를 가져올 위치, 라이브러리가 사용자 로그인 (인증), 보호 된 웹 Api (인증)에 대 한 액세스 또는 둘 모두를 지원 하는지 여부가 포함 됩니다.

Microsoft id 플랫폼은 [인증 된 openid connect 공급자로](https://openid.net/certification/)openid connect Foundation에서 인증 되었습니다. MSAL (Microsoft 인증 라이브러리) 이외의 라이브러리나 Microsoft에서 지 원하는 다른 라이브러리를 사용 하려는 경우 [인증 된 Openid connect Connect 구현](https://openid.net/developers/certified/)으로 하나를 선택 합니다.

[OAuth 2.0 또는 Openid connect Connect 1.0](active-directory-v2-protocols.md)의 고유한 프로토콜 수준 구현을 직접 코딩 하도록 선택 하는 경우 각 표준 사양의 보안 고려 사항에 주의 하 고 [Microsoft SDL][Microsoft-SDL]과 같은 sdl (소프트웨어 개발 수명 주기) 방법론을 따릅니다.

## <a name="single-page-application-spa"></a>SPA (단일 페이지 응용 프로그램)

단일 페이지 응용 프로그램은 브라우저 화면에서 전적으로 실행 되며 페이지 데이터 (HTML, CSS 및 JavaScript)를 동적으로 또는 응용 프로그램 로드 시 인출 합니다. 웹 Api를 호출 하 여 백 엔드 데이터 원본과 상호 작용할 수 있습니다.

SPA의 코드가 브라우저에서 완전히 실행 되기 때문에 암호를 안전 하 게 저장할 수 없는 *공용 클라이언트로* 간주 됩니다.

[!INCLUDE [active-directory-develop-libraries-spa](../../../includes/active-directory-develop-libraries-spa.md)]

## <a name="web-application"></a>웹 애플리케이션

웹 응용 프로그램은 렌더링 되는 사용자의 웹 브라우저에 HTML, CSS 및 JavaScript를 생성 하 고 보내는 서버에서 코드를 실행 합니다. 사용자의 id는 사용자의 브라우저 (프런트 엔드)와 웹 서버 (백 엔드) 사이에서 세션으로 유지 됩니다.

웹 응용 프로그램의 코드는 웹 서버에서 실행 되므로 비밀을 안전 하 게 저장할 수 있는 *기밀 클라이언트* 라고 합니다.

[!INCLUDE [active-directory-develop-libraries-webapp](../../../includes/active-directory-develop-libraries-webapp.md)]

## <a name="desktop-application"></a>데스크톱 애플리케이션

데스크톱 응용 프로그램은 일반적으로 사용자 인터페이스를 표시 하 고 사용자의 데스크톱에서 실행 하기 위한 이진 (컴파일) 코드입니다.

데스크톱 응용 프로그램은 사용자의 데스크톱에서 실행 되므로 비밀을 안전 하 게 저장할 수 없는 *공용 클라이언트로* 간주 됩니다.

[!INCLUDE [active-directory-develop-libraries-desktop](../../../includes/active-directory-develop-libraries-desktop.md)]

## <a name="mobile-application"></a>모바일 애플리케이션

모바일 응용 프로그램은 일반적으로 사용자 인터페이스를 표시 하 고 사용자의 모바일 장치에서 실행 되는 이진 (컴파일) 코드입니다.

모바일 응용 프로그램은 사용자의 모바일 장치에서 실행 되기 때문에 암호를 안전 하 게 저장할 수 없는 *공용 클라이언트로* 간주 됩니다.

[!INCLUDE [active-directory-develop-libraries-mobile](../../../includes/active-directory-develop-libraries-mobile.md)]

## <a name="service--daemon"></a>서비스/디먼

서비스 및 디먼는 일반적으로 서버 간 및 기타 무인 ( *헤드리스*) 통신에 사용 됩니다. 키보드에 자격 증명을 입력 하거나 리소스 액세스에 동의할 수 있는 사용자가 없기 때문에, 이러한 응용 프로그램은 웹 API 리소스에 대 한 권한 있는 액세스를 요청할 때 사용자가 아닌 자신으로 인증 합니다.

서버에서 실행 되는 서비스 또는 디먼은 암호를 안전 하 게 저장할 수 있는 *기밀 클라이언트로* 간주 됩니다.

[!INCLUDE [active-directory-develop-libraries-daemon](../../../includes/active-directory-develop-libraries-daemon.md)]

## <a name="next-steps"></a>다음 단계

Microsoft 인증 라이브러리에 대 한 자세한 내용은 [MSAL (Microsoft 인증 라이브러리) 개요](msal-overview.md)를 참조 하세요.

<!--Image references-->
[y]: ./media/common/yes.png
[n]: ./media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
