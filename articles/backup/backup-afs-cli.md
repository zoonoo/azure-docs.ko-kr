---
title: Azure CLI를 통해 Azure 파일 공유 백업
description: Azure CLI를 사용하여 복구 서비스 자격 증명 모음에서 Azure 파일 공유를 백업하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ff1d8c6245521d2d0262b0440177d65713058742
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844044"
---
# <a name="back-up-azure-file-shares-with-cli"></a>CLI를 통해 Azure 파일 공유 백업

CLI(Azure 명령줄 인터페이스)는 Azure 리소스 관리를 위한 명령줄 환경을 제공합니다. Azure 리소스를 사용하도록 사용자 지정 자동화를 빌드하는 데 유용한 도구입니다. 이 문서에서는 Azure CLI를 사용하여 Azure 파일 공유를 백업하는 방법에 대해 자세히 설명합니다. [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation)을 사용하거나 [Azure Portal](backup-afs.md)에서 이 단계를 수행할 수도 있습니다.

이 자습서의 끝으로 Azure CLI를 사용하여 아래 작업을 수행하는 방법을 배웁니다.

* 복구 서비스 자격 증명 모음 만들기
* Azure 파일 공유에 대한 백업 사용
* 파일 공유에 대한 온디맨드 백업 트리거

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하고 사용하려는 경우 Azure CLI 버전 2.0.18 이상을 사용해야 합니다. CLI 버전을 찾으려면 `run az --version`. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요.

## <a name="create-a-recovery-services-vault"></a>복구 서비스 볼트 만들기

복구 서비스 자격 증명 모음은 모든 백업 항목에서 통합된 보기 및 관리 기능을 제공하는 엔터티입니다. 보호된 리소스에 대한 백업 작업이 실행될 때 Recovery Services 자격 증명 모음 내에 복구 지점을 만듭니다. 이러한 복구 지점 중 하나를 사용하여 지정된 특정 시점으로 데이터를 복원할 수 있습니다.

다음 단계에 따라 복구 서비스 자격 증명 모음을 만듭니다.

1. 볼트는 리소스 그룹에 배치됩니다. 기존 리소스 그룹이 없는 경우 [az 그룹이 만듭니다.](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) 이 자습서에서는 미국 동부 지역에 새 리소스 그룹 *azurefiles를* 만듭니다.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

2. az [백업 볼트 를](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-create) 사용하여 cmdlet을 만들어 볼트를 만듭니다. 리소스 그룹에 사용된 동일한 위치를 자격 증명 모음에도 지정합니다.

    다음 예제에서는 미국 동부 지역에서 *azurefilesvault라는* 복구 서비스 볼트를 만듭니다.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

3. 볼트 저장소에 사용할 중복 유형을 지정합니다. [로컬 중복 스토리지](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs) 또는 [지역 중복 스토리지](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)를 사용할 수 있습니다.

    다음 예제는 [az 백업 볼트 백업 속성 세트](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set) cmdlet을 사용하여 *azurefilesvault에* 대한 저장소 중복 옵션을 **지오레디던트로** 설정합니다.

    ```azurecli-interactive
    az backup vault backup-properties set --name azurefilesvault --resource-group azurefiles --backup-storage-redundancy Georedundant
    ```

    볼트가 성공적으로 만들어졌는지 확인하려면 az [백업 볼트 표시](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-show) cmdlet을 사용하여 볼트에 대한 세부 정보를 얻을 수 있습니다. 다음 예제에서는 위의 단계에서 만든 *azurefilesvault의* 세부 정보를 표시 합니다.

    ```azurecli-interactive
    az backup vault show --name azurefilesvault --resource-group azurefiles --output table
    ```

    출력은 다음 응답과 유사합니다.

    ```output
    Location     Name               ResourceGroup
    ----------   ---------------    ---------------
    eastus       azurefilesvault    azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Azure 파일 공유에 대한 백업 사용

이 섹션에서는 백업을 구성하려는 Azure 파일 공유가 이미 있다고 가정합니다. 없는 경우 az 저장소 공유 만들기 명령을 사용하여 Azure 파일 [공유를](https://docs.microsoft.com/cli/azure/storage/share?view=azure-cli-latest#az-storage-share-create) 만듭니다.

파일 공유에 대한 백업을 사용하려면 백업 작업이 실행되는 시기와 복구 지점이 저장되는 길이를 정의하는 보호 정책을 만들어야 합니다. az 백업 정책 만들기 cmdlet을 사용하여 [백업 정책을 만들](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) 수 있습니다.

다음 예제에서는 [az 백업 보호 사용 azurefileshare](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurefileshare) cmdlet 을 사용 하 여 *일정 1* 백업 정책을 사용 하 여 *afsaccount* 저장소 계정에서 *azurefiles* 파일 공유에 대 한 백업을 사용 하 여 합니다.

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

출력의 **Name** 특성은 **활성화 백업** 작업에 대한 백업 서비스에서 만든 작업의 이름과 일치합니다. 작업의 상태를 추적하려면 az [백업 작업 표시](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet을 사용합니다.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>파일 공유를 위한 온디맨드 백업 트리거

백업 정책이 예약된 시간에 작업을 실행할 때까지 기다리지 않고 파일 공유에 대한 온디맨드 백업을 트리거하려면 [az 백업 보호 백업 지금](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-backup-now) cmdlet을 사용합니다.

온디맨드 백업을 트리거하려면 다음 매개 변수를 정의해야 합니다.

* **--컨테이너 이름은** 파일 공유를 호스팅하는 저장소 계정의 이름입니다. 컨테이너의 **이름이나** **친숙한 이름을** 검색하려면 [az 백업 컨테이너 목록](/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) 명령을 사용합니다.
* **--항목 이름은** 주문형 백업을 트리거하려는 파일 공유의 이름입니다. 백업된 항목의 **이름이나** **친숙한 이름을** 검색하려면 [az 백업 항목 목록](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) 명령을 사용합니다.
* **--유지-때까지** 복구 지점을 유지하려는 날짜까지 날짜를 지정합니다. 값은 UTC 시간 형식(dd-mm-yyyy)으로 설정해야 합니다.

다음 예제에서는 *afsaccount* 저장소 계정의 *azuresfiles* 파일 공유에 대 한 주문형 백업을 트리거합니다 *20-01-2020까지*보존.

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

출력의 **Name** 특성은 "주문형 백업" 작업에 대한 백업 서비스에서 만든 작업의 이름에 해당합니다. 작업의 상태를 추적하려면 [az 백업 작업 표시](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet을 사용합니다.

## <a name="next-steps"></a>다음 단계

* [CLI를 사용하여 Azure 파일 공유를 복원하는](restore-afs-cli.md) 방법 알아보기
* [CLI를 사용하여 Azure 파일 공유 ackups를 관리하는](manage-afs-backup-cli.md) 방법 알아보기
