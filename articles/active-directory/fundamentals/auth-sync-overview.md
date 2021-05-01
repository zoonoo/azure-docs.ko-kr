---
title: Azure Active Directory 인증 및 동기화 프로토콜 개요
description: Azure AD를 레거시 인증 프로토콜 및 동기화 패턴과 통합하기 위한 아키텍처 지침
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
ms.openlocfilehash: ab63bc5bd2819a239741da525eebb2404a47bbf9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92441201"
---
# <a name="azure-active-directory-integrations-with-authentication-and-synchronization-protocols"></a>인증 및 동기화 프로토콜과 Azure Active Directory 통합

Microsoft Azure AD(Azure Active Directory)를 사용하면 여러 인증 및 동기화 프로토콜과 통합할 수 있습니다. 인증 통합을 통해 레거시 인증 방법을 사용하는 애플리케이션을 거의 또는 전혀 변경하지 않고 Azure AD와 해당 보안 및 관리 기능을 사용할 수 있습니다. 동기화 통합을 통해 사용자 및 그룹 데이터를 Azure AD에 동기화한 다음, 사용자 Azure AD 관리 기능을 사용할 수 있습니다. 일부 동기화 패턴은 자동화된 프로비전도 사용할 수 있습니다.

## <a name="legacy-authentication-protocols"></a>레거시 인증 프로토콜

다음 표에는 레거시 인증 프로토콜과 해당 기능과의 인증 Azure AD 통합이 나와 있습니다. 확인할 인증 프로토콜의 이름 선택

* 자세한 설명

* 사용하는 경우

* 아키텍처 다이어그램

* 시스템 구성 요소에 대한 설명

* 통합을 구현하는 방법에 대한 링크

 

| 인증 프로토콜| 인증| 권한 부여| Multi-Factor 인증| 조건부 액세스 |
| - |- | - | - | - |
| [헤더 기반 인증](auth-header-based.md)|![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png) |
| [LDAP 인증](auth-ldap.md)| ![확인 표시](./media/authentication-patterns/check.png)| | |  |
| [OAuth 2.0 인증](auth-oauth2.md)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png) |
| [OIDC 인증](auth-oidc.md)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png) |
| [암호 기반 SSO 인증](auth-password-based-sso.md )| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png) |
| [RADIUS 인증]( auth-radius.md)| ![확인 표시](./media/authentication-patterns/check.png)| | ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png) |
| [원격 데스크톱 게이트웨이 서비스](auth-remote-desktop-gateway.md)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png) |
| [SSH(보안 셸)](auth-ssh.md): |  ![확인 표시](./media/authentication-patterns/check.png)| | ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png) |
| [SAML 인증](auth-saml.md)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png) |
| [Windows 인증 - Kerberos 제한된 위임](auth-kcd.md)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png) |


 
## <a name="synchronization-patterns"></a>동기화 패턴

다음 표에서는 동기화 패턴 및 해당 기능과의 Azure AD 통합을 보여 줍니다. 표시할 패턴의 이름 선택

* 자세한 설명

* 사용하는 경우

* 아키텍처 다이어그램

* 시스템 구성 요소에 대한 설명

* 통합을 구현하는 방법에 대한 링크



| 동기화 패턴| 디렉터리 동기화| 사용자 프로비전 |
| - | - | - |
| [디렉터리 동기화](sync-directory.md)| ![확인 표시](./media/authentication-patterns/check.png)|  |
| [LDAP 동기화](sync-ldap.md)| ![확인 표시](./media/authentication-patterns/check.png)|  |
| [SCIM 동기화](sync-scim.md)| ![확인 표시](./media/authentication-patterns/check.png)| ![확인 표시](./media/authentication-patterns/check.png) |

