---
title: "Azure AD Connect Health 버전 내역"
description: "이 문서는 Azure AD Connect Health에 대한 릴리스 및 해당 릴리스에서 포함된 항목을 설명합니다."
services: active-directory
documentationcenter: 
author: karavar
manager: samueld
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 6c990a184d44771c78330f54f518bb4c35a36a35
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health: 버전 릴리스 내역
Azure Active Directory 팀은 새로운 기능과 성능으로 Azure AD Connect Health를 정기적으로 업데이트합니다. 이 문서는 출시된 버전 및 기능을 나열합니다.

## <a name="october-2016"></a>2016년 10월
**에이전트 업데이트:**

* AD FS에 대한 Azure AD Connect Health Agent\(버전 2.6.408.0\)
  1. 인증 요청에서 클라이언트 IP 주소를 검색하는 향상된 기능
  2. 경고와 관련된 버그 수정
* AD FS에 대한 Azure AD Connect Health Agent(버전 2.6.408.0)
  1. 경고와 관련된 버그 수정
* Azure AD Connect 버전 1.1.281.0와 함께 출시된 Azure AD Connect Health Agent(동기화 버전 2.6.353.0)
  1. 동기화 오류 보고서에 대한 필요한 데이터 제공
  2. 경고와 관련된 버그 수정

**새로운 미리 보기 기능:**

* Azure AD Connect에 대한 동기화 오류 보고서

**새로운 기능:**

* AD FS용 Azure AD Connect Health의 IP 주소 필드는 잘못된 사용자 이름/암호를 사용하는 상위 50 사용자에 대한 보고서에 표시됩니다.

## <a name="july-2016"></a>2016년 7월
**새로운 미리 보기 기능:**

* [AD DS용 Azure AD Connect Health](active-directory-aadconnect-health-adds.md)

## <a name="january-2016"></a>2016년 1월
**에이전트 업데이트:**

* AD FS에 대한 Azure AD Connect Health 에이전트(version 2.6.91.1512)

**새로운 기능:**

* [Azure AD Connect Health 에이전트에 대한 테스트 연결 도구](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>2015년 11월
**새로운 기능:**

* [역할 기반 액세스 제어](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)

**새로운 미리 보기 기능:**

* [동기화를 위한 Azure AD Connect Health](active-directory-aadconnect-health-sync.md).

**수정된 문제:**

* 에이전트를 등록하는 동안 표시된 오류에 대한 버그 수정.

## <a name="september-2015"></a>2015년 9월
**새로운 기능:**

* AD FS에 대한 잘못된 사용자 이름 암호 보고서
* 인증되지 않은 HTTP 프록시를 구성하는 지원
* Server 코어에서 에이전트를 구성하는 지원
* AD FS에 대한 경고의 향상된 기능
* 연결 및 데이터 업로드를 위한 AD FS에 대한 Azure AD Connect Health 에이전트의 향상된 기능.

**수정된 문제:**

* AD FS 오류 형식에 대한 사용 현황 정보에서 버그 수정.

## <a name="june-2015"></a>2015년 6월
**AD FS 및 AD FS 프록시에 대한 Azure AD Connect Health의 최초 릴리스.**

**새로운 기능:**

* 전자 메일 알림으로 AD FS 및 AD FS 프록시 서버의 모니터링에 대한 경고.
* AD FS 토폴로지 및 AD FS 성능 카운터의 패턴에 쉽게 액세스합니다.
* 응용 프로그램, 인증 방법, 요청 네트워크 위치 등으로 그룹화된 AD FS 서버에서 토큰 요청이 성공하는 추세입니다.
* 응용 프로그램, 오류 형식 등으로 그룹화된 AD FS 서버에서 요청이 실패하는 추세입니다.
* Azure AD 전역 관리자 자격 증명을 사용하여 간단한 에이전트 배포.  

## <a name="next-steps"></a>다음 단계
[온-프레미스 ID 인프라 및 클라우드 동기화 서비스 모니터링](active-directory-aadconnect-health.md)에 대해 자세히 알아봅니다.

