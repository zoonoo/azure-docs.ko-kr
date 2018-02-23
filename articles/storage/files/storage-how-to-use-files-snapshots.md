---
title: "공유 스냅숏 작업(미리 보기) | Microsoft Docs"
description: "공유 스냅숏은 공유를 백업하는 방법으로 특정 시점에 생성된 Azure 파일 공유의 읽기 전용 버전입니다."
services: storage
documentationcenter: .net
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: renash
ms.openlocfilehash: eb5c7d0fcb9e1106dbc0aa577ba5ecfa1bf6bee7
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="work-with-share-snapshots-preview"></a>공유 스냅숏 작업(미리 보기)
공유 스냅숏은 특정 시점에 생성되는 Azure Files 공유의 읽기 전용 버전입니다. 공유 스냅숏이 생성된 후에는 읽거나 복사하거나 삭제할 수 있지만 수정할 수는 없습니다. 공유 스냅숏은 특정 시점에 표시된 대로 공유를 백업하는 방법을 제공합니다. 

이 문서에서는 공유 스냅숏을 만들고, 관리하고, 삭제하는 방법에 대해 알아봅니다. 자세한 내용은 [공유 스냅숏 개요](storage-snapshots-files.md) 또는 [스냅숏 FAQ](storage-files-faq.md)를 참조하세요.

## <a name="create-a-share-snapshot"></a>공유 스냅숏 만들기

Azure Portal, PowerShell, Azure CLI, REST API 또는 Azure Storage SDK를 사용하여 공유 스냅숏을 만들 수 있습니다. 다음 섹션에서는 포털, CLI 및 PowerShell을 사용하여 공유 스냅숏을 만드는 방법에 대해 설명합니다. 

파일 공유를 사용하는 동안 해당 공유 스냅숏을 생성할 수 있습니다. 그러나 공유 스냅숏은 공유 스냅숏 명령을 실행할 때 이미 파일 공유에 기록된 데이터만 캡처합니다. 즉, 응용 프로그램 또는 운영 체제에서 캐시한 데이터는 제외될 수 있습니다.

### <a name="create-a-share-snapshot-by-using-the-portal"></a>포털을 사용하여 공유 스냅숏 만들기  
특정 시점의 공유 스냅숏을 만들려면 포털에서 파일 공유로 이동하고 **스냅숏 만들기** 단추를 선택합니다.

>   ![포털의 스냅숏 메뉴](./media/storage-snapshots-create/portal-create-snapshot.png)


### <a name="create-a-share-snapshot-by-using-azure-cli-20"></a>Azure CLI 2.0을 사용하여 공유 스냅숏 만들기
`az storage share snapshot` 명령을 사용하면 공유 스냅숏을 만들 수 있습니다.

```azurecli-interactive
az storage share snapshot -n <share name>
```

예제 출력:
```json
{
  "metadata": {},
  "name": "<share name>",
  "properties": {
    "etag": "\"0x8D50B7F9A8D7F30\"",
    "lastModified": "2017-10-04T23:28:22+00:00",
    "quota": null
  },
  "snapshot": "2017-10-04T23:28:35.0000000Z"
}
```

### <a name="create-a-share-snapshot-by-using-powershell"></a>PowerShell을 사용하여 공유 스냅숏 만들기
`$share.Snapshot()` 명령을 사용하면 공유 스냅숏을 만들 수 있습니다.

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##create snapshot
$share=Get-AzureStorageShare -Context $ctx -Name <file share name>
$share.Properties.LastModified
$share.IsSnapshot
$snapshot=$share.Snapshot()

```

## <a name="perform-common-share-snapshot-operations"></a>일반적인 공유 스냅숏 작업 수행

REST, 클라이언트 라이브러리, PowerShell 및 포털을 통해 Windows에서 **이전 버전** 탭을 사용하여 파일 공유와 연결된 공유 스냅숏을 열거할 수 있습니다. 파일 공유가 탑재되면 Windows에서 **이전 버전** 탭을 사용하여 파일의 이전 버전을 모두 볼 수 있습니다. 

다음 섹션에서는 Azure Portal, Windows 및 Azure CLI 2.0을 사용하여 공유 스냅숏을 나열하고, 찾아보고, 복원하는 방법에 대해 설명합니다.

### <a name="share-snapshot-operations-in-the-portal"></a>포털에서 공유 스냅숏 작업

포털에서 파일 공유에 대한 모든 공유 스냅숏을 보고 해당 공유 스냅숏으로 이동하여 그 내용을 확인할 수 있습니다.

#### <a name="view-a-share-snapshot"></a>공유 스냅숏 보기
파일 공유의 **스냅숏** 아래에서 **스냅숏 보기**를 선택합니다.

![포털의 "스냅숏 보기" 명령](./media/storage-snapshots-list-browse/snapshot-view-portal.png)

#### <a name="list-and-browse-to-share-snapshot-content"></a>공유 스냅숏 내용 나열 및 찾아보기
공유 스냅숏 목록을 보고 원하는 타임스탬프를 선택하여 해당 스냅숏의 내용을 직접 찾아봅니다.

![타임스탬프의 목록에서 선택한 스냅숏](./media/storage-snapshots-list-browse/snapshot-browsefiles-portal.png)

또한 목록 스냅숏 보기에서 **연결** 단추를 선택하여 `net use` 명령 및 특정 공유 스냅숏에 대한 디렉터리 경로를 가져올 수도 있습니다. 그러면 해당 스냅숏을 직접 찾아볼 수 있습니다.


![명령 상자에서 창 연결](./media/storage-snapshots-list-browse/snapshot-connect-portal.png)

#### <a name="download-or-restore-from-a-share-snapshot"></a>공유 스냅숏에서 다운로드 또는 복원
포털에서 **다운로드** 또는 **복원** 단추를 각각 사용하여 스냅숏에서 파일을 다운로드하거나 복원합니다.

![다운로드 및 복원 단추](./media/storage-snapshots-list-browse/snapshot-download-restore-portal.png)

### <a name="share-snapshot-operations-in-windows"></a>Windows에서 공유 스냅숏 작업
이미 파일 공유의 공유 스냅숏을 만들었으면 Windows에 탑재된 파일 공유에서 이전 버전의 공유, 디렉터리 또는 특정 파일을 볼 수 있습니다. 예를 들어 [이전 버전] 기능을 사용하여 Windows에서 이전 버전의 디렉터리를 보고 복원할 수 있는 방법은 다음과 같습니다.

> [!Note]  
> 공유 수준과 파일 수준에서 동일한 작업을 수행할 수 있습니다. 해당 디렉터리 또는 파일에 대한 변경 내용이 포함된 버전만 목록에 표시됩니다. 두 공유 스냅숏 간에 특정 디렉터리 또는 파일이 변경되지 않은 경우, 공유 스냅숏은 공유 수준 이전 버전 목록에만 표시되고 디렉터리 또는 파일의 이전 버전 목록에는 표시되지 않습니다.

#### <a name="mount-a-file-share"></a>파일 공유 탑재
먼저 `net use` 명령을 사용하여 파일 공유를 탑재합니다.

#### <a name="open-a-mounted-share-in-file-explorer"></a>파일 탐색기에서 탑재된 공유 열기
파일 탐색기로 이동하고 탑재된 공유를 찾습니다.

![파일 탐색기의 탑재된 공유](./media/storage-snapshots-list-browse/snapshot-windows-mount.png)

#### <a name="list-previous-versions"></a>이전 버전 나열
복원해야 하는 항목 또는 부모 항목을 찾습니다. 해당 항목을 두 번 클릭하여 원하는 디렉터리로 이동합니다. 마우스 오른쪽 단추로 클릭하고 메뉴에서 **속성**을 선택합니다.

![선택한 디렉터리를 마우스 오른쪽 단추로 클릭하여 표시된 메뉴](./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png)

**이전 버전**을 선택하여 이 디렉터리에 대한 공유 스냅숏의 목록을 봅니다. 네트워크 속도 및 디렉터리의 공유 스냅숏 수에 따라 목록이 표시되는 데 수 초 정도 걸릴 수 있습니다.

![이전 버전 탭](./media/storage-snapshots-list-browse/snapshot-windows-list.png)

**열기**를 선택하여 특정 스냅숏을 열 수 있습니다. 

![열린 스냅숏](./media/storage-snapshots-list-browse/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>이전 버전에서 복원
**복원**을 선택하여 공유 스냅숏을 만들 때의 전체 디렉터리의 내용을 원래 위치에 재귀적으로 복사합니다.
 ![경고 메시지의 복원 단추](./media/storage-snapshots-list-browse/snapshot-windows-restore.png)

### <a name="share-snapshot-operations-in-azure-cli-20"></a>Azure CLI 2.0에서 공유 스냅숏 작업
Azure CLI 2.0을 사용하여 공유 스냅숏 나열, 공유 스냅숏 내용 찾아보기, 공유 스냅숏에서 파일 복원/다운로드 또는 공유 스냅숏 삭제 등의 작업을 수행할 수 있습니다.

#### <a name="list-share-snapshots"></a>공유 스냅숏 나열

`--include-snapshots`과 함께 [`az storage share list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list)를 사용하여 특정 공유의 스냅숏을 나열할 수 있습니다.

```azurecli-interactive 
az storage share list --include-snapshots
```

이 명령은 관련된 모든 속성과 함께 공유 스냅숏의 목록을 제공합니다. 다음은 출력 예제입니다.

```json
[
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:44:13.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:45:18.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": null
  }
]
```

#### <a name="browse-to-a-share-snapshot"></a>공유 스냅숏 찾아보기
[`az storage file list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list)를 사용하여 특정 공유 스냅숏을 찾아서 해당 내용을 볼 수 있습니다. 다음 예제와 같이 찾아보려는 공유 이름과 타임스탬프를 지정합니다.

```azurecli-interactive 
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

출력에서 공유 스냅숏의 내용이 해당 공유 스냅숏을 만든 시점의 공유 내용과 같다는 것을 알 수 있습니다.

```
Name            Content Length    Type    Last Modified
--------------  ----------------  ------  ---------------
HelloWorldDir/                    dir
IMG_0966.JPG    533568            file
IMG_1105.JPG    717711            file
IMG_1341.JPG    608459            file
IMG_1405.JPG    652156            file
IMG_1611.JPG    442671            file
IMG_1634.JPG    1495999           file
IMG_1635.JPG    974058            file

```
#### <a name="restore-from-a-share-snapshot"></a>공유 스냅숏에서 복원

공유 스냅숏에서 파일을 복사하거나 다운로드하여 파일을 복원할 수 있습니다. 다음 예제와 같이 `az storage file download` 명령을 사용합니다.

```azurecli-interactive 
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```

출력에서 다운로드한 파일의 내용과 해당 속성이 공유 스냅숏을 만든 시점의 내용과 속성과 같다는 것을 알 수 있습니다.

```json
{
  "content": null,
  "metadata": {},
  "name": "IMG_0966.JPG",
  "properties": {
    "contentLength": 533568,
    "contentRange": "bytes 0-533567/533568",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentType": "application/octet-stream"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D50B5F49F7ACDF\"",
    "lastModified": "2017-10-04T19:37:03+00:00",
    "serverEncrypted": true
  }
}
```

### <a name="file-share-snapshot-operations-in-azure-powershell"></a>Azure PowerShell에서 파일 공유 스냅숏 작업
Azure PowerShell을 사용하여 공유 스냅숏 나열, 공유 스냅숏 내용 찾아보기, 공유 스냅숏에서 파일 복원 또는 다운로드, 또는 공유 스냅숏 삭제 등 동일한 작업을 수행할 수 있습니다.

#### <a name="list-share-snapshots"></a>공유 스냅숏 나열

`Get-AzureStorageShare`를 사용하여 특정 공유의 공유 스냅숏을 나열할 수 있습니다.

```powershell
Get-AzureStorageShare -Name "ContosoShare06" -SnapshotTime "6/16/2017 9:48:41 AM +00:00"
```

#### <a name="browse-share-snapshots"></a>공유 스냅숏 찾아보기
특정 스냅숏을 가리키는 `-Share` 값을 가진 `Get-AzureStorageFile`을 사용하여 특정 공유 스냅숏으로 이동하여 해당 콘텐츠를 볼 수도 있습니다.

```powershell
$snapshot = Get-AzureStorageShare -Name "ContosoShare06" -SnapshotTime "6/16/2017 9:48:41 AM +00:00"
Get-AzureStorageFile -Share $snapshot
```

#### <a name="restore-from-share-snapshots"></a>공유 스냅숏에서 복원

`Get-AzureStorageFileContent` 명령을 사용하여 공유 스냅숏에서 파일을 복사 또는 다운로드하여 파일을 복원할 수 있습니다.

```powershell
$download='C:\Temp\Download'
Get-AzureStorageFileContent -Share $snapshot -Path $file -Destination $download
```

```powershell
$snapshot = Get-AzureStorageShare -Name "ContosoShare06" -SnapshotTime "6/16/2017 9:48:41 AM +00:00"
$directory = Get-AzureStorageFile -ShareName "ContosoShare06" -Path "ContosoWorkingFolder" | Get-AzureStorageFile
Get-AzureStorageFileContent -Share $snapshot -Path $file -Destination $directory
```

## <a name="delete-a-share-snapshot"></a>공유 스냅숏 삭제

Azure Portal, PowerShell, CLI, REST API 또는 저장소 SDK를 사용하여 공유 스냅숏을 삭제할 수 있습니다. 다음 섹션에서는 Azure Portal, CLI 및 PowerShell을 사용하여 공유 스냅숏을 삭제하는 방법에 대해 설명합니다.

비교 도구를 사용하여 스냅숏을 찾아보고 두 스냅숏 간의 차이점을 볼 수 있습니다. 그런 다음 삭제할 공유 스냅숏을 결정할 수 있습니다. 

공유 스냅숏이 있는 공유는 삭제할 수 없습니다. 공유를 삭제하려면 먼저 해당 공유의 모든 공유 스냅숏을 삭제해야 합니다.

### <a name="delete-a-share-snapshot-by-using-the-portal"></a>포털을 사용하여 공유 스냅숏 삭제  
포털에서 파일 공유 블레이드로 이동하고, **삭제** 단추를 선택하여 하나 이상의 공유 스냅숏을 삭제할 수 있습니다.

>   ![삭제 단추](./media/storage-snapshots-delete/portal-snapshots-delete.png)


### <a name="delete-a-share-snapshot-by-using-azure-cli-20"></a>Azure CLI 2.0을 사용하여 공유 스냅숏 삭제
`[az storage share delete]` 명령을 사용하여 공유 스냅숏을 삭제할 수 있습니다. 다음 예제에서는 `--snapshot '2017-10-04T23:28:35.0000000Z' ` 매개 변수에 대한 공유 스냅숏 타임스탬프를 사용합니다.

```azurecli-interactive
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

예제 출력:
```json
{
  "deleted": true
}
```

### <a name="delete-a-share-snapshot-by-using-powershell"></a>PowerShell을 사용하여 공유 스냅숏 삭제
`Remove-AzureStorageShare -Share` 명령을 사용하여 공유 스냅숏을 삭제할 수 있습니다.

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##Create snapshot
$share=Get-AzureStorageShare -Context $ctx -Name <file share name>
$share.Properties.LastModified
$share.IsSnapshot
$snapshot=$share.Snapshot()

##Delete snapshot
Remove-AzureStorageShare -Share $snapshot

```

## <a name="next-steps"></a>다음 단계
* [스냅숏 개요](storage-snapshots-files.md)
* [스냅숏 FAQ](storage-files-faq.md)
