---
title: 포함 파일
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/26/2020
ms.author: rogara
ms.custom: include file
ms.openlocfilehash: 4773446ec0007ffbed99bc01939d1f92f5823d99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95554286"
---
## <a name="assign-access-permissions-to-an-identity"></a>ID에 액세스 권한 할당

ID 기반 인증을 사용하여 Azure Files 리소스에 액세스하려는 경우 ID(사용자, 그룹 또는 서비스 주체)에 공유 수준에서 필요한 권한이 있어야 합니다. 이 프로세스는 파일 공유에 대한 특정 사용자의 액세스 권한 유형을 지정하는 Windows 공유 권한 지정 과정과 비슷합니다. 이 섹션의 지침에서는 파일 공유에 대한 읽기, 쓰기 또는 삭제 권한을 ID에 할당하는 방법을 보여줍니다. 

사용자에게 공유 수준 권한을 부여하기 위한 세 가지 Azure 기본 제공 역할이 도입되었습니다.

- **스토리지 파일 데이터 SMB 공유 읽기 권한자** 는 SMB를 통한 Azure Storage 파일 공유에서 읽기 권한을 허용합니다.
- **스토리지 파일 데이터 SMB 공유 기여자** 는 SMB를 통해 Azure Storage 파일 공유에 대한 읽기, 쓰기, 삭제 액세스를 허용합니다.
- **스토리지 파일 데이터 SMB 공유 상승된 기여자** 는 SMB를 통한 Azure Storage 파일 공유에서 NTFS 읽기, 쓰기, 삭제, 수정 권한을 허용합니다.

> [!IMPORTANT]
> 파일 소유권을 가져오는 기능을 포함하여 파일 공유에 대한 모든 관리 권한을 사용하려면 스토리지 계정 키가 있어야 합니다. Azure AD 자격 증명으로는 관리 권한이 지원되지 않습니다.

Azure Portal, PowerShell 또는 Azure CLI를 사용하여 공유 수준 권한을 부여하는 사용자의 Azure AD ID에 기본 제공 역할을 할당할 수 있습니다. 공유 수준 Azure 역할 할당은 적용되는 데 어느 정도 시간이 걸릴 수 있습니다. 

> [!NOTE]
> 인증에 온-프레미스 AD DS를 사용하려는 경우 [AD DS 자격 증명을 Azure AD와 동기화](../articles/active-directory/hybrid/how-to-connect-install-roadmap.md)해야 합니다. AD DS에서 Azure AD로의 암호 해시 동기화는 선택 사항입니다. 공유 수준 권한은 온-프레미스 AD DS에서 동기화되는 Azure AD ID에 부여됩니다.

일반적인 권장 사항은 사용자 및 ID 그룹을 나타내는 AD 그룹에 대한 대략적인 액세스 관리에 공유 수준 권한을 사용한 다음, 디렉터리/파일 수준에 대한 세부적인 액세스 제어에 NTFS 권한을 활용하는 것입니다. 

### <a name="assign-an-azure-role-to-an-ad-identity"></a>AD ID에 Azure 역할 할당

# <a name="portal"></a>[포털](#tab/azure-portal)
[Azure Portal](https://portal.azure.com)을 사용하여 Azure AD ID에 Azure 역할을 할당하려면 다음 단계를 수행합니다.

1. Azure Portal에서 파일 공유로 이동하거나 [파일 공유를 만듭니다](../articles/storage/files/storage-how-to-create-file-share.md).
2. **Access Control(IAM)** 을 선택합니다.
3. **역할 할당 추가** 를 선택합니다.
4. **역할 할당 추가** 블레이드의 **역할** 목록에서 적절 한 기본 제공 역할(스토리지 파일 데이터 SMB 공유 읽기 권한자, 스토리지 파일 데이터 SMB 공유 기여자)을 선택합니다. **다음에 대한 액세스 할당** 은 기본 설정인 **Azure AD 사용자, 그룹 또는 서비스 주체** 로 그대로 둡니다. 이름 또는 이메일 주소를 기준으로 대상 Azure AD ID를 선택합니다.
5. **저장** 을 선택하여 역할 할당 작업을 완료합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

다음 PowerShell 샘플은 로그인 이름을 기준으로 Azure AD ID에 Azure 역할을 할당하는 방법을 보여 줍니다. PowerShell을 사용하여 Azure 역할을 할당하는 방법에 대한 자세한 내용은 [RBAC 및 Azure PowerShell을 사용하여 액세스 관리](../articles/role-based-access-control/role-assignments-powershell.md)를 참조하세요.

다음 샘플 스크립트를 실행하기 전에 대괄호를 포함한 자리 표시자 값을 사용자 고유의 값으로 변경해야 합니다.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
  
다음 CLI 2.0 명령은 로그인 이름을 기준으로 Azure AD ID에 Azure 역할을 할당하는 방법을 보여 줍니다. Azure CLI를 사용하여 Azure 역할을 할당하는 방법에 대한 자세한 내용은 [RBAC 및 Azure CLI를 사용하여 액세스 관리](../articles/role-based-access-control/role-assignments-cli.md)를 참조하세요. 

다음 샘플 스크립트를 실행하기 전에 대괄호를 포함한 자리 표시자 값을 사용자 고유의 값으로 변경해야 합니다.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```
---

## <a name="configure-ntfs-permissions-over-smb"></a>SMB를 통한 NTFS 권한 구성

RBAC를 사용하여 공유 수준 권한할당을 한 후에는 루트, 디렉터리 또는 파일 수준에서 적절한 NTFS 권한을 할당해야 합니다. 공유 수준 권한은 사용자가 공유에 액세스할 수 있는지 여부를 결정하는 상위 수준의 게이트키퍼로 생각하면 됩니다. 반면에 NTFS는 보다 세부적인 수준에서 작동하여 사용자가 디렉터리 또는 파일 수준에서 수행할 수 있는 작업을 결정합니다.

Azure Files는 NTFS 기본 및 고급 권한의 전체 집합을 지원합니다. 공유를 탑재한 다음, Windows 파일 탐색기를 사용하거나 Windows [icacls](/windows-server/administration/windows-commands/icacls) 또는 [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl) 명령을 실행하여 Azure 파일 공유의 디렉터리와 파일에 대한 NTFS 권한을 확인하고 구성할 수 있습니다. 

슈퍼 사용자 권한으로 NTFS를 구성하려면 사용자가 도메인 조인 VM에서 스토리지 계정 키를 사용하여 공유를 탑재해야 합니다. 다음 섹션의 지침에 따라 명령 프롬프트에서 Azure 파일 공유를 탑재하고 NTFS 권한을 적절하게 구성하세요.

파일 공유의 루트 디렉터리에서 지원되는 권한 집합은 다음과 같습니다.

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>명령 프롬프트에서 파일 공유 탑재

Windows **net use** 명령을 사용하여 Azure 파일 공유를 탑재합니다. 다음 예제의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다. 파일 공유 탑재에 대한 자세한 내용은 [Windows에서 Azure 파일 공유 사용](../articles/storage/files/storage-how-to-use-files-windows.md)을 참조하세요. 

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
 net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
 Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

Azure Files에 연결하는 데 문제가 발생하는 경우 [Windows에서 발생하는 Azure Files 탑재 오류를 위해 게시된 문제 해결 도구](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/)를 참조하세요. 또한 포트 445가 차단되는 시나리오를 해결하기 위한 [지침](../articles/storage/files/storage-files-faq.md#on-premises-access)도 제공합니다. 


### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Windows 파일 탐색기를 사용하여 NTFS 사용 권한 구성

루트 디렉터리를 비롯하여 파일 공유에 있는 모든 디렉터리와 파일에 대한 모든 권한을 부여하려면 Windows 파일 탐색기를 사용합니다.

1. Windows 파일 탐색기를 열고 파일/디렉터리를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다.
2. **보안** 탭을 선택합니다.
3. **편집** 을 선택하여 권한을 변경합니다.
4. 기존 사용자의 권한을 변경하거나 **추가...** 를 선택하여 새 사용자에게 권한을 부여할 수 있습니다.
5. 새 사용자 추가 프롬프트 창에서 **선택할 개체 이름 입력** 상자에 권한을 부여하려는 대상 사용자 이름을 입력하고 **이름 확인** 을 선택하여 대상 사용자의 전체 UPN 이름을 찾습니다.
7.    **확인** 을 선택합니다.
8.    **보안** 탭에서 새 사용자에게 부여할 권한을 모두 선택합니다.
9.    **적용** 을 선택합니다.

### <a name="configure-ntfs-permissions-with-icacls"></a>icacls를 사용하여 NTFS 권한 구성

루트 디렉터리를 비롯하여 파일 공유에 있는 모든 디렉터리와 파일에 대한 모든 권한을 부여하려면 다음 Windows 명령을 사용합니다. 예제의 자리 표시자 값은 실제 값으로 바꾸세요.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

icacls를 사용하여 NTFS 권한을 설정하는 방법과 지원되는 여러 권한 유형에 대한 자세한 내용은 [icacls용 명령줄 참조](/windows-server/administration/windows-commands/icacls)를 참조하세요.

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>도메인 조인 VM에서 파일 공유 탑재

다음 프로세스는 내 파일 공유 및 액세스 권한이 올바르게 설정되어 있는지 확인하고 도메인 조인 VM에서 Azure 파일 공유에 액세스할 수 있는지 확인합니다. 공유 수준 Azure 역할 할당은 적용되는 데 어느 정도 시간이 걸릴 수 있습니다. 

먼저 아래 그림과 같이 권한을 부여한 Azure AD ID를 사용하여 VM에 로그인합니다. Azure Files에 대해 온-프레미스 AD DS 인증을 사용하도록 설정한 경우 AD DS 자격 증명을 사용합니다. Azure AD DS 인증의 경우 Azure AD 자격 증명을 사용하여 로그인합니다.

![사용자 인증용 Azure AD 로그인 화면이 표시된 스크린샷](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

다음 명령을 사용하여 Azure 파일 공유를 탑재합니다. 예제의 자리 표시자 값은 실제 값으로 바꾸세요. 인증되었으므로 스토리지 계정 키, 온-프레미스 AD DS 자격 증명 또는 Azure AD DS 자격 증명을 제공할 필요가 없습니다. Single Sign-On 환경은 온-프레미스 AD DS 또는 Azure AD DS 인증에 대해 지원됩니다. AD DS 자격 증명으로 탑재하는 데 문제가 발생하는 경우 [Windows에서 Azure Files 문제 해결](../articles/storage/files/storage-troubleshoot-windows-file-connection-problems.md)을 참조하세요.

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
 net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
 Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}
```