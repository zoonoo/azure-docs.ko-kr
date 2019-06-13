---
title: Azure PowerShell을 사용하여 Azure 파일 공유를 관리하기 위한 빠른 시작
description: 이 빠른 시작을 사용하여 Azure PowerShell을 사용하여 Azure 파일 공유를 관리하는 방법을 알아봅니다.
services: storage
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e32aead791fb84415da1b00f1e979a6ac0f28155
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729032"
---
# <a name="quickstart-create-and-manage-an-azure-file-share-with-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 Azure 파일 공유 만들기 및 관리 
이 가이드에서는 PowerShell을 사용하여 [Azure 파일 공유](storage-files-introduction.md)로 작업하는 기본 사항을 설명합니다. Azure 파일 공유는 다른 파일 공유와 유사하지만, 클라우드에 저장되고 Azure Platform에서 지원합니다. Azure 파일 공유는 산업 표준 SMB 프로토콜을 지원하며 여러 머신, 애플리케이션 및 인스턴스 전반에서 파일 공유를 활성화합니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하려는 경우 이 자습서에서는 Azure PowerShell 모듈 Az 버전 0.7 이상이 필요합니다. 실행 중인 Azure PowerShell 모듈의 버전을 알아보려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. PowerShell을 로컬로 실행하는 경우 `Login-AzAccount`를 실행하여 Azure 계정에 로그인해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기
리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. Azure 리소스 그룹이 아직 없는 경우 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet을 사용하여 새 리소스 그룹을 만들 수 있습니다. 

다음 예제에서는 미국 동부 지역에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup `
    -Name myResourceGroup `
    -Location EastUS
```

## <a name="create-a-storage-account"></a>저장소 계정 만들기
저장소 계정은 Azure 파일 공유 또는 Blob나 큐와 같은 다른 저장소 리소스를 배포하는 데 사용할 수 있는 저장소의 공유 풀입니다. 저장소 계정에 포함할 수 있는 공유 수에는 제한이 없으며, 공유에 저장할 수 있는 파일 수에는 저장소 계정의 최대 용량 한도까지 제한이 없습니다.

이 예제에서는 [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) cmdlet을 사용하여 스토리지 계정을 만듭니다. 스토리지 계정의 이름은 *mystorageaccount\<임의 번호>* 이며, 해당 스토리지 계정에 대한 참조는 **$storageAcct** 변수에 저장됩니다. 저장소 계정 이름은 고유해야 합니다. 따라서 `Get-Random`를 사용하여 고유하게 만들 이름에 숫자를 추가합니다. 

```azurepowershell-interactive 
$storageAcct = New-AzStorageAccount `
                  -ResourceGroupName "myResourceGroup" `
                  -Name "mystorageacct$(Get-Random)" `
                  -Location eastus `
                  -SkuName Standard_LRS 
```

## <a name="create-an-azure-file-share"></a>Azure 파일 공유 만들기
이제 첫 번째 Azure 파일 공유를 만들 수 있습니다. [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) cmdlet을 사용하여 파일 공유를 만들 수 있습니다. 이 예제에서는 `myshare`라는 공유를 만듭니다.

```azurepowershell-interactive
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

공유 이름은 모두 소문자, 숫자 및 단일 하이픈이어야 하지만 하이픈으로 시작할 수 없습니다. 파일 공유 및 파일 이름 지정에 대한 자세한 내용은 [공유, 디렉터리, 파일 및 메타데이터 이름 지정 및 참조](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)를 참조하세요.

## <a name="use-your-azure-file-share"></a>Azure 파일 공유 사용
Azure Files는 Azure 파일 공유 내에서 파일 및 폴더를 작업하는 두 가지 방법(업계 표준 [SMB(서버 메시지 블록) 프로토콜](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) 및 [File REST 프로토콜](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api))을 제공합니다. 

SMB를 사용하여 파일 공유를 탑재하려면 OS에 따라 다음 문서를 참조하세요.
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>File REST 프로토콜로 Azure 파일 공유 사용 
File REST 프로토콜을 직접 작업하는 것이 가능하지만(즉, REST HTTP 호출 직접 생성), File REST 프로토콜을 사용하는 가장 일반적인 방법은 Azure PowerShell 모듈, [Azure CLI](storage-how-to-use-files-cli.md) 또는 Azure Storage SDK를 사용하는 것입니다. 이들 모두 선택한 스크립팅/프로그래밍 언어에서 File REST 프로토콜에 좋은 래퍼를 제공합니다.  

대부분의 경우 Azure 파일 공유에서는 기존 애플리케이션과 도구를 사용할 수 있기 때문에 SMB 프로토콜이 아닌 Azure 파일 공유를 선택하겠지만, SMB보다 파일 REST API를 사용하는 것이 더 나은 이유가 다음과 같이 몇 가지 있습니다.

- PowerShell Cloud Shell에서 파일 공유를 탐색하고 있는 경우(SMB를 통해 파일 공유를 탑재할 수 없음)
- 포트 445가 잠금 해제되어 있지 않은 온-프레미스 클라이언트 등, SMB 공유를 탑재할 수 없는 클라이언트에서 스크립트 또는 애플리케이션을 실행해야 하는 경우
- 서버리스 리소스([Azure Functions](../../azure-functions/functions-overview.md) 등)를 활용하고 있는 경우 

다음 예제에서는 Azure PowerShell 모듈을 사용하여 File REST 프로토콜로 Azure 파일 공유를 조작하는 방법을 보여줍니다. 

#### <a name="create-directory"></a>디렉터리 만들기
Azure 파일 공유의 루트에 *myDirectory*라는 새 디렉터리를 만들려면 [New-AzStorageDirectory](/powershell/module/az.storage/New-AzStorageDirectory) cmdlet을 사용합니다.

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Path "myDirectory"
```

#### <a name="upload-a-file"></a>파일 업로드
[Set-AzStorageFileContent](/powershell/module/az.storage/Set-AzStorageFileContent) cmdlet을 사용하여 파일을 업로드하는 방법을 설명하려면 먼저 업로드할 PowerShell Cloud Shell의 임시 드라이브 내에 파일을 만들어야 합니다. 

이 예제에서는 임시 드라이브의 새 파일에 현재 날짜 및 시간을 저장한 다음, 파일 공유에 파일을 업로드합니다.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
Get-Date | Out-File -FilePath "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Source "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

PowerShell을 로컬로 실행 중인 경우 `C:\Users\ContainerAdministrator\CloudDrive\`를 컴퓨터에 있는 경로로 대체해야 합니다.

파일을 업로드한 후에 [Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile) cmdlet을 사용하여 파일이 Azure 파일 공유에 업로드되었는지 확인할 수 있습니다. 

```azurepowershell-interactive
Get-AzStorageFile -Context $storageAcct.Context -ShareName "myshare" -Path "myDirectory" 
```

#### <a name="download-a-file"></a>파일 다운로드
[Get-AzStorageFileContent](/powershell/module/az.storage/Get-AzStorageFilecontent) cmdlet을 사용하여 Cloud Shell의 임시 드라이브에 업로드된 파일의 복사본을 다운로드할 수 있습니다.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item `
     -Path "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt" `
     -Force `
     -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt" ` 
    -Destination "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt"
```

파일에 다운로드한 후에 `Get-ChildItem`을 사용하여 PowerShell Cloud Shell 임시 드라이브에 파일을 다운로드했는지 확인할 수 있습니다.

```azurepowershell-interactive
Get-ChildItem -Path "C:\Users\ContainerAdministrator\CloudDrive"
``` 

#### <a name="copy-files"></a>파일 복사
일반적인 작업은 하나의 파일 공유에서 다른 파일 공유로 파일을 복사하거나 Azure Blob Storage 컨테이너 간에 파일을 복사하는 것입니다. 이 기능을 보여주기 위해 [Start-AzStorageFileCopy](/powershell/module/az.storage/Start-AzStorageFileCopy) cmdlet을 사용하여 새 공유를 만들고, 이러한 새 공유에 업로드한 파일을 복사할 수 있습니다. 

```azurepowershell-interactive
New-AzStorageShare `
    -Name "myshare2" `
    -Context $storageAcct.Context
  
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare2" `
   -Path "myDirectory2"

Start-AzStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName "myshare" `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName "myshare2" `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

이제 새 공유에서 파일을 나열하는 경우 복사된 파일이 표시됩니다.

```azurepowershell-interactive
Get-AzStorageFile -Context $storageAcct.Context -ShareName "myshare2" -Path "myDirectory2" 
```

`Start-AzStorageFileCopy` cmdlet은 Azure 파일 공유와 Azure Blob Storage 컨테이너 간에 임시 파일 이동에 편리하지만(이동되는 파일의 수 또는 크기 측면에서) 대규모 이동에 AzCopy를 사용하는 것이 좋습니다. [Windows용 AzCopy](../common/storage-use-azcopy.md) 및 [Linux용 AzCopy](../common/storage-use-azcopy-linux.md)에 대해 자세히 알아봅니다. AzCopy를 로컬로 설치해야 합니다. Cloud Shell에서 사용할 수 없습니다. 

## <a name="create-and-manage-share-snapshots"></a>공유 스냅샷 만들기 및 관리
Azure 파일 공유로 수행할 수 있는 유용한 한 가지 추가 작업은 공유 스냅샷을 만드는 것입니다. 스냅샷은 Azure 파일 공유의 지정 시간을 유지합니다. 공유 스냅샷은 이미 익숙한 다음과 같은 운영 시스템 기술과 유사합니다.
- NTFS 및 ReFS와 같은 Windows 파일 시스템용 [VSS(볼륨 섀도 복사본 서비스)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal)
- Linux 시스템용 [LVM(논리 볼륨 관리자)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) 스냅샷
- macOS용 [APFS(Apple 파일 시스템)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) 스냅샷. 
 파일 공유에 대한 PowerShell 개체에서 `Snapshot` 메서드를 사용하여 공유에 대한 공유 스냅샷을 만들 수 있습니다. 이 항목은 [Get-AzStorageShare](/powershell/module/az.storage/Get-AzStorageShare) cmdlet을 사용하여 검색됩니다. 

```azurepowershell-interactive
$share = Get-AzStorageShare -Context $storageAcct.Context -Name "myshare"
$snapshot = $share.Snapshot()
```

### <a name="browse-share-snapshots"></a>공유 스냅샷 찾아보기
`Get-AzStorageFile` cmdlet의 `-Share` 매개 변수에 스냅샷 참조(`$snapshot`)를 전달하여 공유 스냅샷의 콘텐츠를 찾아볼 수 있습니다.

```azurepowershell-interactive
Get-AzStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>공유 스냅샷 나열
다음 명령을 사용하여 공유에 만든 스냅샷의 목록을 볼 수 있습니다.

```azurepowershell-interactive
Get-AzStorageShare -Context $storageAcct.Context | Where-Object { $_.Name -eq "myshare" -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>공유 스냅샷에서 복원
이전에 사용한 `Start-AzStorageFileCopy` 명령을 사용하여 파일을 복원할 수 있습니다. 이 빠른 시작의 목적상 스냅샷에서 복원할 수 있도록 먼저 이전에 업로드한 `SampleUpload.txt` 파일을 삭제합니다.

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt"
 # Restore SampleUpload.txt from the share snapshot
Start-AzStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName "myshare" `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>공유 스냅샷 삭제
`-Share` 매개 변수에 대한 `$snapshot` 참조를 포함하는 변수와 함께 [Remove-AzStorageShare](/powershell/module/az.storage/Remove-AzStorageShare) cmdlet을 사용하여 공유 스냅샷을 삭제할 수 있습니다.

```azurepowershell-interactive
Remove-AzStorageShare -Share $snapshot
```

## <a name="clean-up-resources"></a>리소스 정리
작업을 완료하면 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet을 사용하여 리소스 그룹 및 모든 관련 리소스를 제거할 수 있습니다. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

또는 리소스를 하나씩 제거할 수 있습니다.

- 이 빠른 시작에서 만든 Azure 파일 공유를 제거하려면:

    ```azurepowershell-interactive
    Get-AzStorageShare -Context $storageAcct.Context | Where-Object { $_.IsSnapshot -eq $false } | ForEach-Object { 
        Remove-AzStorageShare -Context $storageAcct.Context -Name $_.Name
    }
    ```

- 스토리지 계정 자체를 제거하려면: (그러면 암시적으로 만든 Azure 파일 공유뿐만 아니라 Azure Blob Storage 컨테이너와 같이 만든 다른 스토리지 리소스도 제거합니다.)

    ```azurepowershell-interactive
    Remove-AzStorageAccount -ResourceGroupName $storageAcct.ResourceGroupName -Name $storageAcct.StorageAccountName
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Files란?](storage-files-introduction.md)
