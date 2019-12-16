---
title: Android(v1.0)에서 사용자 로그인 및 Microsoft Graph API 호출 | Azure
description: 내 Android 앱에서 사용자를 로그인하고 Microsoft Graph API를 호출하는 방법을 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: da1ee39f-89d3-4d36-96f1-4eabbc662343
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: quickstart
ms.date: 10/25/2019
ms.author: ryanwi
ms.reviewer: brandwe, jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87dd60f00278389c1fc03d6fd69652608ae0c998
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74966064"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app-v10"></a>빠른 시작: Android 앱(v1.0)에서 사용자 로그인 및 Microsoft Graph API 호출

[Microsoft ID 플랫폼](v2-overview.md)은 Azure AD(Azure Active Directory) 개발자 플랫폼의 발전된 형태입니다. 이 플랫폼을 사용하면 개발자가 모든 Microsoft ID에 로그인하고, Microsoft Graph와 같은 Microsoft API 또는 개발자가 빌드한 API를 호출하기 위한 토큰을 가져오는 애플리케이션을 빌드할 수 있습니다.

[MSAL(Microsoft 인증 라이브러리)](msal-overview.md)을 통해 개발자는 보안 웹 API에 액세스하기 위해 Microsoft ID 플랫폼 엔드포인트에서 토큰을 획득할 수 있습니다. ADAL(Active Directory 인증 라이브러리)은 개발자용 Azure AD(v1.0) 엔드포인트와 통합되며, MSAL은 Microsoft ID 플랫폼(v2.0) 엔드포인트와 통합됩니다.

## <a name="next-steps"></a>다음 단계

새 Android 애플리케이션의 경우 Microsoft ID 플랫폼(v2.0) 및 MSAL을 사용하여 토큰을 획득하고 보안 웹 API에 액세스하는 것이 좋습니다. [빠른 시작: Android 앱에서 사용자 로그인 및 Microsoft Graph API 호출](quickstart-v2-android.md)을 참조하여 시작하세요.
