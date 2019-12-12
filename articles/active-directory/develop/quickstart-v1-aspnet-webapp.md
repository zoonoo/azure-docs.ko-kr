---
title: ASP.NET 웹앱에 Microsoft로 로그인 추가 | Microsoft Docs
description: OpenID Connect 표준을 사용하는 기존 웹 브라우저 기반 애플리케이션을 사용하여 ASP.NET 솔루션에서 Microsoft 로그인을 추가하는 방법을 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.topic: quickstart
ms.workload: identity
ms.date: 10/25/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc68db841b900ca96899e6fda8f8f3e07f41256a
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921095"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>빠른 시작: ASP.NET 웹앱에 Microsoft에 로그인 추가

[Microsoft ID 플랫폼](v2-overview.md)은 Azure AD(Azure Active Directory) 개발자 플랫폼의 발전된 형태입니다. 이 플랫폼을 사용하면 개발자가 모든 Microsoft ID에 로그인하고, Microsoft Graph와 같은 Microsoft API 또는 개발자가 빌드한 API를 호출하기 위한 토큰을 가져오는 애플리케이션을 빌드할 수 있습니다.

[MSAL(Microsoft 인증 라이브러리)](msal-overview.md)을 통해 개발자는 보안 웹 API에 액세스하기 위해 Microsoft ID 플랫폼 엔드포인트에서 토큰을 획득할 수 있습니다. ADAL(Active Directory 인증 라이브러리)은 개발자용 Azure AD(v1.0) 엔드포인트와 통합되며, MSAL은 Microsoft ID 플랫폼(v2.0) 엔드포인트와 통합됩니다.

## <a name="next-steps"></a>다음 단계

새 웹 애플리케이션의 경우 Microsoft ID 플랫폼(v2.0) 및 MSAL을 사용하여 토큰을 획득하고 보안 웹 API에 액세스하는 것이 좋습니다. [빠른 시작: ASP.NET 웹앱에 Microsoft로 로그인 추가](quickstart-v2-aspnet-webapp.md)를 참조하세요.
