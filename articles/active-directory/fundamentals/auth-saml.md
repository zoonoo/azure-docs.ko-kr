---
title: Azure Active Directory 사용 하는 SAML 인증
description: Azure Active Directory를 사용 하 여 SAML 인증을 얻기 위한 아키텍처 지침
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ab14413de1f999747e5b3fb58b505e0a9258a55
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92441218"
---
# <a name="saml-authentication-with-azure-active-directory"></a>Azure Active Directory 사용 하는 SAML 인증

SAML (Security Assertion Markup Language)은 id 공급자와 서비스 공급자 간에 인증 및 권한 부여 데이터를 교환 하기 위한 개방형 표준입니다. SAML은 보안 어설션을 위한 XML 기반 태그 언어로, 서비스 공급자가 액세스 제어 결정을 내리는 데 사용 하는 문입니다. 

SAML 사양은 다음과 같은 세 가지 역할을 정의 합니다.

* 보안 주체 (일반적으로 사용자)
* IdP (id 공급자)
* SP (서비스 공급자)


## <a name="use-when"></a>적용 가능한 상황

엔터프라이즈 SAML 응용 프로그램에 대 한 SSO (Single Sign-On) 환경을 제공 해야 합니다.

SAML 주소를 사용 하는 가장 중요 한 사용 사례 중 하나는 SSO 이며 특히 보안 도메인 간에 SSO를 확장 하는 경우에는 다른 사용 사례 (프로필 이라고 함)도 있습니다. 

![SAML 아키텍처 다이어그램](./media/authentication-patterns/saml-auth.png)

## <a name="components-of-system"></a>시스템의 구성 요소

* **User**: 응용 프로그램에서 서비스를 요청 합니다.

* **웹 브라우저**: 사용자가 상호 작용 하는 구성 요소입니다.

* **웹 앱**: SAML을 지원 하 고 IdP로 Azure AD를 사용 하는 엔터프라이즈 응용 프로그램입니다.

* **Token**: IdP (사용자)에 대해 수행 된 클레임 집합을 전달 하는 saml 어설션 (saml 토큰이 라고도 함)입니다. 여기에는 인증 정보, 특성 및 권한 부여 결정 문이 포함 됩니다.

* **AZURE AD**: SAML 앱에 대 한 SSO 및 multi-factor authentication을 제공 하는 엔터프라이즈 클라우드 IdP. 인증 서비스를 신뢰 응용 프로그램에 제공 하는 동시에 사용자에 대 한 id 정보를 동기화 하 고 유지 관리 하 고 관리 합니다. 

## <a name="implement-saml-authentication-with-azure-ad"></a>Azure AD를 사용 하 여 SAML 인증 구현

* [Azure Active Directory를 사용 하 여 SaaS 응용 프로그램을 통합 하기 위한 자습서](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 

* [비 갤러리 응용 프로그램에 대 한 SAML 기반 Single Sign-On 구성](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app) 

* [Azure AD에서 SAML 프로토콜을 사용하는 방법](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference)
