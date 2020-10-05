---
title: Azure 파일 공유에 대 한 액세스 제어-온-프레미스 AD DS 인증
description: 저장소 계정을 나타내는 Active Directory Domain Services id에 사용 권한을 할당 하는 방법에 대해 알아봅니다. 이를 통해 id 기반 인증을 사용 하 여 액세스를 제어할 수 있습니다.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: af88f0b3403fb80acbb7dacebe293ac583e35799
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91716046"
---
# <a name="part-two-assign-share-level-permissions-to-an-identity"></a>2 부: id에 공유 수준 권한 할당

이 문서를 시작 하기 전에 이전 문서, [계정에 대 한 AD DS 인증 사용](storage-files-identity-ad-ds-enable.md)을 완료 했는지 확인 합니다.

저장소 계정에 대 한 Active Directory Domain Services (AD DS) 인증을 사용 하도록 설정한 후에는 파일 공유에 대 한 액세스 권한을 얻기 위해 공유 수준 권한을 구성 해야 합니다. Azure 파일 공유 리소스에 액세스 하려는 id는 AD DS 및 Azure AD 둘 다에 존재 하는 하이브리드 id 여야 합니다. 예를 들어 AD DS에 사용자가 있고 user1@onprem.contoso.com Azure AD Connect sync를 사용 하 여 AZURE AD에 동기화 한 사용자가 있다고 가정 user1@contoso.com 합니다. 이 사용자가 Azure Files에 액세스할 수 있도록 하려면에 대 한 공유 수준 권한을 할당 해야 합니다 user1@contoso.com . 그룹 또는 서비스 주체에도 동일한 개념이 적용 됩니다. 따라서 Azure AD Connect sync를 사용 하 여 AD DS에서 Azure AD로 사용자 및 그룹을 동기화 해야 합니다. 

Azure 파일 공유에 대 한 AD DS 인증을 지원 하려면 AD DS의 동일한 사용자 또는 그룹을 나타내는 Azure AD id에 공유 수준 권한을 할당 해야 합니다. Azure Msi (관리 Id)와 같이 Azure AD에만 있는 id에 대 한 인증 및 권한 부여는 AD DS 인증에서 지원 되지 않습니다. 이 문서에서는 파일 공유에 대 한 공유 수준 권한을 id에 할당 하는 방법을 보여 줍니다.


## <a name="share-level-permissions"></a>공유 수준 권한

일반적으로 사용자 및 id 그룹을 나타내는 Azure AD 그룹에 대 한 고급 액세스 관리에 대 한 공유 수준 권한을 사용 하 고, 디렉터리/파일 수준에 대 한 세분화 된 액세스 제어를 위해 Windows Acl을 활용 하는 것이 좋습니다. 

사용자에 게 공유 수준 권한을 부여 하기 위한 세 가지 Azure 기본 제공 역할은 다음과 같습니다.

- **저장소 파일 데이터 Smb 공유 판독기** 는 smb를 통한 Azure Storage 파일 공유에서 읽기 권한을 허용 합니다.
- **저장소 파일 데이터 Smb 공유 참가자** 는 smb를 통해 Azure Storage 파일 공유에 대 한 읽기, 쓰기 및 삭제 액세스를 허용 합니다.
- **저장소 파일 데이터 Smb 공유 승격 된 참가자** 는 SMB를 통해 Azure Storage 파일 공유에서 Windows acl을 읽고, 쓰고, 삭제 하 고, 수정할 수 있습니다.

> [!IMPORTANT]
> 파일 소유권을 가져오는 기능을 포함 하 여 파일 공유에 대 한 모든 관리 권한을 저장소 계정 키를 사용 해야 합니다. Azure AD 자격 증명으로는 관리 권한이 지원되지 않습니다.

Azure Portal, Azure PowerShell 또는 Azure CLI를 사용 하 여 공유 수준 권한을 부여 하는 사용자의 Azure AD id에 기본 제공 역할을 할당할 수 있습니다.

## <a name="assign-an-azure-role"></a>Azure 역할 할당

### <a name="azure-portal"></a>Azure portal

[Azure Portal](https://portal.azure.com)를 사용 하 여 azure AD Id에 azure 역할을 할당 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 파일 공유로 이동 하거나 [파일 공유를 만듭니다](storage-how-to-create-file-share.md).
1. **Access Control(IAM)** 을 선택합니다.
1. **역할 할당 추가** 선택
1. **역할 할당 추가** 블레이드의 **역할** 목록에서 적절 한 기본 제공 역할 (저장소 파일 데이터 Smb 공유 판독기, 저장소 파일 데이터 smb 공유 참가자)을 선택 합니다. 기본 설정인 **AZURE AD 사용자, 그룹 또는 서비스 주체**에 대 한 **액세스 할당을** 그대로 둡니다. 이름 또는 전자 메일 주소를 기준으로 대상 Azure AD id를 선택 합니다. **선택한 Azure AD id는 하이브리드 id 여야 하며 클라우드 전용 id 일 수 없습니다.** 즉, 동일한 id가 AD DS에도 표시 됩니다.
1. **저장** 을 선택 하 여 역할 할당 작업을 완료 합니다.

### <a name="powershell"></a>PowerShell

다음 PowerShell 샘플은 로그인 이름에 따라 azure AD id에 Azure 역할을 할당 하는 방법을 보여 줍니다. PowerShell로 Azure 역할을 할당 하는 방법에 대 한 자세한 내용은 [Azure PowerShell 모듈을 사용 하 여 azure 역할 할당 추가 또는 제거](../../role-based-access-control/role-assignments-powershell.md)를 참조 하세요.

다음 샘플 스크립트를 실행 하기 전에 대괄호를 포함 하 여 자리 표시자 값을 사용자의 값으로 대체 합니다.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

### <a name="cli"></a>CLI
  
다음 CLI 2.0 명령은 로그인 이름에 따라 azure AD id에 Azure 역할을 할당 합니다. Azure CLI로 Azure 역할을 할당 하는 방법에 대 한 자세한 내용은 [Azure CLI를 사용 하 여 azure 역할 할당 추가 또는 제거](../../role-based-access-control/role-assignments-cli.md)를 참조 하세요. 

다음 샘플 스크립트를 실행 하기 전에 대괄호를 포함 하 여 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="next-steps"></a>다음 단계

이제 공유 수준의 사용 권한을 할당 했으므로 디렉터리와 파일 수준 사용 권한을 구성 해야 합니다. 다음 문서를 계속 진행 합니다.

[3 부: SMB를 통한 디렉터리 및 파일 수준 권한 구성](storage-files-identity-ad-ds-configure-permissions.md)
