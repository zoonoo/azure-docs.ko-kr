---
title: Azure 파일 공유에 대 한 AD DS 인증 사용
description: Azure 파일 공유에 대해 SMB를 통한 Active Directory Domain Services 인증을 사용 하도록 설정 하는 방법을 알아봅니다. 그런 다음 도메인에 가입 된 Windows 가상 머신은 AD DS 자격 증명을 사용 하 여 Azure 파일 공유에 액세스할 수 있습니다.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: 612584a71aa6be54d726ccdd74d9368ba9cddbc9
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87535079"
---
# <a name="part-one-enable-ad-ds-authentication-for-your-azure-file-shares"></a>1 부: Azure 파일 공유에 대 한 AD DS 인증 사용 

Active Directory Domain Services (AD DS) 인증을 사용 하도록 설정 하기 전에 [개요 문서](storage-files-identity-auth-active-directory-enable.md) 를 확인 하 여 지원 되는 시나리오와 요구 사항을 이해 해야 합니다.

이 문서에서는 저장소 계정에서 Active Directory Domain Services (AD DS) 인증을 사용 하도록 설정 하는 데 필요한 프로세스를 설명 합니다. 기능을 사용 하도록 설정한 후에는 Azure 파일 공유에 대 한 인증에 AD DS 자격 증명을 사용 하도록 저장소 계정 및 AD DS를 구성 해야 합니다. Azure 파일 공유에 대해 SMB를 통한 AD DS 인증을 사용 하도록 설정 하려면 AD DS에 저장소 계정을 등록 한 후 저장소 계정에 필요한 도메인 속성을 설정 해야 합니다.

저장소 계정을 AD DS에 등록 하려면 AD DS에서 해당 계정을 나타내는 계정을 만듭니다. 이 프로세스는 AD DS에서 온-프레미스 Windows 파일 서버를 나타내는 계정을 만드는 것 처럼 생각할 수 있습니다. 저장소 계정에서이 기능을 사용 하도록 설정 하면 계정에 있는 모든 신규 및 기존 파일 공유에 적용 됩니다.

## <a name="option-one-recommended-use-azfileshybrid-powershell-module"></a>옵션 1 (권장): AzFilesHybrid PowerShell 모듈 사용

AzFilesHybrid PowerShell 모듈의 cmdlet은 필요한 수정 작업을 수행 하 고 기능을 사용 하도록 설정 합니다. Cmdlet의 일부는 온-프레미스 AD DS와 상호 작용 하기 때문에 cmdlet이 수행 하는 작업을 설명 하므로 변경 내용이 준수 및 보안 정책과 일치 하는지 여부를 확인 하 고 cmdlet을 실행할 수 있는 적절 한 권한이 있는지 확인할 수 있습니다. AzFilesHybrid 모듈을 사용 하는 것이 좋지만이 작업을 수행할 수 없는 경우 수동으로 수행할 수 있도록 단계를 제공 합니다.

### <a name="download-azfileshybrid-module"></a>AzFilesHybrid 모듈 다운로드

- [AzFilesHybrid 모듈 다운로드 및 압축 풀기](https://github.com/Azure-Samples/azure-files-samples/releases) (GA 모듈: v 0.2.0 +)
- 대상 AD에서 서비스 로그온 계정 또는 컴퓨터 계정을 만들 수 있는 권한이 있는 AD DS 자격 증명을 사용 하 여 온-프레미스 AD DS에 도메인에 가입 된 장치에 모듈을 설치 하 고 실행 합니다.
-  Azure AD에 동기화 된 온-프레미스 AD DS 자격 증명을 사용 하 여 스크립트를 실행 합니다. 온-프레미스 AD DS 자격 증명에는 저장소 계정 소유자 또는 참가자 Azure 역할 권한이 있어야 합니다.

### <a name="run-join-azstorageaccountforauth"></a>AzStorageAccountForAuth 실행

`Join-AzStorageAccountForAuth`Cmdlet은 지정 된 저장소 계정을 대신 하 여 오프 라인 도메인 조인과 동일한 기능을 수행 합니다. 이 스크립트는 cmdlet을 사용 하 여 AD 도메인에 [컴퓨터 계정을](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) 만듭니다. 어떤 이유로 든 컴퓨터 계정을 사용할 수 없는 경우에는 스크립트를 변경 하 여 [서비스 로그온 계정을](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) 만들 수 있습니다. 명령을 수동으로 실행 하도록 선택 하는 경우 사용자 환경에 가장 적합 한 계정을 선택 해야 합니다.

Cmdlet에서 만든 AD DS 계정은 저장소 계정을 나타냅니다. 암호 만료를 적용 하는 OU (조직 구성 단위)에서 AD DS 계정을 만든 경우 최대 암호 사용 기간 전에 암호를 업데이트 해야 합니다. 이 날짜 이전에 계정 암호를 업데이트 하지 못하면 Azure 파일 공유에 액세스할 때 인증 오류가 발생 합니다. 암호를 업데이트 하는 방법을 알아보려면 [업데이트 AD DS 계정 암호](storage-files-identity-ad-ds-update-password.md)를 참조 하세요.

PowerShell에서 실행 하기 전에 아래 매개 변수에서 자리 표시자 값을 고유한 값으로 바꿉니다.
> [!IMPORTANT]
> 도메인 가입 cmdlet은 ad의 저장소 계정 (파일 공유)을 나타내는 AD 계정을 만듭니다. 컴퓨터 계정 또는 서비스 로그온 계정으로 등록 하도록 선택할 수 있습니다. 자세한 내용은 [FAQ](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) 를 참조 하십시오. 컴퓨터 계정의 경우 AD에 30 일 동안 기본 암호 만료 기간이 설정 되어 있습니다. 마찬가지로, 서비스 로그온 계정에는 AD 도메인 또는 OU (조직 구성 단위)에 대 한 기본 암호 만료 기간이 설정 되어 있을 수 있습니다.
> 두 계정 유형 모두에서 AD 환경에 구성 된 암호 만료 기간을 확인 하 고 최대 암호 사용 기간 전에 AD 계정의 [저장소 계정 id 암호를 업데이트](storage-files-identity-ad-ds-update-password.md) 하도록 계획 하는 것이 좋습니다. [Ad에서 새 AD OU (조직 구성 단위)를 만들고](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps) 이에 따라 [컴퓨터 계정](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)?redirectedfrom=MSDN) 또는 서비스 로그온 계정에 대 한 암호 만료 정책을 사용 하지 않도록 설정할 수 있습니다. 

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer Azure role assignment
Connect-AzAccount

#Define parameters
$SubscriptionId = "<your-subscription-id-here>"
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId $SubscriptionId 

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName. You can choose to provide one of the two names to specify the target OU.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account (default parameter value), depends on the AD permission you have and preference. 
# Run Get-Help Join-AzStorageAccountForAuth for more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -StorageAccountName $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" `
        -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" # If you don't provide the OU name as an input parameter, the AD identity that represents the storage account is created under the root directory.

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, see Azure Files Windows troubleshooting guide.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="option-2-manually-perform-the-enablement-actions"></a>옵션 2: 수동으로 기능 작업 수행

위의 스크립트를 이미 실행 한 경우에는 `Join-AzStorageAccountForAuth` [기능이 사용 하도록 설정 되었는지 확인](#confirm-the-feature-is-enabled) 섹션으로 이동 합니다. 다음 수동 단계를 수행할 필요가 없습니다.

### <a name="checking-environment"></a>환경 확인

먼저, 환경의 상태를 확인 해야 합니다. 특히, PowerShell이 설치 되어 있는지, 그리고 관리자 권한으로 셸이 실행 중인지 [Active Directory](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) 확인 해야 합니다. 그런 다음 [Az.Storage 2.0 모듈](https://www.powershellgallery.com/packages/Az.Storage/2.0.0)이 설치되어 있는지 확인하고, 그렇지 않으면 설치합니다. 이러한 검사를 완료 한 후 AD DS를 확인 하 여 이미 SPN/UPN을 사용 하 여 생성 된 [컴퓨터 계정](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (기본값) 또는 [서비스 로그온 계정이](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) "cifs/your-name-file 계정이 존재 하지 않는 경우 다음 섹션에 설명 된 대로 계정을 만듭니다.

### <a name="creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>수동으로 AD의 저장소 계정을 나타내는 id 만들기

이 계정을 수동으로 만들려면 저장소 계정에 대 한 새 Kerberos 키를 만듭니다. 그런 다음 아래의 PowerShell cmdlet을 사용 하 여 해당 Kerberos 키를 계정의 암호로 사용 합니다. 이 키는 설치 중에만 사용 되며 저장소 계정에 대 한 모든 제어 또는 데이터 평면 작업에는 사용할 수 없습니다. 

```PowerShell
# Create the Kerberos key on the storage account and get the Kerb1 key as the password for the AD identity to represent the storage account
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

New-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -KeyName kerb1
Get-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -ListKerbKey | where-object{$_.Keyname -contains "kerb1"}
```

해당 키가 있으면 OU에서 서비스 또는 컴퓨터 계정을 만듭니다. 다음 사양을 사용 합니다 (예제 텍스트를 저장소 계정 이름으로 대체 해야 합니다.).

SPN: "cifs/저장소-이름-이름-. w i n. w i n. w i n.

OU에서 암호 만료를 적용 하는 경우 Azure 파일 공유에 액세스할 때 인증 오류가 발생 하지 않도록 최대 암호 사용 기간 전에 암호를 업데이트 해야 합니다. 자세한 내용은 [AD에서 저장소 계정 id의 암호 업데이트](storage-files-identity-ad-ds-update-password.md) 를 참조 하세요.

새로 만든 id의 SID를 유지 하 고 다음 단계에 필요 합니다. 저장소 계정을 나타내는 id를 Azure AD에 동기화 할 필요가 없습니다.

### <a name="enable-the-feature-on-your-storage-account"></a>저장소 계정에서 기능을 사용 하도록 설정

이제 저장소 계정에서이 기능을 사용 하도록 설정할 수 있습니다. 다음 명령에서 도메인 속성에 대 한 구성 세부 정보를 제공 하 고 실행 합니다. 다음 명령에 필요한 저장소 계정 SID는 [이전 섹션](#creating-an-identity-representing-the-storage-account-in-your-ad-manually)의 AD DS에서 만든 ID의 sid입니다.

```PowerShell
# Set the feature flag on the target storage account and provide the required AD domain information
Set-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>" `
        -EnableActiveDirectoryDomainServicesForFile $true `
        -ActiveDirectoryDomainName "<your-domain-name-here>" `
        -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" `
        -ActiveDirectoryForestName "<your-forest-name-here>" `
        -ActiveDirectoryDomainGuid "<your-guid-here>" `
        -ActiveDirectoryDomainsid "<your-domain-sid-here>" `
        -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```

### <a name="debugging"></a>디버깅

AzStorageAccountAuth cmdlet을 실행 하 여 로그온 한 AD 사용자를 사용 하 여 AD 구성에 대 한 기본 검사 집합을 수행할 수 있습니다. 이 cmdlet은 AzFilesHybrid v0.1.2 이상 버전에서 지원됩니다. 이 cmdlet에서 수행 하는 검사에 대 한 자세한 내용은 Windows의 문제 해결 가이드에서 [AD 자격 증명을 사용 하 여 Azure Files를 탑재할 수 없음](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials) 을 참조 하세요.

```PowerShell
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="confirm-the-feature-is-enabled"></a>기능이 사용 하도록 설정 되었는지 확인

다음 스크립트를 사용 하 여 저장소 계정에 기능을 사용할 수 있는지 여부를 확인할 수 있습니다.

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD DS authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

## <a name="next-steps"></a>다음 단계

이제 저장소 계정에서이 기능을 사용 하도록 설정 했습니다. 기능을 사용 하려면 공유 수준 권한을 할당 해야 합니다. 다음 섹션으로 계속 진행 합니다.

[2 부: id에 공유 수준 권한 할당](storage-files-identity-ad-ds-assign-permissions.md)
