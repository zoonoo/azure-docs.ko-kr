---
title: 단일 페이지 앱을 프로덕션으로 이동 - Microsoft ID 플랫폼 | Azure
description: 단일 페이지 응용 프로그램을 빌드하는 방법 알아보기(프로덕션으로 이동)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 01b923e0d013fab1815456e55eac6036ded772bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701844"
---
# <a name="single-page-application-move-to-production"></a>단일 페이지 응용 프로그램: 프로덕션으로 이동

이제 웹 API를 호출하는 토큰을 획득하는 방법을 배웠으니 프로덕션으로 이동하는 방법을 알아봅니다.

## <a name="improve-your-app"></a>앱 개선

[로깅을 활성화하여](msal-logging.md) 앱 프로덕션을 준비합니다.

## <a name="test-your-integration"></a>통합 테스트

[Microsoft ID 플랫폼 통합 검사 목록](identity-platform-integration-checklist.md)에 따라 통합을 테스트합니다.

## <a name="next-steps"></a>다음 단계

사용자를 로그인하고 MSAL.js를 사용하여 Microsoft 그래프 API를 호출하는 액세스 토큰을 얻는 방법에 대한 코드를 설명하는 빠른 시작 샘플에 대해 자세히 설명합니다.

> [!div class="nextstepaction"]
> [자바 스크립트 스파 자습서](./tutorial-v2-javascript-spa.md)

MSAL.js를 사용하여 자신의 백 엔드 웹 API에 대한 토큰을 얻는 방법을 보여 주는 샘플:

> [!div class="nextstepaction"]
> [ASP.NET 백 엔드가 있는 SPA](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2)

MSAL.js를 사용하여 Azure Active Directory B2C(Azure AD B2C)에 등록된 앱에서 사용자를 로그인하는 방법을 보여 주는 샘플입니다.

> [!div class="nextstepaction"]
> [Azure AD B2C를 갖춘 SPA](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
