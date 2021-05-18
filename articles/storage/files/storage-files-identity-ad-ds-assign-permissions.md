---
title: Azure 파일 공유에 대한 액세스 제어 - 온-프레미스 AD DS 인증
description: 스토리지 계정을 나타내는 Active Directory Domain Services ID에 사용 권한을 할당하는 방법을 알아봅니다. 이렇게 하면 ID 기반 인증을 사용하여 액세스를 제어할 수 있습니다.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: af88f0b3403fb80acbb7dacebe293ac583e35799
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91716046"
---
# <a name="part-two-assign-share-level-permissions-to-an-identity"></a>2부: ID에 공유 수준 권한 할당

이 문서를 시작하기 전에 이전 문서인 [계정에 AD DS 인증 사용](storage-files-identity-ad-ds-enable.md)을 완료했는지 확인합니다.

스토리지 계정에 AD DS(Active Directory Domain Services) 인증을 사용하도록 설정한 후에는 파일 공유에 대한 액세스 권한을 얻기 위해 공유 수준 권한을 구성해야 합니다. Azure 파일 공유 리소스에 액세스하려는 ID는 AD DS 및 Azure AD 둘 다에 있는 하이브리드 ID여야 합니다. 예를 들어 AD DS에 user1@onprem.contoso.com인 사용자가 있고 Azure AD Connect 동기화를 사용하여 user1@contoso.com으로 Azure AD에 동기화했다고 가정합니다. 이 사용자가 Azure Files에 액세스할 수 있도록 하려면 user1@contoso.com에 공유 수준 권한을 할당해야 합니다. 그룹 또는 서비스 주체에도 동일한 개념이 적용됩니다. 따라서 Azure AD Connect 동기화를 사용하여 AD DS에서 Azure AD로 사용자 및 그룹을 동기화해야 합니다. 

Azure 파일 공유에 대한 AD DS 인증을 지원하려면 AD DS의 동일한 사용자 또는 그룹을 나타내는 Azure AD ID에 공유 수준 권한을 할당해야 합니다. Azure 관리 ID(MSI)와 같이 Azure AD에만 있는 ID에 대한 인증 및 권한 부여는 AD DS 인증에서 지원되지 않습니다. 이 문서에서는 파일 공유에 대한 공유 수준 권한을 ID에 할당하는 방법을 보여 줍니다.


## <a name="share-level-permissions"></a>공유 수준 권한

일반적으로 사용자 및 ID 그룹을 나타내는 Azure AD 그룹에 대한 고급 액세스 관리를 위해 공유 수준 권한을 사용한 다음, 디렉터리/파일 수준에 대한 세분화된 액세스 제어를 위해 Windows ACL을 활용하는 것이 좋습니다. 

사용자에게 공유 수준 권한을 부여하기 위한 세 가지 Azure 기본 제공 역할은 다음과 같습니다.

- **스토리지 파일 데이터 SMB 공유 읽기 권한자** 는 SMB를 통한 Azure Storage 파일 공유에서 읽기 권한을 허용합니다.
- **스토리지 파일 데이터 SMB 공유 기여자** 는 SMB를 통해 Azure Storage 파일 공유에 대한 읽기, 쓰기, 삭제 액세스를 허용합니다.
- **스토리지 파일 데이터 SMB 공유 상승된 기여자** 는 SMB를 통한 Azure Storage 파일 공유에서 읽기, 쓰기, 삭제, 수정 Windows ACL을 허용합니다.

> [!IMPORTANT]
> 파일 소유권을 가져오는 기능을 포함하여 파일 공유에 대한 모든 관리 권한을 사용하려면 스토리지 계정 키가 있어야 합니다. Azure AD 자격 증명으로는 관리 권한이 지원되지 않습니다.

Azure Portal, Azure PowerShell 또는 Azure CLI를 사용하여 공유 수준 권한을 부여하는 사용자의 Azure AD ID에 기본 제공 역할을 할당할 수 있습니다.

## <a name="assign-an-azure-role"></a>Azure 역할 할당

### <a name="azure-portal"></a>Azure portal

[Azure Portal](https://portal.azure.com)을 사용하여 Azure AD ID에 Azure 역할을 할당하려면 다음 단계를 수행합니다.

1. Azure Portal에서 파일 공유로 이동하거나 [파일 공유를 만듭니다](storage-how-to-create-file-share.md).
1. **Access Control(IAM)** 을 선택합니다.
1. **역할 할당 추가** 를 선택합니다.
1. **역할 할당 추가** 블레이드의 **역할** 목록에서 적절한 기본 제공 역할(스토리지 파일 데이터 SMB 공유 읽기 권한자, 스토리지 파일 데이터 SMB 공유 기여자)을 선택합니다. **다음에 대한 액세스 할당** 은 기본 설정인 **Azure AD 사용자, 그룹 또는 서비스 주체** 로 그대로 둡니다. 이름 또는 메일 주소를 기준으로 대상 Azure AD ID를 선택합니다. **선택한 Azure AD ID는 하이브리드 ID여야 하며 클라우드 전용 ID일 수 없습니다.** 즉, 동일한 ID가 AD DS에도 표시됩니다.
1. **저장** 을 선택하여 역할 할당 작업을 완료합니다.

### <a name="powershell"></a>PowerShell

다음 PowerShell 샘플은 로그인 이름을 기준으로 Azure AD ID에 Azure 역할을 할당하는 방법을 보여 줍니다. PowerShell을 사용하여 Azure 역할을 할당하는 방법에 대한 자세한 내용은 [Azure PowerShell 모듈을 사용하여 Azure 역할 할당 추가 또는 제거](../../role-based-access-control/role-assignments-powershell.md)를 참조하세요.

다음 샘플 스크립트를 실행하기 전에 대괄호를 포함한 자리 표시자 값을 원하는 값으로 바꿉니다.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

### <a name="cli"></a>CLI
  
다음 CLI 2.0 명령은 로그인 이름을 기준으로 Azure AD ID에 Azure 역할을 할당합니다. Azure CLI를 사용하여 Azure 역할을 할당하는 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 Azure 역할 할당 추가 또는 제거](../../role-based-access-control/role-assignments-cli.md)를 참조하세요. 

다음 샘플 스크립트를 실행하기 전에 대괄호를 포함한 자리 표시자 값을 원하는 값으로 바꾸어야 합니다.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="next-steps"></a>다음 단계

이제 공유 수준 권한을 할당했으므로 디렉터리 및 파일 수준 권한을 구성해야 합니다. 다음 문서를 계속 진행합니다.

[3부: SMB를 통한 디렉터리 및 파일 수준 권한 구성](storage-files-identity-ad-ds-configure-permissions.md)
