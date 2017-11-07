---
title: "PowerShell을 사용하여 Azure Blob Storage(개체 저장소)에서 작업 수행 | Microsoft Docs"
description: "자습서 - PowerShell을 사용하여 Azure Blob Storage(개체 저장소)에서 작업 수행"
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: robinsh
ms.openlocfilehash: 565bcba848de1c518b25ff4c55a9a47aaa45bfb4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="perform-azure-blob-storage-operations-with-azure-powershell"></a>Azure PowerShell을 사용하여 Azure Blob Storage 작업 수행

Azure Blob 저장소는 HTTP 또는 HTTPS를 통해 전 세계 어디에서든 액세스할 수 있는 다량의 구조화되지 않은 개체 데이터(예: 텍스트 또는 이진 데이터)를 저장할 수 있는 서비스입니다. 이 문서에서는 Blob 업로드, 다운로드 및 삭제와 같은 Azure Blob Storage의 기본 작업에 대해 설명합니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 컨테이너 만들기 
> * Blob 업로드
> * 컨테이너의 Blob 나열 
> * Blob 다운로드
> * Blob 복사
> * Blob 삭제
> * Blob 메타데이터와 속성 보기 및 설정
> * 공유 액세스 서명을 사용하여 보안 관리

이 자습서에는 Azure PowerShell 모듈 버전 3.6 이상이 필요합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요.

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>컨테이너 만들기

Blob은 항상 컨테이너에 업로드됩니다. 컨테이너는 컴퓨터의 디렉터리와 유사하므로 컴퓨터의 폴더에 파일을 정리하는 것처럼 컨테이너에 있는 Blob 그룹을 구성할 수 있습니다. 저장소 계정은 여러 개의 컨테이너를 포함할 수 있으며 저장소 계정에서 차지하는 공간(최대 500TB)에 의해서만 제한됩니다. 

컨테이너를 만들 때 해당 컨테이너의 Blob에 액세스할 수 있는 사용자를 정의하는 데 도움이 되는 액세스 수준을 설정할 수 있습니다. 예를 들어 개인(액세스 수준 = `Off`)일 수 있습니다. 즉, 공유 액세스 서명이나 저장소 계정의 액세스 키 없이는 아무도 액세스할 수 없습니다. 컨테이너를 만들 때 액세스 수준을 지정하지 않으면 기본적으로 개인입니다.

컨테이너의 이미지를 공개적으로 액세스할 수 있도록 하려고 할 수 있습니다. 예를 들어 웹 사이트에 표시할 이미지를 저장하려면 해당 이미지를 공개해야 합니다. 이 경우 컨테이너 액세스 수준을 `blob`으로 설정하고 해당 컨테이너의 Blob을 가리키는 URL을 통해 모든 사람이 Blob에 액세스할 수 있습니다.

컨테이너를 만들려면 컨테이너 이름을 설정한 후 컨테이너를 만들고 사용 권한을 Blob'으로 설정합니다. 컨테이너 이름은 문자 또는 숫자로 시작해야 하며 문자, 숫자 및 하이픈 문자(-)만 포함할 수 있습니다. Blob 및 컨테이너의 이름을 지정하는 방법에 대한 자세한 규칙은 [컨테이너, Blob, 메타데이터 이름 명명 및 참조](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)를 참조하세요.

[New-AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer)를 사용하여 새 컨테이너를 만듭니다. 액세스 수준을 공용으로 설정합니다. 이 예제의 컨테이너 이름은 *howtoblobs*입니다.

```powershell
$containerName = "howtoblobs"
New-AzureStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-into-a-container"></a>컨테이너에 Blob 업로드

Azure Blob Storage는 블록 Blob, 추가 Blob 및 페이지 Blob을 지원합니다.  IaaS VM을 백업하는 데 사용된 VHD 파일은 페이지 Blob입니다. 추가 Blob은 파일에 쓰고 더 많은 정보를 계속해서 추가하려는 경우처럼 로깅에 사용됩니다. Blob Storage에 저장된 대부분의 파일은 블록 Blob입니다. 

블록 Blob에 파일을 업로드하려면 컨테이너 참조를 가져온 다음 해당 컨테이너의 블록 Blob에 대한 참조를 가져옵니다. Blob 참조가 있으면 [Set-AzureStorageBlobContent](/powershell/module/azure.storage/set-azurestorageblobcontent)를 사용하여 데이터를 업로드할 수 있습니다. 이 작업은 Blob이 없는 경우 만들고, Blob이 이미 있는 경우 덮어씁니다.

다음은 컨테이너에 Blob을 업로드하는 방법을 보여 줍니다. 먼저, 파일이 있는 로컬 컴퓨터의 디렉터리를 가리키는 변수를 설정하고 업로드할 파일의 이름에 대한 변수를 설정합니다. 이 방법은 동일한 작업을 반복해서 수행하려는 경우 유용합니다. 컨테이너에 Blob을 나열할 때 여러 항목을 볼 수 있도록 두서너 개의 파일을 업로드합니다.

다음 예제에서는 로컬 디스크의 D:\\_TestImages에서 방금 만든 컨테이너로 Image001.jpg 및 Image002.png를 로드합니다.

```powershell
$localFileDirectory = "D:\_TestImages\"

$blobName = "Image001.jpg"
$localFile = $localFileDirectory + $blobName
Set-AzureStorageBlobContent -File $localFile `
  -Container $containerName `
  -Blob $blobName `
  -Context $ctx 
```

다른 파일을 업로드합니다. 

```powershell
$blobName = "Image002.png"
$localFile = $localFileDirectory + $blobName 
Set-AzureStorageBlobContent -File $localFile `
  -Container $containerName `
  -Blob $blobName `
  -Context $ctx
```

원하는 만큼 파일을 업로드한 후 계속합니다.

## <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열

[Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob)을 사용하고 표시할 Blob 이름을 선택하여 컨테이너의 Blob 목록을 가져옵니다.

```powershell
Get-AzureStorageBlob -Container $ContainerName -Context $ctx | select Name 
```

## <a name="download-blobs"></a>Blob 다운로드

로컬 디스크로 Blob을 다운로드합니다. 먼저, Blob을 다운로드할 로컬 폴더를 가리키는 변수를 설정합니다. 그런 다음 다운로드할 각 Blob에 대해 이름을 설정하고 [Get-AzureStorageBlobContent](/powershell/module/azure.storage/get-azurestorageblobcontent)를 호출하여 Blob을 다운로드합니다.

이 예에서는 로컬 디스크의 D:\\_TestImages\Downloads에 Blob을 복사합니다. 

```powershell
# local directory to which to download the files
# example "D:\_TestImages\Downloads\"
$localTargetDirectory = "D:\_TestImages\Downloads\"

# download the first blob
$blobName = "Image001.jpg"
Get-AzureStorageBlobContent -Blob $blobName `
  -Container $containerName `
  -Destination $localTargetDirectory `
  -Context $ctx 

# download another blob
$blobName = "Image002.png"
Get-AzureStorageBlobContent -Blob $blobName `
  -Container $containerName `
  -Destination $localTargetDirectory `
  -Context $ctx 
```

## <a name="copy-blobs"></a>Blob 복사

Blob을 복사하는 경우에는 여러 가지를 고려해야 합니다. 동일한 위치에 새 이름으로 Blob을 복사하는 것일 수도 있습니다. Blob을 별도 저장소 계정에 복사하는 것일 수 있습니다. 다른 저장소 계정에 큰 Blob(예: VHD 파일)을 복사하는 것일 수 있습니다. 이들 작업은 각각 다르게 수행됩니다.

### <a name="simple-blob-copy"></a>간단한 Blob 복사

새 Blob에 복사하여 컨테이너에서 Blob 중 하나의 복사본을 만들 수 있습니다. 이 예제에서는 동일한 컨테이너에 다른 이름으로 복사하지만 다른 컨테이너를 쉽게 만들고 대신 복사할 수 있습니다. 이 예제에서는 [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy)를 사용하여 Blob을 복사하는 방법을 보여 줍니다. 원본 및 대상 Blob 이름과 컨테이너 이름을 모두 지정해야 합니다.

```powershell
$blobName = "Image002.png"
$newBlobName = "CopyOf_" + $blobName 

Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName `
  -DestBlob $newBlobName `
  -Context $ctx 

# get list of blobs to verify the new one has been created
Get-AzureStorageBlob -Container $containerName -Context $ctx | select Name 
```

### <a name="copy-a-blob-to-a-different-storage-account"></a>다른 저장소 계정에 Blob 복사 

별도 저장소 계정에 Blob을 복사하려고 할 수 있습니다. 이 작업을 수행하기 위한 한 가지 예는 백업을 위해 VM 중 하나를 다른 저장소 계정에 백업하는 VHD 파일을 복사하는 것입니다. 

두 번째 저장소 계정을 설정하고 컨텍스트를 검색하고 해당 저장소 계정에서 컨테이너를 설정하며 복사를 수행합니다. 스크립트의 이 부분은 첫 번째 대신 두 번째 저장소 계정을 사용하는 것을 제외하고는 위의 스크립트와 거의 동일합니다.

```powershell
#create new storage acount, get context 
$storageAccount2Name = "blobstutorialtestcopy"
$storageAccount2 = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccount2Name `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob

$ctx2 = $storageAccount2.Context

#create a container in the second storage account 
$containerName2 = "tutorialblobscopied"
New-AzureStorageContainer -Name $containerName2 `
  -Context $ctx2 `
  -Permission blob

# copy one of the blobs from the first storage account to the second one 
# specify the source and destination container, blob name, and context
Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName2 `
  -DestBlob $blobName `
  -SrcContext $ctx `
  -DestContext $ctx2 

# list the blobs in the container in the second storage account
# to verify that the newly copied blob is there
Get-AzureStorageBlob -Container $containerName2 -Context $ctx2 | select Name 
```

### <a name="copying-very-large-blobs-asynchronously"></a>매우 큰 Blob을 비동기로 복사

여러 GB인 매우 큰 Blob을 복사하는 경우 시작한 다음 다시 돌아와 완료될 때까지 상태를 확인하여 비동기식 복사를 활용할 수 있습니다. 이렇게 하면 복사 작업을 수행하는 동안 행동이 제한되지 않습니다.

저장소 계정 내에서 복사하는 경우 매우 빠르게 복사됩니다. 동일한 지역의 두 저장소 계정 간에 복사하는 경우 매우 빠르게 복사됩니다. 그러나 원본 및 대상이 별도의 지역에 있는 경우 거리 및 파일 크기에 따라 복사를 완료하는 데 몇 시간이 걸릴 수 있습니다. 

이 스크립트는 마지막 복사 예제에 정의된 동일한 변수를 사용합니다. 차이점은 복사본의 상태를 캡처하고 완료될 때까지 10초마다 반복적으로 쿼리한다는 것입니다. 완료하는 데 두 번 이상 반복되는 것을 보려면 저장소 계정에 매우 큰 Blob을 업로드해야 할 수 있습니다.

[Get-AzureStorageBlobCopyState](/powershell/module/azure.storage/get-azurestorageblobcopystate)를 사용하여 복사 상태를 쿼리합니다. 

```powershell
# start the blob copy, and assign the result to $blobResult
$blobResult = Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName2 `
  -DestBlob $blobName `
  -Context $ctx `
  -DestContext $ctx2

# get the status of the blob copy
$status = $blobResult | Get-AzureStorageBlobCopyState 
# show the value of the status
$status 

# loop until it finishes copying, pausing for 10 seconds after each iteration
# it is finished when the status is no longer 'Pending'
while ($status.Status -eq "Pending") {
    $status = $blobResult | Get-AzureStorageBlobCopyState 
    $status
    Start-Sleep 10
}

# get the list of blobs to see the new file in the second storage account 
Get-AzureStorageBlob -Container $containerName2 -Context $ctx2 | select Name 
```

## <a name="delete-blobs"></a>Blob 삭제

저장소 계정에서 Blob을 제거하려면 [Remove-AzureStorageBlob](/powershell/module/azure.storage/Remove-AzureStorageBlob)을 사용합니다. 

```powershell
$blobName = "Image001.jpg"
Remove-AzureStorageBlob -Blob $blobName -Container $containerName -Context $ctx

# get list of blobs to see the one you deleted is gone
Get-AzureStorageBlob -Container $containerName -Context $ctx | select Name 
```

## <a name="read-and-write-a-blobs-properties-and-metadata"></a>Blob 속성과 메타데이터 읽기 및 쓰기

반환된 **IListBlobItem**에 대한 속성 및 메서드 집합에 액세스하려면 **CloudBlockBlob**, **CloudPageBlob** 또는 **CloudBlobDirectory** 개체로 캐스트(또는 변환)해야 합니다. 형식을 알 수 없는 경우 형식 검사를 사용하여 캐스트할 형식을 확인할 수 있습니다. 다음 코드에서는 [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob)을 사용하고 결과를 CloudBlockBlob 개체로 캐스트하여 이전에 업로드한 Blob 중 하나의 속성을 검색 및 출력하는 방법을 보여 줍니다.

```powershell
# blob properties
# get a reference to the blob you uploaded -- this is an **IListBlobItem** -- then
# convert it to a CloudBlockBlob, giving you access to the properties 
# and methods of the blob 
$blobName = "Image001.jpg"
$blob = Get-AzureStorageBlob -Context $ctx `
   -Container $containerName `
   -Blob $blobName 

#convert $blob to a CloudBlockBlob object
$cloudBlockBlob = [Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob] $blob.ICloudBlob

# you can view the properties by typing in $cloudBlockBlob 
#   and hitting the period key; this brings up IntelliSense,
#   which shows you all of the available properties
Write-Host "blob type = " $cloudBlockBlob.BlobType
Write-Host "blob name = " $cloudBlockBlob.Name
Write-Host "blob uri = " $cloudBLockBlob.Uri
```

FetchAttributes를 호출하여 시스템 속성을 채운 후 해당 속성을 봅니다. 일부 속성은 쓰기 가능하고 해당 값을 변경할 수 있습니다. 이 예제에는 MIME 형식이라고도 하는 콘텐츠 형식을 변경하는 방법을 보여 줍니다.

```powershell
# populate the system properties
$cloudBlockBlob.FetchAttributes()

# view some of the system properties
# contentType is commonly referred to as MIME type
Write-Host "content type = " $cloudBlockBlob.Properties.ContentType
Write-Host "size = " $cloudBlockBlob.Properties.Length 

# change the content type to image/png
$contentType = "image/jpg"
$cloudBlockBlob.Properties.ContentType = $contentType 
$cloudBlockBlob.SetProperties() 

# get the system properties again to show that the content type has changed
$cloudBlockBlob.FetchAttributes()
Write-Host "content type = " $cloudBlockBlob.Properties.ContentType
```

각 Blob에는 설정할 수 있는 고유한 메타데이터가 있습니다. 예를 들어, Blob을 업로드한 사용자 이름 또는 처음으로 업로드했던 날짜/시간을 저장할 수 있습니다. 메타데이터는 키/값 쌍으로 이루어져 있습니다. 다음과 같이 PowerShell을 사용하여 수정할 수 있습니다.

```powershell
# "filename" is the key, "original file name" is the value
$cloudBlockBlob.Metadata["filename"] = "original file name"

# "owner" is the key, "RobinS" is the value
$cloudBlockBlob.Metadata["owner"] = "RobinS"

# save the metadata and then display it
$cloudBlockBlob.SetMetadata()
$cloudBlockBlob.Metadata

# clear the metadata, save it, and show it
$cloudBlockBlob.Metadata.Clear()
$cloudBlockBlob.SetMetadata()
$cloudBlockBlob.Metadata
```

## <a name="managing-security-for-blobs"></a>Blob 보안 관리 

기본적으로 Azure Storage는 액세스를 저장소 계정 액세스 키를 보유한 계정 소유자로 제한하여 데이터를 보호합니다. 저장소 계정의 Blob 데이터를 공유해야 할 경우 계정 액세스 키의 보안을 손상시키지 않고 공유하는 것이 중요합니다. 이를 위해, 특정 시간 동안 특정 수준의 사용 권한을 허용하는 보안 토큰 및 쿼리 매개 변수가 들어있는 자산의 URL인 공유 액세스 서명 URL을 사용할 수 있습니다. 예를 들어 개인 Blob에 대한 읽기 권한을 5분 동안 부여하여 다른 사람이 볼 수 있도록 할 수 있습니다. 

### <a name="set-the-access-level-of-the-container-and-its-blobs-to-private"></a>컨테이너와 해당 Blob의 액세스 수준을 개인으로 설정

먼저 컨테이너의 액세스 수준을 `Off`로 설정합니다. 이 수준은 공유 액세스 서명 또는 계정 키 없이는 액세스할 수 없음을 나타냅니다. [Set-AzureStorageContainerAcl](/powershell/module/azure.storage/Set-AzureStorageContainerAcl)을 사용합니다.

```powershell
Set-AzureStorageContainerAcl -Name $containerName -Context $ctx -Permission Off 
```

### <a name="test-private-access"></a>개인 액세스 테스트

해당 컨테이너의 Blob에 액세스할 수 없는지를 확인하려면 공유 액세스 서명이 없는 Blob 중 하나에 대한 URL을 생성하고 Blob을 보려고 시도합니다. HTTPS 프로토콜을 사용하는 URL의 형식은 다음과 같습니다.

    `https://storageaccountname.blob.core.windows.net/containername/blobname`

Blob URL을 만드는 방법을 보여 줍니다.

```powershell
$blobUrl = "https://" `
  $storageAccountName ".blob.core.windows.net/" + `
  $containerName +  "/" $blobName

Write-Host "Blob URL = " $blobUrl 
```

Blob URL을 복사하여 개인 브라우저 창에 붙여넣습니다. Blob이 개인이고 공유 액세스 서명을 포함하지 않으므로 권한 부여 오류가 표시됩니다. 

### <a name="create-the-sas-uri"></a>SAS URI 만들기

SAS URI를 만듭니다. SAS를 구성하는 보안 토큰 및 쿼리 매개 변수를 포함하는 Blob에 대한 링크입니다. 이 URI를 개인 검색 창에 붙여넣으면 이미지가 표시됩니다. 

먼저 액세스를 위한 시작 날짜/시간과 만료 날짜/시간을 만듭니다. 여기서는 2분 기간을 사용합니다. 

```powershell
# set the start time to the current date/time 
# set the end time to 2 minutes in the future
$StartTime = Get-Date
$EndTime = $StartTime.AddMinutes(2.0)
```

[New-AzureStorageBlobSASToken](/powershell/module/azure.storage/new-azurestorageblobsastoken)을 사용하여 SAS URI를 만듭니다. 컨테이너 이름, Blob 이름, 저장소 계정 컨텍스트, 부여할 사용 권한(이 경우 읽기, 쓰기 및 삭제), 액세스 시작 및 종료 시간이 필요합니다. 

```powershell
$SASURI = New-AzureStorageBlobSASToken -Container $containerName `
    -Blob $blobName `
    -Context $ctx `
    -Permission "rwd" `
    -StartTime $StartTime `
    -ExpiryTime $EndTime `
    -FullUri

Write-Host "URL with SAS = " $SASURI
```

SAS URI를 복사하여 개인 브라우저 창에 붙여넣으면 이미지가 표시됩니다.

URL이 만료될 때까지 충분히 기다린 후(이 예제의 경우 2분) URL을 다른 개인 브라우저 창에 붙여넣습니다. 이번에는 권한 부여 오류가 발생하며 SAS URI가 만료되었기 때문에 그림이 표시되지 않습니다.

## <a name="clean-up-resources"></a>리소스 정리

만든 자산을 모두 제거합니다. 이를 위해 리소스 그룹을 제거할 수 있습니다. 그러면 그룹 내에 포함된 모든 리소스도 삭제됩니다. 이 경우 저장소 계정 및 리소스 그룹 자체가 모두 제거됩니다.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법과 같이 기본 Blob Storage 관리에 대해 알아보았습니다.

> [!div class="checklist"]
> * 컨테이너 만들기 
> * Blob 업로드
> * 컨테이너의 Blob 나열 
> * Blob 다운로드
> * Blob 복사
> * Blob 삭제
> * Blob 메타데이터와 속성 읽기 및 쓰기
> * 공유 액세스 서명을 사용하여 보안 관리

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Microsoft Azure PowerShell Storage cmdlet
* [Storage PowerShell cmdlet](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure 저장소 탐색기
* [Microsoft Azure Storage 탐색기](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)는 Windows, MacOS 및 Linux에서 Azure Storage 데이터로 시각적으로 작업할 수 있도록 해주는 Microsoft의 독립 실행형 무료 앱입니다.