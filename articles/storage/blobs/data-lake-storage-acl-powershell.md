---
title: PowerShell을 사용 하 여 Azure Data Lake Storage Gen2에서 Acl 관리
description: PowerShell cmdlet을 사용 하 여 HNS (계층적 네임 스페이스)를 사용 하도록 설정 된 저장소 계정에서 ACL (액세스 제어 목록)을 관리 합니다.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: dd522355d30564d84fec15bdc57c7397c1e6cfe4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104702544"
---
# <a name="use-powershell-to-manage-acls-in-azure-data-lake-storage-gen2"></a>PowerShell을 사용 하 여 Azure Data Lake Storage Gen2에서 Acl 관리

이 문서에서는 PowerShell을 사용 하 여 디렉터리 및 파일의 액세스 제어 목록을 가져오고, 설정 하 고, 업데이트 하는 방법을 보여 줍니다. 

부모 디렉터리 아래에 생성 된 새 자식 항목에는 ACL 상속이 이미 사용할 수 있습니다. 그러나 각 자식 항목에 대해 개별적으로 변경할 필요 없이 부모 디렉터리의 기존 자식 항목에서 Acl을 재귀적으로 추가, 업데이트 및 제거할 수도 있습니다. 

[참조](/powershell/module/Az.Storage/)  |  [재귀 ACL 샘플](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D)  |  [사용자 의견 제공](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

- HNS(계층 구조 네임스페이스)를 사용하도록 설정된 스토리지 계정입니다. 만들려면 [다음 지침](create-data-lake-storage-account.md)을 수행합니다.

- Azure CLI 버전 `2.6.0` 이상.

- 다음 보안 권한 중 하나입니다.

  - 대상 컨테이너, 부모 리소스 그룹 또는 구독의 범위에서 [저장소 Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 역할이 할당 된 프로 비전 된 AD (Azure Active Directory) [보안 주체](../../role-based-access-control/overview.md#security-principal) 입니다.  

  - ACL 설정을 적용 하려는 대상 컨테이너 또는 디렉터리를 소유 하는 사용자입니다. Acl을 재귀적으로 설정 하기 위해 대상 컨테이너 또는 디렉터리의 모든 자식 항목을 포함 합니다.
  
  - 저장소 계정 키입니다.

## <a name="install-the-powershell-module"></a>PowerShell 모듈 설치

1. 다음 명령을 사용 하 여 설치한 PowerShell 버전이 이상 인지 확인 `5.1` 합니다.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```

   PowerShell 버전을 업그레이드 하려면 [기존 Windows Powershell 업그레이드](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell) 를 참조 하세요.

2. 설치 **Az. Storage** module.

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   PowerShell 모듈을 설치 하는 방법에 대 한 자세한 내용은 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps) 를 참조 하세요.

## <a name="connect-to-the-account"></a>계정에 연결

명령을 통해 저장소 계정에 대 한 권한 부여를 가져오는 방법을 선택 합니다. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>옵션 1: Azure Active Directory (AD)를 사용 하 여 권한 부여 가져오기

> [!NOTE]
> Azure AD (Azure Active Directory)를 사용 하 여 액세스 권한을 부여 하는 경우 보안 주체가 [저장소 Blob 데이터 소유자 역할](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)에 할당 되었는지 확인 합니다. ACL 사용 권한을 적용 하는 방법 및 변경의 영향에 대 한 자세한 내용은  [Azure Data Lake Storage Gen2의 액세스 제어 모델](./data-lake-storage-access-control-model.md)을 참조 하세요.

이 접근 방식을 사용 하는 경우 시스템은 사용자 계정에 적절 한 Azure RBAC (역할 기반 액세스 제어) 할당 및 ACL 권한이 있는지 확인 합니다.

1. Windows PowerShell 명령 창을 열고 명령을 사용 하 여 Azure 구독에 로그인 하 `Connect-AzAccount` 고 화면의 지시를 따릅니다.

   ```powershell
   Connect-AzAccount
   ```

2. Id가 둘 이상의 구독과 연결 된 경우 활성 구독을 디렉터리를 만들고 관리 하려는 저장소 계정의 구독으로 설정 합니다. 이 예에서는 `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

   ```powershell
   Select-AzSubscription -SubscriptionId <subscription-id>
   ``` 

3. 저장소 계정 컨텍스트를 가져옵니다.

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
   ```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>옵션 2: 저장소 계정 키를 사용 하 여 권한 부여 가져오기

이 방법을 사용 하면 시스템에서 Azure RBAC 또는 ACL 사용 권한을 확인 하지 않습니다. 계정 키를 사용 하 여 저장소 계정 컨텍스트를 가져옵니다.

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -StorageAccountKey '<storage-account-key>'
```

## <a name="get-acls"></a>Acl 가져오기

Cmdlet을 사용 하 여 디렉터리 또는 파일의 ACL을 가져옵니다 `Get-AzDataLakeGen2Item` .

이 예제에서는 **컨테이너** 의 루트 디렉터리에 대 한 acl을 가져온 다음이 acl을 콘솔에 출력 합니다.

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

이 예에서는 **디렉터리** 의 acl을 가져온 다음 해당 acl을 콘솔에 출력 합니다.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

이 예제에서는 **파일** 의 acl을 가져온 다음 해당 acl을 콘솔에 출력 합니다.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

다음 이미지는 디렉터리의 ACL을 가져온 후 출력을 보여 줍니다.

![디렉터리에 대 한 ACL 출력 가져오기](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

이 예제에서 소유 사용자에게 읽기, 쓰기 및 실행 권한이 있습니다. 소유 그룹에는 읽기 및 실행 권한만 있습니다. 액세스 제어 목록에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](data-lake-storage-access-control.md)를 참조하세요.

## <a name="set-acls"></a>Acl 설정

ACL을 *설정* 하는 경우 모든 항목을 포함 하 여 전체 acl을 **바꿉니다** . 보안 주체의 권한 수준을 변경 하거나 다른 기존 항목에 영향을 주지 않고 ACL에 새 보안 주체를 추가 하려면 대신 ACL을 *업데이트* 해야 합니다. ACL을 대체 하는 대신 업데이트 하려면이 문서의 [Acl 업데이트](#update-acls) 섹션을 참조 하세요.  

ACL을 *설정* 하도록 선택한 경우 소유 하는 사용자에 대 한 항목, 소유 그룹에 대 한 항목 및 다른 모든 사용자에 대 한 항목을 추가 해야 합니다. 소유 하는 사용자, 소유 그룹 및 다른 모든 사용자에 대 한 자세한 내용은 [사용자 및 id](data-lake-storage-access-control.md#users-and-identities)를 참조 하세요.

이 섹션에서는 다음을 수행 하는 방법을 보여 줍니다.

- ACL 설정
- 반복적으로 ACL 설정

### <a name="set-an-acl"></a>ACL 설정

Cmdlet을 사용 `set-AzDataLakeGen2ItemAclObject` 하 여 소유 하는 사용자, 소유 그룹 또는 기타 사용자에 대 한 ACL을 만듭니다. 그런 다음 cmdlet을 사용 `Update-AzDataLakeGen2Item` 하 여 ACL을 커밋합니다.

이 예제에서는 소유 사용자, 소유 그룹 또는 기타 사용자에 대 한 **컨테이너** 의 루트 디렉터리에 대 한 acl을 설정 하 고 콘솔에 acl을 인쇄 합니다.

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

이 예에서는 소유 사용자, 소유 그룹 또는 기타 사용자에 대 한 **디렉터리** 의 acl을 설정 하 고 콘솔에 acl을 인쇄 합니다.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

> [!NOTE]
> **기본** ACL 항목을 설정 하려면 **AzDataLakeGen2ItemAclObject** 명령을 실행할 때 **-defaultscope** 매개 변수를 사용 합니다. 예를 들어 `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`을 참조하십시오. 

이 예에서는 소유 사용자, 소유 그룹 또는 기타 사용자에 대 한 **파일** 에 acl을 설정 하 고 콘솔에 acl을 인쇄 합니다.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

> [!NOTE]
> 특정 그룹 또는 사용자의 ACL을 설정 하려면 해당 개체 Id를 사용 합니다. 예를 들어 `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 또는 `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`입니다.

다음 이미지는 파일의 ACL을 설정한 후 출력을 보여 줍니다.

![파일에 대 한 ACL 출력 가져오기](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

다음 예제에서는 소유 사용자 및 소유 그룹에 읽기 및 쓰기 권한만 있습니다. 다른 모든 사용자에게는 쓰기 및 실행 권한이 있습니다. 액세스 제어 목록에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](data-lake-storage-access-control.md)를 참조하세요.

### <a name="set-acls-recursively"></a>반복적으로 ACL 설정

**AzDataLakeGen2AclRecursive** cmdlet을 사용 하 여 acl을 재귀적으로 설정 합니다.

이 예에서는 라는 디렉터리의 ACL을 설정 합니다 `my-parent-directory` . 이러한 항목은 소유 하는 사용자에 게 읽기, 쓰기 및 실행 권한을 부여 하 고, 소유 그룹만 읽기 및 실행 권한을 부여 하 고, 다른 모든 사용자에 게 액세스 권한을 부여 하지는 않습니다. 이 예제의 마지막 ACL 항목은 개체 ID "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" 읽기 및 실행 권한이 있는 특정 사용자를 제공 합니다.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl 

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> **기본** ACL 항목을 설정 하려면 **AzDataLakeGen2ItemAclObject** 명령을 실행할 때 **-defaultscope** 매개 변수를 사용 합니다. 예를 들어 `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`을 참조하십시오.

일괄 처리 크기를 지정 하 여 일괄 처리에서 Acl을 재귀적으로 설정 하는 예제를 보려면 [AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) 참조 문서를 참조 하세요.

## <a name="update-acls"></a>Acl 업데이트

Acl을 *업데이트할* 때 acl을 대체 하는 대신 acl을 수정 합니다. 예를 들어 ACL에 나열 된 다른 보안 주체에 영향을 주지 않고 ACL에 새 보안 주체를 추가할 수 있습니다.  ACL을 업데이트 하지 않고 바꾸려면이 문서의 [Acl 설정](#set-acls) 섹션을 참조 하세요.

ACL을 업데이트 하려면 업데이트 하려는 ACL 항목을 사용 하 여 새 ACL 개체를 만든 다음 ACL 업데이트 작업에서 해당 개체를 사용 합니다. 기존 ACL을 가져오지 말고 업데이트할 ACL 항목만 제공 하면 됩니다.

이 섹션에서는 다음을 수행 하는 방법을 보여 줍니다.

- ACL 업데이트
- Acl을 재귀적으로 업데이트

### <a name="update-an-acl"></a>ACL 업데이트

먼저 ACL을 가져옵니다. 그런 다음 cmdlet을 사용 `set-AzDataLakeGen2ItemAclObject` 하 여 ACL 항목을 추가 하거나 업데이트 합니다. Cmdlet을 사용 `Update-AzDataLakeGen2Item` 하 여 ACL을 커밋합니다.

이 예에서는 사용자에 대 한 **디렉터리** 에서 ACL을 만들거나 업데이트 합니다.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -InputObject $acl 
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```

> [!NOTE]
> **기본** ACL 항목을 업데이트 하려면 **AzDataLakeGen2ItemAclObject** 명령을 실행할 때 **-defaultscope** 매개 변수를 사용 합니다. 예를 들어 `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -DefaultScope`을 참조하십시오.

### <a name="update-acls-recursively"></a>Acl을 재귀적으로 업데이트

**AzDataLakeGen2AclRecursive** cmdlet을 사용 하 여 acl을 재귀적으로 업데이트 합니다.

이 예에서는 write 권한이 있는 ACL 항목을 업데이트 합니다.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> 특정 그룹 또는 사용자의 ACL을 설정 하려면 해당 개체 Id를 사용 합니다. 예를 들어 `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 또는 `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`입니다.

일괄 처리 크기를 지정 하 여 일괄 처리에서 Acl을 재귀적으로 업데이트 하는 예제를 보려면 [AzDataLakeGen2AclRecursive](/powershell/module/az.storage/update-azdatalakegen2aclrecursive) 참조 문서를 참조 하세요.

## <a name="remove-acl-entries"></a>ACL 항목 제거

이 섹션에서는 다음을 수행 하는 방법을 보여 줍니다.

- ACL 항목 제거
- ACL 항목을 재귀적으로 제거

### <a name="remove-an-acl-entry"></a>ACL 항목 제거

이 예에서는 기존 ACL에서 항목을 제거 합니다.

```powershell
$id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "User"-and $a.DefaultScope -eq $false -and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $aclnew
```

### <a name="remove-acl-entries-recursively"></a>ACL 항목을 재귀적으로 제거

하나 이상의 ACL 항목을 재귀적으로 제거할 수 있습니다. ACL 항목을 제거 하려면 acl 항목을 제거할 새 ACL 개체를 만든 다음 ACL 제거 작업에서 해당 개체를 사용 합니다. 기존 ACL을 가져오지 말고 제거할 ACL 항목만 제공 하면 됩니다.

**AzDataLakeGen2AclRecursive** cmdlet을 사용 하 여 ACL 항목을 제거 합니다.

이 예에서는 컨테이너의 루트 디렉터리에서 ACL 항목을 제거 합니다.  

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" 

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

> [!NOTE]
> **기본** ACL 항목을 제거 하려면 **AzDataLakeGen2ItemAclObject** 명령을 실행할 때 **-defaultscope** 매개 변수를 사용 합니다. 예를 들어 `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" -DefaultScope`을 참조하십시오.

일괄 처리 크기를 지정 하 여 일괄 처리에서 Acl을 재귀적으로 제거 하는 예제를 보려면 [AzDataLakeGen2AclRecursive](/powershell/module/az.storage/remove-azdatalakegen2aclrecursive) 참조 문서를 참조 하세요.

## <a name="recover-from-failures"></a>오류에서 복구

Acl을 재귀적으로 수정할 때 런타임 또는 권한 오류가 발생할 수 있습니다. 

런타임 오류의 경우 처음부터 프로세스를 다시 시작 합니다. 보안 주체에 수정 되는 디렉터리 계층 구조에 있는 디렉터리 또는 파일의 ACL을 수정할 수 있는 권한이 없으면 권한 오류가 발생할 수 있습니다. 권한 문제를 해결 한 다음 연속 토큰을 사용 하 여 오류 지점에서 프로세스를 다시 시작 하거나 프로세스를 처음부터 다시 시작 하도록 선택 합니다. 처음부터 다시 시작 하는 것을 선호 하는 경우 연속 토큰을 사용할 필요가 없습니다. 부정적인 영향 없이 ACL 항목을 다시 적용할 수 있습니다.

이 예에서는 결과를 변수로 반환 하 고 실패 한 항목을 형식이 지정 된 테이블로 파이프 합니다.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft 
```

테이블의 출력에 따라 모든 사용 권한 오류를 수정한 다음 연속 토큰을 사용 하 여 실행을 다시 시작할 수 있습니다.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

일괄 처리 크기를 지정 하 여 일괄 처리에서 Acl을 재귀적으로 설정 하는 예제를 보려면 [AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) 참조 문서를 참조 하세요.

권한 오류가 발생 해도 프로세스가 중단 되지 않도록 하려면이를 지정할 수 있습니다.

이 예제에서는 매개 변수를 사용 하 여 `ContinueOnFailure` 작업에 사용 권한 오류가 발생 해도 계속 실행 되도록 합니다. 

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinueOnFailure

echo "[Result Summary]"
echo "TotalDirectoriesSuccessfulCount: `t$($result.TotalFilesSuccessfulCount)"
echo "TotalFilesSuccessfulCount: `t`t`t$($result.TotalDirectoriesSuccessfulCount)"
echo "TotalFailureCount: `t`t`t`t`t$($result.TotalFailureCount)"
echo "FailedEntries:"$($result.FailedEntries | ft) 
```

일괄 처리 크기를 지정 하 여 일괄 처리에서 Acl을 재귀적으로 설정 하는 예제를 보려면 [AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) 참조 문서를 참조 하세요.

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>참고 항목

- [알려진 문제](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [스토리지 PowerShell cmdlet](/powershell/module/az.storage)
- [Azure Data Lake Storage Gen2의 액세스 제어 모델](data-lake-storage-access-control.md)
- [Azure Data Lake Storage Gen2의 Acl (액세스 제어 목록)](data-lake-storage-access-control.md)
