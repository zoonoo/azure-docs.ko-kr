---
title: 'Azure AD Connect 클라우드 프로 비전 에이전트: 레지스트리 옵션 관리 | Microsoft Docs'
description: 이 문서에서는 Azure AD Connect 클라우드 프로 비전 에이전트에서 레지스트리 옵션을 관리 하는 방법을 설명 합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2020
ms.subservice: hybrid
ms.reviewer: chmutali
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4cdda52271bc7b9e9d854e0af181e2c8f22ad9a
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613633"
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

## <a name="skip-gmsa-configuration"></a>GMSA 구성 건너뛰기
에이전트 버전 1.1.281.0 +를 사용 하는 경우 기본적으로 에이전트 구성 마법사를 실행 하면 [GMSA (그룹 관리 서비스 계정)](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)를 설정 하 라는 메시지가 표시 됩니다. 마법사에의 한 GMSA 설치는 모든 동기화 및 프로 비전 작업에 대해 런타임에 사용 됩니다. 

이전 버전의 에이전트에서 업그레이드 하 고 Active Directory 토폴로지와 관련 된 위임 된 OU 수준 권한으로 사용자 지정 서비스 계정을 설정 하는 경우 GMSA 구성을 건너뛰거나 연기 하 고이 변경에 대 한 계획을 수립할 수 있습니다. 

> [!NOTE]
> 이 지침은 1.1.281.0 이전에 에이전트 버전으로 HR (Workday/SuccessFactors) 인바운드 프로비저닝을 구성 하 고 에이전트 작업에 대 한 사용자 지정 서비스 계정을 설정 하는 고객에 게 특히 적용 됩니다. 장기 실행에서 모범 사례로 GMSA로 전환 하는 것이 좋습니다.  

이 시나리오에서는 여전히 에이전트 이진 파일을 업그레이드 하 고 다음 단계를 사용 하 여 GMSA 구성을 건너뛸 수 있습니다. 

1. Azure AD Connect 프로 비전 에이전트를 실행 하는 Windows server에서 관리자로 로그온 합니다.
1. 에이전트 설치 관리자를 실행 하 여 새 에이전트 이진 파일을 설치 합니다. 설치에 성공한 후 자동으로 열리는 에이전트 구성 마법사를 닫습니다. 
1. *실행* 메뉴 항목을 사용 하 여 레지스트리 편집기 (regedit.exe)를 엽니다. 
1. 키 폴더를 찾습니다 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. 마우스 오른쪽 단추로 클릭 하 고 "새로 만들기-> DWORD 값"을 선택 합니다.
1. 이름을 제공 합니다. `UseCredentials`
1. **값 이름을** 두 번 클릭 하 고 값 데이터를로 입력 합니다 `1` .  
    > [!div class="mx-imgBorder"]
    > ![자격 증명 사용](media/how-to-manage-registry-options/use-credentials.png)
1. *서비스* 콘솔에서 Azure AD Connect 프로 비전 서비스를 다시 시작 합니다.
1. 여러 프로 비전 에이전트를 배포한 경우 일관성을 위해 모든 에이전트에이 레지스트리 변경 내용을 적용 합니다.
1. 바탕 화면 짧은 잘라내기에서 에이전트 구성 마법사를 실행 합니다. 마법사가 GMSA 구성을 건너뜁니다. 


> [!NOTE]
> [자세한 정보 로깅을](how-to-troubleshoot.md#log-files)사용 하 여 레지스트리 옵션이 설정 되었는지 확인할 수 있습니다. 에이전트를 시작 하는 동안 내보낸 로그에는 레지스트리에서 선택한 구성 값이 표시 됩니다. 

## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 동기화 란?](what-is-cloud-sync.md)

