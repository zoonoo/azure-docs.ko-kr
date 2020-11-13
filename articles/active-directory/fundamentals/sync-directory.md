---
title: Azure Active Directory와 디렉터리 동기화
description: Azure Active Directory와의 디렉터리 동기화를 달성 하기 위한 아키텍처 지침입니다.
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
ms.openlocfilehash: c340f973193f9c46735423c86112816003fecfcd
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578912"
---
# <a name="directory-synchronization"></a>디렉터리 동기화

많은 조직에는 온-프레미스 및 클라우드 구성 요소를 모두 포함 하는 하이브리드 인프라가 있습니다. 로컬 디렉터리와 클라우드 디렉터리 간에 사용자 id를 동기화 하면 사용자가 단일 자격 증명 집합을 사용 하 여 리소스에 액세스할 수 있습니다. 

동기화는의 프로세스입니다. 

* 특정 조건에 따라 개체 만들기
* 개체를 업데이트 된 상태로 유지
* 조건이 더 이상 충족 되지 않을 때 개체를 제거 합니다. 

온-프레미스 프로 비전에는 온-프레미스 원본 (예: Active Directory)에서 Azure Active Directory (Azure AD)로의 프로 비전이 포함 됩니다. 

## <a name="use-when"></a>적용 가능한 상황

온-프레미스 Active Directory 환경의 id 데이터를 Azure AD로 동기화 해야 합니다.

![아키텍처 다이어그램](./media/authentication-patterns/dir-sync-auth.png)

## <a name="components-of-system"></a>시스템의 구성 요소

* **사용자** : Azure AD를 사용 하 여 응용 프로그램에 액세스 합니다.

* **웹 브라우저** : 응용 프로그램의 외부 URL에 액세스 하기 위해 사용자가 상호 작용 하는 구성 요소입니다.

* **응용 프로그램** : 인증 및 권한 부여를 위해 Azure AD 사용에 의존 하는 웹 앱입니다.

* **AZURE AD** : Azure AD Connect을 통해 조직의 온-프레미스 디렉터리에서 id 정보를 동기화 합니다. 

* **Azure AD Connect** : 온-프레미스 id 인프라를 Microsoft Azure AD에 연결 하기 위한 도구입니다. 마법사 및 단계별 경험을 통해 Active Directory에서 Azure AD로 동기화 및 로그인을 포함 하 여 연결에 필요한 필수 구성 요소 및 구성 요소를 배포 하 고 구성할 수 있습니다. 

* **Active Directory** : Active Directory는 대부분의 Windows Server 운영 체제에 포함 된 디렉터리 서비스입니다. Active Directory Domain Services (AD DS)을 실행 하는 서버를 도메인 컨트롤러 라고 합니다. 도메인의 모든 사용자 및 컴퓨터를 인증 하 고 권한을 부여 합니다.

## <a name="implement-directory-synchronization-with-azure-ad"></a>Azure AD를 사용 하 여 디렉터리 동기화 구현

* [ID 프로비저닝이란?](https://docs.microsoft.com/azure/active-directory/cloud-provisioning/what-is-provisioning) 

* [하이브리드 id 디렉터리 통합 도구](https://docs.microsoft.com/azure/active-directory/hybrid/plan-hybrid-identity-design-considerations-tools-comparison) 

* [Azure AD Connect 설치 로드맵](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-roadmap)
