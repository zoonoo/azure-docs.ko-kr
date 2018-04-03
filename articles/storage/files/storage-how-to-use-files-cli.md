---
title: Azure CLI를 사용하여 Azure 파일 공유 관리
description: Azure CLI를 사용하여 Azure Files를 관리하는 방법을 알아봅니다.
services: storage
documentationcenter: na
author: wmgries
manager: jeconnoc
editor: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.openlocfilehash: 066a43b553be18a5a0bc889fff441824df301b98
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2018
---
# <a name="managing-azure-file-shares-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure 파일 공유 관리
[Azure Files](storage-files-introduction.md)는 사용하기 쉬운 Microsoft 클라우드 파일 시스템입니다. Azure 파일 공유는 Windows, Linux 및 macOS에 탑재할 수 있습니다. 이 가이드에서는 Azure CLI를 사용하여 Azure 파일 공유로 작업하는 기본 사항을 설명합니다. 방법 배우기: 

> [!div class="checklist"]
> * 리소스 그룹 및 저장소 계정 만들기
> * Azure 파일 공유 만들기 
> * 디렉터리 만들기
> * 파일 업로드 
> * 파일 다운로드
> * 공유 스냅숏 만들기 및 사용

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Azure CLI를 로컬로 설치하여 사용하도록 결정한 경우 이 가이드에서는 Azure CLI 버전 2.0.4 이상을 실행해야 합니다. 버전을 찾으려면 **az --version**을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요. 

기본적으로 Azure CLI 명령은 JSON(JavaScript Object Notation)을 반환합니다. 이것이 REST API에서 메시지를 송수신하는 사실상의 방법입니다. JSON 응답으로 작업을 용이하게 하기 위해 이 가이드의 일부 예제에서는 Azure CLI 명령에 쿼리 매개 변수를 사용합니다. 이 매개 변수는 JSON을 구문 분석하기 위해 [JMESPath 쿼리 언어](http://jmespath.org/)를 사용합니다. [JMESPath 자습서](http://jmespath.org/tutorial.html)를 따라 Azure CLI 명령의 결과를 조작하는 방법에 대해 자세히 알아볼 수 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기
리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. Azure 리소스 그룹이 아직 없는 경우 [az group create](/cli/azure/group#create) 명령을 사용하여 새 리소스 그룹을 만들 수 있습니다. 

다음 예제에서는 *미국 동부* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>저장소 계정 만들기
저장소 계정은 Azure 파일 공유 또는 Blob나 큐와 같은 다른 저장소 리소스를 배포할 수 있는 저장소의 공유 풀입니다. 저장소 계정에 포함할 수 있는 파일 공유 수에는 제한이 없으며, 공유에 저장할 수 있는 파일 수에는 저장소 계정의 최대 용량 한도까지 제한이 없습니다.

이 예제에서는 [az storage account create](/cli/azure/storage/account#create) 명령을 사용하여 `mystorageaccount<random number>`라는 저장소 계정을 만든 다음, 해당 저장소 계정의 이름을 `$STORAGEACCT` 변수에 배치합니다. 저장소 계정 이름은 고유해야 합니다. `$RANDOM`를 사용하면 저장소 계정 이름의 끝에 숫자를 추가하여 고유하게 만듭니다. 

```azurecli-interactive 
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

### <a name="get-the-storage-account-key"></a>저장소 계정 키 가져오기
저장소 계정 키를 사용하여 저장소 계정에서 리소스에 대한 액세스를 제어합니다. 저장소 계정을 만들 때 자동으로 만들어집니다. [az storage account keys list](/cli/azure/storage/account/keys#list) 명령을 사용하여 저장소 계정에 대한 저장소 계정 키를 가져올 수 있습니다. 

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Azure 파일 공유 만들기
이제 첫 번째 Azure 파일 공유를 만들 수 있습니다. [az storage share create](/cli/azure/storage/share#create) 명령을 사용하여 파일 공유를 만들 수 있습니다. 이 예제에서는 `myshare`라는 Azure 파일 공유를 만듭니다. 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

> [!Important]  
> 공유 이름은 모두 소문자, 숫자 및 단일 하이픈이어야 하지만 하이픈으로 시작할 수 없습니다. 파일 공유 및 파일 이름 지정에 대한 자세한 내용은 [공유, 디렉터리, 파일 및 메타데이터 이름 지정 및 참조](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)를 참조하세요.

## <a name="manipulating-the-contents-of-the-azure-file-share"></a>Azure 파일 공유의 콘텐츠 조작
이제 Azure 파일 공유를 만들었으므로 [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) 또는 [macOS](storage-how-to-use-files-mac.md)에서 SMB를 사용하여 파일 공유를 탑재할 수 있습니다. 또는 Azure CLI를 사용하여 Azure 파일 공유를 조작할 수 있습니다. 이점이 SMB를 사용하여 파일 공유를 탑재하는 이점입니다. Azure CLI를 사용하여 만든 모든 요청이 파일 REST API로 구성되어 다음에서 파일 공유의 파일 및 디렉터리를 만들고, 수정하고, 삭제할 수 있기 때문입니다.

- Bash Cloud Shell(SMB를 통한 파일 공유를 탑재할 수 없음)
- 포트 445의 차단을 해제하지 않은 온-프레미스 클라이언트 등 SMB 공유를 탑재할 수 없는 클라이언트
- [Azure Functions](../../azure-functions/functions-overview.md)와 같은 서버가 없는 시나리오 

### <a name="create-directory"></a>디렉터리 만들기
Azure 파일 공유의 루트에 *myDirectory*라는 새 디렉터리를 만들려면 [`az storage directory create`](/cli/azure/storage/directory#az_storage_directory_create) 명령을 사용합니다.

```azurecli-interactive
az storage directory create \
   --account-name $STORAGEACCT \
   --account-key $STORAGEKEY \
   --share-name "myshare" \
   --name "myDirectory" 
```

### <a name="upload-a-file"></a>파일 업로드
[`az storage file upload`](/cli/azure/storage/file#az_storage_file_upload) 명령을 사용하여 파일을 업로드하는 방법을 보여주려면 먼저 업로드할 Azure CLI Cloud Shell의 임시 드라이브 내에 파일을 만들어야 합니다. 다음 예제에서는 파일을 만든 다음, 업로드합니다.

```azurecli-interactive
date > ~/clouddrive/SampleUpload.txt

az storage file upload \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --source "~/clouddrive/SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Azure CLI을 로컬로 실행 중인 경우 `~/clouddrive`를 컴퓨터에 있는 경로로 대체해야 합니다.

파일을 업로드한 후에 [`az storage file list`](/cli/azure/storage/file#az_storage_file_list) 명령을 사용하여 파일이 Azure 파일 공유에 업로드되었는지 확인할 수 있습니다.

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>파일 다운로드
[`az storage file download`](/cli/azure/storage/file#az_storage_file_download) 명령을 사용하여 Cloud Shell의 임시 드라이브에 업로드된 파일의 복사본을 다운로드할 수 있습니다.

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before
rm -rf ~/clouddrive/SampleDownload.txt

az storage file download \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt" \
    --dest "~/clouddrive/SampleDownload.txt"
```

### <a name="copy-files"></a>파일 복사
일반적인 작업은 하나의 파일 공유에서 다른 파일 공유로 파일을 복사하거나 Azure Blob Storage 컨테이너 간에 파일을 복사하는 것입니다. 이 기능을 보여주기 위해 [az storage file copy](/cli/azure/storage/file/copy) 명령을 사용하여 새 공유를 만들고, 이러한 새 공유에 업로드한 파일을 복사할 수 있습니다. 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare2"

az storage directory create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --name "myDirectory2"

az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-share "myshare" \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share "myshare2" \
    --destination-path "myDirectory2/SampleCopy.txt"
```

이제 새 공유에서 파일을 나열하는 경우 복사된 파일이 표시됩니다.

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --output table
```

`az storage file copy start` 명령은 Azure 파일 공유과 Azure Blob Storage 컨테이너 간에 임시 파일 이동에 편리하지만(이동되는 파일의 수 또는 크기 측면에서) 대규모 이동에 AzCopy를 사용하는 것이 좋습니다. [Linux용 AzCopy](../common/storage-use-azcopy-linux.md) 및 [Windows용 AzCopy](../common/storage-use-azcopy.md)에 대해 자세히 알아봅니다. AzCopy를 로컬로 설치해야 합니다. Cloud Shell에서 사용할 수 없습니다. 

## <a name="create-and-modify-share-snapshots"></a>공유 스냅숏 만들기 및 수정
Azure 파일 공유로 수행할 수 있는 유용한 한 가지 추가 작업은 공유 스냅숏을 만드는 것입니다. 스냅숏은 Azure 파일 공유의 지정 시간을 유지합니다. 공유 스냅숏은 이미 익숙한 다음과 같은 운영 시스템 기술과 유사합니다.
- Linux 시스템용 [LVM(논리 볼륨 관리자)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) 스냅숏
- macOS용 [APFS(Apple 파일 시스템)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) 스냅숏 
- NTFS 및 ReFS와 같은 Windows 파일 시스템용 [VSS(볼륨 섀도 복사본 서비스)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636)

[`az storage share snapshot`](/cli/azure/storage/share#az_storage_share_snapshot) 명령을 사용하여 공유 스냅숏을 만들 수 있습니다.

```azurecli-interactive
SNAPSHOT=$(az storage share snapshot \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>공유 스냅숏 콘텐츠 찾아보기
`az storage file list` 명령에 대한 `$SNAPSHOT` 변수에서 캡처한 공유 스냅숏의 타임스탬프를 전달하여 공유 스냅숏의 콘텐츠를 찾아볼 수 있습니다.

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --snapshot $SNAPSHOT \
    --output table
```

### <a name="list-share-snapshots"></a>공유 스냅숏 나열
다음 명령을 사용하여 공유에 만든 스냅숏의 목록을 볼 수 있습니다.

```azurecli-interactive
az storage share list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --include-snapshot \
    --query "[? name=='myshare' && snapshot!=null]" | tr -d '"'
```

### <a name="restore-from-a-share-snapshot"></a>공유 스냅숏에서 복원
이전에 사용한 `az storage file copy start` 명령을 사용하여 파일을 복원할 수 있습니다. 먼저 스냅숏에서 복원할 수 있도록 업로드한 `SampleUpload.txt` 파일을 삭제합니다.

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt"

# Build the source URI for snapshot restore
URI=$(az storage account show \
    --resource-group "myResourceGroup" \
    --name $STORAGEACCT \
    --query "primaryEndpoints.file" | tr -d '"')

URI=$URI"myshare/myDirectory/SampleUpload.txt?sharesnapshot="$SNAPSHOT

# Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-uri $URI \
    --destination-share "myshare" \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>공유 스냅숏 삭제
`--snapshot` 매개 변수에 대한 `$SNAPSHOT` 참조를 포함하는 변수와 함께 [`az storage share delete`](/cli/azure/storage/share#az_storage_share_delete) 명령을 사용하여 공유 스냅숏을 삭제할 수 있습니다.

```azurecli-interactive
az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --snapshot $SNAPSHOT
```

## <a name="clean-up-resources"></a>리소스 정리
작업을 완료하면 [`az group delete`](/cli/azure/group#delete) 명령을 사용하여 리소스 그룹 및 모든 관련된 리소스를 제거할 수 있습니다. 

```azurecli-interactive 
az group delete --name "myResourceGroup"
```

또는 리소스를 하나씩 제거할 수 있습니다.
- 이 빠른 시작에서 만든 Azure 파일 공유를 제거하려면:

```azurecli-interactive
az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --delete-snapshots include

az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare2" \
    --delete-snapshots include
```

- 저장소 계정 자체를 제거하려면: (그러면 암시적으로 만든 Azure 파일 공유뿐만 아니라 Azure Blob Storage 컨테이너와 같이 만든 다른 저장소 리소스도 제거합니다.)

```azurecli-interactive
az storage account delete \
    --resource-group "myResourceGroup" \
    --name $STORAGEACCT \
    --yes
```

## <a name="next-steps"></a>다음 단계
- [Azure Portal을 사용하여 파일 공유 관리](storage-how-to-use-files-portal.md)
- [Azure PowerShell을 사용하여 파일 공유 관리](storage-how-to-use-files-powershell.md)
- [저장소 탐색기를 사용하여 파일 공유 관리](storage-how-to-use-files-storage-explorer.md)
- [Azure 파일 배포에 대한 계획](storage-files-planning.md)