---
title: 사용자를 로그인 하는 웹 앱 (프로덕션으로 이동)-Microsoft identity platform
description: 사용자를 로그인 하는 웹 앱을 빌드하는 방법 알아보기 (프로덕션으로 이동)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: efcc7ed4f80d0e3e9750d19ff95d010052a08b87
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481882"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>사용자가 로그인 하는 웹 앱: 프로덕션으로 이동

웹 Api를 호출 하는 토큰을 가져오는 방법을 배웠으므로 이제 프로덕션으로 이동 하는 방법을 알아보세요.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>다음 단계

### <a name="scenario-for-calling-web-apis"></a>웹 Api 호출에 대 한 시나리오

웹 앱이 로그인 한 후에는 로그인 한 사용자를 대신 하 여 web Api를 호출할 수 있습니다. 웹 앱에서 웹 Api를 호출 하는 것은 다음 시나리오의 개체입니다.

> [!div class="nextstepaction"]
> [Web API를 호출하는 웹앱](scenario-web-app-call-api-overview.md)

### <a name="deep-dive-aspnet-core-web-app-tutorial"></a>심층 살펴보기: ASP.NET Core 웹 앱 자습서

이 ASP.NET Core 자습서를 사용 하 여 사용자를 로그인 하는 다른 방법에 대해 알아봅니다. 

> [!div class="nextstepaction"]
> [개발자를 위한 Microsoft id 플랫폼을 사용 하 여 웹 앱에서 사용자를 로그인 하 고 Api를 호출할 수 있도록 설정](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

이 프로그레시브 자습서에는의 계정을 사용 하 여 로그인을 추가 하는 방법을 비롯 하 여 웹 앱에 대 한 프로덕션 준비 코드가 있습니다.

- 조직
- 여러 조직
- 회사 또는 학교 계정 또는 개인 Microsoft 계정
- [Azure AD B2C](https://aka.ms/aadb2c)
- 국가별 클라우드

### <a name="sample-code-java-web-app"></a>샘플 코드: Java 웹 앱

GitHub에서이 샘플의 Java 웹 앱에 대해 자세히 알아보세요. 

> [!div class="nextstepaction"]
> [Microsoft id 플랫폼을 사용 하 여 사용자를 로그인 하 고를 호출 하는 Java 웹 응용 프로그램 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
