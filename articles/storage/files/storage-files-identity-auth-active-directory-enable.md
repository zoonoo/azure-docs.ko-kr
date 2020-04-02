---
title: Azure 파일에 대한 SMB를 통해 활성 디렉터리 인증 사용
description: Active Directory를 통해 Azure 파일 공유에 대한 SMB에 대한 ID 기반 인증을 활성화하는 방법을 알아봅니다. 그런 다음 도메인에 가입한 VM(Windows 가상 컴퓨터)은 AD 자격 증명을 사용하여 Azure 파일 공유에 액세스할 수 있습니다.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: rogarana
ms.openlocfilehash: dbcc204f71a9bfe4f48f51ab6af36014e130cb7f
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548942"
---
# <a name="enable-active-directory-authentication-over-smb-for-azure-file-shares"></a>Azure 파일 공유에 대한 SMB를 통해 Active Directory 인증 사용

[Azure Files는](storage-files-introduction.md) 두 가지 유형의 도메인 서비스 인 Azure Active Directory 도메인 서비스(Azure AD DS) 및 AD(AD) (미리 보기)의 두 가지 유형의 도메인 서비스를 통해 SMB(서버 메시지 블록)에 대한 ID 기반 인증을 지원합니다. 이 문서에서는 Azure 파일 공유에 대한 인증을 위해 Active Directory 도메인 서비스를 활용하는 새로 도입된(미리 보기) 지원에 중점을 둡니다. Azure 파일 공유에 대한 AZURE AD DS(Azure AD DS) 인증을 사용하려면 [주제에 대한 도움말을](storage-files-identity-auth-active-directory-domain-service-enable.md)참조하십시오.

> [!NOTE]
> Azure 파일은 Azure Active Directory 도메인 서비스(Azure AD DS) 또는 AD(활성 디렉터리)의 한 도메인 서비스에 대한 지원 인증만 공유합니다. 
>
> Azure 파일 공유 인증에 사용되는 AD ID를 Azure AD에 동기화해야 합니다. 암호 해시 동기화는 선택 사항입니다. 
> 
> AD 인증은 AD에서 만든 컴퓨터 계정에 대한 인증을 지원하지 않습니다. 
> 
> AD 인증은 저장소 계정이 등록된 하나의 AD 포리스트에 대해서만 지원될 수 있습니다. 기본적으로 단일 AD 포리스트의 AD 자격 증명으로 Azure 파일 공유에만 액세스할 수 있습니다. 다른 포리스트에서 Azure 파일 공유에 액세스해야 하는 경우 적절한 포리스트 신뢰가 구성되어 있는지 확인하려면 자세한 내용은 [FAQ를](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) 참조하십시오.  
> 
> SMB 액세스 및 ACL 지속성에 대한 AD 인증은 Azure 파일 동기화에서 관리하는 Azure 파일 공유에 대해 지원됩니다.
>
> Azure 파일은 RC4-HMAC 암호화를 사용한 AD를 통해 Kerberos 인증을 지원합니다. AES Kerberos 암호화는 아직 지원되지 않습니다.

SMB를 통해 Azure 파일 공유에 대한 AD를 사용하도록 설정하면 AD 도메인에 가입한 컴퓨터에서 기존 AD 자격 증명을 사용하여 Azure 파일 공유를 탑재할 수 있습니다. 이 기능은 온프레미 컴퓨터에서 호스팅되거나 Azure에서 호스팅되는 AD 환경에서 사용할 수 있습니다.

Azure 파일 공유에 액세스하는 데 사용되는 AD ID는 표준 [역할 기반 액세스 제어(RBAC)](../../role-based-access-control/overview.md) 모델을 통해 공유 수준 파일 권한을 적용하기 위해 Azure AD에 동기화되어야 합니다. 기존 파일 서버에서 이월된 파일/디렉터리에 대한 [Windows 스타일 DACL은](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) 보존되고 적용됩니다. 이 기능은 엔터프라이즈 AD 도메인 인프라와 원활하게 통합됩니다. 온프레미 파일 서버를 Azure 파일 공유로 바꾸면 기존 사용자는 사용 중 자격 증명을 변경하지 않고 현재 클라이언트의 Azure 파일 공유에 단일 사인온 환경으로 액세스할 수 있습니다.  
 
## <a name="prerequisites"></a>사전 요구 사항 

Azure 파일 공유에 대한 AD 인증을 사용하도록 설정하기 전에 다음 필수 구성 조건을 완료했는지 확인합니다. 

- AD 환경을 선택하거나 만들고 Azure AD에 동기화합니다. 

    새 또는 기존 AD 환경에서 기능을 활성화할 수 있습니다. 액세스에 사용되는 ID는 Azure AD에 동기화되어야 합니다. Azure AD 테넌트와 액세스하는 파일 공유는 동일한 구독과 연결되어 있어야 합니다. 

    AD 도메인 환경을 설정하려면 [활성 디렉터리 도메인 서비스 개요를](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)참조하십시오. AD를 Azure AD에 동기화하지 않은 경우 기본 인증 방법 및 Azure AD Connect 설정 옵션을 확인하려면 [Azure Active Directory와 하이브리드 ID란 무엇입니까?의](../../active-directory/hybrid/whatis-hybrid-identity.md) 지침을 따릅니다. 

- 온-프레미스 컴퓨터 또는 Azure VM을 AD에 도메인 조인(AD DS라고도 함). 

    컴퓨터 또는 VM의 AD 자격 증명을 사용하여 파일 공유에 액세스하려면 장치가 AD에 도메인으로 연결되어 있어야 합니다. AD에 도메인 가입하는 방법에 대한 자세한 내용은 [도메인에 컴퓨터 가입을](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)참조하십시오. 

- [지원되는 지역에서](#regional-availability)Azure 저장소 계정을 선택하거나 만듭니다. 

    파일 공유가 포함된 저장소 계정이 Azure AD DS 인증에 대해 아직 구성되지 않았는지 확인합니다. 저장소 계정에서 Azure 파일 Azure AD DS 인증을 사용하도록 설정한 경우 AD를 사용하도록 변경하기 전에 비활성화해야 합니다. 이는 Azure AD DS 환경에서 구성된 기존 ACL을 적절한 사용 권한 적용을 위해 다시 구성해야 한다는 것을 의미합니다.
    
    새 파일 공유를 만드는 것에 대한 자세한 내용은 [Azure 파일 의 파일 공유 만들기를](storage-how-to-create-file-share.md)참조하십시오.
    
    최적의 성능을 위해 공유에 액세스하려는 VM과 동일한 지역에 저장소 계정을 배포하는 것이 좋습니다. 

- 저장소 계정 키를 사용하여 Azure 파일 공유를 탑재하여 연결을 확인합니다. 

    장치 및 파일 공유가 제대로 구성되었는지 확인하려면 저장소 계정 키를 사용하여 파일 공유를 탑재해 보십시오. 자세한 내용은 [Windows에서 Azure 파일 공유 사용을](storage-how-to-use-files-windows.md)참조하십시오.

## <a name="regional-availability"></a>국가별 가용성

Azure 파일 AD 인증(미리 보기)은 [퍼블릭 클라우드의 모든 리전에서](https://azure.microsoft.com/global-infrastructure/regions/)사용할 수 있습니다.

## <a name="workflow-overview"></a>워크플로 개요

Azure 파일 공유에 대해 SMB를 통해 AD 인증을 사용하도록 설정하기 전에 [필수 구성 조건을](#prerequisites) 살펴보고 모든 단계를 완료했는지 확인하는 것이 좋습니다. 필수 구성 조건은 AD, Azure AD 및 Azure 저장소 환경이 제대로 구성되어 있는지 확인합니다. 

다음으로 AD 자격 증명을 사용하여 Azure Files 리소스에 대한 액세스 권한을 부여합니다. 

- 저장소 계정에서 Azure 파일 AD 인증을 사용하도록 설정합니다.  

- 대상 AD ID와 동기화된 Azure AD ID(사용자, 그룹 또는 서비스 주체)에 공유에 대한 액세스 권한을 할당합니다. 

- 디렉터리 및 파일에 대해 SMB를 통해 ACL을 구성합니다. 

- AD 도메인에 가입한 VM에서 Azure 파일 공유를 마운트합니다. 

다음 다이어그램은 Azure 파일 공유에 대한 SMB에 대한 Azure AD 인증을 사용하도록 설정하는 종단 간 워크플로를 보여 줍니다. 

![파일 AD 워크플로 다이어그램](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> Azure 파일 공유에 대한 SMB에 대한 AD 인증은 Windows 7 또는 Windows Server 2008 R2보다 최신 OS 버전에서 실행되는 컴퓨터 또는 VM에서만 지원됩니다. 

## <a name="enable-ad-authentication-for-your-account"></a>계정에 대한 AD 인증 사용 

Azure 파일 공유에 대한 SMB에 대한 AD 인증을 사용하려면 먼저 저장소 계정을 AD에 등록한 다음 저장소 계정에 필요한 도메인 속성을 설정해야 합니다. 저장소 계정에서 기능을 사용하도록 설정하면 계정의 모든 새 파일 및 기존 파일 공유에 적용됩니다. 기능을 `join-AzStorageAccountForAuth` 활성화하는 데 사용합니다. 아래 섹션에서 종단 간 워크플로우에 대한 자세한 설명을 찾을 수 있습니다. 

> [!IMPORTANT]
> cmdlet은 `Join-AzStorageAccountForAuth` AD 환경을 수정합니다. 다음 설명을 읽고 명령을 실행할 수 있는 적절한 권한이 있고 적용된 변경 내용이 규정 준수 및 보안 정책과 일치하는지 확인하기 위해 수행하는 작업을 더 잘 이해합니다. 

cmdlet은 `Join-AzStorageAccountForAuth` 표시된 저장소 계정을 대신하여 오프라인 도메인 가입과 동등한 작업을 수행합니다. [컴퓨터](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) 계정 또는 [서비스 로그온 계정](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts)중 하나인 AD 도메인에 계정이 생성됩니다. 생성된 AD 계정은 AD 도메인의 저장소 계정을 나타냅니다. 암호 만료를 적용하는 AD 조직 단위(OU)에 따라 AD 계정을 만든 경우 최대 암호 사용 시간 전에 암호를 업데이트해야 합니다. AD 계정 암호를 업데이트하지 못하면 Azure 파일 공유에 액세스할 때 인증 오류가 발생합니다. 암호를 업데이트하는 방법을 알아보려면 [AD 계정 암호 업데이트를](#update-ad-account-password)참조하십시오.

다음 스크립트를 사용하여 등록을 수행하고 기능을 사용하도록 설정하거나 스크립트가 수행하는 작업을 수동으로 수행할 수 있습니다. 이러한 작업은 스크립트 다음 섹션에 설명되어 있습니다. 둘 다 할 필요는 없습니다.

### <a name="1-check-prerequisites"></a>1. 전제 조건 확인
- [AzFiles하이브리드 모듈 다운로드 및 압축 해제](https://github.com/Azure-Samples/azure-files-samples/releases)
- 대상 AD에서 서비스 로그온 계정 또는 컴퓨터 계정을 만들 수 있는 권한이 있는 AD 자격 증명을 사용하여 도메인이 AD에 가입된 장치에서 모듈을 설치하고 실행합니다.
-  Azure AD에 동기화된 AD 자격 증명을 사용하여 스크립트를 실행합니다. AD 자격 증명에는 저장소 계정 소유자 또는 기여자 RBAC 역할 권한이 있어야 합니다.
- 저장소 계정이 [지원되는 지역에](#regional-availability)있는지 확인합니다.

### <a name="2-domain-join-your-storage-account"></a>2. 도메인 가입 저장소 계정
PowerShell에서 실행하기 전에 아래 매개 변수에서 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId "<your-subscription-id-here>"

# Register the target storage account with your active directory environment under the target OU (for example: "OU=ComputersOU,DC=prod,DC=corp,DC=contoso,DC=com")
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account, depends on the AD permission you have and preference. 
Join-AzStorageAccountForAuth `
        -ResourceGroupName "<resource-group-name-here>" `
        -Name "<storage-account-name-here>" `
        -DomainAccountType "ComputerAccount" `
        -OrganizationalUnitName "<ou-name-here>"
```

다음 설명에서는 `Join-AzStorageAccountForAuth` cmdlet이 실행될 때 수행되는 모든 작업을 요약합니다. 명령을 사용하지 않으려면 다음 단계를 수동으로 수행할 수 있습니다.

> [!NOTE]
> 위의 스크립트를 `Join-AzStorageAccountForAuth` 이미 성공적으로 실행한 경우 다음 섹션 "3으로 이동하십시오. 기능이 활성화되어 있는지 확인합니다." 아래 작업을 다시 수행할 필요가 없습니다.

#### <a name="a-checking-environment"></a>a. 환경 확인

먼저 환경을 검사합니다. 특히 [Active Directory PowerShell이](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) 설치되어 있는지, 셸이 관리자 권한으로 실행되고 있는지 확인합니다. 그런 다음 [Az.Storage 1.11.1 미리 보기 모듈이](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) 설치되었는지 확인하고 그렇지 않은 경우 설치합니다. 이러한 수표가 통과하면 AD를 확인하여 SPN/UPN으로 이미 생성된 컴퓨터 계정(기본값) 또는 [서비스 로그온 계정이](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) "cifs/storage-account-name-here.file.net"으로 만들어졌는지 확인합니다. [computer account](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) 계정이 없으면 아래 섹션 b에 설명된 대로 계정을 만듭니다.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. AD에서 저장소 계정을 수동으로 나타내는 ID 만들기

이 계정을 수동으로 만들려면 을 사용하여 `New-AzStorageAccountKey -KeyName kerb1`저장소 계정에 대한 새 kerberos 키를 만듭니다. 그런 다음 해당 kerberos 키를 계정의 암호로 사용합니다. 이 키는 설정하는 동안에만 사용되며 저장소 계정에 대한 제어 또는 데이터 평면 작업에사용할 수 없습니다.

해당 키가 있으면 OU 아래에 서비스 또는 컴퓨터 계정을 만듭니다. 다음 사양을 사용 하 여: SPN: "cifs/귀하의 저장소-계정-이름-here.file.core.windows.net" 암호: 저장소 계정에 대 한 Kerberos 키.

OU에서 암호 만료를 적용하는 경우 Azure 파일 공유에 액세스할 때 인증 실패를 방지하기 위해 암호 가 최대 사용 시간 전에 암호를 업데이트해야 합니다. 자세한 내용은 [AD 계정 암호 업데이트를](#update-ad-account-password) 참조하십시오.

새로 만든 계정의 SID를 유지, 다음 단계에 대 한 필요 합니다. 저장소 계정을 나타내는 방금 만든 AD ID를 Azure AD에 동기화할 필요가 없습니다.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>다. 저장소 계정의 기능 사용

그런 다음 스크립트를 사용하여 저장소 계정의 기능을 활성화합니다. 이 설정을 수동으로 수행하려면 다음 명령에서 도메인 속성에 대한 몇 가지 구성 세부 정보를 제공한 다음 실행합니다. 다음 명령에 필요한 저장소 계정 SID는 AD에서 만든 ID의 SID입니다(위의 섹션 b).

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


### <a name="3-confirm-that-the-feature-is-enabled"></a>3. 기능이 활성화되어 있는지 확인합니다.

저장소 계정에서 기능이 활성화되어 있는지 확인하려면 다음 스크립트를 사용할 수 있습니다.

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

이제 저장소 계정에서 기능을 성공적으로 활성화했습니다. 이 기능을 사용하도록 설정되어 있더라도 이 기능을 제대로 사용하려면 추가 작업을 완료해야 합니다.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

이제 SMB를 통해 AD 인증을 성공적으로 활성화하고 AD ID를 사용하여 Azure 파일 공유에 대한 액세스를 제공하는 사용자 지정 역할을 할당했습니다. 추가 사용자에게 파일 공유에 대한 액세스 권한을 부여하려면 SMB 섹션을 통해 ID를 사용하고 [NTFS 권한을 구성할](#configure-ntfs-permissions-over-smb) 수 있는 [액세스 권한 할당의](#assign-access-permissions-to-an-identity) 지침을 따릅니다.

## <a name="update-ad-account-password"></a>AD 계정 암호 업데이트

암호 만료 시간을 적용하는 OU로 저장소 계정을 나타내는 AD ID/계정을 등록한 경우 최대 암호 사용 기간 전에 암호를 회전해야 합니다. AD 계정의 암호를 업데이트하지 못하면 Azure 파일 공유에 액세스하는 인증 실패가 발생합니다.  

암호 회전을 트리거하려면 AzFilesHybrid 모듈에서 `Update-AzStorageAccountADObjectPassword` 명령을 실행할 수 있습니다. cmdlet은 저장소 계정 키 회전과 유사한 작업을 수행합니다. 저장소 계정의 두 번째 Kerberos 키를 가져옵니다 및 AD에 등록 된 계정의 암호를 업데이트 하는 데 사용 합니다. 그런 다음 저장소 계정의 대상 Kerberos 키를 다시 생성하고 AD에 등록된 계정의 암호를 업데이트합니다. AD 도메인 에 가입된 환경에서 이 cmdlet을 실행해야 합니다.

```PowerShell
#Update the password of the AD account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>다음 단계

Azure 파일 및 SMB를 통해 AD를 사용하는 방법에 대한 자세한 내용은 다음 리소스를 참조하십시오.

- [SMB 액세스에 대한 Azure 파일 ID 기반 인증 지원 개요](storage-files-active-directory-overview.md)
- [FAQ](storage-files-faq.md)
