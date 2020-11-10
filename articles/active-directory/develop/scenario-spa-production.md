---
title: 단일 페이지 앱을 프로덕션으로 이동-Microsoft id 플랫폼 | Microsoft
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
ms.openlocfilehash: 0a51442870fb72e2b3cd93d9f03736d2c679ed06
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442823"
---
# <a name="single-page-application-move-to-production"></a>단일 페이지 응용 프로그램: 프로덕션으로 이동

이제 web Api를 호출 하는 토큰을 획득 하는 방법을 배웠으므로 프로덕션으로 이동 하는 방법을 알아보세요.

## <a name="improve-your-app"></a>앱 개선

[로깅을 사용](msal-logging.md) 하 여 앱 프로덕션을 준비 합니다.

## <a name="test-your-integration"></a>통합 테스트

[Microsoft ID 플랫폼 통합 검사 목록](identity-platform-integration-checklist.md)에 따라 통합을 테스트합니다.

## <a name="deploy-your-app"></a>앱 배포

Azure Storage 및 Azure 앱 서비스를 사용 하 여 SPA 및 Web API 프로젝트를 배포 하는 방법에 대 한 자세한 내용은 [배포 샘플](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3) 을 확인 하세요. 

## <a name="next-steps"></a>다음 단계

- 사용자 로그인 및 **MSAL.js** : [JavaScript SPA 자습서](./tutorial-v2-javascript-spa.md)를 사용 하 여 **Microsoft Graph API** 를 호출 하는 액세스 토큰을 가져오는 방법에 대 한 코드를 설명 하는 빠른 시작 샘플을 자세히 설명 합니다.

- [ASP.NET 백 엔드를](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi)사용 하 여 **MSAL.js** : SPA를 사용 하 여 사용자 고유의 백 엔드 웹 API (ASP.NET Core)에 대 한 토큰을 가져오는 방법을 보여 주는 샘플입니다.

- [Node.js](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) **passport** (azure ad)를 사용 하 여 백 엔드 웹 api (Node.js)에 대 한 액세스 토큰의 유효성을 검사 하는 방법을 보여 주는 샘플입니다.

- **MSAL.js** 를 사용 하 여 **Azure Active Directory B2C** (Azure AD B2C)에 등록 된 앱에서 사용자를 로그인 하는 방법을 보여 주는 샘플: [SPA with Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp).

- **Passport-azure-ad** 를 사용 하 여 **Azure Active Directory B2C** (Azure AD B2C)에 등록 된 앱에 대 한 액세스 토큰의 유효성을 검사 하는 방법을 보여 주는 샘플: [Node.js Web API (Azure AD B2C)](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).
