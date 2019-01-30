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
ms.date: 12/18/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 12/18/2018
ms.openlocfilehash: 09988009712f9312eb97d5c32dc8991ec5b2f1f9
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251353"
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
- 관리자 KeyVault
- KeyVault
- 관리 확장 호스트
- ACS (blob, 테이블 및 큐 저장소 포함)
- ADFS *
- 그래프 *

\* 환경의 id 공급자는 Active Directory Federated Services (AD FS) 하는 경우에 적용할 수 있습니다.

> [!Note]
> 다른 모든 보안 키 및 BMC를 포함 하 여 문자열 및 암호를 전환, 사용자 및 관리자 계정 암호 관리자가 수동으로 업데이트 됩니다.

> [!Important]
> Azure Stack의 1811 버전부터 비밀 회전 내부 및 외부 인증서에 대 한 분리 되었습니다.

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

<sup>*</sup>공용 인증 기관 Windows 신뢰할 수 있는 루트 프로그램의 일부인 해당 됨을 나타냅니다. 문서의 전체 목록을 찾을 수 있습니다 [Microsoft 신뢰할 수 있는 루트 인증서 프로그램: (일부 터 2017 년 6 월 27 일) 참가자](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca)합니다.

## <a name="alert-remediation"></a>경고 수정

암호 만료 후 30 일 내 인 경우 다음 경고는 관리자 포털에서 생성 됩니다.

- 보류 중인 서비스 계정 암호 만료
- 보류 중인 내부 인증서 만료
- 보류 중인 외부 인증서 만료

이러한 경고 해결 아래 지침을 사용 하 여 비밀 회전을 실행 합니다.

> [!Note]
> 1811 이전 버전의 azure Stack 환경 내부 인증서 또는 암호 만료 보류에 대 한 경고 표시 될 수 있습니다.
> 이러한 경고는 정확 하지 않으며 내부 비밀 회전을 실행 하지 않고 무시 해야 합니다.
> 부정확 한 내부 비밀 만료 경고는 2 년에 대 한 환경 활성화 되지 않은 비밀 만료 되지 것입니다 1811 – 내부에 확인 되는 알려진된 문제입니다.

## <a name="pre-steps-for-secret-rotation"></a>비밀 회전에 대 한 사전 단계

   > [!IMPORTANT]
   > Azure Stack 환경에서 이미 수행 된 비밀 회전 1811 또는 비밀 회전에 다시 실행 하기 전에 최신 버전으로 시스템을 업데이트 해야 합니다.
   > 통해 비밀 회전 하 여 실행 되어야 합니다는 [Privileged 끝점](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint) 및 Azure Stack 운영자 자격 증명이 필요 합니다.
   > 비밀 회전에 다시 실행 하기 전에 환경의 Azure Stack 운영자 환경에서 암호 회전 실행 했는지 알지 못하는 경우 1811를 업데이트 합니다.

1. 1811 버전으로 Azure Stack 인스턴스를 업데이트 하는 것이 좋습니다.

    > [!Note] 
    > 사전 1811 버전에 대 한 확장 호스트 인증서를 추가 하는 암호를 회전할 필요가 없습니다. 문서의 지침을 따라야 [Azure Stack에 대 한 확장 호스트에 대 한 준비](azure-stack-extension-host-prepare.md) 확장 호스트 인증서를 추가 합니다.

2. 연산자에는 경고 열기 및 Azure Stack 암호 회전 하는 동안 자동으로 닫기 알 수 있습니다.  이 동작이 예상 되 고 경고를 무시할 수 있습니다.  연산자를 실행 하 여 이러한 경고의 유효성을 확인할 수 있습니다 **테스트 AzureStack**합니다.  SCOM 모니터링을 사용 하 여 운영자에 대 한 시스템 유지 관리 모드로 배치 하 고 Azure Stack 시스템을 이러한 경고는 ITSM 시스템에 도달 하지 못하도록 없게 되지만 Azure Stack 시스템에 연결할 수 없는 경우 경고를 발생 시 계속 됩니다.

3. 유지 관리 작업의 사용자를 게 알립니다. 일반 유지 관리 기간을 최대한, 업무 외 시간을 예약 합니다. 유지 관리 작업 사용자 워크 로드와 포털 작업에 영향을 줄 수 있습니다.

    > [!Note]
    > 다음 단계는 Azure Stack 외부 암호를 회전 하는 경우에 적용 됩니다.

4. 실행할 **[테스트 AzureStack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test)** 암호를 회전 하기 전에 모든 테스트 출력 정상이 확인 합니다.
5. 교체 집합을 새 외부 인증서를 준비 합니다. 새로운 집합에 설명 된 인증서 사양과 일치 합니다 [Azure Stack PKI 인증서 요구 사항](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs)합니다. 에 설명 된 단계를 사용 하 여 새 인증서를 만들거나 구매에 대 한 인증서 서명 요청 (CSR ()를 생성할 수 있습니다 [PKI 인증서를 생성할](https://docs.microsoft.com/azure/azure-stack/azure-stack-get-pki-certs) 단계를사용하여AzureStack환경에서사용하도록준비하고[ Azure Stack PKI 인증서를 준비](https://docs.microsoft.com/azure/azure-stack/azure-stack-prepare-pki-certs)합니다. 에 설명 된 단계를 사용 하 여 준비 하는 인증서의 유효성을 검사 해야 [PKI 인증서의 유효성을 검사](https://docs.microsoft.com/azure/azure-stack/azure-stack-validate-pki-certs)합니다.
6. 백업 회전 안전한 백업 위치에 사용 된 인증서를 저장 합니다. 회전에 실행 되 고 실패 하면 다음을 하는 경우는 회전을 다시 실행 하기 전에 백업 복사본을 사용 하 여 파일 공유에 인증서를 대체 합니다. 참고, 안전한 백업 위치에 백업 복사본을 보관 합니다.
7. ERCS Vm에서 액세스할 수 있습니다 하는 파일 공유를 만듭니다. 파일 공유를 읽을 수 있는 고에 대 한 쓰기 가능 해야 합니다 **CloudAdmin** identity입니다.
8. 파일 공유에 액세스할 수 있는 컴퓨터에서 PowerShell ISE 콘솔을 엽니다. 에 파일 공유로 이동 합니다.
9. 실행할 **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)** 외부 인증서에 대 한 필수 디렉터리를 만들려고 합니다.

> [!IMPORTANT]
> CertDirectoryMaker 스크립트는 준수 하는 폴더 구조를 만듭니다.
>
> **.\Certificates\AAD** 나 ***.\Certificates\ADFS*** Azure Stack에 사용 되는 Id 공급자에 따라
>
> 로 끝나는 폴더 구조에 가장 중요 한 것 **AAD** 하거나 **ADFS** 폴더와 모든 하위 디렉터리는이 구조 내에서 고, 그렇지 **시작-SecretRotation**으로 표시 됩니다.
> ```PowerShell
> Cannot bind argument to parameter 'Path' because it is null.
> + CategoryInfo          : InvalidData: (:) [Test-Certificate], ParameterBindingValidationException
> + FullyQualifiedErrorId : ParameterArgumentValidationErrorNullNotAllowed,Test-Certificate
> + PSComputerName        : xxx.xxx.xxx.xxx
> ```
>
> 알 수 있듯이 프로그램 파일 공유에 액세스 하는 문제가 이지만 실제로 여기에 적용 되는 폴더 구조는 오류 메시지가 나타냅니다.
> 자세한 내용은 Microsoft AzureStack 준비 상태 검사기-에서 찾을 수 있습니다 [PublicCertHelper 모듈](https://www.powershellgallery.com/packages/Microsoft.AzureStack.ReadinessChecker/1.1811.1101.1/Content/CertificateValidation%5CPublicCertHelper.psm1)
>
> 파일 공유 폴더 구조의 시작 해야 이기도 **인증서** 폴더가 고 그렇지 유효성 검사에 실패할 수도 있습니다.
> 파일 공유 탑재 같습니다 **\\ \\ \<IPAddress >\\\<ShareName >\\** 폴더를 포함 해야 하 고  **Certificates\AAD** 나 **Certificates\ADFS** 내에서.
>
> 예: 
> - Fileshare = **\\\\\<IPAddress>\\\<ShareName>\\**
> - CertFolder = **Certificates\AAD**
> - FullPath = **\\\\\<IPAddress>\\\<ShareName>\Certificates\AAD**

## <a name="rotating-external-secrets"></a>외부 암호를 회전합니다.

외부 암호를 회전 합니다.

1. 새로 만든 내 **\Certificates\\\<IdentityProvider >** 전 단계에서 만든 디렉터리에 따라 디렉터리 구조에 새 대체 외부 인증서 집합을 배치 합니다 필수 인증서 섹션에 설명 된 형식으로 된 [Azure Stack PKI 인증서 요구 사항](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates)합니다.

    AAD Id 공급자에 대 한 폴더 구조의 예:
    ```PowerShell
        <ShareName>
        │   │
        │   ├───Certificates
        │   └───AAD
        │       ├───ACSBlob
        │       │       <CertName>.pfx
        │       │
        │       ├───ACSQueue
        │       │       <CertName>.pfx
        │       │
        │       ├───ACSTable
        │       │       <CertName>.pfx
        │       │
        │       ├───Admin Extension Host
        │       │       <CertName>.pfx
        │       │
        │       ├───Admin Portal
        │       │       <CertName>.pfx
        │       │
        │       ├───ARM Admin
        │       │       <CertName>.pfx
        │       │
        │       ├───ARM Public
        │       │       <CertName>.pfx
        │       │
        │       ├───KeyVault
        │       │       <CertName>.pfx
        │       │
        │       ├───KeyVaultInternal
        │       │       <CertName>.pfx
        │       │
        │       ├───Public Extension Host
        │       │       <CertName>.pfx
        │       │
        │       └───Public Portal
        │               <CertName>.pfx

    ```

2. PowerShell 세션을 만듭니다는 [Privileged 끝점](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint) 사용 하 여 합니다 **CloudAdmin** 계정 및 세션 변수로 저장 합니다. 이 변수는 다음 단계에서 매개 변수로 사용 합니다.

    > [!IMPORTANT]  
    > 입력 세션, 세션 변수로 저장 하지 않습니다.

3. 실행할  **[Invoke-command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/Invoke-Command?view=powershell-5.1)** 합니다. 권한 있는 끝점 PowerShell 세션 변수를 전달 합니다 **세션** 매개 변수입니다.

4. 실행할 **시작 SecretRotation** 다음 매개 변수를 사용 하 여:
    - **PfxFilesPath**  
    이전에 만든 인증서 디렉터리에 대 한 네트워크 경로 지정 합니다.  
    - **PathAccessCredential**  
    공유에 대 한 자격 증명을 PSCredential 개체입니다.
    - **CertificatePassword**  
    보안 문자열에 만든 pfx 인증서 파일을 모두 사용 되는 암호입니다.

5. 암호를 회전 하는 동안 기다립니다. 외부 비밀 회전에는 일반적으로 약 1 시간이 걸립니다.

    콘솔 표시를 비밀 회전 성공적으로 완료 되 면 **전체 작업 상태: 성공**합니다.

    > [!Note]
    > 비밀 회전 실패 하면 오류 메시지의 지침에 따라 하 고 다시 실행 **시작 SecretRotation** 사용 하 여 합니다 **-다시 실행** 매개 변수입니다.

    ```PowerShell
    Start-SecretRotation -ReRun
    ```
    비밀 회전 오류가 반복 하는 발생 하는 경우 지원에 문의 하세요.

6. 비밀 회전을 성공적으로 완료 한 후 전 단계에서 만든 공유에서 인증서를 제거 하 고 안전한 백업 위치에 저장 합니다.

## <a name="walkthrough-of-secret-rotation"></a>비밀 회전 연습

다음 PowerShell 예제에서는 cmdlet 및 암호를 회전 하기 위해 실행 매개 변수를 보여 줍니다.

```PowerShell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Run Secret Rotation
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
Invoke-Command -Session $PEPSession -ScriptBlock {
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

## <a name="rotating-only-internal-secrets"></a>내부 비밀만 회전

> [!Note]
> 내부 암호 회전 (빌드 1811 이상)에 대 한 경고 표시 되는 내부 인증서 만료가 발생 한 경우 또는 내부 암호를 공격자가 공격, 악의적인 엔터티를 생각 하는 경우에 수행 되어야 합니다.
> 1811 이전 버전의 azure Stack 환경 내부 인증서 또는 암호 만료 보류에 대 한 경고 표시 될 수 있습니다.
> 이러한 경고는 정확 하지 않으며 내부 비밀 회전을 실행 하지 않고 무시 해야 합니다.
> 부정확 한 내부 비밀 만료 경고는 2 년에 대 한 환경 활성화 되지 않은 비밀 만료 되지 것입니다 1811 – 내부에 확인 되는 알려진된 문제입니다.

1. PowerShell 세션을 만듭니다는 [Privileged 끝점](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint)합니다.
2. 권한 있는 끝점 세션에서 실행할 **시작 SecretRotation-내부**합니다.

    > [!Note]
    > Azure Stack 환경 사전 1811 버전에서 필요 하지 것입니다 합니다 **-내부** 플래그입니다. **시작-SecretRotation** 만 내부 암호를 회전 합니다.

3. 암호를 회전 하는 동안 기다립니다.

콘솔 표시를 비밀 회전 성공적으로 완료 되 면 **전체 작업 상태: 성공**합니다.
    > [!Note]
    > If secret rotation fails, follow the instructions in the error message and rerun **Start-SecretRotation** with the  **–Internal** and **-ReRun** parameters.  

```PowerShell
Start-SecretRotation -Internal -ReRun
```

비밀 회전 오류가 반복 하는 발생 하는 경우 지원에 문의 하세요.

## <a name="start-secretrotation-reference"></a>시작-SecretRotation 참조

Azure Stack 시스템의 암호를 회전합니다. Azure Stack 권한 있는 끝점에 대해 실행만 합니다.

### <a name="syntax"></a>구문

#### <a name="for-external-secret-rotation"></a>외부 비밀 회전

```PowerShell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential <PSCredential>] [-CertificatePassword <SecureString>]  
```

#### <a name="for-internal-secret-rotation"></a>내부 비밀 회전

```PowerShell
Start-SecretRotation [-Internal]  
```

#### <a name="for-external-secret-rotation-rerun"></a>다시 실행 하는 외부 암호 회전

```PowerShell
Start-SecretRotation [-ReRun]
```

#### <a name="for-internal-secret-rotation-rerun"></a>다시 실행 하는 내부 비밀 회전

```PowerShell
Start-SecretRotation [-ReRun] [-Internal]
```

### <a name="description"></a>설명

합니다 **시작 SecretRotation** cmdlet는 Azure Stack 시스템의 인프라 비밀 회전 합니다. 기본적으로 모든 외부 네트워크 인프라 끝점의 인증서만 회전 합니다. 내부-내부 플래그를 사용 하는 경우 인프라 비밀 회전 됩니다. 외부 네트워크 인프라 끝점을 회전 하는 경우 **시작 SecretRotation** 사용 하 여 실행 해야는 **Invoke-command** Azure Stack 환경과 스크립트 블록의 끝점 세션을 권한 있는 로 전달 된 **세션** 매개 변수입니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | Type | 필수 | Position | 기본값 | 설명 |
| -- | -- | -- | -- | -- | -- |
| PfxFilesPath | 문자열  | False  | named  | 없음  | 파일 공유 경로 **\Certificates** 디렉터리 포함 된 모든 외부 네트워크 끝점 인증서입니다. 외부 암호를 회전 하는 경우에 필요 합니다. 최종 디렉터리 여야 합니다 **\Certificates**합니다. |
| CertificatePassword | SecureString | False  | named  | 없음  | -PfXFilesPath에서 제공 하는 모든 인증서에 대 한 암호입니다. 외부 암호 회전 하는 경우 PfxFilesPath가 제공 하는 경우 필수 값입니다. |
| 내부 | 문자열 | False | named | 없음 | Azure Stack 운영자가 내부 인프라 암호를 회전 하려면 언제 든 지 내부 플래그를 사용 해야 합니다. |
| PathAccessCredential | PSCredential | False  | named  | 없음  | 파일 공유에 대 한 PowerShell 자격 증명을 **\Certificates** 디렉터리 포함 된 모든 외부 네트워크 끝점 인증서입니다. 외부 암호를 회전 하는 경우에 필요 합니다.  |
| ReRun | SwitchParameter | False  | named  | 없음  | 비밀 회전 시도 실패 후 다시 시도 언제 든 지 다시 실행을 사용 해야 합니다. |

### <a name="examples"></a>예

#### <a name="rotate-only-internal-infrastructure-secrets"></a>내부 인프라 비밀만 회전

이 통해 Azure Stack에 실행 해야 [환경에는 끝점 권한 있는](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint)합니다.

```PowerShell
PS C:\> Start-SecretRotation -Internal
```

이 명령은 모든 Azure Stack 내부 네트워크에 노출 인프라 암호를 회전 합니다.

#### <a name="rotate-only-external-infrastructure-secrets"></a>외부 인프라 비밀만 회전  

```PowerShell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Create Credentials for the fileshare
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
# Run Secret Rotation
Invoke-Command -Session $PEPSession -ScriptBlock {  
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

이 명령은 Azure Stack의 외부 네트워크 인프라 끝점에 사용 되는 TLS 인증서를 회전 합니다.

#### <a name="rotate-internal-and-external-infrastructure-secrets-pre-1811-only"></a>내부 및 외부 인프라 비밀 회전 (**pre 1811** 만)

> [!IMPORTANT]
> 이 명령은 Azure Stack에만 적용 됩니다 **pre 1811** 처럼 내부 및 외부 인증서 회전 분할 되었습니다.
>
> ***1811 +* 내부 모두 회전할 수 및 외부 인증서를 더 이상!!!**

```PowerShell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Create Credentials for the fileshare
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
# Run Secret Rotation
Invoke-Command -Session $PEPSession -ScriptBlock {
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

이 명령은 모든 Azure Stack의 외부 네트워크 인프라 끝점에 사용 되는 TLS 인증서 뿐만 아니라 Azure Stack 내부 네트워크에 노출 하는 인프라 암호를 회전 합니다. 시작 SecretRotation 모든 스택 생성 된 암호를 회전 하 고 있는 인증서를 제공 하기 때문에 외부 끝점 인증서 회전도 됩니다.  

## <a name="update-the-baseboard-management-controller-bmc-credential"></a>베이스 보드 관리 컨트롤러 (BMC) 자격 증명 업데이트

베이스 보드 관리 컨트롤러 (BMC) 서버의 실제 상태를 모니터링합니다. 사양 및 사용자 계정 이름 및 암호에 BMC의 업데이트에 대 한 지침에는 원래 장비 제조업체 (OEM) 하드웨어 공급 업체에 따라 달라 집니다. 정기적으로 Azure Stack 구성 요소에 대 한 암호를 업데이트 해야 합니다.

1. Azure Stack 물리적 서버에서 BMC에 OEM 지침에 따라 업데이트 합니다. 사용자 계정 이름 및 사용자 환경에서 각 BMC에 대 한 암호에는 동일 해야 합니다.
2. Azure Stack 세션에서 권한 있는 끝점을 엽니다. 자세한 내용은 [권한 있는 끝점을 사용 하 여 Azure Stack에서](azure-stack-privileged-endpoint.md)합니다.
3. 프롬프트에 변경 된 후에 PowerShell **[IP 주소 또는 ERCS VM 이름]: PS >** 또는 **[azs ercs01]: PS >** 실행 환경에 따라 `Set-BmcCredential` 실행 하 여 `Invoke-Command`입니다. 권한 있는 끝점 세션 변수를 매개 변수로 전달 합니다. 예: 

    ```PowerShell
    # Interactive Version
    $PEPIp = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEPCreds = Get-Credential "<Domain>\CloudAdmin" -Message "PEP Credentials"
    $NewBmcPwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString
    $NewBmcUser = Read-Host -Prompt "Enter New BMC user name"

    $PEPSession = New-PSSession -ComputerName $PEPIp -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    Invoke-Command -Session $PEPSession -ScriptBlock {
        # Parameter BmcPassword is mandatory, while the BmcUser parameter is optional.
        Set-BmcCredential -BmcPassword $using:NewBmcPwd -BmcUser $using:NewBmcUser
    }
    Remove-PSSession -Session $PEPSession
    ```

    또한 암호를 사용 하 여 코드 줄으로 정적 PowerShell 버전을 사용할 수 있습니다.

    ```PowerShell
    # Static Version
    $PEPIp = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEPUser = "<Privileged Endpoint user for example Domain\CloudAdmin>"
    $PEPPwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PEPCreds = New-Object System.Management.Automation.PSCredential ($PEPUser, $PEPPwd)
    $NewBmcPwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force
    $NewBmcUser = "<New BMC User name>"

    $PEPSession = New-PSSession -ComputerName $PEPIp -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    Invoke-Command -Session $PEPSession -ScriptBlock {
        # Parameter BmcPassword is mandatory, while the BmcUser parameter is optional.
        Set-BmcCredential -BmcPassword $using:NewBmcPwd -BmcUser $using:NewBmcUser
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="next-steps"></a>다음 단계

[Azure Stack 보안에 자세히 알아보기](azure-stack-security-foundations.md)
