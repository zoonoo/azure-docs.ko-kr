---
title: 인증 및 권한 부여를 위한 Azure AD .NET 웹 API 빌드 | Microsoft Docs
description: 인증 및 권한 부여를 위해 Azure AD와 통합되는 .NET MVC Web API를 빌드하는 방법입니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: abc4c05aab6efcfc55d5e4dc812ab41a3e3fc8aa
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74963276"
---
# <a name="quickstart-build-a-net-web-api-that-integrates-with-azure-ad-for-authentication-and-authorization"></a>빠른 시작: 인증 및 권한 부여를 위해 Azure AD와 통합되는 .NET 웹 API 빌드

[Microsoft ID 플랫폼](v2-overview.md)은 Azure AD(Azure Active Directory) 개발자 플랫폼의 발전된 형태입니다. 이 플랫폼을 사용하면 개발자가 모든 Microsoft ID에 로그인하고, Microsoft Graph와 같은 Microsoft API 또는 개발자가 빌드한 API를 호출하기 위한 토큰을 가져오는 애플리케이션을 빌드할 수 있습니다.

[MSAL(Microsoft 인증 라이브러리)](msal-overview.md)을 통해 개발자는 보안 웹 API에 액세스하기 위해 Microsoft ID 플랫폼 엔드포인트에서 토큰을 획득할 수 있습니다. ADAL(Active Directory 인증 라이브러리)은 개발자용 Azure AD(v1.0) 엔드포인트와 통합되며, MSAL은 Microsoft ID 플랫폼(v2.0) 엔드포인트와 통합됩니다.

새 웹 API의 경우 다음과 같이 Microsoft ID 플랫폼(v2.0) 및 MSAL을 사용하여 토큰을 획득하고 보안 웹 API에 액세스하는 것이 좋습니다. [빠른 시작: ASP.NET 웹앱에 Microsoft에 로그인 추가](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2#calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2)
