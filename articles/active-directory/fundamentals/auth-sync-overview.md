---
title: Azure Active Directory 인증 및 동기화 프로토콜 개요
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
ms.openlocfilehash: 4d881dc3fe3e3caa1058cf97834735910b0de1d9
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114501"
---
# <a name="azure-active-directory-integrations-with-legacy-authentication-and-synchronization-protocols"></a>레거시 인증 및 동기화 프로토콜을 사용 하 Azure Active Directory 통합

Azure AD (Microsoft Azure Active Directory)를 사용 하면 여러 인증 및 동기화 프로토콜과 통합할 수 있습니다. 인증 통합을 사용 하면 레거시 인증 방법을 사용 하는 응용 프로그램을 거의 변경 하지 않고 Azure AD 및 해당 보안 및 관리 기능을 사용할 수 있습니다. 동기화 통합을 사용 하면 Azure AD에 사용자 및 그룹 데이터를 동기화 한 다음 사용자 Azure AD 관리 기능을 사용할 수 있습니다. 일부 동기화 패턴은 자동화 된 프로 비전도 사용 하도록 설정 합니다.

## <a name="authentication-patterns"></a>인증 패턴

다음 표에서는 인증 패턴 및 해당 기능을 보여 줍니다. 확인할 인증 패턴의 이름을 선택 합니다.

* 자세한 설명

* 사용하는 경우

* 아키텍처 다이어그램

* 시스템 구성 요소에 대 한 설명

* 통합을 구현 하는 방법에 대 한 링크

 

| 인증 패턴| 인증| 권한 부여| Multi-Factor 인증| 조건부 액세스 |
| - |- | - | - | - |
| [헤더 기반 인증](auth-header-based.md)|![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png) |
| [LDAP 인증](auth-ldap.md)| ![확인 표시](./media/authentication-patterns/check.png)| | |  |
| [OAuth 2.0 인증](auth-oauth2.md)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png) |
| [OIDC 인증](auth-oidc.md)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png) |
| [암호 기반 SSO 인증](auth-password-based-sso.md )| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png) |
| [RADIUS 인증]( auth-radius.md)| ![확인 표시](./media/authentication-patterns/check.png)| | ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png) |
| [원격 데스크톱 게이트웨이 서비스](auth-remote-desktop-gateway.md)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png) |
| [SAML 인증](auth-saml.md)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png) |
| [Windows 인증-Kerberos 제한 위임](auth-kcd.md)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png) |


 
## <a name="synchronization-patterns"></a>동기화 패턴

다음 표에서는 동기화 패턴 및 해당 기능을 보여 줍니다. 표시할 패턴의 이름을 선택 합니다.

* 자세한 설명

* 사용하는 경우

* 아키텍처 다이어그램

* 시스템 구성 요소에 대 한 설명

* 통합을 구현 하는 방법에 대 한 링크



| 동기화 패턴| 디렉터리 동기화| 사용자 프로비전 |
| - | - | - |
| [디렉터리 동기화](sync-directory.md)| ![확인 표시](./media/authentication-patterns/check.png)|  |
| [LDAP 동기화](sync-ldap.md)| ![확인 표시](./media/authentication-patterns/check.png)|  |
| [SCIM 동기화](sync-scim.md)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png) |

