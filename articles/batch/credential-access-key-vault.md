---
title: Batch를 사용하여 Key Vault에 안전하게 액세스
description: Azure Batch를 사용 하 여 Key Vault에서 프로그래밍 방식으로 자격 증명에 액세스 하는 방법을 알아봅니다.
ms.topic: article
ms.date: 02/13/2020
ms.openlocfilehash: d24904c3a539431e8aff420e9fbd8291cddde78a
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117457"
---
# <a name="securely-access-key-vault-with-batch"></a>Batch를 사용하여 Key Vault에 안전하게 액세스

이 문서에서는 Azure Key Vault에 저장 된 자격 증명에 안전 하 게 액세스 하도록 Batch 노드를 설정 하는 방법을 알아봅니다. Key Vault에서 관리자 자격 증명을 입력 한 다음, 스크립트에서 Key Vault 액세스 하기 위한 자격 증명을 하드 코딩 하는 점이 없습니다. 이 솔루션은 일괄 처리 노드에 Key Vault에 대 한 액세스 권한을 부여 하는 인증서를 사용 하는 것입니다. 몇 가지 단계를 수행 하 여 Batch를 위한 보안 키 저장소를 구현할 수 있습니다.

일괄 처리 노드에서 Azure Key Vault에 인증 하려면 다음이 필요 합니다.

- Azure Active Directory (Azure AD) 자격 증명
- 인증서
- Batch 계정
- 노드가 하나 이상 있는 Batch 풀

## <a name="obtain-a-certificate"></a>인증서 받기

인증서가 아직 없는 경우 하나를 가져오는 가장 쉬운 방법은 명령줄 도구를 `makecert` 사용 하 여 자체 서명 된 인증서를 생성 하는 것입니다.

일반적으로이 경로 `makecert` 에서 찾을 수 있습니다 `C:\Program Files (x86)\Windows Kits\10\bin\<arch>`. 관리자 권한으로 명령 프롬프트를 열고 다음 예제를 `makecert` 사용 하 여로 이동 합니다.

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

그런 다음 `makecert` 도구를 사용 하 여 및 `batchcertificate.cer` `batchcertificate.pvk`라는 자체 서명 된 인증서 파일을 만듭니다. 사용 되는 CN (일반 이름)은이 응용 프로그램에 중요 하지 않지만 인증서를 사용 하는 대상을 알려 주는 것이 좋습니다.

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org" batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

일괄 처리에 `.pfx` 파일이 필요 합니다. [Pvk2pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) 도구를 사용 하 여에서 `.cer` `.pvk` `makecert` 만든 및 파일을 단일 `.pfx` 파일로 변환할 수 있습니다.

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>서비스 주체 만들기

**사용자** 또는 **서비스 주체**에 게 Key Vault에 대 한 액세스 권한이 부여 됩니다. Key Vault 프로그래밍 방식으로 액세스 하려면 이전 단계에서 만든 인증서를 사용 하 여 서비스 주체를 사용 합니다.

Azure 서비스 주체에 대 한 자세한 내용은 [Azure Active Directory의 응용 프로그램 및 서비스 주체 개체](../active-directory/develop/app-objects-and-service-principals.md)를 참조 하세요.

> [!NOTE]
> 서비스 사용자는 Key Vault와 동일한 Azure AD 테 넌 트에 있어야 합니다.

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

응용 프로그램에 대 한 Url은 Key Vault 액세스용 으로만 사용 되므로 중요 하지 않습니다.

## <a name="grant-rights-to-key-vault"></a>Key Vault에 대 한 권한 부여

이전 단계에서 만든 서비스 주체에는 Key Vault에서 비밀을 검색할 수 있는 권한이 필요 합니다. 사용 권한은 Azure Portal 또는 아래의 PowerShell 명령을 통해 부여할 수 있습니다.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>Batch 계정에 인증서 할당

Batch 풀을 만든 다음 풀에서 인증서 탭으로 이동 하 여 만든 인증서를 할당 합니다. 이제 인증서가 모든 Batch 노드에 있습니다.

그런 다음, Batch 계정에 인증서를 할당 해야 합니다. 인증서를 계정에 할당 하면 해당 인증서를 풀에 할당 한 다음 노드에 할당할 수 있습니다. 이 작업을 수행 하는 가장 쉬운 방법은 포털에서 Batch 계정으로 이동 하 여 **인증서**로 이동 하 고 **추가**를 선택 하는 것입니다. `.pfx` [인증서 가져오기](#obtain-a-certificate) 에서 생성 한 파일을 업로드 하 고 암호를 제공 합니다. 완료 되 면 인증서가 목록에 추가 되 고 지문을 확인할 수 있습니다.

이제 Batch 풀을 만들 때 풀 내의 **인증서** 로 이동 하 여 만든 인증서를 풀에 할당할 수 있습니다. 이렇게 하려면 저장소 위치에 대해 **LocalMachine** 를 선택 해야 합니다. 인증서는 풀의 모든 Batch 노드에 로드 됩니다.

## <a name="install-azure-powershell"></a>Azure Powershell 설치

노드에서 PowerShell 스크립트를 사용 하 여 Key Vault에 액세스할 계획인 경우 Azure PowerShell 라이브러리가 설치 되어 있어야 합니다. 이 작업을 수행 하는 몇 가지 방법이 있습니다. 노드에 WMF (Windows Management Framework) 5가 설치 되어 있으면 install-module 명령을 사용 하 여 다운로드할 수 있습니다. WMF 5가 없는 노드를 사용 하는 경우이를 설치 하는 가장 쉬운 방법은 배치 파일을 사용 `.msi` 하 여 Azure PowerShell 파일을 번들로 묶어 배치 시작 스크립트의 첫 번째 부분으로 설치 관리자를 호출 하는 것입니다. 자세한 내용은 다음 예제를 참조 하세요.

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>Key Vault에 액세스합니다.

이제 Batch 노드에서 실행 중인 스크립트의 Key Vault에 대 한 액세스를 모두 설정 하겠습니다. 스크립트에서 Key Vault에 액세스 하려면 스크립트가 인증서를 사용 하 여 Azure AD에 대해 인증 하는 데 필요한 모든 것이 필요 합니다. PowerShell에서이 작업을 수행 하려면 다음 예제 명령을 사용 합니다. **지문**, **앱 id** (서비스 주체의 Id) 및 **테 넌 트 id** (서비스 사용자가 존재 하는 테 넌 트)의 적절 한 GUID를 지정 합니다.

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

인증 된 후에는 일반적으로 KeyVault에 액세스 합니다.

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

스크립트에서 사용할 자격 증명입니다.
