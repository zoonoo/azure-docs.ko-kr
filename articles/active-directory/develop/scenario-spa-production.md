---
title: 단일 페이지 앱을 프로덕션으로 이동
titleSuffix: Microsoft identity platform
description: 단일 페이지 애플리케이션을 빌드하는 방법 알아보기(프로덕션으로 이동)
services: active-directory
author: mtillman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: mtillman
ms.custom: aaddev
ms.openlocfilehash: 48f9b90ef72d79510747cba82a59386d4f3a4372
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112075172"
---
# <a name="single-page-application-move-to-production"></a>단일 페이지 애플리케이션: 프로덕션으로 이동

이제 웹 API를 호출하는 토큰을 획득하는 방법을 배웠으므로 다음 애플리케이션을 프로덕션 환경으로 이동할 때 고려해야 할 몇 가지 사항을 확인합니다.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="deploy-your-app"></a>앱 배포

각각 Azure Storage 및 Azure App Services를 사용하여 SPA 및 Web API 프로젝트를 배포하는 방법을 알아보려면 [배포 샘플](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3)을 확인하세요. 

## <a name="code-samples"></a>코드 샘플

이 코드 샘플은 단일 페이지 앱의 몇 가지 주요 작업을 보여 줍니다.
- [ASP.NET 백 엔드를 사용한 SPA](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi): **MSAL.js** 를 사용하여 고유한 백 엔드 웹 API(ASP.NET Core)용 토큰을 가져오는 방법입니다.

- [Node.js Web API(Azure AD)](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2): **passport-azure-ad** 를 사용하여 백 엔드 웹 API(Node.js)용 액세스 토큰의 유효성을 검사하는 방법입니다.

- [Azure AD B2C를 사용한 SPA](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa): **MSAL.js** 를 사용하여 **Azure AD B2C**(Azure Active Directory B2C)에 등록된 앱에서 사용자를 로그인시키는 방법입니다.

- [Node.js Web API(Azure AD B2C)](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi): **passport-azure-ad** 를 사용하여 Azure AD B2C **(Azure Active Directory B2C)** 에 등록된 앱용 액세스 토큰의 유효성을 검사하는 방법입니다.

## <a name="next-steps"></a>다음 단계

- [JavaScript SPA 자습서](./tutorial-v2-javascript-auth-code.md): 사용자를 로그인시키고 **MSAL.js** 를 사용하여 **Microsoft Graph API** 를 호출하기 위한 액세스 토큰을 가져오는 방법을 자세히 살펴봅니다.