---
title: Azure Stack에서 권한 있는 끝점을 사용 하 여 | Microsoft Docs
description: 'Azure Stack에서 권한 있는 끝점 (PEP)을 사용 하는 방법 (예: Azure Stack 운영자) 보여 줍니다.'
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2018
ms.author: mabrigg
ms.reviewer: fiseraci
ms.openlocfilehash: f064521929bdaf0565a2993e12be62a3959ad567
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945300"
---
# <a name="using-the-privileged-endpoint-in-azure-stack"></a>Azure Stack에서 권한 있는 끝점을 사용 하 여

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack 운영자로 대부분의 일상적인 관리 작업에 대 한 관리자 포털, PowerShell 또는 Azure Resource Manager Api를 사용 해야 합니다. 그러나 덜 일반적인 작업 일부를 사용 해야 합니다 *privileged 끝점* (PEP). PEP는 필요한 작업을 수행 하는 데 필요한 기능을 제공 하는 미리 구성 된 원격 PowerShell 콘솔. 끝점 사용 [PowerShell JEA (Just Enough Administration)](https://docs.microsoft.com/powershell/jea/overview) cmdlet의 제한 된 집합만 노출 합니다. 액세스 하는 PEP 제한 집합이 cmdlet 호출, 권한이 낮은 계정을 사용 됩니다. 관리자 계정이 없는 필요 합니다. 추가 보안을 위해 스크립팅 허용 되지 않습니다.

다음과 같은 작업을 수행 하는 PEP를 사용할 수 있습니다.

- 같은 하위 수준 작업을 수행할 [진단 로그를 수집](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics#log-collection-tool)합니다.
- Microsoft Graph 통합, Active Directory Federation Services (AD FS) 통합을 설정 하는 배포 후 도메인 이름 시스템 (DNS) 전달자를 추가 하는 등의 통합된 시스템에 대 한 여러 데이터 센터 배포 후 통합 작업을 수행 하려면 인증서 회전, 등입니다.
- 지원에 통합된 시스템의 자세한 문제 해결에 대 한 높은 수준의 임시 액세스를 사용 하 여 작동 합니다.

PEP 모든 작업 (및 해당 출력) PowerShell 세션에서 수행 하는 기록 합니다. 이 완전 한 투명성 및 작업의 전체 감사를 제공 합니다. 향후 감사에 대 한 이러한 로그 파일을 유지할 수 있습니다.

> [!NOTE]
> 에 Azure Stack 개발 키트 ASDK ()를 실행할 수 있습니다는 PEP에서 사용할 수 있는 명령 중 일부를 PowerShell 세션에서 직접 개발 키트 호스트에서. 그러나 다음 통합된 시스템 환경에서 특정 작업을 수행할 수 있는 유일한 방법 이기 때문에 로그 수집 등의 PEP를 사용 하 여 일부 작업을 테스트 하는 것이 좋습니다.

## <a name="access-the-privileged-endpoint"></a>권한 있는 끝점에 액세스

PEP를 호스팅하는 가상 컴퓨터에서 원격 PowerShell 세션을 통해의 PEP을 액세스할 수 있습니다. ASDK,이 가상 컴퓨터의 이름은 **AzS ERCS01**합니다. PEP 실행 하는 각 인스턴스의 수가 세 가지 통합된 시스템을 사용 하는 경우 가상 머신 내에서 (*접두사*-, ERCS01 *접두사*-ERCS02, 또는 *접두사*- ERCS03) 복원 력을 위해 서로 다른 호스트에 있습니다. 

통합된 시스템에 대해이 절차를 시작 하기 전에 IP 주소 또는 DNS를 통해의 PEP에 액세스할 수 있는지 확인 합니다. Azure Stack의 초기 배포 후 DNS 통합은 아직 설정 하지 때문에 IP 주소를 통해서만 PEP을 액세스할 수 있습니다. OEM 하드웨어 공급 업체 이라는 JSON 파일을 사용 하 여 제공 됩니다 **AzureStackStampDeploymentInfo** PEP IP 주소를 포함 하는 합니다.


> [!NOTE]
> 보안상의 이유로 연결할 수 있는지는 PEP 하드웨어 수명 주기 호스트의 맨 위에서 실행 하는 확정 된 가상 컴퓨터 에서만에서 또는 전용, 보안 컴퓨터에서 같은 필요는 [Privileged Access Workstation](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)합니다. 하드웨어 수명 주기 호스트의 원래 구성에서 새 소프트웨어를 설치 하는 등, 원래 구성 수정 해서는 안 나의 PEP 연결할 사용 해야 합니다.

1. 트러스트를 설정 합니다.

    - 통합된 시스템을의 PEP 하드웨어 수명 주기 호스트 또는 권한 있는 액세스 워크스테이션에서 실행 하는 확정 된 가상 컴퓨터의 신뢰할 수 있는 호스트를 추가 하려면 관리자 권한 Windows PowerShell 세션에서 다음 명령을 실행 합니다.

      ````PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ````
    - 개발 키트 호스트에는 ADSK를 실행 하는 경우에 로그인 합니다.

2. 하드웨어 수명 주기 호스트 또는 권한 있는 액세스 워크스테이션에서 실행 되는 확정 된 가상 컴퓨터에서 Windows PowerShell 세션을 엽니다. PEP를 호스팅하는 가상 컴퓨터에서 원격 세션을 설정 하려면 다음 명령을 실행 합니다.
 
    - 통합 시스템:
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName <IP_address_of_ERCS> `
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ````
      `ComputerName` 매개 변수는 PEP를 호스팅하는 가상 컴퓨터 중 하나의 DNS 이름 또는 IP 주소 수 있습니다. 
    - ADSK 실행 하는 경우:
     
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName azs-ercs01 `
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```` 
   메시지가 표시 되 면 다음 자격 증명을 사용 합니다.

      - **사용자 이름**: CloudAdmin 계정 형식으로 지정  **&lt; *Azure Stack 도메인*&gt;\cloudadmin**합니다. (ASDK, 사용자 이름은 **azurestack\cloudadmin**.)
      - **암호**: AzureStackAdmin 도메인 관리자 계정에 설치 하는 동안 제공한 동일한 암호를 입력 합니다.

    > [!NOTE]
    > ERCS 끝점에 연결할 수 없는 경우 두 단계를 이미 않았다면 연결할 ERCS VM의 IP 주소를 사용 하 여 다시 시도 하세요.

3.  프롬프트 바뀌어에 연결한 후 **[*IP 주소 또는 ERCS VM 이름*]: PS >** 또는 **[azs ercs01]: PS >** 환경에 따라 합니다. 여기에서 실행 `Get-Command` 사용 가능한 cmdlet 목록을 볼 수 있습니다.

    통합된 시스템 환경 (예: 데이터 센터 통합과 관련 된 cmdlet)에 대해서만 이러한 cmdlet 중 많은 수는 있습니다. ASDK에서 다음 cmdlet 유효성이 검증 되었습니다.

    - Clear-host
    - Close-PrivilegedEndpoint
    - Exit-pssession
    - Get-AzureStackLog
    - Get-AzureStackStampInformation
    - Get-Command
    - Get-FormatData
    - Get-Help
    - Get-ThirdPartyNotices
    - 측정값 개체
    - New-CloudAdminUser
    - Out-default
    - Remove-CloudAdminUser
    - 개체 선택
    - Set-CloudAdminUserPassword
    - Test-AzureStack
    - Stop-AzureStack
    - Get-ClusterLog

## <a name="tips-for-using-the-privileged-endpoint"></a>권한 있는 끝점을 사용 하기 위한 팁 

PEP는 위에서 설명한 대로 [PowerShell JEA](https://docs.microsoft.com/powershell/jea/overview) 끝점입니다. 강력한 보안 계층을 제공 하는 동안 JEA 끝점을 스크립팅 또는 탭 완성 기능 등 기본 PowerShell 기능 중 일부를 줄입니다. 모든 유형의 스크립트 작업을 시도 하면 작업이 실패 하 고 오류 **ScriptsNotAllowed**합니다. 이는 정상적인 동작입니다.

따라서 예를 들어 특정된 cmdlet에 대 한 매개 변수 목록을 가져오려고 하면 다음 명령을 실행 합니다.

```PowerShell
    Get-Command <cmdlet_name> -Syntax
```

사용할 수 있습니다 합니다 [Import-pssession](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Import-PSSession?view=powershell-5.1) PEP cmdlet은 로컬 컴퓨터에서 현재 세션으로 가져오려면 cmdlet. 이 작업을 수행 하면 모든 cmdlet 아 기능을 PEP의 됩니다 탭 완성 기능 등을 함께 로컬 컴퓨터에서 사용할 수 있는 일반적으로 스크립팅 합니다. 

로컬 컴퓨터의 PEP 세션을 가져오려면 다음 단계를 수행 합니다.

1. 트러스트를 설정 합니다.

    통합된 시스템에서의 PEP 하드웨어 수명 주기 호스트 또는 권한 있는 액세스 워크스테이션에서 실행 하는 확정 된 가상 컴퓨터의 신뢰할 수 있는 호스트를 추가 하려면 관리자 권한 Windows PowerShell 세션에서 다음 명령을 실행 합니다.

      ````PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ````
    - 개발 키트 호스트에는 ADSK를 실행 하는 경우에 로그인 합니다.

2. 하드웨어 수명 주기 호스트 또는 권한 있는 액세스 워크스테이션에서 실행 되는 확정 된 가상 컴퓨터에서 Windows PowerShell 세션을 엽니다. PEP를 호스팅하는 가상 컴퓨터에서 원격 세션을 설정 하려면 다음 명령을 실행 합니다.
 
    - 통합 시스템:
      ````PowerShell
        $cred = Get-Credential

        $session = New-PSSession -ComputerName <IP_address_of_ERCS> `
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ````
      `ComputerName` 매개 변수는 PEP를 호스팅하는 가상 컴퓨터 중 하나의 DNS 이름 또는 IP 주소 수 있습니다. 
    - ADSK 실행 하는 경우:
     
      ````PowerShell
       $cred = Get-Credential

       $session = New-PSSession -ComputerName azs-ercs01 `
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```` 
   메시지가 표시 되 면 다음 자격 증명을 사용 합니다.

      - **사용자 이름**: CloudAdmin 계정 형식으로 지정  **&lt; *Azure Stack 도메인*&gt;\cloudadmin**합니다. (ASDK, 사용자 이름은 **azurestack\cloudadmin**.)
      - **암호**: AzureStackAdmin 도메인 관리자 계정에 설치 하는 동안 제공한 동일한 암호를 입력 합니다.

3. 로컬 컴퓨터에 PEP 세션 가져오기
    ````PowerShell 
        Import-PSSession $session
    ````
4. 이제 탭 완성 기능을 사용할 수 있으며 Azure Stack의 보안 태세를 낮추던 모든 함수 및 PEP의 cmdlet을 사용 하 여 로컬 PowerShell 세션에서 일반적으로 스크립팅을 수행. 마음껏 즐기세요!


## <a name="close-the-privileged-endpoint-session"></a>권한 있는 끝점 세션 닫기

 앞에서 설명한 대로 모든 작업 (및 해당 출력) PowerShell 세션에서 수행 하는 PEP 기록 합니다. 사용 하 여 세션을 닫아야 합니다 `Close-PrivilegedEndpoint` cmdlet. 이 cmdlet은 끝점을 올바르게 닫고 보존에 대 한 외부 파일 공유에 로그 파일을 전송 합니다.

끝점 세션을 닫아야 합니다.

1. PEP에서 액세스할 수 있는 외부 파일 공유를 만듭니다. 개발 키트 환경에서 개발 키트 호스트에만 파일 공유를 만들 수 있습니다.
2. 실행 된 `Close-PrivilegedEndpoint` cmdlet. 
3. 기록 로그 파일을 저장 하는 경로 대해 묻는 메시지가 나타납니다. 형식으로 이전에 만든 파일 공유를 지정 &#92; &#92; *servername*&#92;*sharename*. 경로 지정 하지 않으면 cmdlet은 실패 하 고 세션을 열린 상태로 유지 됩니다. 

    ![대 본 대상 경로 지정 하는 위치를 보여 주는 닫기 PrivilegedEndpoint cmdlet 출력](media/azure-stack-privileged-endpoint/closeendpoint.png)

대 본 로그 파일을 파일 공유에 성공적으로 전송할지, 후 자동으로 PEP에서 삭제 합니다. 

> [!NOTE]
> Cmdlet을 사용 하 여 PEP 세션을 닫으면 `Exit-PSSession` 또는 `Exit`, 또는 PowerShell 콘솔을 닫습니다, 파일 공유에 기록 로그를 전송 하지 않습니다. PEP에서 계속 합니다. 다음에 실행할 때 `Close-PrivilegedEndpoint` 이전 기록 로그 세션 전송도, 파일 공유를 포함 합니다. 사용 하지 마세요 `Exit-PSSession` 하거나 `Exit` PEP 세션을 닫으려면 사용 `Close-PrivilegedEndpoint` 대신 합니다.


## <a name="next-steps"></a>다음 단계
[Azure Stack의 진단 도구](azure-stack-diagnostics.md)
