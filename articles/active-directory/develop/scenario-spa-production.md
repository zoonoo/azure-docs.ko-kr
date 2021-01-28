---
title: 단일 페이지 앱을 프로덕션으로 이동
titleSuffix: Microsoft identity platform
description: 단일 페이지 응용 프로그램을 빌드하는 방법 알아보기 (프로덕션으로 이동)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 9656da8be086724482f129efab323e02b73e117e
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954966"
---
# <a name="single-page-application-move-to-production"></a>단일 페이지 응용 프로그램: 프로덕션으로 이동

이제 웹 Api를 호출 하는 토큰을 획득 하는 방법을 배웠으므로 응용 프로그램을 프로덕션 환경으로 이동할 때 고려해 야 할 몇 가지 사항이 있습니다.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="deploy-your-app"></a>앱 배포

Azure Storage 및 Azure 앱 서비스를 사용 하 여 SPA 및 Web API 프로젝트를 배포 하는 방법에 대 한 자세한 내용은 [배포 샘플](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3) 을 확인 하세요. 

## <a name="code-samples"></a>코드 샘플

이러한 코드 샘플은 단일 페이지 앱에 대 한 몇 가지 주요 작업을 보여 줍니다.
- [ASP.NET를](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi)사용 하는 SPA: **MSAL.js** 를 사용 하 여 사용자 고유의 백 엔드 웹 API (ASP.NET Core)에 대 한 토큰을 가져오는 방법입니다.

- [Node.js WEB api (AZURE ad](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2): **passport-azure-AD** 를 사용 하 여 백 엔드 웹 api (Node.js)에 대 한 액세스 토큰의 유효성을 검사 하는 방법입니다.

- [SPA Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp): **MSAL.js** 를 사용 하 여 **Azure Active Directory B2C** 에 등록 된 앱에서 사용자를 로그인 하는 방법 (Azure AD B2C).

- [Node.js WEB API (Azure AD B2C)](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi): **passport** 를 사용 하 여 **Azure Active Directory B2C** 에 등록 된 앱에 대 한 액세스 토큰의 유효성을 검사 하는 방법 (Azure AD B2C).

## <a name="next-steps"></a>다음 단계

- [JAVASCRIPT SPA 자습서](./tutorial-v2-javascript-spa.md): **MSAL.js** 를 사용 하 여 사용자를 로그인 하 고 **Microsoft Graph API** 를 호출 하는 액세스 토큰을 가져오는 방법을 자세히 설명 합니다.