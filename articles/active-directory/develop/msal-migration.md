---
title: MSAL (Microsoft 인증 라이브러리)로 마이그레이션
titleSuffix: Microsoft identity platform
description: MSAL (Microsoft 인증 라이브러리) 및 ADAL (Azure AD 인증 라이브러리)의 차이점 및 MSAL으로 마이그레이션하는 방법에 대해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/27/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 3af18eb09fd9906a0caaebda0b786795400467f3
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164933"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>MSAL (Microsoft 인증 라이브러리)로 응용 프로그램 마이그레이션

MSAL (Microsoft Authentication Library) 및 ADAL (Azure AD 인증 Library)은 azure ad 엔터티를 인증 하 고 Azure AD에서 토큰을 요청 하는 데 사용 됩니다. 지금까지 대부분의 개발자는 ADAL(Azure AD 인증 라이브러리)을 사용하는 토큰을 요청하여 Azure AD ID(회사 및 학교 계정)를 인증하기 위해 개발자 플랫폼(v1.0)에서 Azure AD를 사용했습니다. MSAL 사용:

- Microsoft id 플랫폼 끝점을 사용 하 여 광범위 한 Microsoft id 집합 (Azure AD id 및 Microsoft 계정, Azure AD B2C을 통해 소셜 및 로컬 계정)을 인증할 수 있습니다.
- 사용자는 최상의 single sign-on 환경을 이용할 수 있습니다.
- 응용 프로그램에서 증분 동의를 설정할 수 있으며 조건부 액세스를 지 원하는 것이 더 쉽습니다.
- 혁신을 통해 혜택을 누릴 수 있습니다.

**이제 Msal은 Microsoft id 플랫폼과 함께 사용 하기 위해 권장 되는 인증 라이브러리**입니다. ADAL에는 새로운 기능이 구현 되지 않습니다. 이러한 활동은 MSAL 향상에 중점을 두었습니다.

다음 문서에서는 MSAL과 ADAL 라이브러리 간의 차이점을 설명 하 고 MSAL으로 마이그레이션하는 데 도움이 되는 도움말을 설명 합니다.
- [MSAL.NET로 마이그레이션](msal-net-migration.md)
- [MSAL .js로 마이그레이션](msal-compare-msal-js-and-adal-js.md)
- [MSAL으로 마이그레이션합니다. 용](migrate-android-adal-msal.md)
- [MSAL. iOS/macOS로 마이그레이션](migrate-objc-adal-msal.md)
- [MSAL Python으로 마이그레이션](migrate-python-adal-msal.md)
- [Java 용 MSAL으로 마이그레이션](migrate-adal-msal-java.md)
- [MSAL.NET로 broker를 사용 하 여 Xamarin 앱 마이그레이션](msal-net-migration-ios-broker.md)
