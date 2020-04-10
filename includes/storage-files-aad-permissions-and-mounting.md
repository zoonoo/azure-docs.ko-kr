---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/12/2019
ms.author: rogara
ms.custom: include file
ms.openlocfilehash: c88f5a4dd4f2997ce01b1f6a3ae192c62f530e76
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011426"
---
## <a name="2-assign-access-permissions-to-an-identity"></a>2. ID에 액세스 권한 할당

ID 기반 인증을 사용하여 Azure Files 리소스에 액세스하려면 ID(사용자, 그룹 또는 서비스 주체)가 공유 수준에서 필요한 권한이 있어야 합니다. 이 프로세스는 특정 사용자가 파일 공유에 대해 가지고 있는 액세스 유형을 지정하는 Windows 공유 권한을 지정하는 것과 유사합니다. 이 섹션의 지침에서는 파일 공유에 대한 읽기, 쓰기 또는 삭제 권한을 ID에 할당하는 방법을 보여줍니다. 

사용자에게 공유 수준 권한을 부여하기 위한 세 가지 Azure 기본 제공 역할을 도입했습니다.

- **저장소 파일 데이터 SMB 공유 리더를** 사용하면 SMB를 통해 Azure 저장소 파일 공유에서 읽기 액세스를 사용할 수 있습니다.
- **저장소 파일 데이터 SMB 공유 참여자는** SMB를 통해 Azure Storage 파일 공유에서 읽기, 쓰기 및 삭제할 수 있습니다.
- **저장소 파일 데이터 SMB 공유 상승 된 기여자는** SMB를 통해 Azure 저장소 파일 공유에서 NTFS 권한을 읽고, 쓰고, 삭제하고 수정할 수 있습니다.

> [!IMPORTANT]
> 파일의 소유권을 가져가지 는 기능을 포함하여 파일 공유에 대한 모든 관리 제어는 저장소 계정 키를 사용해야합니다. Azure AD 자격 증명으로는 관리 권한이 지원되지 않습니다.

Azure 포털, PowerShell 또는 Azure CLI를 사용하여 공유 수준 권한을 부여하기 위해 사용자의 Azure AD ID에 기본 제공 역할을 할당할 수 있습니다.

> [!NOTE]
> 인증을 위해 AD를 사용하려는 경우 AD 자격 증명을 Azure AD에 동기화해야 합니다. AD에서 Azure AD로의 암호 해시 동기화는 선택 사항입니다. 공유 수준 권한은 AD에서 동기화되는 Azure AD ID에 부여됩니다.

일반적인 권장 사항은 사용자 및 ID 그룹을 나타내는 AD 그룹에 높은 수준의 액세스 관리에 대한 공유 수준 권한을 사용한 다음 디렉터리/파일 수준에서 세분화된 액세스 제어를 위해 NTFS 권한을 활용하는 것입니다. 

#### <a name="azure-portal"></a>Azure portal
Azure [포털을](https://portal.azure.com)사용하여 Azure AD ID에 RBAC 역할을 할당하려면 다음 단계를 따르십시오.

1. Azure 포털에서 파일 공유로 이동하거나 [파일 공유 만들기.](../articles/storage/files/storage-how-to-create-file-share.md)
2. **액세스 제어(IAM)를**선택합니다.
3. **역할 할당 추가** 선택
4. 역할 **할당 블레이드 추가에서** **역할** 목록에서 적절한 기본 제공 역할(저장소 파일 데이터 SMB 공유 리더, 저장소 파일 데이터 SMB 공유 기여자)을 선택합니다. 기본 **설정(** Azure **AD 사용자, 그룹 또는 서비스 주체)** 이름 또는 전자 메일 주소별로 대상 Azure AD ID를 선택합니다.
5. 역할 할당 작업을 완료하려면 **저장을** 선택합니다.

#### <a name="powershell"></a>PowerShell

다음 PowerShell 샘플에서는 로그인 이름을 기반으로 RBAC 역할을 Azure AD ID에 할당하는 방법을 보여 주며 있습니다. PowerShell을 사용하여 RBAC 역할을 할당하는 방법에 대한 자세한 내용은 [RBAC 및 Azure PowerShell을 사용하여 액세스 관리](../articles/role-based-access-control/role-assignments-powershell.md)를 참조하세요.

다음 샘플 스크립트를 실행하기 전에 괄호를 포함한 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
다음 CLI 2.0 명령은 로그인 이름을 기반으로 AZURE AD ID에 RBAC 역할을 할당하는 방법을 보여 주며 있습니다. Azure CLI에 RBAC 역할 할당에 대한 자세한 내용은 [RBAC 및 Azure CLI를 사용하여 액세스 관리를](../articles/role-based-access-control/role-assignments-cli.md)참조하세요. 

다음 샘플 스크립트를 실행하기 전에 괄호를 포함한 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="3-configure-ntfs-permissions-over-smb"></a>3. 중소기업에 대한 NTFS 권한 구성 
RBAC를 사용하여 공유 수준 권한할당을 한 후에는 루트, 디렉터리 또는 파일 수준에서 적절한 NTFS 권한을 할당해야 합니다. 공유 수준 사용 권한은 사용자가 공유에 액세스할 수 있는지 여부를 결정하는 상위 수준의 게이트키퍼라고 생각하면 됩니다. NTFS 권한은 사용자가 디렉터리 또는 파일 수준에서 수행할 수 있는 작업을 결정하기 위해 보다 세분화된 수준에서 작동합니다.

Azure Files는 NTFS 기본 및 고급 권한의 전체 집합을 지원합니다. 공유를 탑재한 다음 Windows 파일 탐색기를 사용하거나 Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) 또는 [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) 명령을 실행하여 Azure 파일 공유의 디렉터리 및 파일에 대한 NTFS 권한을 보고 구성할 수 있습니다. 

수퍼유저 권한으로 NTFS를 구성하려면 도메인에 가입한 VM의 저장소 계정 키를 사용하여 공유를 탑재해야 합니다. 다음 섹션의 지침에 따라 명령 프롬프트에서 Azure 파일 공유를 탑재하고 그에 따라 NTFS 권한을 구성합니다.

파일 공유의 루트 디렉터리에서 지원되는 권한 집합은 다음과 같습니다.

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="configure-ntfs-permissions-with-icacls"></a>icacls를 사용하여 NTFS 권한 구성
루트 디렉터리를 비롯하여 파일 공유에 있는 모든 디렉터리와 파일에 대한 모든 권한을 부여하려면 다음 Windows 명령을 사용합니다. 예제의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

icacls를 사용하여 NTFS 사용 권한을 설정하는 방법과 지원되는 다양한 사용 권한에 대한 자세한 내용은 [icacls에 대한 명령줄 참조를](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)참조하십시오.

### <a name="mount-a-file-share-from-the-command-prompt"></a>명령 프롬프트에서 파일 공유 탑재

Windows **net use** 명령을 사용하여 Azure 파일 공유를 탑재합니다. 다음 예제의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다. 파일 공유 탑재에 대한 자세한 내용은 [Windows에서 Azure 파일 공유 사용을](../articles/storage/files/storage-how-to-use-files-windows.md)참조하십시오.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```
### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Windows 파일 탐색기를 사용 하 고 NTFS 권한 구성
Windows 파일 탐색기를 사용하여 루트 디렉터리를 포함하여 파일 공유 아래의 모든 디렉터리 및 파일에 대한 전체 권한을 부여합니다.

1. Windows 파일 탐색기를 열고 파일/디렉터리오른쪽 단추를 클릭하고 **속성을** 선택합니다.
2. **보안** 탭을 클릭합니다.
3. **편집을 클릭합니다. . .** 사용 권한을 변경하는 단추
4. 기존 사용자의 권한을 변경하거나 **추가를** 클릭하여 새 사용자에게 권한을 부여할 수 있습니다.
5. 새 사용자를 추가하기 위한 프롬프트 창에서 개체 이름 입력에 권한을 부여할 대상 사용자 이름을 **Check Names** **입력하여 선택합니다.**
7.  **확인을** 클릭합니다.
8.  보안 탭에서 새로 추가된 사용자에게 부여할 모든 권한을 선택합니다.
9.  **적용**을 클릭합니다.

## <a name="4-mount-a-file-share-from-a-domain-joined-vm"></a>4. 도메인 에 가입된 VM에서 파일 공유 마운트

다음 프로세스는 파일 공유 및 액세스 권한이 올바르게 설정되었는지, 도메인에 가입한 VM에서 Azure File 공유에 액세스할 수 있는지 확인합니다. 공유 수준 RBAC 역할 할당이 적용되는 데 다소 시간이 걸릴 수 있습니다. 

다음 이미지와 같이 사용 권한을 부여한 Azure AD ID를 사용하여 VM에 로그인합니다. Azure 파일에 대해 AD 인증을 사용하도록 설정한 경우 AD 자격 증명을 사용합니다. Azure AD DS 인증의 경우 Azure AD 자격 증명으로 로그인합니다.

![사용자 인증용 Azure AD 로그인 화면이 표시된 스크린샷](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

다음 명령을 사용하여 Azure 파일 공유를 탑재합니다. 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다. 인증되었기 때문에 저장소 계정 키, AD 자격 증명 또는 Azure AD 자격 증명을 제공할 필요가 없습니다. AD 또는 Azure AD DS를 사용하면 단일 사인온 환경이 지원됩니다.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```
