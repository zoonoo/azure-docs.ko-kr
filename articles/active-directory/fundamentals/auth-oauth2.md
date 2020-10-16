---
title: Azure Active Directory로 OAUTH 2.0 인증
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
ms.openlocfilehash: ea22c4e5b363eaa3ecc2a736dfef714666310062
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114370"
---
# <a name="oauth-20-authentication-with-azure-active-directory"></a>Azure Active Directory로 OAuth 2.0 인증

OAuth 2.0은 권한 부여에 대 한 업계 프로토콜입니다. 이를 통해 사용자는 보호 된 리소스에 대 한 제한 된 액세스 권한을 부여할 수 있습니다. HTTP (하이퍼텍스트 전송 프로토콜)와 함께 작동 하도록 설계 된 OAuth는 리소스 소유자와 클라이언트의 역할을 분리 합니다. 클라이언트는 리소스 소유자가 제어 하 고 리소스 서버에 의해 호스트 되는 리소스에 대 한 액세스를 요청 합니다. 리소스 서버는 리소스 소유자의 승인으로 액세스 토큰을 발급 합니다. 클라이언트는 액세스 토큰을 사용 하 여 리소스 서버에서 호스팅하는 보호 된 리소스에 액세스 합니다. 

OAuth 2.0은 OIDC (Openid connect Connect)와 직접적으로 관련이 있습니다. OIDC는 OAuth 2.0을 기반으로 구축 된 인증 및 권한 부여 계층 이므로 이전에는 OAuth 1.0과 호환 되지 않습니다. Azure Active Directory (Azure AD)는 모든 OAuth 2.0 흐름을 지원 합니다. 

## <a name="use-when"></a>사용하는 경우:

리치 클라이언트 & 최신 앱 시나리오 및 RESTful Web API 액세스

![아키텍처 다이어그램](./media/authentication-patterns/oauth.png)

## <a name="components-of-system"></a>시스템의 구성 요소

* **User**: 웹 응용 프로그램 (앱)에서 서비스를 요청 합니다. 일반적으로 사용자는 데이터를 소유 하 고 클라이언트가 데이터 또는 리소스에 액세스할 수 있도록 하는 기능을 보유 하 고 있는 리소스 소유자입니다. 

* **웹 브라우저**: 사용자가 상호 작용 하는 웹 브라우저는 OAuth 클라이언트입니다. 

* **웹 앱**: 웹 앱 또는 리소스 서버는 리소스 또는 데이터가 있는 곳입니다. OAuth 클라이언트를 안전하게 인증하고 권한을 부여하는 권한 부여 서버를 신뢰합니다. 

* **AZURE ad**: azure Ad는 IdP (id 공급자) 라고도 하는 권한 부여 서버입니다. 사용자 정보, 해당 액세스 및 트러스트 관계를 사용 하 여 수행할 작업을 안전 하 게 처리 합니다. 리소스에 대 한 액세스 권한을 부여 하 고 취소 하는 토큰을 발급 해야 합니다.

## <a name="implement-oauth-20-with-azure-ad"></a>Azure AD를 사용 하 여 OAuth 2.0 구현

* [Azure AD와 애플리케이션 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 

* [Microsoft Id 플랫폼의 OAuth 2.0 및 Openid connect Connect 프로토콜](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols) 

* [응용 프로그램 종류 및 OAuth2](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types) 

 
