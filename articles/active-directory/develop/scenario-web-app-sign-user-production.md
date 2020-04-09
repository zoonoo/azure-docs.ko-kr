---
title: 사용자에 서명하는 웹 앱을 프로덕션으로 이동 - Microsoft ID 플랫폼 | Azure
description: 사용자에게 서명하는 웹 앱을 빌드하는 방법 알아보기(프로덕션으로 이동)
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
ms.openlocfilehash: 9c5fd444c55a20441325088912a07eb051219b84
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881471"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>사용자에 서명하는 웹 앱: 프로덕션으로 이동

이제 웹 API를 호출하는 토큰을 얻는 방법을 배웠으니 프로덕션으로 이동하는 방법을 알아봅니다.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>다음 단계

### <a name="same-site"></a>동일한 사이트

Chrome 브라우저의 새 버전에서 발생할 수 있는 문제를 이해해야 합니다.

> [!div class="nextstepaction"]
> [크롬 브라우저에서 동일 사이트 쿠키 변경 내용을 처리 하는 방법](howto-handle-samesite-cookie-changes-chrome-browser.md)

### <a name="scenario-for-calling-web-apis"></a>웹 API 호출 시나리오

웹 앱이 사용자에 로그인한 후 로그인한 사용자를 대신하여 웹 API를 호출할 수 있습니다. 웹 앱에서 웹 API를 호출하는 것은 다음 시나리오의 개체입니다.

> [!div class="nextstepaction"]
> [Web API를 호출하는 웹앱](scenario-web-app-call-api-overview.md)

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>심층 분석: ASP.NET 코어 웹 앱 자습서

이 ASP.NET 핵심 자습서를 사용하여 사용자를 로그인하는 다른 방법에 대해 알아봅니다. 

> [!div class="nextstepaction"]
> [웹 앱이 사용자를 로그인하고 개발자를 위한 Microsoft ID 플랫폼을 사용하여 API를 호출할 수 있도록 설정합니다.](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

이 프로그레시브 자습서에는 다음과 같은 계정으로 로그인을 추가하는 방법을 포함하여 웹 앱에 대한 프로덕션 준비 코드가 있습니다.

- 귀사
- 여러 조직
- 직장 또는 학교 계정 또는 개인 Microsoft 계정
- [Azure AD B2C](https://aka.ms/aadb2c)
- 전국 구름

## <a name="sample-code-java-web-app"></a>샘플 코드: 자바 웹 앱

GitHub의 이 샘플에서 Java 웹 앱에 대해 자세히 알아보십시오. 

> [!div class="nextstepaction"]
> [Microsoft ID 플랫폼으로 사용자에 서명하고 Microsoft 그래프를 호출하는 Java 웹 응용 프로그램](https://github.com/Azure-Samples/ms-identity-java-webapp)
