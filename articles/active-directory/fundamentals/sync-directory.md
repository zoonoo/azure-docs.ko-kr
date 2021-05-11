---
title: Azure Active Directory를 사용하여 디렉터리 동기화
description: Azure Active Directory를 사용하여 디렉터리 동기화를 달성하기 위한 아키텍처 참고 자료입니다.
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
ms.openlocfilehash: 98aa697c0120fd8a20adf11ad83e02406499a0d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648495"
---
# <a name="directory-synchronization"></a>디렉터리 동기화

많은 조직에는 온-프레미스 및 클라우드 구성 요소를 모두 포함하는 하이브리드 인프라가 있습니다. 로컬 디렉터리와 클라우드 디렉터리 간에 사용자 ID를 동기화하면 사용자가 단일 자격 증명 집합을 사용하여 리소스에 액세스할 수 있습니다. 

동기화는 다음과 같은 프로세스입니다. 

* 특정 조건에 따라 개체 만들기
* 개체를 업데이트된 상태로 유지
* 조건이 더 이상 충족되지 않을 때 개체를 제거합니다. 

온-프레미스 프로비저닝에는 온-프레미스 원본(예: Active Directory)에서 Azure Active Directory(Azure AD)로의 프로비저닝이 포함됩니다. 

## <a name="use-when"></a>적용 가능한 상황

온-프레미스 Active Directory 환경의 ID 데이터를 Azure AD와 동기화해야 합니다.

![아키텍처 다이어그램](./media/authentication-patterns/dir-sync-auth.png)

## <a name="components-of-system"></a>시스템의 구성 요소

* **사용자**: Azure AD를 사용하여 애플리케이션에 액세스합니다.

* **웹 브라우저**: 애플리케이션의 외부 URL에 액세스하기 위해 사용자가 상호 작용하는 구성 요소입니다.

* **애플리케이션**: 인증 및 권한 부여를 위해 Azure AD 사용에 의존하는 웹앱입니다.

* **Azure AD**: Azure AD Connect을 통해 조직의 온-프레미스 디렉터리에서 ID 정보를 동기화합니다. 

* **Azure AD Connect**: 온-프레미스 ID 인프라를 Microsoft Azure AD에 연결하는 도구입니다. 마법사 및 안내 경험을 통해 Active Directory에서 Azure AD로 동기화 및 로그인을 포함하여 연결에 필요한 필수 구성 요소 및 구성 요소를 배포하고 구성할 수 있습니다. 

* **Active Directory**: Active Directory는 Windows Server 운영 체제 대부분에 포함된 디렉터리 서비스입니다. Active Directory Domain Services(AD DS)를 실행하는 서버를 도메인 컨트롤러라고 합니다. 도메인의 모든 사용자 및 컴퓨터를 인증하고 권한을 부여합니다.

## <a name="implement-directory-synchronization-with-azure-ad"></a>Azure AD를 사용하여 디렉터리 동기화 구현

* [ID 프로비저닝이란?](../cloud-sync/what-is-provisioning.md) 

* [하이브리드 ID 디렉터리 통합 도구](../hybrid/plan-hybrid-identity-design-considerations-tools-comparison.md) 

* [Azure AD Connect 설치 로드맵](../hybrid/how-to-connect-install-roadmap.md)