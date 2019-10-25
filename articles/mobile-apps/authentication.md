---
title: Visual Studio App Center 및 Azure 서비스를 사용 하 여 모바일 앱에 인증 추가
description: 사용자 인증을 설정 하 고 모바일 응용 프로그램이 소셜 계정, Azure Active Directory 및 사용자 지정 인증을 사용 하 여 인증 하도록 설정 하는 데 도움이 되는 App Center와 같은 서비스에 대해 알아봅니다.
author: elamalani
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 8885f6cd91179f4dee6456277d47581f68c21723
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795758"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>모바일 앱에서 인증 추가 및 사용자 id 관리

응용 프로그램에 사용자를 표시 하 고 해당 동작을 사용 하면 개발자가 사용자에 게 적합 한 환경을 만들어 사용자의 참여를 향상할 수 있습니다. 조직 내 사용자를 위해 공동 작업 응용 프로그램을 빌드하는 응용 프로그램 개발자 든, 다음 소셜 네트워크 플랫폼을 만드는 경우 사용자를 인증 하 고 사용자 id를 관리 하는 방법이 필요 합니다. Id 관리 서비스를 사용 하는 것은 모바일 백 엔드 서비스의 가장 중요 한 기능 중 하나입니다.

다음 서비스를 사용 하 여 모바일 앱에서 사용자 인증을 사용 하도록 설정 합니다.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Auth](/appcenter/auth/) 는 개발자가 사용자를 인증 하 고 사용자 id를 관리할 수 있는 클라우드 기반 id 관리 서비스입니다. App Center Auth는 또한 App Center의 다른 부분과 통합 되어 개발자가 사용자 id를 활용 하 여 다른 서비스의 [사용자 데이터를 볼](/appcenter/data/index) 수 있으며, [개별 장치 대신 사용자에 게 푸시 알림을 보낼](/appcenter/push/push-to-user#app-center-auth-set-identity)수 있습니다. 

**주요 기능**
- **Azure Active Directory B2C (Azure AD B2C)** 
    - 엔터프라이즈급.
    - 고가용성.
    - 보안 및 글로벌 서비스.
- 본인 **의 id** 를 가져오고 좋아하게 되었습니다 및 Firebase와 같은 기타 인기 있는 id 및 액세스 관리 공급자를 사용 합니다.
- **AAD 지원** 
    - 기존 AAD 테 넌 트를 연결 합니다. 
    - 회사 도메인에 대해 인증을 사용 하도록 설정 합니다.
    - 중요 한 데이터에 대 한 액세스를 관리 합니다.
- App Center SDK로 MSAL 라이브러리를 래핑하여 **간단한 사용자 환경** 및 마법 sdk 환경
- **플랫폼 지원** -IOS, Android, Xamarin, 네이티브에 반응 합니다.

**참조**
- [App Center 등록](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs) 
- [App Center Auth 시작](/appcenter/auth/)

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/) 는 개발자가 고객을 인증할 수 있도록 하는 b2b id 관리 서비스입니다. 이 흰색 레이블 서비스를 사용 하면 개발자가 웹, 데스크톱, 모바일 또는 단일 페이지 응용 프로그램과 안전 하 게 상호 작용 하는 방법을 사용자 지정 하 고 제어할 수 있습니다. Azure AD B2C를 사용하여 사용자는 등록, 로그인, 암호 재설정 및 프로필 편집을 수행할 수 있습니다. Azure AD B2C는 OpenID Connect 및 OAuth 2.0 프로토콜의 형태를 구현합니다. 

**주요 기능**
- 선호 하는 id 공급자를 사용 하 여 고객을 안전 하 게 인증 합니다.
- **고객 id 및 액세스 관리**.
- Facebook, GitHub, Google, LinkedIn, Twitter, WeChat, Weibo와 같은 소셜 로그인이 지원 됩니다.
- Openid connect Connect 또는 SAML 같은 **업계 표준 프로토콜** 을 사용 하 여 다양 한 플랫폼에서 id를 관리할 수 있도록 사용자 계정에 연결 합니다.
- 브랜드 등록 및 로그인 환경을 제공 합니다.
- CRM 데이터베이스, 마케팅 분석 도구 및 계정 확인 시스템과 쉽게 통합 하세요.
- 고객에 대 한 로그인, 기본 설정 및 변환 데이터를 캡처합니다.

**참조**
- [Azure 포털](https://portal.azure.com/)
- [Azure AD B2C 설명서](/azure/active-directory-b2c/)
- [빠른 시작](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [샘플](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory (AZURE AD)](https://azure.microsoft.com/services/active-directory/) 는 Microsoft의 클라우드 기반 id 및 액세스 관리 서비스로, 직원 로그인 및 액세스에 도움이 됩니다.
- Microsoft Office 365, Azure Portal, 수천 개의 기타 SaaS 애플리케이션을 비롯한 외부 리소스.
- 조직에서 자체 개발한 클라우드 앱과 함께 회사 네트워크와 인트라넷의 앱 같은 내부 리소스.

**주요 기능**
- 사용자를 필요한 응용 프로그램에 연결 하 여 **원활 하 고 안전** 하 게 액세스할 수 있습니다.
- 사용자, 위치, 장치, 데이터 및 응용 프로그램 컨텍스트를 기반으로 id 및 액세스를 위한 **포괄적인 id 보호** 및 보안 강화.
- 상용 및 사용자 지정 응용 프로그램 (예: Office 365, Salesforce.com 및 Box)이 모두 **사전 통합 된 앱입니다** .
- **규모에 맞게 액세스를 관리**합니다.

**참조**
- [Azure 포털](https://portal.azure.com/)
- [Azure AD란?](/azure/active-directory/fundamentals/active-directory-whatis)
- [Azure Active Directory 시작](/azure/active-directory/fundamentals/active-directory-whatis)
- [빠른 시작](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)