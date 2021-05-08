---
title: Azure CLI를 사용하여 Azure 파일 공유 백업
description: Azure CLI를 사용하여 Recovery Services 자격 증명 모음에서 Azure 파일 공유를 백업하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: a5f7472c511a5a50415a6ceb47497dd6f4f1e60b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773624"
---
# <a name="back-up-azure-file-shares-with-azure-cli"></a>Azure CLI를 사용하여 Azure 파일 공유 백업

Azure CLI(명령줄 인터페이스)는 Azure 리소스 관리를 위한 명령줄 환경을 제공합니다. Azure 리소스를 사용하는 사용자 지정 자동화를 빌드하기 위한 좋은 도구입니다. 이 문서에서는 Azure CLI를 사용하여 Azure 파일 공유를 백업하는 방법을 자세히 설명합니다. [Azure PowerShell](./backup-azure-afs-automation.md)을 사용하거나 [Azure Portal](backup-afs.md)에서 이 단계를 수행할 수도 있습니다.

이 자습서의 끝 부분에서는 Azure CLI를 사용하여 아래 작업을 수행하는 방법을 알아봅니다.

* Recovery Services 자격 증명 모음 만들기
* Azure 파일 공유에 대한 백업 사용
* 파일 공유에 대한 주문형 백업 트리거

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - 이 자습서에는 Azure CLI 버전 2.0.18 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

Recovery Services 자격 증명 모음은 모든 백업 항목에서 통합 보기 및 관리 기능을 제공하는 엔터티입니다. 보호된 리소스에 대한 백업 작업이 실행될 때 Recovery Services 자격 증명 모음 내에 복구 지점을 만듭니다. 이러한 복구 지점 중 하나를 사용하여 지정된 특정 시점으로 데이터를 복원할 수 있습니다.

다음 단계에 따라 Recovery Services 자격 증명 모음을 만들 수 있습니다.

1. 자격 증명 모음은 리소스 그룹에 배치됩니다. 기존 리소스 그룹이 없는 경우 [az group create](/cli/azure/group#az_group_create)를 사용하여 새 리소스 그룹을 만듭니다. 이 자습서에서는 미국 동부 지역에 새 리소스 그룹 *azurefiles* 를 만듭니다.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

1. [az backup vault create](/cli/azure/backup/vault#az_backup_vault_create) cmdlet을 사용하여 자격 증명 모음을 만듭니다. 리소스 그룹에 사용된 동일한 위치를 자격 증명 모음에도 지정합니다.

    다음 예는 미국 동부 지역에 *azurefilesvault* 라는 Recovery Services 자격 증명 모음을 만듭니다.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Azure 파일 공유에 대한 백업 사용

이 섹션에서는 백업을 구성하려는 Azure 파일 공유가 이미 있다고 가정합니다. 없는 경우, [az storage share create](/cli/azure/storage/share#az_storage_share_create) 명령을 사용하여 Azure 파일 공유를 만듭니다.

파일 공유에 대한 백업을 사용하도록 설정하려면 백업 작업이 실행되는 시기와 복구 지점이 저장되는 기간을 정의하는 보호 정책을 만들어야 합니다. [az backup policy create](/cli/azure/backup/policy#az_backup_policy_create) cmdlet을 사용하여 백업 정책을 만들 수 있습니다.

다음 예에서는 [az backup protection enable-for-azurefileshare](/cli/azure/backup/protection#az_backup_protection_enable_for_azurefileshare) cmdlet을 사용하여 *스케줄 1* 백업 정책을 통해 *afsaccount* 스토리지 계정의 *azurefiles* 파일 공유에 대한 백업을 사용하도록 설정합니다.

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

출력의 **Name** 특성은 **백업 사용** 작업을 위해 백업 서비스에서 만든 작업의 이름에 해당합니다. 작업 상태를 추적하려면 [az backup job show](/cli/azure/backup/job#az_backup_job_show) cmdlet을 사용합니다.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>파일 공유에 대한 주문형 백업 트리거

백업 정책이 예약된 시간에 작업을 실행할 때까지 기다리지 않고 파일 공유에 대한 주문형 백업을 트리거하려면 [az backup protection backup-now](/cli/azure/backup/protection#az_backup_protection_backup_now) cmdlet을 사용합니다.

요청 시 백업을 트리거하기 위해 다음 매개 변수를 정의해야 합니다.

* **--container-name** 은 파일 공유를 호스팅하는 스토리지 계정의 이름입니다. 컨테이너의 **이름** 또는 **식별 이름** 을 검색하려면 [az backup container list](/cli/azure/backup/container#az_backup_container_list) 명령을 사용합니다.
* **--item-name** 은 주문형 백업을 트리거할 파일 공유의 이름입니다. 백업된 항목의 **이름** 또는 **식별 이름** 을 검색하려면 [az backup item list](/cli/azure/backup/item#az_backup_item_list) 명령을 사용합니다.
* **--retain-until** 은 복구 지점을 유지할 때까지의 날짜를 지정합니다. 값은 UTC 시간 형식(dd-mm-yyyy)으로 설정해야 합니다.

다음 예는 *2020년 1월 20일* 까지 보존된 *afsaccount* 스토리지 계정의 *azurefiles* 파일 공유에 대한 주문형 백업을 트리거합니다.

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

출력의 **Name** 특성은 "주문형 백업" 작업을 위해 백업 서비스에서 만든 작업의 ​​이름에 해당합니다. 작업 상태를 추적하려면 [az backup job show](/cli/azure/backup/job#az_backup_job_show) cmdlet을 사용합니다.

## <a name="next-steps"></a>다음 단계

* [CLI를 사용하여 Azure 파일 공유 복원](restore-afs-cli.md) 방법 알아보기
* [CLI를 사용하여 Azure 파일 공유 백업 관리](manage-afs-backup-cli.md) 방법 알아보기
