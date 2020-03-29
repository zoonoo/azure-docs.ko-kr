---
title: Azure CLI를 사용 하 고 Azure 파일 공유 백업 관리
description: Azure CLI를 사용하여 Azure Backup에서 백업하는 Azure 파일 공유를 관리하고 모니터링하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 44a49913abd99b285397b8b78ad9d4c0f9df52ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934886"
---
# <a name="manage-azure-file-share-backups-with-the-azure-cli"></a>Azure CLI를 사용 하 고 Azure 파일 공유 백업 관리

Azure CLI는 Azure 리소스 관리를 위한 명령줄 환경을 제공합니다. Azure 리소스를 사용하도록 사용자 지정 자동화를 빌드하는 데 유용한 도구입니다. 이 문서에서는 [Azure Backup에서](https://docs.microsoft.com/azure/backup/backup-overview)백업하는 Azure 파일 공유를 관리하고 모니터링하기 위한 작업을 수행하는 방법을 설명합니다. [Azure 포털](https://portal.azure.com/)을 통해 이러한 단계를 수행할 수도 있습니다. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하고 사용하려면 Azure CLI 버전 2.0.18 이상을 실행해야 합니다. CLI 버전을 찾으려면 `az --version`을 실행합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 Azure [백업](https://docs.microsoft.com/azure/backup/backup-overview)에 의해 백업 된 Azure 파일 공유가 이미 있다고 가정합니다. 없는 경우 [CLI를 사용하여 Azure 파일 공유 백업을](backup-afs-cli.md) 참조하여 파일 공유에 대한 백업을 구성합니다. 이 문서에서는 다음 리소스를 사용합니다.

* **리소스 그룹**: *azurefiles*
* **복구 서비스 볼트**: *azurefilesvault*
* **저장 계정**: *afsaccount*
* **파일 공유**: *azurefiles*

## <a name="monitor-jobs"></a>작업 모니터링

백업 또는 복원 작업을 트리거하면 백업 서비스가 추적을 위한 작업을 만듭니다. 완료된 작업 또는 현재 실행 중인 작업을 모니터링하려면 [az 백업 작업 목록](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) cmdlet을 사용합니다. CLI를 사용하면 현재 [실행 중인 작업을 일시 중단하거나](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop) [작업이 완료될 때까지 기다릴 수도](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait)있습니다.

다음 예제는 *azurefilesvault* 복구 서비스 볼트에 대한 백업 작업의 상태를 표시합니다.

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

[az 백업 항목 집합 정책을](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-set-policy)사용하여 백업 정책을 수정하여 백업 빈도 또는 보존 범위를 변경할 수 있습니다.

정책을 변경하려면 다음 매개 변수를 정의합니다.

* **--컨테이너 이름**: 파일 공유를 호스트하는 저장소 계정의 이름입니다. 컨테이너의 **이름이나** **친숙한 이름을** 검색하려면 [az 백업 컨테이너 목록](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) 명령을 사용합니다.
* **--name**: 정책을 변경하려는 파일 공유의 이름입니다. 백업된 항목의 **이름이나** **친숙한 이름을** 검색하려면 [az 백업 항목 목록](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) 명령을 사용합니다.
* **--정책 이름**: 파일 공유에 대해 설정하려는 백업 정책의 이름입니다. [az 백업 정책 목록을](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-list) 사용하여 볼트의 모든 정책을 볼 수 있습니다.

다음 예제에서는 *afsaccount* 저장소 계정에 있는 *azurefiles* 파일 공유에 대 한 *schedule2* 백업 정책을 설정 합니다.

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

컨테이너 및 항목에 대 한 친숙 한 이름을 사용 하 여 이전 명령을 실행할 수 있습니다 다음 두 개의 추가 매개 변수를 제공 하 여:

* **--백업 관리 유형**: *azurestorage*
* **--워크로드 유형**: *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

출력의 **Name** 특성은 변경 정책 작업에 대한 백업 서비스에서 만든 작업의 이름에 해당합니다. 작업의 상태를 추적하려면 [az 백업 작업 표시](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet을 사용합니다.

## <a name="stop-protection-on-a-file-share"></a>파일 공유에 대한 보호 중지

Azure 파일 공유를 중지하는 두 가지 방법이 있습니다.

* 이후의 모든 백업 작업을 중지하고 모든 복구 지점을 *삭제합니다.*
* 이후의 모든 백업 작업을 중지하고 복구 지점을 *남겨 둡니다.*

Azure Backup에서 만든 기본 스냅숏이 유지되므로 저장소에 복구 지점을 두는 것과 관련된 비용이 있을 수 있습니다. 복구 지점을 남기면 나중에 파일 공유를 복원할 수 있는 이점이 있습니다( 원하는 경우). 복구 지점을 떠나는 비용에 대한 자세한 내용은 [가격 세부 정보를](https://azure.microsoft.com/pricing/details/storage/files)참조하십시오. 모든 복구 지점을 삭제하도록 선택하면 파일 공유를 복원할 수 없습니다.

파일 공유에 대한 보호를 중지하려면 다음 매개 변수를 정의합니다.

* **--컨테이너 이름**: 파일 공유를 호스트하는 저장소 계정의 이름입니다. 컨테이너의 **이름이나** **친숙한 이름을** 검색하려면 [az 백업 컨테이너 목록](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) 명령을 사용합니다.
* **--항목 이름**: 보호를 중지하려는 파일 공유의 이름입니다. 백업된 항목의 **이름이나** **친숙한 이름을** 검색하려면 [az 백업 항목 목록](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) 명령을 사용합니다.

### <a name="stop-protection-and-retain-recovery-points"></a>보호 중지 및 복구 지점 유지

데이터를 유지하면서 보호를 중지하려면 [az 백업 보호가](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) cmdlet을 사용하지 않도록 설정합니다.

다음 예제는 *azurefiles* 파일 공유에 대한 보호를 중지하지만 모든 복구 지점을 유지합니다.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

컨테이너 및 항목에 대 한 친숙 한 이름을 사용 하 여 이전 명령을 실행할 수 있습니다 다음 두 개의 추가 매개 변수를 제공 하 여:

* **--백업 관리 유형**: *azurestorage*
* **--워크로드 유형**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

출력의 **Name** 특성은 중지 보호 작업에 대한 백업 서비스에서 만든 작업의 이름에 해당합니다. 작업의 상태를 추적하려면 [az 백업 작업 표시](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet을 사용합니다.

### <a name="stop-protection-without-retaining-recovery-points"></a>복구 지점을 유지하지 않고 보호 중지

복구 지점을 유지하지 않고 보호를 중지하려면 [az 백업 보호 비활성화](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) cmdlet을 사용하여 삭제 백업 **데이터** 옵션을 **true로**설정합니다.

다음 예제는 복구 지점을 유지하지 않고 *azurefiles* 파일 공유에 대한 보호를 중지합니다.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

컨테이너 및 항목에 대 한 친숙 한 이름을 사용 하 여 이전 명령을 실행할 수 있습니다 다음 두 개의 추가 매개 변수를 제공 하 여:

* **--백업 관리 유형**: *azurestorage*
* **--워크로드 유형**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>파일 공유에 대한 보호 다시 시작

Azure 파일 공유에 대한 보호를 중지했지만 복구 지점이 유지된 경우 나중에 보호를 다시 시작할 수 있습니다. 복구 지점을 유지하지 않으면 보호를 다시 시작할 수 없습니다.

파일 공유에 대한 보호를 다시 시작하려면 다음 매개 변수를 정의합니다.

* **--컨테이너 이름**: 파일 공유를 호스트하는 저장소 계정의 이름입니다. 컨테이너의 **이름이나** **친숙한 이름을** 검색하려면 [az 백업 컨테이너 목록](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) 명령을 사용합니다.
* **--항목 이름**: 보호를 다시 시작하려는 파일 공유의 이름입니다. 백업된 항목의 **이름이나** **친숙한 이름을** 검색하려면 [az 백업 항목 목록](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) 명령을 사용합니다.
* **--정책 이름**: 파일 공유에 대한 보호를 다시 시작하려는 백업 정책의 이름입니다.

다음 예제에서는 [az 백업 보호 이력서](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume) cmdlet을 사용하여 *schedule1* 백업 정책을 사용하여 *azurefiles* 파일 공유에 대한 보호를 다시 시작합니다.

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

컨테이너 및 항목에 대 한 친숙 한 이름을 사용 하 여 이전 명령을 실행할 수 있습니다 다음 두 개의 추가 매개 변수를 제공 하 여:

* **--백업 관리 유형**: *azurestorage*
* **--워크로드 유형**: *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

출력의 **Name** 특성은 이력서 보호 작업에 대한 백업 서비스에서 만든 작업의 이름에 해당합니다. 작업의 상태를 추적하려면 [az 백업 작업 표시](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet을 사용합니다.

## <a name="unregister-a-storage-account"></a>저장소 계정 등록 취소

다른 복구 서비스 자격 증명 모음을 사용하여 특정 저장소 계정의 파일 공유를 보호하려면 먼저 해당 저장소 계정의 [모든 파일 공유에 대한 보호를 중지합니다.](#stop-protection-on-a-file-share) 그런 다음 현재 보호에 사용되는 복구 서비스 자격 증명 모음에서 계정을 등록 취소합니다.

저장소 계정 등록을 취소하려면 컨테이너 이름을 제공해야 합니다. 컨테이너의 **이름이나** **친숙한 이름을** 검색하려면 [az 백업 컨테이너 목록](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) 명령을 사용합니다.

다음 예제는 az 백업 컨테이너 를 사용하여 *azurefilesvault에서* *afsaccount* 저장소 계정을 등록 취소합니다 cmdlet을 [등록 취소합니다.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-unregister)

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

다음 추가 매개 변수를 제공하여 컨테이너에 대한 친숙한 이름을 사용하여 이전 cmdlet을 실행할 수도 있습니다.

* **--백업 관리 유형**: *azurestorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure 파일 공유 백업](troubleshoot-azure-files.md)을 참조하세요.
