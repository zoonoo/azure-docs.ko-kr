---
title: Azure Files용 SMB(미리 보기)를 통해 Azure Active Directory 인증 사용 - Azure Storage
description: Azure AD(Active Directory) Domain Services를 사용하여 Azure Files용 SMB(서버 메시지 블록)(미리 보기)를 통해 ID 기반 인증을 사용하도록 설정하는 방법을 알아봅니다. 도메인 가입 Windows VM(가상 머신)은 Azure AD 자격 증명을 사용하여 Azure 파일 공유에 액세스할 수 있습니다.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 07/05/2019
ms.author: rogarana
ms.openlocfilehash: cd4c952caa336f2602d3c30e0db3e10ebee59cb9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67696118"
---
# <a name="enable-azure-active-directory-domain-service-authentication-over-smb-for-azure-files-preview"></a>Azure Files (미리 보기)에 대 한 SMB를 통한 Azure Active Directory 도메인 서비스 인증을 사용 하도록 설정
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Azure Files용 SMB를 통한 Azure AD 인증의 개요는 [Azure Files용 SMB(미리 보기)를 통한 Azure Active Directory 인증의 개요](storage-files-active-directory-overview.md)를 참조하세요.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview-of-the-workflow"></a>워크플로의 개요
Azure Files에 대 한 SMB를 통한 Azure AD DS 인증을 사용 하기 전에 확인 하는 Azure AD에 Azure Storage 환경을 올바르게 구성 되었습니다. [필수 구성 요소](#prerequisites)를 검토하여 필요한 모든 단계를 수행했는지 확인하는 것이 좋습니다. 

그런 후에는 다음 단계를 수행하여 Azure AD 자격 증명을 사용한 Azure Files 리소스 액세스 권한을 부여합니다. 

1. 연결된 된 Azure AD Domain Services 배포를 사용 하 여 저장소 계정을 등록 하 여 저장소 계정에 대 한 SMB를 통한 Azure AD DS 인증을 사용 합니다.
2. Azure AD ID(사용자, 그룹 또는 서비스 주체)에 공유 액세스 권한을 할당합니다.
3. 디렉터리와 파일에 대해 SMB를 통한 NTFS 권한을 구성합니다.
4. 도메인 조인 VM에서 Azure 파일 공유를 탑재합니다.

아래 다이어그램에서는 Azure Files에 대 한 SMB를 통한 Azure AD DS 인증을 사용 하는 것에 대 한 종단 간 워크플로 보여 줍니다. 

![Azure Files용 SMB를 통한 Azure AD 워크플로를 보여 주는 다이어그램](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>전제 조건 

Azure Files에 대한 SMB를 통한 Azure AD를 사용하도록 설정하기 전에 다음 필수 조건을 완료했는지 확인합니다.

1.  **Azure AD 테넌트를 선택하거나 만듭니다.**

    SMB를 통한 Azure AD 인증에는 새 테넌트나 기존 테넌트를 사용할 수 있습니다. 액세스하려는 파일 공유와 테넌트는 같은 구독과 연결되어 있어야 합니다.

    새 Azure AD 테넌트를 만들려는 경우 [Azure AD 테넌트 및 Azure AD 구독을 추가](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription)하면 됩니다. 기존 Azure AD 테넌트가 있지만 Azure Files에 사용할 새 테넌트를 만들려는 경우에는 [Azure Active Directory 테넌트 만들기](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant)를 참조하세요.

2.  **Azure AD 테넌트에서 Azure AD Domain Services를 사용하도록 설정합니다.**

    Azure AD 자격 증명을 사용한 인증을 지원하려면 Azure AD 테넌트에 대해 Azure AD Domain Services를 사용하도록 설정해야 합니다. Azure AD 테넌트의 관리자가 아니라면 관리자에게 문의하고 [Azure Portal을 사용하여 Azure Active Directory Domain Services 활성화](../../active-directory-domain-services/create-instance.md)를 위한 단계별 지침을 따르세요.

    일반적으로는 Azure AD Domain Services 배포를 완료하려면 15분 정도 걸립니다. 다음 단계로 진행하기 전에 Azure AD Domain Services의 상태가 **실행 중**으로 표시되고 암호 해시 동기화가 사용하도록 설정되어 있는지 확인합니다.

3.  **Azure VM을 Azure AD Domain Services에 도메인 조인합니다.**

    VM에서 Azure AD 자격 증명을 사용하여 파일 공유에 액세스하려면 VM을 Azure AD Domain Services에 도메인 조인해야 합니다. VM을 도메인 조인하는 방법에 대한 자세한 내용은 [Windows Server 가상 머신을 관리되는 도메인에 조인](../../active-directory-domain-services/join-windows-vm.md)을 참조하세요.

    > [!NOTE]
    > Azure Files를 사용 하 여 SMB 통한 azure AD DS 인증 Windows 7 또는 Windows Server 2008 R2 이상의 OS 버전에서 실행 중인 Azure Vm 에서만 지원 됩니다.

4.  **Azure 파일 공유를 선택하거나 만듭니다.**

    Azure AD 테넌트와 같은 구독에 연결된 신규 또는 기존 파일 공유를 선택합니다. 새 파일 공유를 만드는 방법에 대한 자세한 내용은 [Azure Files에 파일 공유 만들기](storage-how-to-create-file-share.md)를 참조하세요. 
    성능을 최적화하려면 파일 공유를 해당 공유에 액세스하려는 VM과 같은 지역에 배치하는 것이 좋습니다.

5.  **저장소 계정 키를 사용하여 Azure 파일 공유를 탑재해 Azure Files 연결을 확인합니다.**

    VM 및 파일 공유가 적절하게 구성되었는지 확인하려면 저장소 계정 키를 사용하여 파일 공유를 탑재해 봅니다. 자세한 내용은 [Azure 파일 공유를 탑재하고 Windows에서 공유에 액세스](storage-how-to-use-files-windows.md)를 참조하세요.

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>계정에 대 한 Azure AD DS 인증을 사용 하도록 설정

Azure AD DS 인증 SMB를 통한 Azure files를 사용 하려면 설정할 수 있습니다 속성 2018 년 9 월 24 일 이후 생성 된 저장소 계정에 Azure portal, Azure PowerShell 또는 Azure CLI를 사용 하 여. 이 속성을 설정하면 연결된 Azure AD Domain Services 배포에 저장소 계정이 등록됩니다. SMB 통한 azure AD DS 인증은 저장소 계정에서 모든 신규 및 기존 파일 공유에 대해 설정 됩니다. 

Azure AD 테 넌 트 Azure AD Domain Services를 성공적으로 배포한 후에 SMB를 통한 Azure AD DS 인증을 사용할 수 있는 것을 염두에 두십시오. 자세한 내용은 [필수 구성 요소](#prerequisites)를 참조하세요.

### <a name="azure-portal"></a>Azure Portal

SMB를 사용 하 여 Azure AD DS 인증을 사용 하도록 설정 합니다 [Azure portal](https://portal.azure.com), 다음이 단계를 수행:

1. Azure Portal에서 기존 저장소 계정으로 이동하거나 [저장소 계정을 만듭니다](../common/storage-quickstart-create-account.md).
2. **설정** 섹션에서 **구성**을 선택합니다.
3. **Azure Files용 Azure Active Directory 인증(미리 보기)** 을 사용하도록 설정합니다.

저장소 계정에 대해 SMB를 통한 Azure AD 인증을 사용하도록 설정하는 방법이 다음 이미지에 나와 있습니다.

![Azure Portal에서 SMB를 통한 Azure AD 인증 사용](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)
  
### <a name="powershell"></a>PowerShell  

에 Azure PowerShell을 사용 하 여 SMB를 통한 Azure AD DS 인증을 사용 합니다. 먼저, 최신 Az 모듈 (2.4 이상) 또는 Az.Storage 모듈 (1.5 이상)를 설치 합니다. PowerShell을 설치 하는 방법에 대 한 자세한 내용은 참조 하세요. [PowerShellGet 사용 하 여 Windows에서 Azure PowerShell 설치](https://docs.microsoft.com/powershell/azure/install-Az-ps):

```powershell
Install-Module -Name Az.Storage -AllowPrerelease -Force -AllowClobber
```

다음으로 새 저장소를 만듭니다 계정에 다음 호출 [집합 AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) 설정 된 **EnableAzureActiveDirectoryDomainServicesForFile** 매개 변수를 **true**. 아래 예제의 자리 표시자 값은 실제 값으로 바꾸세요. (이전 미리 보기 모듈을 사용 하는 기능에 대 한 매개 변수는 **EnableAzureFilesAadIntegrationForSMB**.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```
기존 저장소 계정에서이 기능을 사용 하려면 다음 명령을 사용 합니다.

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>Azure CLI

Azure CLI 2.0에서 SMB를 통한 Azure AD 인증을 사용하도록 설정하려면 먼저 `storage-preview` 확장을 설치합니다.

```cli-interactive
az extension add --name storage-preview
```
  
다음으로 새 스토리지 계정을 만든 다음, [az storage account update](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update)를 호출하고 `--file-aad` 속성을 **true**로 설정합니다. 아래 예제의 자리 표시자 값은 실제 값으로 바꾸세요.

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --file-aad true
```

## <a name="assign-access-permissions-to-an-identity"></a>ID에 액세스 권한 할당 

Azure AD 자격 증명을 사용하여 Azure Files 리소스에 액세스하려는 경우 ID(사용자, 그룹 또는 서비스 주체)에 공유 수준에서 필요한 권한이 있어야 합니다. 이 프로세스는 파일 공유에 대한 특정 사용자의 액세스 권한 유형을 지정하는 Windows 공유 권한 지정 과정과 비슷합니다. 이 섹션의 지침에서는 파일 공유에 대한 읽기, 쓰기 또는 삭제 권한을 ID에 할당하는 방법을 보여줍니다.

사용자에 게 공유 수준 권한 부여에 대 한 두 개의 Azure 기본 제공 역할을 도입 했습니다. 저장소 파일 데이터 SMB 공유 판독기 및 저장소 파일 데이터 SMB 공유 참가자입니다. 

- **저장소 파일 데이터 SMB 공유 판독기** SMB를 통한 Azure Storage 파일 공유에 대 한 읽기 액세스 허용
- **저장소 파일 데이터 SMB 공유 참가자** SMB를 통한 Azure Storage 파일 공유 읽기, 쓰기 및 삭제 액세스 허용

> [!IMPORTANT]
> ID에 역할을 할당하는 기능을 비롯하여 파일 공유에 대한 모든 관리 권한을 할당하려면 저장소 계정 키를 사용해야 합니다. Azure AD 자격 증명으로는 관리 권한이 지원되지 않습니다. 

공유 수준 권한 부여에 대 한 사용자의 Azure AD id에 기본 제공 역할을 할당할 Azure portal, PowerShell 또는 Azure CLI를 사용할 수 있습니다. 

#### <a name="azure-portal"></a>Azure Portal
RBAC 역할을 Azure AD id를 할당할를 사용 하 여는 [Azure portal](https://portal.azure.com), 다음이 단계를 수행 합니다.

1. Azure portal에서 파일 공유로 이동 하거나 [Azure Files에 파일 공유 만들기](storage-how-to-create-file-share.md)합니다.
2. **Access Control(IAM)** 을 선택합니다.
3. 선택 **역할 할당 추가**
4. 에 **역할 할당 추가** 블레이드에서에서 적절 한 기본 제공 역할 (Storage 파일 데이터 SMB 공유 판독기, 저장소 파일 데이터 SMB 공유 참가자)를 선택 합니다 **역할** 드롭다운 목록입니다. 유지 된 **에 대 한 액세스 할당** 기본적으로: "Azure AD 사용자, 그룹 또는 서비스 주체" 이름 또는 전자 메일 주소로 대상 Azure AD id를 선택 합니다. 
5. 마지막으로, 선택 **저장할** 역할 할당 작업을 완료 합니다.

#### <a name="powershell"></a>PowerShell

다음 PowerShell 명령을 로그인 이름을 기반으로 하는 Azure AD id에 RBAC 역할을 할당 하는 방법을 보여 줍니다. PowerShell을 사용하여 RBAC 역할을 할당하는 방법에 대한 자세한 내용은 [RBAC 및 Azure PowerShell을 사용하여 액세스 관리](../../role-based-access-control/role-assignments-powershell.md)를 참조하세요.

다음 샘플 스크립트를 실행할 때 대괄호를 포함한 자리 표시자 값을 사용자 고유의 값으로 변경해야 합니다.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
CLI 2.0 명령을 로그인 이름을 기반으로 하는 Azure AD id에 RBAC 역할을 할당 하는 방법을 보여 줍니다. Azure CLI를 사용하여 RBAC 역할을 할당하는 방법에 대한 자세한 내용은 [RBAC 및 Azure CLI를 사용하여 액세스 관리](../../role-based-access-control/role-assignments-cli.md)를 참조하세요. 

다음 샘플 스크립트를 실행할 때 대괄호를 포함한 자리 표시자 값을 사용자 고유의 값으로 변경해야 합니다.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>SMB를 통한 NTFS 권한 구성 
RBAC를 사용하여 공유 수준 권한할당을 한 후에는 루트, 디렉터리 또는 파일 수준에서 적절한 NTFS 권한을 할당해야 합니다. 공유 수준 권한은 사용자가 공유에 액세스할 수 있는지 여부를 결정하는 높은 수준의 게이트키퍼 역할을 하는 반면, NTFS 권한은 더 세분화된 수준에서 작동하여 디렉터리 또는 파일 수준에서 사용자가 수행할 수 있는 작업을 결정합니다. 

Azure Files는 NTFS 기본 및 고급 권한의 전체 집합을 지원합니다. 공유를 탑재한 다음 Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) 또는 [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) 명령을 실행하여 Azure 파일 공유의 디렉터리와 파일에 대한 NTFS 권한을 확인하고 구성할 수 있습니다. 

> [!NOTE]
> 미리 보기 릴리스에서는 Windows 파일 탐색기를 사용한 권한 보기만 지원됩니다. 권한 편집은 아직 지원되지 않습니다.

슈퍼 사용자 권한으로 NTFS 권한을 구성하려면 도메인 조인 VM에서 저장소 계정 키를 사용하여 공유를 탑재해야 합니다. 다음 섹션의 지침에 따라 명령 프롬프트에서 Azure 파일 공유를 탑재하고 NTFS 권한을 적절하게 구성하세요.

파일 공유의 루트 디렉터리에서 지원되는 권한 집합은 다음과 같습니다.

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>명령 프롬프트에서 파일 공유 탑재

Windows **net use** 명령을 사용하여 Azure 파일 공유를 탑재합니다. 예제의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다. 파일 공유 탑재에 대한 자세한 내용은 [Azure 파일 공유를 탑재하고 Windows에서 공유에 액세스](storage-how-to-use-files-windows.md)를 참조하세요.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```

### <a name="configure-ntfs-permissions-with-icacls"></a>icacls를 사용하여 NTFS 권한 구성
루트 디렉터리를 비롯하여 파일 공유에 있는 모든 디렉터리와 파일에 대한 모든 권한을 부여하려면 다음 Windows 명령을 사용합니다. 예제의 대괄호 안에 표시된 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

icacls를 사용하여 NTFS 권한을 설정하는 방법과 지원되는 여러 권한 유형에 대한 자세한 내용은 [icacls용 명령줄 참조](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)를 참조하세요.

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>도메인 조인 VM에서 파일 공유 탑재 

이제 Azure AD 자격 증명을 사용해 도메인 조인 VM에서 Azure 파일 공유에 액세스하여 위의 단계를 올바르게 완료했는지를 확인할 준비가 되었습니다. 먼저 아래 그림과 같이 권한을 부여한 Azure AD ID를 사용하여 VM에 로그인합니다.

![사용자 인증용 Azure AD 로그인 화면이 표시된 스크린샷](media/storage-files-active-directory-enable/azure-active-directory-authentication-dialog.png)

그런 후에 다음 명령을 사용하여 Azure 파일 공유를 탑재합니다. 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다. 이미 인증이 되었기 때문에 저장소 계정 키 또는 Azure AD 사용자 이름과 암호는 입력하지 않아도 됩니다. SMB를 통한 Azure AD에서는 Azure AD 자격 증명을 사용하는 Single Sign-On 환경을 지원합니다.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

이제 SMB를 통한 Azure AD 인증을 올바르게 사용하도록 설정했으며, 파일 공유 액세스 권한을 제공하는 사용자 지정 역할을 Azure AD ID에 할당했습니다. 추가 사용자에게 파일 공유 액세스 권한을 부여하려면 2 및 3단계에 나와 있는 지침을 따르세요.

## <a name="next-steps"></a>다음 단계

Azure Files 및 SMB를 통해 Azure AD를 사용하는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.

- [Azure Files 소개](storage-files-introduction.md)
- [Azure Files용 SMB(미리 보기)를 통한 Azure Active Directory 인증 개요](storage-files-active-directory-overview.md)
- [FAQ](storage-files-faq.md)
