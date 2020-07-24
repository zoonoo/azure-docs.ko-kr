---
title: Azure HPC 캐시 저장소 대상 업데이트
description: Azure HPC 캐시 저장소 대상을 편집 하는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/02/2020
ms.author: v-erkel
ms.openlocfilehash: f11e12c4f30977514e04b09c7e1c3012eb7888a7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092459"
---
# <a name="edit-storage-targets"></a>스토리지 대상 편집

캐시의 **저장소** 대상 포털 페이지에서 또는 Azure CLI를 사용 하 여 저장소 대상을 제거 하거나 수정할 수 있습니다.

> [!TIP]
> [AZURE HPC 캐시 관리 비디오](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) 는 Azure Portal에서 저장소 대상을 편집 하는 방법을 보여 줍니다.

## <a name="remove-a-storage-target"></a>저장소 대상 제거

### <a name="portal"></a>[포털](#tab/azure-portal)

저장소 대상을 제거 하려면 목록에서 해당 대상을 선택 하 고 **삭제** 단추를 클릭 합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

[Az hpc-cache storage-target remove](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-remove) 를 사용 하 여 캐시에서 저장소 대상을 삭제 합니다.

```azurecli
$ az hpc-cache storage-target remove --resource-group cache-rg --cache-name doc-cache0629 --name blob1

{- Finished ..
  "endTime": "2020-07-09T21:45:06.1631571+00:00",
  "name": "2f95eac1-aded-4860-b19c-3f089531a7ec",
  "startTime": "2020-07-09T21:43:38.5461495+00:00",
  "status": "Succeeded"
}
```

---

이 작업은이 Azure HPC 캐시 시스템과의 저장소 대상 연결을 제거 하지만 백 엔드 저장소 시스템은 변경 하지 않습니다. 예를 들어 Azure Blob 저장소 컨테이너를 사용 하는 경우 캐시에서 컨테이너와 해당 콘텐츠를 삭제 한 후에도 컨테이너와 해당 콘텐츠는 계속 존재 합니다. 컨테이너를 다른 Azure HPC 캐시에 추가 하거나,이 캐시에 다시 추가 하거나, Azure Portal를 사용 하 여 삭제할 수 있습니다.

저장소 대상이 제거 되기 전에 캐시에 저장 된 모든 파일 변경 내용이 백 엔드 저장소 시스템에 기록 됩니다. 변경 된 많은 데이터가 캐시에 있는 경우이 프로세스는 한 시간 이상 걸릴 수 있습니다.

## <a name="update-storage-targets"></a>저장소 대상 업데이트

저장소 대상을 편집 하 여 일부 속성을 수정할 수 있습니다. 다른 유형의 저장소에 대해 서로 다른 속성을 편집할 수 있습니다.

* Blob 저장소 대상의 경우 네임 스페이스 경로를 변경할 수 있습니다.

* NFS 저장소 대상의 경우 다음 속성을 변경할 수 있습니다.

  * 네임 스페이스 경로
  * 사용 모델
  * 내보내기
  * 하위 디렉터리 내보내기

저장소 대상의 이름, 유형 또는 백 엔드 저장소 시스템 (Blob 컨테이너 또는 NFS 호스트 이름/i p 주소)은 편집할 수 없습니다. 이러한 속성을 변경 해야 하는 경우 저장소 대상을 삭제 하 고 새 값으로 대체를 만듭니다.

Azure Portal에서 저장소 대상 이름을 클릭 하 고 세부 정보 페이지를 열어 편집할 수 있는 필드를 확인할 수 있습니다. Azure CLI를 사용 하 여 저장소 대상을 수정할 수도 있습니다.

![NFS 저장소 대상에 대 한 편집 페이지의 스크린샷](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>NFS 저장소 대상 업데이트

NFS 저장소 대상의 경우 여러 속성을 업데이트할 수 있습니다. 예제 편집 페이지는 위의 스크린샷을 참조 하세요.

* **사용 모델** -사용 모델은 캐시가 데이터를 보존 하는 방법에 영향을 줍니다. 자세히 알아보려면 [사용 모델 선택](hpc-cache-add-storage.md#choose-a-usage-model) 을 참조 하세요.
* **가상 네임 스페이스 경로** -클라이언트가이 저장소 대상을 탑재 하는 데 사용 하는 경로입니다. 자세한 내용은 [집계 된 네임 스페이스 계획을](hpc-cache-namespace.md) 참조 하세요.
* **NFS 내보내기 경로** -이 네임 스페이스 경로에 사용할 저장소 시스템 내보내기
* **하위 디렉터리 경로** -이 네임 스페이스 경로와 연결할 하위 디렉터리 (내보내기 아래)입니다. 하위 디렉터리를 지정 하지 않아도 되는 경우이 필드를 비워 둡니다.

각 네임 스페이스 경로에는 내보내기와 하위 디렉터리의 고유한 조합이 필요 합니다. 즉, 백 엔드 저장소 시스템에서 정확히 동일한 디렉터리에 두 개의 다른 클라이언트 연결 경로를 만들 수 없습니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

변경 후에는 **확인** 을 클릭 하 여 저장소 대상을 업데이트 하거나 **취소** 를 클릭 하 여 변경 내용을 취소 합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

[Az nfs-storage-target](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target) 명령을 사용 하 여 저장소 대상에 대 한 사용 모델, 가상 네임 스페이스 경로 및 nfs 내보내기 또는 하위 디렉터리 값을 변경 합니다.

* 사용 모델을 변경 하려면 옵션을 사용 ``--nfs3-usage-model`` 합니다. 예: ``--nfs3-usage-model WRITE_WORKLOAD_15``

* 네임 스페이스 경로, 내보내기 또는 내보내기 하위 디렉터리를 변경 하려면 옵션을 사용 ``--junction`` 합니다.

  ``--junction``매개 변수는 다음 값을 사용 합니다.

  * ``namespace-path``-클라이언트 연결 가상 파일 경로
  * ``nfs-export``-클라이언트 연결 경로와 연결할 저장소 시스템 내보내기
  * ``target-path``(선택 사항)-필요한 경우 내보내기의 하위 디렉터리입니다.

  예: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

모든 업데이트 명령에는 캐시 이름, 저장소 대상 이름 및 리소스 그룹이 필요 합니다.

명령 예: <!-- having problem testing this -->

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
    --name rivernfs0 --resource-group doc-rg0619 \
    --nfs3-usage-model READ_HEAVY_INFREQ
```

캐시가 중지 되었거나 정상 상태가 아니면 캐시가 정상 상태가 된 후 업데이트가 적용 됩니다.

---

## <a name="update-an-azure-blob-storage-target"></a>Azure Blob 저장소 대상 업데이트

Blob 저장소 대상의 경우 가상 네임 스페이스 경로를 수정할 수 있습니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

Blob 저장소 대상의 세부 정보 페이지를 사용 하 여 가상 네임 스페이스 경로를 수정할 수 있습니다.

![blob 저장소 대상에 대 한 편집 페이지의 스크린샷](media/hpc-cache-edit-storage-blob.png)

완료 되 면 **확인** 을 클릭 하 여 저장소 대상을 업데이트 하거나 **취소** 를 클릭 하 여 변경 내용을 취소 합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

[Az hpc-cache blob-storage-target update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update) 를 사용 하 여 대상의 네임 스페이스 경로를 업데이트 합니다.

```azurecli
az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --storage-account "/subscriptions/<subscription_ID>/resourceGroups/erinazcli/providers/Microsoft.Storage/storageAccounts/rg"  \
    --container-name "container-name" --virtual-namespace-path "/new-path"
```

캐시가 중지 되었거나 정상 상태가 아니면 캐시가 정상 상태가 된 후 업데이트가 적용 됩니다.

---

## <a name="next-steps"></a>다음 단계

* 이러한 옵션에 대 한 자세한 내용은 [저장소 대상 추가](hpc-cache-add-storage.md) 를 참조 하세요.
* 가상 경로 사용에 대 한 자세한 팁 [은 집계 된 네임 스페이스 계획](hpc-cache-namespace.md) 을 참조 하세요.
