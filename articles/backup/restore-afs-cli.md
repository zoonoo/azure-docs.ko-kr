---
title: Azure CLI를 사용하여 Azure 파일 공유 복원
description: Azure CLI를 사용하여 Recovery Services 자격 증명 모음에서 백업된 Azure 파일 공유를 복원하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: a025de7bfb9db037b2008d69be7782feabb482f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94562324"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>Azure CLI를 사용하여 Azure 파일 공유 복원

Azure CLI는 Azure 리소스를 관리하는 Azure의 명령줄 환경을 제공합니다. Azure 리소스를 사용하기 위해 사용자 지정 자동화를 빌드하는 데 좋은 도구입니다. 이 문서에서는 Azure CLI를 사용하여 [Azure Backup](./backup-overview.md)을 통해 만든 복원 지점에서 전체 파일 공유 또는 특정 파일을 복원하는 방법을 설명합니다. [Azure PowerShell](./backup-azure-afs-automation.md)을 사용하거나 [Azure Portal](backup-afs.md)에서 이 단계를 수행할 수도 있습니다.

이 문서를 끝까지 읽으면 Azure CLI를 사용하여 다음 작업을 수행하는 방법을 배우게 될 것입니다.

* 백업된 Azure 파일 공유의 복원 지점을 확인합니다.
* 전체 Azure 파일 공유를 복원합니다.
* 개별 파일 또는 폴더를 복원합니다.

>[!NOTE]
> 이제 Azure Backup에서는 Azure CLI를 사용하여 원본 또는 대체 위치로 여러 파일 또는 폴더를 복원할 수 있습니다. 자세한 내용은 이 문서에서 [여러 파일 또는 폴더를 원본 또는 대체 위치로 복원](#restore-multiple-files-or-folders-to-original-or-alternate-location) 섹션을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 Azure Backup으로 백업된 Azure 파일 공유가 이미 있다고 가정합니다. 없는 경우 [CLI를 사용한 Azure 파일 공유 백업](backup-afs-cli.md)을 참조하여 파일 공유에 대한 백업을 구성합니다. 이 문서에서는 다음 리소스를 사용합니다.

| 파일 공유 | 스토리지 계정 | 지역 | 세부 정보 |
|---|---|---|---|
| *azurefiles* | *afsaccount* | EastUS | Azure Backup을 사용하여 백업한 원본 |
| *azurefiles1* | *afaccount1* | EastUS | 대체 위치 복구에 사용되는 대상 원본 |

파일 공유에 유사한 구조를 사용하여 이 문서에서 설명하는 다양한 유형의 복원을 시도해 볼 수 있습니다.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

 - 이 자습서에는 Azure CLI 버전 2.0.18 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Azure 파일 공유에 대한 복구 지점 페치

[az backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list) cmdlet을 사용하여 백업된 파일 공유에 대한 모든 복구 지점을 나열합니다.

다음 예제에서는 *afsaccount* 스토리지 계정의 *azurefiles* 파일 공유에 대한 복구 지점의 목록을 페치합니다.

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --backup-management-type azurestorage --item-name "AzureFileShare;azurefiles" --workload-type azurefileshare --out table
```

또한 컨테이너 및 항목에 익숙한 이름을 사용하여 다음과 같은 두 개의 추가 매개 변수를 제공하면 이전 cmdlet을 실행할 수 있습니다.

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

결과 집합은 각 복원 지점에 대한 시간 및 일관성 세부 정보를 포함하는 복구 지점 목록입니다.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

출력의 **Name** 특성은 복구 작업의 **--rp-name** 매개 변수에 대한 값으로 사용할 수 있는 복구 지점 이름에 해당합니다.

## <a name="full-share-recovery-by-using-the-azure-cli"></a>Azure CLI를 사용하여 전체 공유 복구

이 복원 옵션을 사용하여 원래 위치나 대체 위치에서 전체 파일 공유를 복원할 수 있습니다.

다음 매개 변수를 정의하여 복원 작업을 수행합니다.

* **--container-name**: 백업된 원본 파일 공유를 호스트하는 스토리지 계정의 이름입니다. 컨테이너의 이름 또는 식별 이름을 검색하려면 [az backup container list](/cli/azure/backup/container#az-backup-container-list) 명령을 사용합니다.
* **--item-name**: 복원 작업에 사용하려는 백업된 원래 파일 공유의 이름입니다. 백업된 항목의 이름 또는 식별 이름을 검색하려면 [az backup item list](/cli/azure/backup/item#az-backup-item-list) 명령을 사용합니다.

### <a name="restore-a-full-share-to-the-original-location"></a>원래 위치로 전체 공유 복원

원래 위치로 복원하는 경우에는 대상 관련 매개 변수를 지정할 필요가 없습니다. **충돌 해결** 만 제공해야 합니다.

다음 예제에서는 복원 모드가 *originallocation* 으로 설정된 [az backup restore restore-azurefileshare](/cli/azure/backup/restore#az-backup-restore-restore-azurefileshare) cmdlet을 사용하여 원래 위치에 *azurefiles* 파일 공유를 복원합니다. [Azure 파일 공유에 대한 복구 지점 페치](#fetch-recovery-points-for-the-azure-file-share)에서 얻은 복구 지점 932883129628959823을 사용합니다.

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

출력의 **Name** 특성은 복원 작업을 위해 백업 서비스에서 만든 작업의 이름에 해당합니다. 작업 상태를 추적하려면 [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet을 사용합니다.

### <a name="restore-a-full-share-to-an-alternate-location"></a>대체 위치로 복원 전체 공유 복원

이 옵션을 사용하여 파일 공유를 대체 위치로 복원하고 원래 파일 공유를 그대로 유지할 수 있습니다. 대체 위치 복구에 대해 다음 매개 변수를 지정합니다.

* **--target-storage-account**: 백업된 콘텐츠가 복원되는 스토리지 계정입니다. 대상 스토리지 계정은 자격 증명 모음과 동일한 위치에 있어야 합니다.
* **--target-file-share**: 백업된 콘텐츠가 복원되는 대상 스토리지 계정 내의 파일 공유입니다.
* **--target-folder**: 데이터가 복원되는 파일 공유 아래에 있는 폴더입니다. 백업된 콘텐츠를 루트 폴더로 복원해야 하는 경우 대상 폴더 값을 빈 문자열로 제공합니다.
* **--resolve-conflict**: 복원된 데이터와 충돌이 있는 경우 적용되는 지침입니다. **덮어쓰기** 또는 **건너뛰기** 를 수락합니다.

다음 예제에서는 복원 모드가 *alternatelocation* 인 [az backup restore restore-azurefileshare](/cli/azure/backup/restore#az-backup-restore-restore-azurefileshare)를 사용하여 *afsaccount* 스토리지 계정의 *azurefiles* 파일 공유를 *afaccount1* 스토리지 계정의 *azurefiles1"* 파일 공유로 복원합니다.

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

출력의 **Name** 특성은 복원 작업을 위해 백업 서비스에서 만든 작업의 이름에 해당합니다. 작업 상태를 추적하려면 [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet을 사용합니다.

## <a name="item-level-recovery"></a>항목 수준 복구

이 복원 옵션을 사용하여 원래 위치나 대체 위치에서 개별 파일 또는 폴더를 복원할 수 있습니다.

다음 매개 변수를 정의하여 복원 작업을 수행합니다.

* **--container-name**: 백업된 원본 파일 공유를 호스트하는 스토리지 계정의 이름입니다. 컨테이너의 이름 또는 식별 이름을 검색하려면 [az backup container list](/cli/azure/backup/container#az-backup-container-list) 명령을 사용합니다.
* **--item-name**: 복원 작업에 사용하려는 백업된 원래 파일 공유의 이름입니다. 백업된 항목의 이름 또는 식별 이름을 검색하려면 [az backup item list](/cli/azure/backup/item#az-backup-item-list) 명령을 사용합니다.

복구하려는 항목에 대해 다음 매개 변수를 지정합니다.

* **SourceFilePath**: 파일 공유 내에서 복원되는 파일의 절대 경로(문자열)입니다. 이 경로는 [az storage file download](/cli/azure/storage/file#az-storage-file-download) 또는 [az storage file show](/cli/azure/storage/file#az-storage-file-show) CLI 명령에 사용된 경로와 동일합니다.
* **SourceFileType**: 디렉터리 또는 파일이 선택되었는지 여부를 선택합니다. **디렉터리** 또는 **파일** 을 수락합니다.
* **ResolveConflict**: 복원된 데이터와 충돌이 있는 경우 적용되는 지침입니다. **덮어쓰기** 또는 **건너뛰기** 를 수락합니다.

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>개별 파일 또는 폴더를 원래 위치로 복원

복원 모드가 *originallocation* 으로 설정된 [az backup restore restore-azurefiles](/cli/azure/backup/restore#az-backup-restore-restore-azurefiles) cmdlet을 사용하여 특정 파일이나 폴더를 원래 위치로 복원합니다.

다음 예제에서는 원래 위치인 *azurefiles* 파일 공유에서 *RestoreTest.txt* 파일을 복원합니다.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

출력의 **Name** 특성은 복원 작업을 위해 백업 서비스에서 만든 작업의 이름에 해당합니다. 작업 상태를 추적하려면 [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet을 사용합니다.

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>개별 파일 또는 폴더를 대체 위치로 복원

특정 파일 또는 폴더를 대체 위치로 복원하려면 복원 모드가 *alternatelocation* 으로 설정된 [az backup restore restore-azurefiles](/cli/azure/backup/restore#az-backup-restore-restore-azurefiles) cmdlet을 사용하고 다음 대상 관련 매개 변수를 지정합니다.

* **--target-storage-account**: 백업된 콘텐츠가 복원되는 스토리지 계정입니다. 대상 스토리지 계정은 자격 증명 모음과 동일한 위치에 있어야 합니다.
* **--target-file-share**: 백업된 콘텐츠가 복원되는 대상 스토리지 계정 내의 파일 공유입니다.
* **--target-folder**: 데이터가 복원되는 파일 공유 아래에 있는 폴더입니다. 백업된 콘텐츠를 루트 폴더로 복원해야 하는 경우 대상 폴더 값을 빈 문자열로 제공합니다.

다음 예제에서는 원래 *azurefiles* 파일 공유에 있는 *RestoreTest.txt* 파일을 대체 위치로 복원합니다. 이 위치는 *afaccount1* 스토리지 계정에서 호스트되는 *azurefiles1* 파일 공유의 *restoredata* 폴더입니다.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

출력의 **Name** 특성은 복원 작업을 위해 백업 서비스에서 만든 작업의 이름에 해당합니다. 작업 상태를 추적하려면 [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet을 사용합니다.

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>원래 위치나 대체 위치로 여러 파일 또는 폴더 복원

여러 항목에 대해 복원을 수행하려면 복원하려는 모든 파일 또는 폴더의 **공백으로 구분** 된 경로로 **source-file-path** 매개 변수의 값을 전달합니다.

다음 예제에서는 *Restore.txt* 및 *AFS testing Report.docx* 파일을 원래 위치에 복원합니다.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932889937058317910 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore Test.txt" "AFS Testing Report.docx" --resolve-conflict overwrite  --out table
```

다음과 유사하게 출력됩니다.

```output
Name                                          ResourceGroup
------------------------------------          ---------------
649b0c14-4a94-4945-995a-19e2aace0305          azurefiles
```

출력의 **Name** 특성은 복원 작업을 위해 백업 서비스에서 만든 작업의 이름에 해당합니다. 작업 상태를 추적하려면 [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet을 사용합니다.

여러 항목을 대체 위치로 복원하려면 [개별 파일 또는 폴더를 대체 위치로 복원](#restore-individual-files-or-folders-to-an-alternate-location) 섹션에 설명된 대로 대상 관련 매개 변수를 지정하여 위의 명령을 사용합니다.

## <a name="next-steps"></a>다음 단계

[Azure CLI를 사용하여 Azure 파일 공유 백업을 관리](manage-afs-backup-cli.md)하는 방법을 알아봅니다.
