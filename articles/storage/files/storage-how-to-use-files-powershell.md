---
title: Azure PowerShell을 사용하여 Azure 파일 공유를 관리하기 위한 빠른 시작
description: 이 빠른 시작을 사용하여 Azure PowerShell을 사용하여 Azure 파일 공유를 관리하는 방법을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2d67d3d695ce6ba90e01603e262fb014fffc9709
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90561570"
---
# <a name="quickstart-create-and-manage-an-azure-file-share-with-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 Azure 파일 공유 만들기 및 관리 
이 가이드에서는 PowerShell을 사용하여 [Azure 파일 공유](storage-files-introduction.md)로 작업하는 기본 사항을 설명합니다. Azure 파일 공유는 다른 파일 공유와 유사하지만, 클라우드에 저장되고 Azure Platform에서 지원합니다. Azure 파일 공유는 업계 표준 SMB(서버 메시지 블록) 프로토콜, NFS(네트워크 파일 시스템) 프로토콜(미리 보기)을 지원하고 여러 머신, 애플리케이션 및 인스턴스에서 파일 공유가 가능하도록 합니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하려는 경우 이 자습서에서는 Azure PowerShell 모듈 Az 버전 0.7 이상이 필요합니다. 실행 중인 Azure PowerShell 모듈의 버전을 알아보려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. PowerShell을 로컬로 실행하는 경우 `Login-AzAccount`를 실행하여 Azure 계정에 로그인해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기
리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. Azure 리소스 그룹이 아직 없는 경우 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet을 사용하여 새 리소스 그룹을 만들 수 있습니다. 

다음 예제에서는 *myResourceGroup*이라는 리소스 그룹을 미국 서부 2 지역에 만듭니다.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$region = "westus2"

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $region | Out-Null
```

## <a name="create-a-storage-account"></a>스토리지 계정 만들기
스토리지 계정은 Azure 파일 공유를 배포하는 데 사용할 수 있는 스토리지의 공유 풀입니다. 스토리지 계정에 포함할 수 있는 공유 수에는 제한이 없으며, 공유에 저장할 수 있는 파일 수에는 스토리지 계정의 최대 용량 한도까지 제한이 없습니다. 다음 예제에서는 표준 Azure 파일 공유 또는 다른 스토리지 리소스(예: Blob 또는 큐)를 HDD(하드 디스크 드라이브) 회전 미디어에 저장할 수 있는 GPv2(범용 버전 2) 스토리지 계정을 만듭니다. Azure Files는 프리미엄 SSD(반도체 디스크 드라이브)도 지원합니다. 프리미엄 Azure 파일 공유는 FileStorage 스토리지 계정에서 만들 수 있습니다.

이 예제에서는 [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) cmdlet을 사용하여 스토리지 계정을 만듭니다. 스토리지 계정의 이름은 *mystorageaccount\<random number>* 이며 이 스토리지 계정에 대한 참조는 **$storageAcct** 변수에 저장됩니다. 스토리지 계정 이름은 고유해야 합니다. 따라서 `Get-Random`를 사용하여 고유하게 만들 이름에 숫자를 추가합니다. 

```azurepowershell-interactive 
$storageAccountName = "mystorageacct$(Get-Random)"

$storageAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $region `
    -Kind StorageV2 `
    -SkuName Standard_ZRS `
    -EnableLargeFileShare
```

> [!Note]  
> 5TiB보다 큰 공유(공유당 최대 100TiB)는 LRS(로컬 중복) 및 ZRS(영역 중복) 스토리지 계정에서만 사용할 수 있습니다. GRS(지역 중복) 또는 GZRS(지리적 영역 중복) 스토리지 계정을 만들려면 `-EnableLargeFileShare` 매개 변수를 제거합니다.

## <a name="create-an-azure-file-share"></a>Azure 파일 공유 만들기
이제 첫 번째 Azure 파일 공유를 만들 수 있습니다. 파일 공유는 [New-AzRmStorageShare](/powershell/module/az.storage/New-AzRmStorageShare) cmdlet을 사용하여 만들 수 있습니다. 이 예제에서는 `myshare`라는 공유를 만듭니다.

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $shareName `
    -EnabledProtocol SMB `
    -QuotaGiB 1024 | Out-Null
```

공유 이름은 모두 소문자, 숫자 및 단일 하이픈이어야 하지만 하이픈으로 시작할 수 없습니다. 파일 공유 및 파일 이름 지정에 대한 자세한 내용은 [공유, 디렉터리, 파일 및 메타데이터 이름 지정 및 참조](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)를 참조하세요.

## <a name="use-your-azure-file-share"></a>Azure 파일 공유 사용
Azure Files는 Azure 파일 공유 내에서 파일 및 폴더를 작업하는 두 가지 방법(업계 표준 [SMB(서버 메시지 블록) 프로토콜](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) 및 [File REST 프로토콜](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api))을 제공합니다. 

SMB를 사용하여 파일 공유를 탑재하려면 OS에 따라 다음 문서를 참조하세요.
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>File REST 프로토콜로 Azure 파일 공유 사용 
File REST 프로토콜을 직접 사용할 수 있지만(즉, REST HTTP 호출 직접 작성), File REST 프로토콜을 사용하는 가장 일반적인 방법은 Azure PowerShell 모듈, [Azure CLI](storage-how-to-use-files-cli.md) 또는 Azure Storage SDK를 사용하는 것입니다. 이 모두는 File REST 프로토콜에 대한 훌륭한 래퍼를 선택한 스크립팅/프로그래밍 언어로 제공합니다.  

대부분의 경우 Azure 파일 공유에서는 기존 애플리케이션과 도구를 사용할 수 있기 때문에 SMB 프로토콜이 아닌 Azure 파일 공유를 선택하겠지만, SMB보다 파일 REST API를 사용하는 것이 더 나은 이유가 다음과 같이 몇 가지 있습니다.

- PowerShell Cloud Shell에서 파일 공유를 탐색하고 있는 경우(SMB를 통해 파일 공유를 탑재할 수 없음)
- 서버리스 리소스([Azure Functions](../../azure-functions/functions-overview.md) 등)를 활용하고 있는 경우 
- 백업 또는 바이러스 백신 검사를 수행하는 것과 같은 많은 Azure 파일 공유와 상호 작용하는 값 추가 서비스를 만듭니다.

다음 예제에서는 Azure PowerShell 모듈을 사용하여 File REST 프로토콜로 Azure 파일 공유를 조작하는 방법을 보여줍니다. `-Context` 매개 변수는 파일 공유에 대해 표시된 작업을 수행하기 위해 스토리지 계정 키를 검색하는 데 사용됩니다. 스토리지 계정 키를 검색하려면 스토리지 계정에 `Owner` Azure 역할이 있어야 합니다.

#### <a name="create-directory"></a>디렉터리 만들기
Azure 파일 공유의 루트에 *myDirectory*라는 새 디렉터리를 만들려면 [New-AzStorageDirectory](/powershell/module/az.storage/New-AzStorageDirectory) cmdlet을 사용합니다.

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Path "myDirectory"
```

#### <a name="upload-a-file"></a>파일 업로드
[Set-AzStorageFileContent](/powershell/module/az.storage/Set-AzStorageFileContent) cmdlet을 사용하여 파일을 업로드하는 방법을 설명하려면 먼저 업로드할 PowerShell Cloud Shell의 임시 드라이브 내에 파일을 만들어야 합니다. 

이 예제에서는 임시 드라이브의 새 파일에 현재 날짜 및 시간을 저장한 다음, 파일 공유에 파일을 업로드합니다.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
cd "~/CloudDrive/"
Get-Date | Out-File -FilePath "SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Source "SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

PowerShell을 로컬로 실행 중인 경우 `~/CloudDrive/`를 컴퓨터에 있는 경로로 대체해야 합니다.

파일을 업로드한 후에 [Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile) cmdlet을 사용하여 파일이 Azure 파일 공유에 업로드되었는지 확인할 수 있습니다. 

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\" 
```

#### <a name="download-a-file"></a>파일 다운로드
[Get-AzStorageFileContent](/powershell/module/az.storage/Get-AzStorageFilecontent) cmdlet을 사용하여 Cloud Shell의 임시 드라이브에 업로드된 파일의 복사본을 다운로드할 수 있습니다.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item `
    -Path "SampleDownload.txt" `
    -Force `
    -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt" `
    -Destination "SampleDownload.txt"
```

파일에 다운로드한 후에 `Get-ChildItem`을 사용하여 PowerShell Cloud Shell 임시 드라이브에 파일을 다운로드했는지 확인할 수 있습니다.

```azurepowershell-interactive
Get-ChildItem | Where-Object { $_.Name -eq "SampleDownload.txt" }
``` 

#### <a name="copy-files"></a>파일 복사
한 가지 일반적인 작업은 파일을 한 파일 공유에서 다른 파일 공유로 복사하는 것입니다. 이 기능을 보여주기 위해 [Start-AzStorageFileCopy](/powershell/module/az.storage/Start-AzStorageFileCopy) cmdlet을 사용하여 새 공유를 만들고, 이러한 새 공유에 업로드한 파일을 복사할 수 있습니다. 

```azurepowershell-interactive
$otherShareName = "myshare2"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $otherShareName `
    -EnabledProtocol SMB `
    -QuotaGiB 1024 | Out-Null
  
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $otherShareName `
   -Path "myDirectory2"

Start-AzStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName $shareName `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName $otherShareName `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

이제 새 공유에서 파일을 나열하는 경우 복사된 파일이 표시됩니다.

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $otherShareName `
    -Path "myDirectory2" 
```

`Start-AzStorageFileCopy` cmdlet은 Azure 파일 공유 간의 임시 파일 이동, 마이그레이션 및 더 큰 데이터 이동에 편리하지만, Windows의 경우 `robocopy`, macOS 및 Linux의 경우 `rsync`를 사용하는 것이 좋습니다. `robocopy` 및 `rsync`는 FileREST API 대신 SMB를 사용하여 데이터 이동을 수행합니다.

## <a name="create-and-manage-share-snapshots"></a>공유 스냅샷 만들기 및 관리
Azure 파일 공유로 수행할 수 있는 유용한 한 가지 추가 작업은 공유 스냅샷을 만드는 것입니다. 스냅샷은 Azure 파일 공유의 지정 시간을 유지합니다. 공유 스냅샷은 이미 익숙한 다음과 같은 운영 시스템 기술과 유사합니다.

- NTFS 및 ReFS와 같은 Windows 파일 시스템용 [VSS(볼륨 섀도 복사본 서비스)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal)
- Linux 시스템용 [LVM(논리 볼륨 관리자)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) 스냅샷
- macOS용 [APFS(Apple 파일 시스템)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) 스냅샷. 

파일 공유에 대한 PowerShell 개체에서 `Snapshot` 메서드를 사용하여 공유에 대한 공유 스냅샷을 만들 수 있습니다. 이 항목은 [Get-AzStorageShare](/powershell/module/az.storage/Get-AzStorageShare) cmdlet을 사용하여 검색됩니다. 

```azurepowershell-interactive
$share = Get-AzStorageShare -Context $storageAcct.Context -Name $shareName
$snapshot = $share.CloudFileShare.Snapshot()
```

### <a name="browse-share-snapshots"></a>공유 스냅샷 찾아보기
`Get-AzStorageFile` cmdlet의 `-Share` 매개 변수에 스냅샷 참조(`$snapshot`)를 전달하여 공유 스냅샷의 콘텐츠를 찾아볼 수 있습니다.

```azurepowershell-interactive
Get-AzStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>공유 스냅샷 나열
다음 명령을 사용하여 공유에 만든 스냅샷의 목록을 볼 수 있습니다.

```azurepowershell-interactive
Get-AzStorageShare `
        -Context $storageAcct.Context | `
    Where-Object { $_.Name -eq $shareName -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>공유 스냅샷에서 복원
이전에 사용한 `Start-AzStorageFileCopy` 명령을 사용하여 파일을 복원할 수 있습니다. 이 빠른 시작의 목적상 스냅샷에서 복원할 수 있도록 먼저 이전에 업로드한 `SampleUpload.txt` 파일을 삭제합니다.

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt"

# Restore SampleUpload.txt from the share snapshot
Start-AzStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName $shareName `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>공유 스냅샷 삭제
`-Share` 매개 변수에 대한 `$snapshot` 참조를 포함하는 변수와 함께 [Remove-AzStorageShare](/powershell/module/az.storage/Remove-AzStorageShare) cmdlet을 사용하여 공유 스냅샷을 삭제할 수 있습니다.

```azurepowershell-interactive
Remove-AzStorageShare `
    -Share $snapshot `
    -Confirm:$false `
    -Force
```

## <a name="clean-up-resources"></a>리소스 정리
작업을 완료하면 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet을 사용하여 리소스 그룹 및 모든 관련 리소스를 제거할 수 있습니다. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

또는 리소스를 하나씩 제거할 수 있습니다.

- 이 빠른 시작에서 만든 Azure 파일 공유를 제거하려면:

    ```azurepowershell-interactive
    Get-AzRmStorageShare -StorageAccount $storageAcct | Remove-AzRmStorageShare -Force
    ```

    > [!Note]  
    > Azure 파일 공유를 삭제하려면 먼저 만든 Azure 파일 공유에 대한 모든 공유 스냅샷을 삭제해야 합니다.

- 스토리지 계정 자체를 제거하려면: (그러면 암시적으로 만든 Azure 파일 공유뿐만 아니라 Azure Blob Storage 컨테이너와 같이 만든 다른 스토리지 리소스도 제거합니다.)

    ```azurepowershell-interactive
    Remove-AzStorageAccount `
        -ResourceGroupName $storageAcct.ResourceGroupName `
        -Name $storageAcct.StorageAccountName
    ```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Azure Files란?](storage-files-introduction.md)