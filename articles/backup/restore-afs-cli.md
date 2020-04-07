---
title: Azure CLI를 통해 Azure 파일 공유 복원
description: Azure CLI를 사용하여 복구 서비스 자격 증명 모음에서 백업된 Azure 파일 공유를 복원하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 980044011e3417a2aff8447a939e02299923da38
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757099"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>Azure CLI를 통해 Azure 파일 공유 복원

Azure CLI는 Azure 리소스 관리를 위한 명령줄 환경을 제공합니다. Azure 리소스를 사용하도록 사용자 지정 자동화를 빌드하는 데 유용한 도구입니다. 이 문서에서는 Azure CLI를 사용하여 [Azure Backup에서](https://docs.microsoft.com/azure/backup/backup-overview) 만든 복원 지점에서 전체 파일 공유 또는 특정 파일을 복원하는 방법을 설명합니다. [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation)을 사용하거나 [Azure Portal](backup-afs.md)에서 이 단계를 수행할 수도 있습니다.

이 문서의 끝으로 Azure CLI를 사용하여 다음 작업을 수행하는 방법을 배웁니다.

* 백업된 Azure 파일 공유에 대한 복원 지점을 봅니다.
* 전체 Azure 파일 공유를 복원합니다.
* 개별 파일 또는 폴더를 복원합니다.

>[!NOTE]
> 이제 Azure Backup은 Azure CLI를 사용하여 여러 파일 또는 폴더를 원본 또는 대체 위치로 복원하는 것을 지원합니다. 자세한 내용은 이 문서의 [원본 또는 대체 위치 섹션으로 여러 파일 또는 폴더 복원을](#restore-multiple-files-or-folders-to-original-or-alternate-location) 참조하십시오.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하고 사용하려는 경우 Azure CLI 버전 2.0.18 이상을 사용해야 합니다. CLI 버전을 찾으려면 `az --version`을 실행합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 Azure Backup에서 백업한 Azure 파일 공유가 이미 있다고 가정합니다. 없는 경우 [CLI를 사용하여 Azure 파일 공유 백업을](backup-afs-cli.md) 참조하여 파일 공유에 대한 백업을 구성합니다. 이 문서에서는 다음 리소스를 사용합니다.

| 파일 공유  | 스토리지 계정 | 지역 | 세부 정보                                                      |
| ----------- | --------------- | ------ | ------------------------------------------------------------ |
| *azurefiles*  | *afsaccount*      | EastUS | Azure 백업을 사용하여 백업된 원본 원본                 |
| *azurefiles1* | *afaccount1*      | EastUS | 대체 위치 복구에 사용되는 대상 소스 |

파일 공유에 대해 유사한 구조를 사용하여 이 문서에서 설명하는 다양한 유형의 복원을 시도해 볼 수 있습니다.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Azure 파일 공유에 대한 복구 지점 가져오기

az [백업 복구 지점 목록](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) cmdlet을 사용하여 백업된 파일 공유에 대한 모든 복구 지점을 나열합니다.

다음 예제에서는 *afsaccount* 저장소 계정에서 *azurefiles* 파일 공유에 대 한 복구 지점 목록을 가져옵니다.

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --backup-management-type azurestorage --item-name "AzureFileShare;azurefiles" --workload-type azurefileshare --out table
```

컨테이너 및 항목에 대한 친숙한 이름을 사용하여 다음 두 가지 추가 매개 변수를 제공하여 이전 cmdlet을 실행할 수도 있습니다.

* **--백업 관리 유형**: *azurestorage*
* **--워크로드 유형**: *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

결과 집합은 각 복원 지점에 대한 시간 및 일관성 세부 정보가 있는 복구 지점 목록입니다.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

출력의 **Name** 특성은 복구 작업에서 **--rp-name** 매개 변수에 대한 값으로 사용할 수 있는 복구 지점 이름에 해당합니다.

## <a name="full-share-recovery-by-using-the-azure-cli"></a>Azure CLI를 사용하여 전체 공유 복구

이 복원 옵션을 사용하여 원본 또는 대체 위치에서 전체 파일 공유를 복원할 수 있습니다.

복원 작업을 수행할 다음 매개 변수를 정의합니다.

* **--컨테이너 이름**: 백업된 원본 파일 공유를 호스팅하는 저장소 계정의 이름입니다. 컨테이너의 이름이나 친숙한 이름을 검색하려면 [az 백업 컨테이너 목록](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) 명령을 사용합니다.
* **--항목 이름**: 복원 작업에 사용할 백업된 원본 파일 공유의 이름입니다. 백업된 항목의 이름이나 친숙한 이름을 검색하려면 [az 백업 항목 목록](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) 명령을 사용합니다.

### <a name="restore-a-full-share-to-the-original-location"></a>전체 공유를 원래 위치로 복원

원래 위치로 복원할 때 대상 관련 매개변수를 지정할 필요가 없습니다. **충돌 해결만** 제공되어야 합니다.

다음 예제에서는 [az 백업 복원 복원-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) cmdlet을 사용 하 여 복원 모드가 *원래 위치로* 설정 된 원래 위치에 *azurefiles* 파일 공유를 복원 합니다. [Azure 파일 공유에 대한 복구 지점 가져오기에서](#fetch-recovery-points-for-the-azure-file-share)얻은 복구 지점 932883129628999823을 사용합니다.

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

출력의 **Name** 특성은 복원 작업에 대한 백업 서비스에서 만든 작업의 이름에 해당합니다. 작업의 상태를 추적하려면 [az 백업 작업 표시](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet을 사용합니다.

### <a name="restore-a-full-share-to-an-alternate-location"></a>전체 공유를 대체 위치로 복원

이 옵션을 사용하여 파일 공유를 대체 위치로 복원하고 원래 파일 공유를 있는 상태로 유지할 수 있습니다. 대체 위치 복구를 위해 다음 매개 변수를 지정합니다.

* **--대상 저장소 계정**: 백업된 콘텐츠가 복원되는 저장소 계정입니다. 대상 스토리지 계정은 자격 증명 모음과 동일한 위치에 있어야 합니다.
* **--대상 파일 공유**: 백업된 콘텐츠가 복원되는 대상 저장소 계정 내의 파일 공유입니다.
* **--대상 폴더**: 데이터가 복원되는 파일 공유 아래의 폴더입니다. 백업된 콘텐츠를 루트 폴더로 복원해야 하는 경우 대상 폴더 값을 빈 문자열로 제공합니다.
* **--해결-충돌**: 복원된 데이터와 충돌이 있는 경우 명령합니다. **덮어쓰기** 또는 **건너뛰기**를 수락합니다.

다음 예제에서는 [az 백업 복원 복원 복원-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) 를 대체 *로케이션으로* 사용하여 *afsaccount* 저장소 계정의 *azurefiles* 파일 공유를 *afaccount1* 저장소 계정의 *azurefiles1"* 파일 공유로 복원합니다.

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

출력의 **Name** 특성은 복원 작업에 대한 백업 서비스에서 만든 작업의 이름에 해당합니다. 작업의 상태를 추적하려면 [az 백업 작업 표시](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet을 사용합니다.

## <a name="item-level-recovery"></a>항목 수준 복구

이 복원 옵션을 사용하여 원본 또는 대체 위치에 있는 개별 파일 또는 폴더를 복원할 수 있습니다.

복원 작업을 수행할 다음 매개 변수를 정의합니다.

* **--컨테이너 이름**: 백업된 원본 파일 공유를 호스팅하는 저장소 계정의 이름입니다. 컨테이너의 이름이나 친숙한 이름을 검색하려면 [az 백업 컨테이너 목록](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) 명령을 사용합니다.
* **--항목 이름**: 복원 작업에 사용할 백업된 원본 파일 공유의 이름입니다. 백업된 항목의 이름이나 친숙한 이름을 검색하려면 [az 백업 항목 목록](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) 명령을 사용합니다.

복구할 항목에 대해 다음 매개 변수를 지정합니다.

* **SourceFilePath**: 파일 공유 내에서 문자열로 복원할 파일의 절대 경로입니다. 이 경로는 AZ 스토리지 [파일 다운로드](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-download) 또는 [AZ 스토리지 파일 쇼](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-show) CLI 명령에 사용되는 것과 동일한 경로이다.
* **SourceFileType**: 디렉터리 또는 파일을 선택할지 여부를 선택합니다. **디렉터리** 또는 **파일**을 수락합니다.
* **해결충돌**: 복원된 데이터와 충돌이 있는 경우 명령을 수행합니다. **덮어쓰기** 또는 **건너뛰기**를 수락합니다.

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>개별 파일 또는 폴더를 원래 위치로 복원

az [백업 복원 복원 을](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) 사용하여 복원 azurefiles cmdlet 을 *원래로* 설정하여 원래 위치로 설정하여 특정 파일 이나 폴더를 원래 위치로 복원합니다.

다음 예제에서는 원래 위치에 *RestoreTest.txt* 파일을 복원합니다. *azurefiles*

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

출력의 **Name** 특성은 복원 작업에 대한 백업 서비스에서 만든 작업의 이름에 해당합니다. 작업의 상태를 추적하려면 [az 백업 작업 표시](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet을 사용합니다.

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>개별 파일 또는 폴더를 대체 위치로 복원

특정 파일이나 폴더를 대체 위치로 복원하려면 [az 백업 복원 복원 복원-azurefiles](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) cmdlet을 대체 *로케이션으로* 설정하고 다음 대상 관련 매개 변수를 지정합니다.

* **--대상 저장소 계정**: 백업된 콘텐츠가 복원되는 저장소 계정입니다. 대상 스토리지 계정은 자격 증명 모음과 동일한 위치에 있어야 합니다.
* **--대상 파일 공유**: 백업된 콘텐츠가 복원되는 대상 저장소 계정 내의 파일 공유입니다.
* **--대상 폴더**: 데이터가 복원되는 파일 공유 아래의 폴더입니다. 백업된 콘텐츠를 루트 폴더로 복원하려면 대상 폴더의 값을 빈 문자열로 지정합니다.

다음 예제에서는 원래 *azurefiles* 파일 공유에 있는 *RestoreTest.txt* 파일을 대체 *restoredata* 위치로 복원합니다. *azurefiles1* *afaccount1*

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

출력의 **Name** 특성은 복원 작업에 대한 백업 서비스에서 만든 작업의 이름에 해당합니다. 작업의 상태를 추적하려면 [az 백업 작업 표시](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet을 사용합니다.

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>여러 파일 또는 폴더를 원본 또는 대체 위치로 복원

여러 항목에 대한 복원을 수행하려면 **복원하려는** 모든 파일 또는 폴더의 **공백 분리** 경로로 소스 파일 경로 매개 변수의 값을 전달합니다.

다음 예제에서는 *Restore.txt* 및 *AFS 테스트 Report.docx* 파일을 원래 위치에 복원합니다.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932889937058317910 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore Test.txt" "AFS Testing Report.docx" --resolve-conflict overwrite  --out table
```

다음과 유사하게 출력됩니다.

```output
Name                                          ResourceGroup
------------------------------------          ---------------
649b0c14-4a94-4945-995a-19e2aace0305          azurefiles
```

출력의 **Name** 특성은 복원 작업에 대한 백업 서비스에서 만든 작업의 이름에 해당합니다. 작업의 상태를 추적하려면 [az 백업 작업 표시](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet을 사용합니다.

여러 항목을 대체 위치로 복원하려면 [개별 파일 또는 폴더 복원](#restore-individual-files-or-folders-to-an-alternate-location) 섹션에서 설명한 대로 대상 관련 매개 변수를 대체 위치로 지정하여 위의 명령을 사용합니다.

## <a name="next-steps"></a>다음 단계

[Azure CLI를](manage-afs-backup-cli.md)사용하여 Azure 파일 공유 백업을 관리하는 방법에 대해 알아봅니다.
