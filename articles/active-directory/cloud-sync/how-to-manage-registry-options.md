---
title: 'Azure AD Connect 클라우드 프로비저닝 에이전트: 레지스트리 옵션 관리 | Microsoft Docs'
description: 이 문서에서는 Azure AD Connect 클라우드 프로비저닝 에이전트에서 레지스트리 옵션을 관리하는 방법을 설명합니다.
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
ms.openlocfilehash: 1f39ef611e2ea15ef3bc3dbfcf09e9624cbcf8b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678224"
---
# <a name="manage-agent-registry-options"></a>에이전트 레지스트리 옵션 관리

이 섹션에서는 Azure AD Connect 프로비저닝 에이전트의 런타임 처리 동작을 제어하기 위해 설정할 수 있는 레지스트리 옵션에 대해 설명합니다. 

## <a name="configure-ldap-connection-timeout"></a>LDAP 연결 제한 시간 구성
구성된 Active Directory 도메인 컨트롤러에서 LDAP 작업을 수행하는 경우, 기본적으로 프로비저닝 에이전트는 30초의 기본 연결 제한 시간 값을 사용합니다. 도메인 컨트롤러에서 응답하는 데 시간이 더 오래 걸리면 에이전트 로그 파일에 다음과 같은 오류 메시지가 표시될 수 있습니다. 

`
System.DirectoryServices.Protocols.LdapException: The operation was aborted because the client side timeout limit was exceeded.
`

검색 특성이 인덱싱되지 않으면 LDAP 검색 작업 시간이 오래 걸릴 수 있습니다. 첫 단계로 위의 오류가 발생하면 먼저 검색/조회 특성이 [인덱싱](/windows/win32/ad/indexed-attributes)되는지 확인합니다. 검색 특성이 인덱싱되어 오류가 계속 발생하면 다음 단계를 수행하여 LDAP 연결 시간 제한을 늘릴 수 있습니다. 

1. Azure AD Connect 프로비저닝 에이전트를 실행하는 Windows Server에서 관리자로 로그온합니다.
1. *실행* 메뉴 항목을 사용하여 레지스트리 편집기(regedit.exe)를 엽니다. 
1. 키 폴더 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent** 를 찾습니다.
1. 마우스 오른쪽 단추로 클릭하고 “새로 만들기 -> 문자열 값” 선택
1. 이름 제공: `LdapConnectionTimeoutInMilliseconds`
1. **값 이름** 을 두 번 클릭하고 값 데이터를 `60000`밀리초로 입력합니다.
    > [!div class="mx-imgBorder"]
    > ![LDAP 연결 시간 제한](media/how-to-manage-registry-options/ldap-connection-timeout.png)
1. *서비스* 콘솔에서 Azure AD Connect 프로비저닝 서비스를 다시 시작합니다.
1. 여러 프로비저닝 에이전트를 배포한 경우 일관성을 위해 모든 에이전트에 이 레지스트리 변경 내용을 적용합니다. 

## <a name="configure-referral-chasing"></a>조회 추적 구성
기본적으로 Azure AD Connect 프로비저닝 에이전트는 [조회](/windows/win32/ad/referrals)를 추적하지 않습니다. 다음과 같은 특정 HR 인바운드 프로비저닝 시나리오를 지원하기 위해 추적 조회를 사용하도록 설정할 수 있습니다. 
* 여러 도메인에서 UPN의 고유성 확인
* 도메인 간 관리자 참조 확인

다음 단계를 수행하여 참조 추적을 켭니다.

1. Azure AD Connect 프로비저닝 에이전트를 실행하는 Windows Server에서 관리자로 로그온합니다.
1. *실행* 메뉴 항목을 사용하여 레지스트리 편집기(regedit.exe)를 엽니다. 
1. 키 폴더 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent** 를 찾습니다.
1. 마우스 오른쪽 단추로 클릭하고 “새로 만들기 -> 문자열 값” 선택
1. 이름 제공: `ReferralChasingOptions`
1. **값 이름** 을 두 번 클릭하고 값 데이터에 `96`을 입력합니다. 이 값은 `ReferralChasingOptions.All`에 대한 상수 값에 해당하며 하위 트리와 기본 수준 조회를 모두 에이전트가 뒤에 오도록 지정합니다. 
    > [!div class="mx-imgBorder"]
    > ![조회 추적](media/how-to-manage-registry-options/referral-chasing.png)
1. *서비스* 콘솔에서 Azure AD Connect 프로비저닝 서비스를 다시 시작합니다.
1. 여러 프로비저닝 에이전트를 배포한 경우 일관성을 위해 모든 에이전트에 이 레지스트리 변경 내용을 적용합니다.

## <a name="skip-gmsa-configuration"></a>GMSA 구성 건너뛰기
에이전트 버전 1.1.281.0 이상인 경우, 기본적으로 에이전트 구성 마법사를 실행할 때 [GMSA(그룹 관리 서비스 계정)](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)을 설정하라는 메시지가 표시됩니다. 마법사를 통한 GMSA 설정은 모든 동기화 및 프로비저닝 작업에 대해 런타임에 사용됩니다. 

에이전트 이전 버전에서 업그레이드하고 Active Directory 토폴로지와 관련된 위임된 OU 수준 권한으로 사용자 지정 서비스 계정을 설정하는 경우, GMSA 구성을 건너뛰거나 연기하고 이 변경에 대한 계획을 수립할 수 있습니다. 

> [!NOTE]
> 특히 이 지침은 에이전트 1.1.281.0 이전 버전으로 HR(Workday/SuccessFactors) 인바운드 프로비저닝을 구성하고 에이전트 작업에 대해 사용자 지정 서비스 계정을 설정하는 고객에게 적용됩니다. 장기 실행에서 최고의 가용성을 얻을 수 있도록 GMSA로 전환하는 것이 좋습니다.  

이 시나리오에서 에이전트 이진 파일을 업그레이드하고 다음 단계를 수행하여 GMSA 구성을 건너뛸 수 있습니다. 

1. Azure AD Connect 프로비저닝 에이전트를 실행하는 Windows Server에서 관리자로 로그온합니다.
1. 에이전트 설치 프로그램을 실행하여 새 에이전트 이진 파일을 설치합니다. 설치에 성공한 후 자동으로 열리는 에이전트 구성 마법사를 닫습니다. 
1. *실행* 메뉴 항목을 사용하여 레지스트리 편집기(regedit.exe)를 엽니다. 
1. 키 폴더 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent** 를 찾습니다.
1. 마우스 오른쪽 단추로 클릭하고 “새로 만들기 -> DWORD 값” 선택
1. 이름 제공: `UseCredentials`
1. **값 이름** 을 두 번 클릭하고 값 데이터에 `1`을 입력합니다.  
    > [!div class="mx-imgBorder"]
    > ![자격 증명 사용](media/how-to-manage-registry-options/use-credentials.png)
1. *서비스* 콘솔에서 Azure AD Connect 프로비저닝 서비스를 다시 시작합니다.
1. 여러 프로비저닝 에이전트를 배포한 경우 일관성을 위해 모든 에이전트에 이 레지스트리 변경 내용을 적용합니다.
1. 바탕 화면 바로 가기에서 에이전트 구성 마법사를 실행합니다. 마법사가 GMSA 구성을 건너뜁니다. 


> [!NOTE]
> [자세한 정보 로깅](how-to-troubleshoot.md#log-files)을 사용하도록 설정하여 레지스트리 옵션이 설정되었는지 확인할 수 있습니다. 에이전트를 시작하는 동안 내보낸 로그에는 레지스트리에서 선택한 구성 값이 표시됩니다. 

## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 동기화란?](what-is-cloud-sync.md)

