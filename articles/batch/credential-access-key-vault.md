---
title: 일괄 처리 - Azure 일괄 처리를 통해 키 볼트에 안전하게 액세스
description: Azure Batch를 사용하여 키 자격 증명 모음에서 프로그래밍 방식으로 자격 증명에 액세스하는 방법을 알아봅니다.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.workload: big-compute
ms.topic: article
ms.date: 02/13/2020
ms.author: lahugh
ms.openlocfilehash: 0134e7d92ddca9bd3b45abaf642f33de9d209b33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192305"
---
# <a name="securely-access-key-vault-with-batch"></a>Batch를 사용하여 Key Vault에 안전하게 액세스

이 문서에서는 Azure Key Vault에 저장된 자격 증명에 안전하게 액세스하도록 일괄 처리 노드를 설정하는 방법을 알아봅니다. 관리자 자격 증명을 Key Vault에 넣은 다음 스크립트에서 키 볼트에 액세스하기 위해 자격 증명을 하드 코딩할 필요가 없습니다. 해결 방법은 Batch 노드에 키 볼트에 대한 액세스 권한을 부여하는 인증서를 사용하는 것입니다. 몇 가지 단계를 통해 Batch에 대한 보안 키 저장소를 구현할 수 있습니다.

일괄 처리 노드에서 Azure 키 볼트를 인증하려면 다음이 필요합니다.

- Azure Active 디렉터리(Azure AD) 자격 증명
- 인증서
- 일괄 처리 계정
- 노드가 하나 이상 있는 일괄 처리 풀

## <a name="obtain-a-certificate"></a>인증서 받기

인증서가 아직 없는 경우 가장 쉬운 방법은 명령줄 도구를 사용하여 자체 서명된 `makecert` 인증서를 생성하는 것입니다.

일반적으로 이 `makecert` 경로에서 찾을 `C:\Program Files (x86)\Windows Kits\10\bin\<arch>`수 있습니다. 관리자로 명령 프롬프트를 `makecert` 열고 다음 예제를 사용하여 이동합니다.

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

다음으로 이 `makecert` 도구를 사용하여 자체 서명된 `batchcertificate.cer` 인증서 `batchcertificate.pvk`파일인 및 을 만듭니다. CN(일반 이름)은 이 응용 프로그램에 중요하지 않지만 인증서가 사용되는 것을 알려주는 것으로 만드는 것이 좋습니다.

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org" batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

일괄 처리에는 파일이 `.pfx` 필요합니다. [pvk2pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) 도구를 사용하여 만든 `.cer` `.pvk` 파일과 `makecert` 파일을 단일 `.pfx` 파일로 변환합니다.

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>서비스 주체 만들기

키 자격 증명 모음에 대한 액세스는 **사용자** 또는 **서비스 주체에게**부여됩니다. 키 볼트에 프로그래밍 방식으로 액세스하려면 이전 단계에서 만든 인증서와 함께 서비스 주체를 사용합니다.

Azure 서비스 보안 주체에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 및 서비스 주체 개체를](../active-directory/develop/app-objects-and-service-principals.md)참조하십시오.

> [!NOTE]
> 서비스 주체는 키 자격 증명 모음과 동일한 Azure AD 테넌트에 있어야 합니다.

```powershell
$now = [System.DateTime]::Parse("2020-02-10")
# Set this to the expiration date of the certificate
$expirationDate = [System.DateTime]::Parse("2021-02-10")
# Point the script at the cer file you created $cerCertificateFilePath = 'c:\temp\batchcertificate.cer'
$cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$cer.Import($cerCertificateFilePath)
# Load the certificate into memory
$credValue = [System.Convert]::ToBase64String($cer.GetRawCertData())
# Create a new AAD application that uses this certificate
$newADApplication = New-AzureRmADApplication -DisplayName "Batch Key Vault Access" -HomePage "https://batch.mydomain.com" -IdentifierUris "https://batch.mydomain.com" -certValue $credValue -StartDate $now -EndDate $expirationDate
# Create new AAD service principal that uses this application
$newAzureAdPrincipal = New-AzureRmADServicePrincipal -ApplicationId $newADApplication.ApplicationId
```

키 볼트 액세스에만 사용하기 때문에 응용 프로그램의 URL은 중요하지 않습니다.

## <a name="grant-rights-to-key-vault"></a>키 볼트에 대한 권한 부여

이전 단계에서 만든 서비스 주체는 Key Vault에서 비밀을 검색할 수 있는 권한이 필요합니다. 권한은 Azure 포털또는 아래 의 PowerShell 명령을 통해 부여할 수 있습니다.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>Batch 계정에 인증서 할당

일괄 처리 풀을 만든 다음 풀의 인증서 탭으로 이동하여 만든 인증서를 할당합니다. 이제 인증서가 모든 Batch 노드에 있습니다.

그런 다음 Batch 계정에 인증서를 할당해야 합니다. 인증서를 계정에 할당하면 풀에 할당한 다음 노드에 할당할 수 있습니다. 이 작업을 수행하는 가장 쉬운 방법은 포털의 Batch 계정으로 이동하여 **인증서로**이동한 다음 **에 추가를**선택하는 것입니다. 인증서 `.pfx` [획득에서](#obtain-a-certificate) 생성한 파일을 업로드하고 암호를 입력합니다. 완료되면 인증서가 목록에 추가되고 지문을 확인할 수 있습니다.

이제 Batch 풀을 만들 때 풀 내의 **인증서로** 이동하여 만든 인증서를 해당 풀에 할당할 수 있습니다. 이렇게 하면 저장소 위치에 대한 **LocalMachine을** 선택해야 합니다. 인증서는 풀의 모든 Batch 노드에 로드됩니다.

## <a name="install-azure-powershell"></a>Azure Powershell 설치

노드에서 PowerShell 스크립트를 사용하여 키 자격 증명 모음에 액세스하려는 경우 Azure PowerShell 라이브러리를 설치해야 합니다. 노드에 WMF(Windows 관리 프레임워크) 5가 설치된 경우 설치 모듈 명령을 사용하여 다운로드할 수 있습니다. WMF 5가 없는 노드를 사용하는 경우 설치가 가장 쉬운 방법은 Batch 파일과 `.msi` 함께 Azure PowerShell 파일을 번들로 묶은 다음 설치 관리자를 Batch 시작 스크립트의 첫 번째 부분으로 호출하는 것입니다. 자세한 내용은 이 예제를 참조하십시오.

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>Key Vault에 액세스합니다.

이제 Batch 노드에서 실행되는 스크립트에서 키 볼트에 액세스하도록 설정되었습니다. 스크립트에서 Key Vault에 액세스하려면 인증서를 사용하여 스크립트가 Azure AD에 대해 인증하기만 하면 됩니다. PowerShell에서 이 작업을 수행하려면 다음 예제 명령을 사용합니다. **지문,** **앱 ID(서비스** 주체의 ID) 및 **테넌트 ID(서비스** 주체가 있는 테넌트)에 적합한 GUID를 지정합니다.

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

인증되면 평소와 같이 KeyVault에 액세스합니다.

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

스크립트에서 사용할 자격 증명입니다.
