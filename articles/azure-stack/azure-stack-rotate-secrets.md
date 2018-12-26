---
title: Azure Stack에서 암호를 회전 | Microsoft Docs
description: Azure Stack에서 암호를 회전 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 8d84801aacfc60bb11aac4c9046a433378a59b79
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52314476"
---
# <a name="rotate-secrets-in-azure-stack"></a>Azure Stack에서 암호를 회전 합니다.

*이러한 지침은 Azure Stack 통합 시스템 버전 1803 이상에 적용 됩니다. Azure Stack 버전 1802 이전 버전에 대 한 비밀 회전 하지 마세요*

Azure Stack은 Azure Stack 인프라 리소스 및 서비스 간의 보안 통신을 유지 하기 위해 다양 한 암호를 사용 합니다.

- **내부 암호**  
모든는 인증서, 암호, 보안 문자열 및 Azure Stack 운영자의 개입 없이 Azure Stack 인프라에서 사용 되는 키입니다. 

- **외부 암호**  
서비스 인증서는 Azure Stack 연산자에서 제공 하는 외부 연결 서비스에 대 한 인프라입니다. 여기에 다음 서비스용 인증서: 
    - 관리자 포털 
    - 공용 포털 
    - 관리자 Azure Resource Manager 
    - 전역 Azure Resource Manager 
    - 관리자 Keyvault 
    - Keyvault 
    - ACS (blob, 테이블 및 큐 저장소 포함) 
    - ADFS<sup>*</sup>
    - 그래프<sup>*</sup>

   <sup>*</sup> 환경의 id 공급자는 Active Directory Federated Services (AD FS) 하는 경우에 적용할 수 있습니다.

> [!NOTE]
> 다른 모든 보안 키 및 BMC를 포함 하 여 문자열 및 암호를 전환, 사용자 및 관리자 계정 암호 관리자가 수동으로 업데이트 됩니다. 

Azure Stack 인프라의 무결성을 유지 하기 위해 연산자에는 조직의 보안 요구 사항에 부합 하는 빈도에 해당 인프라의 암호를 정기적으로 회전 하는 기능이 필요 합니다.

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>새 인증 기관에서 외부 인증서를 사용 하 여 암호를 회전합니다.

Azure Stack 다음 경우에 새 인증 기관 (CA)에서 외부 인증서를 사용 하 여 비밀 회전을 지원합니다.

|설치 된 CA 인증|CA를 회전 하려면|지원됨|지원 되는 azure Stack 버전|
|-----|-----|-----|-----|
|자체 서명 된|Enterprise|지원되지 않음||
|자체 서명 된|자체 서명 된를|지원되지 않음||
|자체 서명 된|공용<sup>*</sup>|지원됨|1803 & 이상|
|엔터프라이즈에서|Enterprise|고객은 동일한 엔터프라이즈 배포에 사용 되는 CA를 사용 하도록 지원|1803 & 이상|
|엔터프라이즈에서|자체 서명 된를|지원되지 않음||
|엔터프라이즈에서|공용<sup>*</sup>|지원됨|1803 & 이상|
|공용에서<sup>*</sup>|Enterprise|지원되지 않음|1803 & 이상|
|공용에서<sup>*</sup>|자체 서명 된를|지원되지 않음||
|공용에서<sup>*</sup>|공용<sup>*</sup>|지원됨|1803 & 이상|

<sup>*</sup> 다음 공용 인증 기관은 Windows 신뢰할 수 있는 루트 프로그램의 일부인 것입니다. 전체 목록을 찾을 수 있습니다 [Microsoft 신뢰할 수 있는 루트 인증서 프로그램: 참가자 (일부 터 2017 년 6 월 27 일)](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca)합니다.

## <a name="alert-remediation"></a>경고 수정

암호 만료 후 30 일 내 인 경우 다음 경고는 관리자 포털에서 생성 됩니다. 

- 보류 중인 서비스 계정 암호 만료 
- 보류 중인 내부 인증서 만료 
- 보류 중인 외부 인증서 만료 

이러한 경고 해결 아래 지침을 사용 하 여 비밀 회전을 실행 합니다.

## <a name="pre-steps-for-secret-rotation"></a>비밀 회전에 대 한 사전 단계

   > [!IMPORTANT]  
   > 환경에서 암호 회전을 성공적으로 실행 되었을 않은 확인 합니다. 비밀 회전을 이미 수행 하는 경우 Azure Stack 1807 또는 비밀 회전을 실행 하기 전에 최신 버전으로 업데이트 합니다. 

1.  연산자에는 경고 열기 및 Azure Stack 암호 회전 하는 동안 자동으로 닫기 알 수 있습니다.  이 동작이 예상 되 고 경고를 무시할 수 있습니다.  연산자는 AzureStack 테스트를 실행 하 여 이러한 경고의 유효성을 확인할 수 있습니다.  SCOM 모니터링을 사용 하 여 운영자에 대 한 시스템 유지 관리 모드로 배치 하 고 Azure Stack 시스템을 이러한 경고는 ITSM 시스템에 도달 하지 못하도록 없게 되지만 Azure Stack 시스템에 연결할 수 없는 경우 경고를 발생 시 계속 됩니다. 
2. 유지 관리 작업의 사용자를 게 알립니다. 일반 유지 관리 기간을 최대한, 업무 외 시간을 예약 합니다. 유지 관리 작업 사용자 워크 로드와 포털 작업에 영향을 줄 수 있습니다.
    > [!note]  
    > 다음 단계는 Azure Stack 외부 암호를 회전 하는 경우에 적용 됩니다.

3. 실행할 **[테스트 AzureStack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test)** 암호를 회전 하기 전에 모든 테스트 출력 정상이 확인 합니다.
4. 교체 집합을 새 외부 인증서를 준비 합니다. 새로운 집합에 설명 된 인증서 사양과 일치 합니다 [Azure Stack PKI 인증서 요구 사항](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs)합니다.
5.  백업 회전 안전한 백업 위치에 사용 된 인증서를 저장 합니다. 회전에 실행 되 고 실패 하면 다음을 하는 경우는 회전을 다시 실행 하기 전에 백업 복사본을 사용 하 여 파일 공유에 인증서를 대체 합니다. 참고, 안전한 백업 위치에 백업 복사본을 보관 합니다.
6.  ERCS Vm에서 액세스할 수 있습니다 하는 파일 공유를 만듭니다. 파일 공유를 읽을 수 있는 고에 대 한 쓰기 가능 해야 합니다 **CloudAdmin** identity입니다.
7.  파일 공유에 액세스할 수 있는 컴퓨터에서 PowerShell ISE 콘솔을 엽니다. 에 파일 공유로 이동 합니다. 
8.  실행할 **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)** 외부 인증서에 대 한 필수 디렉터리를 만들려고 합니다.

## <a name="rotating-external-and-internal-secrets"></a>외부 및 내부 비밀 회전

두 외부 내부 암호를 회전 합니다.

1. 새로 만든 내 **/인증서** 전 단계에서 만든 디렉터리 필수 인증서 섹션에 설명 된 형식에 따라 디렉터리 구조에 새 인증서 집합을 대체 외부 배치 [Azure Stack PKI 인증서 요구 사항](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates)합니다.
2. PowerShell 세션을 만듭니다는 [Privileged 끝점](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint) 사용 하 여 합니다 **CloudAdmin** 계정 및 세션 변수로 저장 합니다. 이 변수는 다음 단계에서 매개 변수로 사용 합니다.

    > [!IMPORTANT]  
    > 입력 세션, 세션 변수로 저장 하지 않습니다.
    
3. 실행할  **[호출 명령](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-5.1)** 합니다. 권한 있는 끝점 PowerShell 세션 변수를 전달 합니다 **세션** 매개 변수입니다. 
4. 실행할 **시작 SecretRotation** 다음 매개 변수를 사용 하 여:
    - **PfxFilesPath**  
    이전에 만든 인증서 디렉터리에 대 한 네트워크 경로 지정 합니다.  
    - **PathAccessCredential**  
    공유에 대 한 자격 증명을 PSCredential 개체입니다. 
    - **CertificatePassword**  
    보안 문자열에 만든 pfx 인증서 파일을 모두 사용 되는 암호입니다.
4. 암호를 회전 하는 동안 기다립니다.  
비밀 회전 성공적으로 완료 되 면 콘솔에 표시 됩니다 **전반적인 작업 상태: 성공**합니다. 
    > [!note]  
    > 비밀 회전 실패 하면 오류 메시지의 지침에 따라 하 고 다시 시작-secretrotation 사용 하 여를 실행 합니다 **-다시 실행** 매개 변수입니다. 비밀 회전 오류가 반복 하는 발생 하는 경우 지원에 문의 하세요. 
5. 비밀 회전을 성공적으로 완료 한 후 전 단계에서 만든 공유에서 인증서를 제거 하 고 안전한 백업 위치에 저장 합니다. 

## <a name="walkthrough-of-secret-rotation"></a>비밀 회전 연습

다음 PowerShell 예제에서는 cmdlet 및 암호를 회전 하기 위해 실행 매개 변수를 보여 줍니다.

```powershell
#Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IPofERCSMachine>"}'
$PEPCreds = Get-Credential 
$PEPsession = New-PSSession -computername <IPofERCSMachine> -Credential $PEPCreds -ConfigurationName PrivilegedEndpoint 

#Run Secret Rotation
$CertPassword = ConvertTo-SecureString "Certpasswordhere" -AsPlainText -Force
$CertShareCred = Get-Credential 
$CertSharePath = "<NetworkPathofCertShare>"
Invoke-Command -session $PEPsession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword }
Remove-PSSession -Session $PEPSession
```
## <a name="rotating-only-internal-secrets"></a>내부 비밀만 회전

Azure Stack의 내부 비밀만을 회전 합니다.

1. PowerShell 세션을 만듭니다는 [Privileged 끝점](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint)합니다.
2. 권한 있는 끝점 세션에서 실행할 **시작 SecretRotation** 인수 없이 합니다.
3. 암호를 회전 하는 동안 기다립니다.  
비밀 회전 성공적으로 완료 되 면 콘솔에 표시 됩니다 **전반적인 작업 상태: 성공**합니다. 
    > [!note]  
    > 비밀 회전 실패 하면 오류 메시지의 지침에 따라 하 고 사용 하 여 시작 secretrotation 다시 실행 합니다 **-다시 실행** 매개 변수입니다. 비밀 회전 오류가 반복 하는 발생 하는 경우 지원에 문의 하세요. 

## <a name="start-secretrotation-reference"></a>시작-SecretRotation 참조

Azure Stack 시스템의 암호를 회전합니다. Azure Stack 권한 있는 끝점에 대해 실행만 합니다.

### <a name="syntax"></a>구문

경로 (기본값)

```powershell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential] <PSCredential> [-CertificatePassword <SecureString>]  
```

### <a name="description"></a>설명

시작-SecretRotation cmdlet은 Azure Stack 시스템의 인프라 비밀 회전합니다. 내부 인프라 네트워크에 노출 하는 모든 암호 회전 기본적으로 사용자 입력을 사용 하 여 회전 모든 외부 네트워크 인프라 끝점의 인증서입니다. 외부 네트워크 인프라 끝점, 회전 하는 경우 시작 SecretRotation Invoke-command 스크립트 블록을 통해 세션 매개 변수로 전달 하는 Azure Stack 환경 권한 있는 끝점 세션을 사용 하 여 실행 합니다.
 
### <a name="parameters"></a>매개 변수

| 매개 변수 | type | 필수 | Position | 기본값 | 설명 |
| -- | -- | -- | -- | -- | -- |
| PfxFilesPath | 문자열  | False  | named  | 없음  | 파일 공유 경로 **\Certificates** 디렉터리 포함 된 모든 외부 네트워크 끝점 인증서입니다. 외부 암호 또는 모든 암호를 회전 하는 경우에 필요 합니다. 최종 디렉터리 여야 합니다 **\Certificates**합니다. |
| CertificatePassword | SecureString | False  | named  | 없음  | -PfXFilesPath에서 제공 하는 모든 인증서에 대 한 암호입니다. 내부 및 외부 비밀 회전 하는 경우 PfxFilesPath가 제공 하는 경우 필수 값입니다. |
| PathAccessCredential | PSCredential | False  | named  | 없음  | 파일 공유에 대 한 PowerShell 자격 증명을 **\Certificates** 디렉터리 포함 된 모든 외부 네트워크 끝점 인증서입니다. 외부 암호 또는 모든 암호를 회전 하는 경우에 필요 합니다.  |
| 다시 실행 | SwitchParameter | False  | named  | 없음  | 비밀 회전 시도 실패 후 다시 시도 언제 든 지 다시 실행을 사용 해야 합니다. |

### <a name="examples"></a>예
 
**내부 인프라 비밀만 회전**

```powershell  
PS C:\> Start-SecretRotation  
```

이 명령은 모든 Azure Stack 내부 네트워크에 노출 인프라 암호를 회전 합니다. 시작-SecretRotation 모든 스택 생성 된 암호를 회전 하지만 제공 된 인증서가 없습니다. 이기 때문에 외부 끝점 인증서 회전 되지 않습니다.  

**내부 및 외부 인프라 비밀 회전**
  
```powershell
PS C:\> Invoke-Command -session $PEPSession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword } 
Remove-PSSession -Session $PEPSession
```

이 명령은 모든 Azure Stack의 외부 네트워크 인프라 끝점에 사용 되는 TLS 인증서 뿐만 아니라 Azure Stack 내부 네트워크에 노출 하는 인프라 암호를 회전 합니다. 시작 SecretRotation 모든 스택 생성 된 암호를 회전 하 고 있는 인증서를 제공 하기 때문에 외부 끝점 인증서 회전도 됩니다.  


## <a name="update-the-baseboard-management-controller-bmc-password"></a>베이스 보드 관리 컨트롤러 (BMC) 암호를 업데이트 합니다.

베이스 보드 관리 컨트롤러 (BMC) 서버의 실제 상태를 모니터링합니다. 사양 및 BMC의 암호를 업데이트 하는 방법은 원래 장비 제조업체 (OEM) 하드웨어 공급 업체에 따라 다릅니다. 일반 주기로 Azure Stack 구성 요소에 대 한 암호를 업데이트 해야 합니다.

1. Azure Stack의 물리적 서버에서 BMC에 OEM 지침에 따라 업데이트 합니다. 사용자 환경에서 각 BMC에 대 한 암호는 같아야 합니다.
2. Azure Stack 세션에서 권한 있는 끝점을 엽니다. 명령 참조 [권한 있는 끝점을 사용 하 여 Azure Stack에서](azure-stack-privileged-endpoint.md)합니다.
3. PowerShell 프롬프트에를 변경 **[IP 주소 또는 ERCS VM 이름]: PS >** 또는 **[azs ercs01]: PS >** 실행 환경에 따라 `Set-BmcPassword` 실행 하 여 `invoke-command`입니다. 권한 있는 끝점 세션 변수를 매개 변수로 전달 합니다. 예: 

    ```powershell
    # Interactive Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PECred = Get-Credential "<Domain>\CloudAdmin" -Message "PE Credentials" 
    $NewBMCpwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    Remove-PSSession -Session $PEPSession
    ```
    
    또한 암호를 사용 하 여 코드 줄으로 정적 PowerShell 버전을 사용할 수 있습니다.
    
    ```powershell
    # Static Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEUser = "<Privileged Endpoint user for example Domain\CloudAdmin>"
    $PEpwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PECred = New-Object System.Management.Automation.PSCredential ($PEUser, $PEpwd) 
    $NewBMCpwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="next-steps"></a>다음 단계

[Azure Stack 보안에 자세히 알아보기](azure-stack-security-foundations.md)
