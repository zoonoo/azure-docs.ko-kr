---
title: Azure Stack 유효성 검사 도구를 사용 하 여 | Microsoft Docs
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
ms.date: 12/03/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: 82a691c0e0b6280a168605d56ee628d81f10823f
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55245653"
---
# <a name="validate-azure-stack-system-state"></a>Azure Stack 시스템 상태 유효성 검사

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack 운영자, 시스템 주문형의 상태를 파악 하는 능력은 필요 합니다. Azure Stack 유효성 검사 도구 (**테스트 AzureStack**) 수 있는 PowerShell cmdlet을 실행 되는 일련의 테스트 있으면 오류를 식별할 수 있도록 시스템에서. 일반적으로이 도구를 실행 하 라는 메시지가 나타납니다 합니다 [privileged 끝점 (PEP)](azure-stack-privileged-endpoint.md) 문제에 대해 Microsoft 고객 서비스 지원 (CSS)에 문의 하는 경우. 시스템 상태와 상태 정보를 언제 든 CSS 수집 및 자세한 로그 분석을 영역 오류가 발생 하 고 문제를 해결 하를 사용 하는 위치에 집중 합니다.

## <a name="running-the-validation-tool-and-accessing-results"></a>유효성 검사 도구를 실행 하 고 결과 액세스

에서 설명한 대로 유효성 검사 도구는 PEP를 통해 실행 됩니다. 각 테스트에서 반환 된 **통과/실패** PowerShell 창에서 상태. 또한 하는 동안 나중에 액세스할 수 있는 자세한 HTML 보고서가 만들어집니다 [로그 컬렉션](azure-stack-diagnostics.md)합니다. 프로세스를 테스트 하는 종단 간 유효성 검사에 대 한 개요는 다음과 같습니다. 

1. 권한 있는 끝점 (PEP)에 액세스 합니다. PEP 세션을 설정 하려면 다음 명령을 실행 합니다.

   ```powershell
   Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
   ```

   > [!TIP]
   > ASDK 호스트 컴퓨터에서 PEP에 액세스 하려면 AzS ERCS01-ComputerName을 사용 합니다.

2. PEP 있는 경우 다음을 실행 합니다. 

   ```powershell
   Test-AzureStack
   ```

   참조를 [매개 변수 고려 사항](azure-stack-diagnostic-test.md#parameter-considerations) 하 고 [사용 사례 예제](azure-stack-diagnostic-test.md#use-case-examples) 자세한 내용은 섹션입니다.

3. 모든 테스트에서 보고 하는 경우 **실패**을 실행 합니다.

   ```powershell
   Get-AzureStackLog -FilterByRole SeedRing -OutputSharePath "<path>" -OutputShareCredential $cred
   ```

   Cmdlet은 테스트 AzureStack에서 생성 된 로그를 수집 합니다. 진단 로그에 대 한 자세한 내용은 참조 하세요. [진단 도구를 Azure Stack](azure-stack-diagnostics.md)합니다. 로그를 수집 하거나 경우 CSS에 문의 해야 테스트에서 보고 **경고**합니다.

4. CSS에서 유효성 검사 도구를 실행 하도록 명령, CSS 담당자는 문제를 해결 하려면 수집 된 로그를 요청 합니다.

## <a name="tests-available"></a>사용 가능한 테스트

유효성 검사 도구를 사용 하면 일련의 시스템 수준 테스트를 실행 하 고 현재 정보를 제공 하는 기본 클라우드 시나리오 상태 시스템의 문제를 확인 합니다.

### <a name="cloud-infrastructure-tests"></a>클라우드 인프라 테스트

이러한 낮은 영향 테스트는 인프라 수준에서 작동 하 고 다양 한 시스템 구성 요소 및 함수에 대 한 정보를 제공 합니다. 현재 테스트는 다음과 같은 범주로 그룹화 됩니다.

| 테스트 범주                                        | 인수에 대 한 포함 및-무시 |
| :--------------------------------------------------- | :-------------------------------- |
| Azure Stack 경고 요약                            | AzsAlertSummary                   |
| Azure Stack 백업 공유 내게 필요한 옵션 요약       | AzsBackupShareAccessibility       |
| Azure Stack 제어 평면 요약                    | AzsControlPlane                   |
| Azure Stack Defender 요약                         | AzsDefenderSummary                |
| Azure Stack 인프라 펌웨어 요약 호스팅  | AzsHostingInfraFWSummary          |
| Azure Stack 클라우드 인프라 요약 호스팅     | AzsHostingInfraSummary            |
| Azure Stack 클라우드 인프라 사용률 호스팅 | AzsHostingInfraUtilization        |
| Azure Stack 인프라 용량                  | AzsInfraCapacity                  |
| Azure Stack 인프라 성능               | AzsInfraPerformance               |
| Azure Stack 인프라 역할 요약              | AzsInfraRoleSummary               |
| Azure Stack 업데이트 요약                           | AzsInfraUpdateSummary             |
| Azure Stack 포털 및 API 요약                   | AzsPortalAPISummary               |
| Azure Stack 크기 조정 단위 VM 이벤트                     | AzsScaleUnitEvents                |
| Azure Stack 리소스 VM 크기 조정 단위                  | AzsScaleUnitResources             |
| Azure Stack SDN 유효성 검사 요약                   | AzsSDNValidation                  |
| Azure Stack 서비스 패브릭 역할 요약              | AzsSFRoleSummary                  |
| Azure Stack BMC 요약                              | AzsStampBMCSummary                |
| Azure Stack 저장소 서비스 요약                 | AzsStorageSvcsSummary             |
| Azure Stack SQL 저장소 요약                        | AzsStoreSummary                   |
| Azure Stack VM 배치 요약                     | AzsVmPlacement                    |

### <a name="cloud-scenario-tests"></a>클라우드 시나리오 테스트

위의 인프라 테스트 외에 인프라 구성 요소에서 기능을 확인 하려면 클라우드 시나리오 테스트를 실행할 수가 있습니다. 클라우드 관리자 자격 증명 리소스 배포를 포함 하는 경우에 따라 이러한 테스트 실행 해야 합니다. 
    > [!NOTE]
    >
    > Currently you cannot run cloud scenario tests using Active Directory Federated Services (AD FS) credentials. 

다음 클라우드 시나리오 유효성 검사 도구에서 테스트 됩니다.
- 리소스 그룹 만들기   
- 계획 만들기              
- 제품 만들기            
- 저장소 계정 만들기   
- 가상 머신 만들기 
- Blob 저장소 작업   
- 큐 저장소 작업  
- 테이블 저장소 작업  

## <a name="parameter-considerations"></a>매개 변수 고려 사항

- 매개 변수 **목록** 모든 사용 가능한 테스트 범주를 표시 하려면 사용할 수 있습니다.

- 매개 변수 **Include** 하 고 **무시** 포함 하거나 제외할 테스트 범주를 사용할 수 있습니다. 참조 된 [사용 가능한 테스트](azure-stack-diagnostic-test.md#tests-available) 이러한 인수를 사용 하 여 사용할 약식 대 한 자세한 내용은 섹션입니다.

  ```powershell
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
  ```

  ```powershell
  Test-AzureStack -Ignore AzsInfraPerformance
  ```

- VM의 일부로 배포 된 테 넌 트 클라우드 시나리오를 테스트 합니다. 사용할 수 있습니다 **DoNotDeployTenantVm** 이 사용 하지 않도록 설정 합니다. 

- 제공 해야 합니다 **ServiceAdminCredential** 에 설명 된 대로 클라우드 시나리오 테스트를 실행할 매개 변수를 [사용 사례 예제](azure-stack-diagnostic-test.md#use-case-examples) 섹션입니다.

- **BackupSharePath** 하 고 **BackupShareCredential** 에 표시 된 대로 인프라 백업 설정을 테스트할 때 사용 됩니다 합니다 [사용 사례 예제](azure-stack-diagnostic-test.md#use-case-examples) 섹션입니다.

- 유효성 검사 도구는 또한 일반적인 PowerShell 매개 변수를 지원합니다. Verbose, Debug, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable, 및 outvariable을 지원 합니다. 자세한 내용은 [에 대 한 일반 매개 변수](https://go.microsoft.com/fwlink/?LinkID=113216)합니다.  

## <a name="use-case-examples"></a>사용 사례 예제

### <a name="run-validation-without-cloud-scenarios"></a>유효성 검사 없이 클라우드 시나리오를 실행 합니다.

하지 않고 유효성 검사 도구를 실행 합니다 **ServiceAdminCredential** 클라우드 시나리오 테스트를 실행 하지 않으려면 매개 변수: 

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred
Test-AzureStack
```

### <a name="run-validation-with-cloud-scenarios"></a>클라우드 시나리오를 사용 하 여 유효성 검사를 실행 합니다.

사용 하 여 유효성 검사 도구를 제공 합니다 **ServiceAdminCredentials** 매개 변수는 기본적으로 클라우드 시나리오 테스트를 실행 합니다. 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" 
```

테스트의 나머지 부분을 실행 하지 않고 전용 클라우드 시나리오를 실행 하려는 경우 사용할 수 있습니다 합니다 **Include** 이렇게 하려면 매개 변수: 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" -Include AzsScenarios   
```

클라우드 관리자 사용자 이름은 UPN 형식으로 입력 해야 합니다. serviceadmin@contoso.onmicrosoft.com (Azure AD)입니다. 메시지가 표시 되 면 클라우드 관리자 계정에 암호를 입력 합니다.

### <a name="run-validation-tool-to-test-system-readiness-before-installing-update-or-hotfix"></a>유효성 검사 도구를 업데이트 또는 핫픽스를 설치 하기 전에 시스템 준비 테스트를 실행 합니다.

업데이트 또는 핫픽스 설치를 시작 하기 전에 Azure Stack의 상태를 확인 하려면 유효성 검사 도구를 실행 해야 합니다.

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
```

### <a name="run-validation-tool-to-test-infrastructure-backup-settings"></a>인프라 백업 설정을 테스트 하려면 유효성 검사 도구 실행

*앞* 인프라 백업을 구성 테스트 백업 공유 경로 사용 하 여 자격 증명을 **AzsBackupShareAccessibility** 테스트: 

  ```powershell
  New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential <PSCredentials-for-backup-share>
  ```

*후* 백업 구성에서 실행할 수 있습니다 **AzsBackupShareAccessibility** 공유의 유효성을 검사 하 여 ERCS에서 액세스할 수 있습니다.

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility
  ```

구성 된 백업 공유를 사용 하 여 새 자격 증명을 테스트 하려면 다음을 실행 합니다. 

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupShareCredential "<PSCredential for backup share>"
  ```



## <a name="next-steps"></a>다음 단계

Azure Stack의 진단 도구 및 문제 로깅에 대 한 자세한 내용은 참조 하세요 [진단 도구를 Azure Stack](azure-stack-diagnostics.md)합니다.

문제를 해결 하는 방법에 대 한 자세한 내용은 참조 하세요 [Microsoft Azure Stack 문제 해결](azure-stack-troubleshooting.md)합니다.