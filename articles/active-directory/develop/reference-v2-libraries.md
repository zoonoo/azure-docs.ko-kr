---
title: Microsoft ID 플랫폼 인증 라이브러리 | Azure
description: Microsoft ID 플랫폼과 호환되는 클라이언트 라이브러리 및 미들웨어 목록입니다. 이러한 라이브러리를 사용하여 사용자 로그인(인증) 및 보호된 웹 API 액세스(권한 부여)에 대한 지원을 애플리케이션에 추가할 수 있습니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 03/30/2021
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 321a5c473df30dd6f00bbcd1294d48ce8da34009
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060383"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Microsoft ID 플랫폼 인증 라이브러리

다음 표에서는 여러 애플리케이션 유형에 대한 Microsoft 인증 라이브러리 지원을 보여 줍니다. 여기에는 라이브러리 소스 코드 링크, 앱 프로젝트의 패키지 위치, 라이브러리가 사용자 로그인(인증), 보호된 웹 API(인증)에 대한 액세스 또는 두 경우를 모두 지원하는지 여부가 포함됩니다.

Microsoft ID 플랫폼은 OpenID Foundation에서 [인증된 OpenID 공급자](https://openid.net/certification/)로 인증되었습니다. MSAL(Microsoft 인증 라이브러리) 또는 다른 Microsoft 지원 라이브러리 이외의 라이브러리를 사용하려면 [인증된 OpenID Connect 구현](https://openid.net/developers/certified/)을 통해 하나를 선택합니다.

고유한 프로토콜 수준 구현인 [OAuth 2.0 또는 OpenID Connect 1.0](active-directory-v2-protocols.md)을 직접 코딩하려면 각 표준 사양의 보안 고려 사항에 주의하고 [Microsoft SDL][Microsoft-SDL]과 같은 SDL(소프트웨어 개발 수명 주기) 방법론을 따라야 합니다.

## <a name="single-page-application-spa"></a>SPA(단일 페이지 애플리케이션)

단일 페이지 애플리케이션은 전적으로 해당 브라우저에서 실행되며 페이지 데이터(HTML, CSS, JavaScript)를 동적으로 또는 애플리케이션 로드 시간에 불러옵니다. 웹 API를 호출하여 백 엔드 데이터 원본과 상호 작용할 수 있습니다.

SPA의 코드는 전적으로 브라우저에서 실행되기 때문에 비밀을 안전하게 저장할 수 없는 ‘퍼블릭 클라이언트’로 간주됩니다.

[!INCLUDE [active-directory-develop-libraries-spa](../../../includes/active-directory-develop-libraries-spa.md)]

## <a name="web-application"></a>웹 애플리케이션

웹 애플리케이션은 HTML, CSS, JavaScript를 생성하고 렌더링할 사용자 웹 브라우저로 보내는 서버에서 코드를 실행합니다. 사용자의 ID는 사용자의 브라우저(프런트 엔드)와 웹 서버(백 엔드) 사이에서 세션으로 유지 관리됩니다.

웹 애플리케이션의 코드는 웹 서버에서 실행되므로 비밀을 안전하게 저장할 수 있는 ‘기밀 클라이언트’로 간주됩니다.

[!INCLUDE [active-directory-develop-libraries-webapp](../../../includes/active-directory-develop-libraries-webapp.md)]

## <a name="desktop-application"></a>데스크톱 애플리케이션

데스크톱 애플리케이션은 일반적으로 사용자 인터페이스를 표시하는 컴파일된 이진 코드이고 사용자의 데스크톱에서 실행하게 되어 있습니다.

데스크톱 애플리케이션은 사용자의 데스크톱에서 실행되므로 비밀을 안전하게 저장할 수 없는 ‘퍼블릭 클라이언트’로 간주됩니다.

[!INCLUDE [active-directory-develop-libraries-desktop](../../../includes/active-directory-develop-libraries-desktop.md)]

## <a name="mobile-application"></a>모바일 애플리케이션

모바일 애플리케이션은 일반적으로 사용자 인터페이스를 표시하는 컴파일된 이진 코드이고 사용자의 모바일 디바이스에서 실행하게 되어 있습니다.

모바일 애플리케이션은 사용자의 모바일 디바이스에서 실행되므로 비밀을 안전하게 저장할 수 없는 ‘퍼블릭 클라이언트’로 간주됩니다.

[!INCLUDE [active-directory-develop-libraries-mobile](../../../includes/active-directory-develop-libraries-mobile.md)]

## <a name="service--daemon"></a>서비스/디먼

서비스 및 디먼은 일반적으로 서버 간에 사용되거나 기타 무인(‘헤드리스’라고도 함) 통신에 사용됩니다. 키보드에 자격 증명을 입력하거나 리소스 액세스에 동의하는 사용자가 없기 때문에 이러한 애플리케이션은 웹 API 리소스에 대한 권한 부여된 액세스를 요청할 때 사용자가 아닌 애플리케이션 자신으로 인증합니다.

서버에서 실행되는 서비스 또는 디먼은 비밀을 안전하게 저장할 수 있는 ‘기밀 클라이언트’로 간주됩니다.

[!INCLUDE [active-directory-develop-libraries-daemon](../../../includes/active-directory-develop-libraries-daemon.md)]

## <a name="next-steps"></a>다음 단계

Microsoft 인증 라이브러리에 대한 자세한 내용은 [MSAL(Microsoft 인증 라이브러리) 개요](msal-overview.md)를 참조하세요.

<!--Image references-->
[y]: ./media/common/yes.png
[n]: ./media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
