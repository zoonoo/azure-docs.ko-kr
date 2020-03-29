---
title: 마이크로소프트 인증 라이브러리(MSAL)로 마이그레이션
titleSuffix: Microsoft identity platform
description: MSAL(Microsoft 인증 라이브러리)과 Azure AD 인증 라이브러리(ADAL)의 차이점과 MSAL로 마이그레이션하는 방법에 대해 알아봅니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164933"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>응용 프로그램을 MSAL(Microsoft 인증 라이브러리)으로 마이그레이션합니다.

MSAL(Microsoft 인증 라이브러리)과 Azure AD 인증 라이브러리(ADAL)는 모두 Azure AD 엔터티를 인증하고 Azure AD에서 토큰을 요청하는 데 사용됩니다. 지금까지 대부분의 개발자는 ADAL(Azure AD 인증 라이브러리)을 사용하는 토큰을 요청하여 Azure AD ID(회사 및 학교 계정)를 인증하기 위해 개발자 플랫폼(v1.0)에서 Azure AD를 사용했습니다. MSAL 사용:

- Microsoft ID 플랫폼 끝점을 사용할 때 광범위한 Microsoft ID 집합(Azure AD ID 및 Microsoft 계정 및 Azure AD B2C를 통한 소셜 및 로컬 계정)을 인증할 수 있습니다.
- 사용자는 최상의 Single-Sign-On 환경을 제공합니다.
- 응용 프로그램에서 증분 동의를 활성화할 수 있으며 조건부 액세스를 지원하는 것이 더 쉽습니다.
- 혁신의 이점을 누릴 수 있습니다.

**MSAL은 이제 Microsoft ID 플랫폼과 함께 사용할 수 있는 권장 인증 라이브러리입니다.** ADAL에는 새로운 기능이 구현되지 않습니다. 이러한 노력은 MSAL 개선에 초점을 맞추고 있습니다.

다음 문서에서는 MSAL 및 ADAL 라이브러리간의 차이점을 설명하고 MSAL로 마이그레이션하는 데 도움이 됩니다.
- [MSAL.NET으로 마이그레이션](msal-net-migration.md)
- [MSAL.js로 마이그레이션](msal-compare-msal-js-and-adal-js.md)
- [MSAL.Android로 마이그레이션](migrate-android-adal-msal.md)
- [MSAL.iOS / 맥OS로 마이그레이션](migrate-objc-adal-msal.md)
- [MSAL Python으로 마이그레이션](migrate-python-adal-msal.md)
- [Java용 MSAL로 마이그레이션](migrate-adal-msal-java.md)
- [브로커를 사용하여 MSAL.NET으로 Xamarin 앱 마이그레이션](msal-net-migration-ios-broker.md)
