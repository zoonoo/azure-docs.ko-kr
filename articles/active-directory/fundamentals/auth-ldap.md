---
title: Azure Active Directory LDAP 인증
description: Azure Active Directory를 사용 하 여 LDAP 인증을 구현 하는 아키텍처 지침입니다.
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
ms.openlocfilehash: d5314758acecae2a9d68f2405fc1c3d2196950b4
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94577059"
---
# <a name="ldap-authentication-with-azure-active-directory"></a>Azure Active Directory LDAP 인증

LDAP (Lightweight Directory Access Protocol)는 다양 한 디렉터리 서비스를 사용 하기 위한 응용 프로그램 프로토콜입니다. Active Directory와 같은 디렉터리 서비스는 [사용자 및 계정 정보를 저장](https://www.dnsstuff.com/active-directory-service-accounts)하 고 암호와 같은 보안 정보를 저장 합니다. 그러면 서비스에서 네트워크의 다른 장치와 정보를 공유할 수 있습니다. 전자 메일, CRMs (고객 관계 관리자) 및 HR (인적 자원) 소프트웨어와 같은 엔터프라이즈 응용 프로그램은 LDAP를 사용 하 여 정보를 인증, 액세스 및 찾을 수 있습니다. 

Azure Active Directory (Azure AD)는 Azure AD Domain Services (AD DS)를 통해이 패턴을 지원 합니다. 클라우드 우선 전략을 채택 하는 조직이 온-프레미스 LDAP 리소스를 클라우드로 이동 하 여 환경을 현대화 수 있습니다. 즉각적인 혜택은 다음과 같습니다. 

* Azure AD와 통합 됩니다. 사용자 및 그룹의 추가 또는 해당 개체에 대 한 특성 변경 내용은 Azure AD 테 넌 트에서 AD DS로 자동 동기화 됩니다. 온-프레미스 Active Directory의 개체에 대 한 변경 내용은 Azure AD에 동기화 된 다음 AD DS 됩니다.

* 작업 간소화. 온-프레미스 인프라를 수동으로 유지 하 고 패치할 필요성이 줄어듭니다. 

* 신뢰할 수 있습니다. 관리 되 고 항상 사용 가능한 서비스를 얻게 됩니다. 

## <a name="use-when"></a>적용 가능한 상황

응용 프로그램 또는 서비스에서 LDAP 인증을 사용 해야 하는 경우

![아키텍처 다이어그램](./media/authentication-patterns/ldap-auth.png)

## <a name="components-of-system"></a>시스템의 구성 요소

* **사용자** : 브라우저를 통해 LDAP 종속 응용 프로그램에 액세스 합니다.

* **웹 브라우저** : 응용 프로그램의 외부 URL에 액세스 하기 위해 사용자가 상호 작용 하는 인터페이스입니다.

* **Virtual Network** : 레거시 응용 프로그램이 LDAP 서비스를 사용할 수 있는 Azure의 개인 네트워크입니다. 

* **레거시 응용 프로그램** : Azure의 가상 네트워크에 LDAP를 배포 해야 하거나 네트워킹 경로를 통해 AD DS 인스턴스 ip를 표시 하는 응용 프로그램 또는 서버 작업입니다. 

* **AZURE AD** : Azure AD Connect을 통해 조직의 온-프레미스 디렉터리에서 id 정보를 동기화 합니다.

* **Azure AD Domain Services (AD DS)** : Azure AD에서 단방향 동기화를 수행 하 여 중앙의 사용자, 그룹 및 자격 증명에 대 한 액세스를 제공 합니다. AD DS 인스턴스는 가상 네트워크에 할당 됩니다. AD DS에 할당 된 가상 네트워크에 연결 되는 Azure의 응용 프로그램, 서비스 및 Vm은 LDAP, 도메인 가입, 그룹 정책, Kerberos 및 NTLM 인증과 같은 일반적인 AD DS 기능을 사용할 수 있습니다.
   > [!NOTE]
   >  조직에서 암호 해시를 동기화 할 수 없거나 사용자가 스마트 카드를 사용 하 여 로그인 하는 환경에서는 AD DS 리소스 포리스트를 사용 하는 것이 좋습니다. 

* **Azure AD Connect** : 온-프레미스 id 정보를 Microsoft Azure AD 동기화 하기 위한 도구입니다. 배포 마법사 및 단계별 환경을 통해 Active Directory에서 Azure AD로 동기화 및 로그인을 포함 하 여 연결에 필요한 필수 구성 요소 및 구성 요소를 구성할 수 있습니다. 

* **Active Directory** : [온-프레미스 id 정보 (예: 사용자 및 계정 정보](https://www.dnsstuff.com/active-directory-service-accounts)) 및 암호와 같은 보안 정보를 저장 하는 디렉터리 서비스입니다.

## <a name="implement-ldap-authentication-with-azure-ad"></a>Azure AD를 사용 하 여 LDAP 인증 구현

* [Azure AD DS 인스턴스 만들기 및 구성](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance) 

* [Azure AD DS 인스턴스에 대 한 가상 네트워킹 구성](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-configure-networking) 

* [Azure AD DS 관리 되는 도메인에 대 한 보안 LDAP 구성](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-configure-ldaps) 

* [Azure AD DS에서 온-프레미스 도메인에 대 한 아웃 바운드 포리스트 트러스트 만들기](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-forest-trust)

 
