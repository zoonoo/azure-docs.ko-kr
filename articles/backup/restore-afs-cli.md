---
title: Azure CLI를 사용 하 여 Azure 파일 공유 복원
description: Azure CLI를 사용 하 여 Recovery Services 자격 증명 모음에서 백업 된 Azure 파일 공유를 복원 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: be744fdb79f442eaf0ef632952d9c0b9e709d908
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325014"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>Azure CLI를 사용 하 여 Azure 파일 공유 복원

Azure CLI은 Azure 리소스를 관리 하기 위한 명령줄 환경을 제공 합니다. Azure 리소스를 사용 하는 사용자 지정 자동화를 빌드하기 위한 좋은 도구입니다. 이 문서에서는 Azure CLI를 사용 하 여 [Azure Backup](./backup-overview.md) 에서 만든 복원 지점에서 전체 파일 공유 또는 특정 파일을 복원 하는 방법을 설명 합니다. [Azure PowerShell](./backup-azure-afs-automation.md)을 사용하거나 [Azure Portal](backup-afs.md)에서 이 단계를 수행할 수도 있습니다.

이 문서의 끝부분에서는 Azure CLI를 사용 하 여 다음 작업을 수행 하는 방법을 배웁니다.

* 백업 된 Azure 파일 공유의 복원 지점이 표시 됩니다.
* 전체 Azure 파일 공유를 복원 합니다.
* 개별 파일 또는 폴더를 복원 합니다.

>[!NOTE]
> Azure Backup에서는 Azure CLI를 사용 하 여 여러 파일 또는 폴더를 원본 또는 대체 위치로 복원할 수 있습니다. 자세한 내용은이 문서에서 [여러 파일 또는 폴더를 원본 또는 대체 위치로 복원](#restore-multiple-files-or-folders-to-original-or-alternate-location) 섹션을 참조 하세요.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하고 사용하려는 경우 Azure CLI 버전 2.0.18 이상을 사용해야 합니다. CLI 버전을 찾으려면 `az --version`을 실행합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 Azure Backup에 의해 백업 되는 Azure 파일 공유가 이미 있다고 가정 합니다. 없으면 [CLI를 사용 하 여 Azure 파일 공유](backup-afs-cli.md) 백업을 참조 하 여 파일 공유에 대 한 백업을 구성 합니다. 이 문서에서는 다음 리소스를 사용 합니다.

| 파일 공유  | 스토리지 계정 | 지역 | 세부 정보                                                      |
| ----------- | --------------- | ------ | ------------------------------------------------------------ |
| *azurefiles*  | *afsaccount*      | EastUS | Azure Backup를 사용 하 여 백업한 원본 원본                 |
| *azurefiles1* | *afaccount1*      | EastUS | 대체 위치 복구에 사용 되는 대상 원본 |

파일 공유에 비슷한 구조를 사용 하 여이 문서에서 설명 하는 다양 한 유형의 복원을 사용해 볼 수 있습니다.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Azure 파일 공유에 대 한 복구 지점의 페치

[Az backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list) cmdlet을 사용 하 여 백업 된 파일 공유에 대 한 모든 복구 지점의 목록을 표시 합니다.

다음 예에서는 *afsaccount* 저장소 계정의 *azurefiles* 파일 공유에 대 한 복구 지점의 목록을 페치합니다.

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --backup-management-type azurestorage --item-name "AzureFileShare;azurefiles" --workload-type azurefileshare --out table
```

다음과 같은 두 개의 추가 매개 변수를 제공 하 여 컨테이너 및 항목에 대 한 친숙 한 이름을 사용 하 여 이전 cmdlet을 실행할 수도 있습니다.

* **--백업 관리-유형**: *azurestorage*
* **--워크 로드 유형**: *azurefileshare 공유*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

결과 집합은 각 복원 지점에 대 한 시간 및 일관성 정보를 포함 하는 복구 지점 목록입니다.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

출력의 **이름** 특성은 복구 작업의 **--rp-Name** 매개 변수에 대 한 값으로 사용할 수 있는 복구 지점 이름에 해당 합니다.

## <a name="full-share-recovery-by-using-the-azure-cli"></a>Azure CLI를 사용 하 여 전체 공유 복구

이 복원 옵션을 사용 하 여 원래 위치 또는 대체 위치에서 전체 파일 공유를 복원할 수 있습니다.

다음 매개 변수를 정의 하 여 복원 작업을 수행 합니다.

* **--container-name**: 백업 된 원래 파일 공유를 호스트 하는 저장소 계정의 이름입니다. 컨테이너의 이름 또는 이름을 검색 하려면 [az backup container list](/cli/azure/backup/container#az-backup-container-list) 명령을 사용 합니다.
* **--item-name**: 복원 작업에 사용 하려는 백업 된 원래 파일 공유의 이름입니다. 백업 된 항목의 이름 또는 이름을 검색 하려면 [az backup item list](/cli/azure/backup/item#az-backup-item-list) 명령을 사용 합니다.

### <a name="restore-a-full-share-to-the-original-location"></a>전체 공유를 원래 위치로 복원

원래 위치로 복원 하는 경우 대상 관련 매개 변수를 지정할 필요가 없습니다. **충돌 해결** 만 제공 해야 합니다.

다음 예에서는 복원 모드가 *originallocation* 로 설정 된 [az backup restore restore-azurefilefilecmdlet](/cli/azure/backup/restore#az-backup-restore-restore-azurefileshare) 을 사용 하 여 원래 위치에 *azurefileshare* 파일 공유를 복원 합니다. [Azure 파일 공유에 대 한 복구 지점 페치](#fetch-recovery-points-for-the-azure-file-share)에서 얻은 복구 지점 932883129628959823을 사용 합니다.

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

출력의 **이름** 특성은 복원 작업을 위해 백업 서비스에 의해 생성 된 작업의 이름에 해당 합니다. 작업 상태를 추적 하려면 [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet을 사용 합니다.

### <a name="restore-a-full-share-to-an-alternate-location"></a>대체 위치로 전체 공유 복원

이 옵션을 사용 하 여 파일 공유를 대체 위치로 복원 하 고 원래 파일 공유를 그대로 유지할 수 있습니다. 대체 위치 복구에 대해 다음 매개 변수를 지정 합니다.

* **--target-account**: 백업 된 콘텐츠를 복원할 저장소 계정입니다. 대상 스토리지 계정은 자격 증명 모음과 동일한 위치에 있어야 합니다.
* **--target-파일-공유**: 백업 된 콘텐츠를 복원할 대상 저장소 계정 내의 파일 공유입니다.
* **--target-folder**: 데이터가 복원 되는 파일 공유의 폴더입니다. 백업된 콘텐츠를 루트 폴더로 복원해야 하는 경우 대상 폴더 값을 빈 문자열로 제공합니다.
* **--해결-충돌**: 복원 된 데이터와 충돌 하는 경우 명령입니다. **덮어쓰기** 또는 **건너뛰기**를 수락합니다.

다음 예제에서는 [az backup restore restore-azurefileshare](/cli/azure/backup/restore#az-backup-restore-restore-azurefileshare) 이름으로 복원 모드를 *alternatelocation* 로 사용 하 여 *afsaccount* 저장소 계정의 *azurefileshare* 파일 공유를 *afaccount1* 저장소 계정의 *azurefiles1 "* 파일 공유로 복원 합니다.

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

출력의 **이름** 특성은 복원 작업을 위해 백업 서비스에 의해 생성 된 작업의 이름에 해당 합니다. 작업 상태를 추적 하려면 [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet을 사용 합니다.

## <a name="item-level-recovery"></a>항목 수준 복구

이 복원 옵션을 사용 하 여 원본 또는 대체 위치의 개별 파일 또는 폴더를 복원할 수 있습니다.

다음 매개 변수를 정의 하 여 복원 작업을 수행 합니다.

* **--container-name**: 백업 된 원래 파일 공유를 호스트 하는 저장소 계정의 이름입니다. 컨테이너의 이름 또는 이름을 검색 하려면 [az backup container list](/cli/azure/backup/container#az-backup-container-list) 명령을 사용 합니다.
* **--item-name**: 복원 작업에 사용 하려는 백업 된 원래 파일 공유의 이름입니다. 백업 된 항목의 이름 또는 이름을 검색 하려면 [az backup item list](/cli/azure/backup/item#az-backup-item-list) 명령을 사용 합니다.

복구 하려는 항목에 대해 다음 매개 변수를 지정 합니다.

* **Sourcefilepath**: 파일 공유 내에서 복원할 파일의 절대 경로입니다. 이 경로는 [az storage file download](/cli/azure/storage/file#az-storage-file-download) 또는 [az storage file show](/cli/azure/storage/file#az-storage-file-show) CLI 명령에 사용 된 경로와 동일 합니다.
* **Sourcefiletype**: 디렉터리 또는 파일 선택 여부를 선택 합니다. **디렉터리** 또는 **파일**을 허용 합니다.
* **Resolveconflict가**: 복원 된 데이터와 충돌 하는 경우 명령입니다. **덮어쓰기** 또는 **건너뛰기**를 수락합니다.

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>개별 파일 또는 폴더를 원래 위치로 복원

복원 모드가 *originallocation* 로 설정 된 [az backup restore restore-azurefiles](/cli/azure/backup/restore#az-backup-restore-restore-azurefiles) cmdlet을 사용 하 여 특정 파일이 나 폴더를 원래 위치로 복원 합니다.

다음 예에서는 원래 위치인 *azurefiles* 파일 공유에서 *RestoreTest.txt* 파일을 복원 합니다.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

출력의 **이름** 특성은 복원 작업을 위해 백업 서비스에 의해 생성 된 작업의 이름에 해당 합니다. 작업 상태를 추적 하려면 [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet을 사용 합니다.

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>개별 파일 또는 폴더를 대체 위치로 복원

특정 파일 또는 폴더를 대체 위치로 복원 하려면 복원 모드가 *alternatelocation* 로 설정 된 [az backup restore restore-azurefiles](/cli/azure/backup/restore#az-backup-restore-restore-azurefiles) cmdlet을 사용 하 고 다음 대상 관련 매개 변수를 지정 합니다.

* **--target-account**: 백업 된 콘텐츠를 복원할 저장소 계정입니다. 대상 스토리지 계정은 자격 증명 모음과 동일한 위치에 있어야 합니다.
* **--target-파일-공유**: 백업 된 콘텐츠를 복원할 대상 저장소 계정 내의 파일 공유입니다.
* **--target-folder**: 데이터가 복원 되는 파일 공유의 폴더입니다. 백업 된 콘텐츠를 루트 폴더로 복원 하려면 대상 폴더의 값을 빈 문자열로 지정 합니다.

다음 예에서는 원래 *azurefiles* 파일 공유에 있는 *RestoreTest.txt* 파일을 대체 위치로 복원 합니다 .이 파일은 *afaccount1* 저장소 계정에서 호스트 되는 *azurefiles1* 파일 공유의 *restoredata* 폴더입니다.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

출력의 **이름** 특성은 복원 작업을 위해 백업 서비스에 의해 생성 된 작업의 이름에 해당 합니다. 작업 상태를 추적 하려면 [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet을 사용 합니다.

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>원본 또는 대체 위치로 여러 파일 또는 폴더 복원

여러 항목에 대해 복원을 수행 하려면 복원 하려는 모든 파일 또는 폴더의 **공백으로 구분** 된 경로로 **원본 파일 경로** 매개 변수의 값을 전달 합니다.

다음 예제에서는 *Restore.txt* 및 *AFS 테스트 Report.docx* 파일을 원래 위치에 복원 합니다.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932889937058317910 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore Test.txt" "AFS Testing Report.docx" --resolve-conflict overwrite  --out table
```

다음과 유사하게 출력됩니다.

```output
Name                                          ResourceGroup
------------------------------------          ---------------
649b0c14-4a94-4945-995a-19e2aace0305          azurefiles
```

출력의 **이름** 특성은 복원 작업을 위해 백업 서비스에 의해 생성 된 작업의 이름에 해당 합니다. 작업 상태를 추적 하려면 [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet을 사용 합니다.

여러 항목을 대체 위치로 복원 하려면 [개별 파일 또는 폴더를 대체 위치에 복원](#restore-individual-files-or-folders-to-an-alternate-location) 섹션에 설명 된 대로 대상 관련 매개 변수를 지정 하 여 위의 명령을 사용 합니다.

## <a name="next-steps"></a>다음 단계

[Azure CLI를 사용 하 여 Azure 파일 공유 백업을 관리](manage-afs-backup-cli.md)하는 방법을 알아봅니다.
