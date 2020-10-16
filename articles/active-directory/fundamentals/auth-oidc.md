---
title: Azure Active Directory를 사용 하 여 Openid connect 연결 인증
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
ms.openlocfilehash: c6f2ad3b5e86eebfc2d6f1f42f8a2ab0520144b5
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114369"
---
# <a name="openid-connect-authentication-with-azure-active-directory"></a>Azure Active Directory를 사용 하 여 Openid connect 연결 인증

OIDC (Openid connect Connect)는 OAuth2 프로토콜 (권한 부여에 사용 됨)을 기반으로 하는 인증 프로토콜입니다. OIDC는 OAuth2의 표준화 된 메시지 흐름을 사용 하 여 id 서비스를 제공 합니다. 

OIDC의 디자인 목표는 "간단 하 고 복잡 한 작업을 수행 하는 것"입니다. OIDC를 통해 개발자는 암호 파일을 소유 하 고 관리할 필요 없이 웹 사이트 및 앱에서 사용자를 인증할 수 있습니다. 이는 응용 프로그램에 연결 된 응용 프로그램 또는 네이티브 앱을 현재 사용 중인 사용자의 id를 확인 하는 안전한 방법을 앱 작성기에 제공 합니다.

사용자의 인증이 사용자의 세션 또는 자격 증명을 확인 하는 id 공급자에서 수행 되어야 합니다. 이렇게 하려면 신뢰할 수 있는 에이전트가 필요 합니다. 일반적으로 네이티브 앱은 이러한 용도로 시스템 브라우저를 시작 합니다. 앱이 사용자 암호에 대 한 스누핑를 방지할 수 있는 것은 없으므로 포함 된 보기는 신뢰할 수 없는 것으로 간주 됩니다. 

인증 외에도 사용자에 게 동의를 요청 하는 메시지가 표시 될 수 있습니다. 동의는 응용 프로그램에서 보호 된 리소스에 액세스할 수 있도록 하는 사용자의 명시적 사용 권한입니다. 동의는 리소스에 대해 한 번만 제공 해야 하므로 인증과는 다릅니다. 사용자 또는 관리자가 허가를 수동으로 해지할 때까지 승인이 유효 하 게 유지 됩니다. 

## <a name="use-when"></a>적용 가능한 상황

사용자 동의 및 웹 로그인이 필요 합니다.

![아키텍처 다이어그램](./media/authentication-patterns/oidc-auth.png)

## <a name="components-of-system"></a>시스템의 구성 요소

* **User**: 응용 프로그램에서 서비스를 요청 합니다.

* **신뢰할 수 있는 에이전트**: 사용자가 상호 작용 하는 구성 요소입니다. 이 신뢰할 수 있는 에이전트는 일반적으로 웹 브라우저입니다.

* **응용 프로그램**: 응용 프로그램 또는 리소스 서버는 리소스 또는 데이터가 있는 곳입니다. Id 공급자를 신뢰 하 여 신뢰할 수 있는 에이전트를 안전 하 게 인증 하 고 권한을 부여 합니다. 

* **AZURE AD**: id 공급자 라고도 하는 oidc 공급자는 사용자 정보, 해당 액세스 권한 및 흐름의 파티 간 트러스트 관계를 사용 하 여 수행할 모든 작업을 안전 하 게 관리 합니다. 사용자의 id를 인증 하 고, 리소스에 대 한 액세스 권한을 부여 및 취소 하 고, 토큰을 발급 합니다. 

## <a name="implement-oidc-with-azure-ad"></a>Azure AD를 사용 하 여 OIDC 구현

* [Azure AD와 애플리케이션 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 

* [Microsoft Id 플랫폼의 OAuth 2.0 및 Openid connect Connect 프로토콜](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols) 

* [Microsoft ID 플랫폼 및 OpenID Connect 프로토콜](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc) 

* [Azure Active Directory B2C에서 OpenID Connect로 웹 로그인](https://docs.microsoft.com/azure/active-directory-b2c/openid-connect) 

* [OpenID Connect 및 Azure AD를 사용하여 애플리케이션 보호](https://docs.microsoft.com/learn/modules/secure-app-with-oidc-and-azure-ad/) 

 
