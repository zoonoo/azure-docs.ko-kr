---
title: 단일 페이지 응용 프로그램 (프로덕션으로 이동)-Microsoft id 플랫폼
description: 단일 페이지 응용 프로그램 (프로덕션으로 이동)을 빌드하는 방법을 알아봅니다
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6a04f5d62ec750cfbe44765e833552bd694654d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65075012"
---
# <a name="single-page-application---move-to-production"></a>단일 페이지 응용 프로그램-프로덕션으로 이동

이제 Web Api를 호출 하는 토큰을 획득 하는 방법을 알았으므로 프로덕션으로 이동 하는 방법에 알아봅니다.

## <a name="improve-your-app"></a>앱 개선

앱 프로덕션을 준비 확인 하는 데 필요한 단계를 따릅니다.

- 애플리케이션에 [로그인이 가능하도록 설정](msal-logging.md)합니다.

## <a name="test-your-integration"></a>통합 테스트

- [Microsoft ID 플랫폼 통합 검사 목록](identity-platform-integration-checklist.md)에 따라 통합을 테스트합니다.

## <a name="next-steps"></a>다음 단계

몇 가지 다른 샘플/자습서는 다음과 같습니다.

- 빠른 시작의 심층 분석에 대 한 사용자를 로그인 하 고 MSAL.js를 사용 하 여 MS Graph API를 호출 하는 액세스 토큰을 얻는 방법에 대 한 코드를 설명 하는 샘플

    > [!div class="nextstepaction"]
    > [JavaScript SPA 자습서](./tutorial-v2-javascript-spa.md)

- MSAL.js를 사용 하 여 고유한 백 엔드 웹 API에 대 한 토큰을 가져오도록 하는 방법을 보여 주는 샘플

     > [!div class="nextstepaction"]
     > [ASP.NET 백 엔드를 사용 하 여 SPA](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2)

- MSAL.js를 사용 하 여 Azure AD B2C를 사용 하 여 등록 된 앱에서 사용자를 로그인 하는 방법을 보여 주는 샘플

    > [!div class="nextstepaction"]
    > [Azure AD B2C를 사용 하 여 SPA](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
