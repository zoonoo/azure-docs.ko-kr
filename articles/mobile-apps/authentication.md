---
title: Visual Studio 앱 센터 및 Azure 서비스를 사용하여 모바일 앱에 인증 추가
description: 사용자 인증을 설정하고 모바일 응용 프로그램이 소셜 계정, Azure Active Directory 및 사용자 지정 인증을 사용하여 인증할 수 있도록 지원하는 Visual Studio App Center와 같은 서비스에 대해 알아봅니다.
author: codemillmatt
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 820005bca008ea3bdb59659c815da3ec36beb0d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241046"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>모바일 앱에서 인증 추가 및 사용자 ID 관리

응용 프로그램 전체에서 사용자와 사용자의 동작을 볼 수 있으면 개발자가 사용자 자신에게 맞는 환경을 만들어 사용자의 참여를 유도할 수 있습니다. 조직 내 사용자를 위한 공동 작업 응용 프로그램을 빌드하는 응용 프로그램 개발자이든 다음 소셜 네트워크 플랫폼을 만들든 사용자를 인증하고 사용자 ID를 관리할 수 있는 방법이 필요합니다. ID 관리 서비스는 모바일 백 엔드 서비스의 가장 중요한 기능 중 하나입니다.

다음 서비스를 사용하여 모바일 앱에서 사용자 인증을 활성화합니다.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Auth는](/appcenter/auth/) 개발자가 사용자를 인증하고 사용자 ID를 관리하는 데 사용할 수 있는 클라우드 기반 ID 관리 서비스입니다. 앱 센터 Auth는 또한 비주얼 스튜디오 앱 센터의 다른 부분과 통합됩니다. 개발자는 사용자 ID를 사용하여 다른 서비스의 [사용자 데이터를 보고](/appcenter/data/index) 개별 장치 대신 사용자에게 푸시 알림을 [보낼](/appcenter/push/push-to-user#setting-user-identity)수도 있습니다. 

**주요 기능**
- Azure Active Directory B2C(Azure AD B2C)에 의해 구동됩니다. 
    - 엔터프라이즈 급행.
    - 고가용성.
    - 안전하고 글로벌 한 서비스.
- Auth0 및 Firebase와 같은 다른 인기 있는 ID 및 액세스 관리 공급자를 사용할 수 있는 옵션과 자신의 ID를 가져오십시오.
- Azure Active Directory 지원.
    - 기존 Azure AD 테넌트에 연결합니다. 
    - 회사 도메인에 대한 인증을 활성화합니다.
    - 중요한 데이터에 대한 액세스를 관리합니다.
- Visual Studio 앱 센터 SDK로 Microsoft 인증 라이브러리를 래핑하여 간단한 사용자 환경과 마법같은 SDK 환경을 제공합니다.
- iOS, 안드로이드, 자마린 및 리폰 네이티브에 대한 플랫폼 지원.

**참조**
- [비주얼 스튜디오 앱 센터에 가입](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs) 
- [앱 센터 인증 시작](/appcenter/auth/)

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[Azure AD B2C는](https://azure.microsoft.com/services/active-directory-b2c/) 개발자가 고객을 인증하는 데 사용할 수 있는 B2C(기업 간) ID 관리 서비스입니다. 이 화이트 라벨 서비스를 통해 개발자는 사용자가 웹, 데스크톱, 모바일 또는 단일 페이지 응용 프로그램과 안전하게 상호 작용하는 방법을 사용자 지정하고 제어할 수 있습니다. Azure AD B2C를 사용하여 사용자는 등록, 로그인, 암호 재설정 및 프로필 편집을 수행할 수 있습니다. Azure AD B2C는 OpenID Connect 및 OAuth 2.0 프로토콜의 형태를 구현합니다. 

**주요 기능**
- 선호하는 ID 공급자를 통해 고객을 안전하게 인증합니다.
- 고객 ID 및 액세스를 관리합니다.
- 페이스 북, GitHub, 구글, 링크드 인, 트위터, 위챗, 웨이 보와 같은 소셜 미디어에 대한 로그인 지원을 얻을.
- OpenID Connect 또는 SAML과 같은 업계 표준 프로토콜을 사용하여 사용자 계정에 연결하여 다양한 플랫폼에서 ID 관리를 가능하게 합니다.
- 브랜드 등록 및 로그인 환경을 제공합니다.
- CRM 데이터베이스, 마케팅 분석 도구 및 계정 확인 시스템과 쉽게 통합할 수 있습니다.
- 고객을 위한 로그인, 기본 설정 및 전환 데이터를 캡처합니다.

**참조**
- [Azure 포털](https://portal.azure.com/)
- [Azure AD B2C 설명서](/azure/active-directory-b2c/)
- [빠른 시작](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [샘플](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory는](https://azure.microsoft.com/services/active-directory/) Microsoft의 클라우드 기반 ID 및 액세스 관리 서비스로, 직원이 로그인하여 다음에 액세스할 수 있도록 도와줍니다.
- Microsoft Office 365, Azure 포털 및 서비스(SaaS) 응용 프로그램으로 서 다른 소프트웨어의 수천 과 같은 외부 리소스입니다.
- 조직에서 자체 개발한 클라우드 앱과 함께 회사 네트워크와 인트라넷의 앱 같은 내부 리소스.

**주요 기능**
- 필요한 애플리케이션에 사용자를 연결하여 원활하고 매우 안전한 액세스.
- 사용자, 위치, 장치, 데이터 및 응용 프로그램 컨텍스트를 기반으로 ID 및 액세스에 대한 포괄적인 ID 보호 및 향상된 보안.
- Office 365, Salesforce.com 및 Box와 같은 상용 및 사용자 지정 응용 프로그램을 위한 수천 개의 사전 통합 된 앱입니다.
- 대규모 액세스를 관리할 수 있는 기능.

**참조**
- [Azure 포털](https://portal.azure.com/)
- [Azure AD란?](/azure/active-directory/fundamentals/active-directory-whatis)
- [Azure Active Directory 시작](/azure/active-directory/fundamentals/active-directory-whatis)
- [빠른 시작](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)