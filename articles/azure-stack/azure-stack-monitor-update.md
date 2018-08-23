---
title: 권한 있는 끝점을 사용 하 여 Azure Stack의 업데이트 모니터링 | Microsoft Docs
description: Azure Stack 통합 시스템에 대 한 업데이트 상태를 모니터링 하는 권한 있는 끝점을 사용 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2018
ms.author: mabrigg
ms.openlocfilehash: 8f384a79811c9a9b104acb98c8f6b6e162946ab8
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2018
ms.locfileid: "42139361"
---
# <a name="monitor-updates-in-azure-stack-using-the-privileged-endpoint"></a>권한 있는 끝점을 사용 하 여 Azure Stack의 업데이트를 모니터링 합니다.

*적용 대상: Azure Stack 통합 시스템*

Azure Stack 업데이트 실행을의 진행률을 모니터링 하려면 권한 있는 끝점을 사용할 수 및 마지막으로 성공한 단계에서 실행 실패 한 업데이트를 다시 시작 해야 Azure Stack 포털을 사용할 수 없게 합니다.  Azure Stack 포털을 사용 하는 Azure Stack에서 업데이트를 관리 하는 것이 좋습니다.

업데이트 관리에 대 한 다음 새 PowerShell cmdlet은 Azure Stack 통합 시스템이 1710 업데이트에 포함 됩니다.

| Cmdlet  | 설명  |
|---------|---------|
| `Get-AzureStackUpdateStatus` | 현재 실행 중인, 완료 또는 실패 한 업데이트의 상태를 반환 합니다. 업데이트 작업 및 현재 단계 및 해당 상태를 설명 하는 XML 문서의 개략적인 상태를 제공 합니다. |
| `Resume-AzureStackUpdate` | 실패 한 지점에서 실패 한 업데이트를 다시 시작 합니다. 특정 시나리오에서 업데이트를 다시 시작 하기 전에 완화 단계를 완료 해야 할 수 있습니다.         |
| | |

## <a name="verify-the-cmdlets-are-available"></a>Cmdlet을 사용할 수 확인
Cmdlet은 Azure Stack에 대 한 1710 업데이트 패키지의 새 이기 때문에 1710 업데이트 프로세스를 모니터링 하는 기능을 사용할 특정 시점에 도달 해야 합니다. 일반적으로 cmdlet은 관리자 포털에서 상태를 나타내면 1710 업데이트를 사용할 수는 **저장소 호스트를 다시 시작** 단계입니다. Cmdlet은 업데이트 하는 동안 발생 하는 특히 **단계: 2.6-PrivilegedEndpoint 업데이트 허용 목록에 추가 단계를 실행 중인**합니다.

Cmdlet은 사용 가능한 지 프로그래밍 방식으로 권한 있는 끝점에서 명령 목록을 쿼리하여 확인할 수 있습니다. 이 위해 수명 주기 호스트 하드웨어에서에서 또는 권한 있는 액세스 워크스테이션에서 다음 명령을 실행 합니다. 또한 권한 있는 끝점은 신뢰할 수 있는 호스트 해야 합니다. 자세한 내용은 참조의 1 단계 [끝점에 권한 있는 액세스](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)합니다. 

1. Azure Stack 환경에서 ERCS 가상 컴퓨터에서 PowerShell 세션을 만듭니다 (*접두사*-, ERCS01 *접두사*-ERCS02, 또는 *접두사*-ERCS03). 바꿉니다 *접두사* 환경과 관련 된 가상 머신 접두사 문자열을 사용 합니다.

   ```powershell
   $cred = Get-Credential

   $pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
   ```
   사용 하 여 자격 증명을 묻는 메시지가 나타나면 합니다 &lt; *Azure Stack 도메인*&gt;\cloudadmin 계정이 나 CloudAdmins 그룹의 구성원 인 계정입니다. CloudAdmin 계정의 AzureStackAdmin 도메인 관리자 계정에 설치 하는 동안 제공한 동일한 암호를 입력 합니다.

2. 권한 있는 끝점에서 사용할 수 있는 명령의 전체 목록을 가져옵니다. 

   ```powershell
   $commands = Invoke-Command -Session $pepSession -ScriptBlock { Get-Command } 
   ```
3. 권한 있는 끝점 업데이트 된 경우를 결정 합니다.

   ```powershell
   $updateManagementModuleName = "Microsoft.Azurestack.UpdateManagement"
    if (($commands | ? Source -eq $updateManagementModuleName)) {
   Write-Host "Privileged endpoint was updated to support update monitoring tools."
    } else {
   Write-Host "Privileged endpoint has not been updated yet. Please try again later."
    } 
   ```

4. Microsoft.AzureStack.UpdateManagement 모듈 관련 명령을 나열 합니다.

   ```powershell
   $commands | ? Source -eq $updateManagementModuleName 
   ```
   예: 
   ```powershell
   $commands | ? Source -eq $updateManagementModuleName
   
   CommandType     Name                                               Version    Source                                                  PSComputerName
    -----------     ----                                               -------    ------                                                  --------------
   Function        Get-AzureStackUpdateStatus                         0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   Function        Resume-AzureStackUpdate                            0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   ``` 

## <a name="use-the-update-management-cmdlets"></a>업데이트 관리 cmdlet을 사용 하 여

> [!NOTE]
> 수명 주기 호스트 하드웨어에서에서 또는 권한 있는 액세스 워크스테이션에서 다음 명령을 실행 합니다. 또한 권한 있는 끝점은 신뢰할 수 있는 호스트 해야 합니다. 자세한 내용은 참조의 1 단계 [끝점에 권한 있는 액세스](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)합니다.

### <a name="connect-to-the-privileged-endpoint-and-assign-session-variable"></a>권한 있는 끝점에 연결 하 고 세션 변수 할당

Azure Stack 환경에서 ERCS 가상 컴퓨터에서 PowerShell 세션을 만들려면 다음 명령을 실행 (*접두사*-, ERCS01 *접두사*-ERCS02, 또는 *접두사*-ERCS03), 세션 변수를 할당할 수 있습니다.

```powershell
$cred = Get-Credential

$pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
```
 사용 하 여 자격 증명을 묻는 메시지가 나타나면 합니다 &lt; *Azure Stack 도메인*&gt;\cloudadmin 계정이 나 CloudAdmins 그룹의 구성원 인 계정입니다. CloudAdmin 계정의 AzureStackAdmin 도메인 관리자 계정에 설치 하는 동안 제공한 동일한 암호를 입력 합니다.

### <a name="get-high-level-status-of-the-current-update-run"></a>현재 업데이트 실행의 개략적인 상태를 가져옵니다. 

현재 업데이트 실행의 개략적인 상태를 가져오려면 다음 명령을 실행 합니다. 

```powershell
$statusString = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus -StatusOnly }

$statusString.Value 
```

가능한 값은 다음과 같습니다.

- 실행 중
- Completed
- 실패 
- Canceled

최신 상태를 반복적으로 이러한 명령을 실행할 수 있습니다. 다시 확인에 대 한 연결을 설정할 필요가 없습니다.

### <a name="get-the-full-update-run-status-with-details"></a>실행 상태 세부 정보를 사용 하 여 전체 업데이트를 가져오려면 

XML 문자열로 요약 실행 전체 업데이트를 가져올 수 있습니다. 검사에 대 한 파일에 문자열을 작성 하 고 또는 XML 문서로 변환 하 고, PowerShell을 사용 하 여 구문 분석 수 있습니다. 다음 명령은 현재 실행 중인 단계의 계층적 목록을 가져오려면 XML 구문 분석 합니다.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']")
```

다음 예제에서는 최상위 단계 (클라우드 업데이트)에 자식 계획을 업데이트 하는 저장소 호스트를 다시 시작 합니다. 저장소 호스트를 다시 시작 계획 호스트 중 하나에서 Blob Storage 서비스를 업데이트 하 고 있음을 보여 줍니다.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']") 

    FullStepIndex : 2
    Index         : 2
    Name          : Cloud Update
    Description   : Perform cloud update.
    StartTimeUtc  : 2017-10-13T12:50:39.9020351Z
    Status        : InProgress
    Task          : Task
    
    FullStepIndex  : 2.9
    Index          : 9
    Name           : Restart Storage Hosts
    Description    : Restart Storage Hosts.
    EceErrorAction : Stop
    StartTimeUtc   : 2017-10-13T15:44:06.7431447Z
    Status         : InProgress
    Task           : Task
    
    FullStepIndex : 2.9.2
    Index         : 2
    Name          : PreUpdate ACS Blob Service
    Description   : Check function level, update deployment artifacts, configure Blob service settings
    StartTimeUtc  : 2017-10-13T15:44:26.0708525Z
    Status        : InProgress
    Task          : Task
```

### <a name="resume-a-failed-update-operation"></a>실패 한 업데이트 작업을 다시 시작

업데이트에 실패 하면 업데이트 중단 된 실행을 재개할 수 있습니다.

```powershell
Invoke-Command -Session $pepSession -ScriptBlock { Resume-AzureStackUpdate } 
```

## <a name="troubleshoot"></a>문제 해결

권한 있는 끝점은 Azure Stack 환경에서 모든 ERCS 가상 머신에서 사용할 수 있습니다. 항상 사용 가능한 끝점에는 연결 되지, 때문에 가끔 중단, 경고 또는 오류 메시지가 발생할 수 있습니다. 이러한 메시지는 세션 연결이 끊어진 또는 ECE 서비스와 통신 오류가 발생 했음을 나타낼 수 있습니다. 이 동작은 예상 됩니다. 잠시 후에 작업을 다시 시도 하거나 다른 ERCS 가상 컴퓨터 중 하나에서 새 끝점 권한 있는 세션을 만들 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- [Azure Stack에서 업데이트 관리](azure-stack-updates.md) 


