---
title: Azure Active Directory를 사용한 암호 기반 인증
description: Azure Active Directory를 사용한 암호 기반 인증에 대한 아키텍처 지침입니다.
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
ms.openlocfilehash: 737a073a0360842d2ddd8010970e8a3461193742
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111966044"
---
# <a name="password-based-authentication-with-azure-active-directory"></a>Azure Active Directory를 사용한 암호 기반 인증

암호 기반 SSO(Single Sign-On)는 애플리케이션에 대한 기존 인증 프로세스를 사용합니다. 암호 기반 SSO를 사용하도록 설정하는 경우 Azure AD(Azure Active Directory)는 사용자 자격 증명을 수집하고 암호화하여 디렉터리에 안전하게 저장합니다. Azure AD는 사용자가 로그인을 시도할 때 애플리케이션에 사용자 이름과 암호를 제공합니다.

애플리케이션이 액세스 토큰 및 헤더 대신 사용자 이름과 암호를 사용하여 인증할 때 암호 기반 SSO를 선택합니다. 암호 기반 SSO는 HTML 기반 로그인 페이지가 있는 모든 클라우드 기반 애플리케이션을 지원합니다. 

## <a name="use-when"></a>적용 가능한 상황

사전 인증을 사용하여 보호하고 웹앱에 대한 암호 보관을 통해 SSO를 제공해야 합니다.

![아키텍처 다이어그램](./media/authentication-patterns/password-based-sso-auth.png)


## <a name="components-of-system"></a>시스템의 구성 요소

* **사용자**: 내 앱에서 또는 사이트를 직접 방문하여 양식 기반 애플리케이션에 액세스합니다. 

* **웹 브라우저**: 애플리케이션의 외부 URL에 액세스하기 위해 사용자가 상호 작용하는 구성 요소입니다. 사용자는 MyApps 확장을 통해 양식 기반 애플리케이션에 액세스합니다. 

* **MyApps 확장**: 구성된 암호 기반 SSO 애플리케이션을 식별하고 로그인 양식에 자격 증명을 제공합니다. MyApps 확장은 웹 브라우저에 설치됩니다. 

* **Azure AD**: 사용자를 인증합니다.

## <a name="implement-password-based-sso-with-azure-ad"></a>Azure AD를 사용하여 암호 기반 SSO 구현

* [암호 기반 SSO란?](../manage-apps/what-is-single-sign-on.md) 

* [클라우드 애플리케이션용 암호 기반 SSO 구성](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)

* [애플리케이션 프록시를 사용하여 온-프레미스 애플리케이션용 암호 기반 SSO 구성](../app-proxy/application-proxy-configure-single-sign-on-password-vaulting.md)