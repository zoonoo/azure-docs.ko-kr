---
title: .NET Desktop(WPF)에서 사용자 로그인 및 Microsoft Graph API 호출 | Azure
description: 로그인을 위해 Azure AD와 통합되고 OAuth 2.0을 사용하여 Azure AD로 보호되는 API를 호출하는 .NET Windows Desktop 애플리케이션을 빌드하는 방법을 알아보세요.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
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
ms.openlocfilehash: 0f1aca48c547fc8c298128b35d9c571512fcbdf8
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965945"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-a-net-desktop-wpf-app"></a>빠른 시작: .NET Desktop(WPF) 앱에서 사용자 로그인 및 Microsoft Graph API 호출

[Microsoft ID 플랫폼](v2-overview.md)은 Azure AD(Azure Active Directory) 개발자 플랫폼의 발전된 형태입니다. 이 플랫폼을 사용하면 개발자가 모든 Microsoft ID에 로그인하고, Microsoft Graph와 같은 Microsoft API 또는 개발자가 빌드한 API를 호출하기 위한 토큰을 가져오는 애플리케이션을 빌드할 수 있습니다.

[MSAL(Microsoft 인증 라이브러리)](msal-overview.md)을 통해 개발자는 보안 웹 API에 액세스하기 위해 Microsoft ID 플랫폼 엔드포인트에서 토큰을 획득할 수 있습니다. ADAL(Active Directory 인증 라이브러리)은 개발자용 Azure AD(v1.0) 엔드포인트와 통합되며, MSAL은 Microsoft ID 플랫폼(v2.0) 엔드포인트와 통합됩니다.

새 데스크톱 애플리케이션의 경우 다음과 같이 Microsoft ID 플랫폼(v2.0) 및 MSAL을 사용하여 토큰을 획득하고 보안 웹 API에 액세스하는 것이 좋습니다. [빠른 시작: Windows 데스크톱 앱에서 토큰 가져오기 및 Microsoft Graph API 호출](quickstart-v2-windows-desktop.md)
