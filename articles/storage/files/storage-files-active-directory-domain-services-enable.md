---
title: Azure Files SMB를 통한 Active Directory 인증 사용
description: Active Directory를 통해 Azure 파일 공유에 대 한 SMB를 통해 id 기반 인증을 사용 하도록 설정 하는 방법을 알아봅니다. 그런 다음 도메인에 가입 된 Windows Vm (가상 머신)에서 AD 자격 증명을 사용 하 여 Azure 파일 공유에 액세스할 수 있습니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: rogarana
ms.openlocfilehash: 4a80565df10b470fe6d3fe590578681f85a3bdd9
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566284"
---
# <a name="enable-active-directory-authentication-over-smb-for-azure-file-shares"></a>Azure 파일 공유에 대해 SMB를 통한 Active Directory 인증 사용

[Azure Files](storage-files-introduction.md) 는 두 가지 유형의 도메인 서비스 (Azure Active Directory Domain Services (Azure AD DS) (GA)와 ACTIVE DIRECTORY (AD) (미리 보기)를 통해 SMB (서버 메시지 블록)를 통해 id 기반 인증을 지원 합니다. 이 문서에서는 Azure 파일 공유에 대 한 인증에 Active Directory 도메인 서비스를 활용 하는 새로 도입 된 (미리 보기) 지원을 중점적으로 다룹니다. Azure 파일 공유에 대 한 GA (Azure AD DS) 인증을 사용 하려는 경우 [주제에 대 한 문서](storage-files-active-directory-enable.md)를 참조 하세요. 

> [!NOTE]
> Azure 파일 공유는 Azure Active Directory 도메인 서비스 (Azure AD DS) 또는 Active Directory (AD) 중 하나의 도메인 서비스에 대해서만 인증을 지원 합니다. 
>
> Azure 파일 공유 인증에 사용 되는 AD id를 Azure AD에 동기화 해야 합니다. 암호 해시 동기화는 선택 사항입니다. 
> 
> AD 인증은 Azure AD DS에서 만든 컴퓨터 계정에 대 한 인증을 지원 하지 않습니다. 
> 
> AD 인증은 저장소 계정이 등록 된 하나의 AD 포리스트에 대해서만 지원할 수 있습니다. 기본적으로 단일 AD 포리스트의 AD 자격 증명을 사용 하 여 Azure 파일 공유에 액세스할 수 있습니다. 다른 포리스트에서 Azure 파일 공유에 액세스 해야 하는 경우 적절 한 포리스트 트러스트를 구성 했는지 확인 합니다.  
> 
> Azure File Sync에서 관리 하는 Azure 파일 공유에 대해 SMB 액세스 및 ACL 지 속성에 대 한 AD 인증이 지원 됩니다.

SMB를 통해 Azure 파일 공유에 AD를 사용 하도록 설정 하면 AD 도메인에 가입 된 컴퓨터에서 기존 AD 자격 증명을 사용 하 여 Azure 파일 공유를 탑재할 수 있습니다. 온-프레미스 컴퓨터 또는 Azure에서 호스트 되는 AD 환경에서이 기능을 사용 하도록 설정할 수 있습니다.

표준 [RBAC (역할 기반 액세스 제어)](../../role-based-access-control/overview.md) 모델을 통해 공유 수준 파일 권한을 적용 하려면 azure 파일 공유에 액세스 하는 데 사용 되는 ad Id를 azure AD에 동기화 해야 합니다. 기존 파일 서버에서 전달 되는 파일/디렉터리의 [Windows 스타일 dacl](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) 은 유지 되 고 적용 됩니다. 이를 통해 엔터프라이즈 AD 도메인 인프라와 원활한 통합이 가능 합니다. 온-프레미스 파일 서버를 Azure 파일 공유로 바꾸면 기존 사용자가 사용 중인 자격 증명을 변경 하지 않고 Single Sign-On 환경을 사용 하 여 현재 클라이언트에서 Azure 파일 공유에 액세스할 수 있습니다.  
 
## <a name="prerequisites"></a>필수 조건 

Azure 파일 공유에 대 한 AD 인증을 사용 하도록 설정 하기 전에 다음 필수 구성 요소를 완료 했는지 확인 합니다. 

- AD 환경을 선택 하거나 만들고 Azure AD에 동기화 합니다. 

    새 또는 기존 AD 환경에서이 기능을 사용 하도록 설정할 수 있습니다. 액세스에 사용 되는 id를 Azure AD와 동기화 해야 합니다. 액세스 하는 Azure AD 테 넌 트와 파일 공유는 동일한 구독과 연결 되어 있어야 합니다. 

    AD 도메인 환경을 설정 하려면 [Active Directory Domain Services 개요](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)를 참조 하세요. AD를 Azure AD와 동기화 하지 않은 경우 기본 인증 방법을 결정 하 고 설치 옵션을 Azure AD Connect 하려면 [Azure Active Directory의 하이브리드 id 란?](../../active-directory/hybrid/whatis-hybrid-identity.md) 의 지침을 따르세요. 

- AD DS 또는 AD를 사용 하 여 온-프레미스 컴퓨터 또는 Azure VM에 도메인 가입 합니다. 

    컴퓨터 또는 VM에서 AD 자격 증명을 사용 하 여 파일 공유에 액세스 하려면 장치가 AD DS에 도메인에 가입 되어 있어야 합니다. AD에 도메인에 가입 하는 방법에 대 한 자세한 내용은 [컴퓨터를 도메인에 가입](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)을 참조 하세요. 

- [지원 되는 지역](#regional-availability)에서 Azure storage 계정을 선택 하거나 만듭니다. 

    파일 공유가 포함 된 저장소 계정이 Azure AD DS 인증에 대해 아직 구성 되지 않았는지 확인 합니다. 저장소 계정에서 Azure AD DS 인증을 사용 하도록 설정한 Azure Files AD를 사용 하도록 변경 하기 전에 사용 하지 않도록 설정 해야 합니다. 이는 Azure AD DS 환경에서 구성 된 기존 Acl을 적절 한 사용 권한을 적용 하도록 다시 구성 해야 함을 의미 합니다.
    
    새 파일 공유를 만드는 방법에 대 한 자세한 내용은 [Azure Files에서 파일 공유 만들기](storage-how-to-create-file-share.md)를 참조 하세요.
    
    최적의 성능을 위해서는 공유에 액세스 하려는 VM과 동일한 지역에 저장소 계정을 저장 하는 것이 좋습니다. 

- 저장소 계정 키를 사용 하 여 Azure 파일 공유를 탑재 하 여 연결을 확인 합니다. 

    장치 및 파일 공유가 올바르게 구성 되었는지 확인 하려면 저장소 계정 키를 사용 하 여 파일 공유를 탑재 해 보세요. 자세한 내용은 [Windows에서 Azure 파일 공유 사용](storage-how-to-use-files-windows.md)을 참조 하세요.

## <a name="regional-availability"></a>국가별 가용성

Azure Files AD 인증은 [대부분의 공용 지역](https://azure.microsoft.com/global-infrastructure/regions/)에서 사용할 수 있으며, 아직에서 사용할 수 없는 지역의 하위 집합은 다음과 같습니다.

- 미국 서부
- 미국 서부 2
- 미국 동부
- 미국 동부 2
- 서유럽
- 북유럽

## <a name="workflow-overview"></a>워크플로 개요

Azure 파일 공유에 대해 SMB를 통한 AD 인증을 사용 하도록 설정 하기 전에  [필수 구성 요소](#prerequisites) 를 안내 하 고 모든 단계를 완료 했는지 확인 하는 것이 좋습니다. 필수 구성 요소는 AD, Azure AD 및 Azure Storage 환경이 제대로 구성 되어 있는지 확인 합니다. 

다음으로, AD 자격 증명을 사용 하 여 Azure Files 리소스에 대 한 액세스 권한을 부여 합니다. 

- 저장소 계정에서 Azure Files AD 인증을 사용 하도록 설정 합니다.  

- 대상 AD id와 동기화 되는 Azure AD id (사용자, 그룹 또는 서비스 주체)에 공유에 대 한 액세스 권한을 할당 합니다. 

- 디렉터리 및 파일에 대 한 SMB를 통해 Acl을 구성 합니다. 

- AD 도메인에 가입 된 VM에서 Azure 파일 공유를 탑재 합니다. 

다음 다이어그램에서는 Azure 파일 공유에 대해 SMB를 통한 Azure AD 인증을 사용 하도록 설정 하는 종단 간 워크플로를 보여 줍니다. 

![파일 광고 워크플로 다이어그램](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> Azure 파일 공유에 대 한 SMB를 통한 AD 인증은 Windows 7 또는 Windows Server 2008 r 2 보다 최신 버전의 OS에서 실행 되는 컴퓨터 또는 Vm 에서만 지원 됩니다. 

## <a name="enable-ad-authentication-for-your-account"></a>계정에 AD 인증 사용 

Azure 파일 공유에 대해 SMB를 통한 AD 인증을 사용 하도록 설정 하려면 먼저 AD에 저장소 계정을 등록 한 후 저장소 계정에 필요한 도메인 속성을 설정 해야 합니다. 저장소 계정에서이 기능을 사용 하도록 설정 하면 계정에 있는 모든 신규 및 기존 파일 공유에 적용 됩니다. `join-AzStorageAccountForAuth` 사용 하 여 기능을 사용 하도록 설정 합니다. 아래 섹션에서 종단 간 워크플로에 대 한 자세한 설명을 확인할 수 있습니다. 

> [!IMPORTANT]
> `join-AzStorageAccountForAuth` cmdlet은 AD 환경을 수정 합니다. 다음 설명을 참조 하 여 명령을 실행할 수 있는 적절 한 권한이 있고 적용 된 변경 내용이 준수 및 보안 정책과 일치 하는지 확인 하기 위해 수행 하는 작업을 더 잘 이해 합니다. 

`join-AzStorageAccountForAuth` cmdlet은 표시 된 저장소 계정을 대신 하 여 오프 라인 도메인 조인과 동일한 기능을 수행 합니다. AD 도메인에서 [컴퓨터 계정](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) 또는 [서비스 로그온 계정](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts)으로 계정을 만듭니다. 만든 AD 계정은 AD 도메인의 저장소 계정을 나타냅니다. 암호 만료를 적용 하는 AD OU (조직 구성 단위)에서 AD 계정을 만든 경우 최대 암호 사용 기간 전에 암호를 업데이트 해야 합니다. AD 계정 암호를 업데이트 하지 못하면 Azure 파일 공유에 액세스할 때 인증 오류가 발생 합니다. 암호를 업데이트 하는 방법을 알아보려면 [AD 계정 암호 업데이트](#update-ad-account-password)를 참조 하세요.

다음 스크립트를 사용 하 여 등록을 수행 하 고 기능을 사용 하도록 설정 하거나 스크립트에서 수행 하는 작업을 수동으로 수행할 수 있습니다. 이러한 작업은 스크립트 다음에 나오는 섹션에 설명 되어 있습니다. 두 작업을 모두 수행할 필요는 없습니다.

### <a name="script-prerequisites"></a>스크립트 필수 구성 요소

- [AzFilesHybrid 모듈 다운로드 및 압축 풀기](https://github.com/Azure-Samples/azure-files-samples/releases)
- 대상 AD에서 서비스 로그온 계정 또는 컴퓨터 계정을 만들 수 있는 권한이 있는 ad 자격 증명을 사용 하 여 AD에 도메인에 가입 된 장치에 모듈을 설치 하 고 실행 합니다.
-  Azure AD에 동기화 되는 AD 자격 증명을 사용 하 여 스크립트를 실행 합니다. AD 자격 증명에는 저장소 계정 소유자 또는 참가자 RBAC 역할 권한이 있어야 합니다.
- 저장소 계정이 [지원 되는 지역](#regional-availability)에 있는지 확인 하세요.

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope Currentuser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Select the target subscription for the current session
Select-AzureSubscription -SubscriptionId "<yourSubscriptionIdHere>"

#Register the target storage account with your active directory environment under the target OU
join-AzStorageAccountForAuth -ResourceGroupName "<resource-group-name-here>" -Name "<storage-account-name-here>" -DomainAccountType "<ServiceLogonAccount|ComputerAccount>" -OrganizationUnitName "<ou-name-here>"
```


다음은 `join-AzStorageAccountForAuth` 명령을 사용할 때 수행 되는 동작에 대 한 설명입니다. 명령을 사용 하지 않으려는 경우 다음 단계를 수동으로 수행할 수 있습니다.

### <a name="checking-environment"></a>환경 확인

먼저 사용자 환경을 확인 합니다. 특히 [Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) 이 설치 되어 있는지와 셸이 관리자 권한으로 실행 되 고 있는지 확인 합니다. 그런 다음 [Az. Storage 1.11.1-preview 모듈이](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) 설치 되어 있는지 확인 하 고 그렇지 않으면 설치 합니다. 이러한 검사가 통과 되 면 AD를 확인 하 여 이미 SPN/UPN을 사용 하 여 생성 된 [컴퓨터 계정](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (기본값) 또는 [서비스 로그온 계정이](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) 있는지 확인 합니다 .이 계정이 없으면 이미 SPN/UPN을 사용 하 여 생성 된 것입니다.

### <a name="creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>수동으로 AD의 저장소 계정을 나타내는 id 만들기

이 계정을 수동으로 만들려면 `New-AzStorageAccountKey -Keynam "yourKeyName"`를 사용 하 여 저장소 계정에 대 한 새 kerberos 키를 만듭니다. 그런 다음 해당 kerberos 키를 계정의 암호로 사용 합니다. 이 키는 설치 중에만 사용 되며 저장소 계정에 대 한 모든 제어 또는 데이터 평면 작업에는 사용할 수 없습니다.

해당 키가 있으면 OU에서 서비스 또는 컴퓨터 계정을 만듭니다. 저장소 계정에 대 한 SPN: "cifs/저장소-이름-파일-이름-w i n. w i n.

OU에서 암호 만료를 적용 하는 경우 Azure 파일 공유에 액세스할 때 인증 오류가 발생 하지 않도록 최대 암호 사용 기간 전에 암호를 업데이트 해야 합니다. 자세한 내용은 [AD 계정 암호 업데이트](#update-ad-account-password) 를 참조 하세요.

새로 만든 계정의 SID를 유지 하 고 다음 단계에 필요 합니다.

### <a name="enable-the-feature-on-your-storage-account"></a>저장소 계정에서 기능을 사용 하도록 설정

그런 다음 스크립트는 저장소 계정에서이 기능을 사용 하도록 설정 합니다. 이 작업을 수동으로 수행 하려면 다음 명령에서 도메인 속성에 대 한 구성 세부 정보를 제공 하 고 실행 합니다. 다음 명령에 필요한 저장소 계정 SID는 AD에서 만든 id의 SID입니다.

```PowerShell
#Set the feature flag on the target storage account and provide the required AD domain information

Set-AzStorageAccount -ResourceGroupName "<your-resource-group-name-here>" -Name "<your-storage-account-name-here>" -EnableActiveDirectoryDomainServiesForFile $true -ActiveDirectoryDomainName "<your-domain-name-here>" -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" -ActiveDirectoryForestName "<your-forest-name-here>" -ActiveDirectoryDomainGuid "<your-guid-here>" -ActiveDirectoryDomainsid "<your-domain-sid-here>" -ActiveDirectoryAzureStirageSid "<your-storage-account-sid>"
```


### <a name="check-if-the-feature-is-enabled"></a>기능이 사용 되는지 확인 합니다.

저장소 계정에서 기능을 사용할 수 있는지 여부를 확인 하려는 경우 다음 스크립트를 사용할 수 있습니다.

```PowerShell
#Get the target storage account
$storageaccount = Get-AzStorageAccount -ResourceGroupName "<your-resource-group-name-here>" -Name "<your-storage-account-name-here>"

#List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

#List the directory domain information if the storage account has enabled AD authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

이제 저장소 계정에서이 기능을 사용 하도록 설정 했습니다. 기능을 사용 하도록 설정한 경우에도 기능을 제대로 사용할 수 있도록 추가 작업을 완료 해야 합니다.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

이제 SMB를 통해 Azure AD 인증을 사용 하도록 설정 하 고 AD id를 사용 하 여 Azure 파일 공유에 대 한 액세스를 제공 하는 사용자 지정 역할을 할당 했습니다. 추가 사용자에 게 파일 공유에 대 한 액세스 권한을 부여 하려면 [액세스 권한 할당](#assign-access-permissions-to-an-identity) 을 사용 하 여 id를 사용 하 고 [SMB를 통한 NTFS 사용 권한 구성](#configure-ntfs-permissions-over-smb) 섹션의 지침을 따르세요.

## <a name="update-ad-account-password"></a>AD 계정 암호 업데이트

암호 만료 시간을 적용 하는 OU에서 저장소 계정을 나타내는 AD 계정을 등록 한 경우 최대 암호 사용 기간 보다 먼저 암호를 회전 해야 합니다. AD 계정의 암호를 업데이트 하지 못하면 인증 오류가 발생 하 여 Azure 파일 공유에 액세스 하지 못합니다.  

암호 회전을 트리거하려면 [AzFilesHybrid 모듈](#script-prerequisites)에서 `Update-AzStorageAccountADObjectPassword` 명령을 실행할 수 있습니다. Cmdlet은 저장소 계정 키 회전과 유사한 작업을 수행 합니다. 저장소 계정의 두 번째 Kerberos 키를 가져오고이를 사용 하 여 AD에서 등록 된 계정의 암호를 업데이트 합니다. 그런 다음 저장소 계정의 대상 Kerberos 키를 다시 생성 하 고 AD에서 등록 된 계정의 암호를 업데이트 합니다. AD 도메인 가입 환경에서이 cmdlet을 실행 해야 합니다.

```PowerShell
#Update the password of the AD account registered for the storage account
Update-AzStorageAccountADObjectPassword -RotateToKerbKey kerb2 -ResourceGroupName "your-resource-group-name-here" -StorageAccountName "your-storage-account-name-here"
```

## <a name="next-steps"></a>다음 단계

Azure Files 및 SMB에서 AD를 사용 하는 방법에 대 한 자세한 내용은 다음 리소스를 참조 하세요.

- [SMB 액세스를 위한 Azure Files id 기반 인증 지원 개요](storage-files-active-directory-overview.md)
- [FAQ](storage-files-faq.md)