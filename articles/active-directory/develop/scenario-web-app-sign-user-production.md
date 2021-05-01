---
title: 사용자가 로그인하는 웹앱을 프로덕션으로 이동 | Azure
titleSuffix: Microsoft identity platform
description: 사용자를 로그인하는 웹앱을 빌드하는 방법 알아보기(프로덕션으로 이동)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: a026d1c0c70fb210a1a24dd4a8d85beba97b9612
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108124216"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>사용자가 로그인하는 웹앱: 프로덕션으로 이동

이제 웹 API를 호출하는 토큰을 가져오는 방법을 배웠으므로 애플리케이션을 프로덕션 환경으로 이동할 때 고려해야 할 몇 가지 사항을 알아 봅니다.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="troubleshooting"></a>문제 해결
사용자가 처음으로 웹 애플리케이션에 로그인할 때, 동의를 해야 합니다. 그러나 일부 조직에서는 사용자가 다음과 같은 메시지를 볼 수 있습니다. *AppName에는 관리자만 부여할 수 있는 조직의 리소스에 액세스하기 위한 권한이 필요합니다. 이 앱을 사용하려면 먼저 관리자에게 이 앱에 대한 권한을 부여하도록 요청하세요.*
이는 테넌트 관리자가 사용자의 동의 기능을 **해제** 했기 때문입니다. 이 경우 테넌트 관리자에게 애플리케이션에서 요구하는 범위에 대한 관리자 동의를 수락하도록 요청합니다.

## <a name="same-site"></a>같은 사이트

[Chrome 브라우저에서 SameSite 쿠키 변경을 처리하는 방법](howto-handle-samesite-cookie-changes-chrome-browser.md)을 통해 새 버전의 Chrome 브라우저에서 발생할 수 있는 문제를 알아 두세요.

The Microsoft.Identity.Web NuGet 패키지는 일반적인 SameSite 문제를 처리할 수 있습니다.

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>심층 분석: ASP.NET Core 웹앱 자습서

이 ASP.NET Core 자습서를 사용해 사용자가 로그인하는 다른 방법에 대해 알아봅니다. 

[개발자용 Microsoft ID 플랫폼을 사용하여 웹앱에서 사용자가 로그인하고 API를 호출할 수 있도록 설정](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)

이 최신 자습서에는 다음의 계정을 사용하여 로그인을 추가하는 방법을 비롯한 웹앱용 프로덕션 준비 코드가 있습니다.

- 조직
- 여러 조직
- 회사 및 학교 계정 또는 개인 Microsoft 계정
- [Azure AD B2C](../../active-directory-b2c/overview.md)
- 국가별 클라우드

## <a name="tutorial-nodejs-web-app"></a>자습서: Node.js 웹앱

이 자습서의 Node.js 웹에 대해 자세히 알아보세요.

[자습서: Node.js 및 Express 웹앱에서 사용자 로그인](./tutorial-v2-nodejs-webapp-msal.md)

## <a name="sample-code-java-web-app"></a>샘플 코드: Java 웹앱

GitHub에서 이 샘플 Java 웹앱에 대해 자세히 알아보세요. 

[Microsoft ID 플랫폼을 사용하여 사용자가 로그인하고 Microsoft Graph를 호출하는 Java 웹 애플리케이션](https://github.com/Azure-Samples/ms-identity-java-webapp)

## <a name="next-steps"></a>다음 단계

웹앱에 사용자가 로그인한 후에는 로그인한 사용자를 대신하여 웹 API를 호출할 수 있습니다. 웹앱에서 웹 API 호출은 [웹 API를 호출하는 웹앱](scenario-web-app-call-api-overview.md) 시나리오의 개체입니다.