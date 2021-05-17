---
title: Azure Active Directory를 사용한 LDAP 인증
description: Azure Active Directory를 사용한 LDAP 인증에 대한 아키텍처 지침입니다.
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
ms.openlocfilehash: f209d394e1a0c2c4ddde9cbf8df2704647e2822a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96168715"
---
# <a name="ldap-authentication-with-azure-active-directory"></a>Azure Active Directory를 사용한 LDAP 인증

LDAP(Lightweight Directory Access Protocol)는 다양한 디렉터리 서비스를 사용하기 위한 애플리케이션 프로토콜입니다. Active Directory와 같은 디렉터리 서비스는 [사용자 및 계정 정보를 저장](https://www.dnsstuff.com/active-directory-service-accounts)하고 암호 등의 보안 정보를 저장합니다. 그러면 서비스에서 네트워크의 다른 디바이스와 정보를 공유할 수 있습니다. 메일, CRM(고객 관계 관리자) 및 HR(인적 자원) 소프트웨어와 같은 엔터프라이즈 애플리케이션은 LDAP를 사용하여 정보를 인증 및 액세스하고 찾을 수 있습니다. 

Azure AD(Azure Active Directory)는 AD DS(Azure AD Domain Services)를 통해 이 패턴을 지원합니다. 이를 통해 클라우드 우선 전략을 채택하는 조직이 온-프레미스 LDAP 리소스를 클라우드로 이동하여 환경을 현대화할 수 있습니다. 즉각적인 혜택은 다음과 같습니다. 

* Azure AD와 통합 사용자 및 그룹의 추가 또는 해당 개체에 대한 특성 변경 내용은 Azure AD 테넌트에서 AD DS로 자동 동기화됩니다. 온-프레미스 Active Directory의 개체에 대한 변경 내용은 Azure AD에 동기화된 후 AD DS로 동기화됩니다.

* 작업의 간소화 온-프레미스 인프라를 수동으로 유지하고 패치할 필요가 줄어듭니다. 

* 신뢰할 수 있습니다. 관리되는 고가용성 서비스를 이용할 수 있습니다. 

## <a name="use-when"></a>적용 가능한 상황

애플리케이션 또는 서비스에서 LDAP 인증을 사용해야 하는 경우

![아키텍처 다이어그램](./media/authentication-patterns/ldap-auth.png)

## <a name="components-of-system"></a>시스템의 구성 요소

* **사용자**: 브라우저를 통해 LDAP 종속 애플리케이션에 액세스합니다.

* **웹 브라우저**: 애플리케이션의 외부 URL에 액세스하기 위해 사용자가 상호 작용하는 구성 요소입니다.

* **가상 네트워크**: 레거시 애플리케이션이 LDAP 서비스를 사용할 수 있는 Azure의 개인 네트워크입니다. 

* **레거시 애플리케이션**: Azure의 가상 네트워크에 LDAP를 배포해야 하거나 네트워킹 경로를 통해 AD DS 인스턴스 IP를 표시하는 애플리케이션 또는 서버 워크로드입니다. 

* **Azure AD**: Azure AD Connect을 통해 조직의 온-프레미스 디렉터리에서 ID 정보를 동기화합니다.

* **AD DS(Azure AD Domain Services)** : Azure AD에서 단방향 동기화를 수행하여 한 곳의 사용자, 그룹 및 자격 증명 세트에 대한 액세스를 제공합니다. AD DS 인스턴스는 가상 네트워크에 할당됩니다. 그러면 가상 네트워크에 연결하는 Azure의 애플리케이션, 서비스 및 VM에서 LDAP, 도메인 조인, 그룹 정책, Kerberos 및 NTLM 인증과 같은 일반적인 AD DS 기능을 사용할 수 있습니다.
   > [!NOTE]
   >  조직에서 암호 해시를 동기화할 수 없거나 사용자가 스마트 카드를 사용하여 로그인하는 환경에서는 AD DS의 리소스 포리스트를 사용하는 것이 좋습니다. 

* **Azure AD Connect**: 온-프레미스 ID 정보를 Microsoft Azure AD에 동기화하기 위한 도구입니다. 배포 마법사 및 안내 경험을 통해 Active Directory에서 Azure AD로 동기화 및 로그인을 포함하여 연결에 필요한 필수 구성 요소 및 구성 요소를 구성할 수 있습니다. 

* **Active Directory**: [온-프레미스 ID 정보(예: 사용자 및 계정 정보](https://www.dnsstuff.com/active-directory-service-accounts)) 및 암호와 같은 보안 정보를 저장하는 디렉터리 서비스입니다.

## <a name="implement-ldap-authentication-with-azure-ad"></a>Azure AD를 사용한LDAP 인증 구현

* [Azure AD DS 인스턴스를 만들고 구성하는 방법](../../active-directory-domain-services/tutorial-create-instance.md) 

* [Azure AD DS 인스턴스에 대 한 가상 네트워킹 구성](../../active-directory-domain-services/tutorial-configure-networking.md) 

* [Azure AD DS 관리되는 도메인에 대한 보안 LDAP 구성](../../active-directory-domain-services/tutorial-configure-ldaps.md) 

* [Azure AD DS에서 온-프레미스 도메인에 대한 아웃바운드 포리스트 트러스트 만들기](../../active-directory-domain-services/tutorial-create-forest-trust.md)

