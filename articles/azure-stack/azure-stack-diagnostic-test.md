---
title: Azure Stack에서 유효성 검사 테스트를 실행 합니다. | Microsoft Docs
description: Azure Stack의 진단에 대 한 로그 파일을 수집 하는 방법입니다.
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 11/02/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: 1726b7d1ac6a4c39e7a8dab733644d01e5f3381e
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50957510"
---
# <a name="run-a-validation-test-for-azure-stack"></a>Azure Stack에 대 한 유효성 검사 테스트를 실행 합니다.

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*
 
에 Azure Stack의 상태를 확인할 수 있습니다. 문제일 경우에 Microsoft 고객 서비스 지원에 문의 합니다. 지원 요청을 실행할 수 **테스트 AzureStack** 관리 노드에서 합니다. 유효성 검사 테스트 실패를 격리합니다. 지원 수 다음 자세한 로그 분석, 오류가 발생 하는 영역에 집중 및 작동 하는 문제를 해결 합니다.

## <a name="run-test-azurestack"></a>AzureStack 테스트를 실행 합니다.

문제가 발생 하는 경우 Microsoft 고객 서비스 지원에 문의 한 다음 실행 **실행할 테스트 AzureStack**합니다.

1. 문제가 발생 합니다.
2. 연락처 Microsoft 고객 지원 서비스.
3. 실행할 **테스트 AzureStack** 권한 있는 끝점에서.
    1. 권한 있는 끝점에 액세스 합니다. 자세한 내용은 [권한 있는 끝점을 사용 하 여 Azure Stack에서](azure-stack-privileged-endpoint.md)합니다. 
    2. ASDK를에서 관리 호스트에 로그인 **AzureStack\CloudAdmin**합니다.  
    통합된 시스템을 권한 있는--끝점 OEM 하드웨어 공급 업체에서 제공 된 관리에 대 한 IP 주소를 사용 하도록 해야 합니다.
    3. 관리자 권한으로 PowerShell을 엽니다.
    4. 실행: `Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint`
    5. 실행: `Test-AzureStack`
4. 모든 테스트에서 보고 하는 경우 **실패**실행: `Get-AzureStackLog -FilterByRole SeedRing -OutputSharePath “<path>” -OutputShareCredential $cred` cmdlet 테스트-azurestack의 경우에서 로그를 수집 합니다. 진단 로그에 대 한 자세한 내용은 참조 하세요. [진단 도구를 Azure Stack](azure-stack-diagnostics.md)합니다. 로그를 수집 하거나 하는 경우 Microsoft 고객 서비스 지원 (CSS)에 문의 해야 테스트에서 보고 **경고**합니다.
5. 전송 합니다 **SeedRing** Microsoft 고객 서비스 지원에 로그 합니다. Microsoft 고객 서비스 지원 문제를 해결 하는 작동 합니다.

## <a name="reference-for-test-azurestack"></a>테스트-azurestack의 경우에 대 한 참조

이 섹션에서는 테스트 AzureStack cmdlet 및 유효성 검사 보고서의 요약에 대 한 개요가 포함 되어 있습니다.

### <a name="test-azurestack"></a>Test-AzureStack

Azure Stack의 상태를 확인합니다. Cmdlet은 Azure Stack 하드웨어 및 소프트웨어의 상태를 보고합니다. 지원 담당자가이 보고서를 사용 하 여 Azure Stack 지원 사례를 해결 하려면 시간을 줄일 수 있습니다.

> [!Note]  
> **테스트 AzureStack** 디스크 또는 단일 실제 호스트 노드 오류를 단일 실패와 같은 클라우드 중단은 생성 되지 않고 있는 오류를 발견할 수 있습니다.

#### <a name="syntax"></a>구문

````PowerShell
  Test-AzureStack
````

#### <a name="parameters"></a>매개 변수

| 매개 변수               | 값           | 필수 | 기본값 |
| ---                     | ---             | ---      | ---     |
| ServiceAdminCredentials | 문자열    | 아닙니다.       | FALSE   |
| DoNotDeployTenantVm     | SwitchParameter | 아닙니다.       | FALSE   |
| AdminCredential         | PSCredential    | 아닙니다.       | 해당 없음      |
| 나열                    | SwitchParameter | 아닙니다.       | FALSE   |
| 무시                  | 문자열          | 아닙니다.       | 해당 없음      |
| 포함                 | 문자열          | 아닙니다.       | 해당 없음      |
| BackupSharePath         | 문자열          | 아닙니다.       | 해당 없음      |
| BackupShareCredential   | PSCredential    | 아닙니다.       | 해당 없음      |


테스트-azurestack의 경우 cmdlet은 공통 매개 변수를 지원 합니다: Verbose, Debug, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable, 및 OutVariable 합니다. 자세한 내용은 [에 대 한 일반 매개 변수](http://go.microsoft.com/fwlink/?LinkID=113216)합니다. 

### <a name="examples-of-test-azurestack"></a>테스트-azurestack의 경우의 예

다음 예에서는 가정으로 로그인 **CloudAdmin** (PEP) 권한이 가진된 끝점에 액세스 합니다. 자세한 내용은 [권한 있는 끝점을 사용 하 여 Azure Stack에서](azure-stack-privileged-endpoint.md)합니다. 

#### <a name="run-test-azurestack-interactively-without-cloud-scenarios"></a>클라우드 시나리오 없이 테스트 AzureStack을 대화형으로 실행

PEP 세션에서 다음을 실행 합니다.

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack
````

#### <a name="run-test-azurestack-with-cloud-scenarios"></a>클라우드 시나리오를 사용 하 여 AzureStack 테스트 실행

사용할 수 있습니다 **테스트 AzureStack** Azure Stack에 대해 클라우드 시나리오를 실행할 수 있습니다. 이 시나리오에는 다음이 포함됩니다.

 - 리소스 그룹 만들기
 - 계획 만들기
 - 제품 만들기
 - 저장소 계정 만들기
 - 가상 컴퓨터 만들기
 - 테스트 시나리오에서 만든 저장소 계정을 사용 하 여 blob 작업 수행
 - 테스트 시나리오에서 만든 저장소 계정을 사용 하 여 큐 작업을 수행 합니다.
 - 테스트 시나리오에서 만든 저장소 계정을 사용 하 여 테이블 작업을 수행 합니다.

클라우드 시나리오에는 클라우드 관리자 자격 증명이 필요합니다. 
> [!Note]  
> Active Directory Federated Services (AD FS) 자격 증명을 사용 하 여 클라우드 시나리오를 실행할 수 없습니다. 합니다 **테스트 AzureStack** 만 cmdlet에는 PEP 통해 액세스할 수 있습니다. 그러나는 PEP ADFS 자격 증명을 지원 하지 않습니다.

클라우드 관리자 사용자 이름은 UPN 형식으로 입력 serviceadmin@contoso.onmicrosoft.com (Azure AD). 메시지가 표시 되 면 클라우드 관리자 계정에 암호를 입력 합니다.

PEP 세션에서 다음을 실행 합니다.

````PowerShell
  Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Test-AzureStack -ServiceAdminCredentials <Cloud administrator user name>
````

#### <a name="run-test-azurestack-without-cloud-scenarios"></a>클라우드 시나리오 없이 AzureStack 테스트 실행

PEP 세션에서 다음을 실행 합니다.

````PowerShell
  $session = New-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Invoke-Command -Session $session -ScriptBlock {Test-AzureStack}
````

#### <a name="list-available-test-scenarios"></a>사용 가능한 테스트 시나리오를 나열 합니다.

PEP 세션에서 다음을 실행 합니다.

````PowerShell
  Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Test-AzureStack -List
````

#### <a name="run-a-specified-test"></a>지정 된 테스트를 실행 합니다.

PEP 세션에서 다음을 실행 합니다.

````PowerShell
  Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
````

특정 테스트를 제외 합니다.

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack -Ignore AzsInfraPerformance
````

### <a name="run-test-azurestack-to-test-infrastructure-backup-settings"></a>인프라 백업 설정을 테스트 하려면 테스트 AzureStack 실행

백업 인프라를 구성 하기 전에 테스트 백업 공유 경로 사용 하 여 자격 증명을 **AzsBackupShareAccessibility** 테스트 합니다.

PEP 세션에서 다음을 실행 합니다.

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential <PSCredentials-for-backup-share>
````
백업을 구성 후 실행할 수 있습니다 AzsBackupShareAccessibility 공유의 유효성을 검사 하는 실행 PEP 세션에서 ERCS에서 액세스할 수 있습니다.

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack -Include AzsBackupShareAccessibility
````

구성 된 백업 공유를 사용 하 여 새 자격 증명을 테스트 하려면 PEP 세션에서 실행 합니다.

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack -Include AzsBackupShareAccessibility -BackupShareCredential <PSCredential for backup share>
````

### <a name="validation-test"></a>유효성 검사 테스트

다음 표에서 실행 하는 유효성 검사 테스트 **테스트 AzureStack**합니다.

| 이름                                                                                                                              |
|-----------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Azure Stack 클라우드 인프라 요약 호스팅                                                                                  |
| Azure Stack 저장소 서비스 요약                                                                                              |
| Azure Stack 인프라 역할 인스턴스 요약                                                                                  |
| Azure Stack 클라우드 인프라 사용률 호스팅                                                                              |
| Azure Stack 인프라 용량                                                                                               |
| Azure Stack 포털 및 API 요약                                                                                                |
| Azure Stack Azure Resource Manager 인증서 요약                                                                                               |
| 인프라 관리 컨트롤러, 네트워크 컨트롤러, 저장소 서비스 및 인프라 역할 권한 있는 끝점          |
| 인프라 관리 컨트롤러, 네트워크 컨트롤러, 저장소 서비스 및 권한이 부여 된 끝점 인프라 역할 인스턴스 |
| Azure Stack 인프라 역할 요약                                                                                           |
| Azure Stack 클라우드 서비스 패브릭 서비스                                                                                         |
| Azure Stack 인프라 역할 인스턴스 성능                                                                              |
| Azure Stack 클라우드 호스트 성능 요약                                                                                        |
| Azure Stack 서비스 리소스 사용량 요약                                                                                  |
| Azure Stack 배율 단위 중요 한 이벤트 (마지막 8 시간)                                                                             |
| Azure Stack 저장소 서비스 실제 디스크 요약                                                                               |
|Azure Stack 백업 공유 내게 필요한 옵션 요약                                                                                     |

## <a name="next-steps"></a>다음 단계

 - Azure Stack의 진단 도구 및 문제 로깅에 대 한 자세한 내용은 참조 하세요 [ 진단 도구를 Azure Stack](azure-stack-diagnostics.md)합니다.
 - 문제를 해결 하는 방법에 대 한 자세한 내용은를 참조 하세요. [Microsoft Azure Stack 문제 해결](azure-stack-troubleshooting.md)
