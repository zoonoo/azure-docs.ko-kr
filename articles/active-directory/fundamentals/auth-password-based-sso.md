---
title: Azure Active Directory를 사용 하 여 암호 기반 인증
description: 이 인증 패턴을 달성 하는 데 대 한 아키텍처 지침
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
ms.openlocfilehash: f05bf8f5f7ec4907c2cd61ff48e3438dfa1e097a
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114358"
---
# <a name="password-based-authentication-with-azure-active-directory"></a>Azure Active Directory를 사용 하 여 암호 기반 인증

암호 기반 SSO (Single Sign-On)는 응용 프로그램에 대 한 기존 인증 프로세스를 사용 합니다. 암호 기반 SSO를 사용 하도록 설정 하는 경우 Azure AD (Azure Active Directory)는 디렉터리에 사용자 자격 증명을 수집, 암호화 및 안전 하 게 저장 합니다. Azure AD는 사용자가 로그인을 시도할 때 응용 프로그램에 사용자 이름과 암호를 제공 합니다.

응용 프로그램에서 액세스 토큰 및 헤더 대신 사용자 이름 및 암호를 사용 하 여 인증 하는 경우 암호 기반 SSO를 선택 합니다. 암호 기반 SSO는 HTML 기반 로그인 페이지가 있는 모든 클라우드 기반 응용 프로그램을 지원 합니다. 

## <a name="use-when"></a>적용 가능한 상황

사전 인증을 사용 하 여 보호 하 고 웹 앱에 대 한 암호 보관을 통해 SSO를 제공 해야 합니다.

![아키텍처 다이어그램](./media/authentication-patterns/password-based-sso-auth.png)


## <a name="components-of-system"></a>시스템의 구성 요소

* **사용자**: 내 앱에서 또는 사이트를 직접 방문 하 여 원본 기반 응용 프로그램에 액세스 합니다. 

* **웹 브라우저**: 응용 프로그램의 외부 URL에 액세스 하기 위해 사용자가 상호 작용 하는 구성 요소입니다. 사용자는 MyApps 확장을 통해 양식 기반 응용 프로그램에 액세스 합니다. 

* **Myapps 확장**: 구성 된 암호 기반 SSO 응용 프로그램을 식별 하 고 로그인 양식에 자격 증명을 제공 합니다. MyApps 확장은 웹 브라우저에 설치 됩니다. 

* **AZURE AD**: 사용자를 인증 합니다.

## <a name="implement-password-based-sso-with-azure-ad"></a>Azure AD를 사용 하 여 암호 기반 SSO 구현

* [암호 기반 SSO 란?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) 

* [클라우드 응용 프로그램에 대 한 암호 기반 SSO 구성 ](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications)

* [응용 프로그램 프록시를 사용 하 여 온-프레미스 응용 프로그램에 대 한 암호 기반 SSO 구성](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-password-vaulting)

 
