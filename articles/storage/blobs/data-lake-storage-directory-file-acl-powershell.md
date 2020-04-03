---
title: ACL을 & 파일용 Azure 데이터 레이크 스토리지 Gen2 PowerShell(미리 보기)
description: PowerShell cmdlet을 사용하여 계층적 네임스페이스(HNS)가 활성화된 저장소 계정에서 디렉터리 및 파일 및 디렉터리 액세스 제어 목록(ACL)을 관리합니다.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 9be3b2c9b2624d4cd758081703373a433861e4a7
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585305"
---
# <a name="use-powershell-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>PowerShell을 사용하여 Azure 데이터 레이크 저장소 Gen2의 디렉터리, 파일 및 ACL관리(미리 보기)

이 문서에서는 PowerShell을 사용하여 계층적 네임스페이스(HNS)가 활성화된 저장소 계정에서 디렉터리, 파일 및 사용 권한을 만들고 관리하는 방법을 보여 주며, 이 문서에서는 

> [!IMPORTANT]
> 이 문서에 소개된 PowerShell 모듈은 현재 공개 미리 보기상태입니다.

[Gen 1에서 Gen2 매핑에](#gen1-gen2-map) | [피드백 제공](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>사전 요구 사항

> [!div class="checklist"]
> * Azure 구독 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
> * 계층적 네임스페이스(HNS)가 활성화된 저장소 계정입니다. [다음](data-lake-storage-quickstart-create-account.md) 지침에 따라 만들 수 있습니다.
> * .NET 프레임워크는 4.7.2 이상 설치되어 있습니다. [.NET 프레임워크 다운로드를 참조하십시오.](https://dotnet.microsoft.com/download/dotnet-framework)
> * 파워쉘 `5.1` 버전 이상.

## <a name="install-powershell-modules"></a>PowerShell 모듈 설치

1. 다음 명령을 사용하여 설치한 PowerShell `5.1` 버전이 더 높은지 확인합니다.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   PowerShell 버전을 업그레이드하려면 [기존 Windows PowerShell 업그레이드를](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell) 참조하세요.
    
2. 최신 **PowershellGet** 모듈을 설치합니다. 그런 다음 PowerShell 콘솔을 닫고 다시 엽니다.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force 
   ```

3. **Az.Storage** 미리 보기 모듈을 설치합니다.

   ```powershell
   Install-Module az.storage -RequiredVersion 1.13.3-preview -Repository PSGallery -AllowClobber -AllowPrerelease -Force 
   ```

   PowerShell 모듈을 설치하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 모듈을](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) 참조하십시오.

## <a name="connect-to-the-account"></a>계정에 연결

Windows PowerShell 명령 창을 연 다음 명령을 사용하여 Azure `Connect-AzAccount` 구독에 로그인한 다음 화면의 지침을 따릅니다.

```powershell
Connect-AzAccount
```

ID가 두 개 이상의 구독과 연결된 경우 활성 구독을 만들려는 저장소 계정의 구독으로 설정하고 디렉터리를 관리합니다. 이 예제에서는 `<subscription-id>` 자리 표시자 값을 구독ID로 바꿉습니다.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

그런 다음 명령을 사용하여 저장소 계정에 대한 승인을 얻는 방법을 선택합니다. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>옵션 1: Azure Active Directory(AD)를 사용하여 권한 부여 를 얻습니다.

이 방법을 사용하면 시스템에서 사용자 계정에 적절한 역할 기반 액세스 제어(RBAC) 할당 및 ACL 권한이 있는지 확인합니다. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>옵션 2: 저장소 계정 키를 사용하여 권한 부여 를 얻습니다.

이 방법을 사용하면 시스템에서 RBAC 또는 ACL 권한을 확인하지 않습니다.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

## <a name="create-a-file-system"></a>파일 시스템 만들기

파일 시스템은 파일의 컨테이너 역할을 합니다. cmdlet을 `New-AzDatalakeGen2FileSystem` 사용하여 만들 수 있습니다. 

이 예제는 .라는 `my-file-system`파일 시스템을 만듭니다.

```powershell
$filesystemName = "my-file-system"
New-AzDatalakeGen2FileSystem -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>디렉터리 만들기

cmdlet을 사용하여 디렉터리 참조를 `New-AzDataLakeGen2Item` 만듭니다. 

이 예제는 파일 `my-directory` 시스템에 명명된 디렉터리를 추가합니다.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

이 예제에서는 동일한 디렉터리뿐만 아니라 사용 권한, umask, 속성 값 및 메타데이터 값도 설정합니다. 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>디렉터리 속성 표시

이 예제에서는 cmdlet을 `Get-AzDataLakeGen2Item` 사용 하 여 디렉터리를 가져옵니다 및 콘솔에 속성 값을 인쇄 합니다.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Group
$dir.Owner
$dir.Metadata
$dir.Properties
```

## <a name="rename-or-move-a-directory"></a>디렉터리 이름 바꾸기 또는 이동

`Move-AzDataLakeGen2Item` cmdlet을 사용하여 디렉터리 이름을 바꾸거나 이동합니다.

이 예제는 디렉터리 이름을 `my-directory` 이름에서 `my-new-directory`이름으로 바꿉니다.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> 프롬프트 없이 덮어쓰려면 `-Force` 매개 변수를 사용합니다.

이 예제는 명명된 `my-directory` 디렉터리를 `my-directory-2` 명명된 `my-subdirectory`의 하위 디렉터리로 이동합니다. 이 예제는 하위 디렉터리에도 umask를 적용합니다.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2
```

## <a name="delete-a-directory"></a>디렉터리 삭제

cmdlet을 사용하여 `Remove-AzDataLakeGen2Item` 디렉토리를 삭제합니다.

이 예제는 .라는 `my-directory`디렉터리를 삭제합니다. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

매개 변수를 `-Force` 사용하여 프롬프트 없이 파일을 제거할 수 있습니다.

## <a name="download-from-a-directory"></a>디렉토리에서 다운로드

`Get-AzDataLakeGen2ItemContent` cmdlet을 사용하여 디렉토리에서 파일을 다운로드합니다.

이 예제는 `upload.txt` `my-directory`에서 명명된 디렉터리에서 명명된 파일을 다운로드합니다. 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>디렉터리 콘텐츠 나열

`Get-AzDataLakeGen2ChildItem` cmdlet을 사용하여 디렉터리 내용을 나열합니다. 선택적 매개 변수를 `-OutputUserPrincipalName` 사용하여 개체 ID 대신 사용자의 이름을 얻을 수 있습니다.

이 예제에는 .라는 `my-directory`디렉터리의 내용이 나열됩니다.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

이 예제에서는 `ACL`에 `Permissions` `Group` `Owner` 대한 값을 반환하지 않습니다. 이러한 값을 얻으려면 매개 `-FetchProperty` 변수를 사용합니다. 

다음 예제에서는 동일한 디렉터리내용을 나열하지만 매개 변수를 `-FetchProperty` `ACL`사용하여 `Permissions`에 `Group` `Owner` 대한 값을 반환합니다. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

파일 시스템의 내용을 나열하려면 명령에서 매개 `-Path` 변수를 생략합니다.

## <a name="upload-a-file-to-a-directory"></a>디렉토리에 파일 업로드

cmdlet을 사용하여 디렉토리에 `New-AzDataLakeGen2Item` 파일을 업로드합니다.

이 예제는 `upload.txt` `my-directory`을 라는 디렉터리로 파일을 업로드합니다. 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

이 예제에서는 동일한 파일을 업로드하지만 대상 파일의 권한, umask, 속성 값 및 메타데이터 값을 설정합니다. 이 예제는 이러한 값을 콘솔에 인쇄합니다.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.File.Metadata
$file1.File.Properties
```

## <a name="show-file-properties"></a>파일 속성 표시

이 예제에서는 cmdlet을 `Get-AzDataLakeGen2Item` 사용하여 파일을 가져옵니다.

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.Group
$file.Owner
$file.Metadata
$file.Properties
```

## <a name="delete-a-file"></a>파일 삭제

`Remove-AzDataLakeGen2Item` cmdlet을 사용하여 파일을 삭제합니다.

이 예제는 .라는 `upload.txt`파일을 삭제합니다. 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

매개 변수를 `-Force` 사용하여 프롬프트 없이 파일을 제거할 수 있습니다.

## <a name="manage-access-permissions"></a>액세스 권한 관리

파일 시스템, 디렉터리 및 파일의 액세스 권한을 얻습니다.

> [!NOTE]
> Azure Active Directory(Azure AD)를 사용하여 명령을 승인하는 경우 보안 주체가 저장소 [Blob 데이터 소유자 역할을](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)할당했는지 확인합니다. ACL 사용 권한이 적용되는 방법과 이를 변경하는 효과에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어를](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)참조하십시오.

### <a name="get-permissions"></a>권한 받기

cmdlet을 사용하여 디렉터리 또는 파일의 ACL을 `Get-AzDataLakeGen2Item`가져옵니다.


이 예제는 **파일 시스템의** ACL을 가져옵니다 및 콘솔에 ACL을 인쇄 합니다.

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

이 예제에서는 **디렉터리의**ACL을 가져옵니다.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

이 예제는 **파일의** ACL을 가져옵니다 하 고 콘솔에 ACL을 인쇄 합니다.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

다음 이미지는 디렉터리ACL을 받은 후의 출력을 보여 주며,

![ACL 출력 받기](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

이 예제에서는 소유 사용자에게 사용 권한을 읽고, 쓰고, 실행합니다. 소유 그룹에는 읽기 및 실행 권한만 있습니다. 액세스 제어 목록에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어를](data-lake-storage-access-control.md)참조하십시오.

### <a name="set-or-update-permissions"></a>사용 권한 설정 또는 업데이트

cmdlet을 `set-AzDataLakeGen2ItemAclObject` 사용하여 소유 사용자, 소유 그룹 또는 다른 사용자에 대한 ACL을 만듭니다. 그런 다음 `Update-AzDataLakeGen2Item` cmdlet을 사용하여 ACL을 커밋합니다.

이 예제에서는 소유 사용자, 소유 그룹 또는 다른 사용자에 대한 **파일 시스템의** ACL을 설정하고 콘솔에 ACL을 인쇄합니다.

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$filesystem.ACL
```

이 예제에서는 소유 사용자, 소유 그룹 또는 다른 사용자에 대한 **디렉터리의** ACL을 설정하고 콘솔에 ACL을 인쇄합니다.

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
이 예제에서는 소유 사용자, 소유 그룹 또는 다른 사용자에 대한 **파일의** ACL을 설정하고 콘솔에 ACL을 인쇄합니다.

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

다음 이미지는 파일의 ACL을 설정한 후의 출력을 보여 주며,

![ACL 출력 받기](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

이 예제에서는 소유 사용자 및 소유 그룹에는 읽기 및 쓰기 권한만 있습니다. 다른 모든 사용자에게는 쓰기 및 실행 권한이 있습니다. 액세스 제어 목록에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어를](data-lake-storage-access-control.md)참조하십시오.


### <a name="set-permissions-on-all-items-in-a-file-system"></a>파일 시스템의 모든 항목에 대한 사용 권한 설정

cmdlet과 `Get-AzDataLakeGen2Item` 함께 `-Recurse` 및 매개 변수를 사용하여 파일 시스템의 모든 디렉터리 및 파일의 ACL을 재귀적으로 설정할 수 있습니다. `Update-AzDataLakeGen2Item` 

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse -FetchProperty | Update-AzDataLakeGen2Item -Acl $acl
```
<a id="gen1-gen2-map" />

## <a name="gen1-to-gen2-mapping"></a>Gen 1 ~ Gen2 매핑

다음 표에서는 데이터 레이크 저장소 Gen1에 사용된 cmdlet이 데이터 레이크 저장소 Gen2의 cmdlet에 어떻게 매핑되는지 보여 줍니다.

|데이터 레이크 스토리지 Gen1 cmdlet| 데이터 레이크 스토리지 Gen2 cmdlet| 참고 |
|--------|---------|-----|
|겟 아즈데이데이레이크스토어차일드아이템|겟-아즈데이레이크겐2차일드아이템|기본적으로 Get-AzDataLakeGen2ChildItem cmdlet은 첫 번째 수준의 하위 항목만 나열합니다. -Recurse 매개 변수는 자식 항목을 재귀적으로 나열합니다. |
|겟-아즈데이터레이크스토어아이템<br>겟-아즈데이터레이크스토어항목항목<br>겟-아즈데이데이레이크스토어아이템 오너<br>겟-아즈데이터레이크스토어아이템허가|겟-아즈데이레이크겐2항목|Get-AzDataLakeGen2Item cmdlet의 출력 항목에는 Acl, 소유자, 그룹, 권한 등 이러한 속성이 있습니다.|
|겟-아즈데이데이레이크스토어아이템콘텐츠|겟-아즈데이레이크겐2파일콘텐츠|Get-AzDataLakeGen2File콘텐츠 cmdlet 다운로드 파일 콘텐츠를 로컬 파일에.|
|무브-아즈데이터레이크스토어아이템|무브-아즈데이레이키겐2항목||
|뉴 아즈데이터레이크스토어아이템|뉴 아즈데이레이레이크겐2항목|이 cmdlet은 로컬 파일에서 새 파일 콘텐츠를 업로드합니다.|
|제거-아즈데이레이레이크스토어아이템|제거-아즈데이레이레이크겐2항목||
|세트 아즈데이데이레이크스토어오너<br>세트 아즈데이터레이크스토어아이템권한<br>세트 아즈데이데이레이크스토어아이템아클|업데이트-아즈데이레이레이크겐2항목|업데이트-AzDataLakeGen2Item cmdlet은 단일 항목만 업데이트하며 재귀적으로 업데이트하지 않습니다. 재귀적으로 업데이트하려면 Get-AzDataLakeStoreChildItem cmdlet을 사용하여 항목을 나열한 다음 업데이트-AzDataLakeGen2Item cmdlet으로 파이프라인을 지정합니다.|
|테스트-아즈데이터레이크스토어아이템|겟-아즈데이레이크겐2항목|항목이 없는 경우 Get-AzDataLakeGen2Item cmdlet에 오류가 보고됩니다.|



## <a name="see-also"></a>참고 항목

* [알려진 문제](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Azure 저장소와 함께 Azure PowerShell 을 사용 하 여](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)입니다.
* [스토리지 파워쉘 cmdlet.](/powershell/module/az.storage)

