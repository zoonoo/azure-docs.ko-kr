---
title: Azure Active Directory를 사용하여 OpenID Connect 인증
description: Azure Active Directory를 사용하여 OpenID Connect 인증을 달성하기 위한 아키텍처 참고 자료를 제공합니다.
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
ms.openlocfilehash: 0e5bf7e51de38d42e64f6737e687c5946a464160
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96168664"
---
# <a name="openid-connect-authentication-with-azure-active-directory"></a>Azure Active Directory를 사용하여 OpenID Connect 인증

OIDC(OpenID Connect)는 OAuth2 프로토콜(권한 부여에 사용됨)을 기반으로 하는 인증 프로토콜입니다. OIDC는 OAuth2의 표준화된 메시지 흐름을 사용하여 ID 서비스를 제공합니다. 

OIDC의 디자인 목표는 "간단한 일은 간단히 해결하고 어려운 일은 가능하게 하는 것"입니다. 개발자는 OIDC를 통해 암호 파일을 소유하고 관리할 필요 없이 웹 사이트 및 앱에서 사용자를 인증할 수 있습니다. 이는 앱 작성기에 현재 브라우저나 애플리케이션에 연결된 네이티브 앱을 사용하고 있는 사람의 ID를 확인하는 안전한 방법을 제공합니다.

사용자의 인증은 사용자의 세션 또는 자격 증명을 확인하는 ID 공급자에서 수행되어야 합니다. 이를 위해 신뢰할 수 있는 에이전트가 필요합니다. 일반적으로 네이티브 앱은 이러한 용도로 시스템 브라우저를 시작합니다. 포함된 보기는 앱이 사용자 암호를 스누핑하는 것을 방지할 수단이 없으므로 신뢰할 수 없는 것으로 간주됩니다. 

인증 외에도 사용자에게 동의를 요청하는 메시지가 표시될 수 있습니다. 동의는 애플리케이션이 보호된 리소스에 액세스할 수 있도록 하는 사용자의 명시적 권한입니다. 동의는 리소스에 한 번만 제공하면 되므로 인증과는 다릅니다. 사용자 또는 관리자가 권한 부여를 수동으로 해지할 때까지 승인이 유효합니다. 

## <a name="use-when"></a>적용 가능한 상황

사용자 동의 및 웹 로그인이 필요합니다.

![아키텍처 다이어그램](./media/authentication-patterns/oidc-auth.png)

## <a name="components-of-system"></a>시스템의 구성 요소

* **사용자**: 애플리케이션에서 서비스를 요청합니다.

* **신뢰할 수 있는 에이전트**: 사용자가 상호 작용하는 구성 요소입니다. 이 신뢰할 수 있는 에이전트는 일반적으로 웹 브라우저입니다.

* **애플리케이션**: 애플리케이션 또는 리소스 서버는 리소스 또는 데이터가 있는 곳입니다. ID 공급자를 신뢰하여 신뢰할 수 있는 에이전트를 안전하게 인증하고 권한을 부여합니다. 

* **Azure AD**: OIDC 공급자는 ID 공급자라고도 하며, 사용자 정보, 해당 액세스 및 흐름의 요소 간 신뢰 관계와 관련된 모든 사항을 안전하게 관리합니다. 사용자 ID를 인증하고, 리소스에 대한 액세스 권한을 부여 및 취소하고, 토큰을 발급합니다. 

## <a name="implement-oidc-with-azure-ad"></a>Azure AD를 사용하여 OIDC 구현

* [Azure AD와 애플리케이션 통합](../saas-apps/tutorial-list.md) 

* [ Microsoft ID 플랫폼의 OAuth 2.0 및 OpenID Connect 프로토콜](../develop/active-directory-v2-protocols.md) 

* [Microsoft ID 플랫폼 및 OpenID Connect 프로토콜](../develop/v2-protocols-oidc.md) 

* [Azure Active Directory B2C에서 OpenID Connect로 웹 로그인](../../active-directory-b2c/openid-connect.md) 

* [OpenID Connect 및 Azure AD를 사용하여 애플리케이션 보호](/learn/modules/secure-app-with-oidc-and-azure-ad/) 

