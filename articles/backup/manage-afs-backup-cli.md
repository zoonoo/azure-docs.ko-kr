---
title: Azure CLI를 사용하여 Azure 파일 공유 백업 관리
description: Azure CLI를 사용하여 Azure Backup으로 백업한 Azure 파일 공유를 관리하고 모니터링하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: e389f5cde12734ef4bf0be4ecfba69ba33f5e030
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773606"
---
# <a name="manage-azure-file-share-backups-with-the-azure-cli"></a>Azure CLI를 사용하여 Azure 파일 공유 백업 관리

Azure CLI는 Azure 리소스를 관리하는 Azure의 명령줄 환경을 제공합니다. Azure 리소스를 사용하기 위해 사용자 지정 자동화를 빌드하는 데 좋은 도구입니다. 이 문서에서는 [Azure Backup](./backup-overview.md)에 의해 백업된 Azure 파일 공유를 관리하고 모니터링하기 위한 태스크를 수행하는 방법을 설명합니다. [Azure Portal](https://portal.azure.com/)에서 이러한 단계를 수행할 수도 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 [Azure Backup](./backup-overview.md)으로 백업된 Azure 파일 공유가 이미 있다고 가정합니다. 없으면 [CLI를 사용한 Azure 파일 공유 백업](backup-afs-cli.md)을 참조하여 파일 공유에 대한 백업을 구성합니다. 이 문서에서는 다음 리소스를 사용합니다.
   -  **리소스 그룹**: *azurefiles*
   -  **RecoveryServicesVault**: *azurefilesvault*
   -  **스토리지 계정**: *afsaccount*
   -  **파일 공유**: *azurefiles*
  
  [!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
   - 이 자습서에는 Azure CLI 버전 2.0.18 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="monitor-jobs"></a>작업 모니터링

복원 작업이 트리거되거나 작업이 복구되면 백업 서비스에서 추적 작업을 만듭니다. 완료된 작업 또는 현재 실행 중인 작업을 모니터링하려면 [az backup job list](/cli/azure/backup/job#az_backup_job_list) cmdlet을 사용합니다. CLI를 사용하면 또한 [현재 실행 중인 작업을 일시 중단](/cli/azure/backup/job#az_backup_job_stop)하거나 [작업이 완료될 때까지 대기](/cli/azure/backup/job#az_backup_job_wait)할 수 있습니다.

다음 예는 *azurefilesvault* Recovery Services 자격 증명 모음에 대한 백업 작업의 상태를 표시합니다.

```azurecli-interactive
az backup job list --resource-group azurefiles --vault-name azurefilesvault
```

```output
[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "location": null,
    "name": "d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "properties": {
      "actionsInfo": null,
      "activityId": "3cef43ed-0af4-43e2-b9cb-1322c496ccb4",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:29.718011",
      "endTime": "2020-01-13T08:05:29.180606+00:00",
      "entityFriendlyName": "azurefiles",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.462595+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "location": null,
    "name": "1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "properties": {
      "actionsInfo": null,
      "activityId": "2663449c-94f1-4735-aaf9-5bb991e7e00c",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:28.145216",
      "endTime": "2020-01-13T08:05:27.519826+00:00",
      "entityFriendlyName": "azurefilesresource",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.374610+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  }
]
```

## <a name="modify-policy"></a>정책 수정

[Az backup item set-policy](/cli/azure/backup/item#az_backup_item_set_policy)를 사용하여 백업 빈도 또는 보존 범위를 변경하는 백업 정책을 수정할 수 있습니다.

정책을 변경하려면 다음 매개 변수를 정의합니다.

* **--container-name** 은 파일 공유를 호스트하는 스토리지 계정의 이름입니다. 컨테이너의 **이름** 또는 **식별 이름** 을 검색하려면 [az backup container list](/cli/azure/backup/container#az_backup_container_list) 명령을 사용합니다.
* **--name**: 정책을 변경하려는 파일 공유의 이름입니다. 백업된 항목의 **이름** 또는 **식별 이름** 을 검색하려면 [az backup item list](/cli/azure/backup/item#az_backup_item_list) 명령을 사용합니다.
* **--policy-name**: 파일 공유에 대해 설정하려는 백업 정책의 이름입니다. [az 백업 정책](/cli/azure/backup/policy#az_backup_policy_list)을 사용하여 자격 증명 모음에 대한 모든 정책을 볼 수 있습니다.

다음 예에서는 *afsaccount* 저장소 계정에 있는 *azurefiles* 파일 공유에 대한 *schedule2* 백업 정책을 설정합니다.

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

또한 컨테이너 및 항목에 익숙한 이름을 사용하여 다음과 같은 두 개의 추가 매개 변수를 제공하면 이전 명령을 실행할 수도 있습니다.

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

출력의 **Name** 특성은 정책 변경 작업을 위해 백업 서비스에서 만든 작업의 이름에 해당합니다. 작업 상태를 추적하려면 [az backup job show](/cli/azure/backup/job#az_backup_job_show) cmdlet을 사용합니다.

## <a name="stop-protection-on-a-file-share"></a>파일 공유에 대한 보호 중지

Azure 파일 공유를 중지하는 두 가지 방법이 있습니다.

* 미래의 모든 백업 작업을 중지하고 모든 복구 지점을 삭제합니다.
* 이후의 모든 백업 작업은 중지하지만 복구 지점은 그대로 유지합니다.

복구 지점을 유지할 경우 Azure Backup에서 만드는 기본 스냅샷이 보관되므로 비용이 발생할 수 있습니다. 복구 지점을 유지할 경우 나중에 원할 때 파일 공유를 복원할 수 있다는 장점이 있습니다. 복구 지점을 유지하는 비용에 대한 자세한 내용은 [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/storage/files)를 참조하세요. 모든 복구 지점을 삭제하기로 선택하면 파일 공유를 복원할 수 없습니다.

파일 공유에 대한 보호를 중지하려면 다음 매개 변수를 정의합니다.

* **--container-name** 은 파일 공유를 호스트하는 스토리지 계정의 이름입니다. 컨테이너의 **이름** 또는 **식별 이름** 을 검색하려면 [az backup container list](/cli/azure/backup/container#az_backup_container_list) 명령을 사용합니다.
* **--name**: 보호를 중지하려는 파일 공유의 이름입니다. 백업된 항목의 **이름** 또는 **식별 이름** 을 검색하려면 [az backup item list](/cli/azure/backup/item#az_backup_item_list) 명령을 사용합니다.

### <a name="stop-protection-and-retain-recovery-points"></a>보호 중지 및 복구 지점 보존

데이터 보존 없이 보호를 중지하려면 [az backup protection disable](/cli/azure/backup/protection#az_backup_protection_disable) cmdlet을 사용합니다.

다음 예에서는 *azurefiles* 파일 공유에 대한 보호를 중지하지만 모든 복구 지점을 유지합니다.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

또한 컨테이너 및 항목에 익숙한 이름을 사용하여 다음과 같은 두 개의 추가 매개 변수를 제공하면 이전 명령을 실행할 수 있습니다.

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

출력의 **Name** 특성은 보호 중지 작업을 위해 백업 서비스에서 만든 작업의 이름에 해당합니다. 작업 상태를 추적하려면 [az backup job show](/cli/azure/backup/job#az_backup_job_show) cmdlet을 사용합니다.

### <a name="stop-protection-without-retaining-recovery-points"></a>복구 지점을 유지하지 않고 보호 중지

복구 지점을 유지하지 않고 보호를 중지하려면 [az backup protection disable](/cli/azure/backup/protection#az_backup_protection_disable) cmdlet을 사용하여 **delete-backup-data** 옵션을 **true** 로 설정합니다.

다음 예제에서는 복구 지점을 유지하지 않고 *azurefiles* 파일 공유에 대한 보호를 중지합니다.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

또한 컨테이너 및 항목에 익숙한 이름을 사용하여 다음과 같은 두 개의 추가 매개 변수를 제공하면 이전 명령을 실행할 수 있습니다.

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>파일 공유에 대한 보호 다시 시작

Azure 파일 공유에 대한 보호를 중지했지만 복구 지점을 유지하는 경우 나중에 보호를 다시 시작할 수 있습니다. 복구 지점을 유지하지 않으면 보호를 계속할 수 없습니다.

파일 공유에 대한 보호를 계속하려면 다음 매개 변수를 정의합니다.

* **--container-name** 은 파일 공유를 호스트하는 스토리지 계정의 이름입니다. 컨테이너의 **이름** 또는 **식별 이름** 을 검색하려면 [az backup container list](/cli/azure/backup/container#az_backup_container_list) 명령을 사용합니다.
* **--name**: 보호를 계속하려는 파일 공유의 이름입니다. 백업된 항목의 **이름** 또는 **식별 이름** 을 검색하려면 [az backup item list](/cli/azure/backup/item#az_backup_item_list) 명령을 사용합니다.
* **--policy-name**: 파일 공유에 대한 보호를 계속하려는 백업 정책의 이름입니다.

다음 예에서는 [az backup protection resume](/cli/azure/backup/protection#az_backup_protection_resume) cmdlet을 사용하여 *schedule1* 백업 정책으로 *azurefiles* 파일 공유에 대한 보호를 계속합니다.

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

또한 컨테이너 및 항목에 익숙한 이름을 사용하여 다음과 같은 두 개의 추가 매개 변수를 제공하면 이전 명령을 실행할 수 있습니다.

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

출력의 **Name** 특성은 보호 재개 작업을 위해 백업 서비스에서 만든 작업의 이름에 해당합니다. 작업 상태를 추적하려면 [az backup job show](/cli/azure/backup/job#az_backup_job_show) cmdlet을 사용합니다.

## <a name="unregister-a-storage-account"></a>스토리지 계정 등록 취소

다른 Recovery Services 자격 증명 모음을 사용하여 특정 스토리지 계정에서 파일 공유를 보호하려면 먼저 해당 스토리지 계정에서 [모든 파일 공유에 대한 보호를 중지](#stop-protection-on-a-file-share)합니다. 그런 다음 현재 보호에 사용되는 Recovery Services 자격 증명 모음에서 계정을 등록 취소합니다.

스토리지 계정의 등록을 취소하려면 컨테이너 이름을 제공해야 합니다. 컨테이너의 **이름** 또는 **식별 이름** 을 검색하려면 [az backup container list](/cli/azure/backup/container#az_backup_container_list) 명령을 사용합니다.

다음 예제에서는 [az backup container unregister](/cli/azure/backup/container#az_backup_container_unregister) cmdlet을 사용하여 *azurefilesvault* 에서 *afsaccount* 스토리지 계정의 등록을 취소합니다.

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

또한 컨테이너에 대한 식별 이름을 사용하여 다음과 같은 추가 매개 변수를 제공하면 이전 cmdlet을 실행할 수 있습니다.

* **--backup-management-type**: *azurestorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure 파일 공유 백업 문제 해결](troubleshoot-azure-files.md)을 참조하세요.
