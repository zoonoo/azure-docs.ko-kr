---
title: 'Azure AD Connect 클라우드 프로 비전 에이전트: 레지스트리 옵션 관리 | Microsoft Docs'
description: 이 문서에서는 Azure AD Connect 클라우드 프로 비전 에이전트에서 레지스트리 옵션을 관리 하는 방법을 설명 합니다.
services: active-directory
documentationcenter: ''
author: cmmdesai
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2020
ms.subservice: hybrid
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad3bd938355d138e660958e34d046d7af03e75c7
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/13/2020
ms.locfileid: "97371136"
---
# <a name="manage-agent-registry-options"></a>에이전트 레지스트리 옵션 관리

이 섹션에서는 Azure AD Connect 프로 비전 에이전트의 런타임 처리 동작을 제어 하기 위해 설정할 수 있는 레지스트리 옵션에 대해 설명 합니다. 

## <a name="configure-ldap-connection-timeout"></a>LDAP 연결 제한 시간 구성
구성 된 Active Directory 도메인 컨트롤러에서 LDAP 작업을 수행할 때 기본적으로 프로 비전 에이전트는 기본 연결 제한 시간 값인 30 초를 사용 합니다. 도메인 컨트롤러에서 응답 하는 데 시간이 더 오래 걸리면 에이전트 로그 파일에 다음과 같은 오류 메시지가 표시 될 수 있습니다. 

`
System.DirectoryServices.Protocols.LdapException: The operation was aborted because the client side timeout limit was exceeded.
`

검색 특성이 인덱싱되지 않으면 LDAP 검색 작업에 시간이 오래 걸릴 수 있습니다. 첫 번째 단계로 위의 오류가 발생 하면 먼저 검색/조회 특성이 [인덱싱되](https://docs.microsoft.com/windows/win32/ad/indexed-attributes)는 지 확인 합니다. 검색 특성이 인덱싱되어 오류가 계속 발생 하면 다음 단계를 사용 하 여 LDAP 연결 시간 제한을 늘릴 수 있습니다. 

1. Azure AD Connect 프로 비전 에이전트를 실행 하는 Windows server에서 관리자로 로그온 합니다.
1. *실행* 메뉴 항목을 사용 하 여 레지스트리 편집기 (regedit.exe)를 엽니다. 
1. 키 폴더를 찾습니다 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. 마우스 오른쪽 단추를 클릭 하 고 "새로 만들기-> 문자열 값"을 선택 합니다.
1. 이름을 제공 합니다. `LdapConnectionTimeoutInMilliseconds`
1. **값 이름을** 두 번 클릭 하 고 값 데이터를 밀리초로 입력 합니다 `60000` .
    > [!div class="mx-imgBorder"]
    > ![LDAP 연결 시간 제한](media/how-to-manage-registry-options/ldap-connection-timeout.png)
1. *서비스* 콘솔에서 Azure AD Connect 프로 비전 서비스를 다시 시작 합니다.
1. 여러 프로 비전 에이전트를 배포한 경우 일관성을 위해 모든 에이전트에이 레지스트리 변경 내용을 적용 합니다. 

## <a name="configure-referral-chasing"></a>조회 추적 구성
기본적으로 Azure AD Connect 프로 비전 에이전트는 [조회](https://docs.microsoft.com/windows/win32/ad/referrals)를 추적 하지 않습니다. 다음과 같은 특정 HR 인바운드 프로 비전 시나리오를 지원 하기 위해 조회 추적을 사용 하도록 설정할 수 있습니다. 
* 여러 도메인에서 UPN의 고유성을 확인 하는 중
* 도메인 간 관리자 참조 확인

다음 단계를 사용 하 여 조회 추적을 설정 합니다.

1. Azure AD Connect 프로 비전 에이전트를 실행 하는 Windows server에서 관리자로 로그온 합니다.
1. *실행* 메뉴 항목을 사용 하 여 레지스트리 편집기 (regedit.exe)를 엽니다. 
1. 키 폴더를 찾습니다 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. 마우스 오른쪽 단추를 클릭 하 고 "새로 만들기-> 문자열 값"을 선택 합니다.
1. 이름을 제공 합니다. `ReferralChasingOptions`
1. **값 이름을** 두 번 클릭 하 고 값 데이터를로 입력 합니다 `96` . 이 값은에 대 한 상수 값에 해당 `ReferralChasingOptions.All` 하며, 하위 트리와 기본 수준 조회를 모두 에이전트가 뒤에 오도록 지정 합니다. 
    > [!div class="mx-imgBorder"]
    > ![조회 추적](media/how-to-manage-registry-options/referral-chasing.png)
1. *서비스* 콘솔에서 Azure AD Connect 프로 비전 서비스를 다시 시작 합니다.
1. 여러 프로 비전 에이전트를 배포한 경우 일관성을 위해 모든 에이전트에이 레지스트리 변경 내용을 적용 합니다.

> [!NOTE]
> [자세한 정보 로깅을](how-to-troubleshoot.md#log-files)사용 하 여 레지스트리 옵션이 설정 되었는지 확인할 수 있습니다. 에이전트를 시작 하는 동안 내보낸 로그에는 레지스트리에서 선택한 구성 값이 표시 됩니다. 

## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 프로비저닝이란?](what-is-cloud-provisioning.md)

