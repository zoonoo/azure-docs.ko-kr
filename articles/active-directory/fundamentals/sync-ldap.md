---
title: Azure Active Directory와의 LDAP 동기화
description: Azure Active Directory와의 LDAP 동기화를 달성 하는 아키텍처 지침입니다.
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
ms.openlocfilehash: e617d7ccc14e65c18eb86877b1c7fb1aeef74cd0
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578895"
---
# <a name="ldap-synchronization-with-azure-active-directory"></a>Azure Active Directory와의 LDAP 동기화

LDAP (Lightweight Directory Access Protocol)는 TCP/IP 스택에서 실행 되는 디렉터리 서비스 프로토콜입니다. 인터넷 디렉터리에 연결 하 고, 검색 하 고, 수정 하는 데 사용 되는 메커니즘을 제공 합니다. LDAP 디렉터리 서비스는 클라이언트-서버 모델을 기반으로 하며 함수는 기존 디렉터리에 대 한 액세스를 사용 하도록 설정 합니다. 많은 회사에서 중요 한 비즈니스 앱에 대 한 사용자 및 그룹을 저장 하기 위해 온-프레미스 LDAP 서버에 의존 합니다. 

Azure Active Directory (Azure AD)는 LDAP 동기화를 Azure AD Connect으로 바꿀 수 있습니다. Azure AD Connect 동기화 서비스는 온-프레미스 환경과 Azure AD 간의 id 데이터 동기화와 관련 된 모든 작업을 수행 합니다. 

## <a name="use-when"></a>적용 가능한 상황

온-프레미스 LDAP v3 디렉터리와 Azure AD 간의 id 데이터를 동기화 해야 합니다. 

![아키텍처 다이어그램](./media/authentication-patterns/ldap-sync.png)

## <a name="components-of-system"></a>시스템의 구성 요소

* **사용자** : LDAP v3 디렉터리를 사용 하 여 사용자와 암호를 정렬 하는 응용 프로그램에 액세스 합니다.

* **웹 브라우저** : 응용 프로그램의 외부 URL에 액세스 하기 위해 사용자가 상호 작용 하는 구성 요소입니다.

* **웹 앱** : LDAP v3 디렉터리에 대 한 종속성이 있는 응용 프로그램입니다.

* **AZURE ad** : azure ad는 Azure AD Connect를 통해 조직의 온-프레미스 LDAP 디렉터리에서 id 정보 (사용자, 그룹, 암호)를 동기화 합니다. 

* **Azure AD Connect** : 온-프레미스 id 인프라를 Microsoft Azure AD에 연결 하기 위한 도구입니다. 마법사 및 단계별 경험을 통해 연결에 필요한 필수 구성 요소 및 구성 요소를 배포 하 고 구성할 수 있습니다. 

* **사용자 지정 커넥터** : 일반 ldap 커넥터를 사용 하면 Azure AD Connect 동기화 서비스를 LDAP v3 서버와 통합할 수 있습니다. Azure AD Connect에 있습니다.

* **Active Directory** : Active Directory는 대부분의 Windows Server 운영 체제에 포함 된 디렉터리 서비스입니다. Active Directory Directory 서비스를 실행 하는 서버를 도메인 컨트롤러 라고 하며 Windows 도메인의 모든 사용자 및 컴퓨터를 인증 하 고 권한을 부여 합니다.

* **Ldap v3 서버** : 디렉터리 서비스 인증에 사용 되는 회사 사용자 및 암호를 저장 하는 ldap 프로토콜 규격 디렉터리입니다.

## <a name="implement-ldap-synchronization-with-azure-ad"></a>Azure AD를 사용 하 여 LDAP 동기화 구현

* [하이브리드 Id 디렉터리 통합 도구](https://docs.microsoft.com/azure/active-directory/hybrid/plan-hybrid-identity-design-considerations-tools-comparison) 

* [Azure AD Connect 설치 로드맵](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-roadmap) 

* [LDAP 커넥터 개요 및 만들기](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap) 

   > [!NOTE]
   > LDAP 커넥터는 Forefront Identity Manager 및/또는 Microsoft Identity Manager에 대 한 지식이 필요한 고급 구성입니다. 프로덕션 환경에서 사용 하는 경우이 구성에 대 한 질문은 [프리미어 지원](https://support.microsoft.com/premier) 또는 Microsoft 파트너 네트워크를 통해 수행 해야 합니다.

 
