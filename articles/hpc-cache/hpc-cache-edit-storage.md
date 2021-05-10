---
title: Azure HPC Cache 스토리지 대상 업데이트
description: Azure HPC Cache 스토리지 대상을 편집하는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/10/2021
ms.author: v-erkel
ms.openlocfilehash: 0c505937d4adbe2596e91ed7269676e60ada8253
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104772592"
---
# <a name="edit-storage-targets"></a>스토리지 대상 편집

Azure Portal 또는 Azure CLI를 사용하여 스토리지 대상을 제거하거나 수정할 수 있습니다.

스토리지 유형에 따라 다음과 같은 스토리지 대상 값을 수정할 수 있습니다.

* Blob 스토리지 대상의 경우 네임스페이스 경로 및 액세스 정책을 변경할 수 있습니다.

* NFS 스토리지 대상의 경우 다음 값을 변경할 수 있습니다.

  * 네임스페이스 경로
  * 액세스 정책
  * 네임스페이스 경로와 연결된 스토리지 내보내기 또는 내보내기 하위 디렉터리
  * 사용 모델

* ADLS-NFS 스토리지 대상의 경우 네임스페이스 경로, 액세스 정책 및 사용 모델을 변경할 수 있습니다.

스토리지 대상의 이름, 유형 또는 백 엔드 스토리지 시스템(Blob 컨테이너 또는 NFS 호스트 이름/IP 주소)은 편집할 수 없습니다. 이러한 속성을 변경해야 하는 경우 스토리지 대상을 삭제하고 새 값으로 바꿉니다.

> [!TIP]
> [Azure HPC Cache 관리 비디오](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)는 Azure Portal에서 스토리지 대상을 편집하는 방법을 보여 줍니다.

## <a name="remove-a-storage-target"></a>스토리지 대상 제거

### <a name="portal"></a>[포털](#tab/azure-portal)

스토리지 대상을 제거하려면 **스토리지 대상** 페이지를 엽니다. 목록에서 스토리지 대상을 선택하고 **삭제** 단추를 클릭합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC Cache용 Azure CLI 설치](./az-cli-prerequisites.md).

[az hpc-cache storage-target remove](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-remove)를 사용하여 캐시에서 스토리지 대상을 삭제합니다.

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

스토리지 대상을 삭제하면 이 Azure HPC Cache 시스템과의 스토리지 시스템 연결이 제거되지만 백 엔드 스토리지 시스템은 변경되지 않습니다. 예를 들어, Azure Blob 스토리지 컨테이너를 사용하는 경우 캐시에서 컨테이너와 해당 콘텐츠를 삭제한 후에도 컨테이너와 해당 콘텐츠는 계속 존재합니다. 컨테이너를 다른 Azure HPC Cache에 추가하거나, 이 캐시에 다시 추가하거나, Azure Portal에서 삭제할 수 있습니다.

스토리지 대상이 제거되기 전에 캐시에 저장된 모든 파일 변경 내용이 백 엔드 스토리지 시스템에 기록됩니다. 변경된 많은 데이터가 캐시에 있는 경우 이 프로세스는 1시간 이상 걸릴 수 있습니다.

## <a name="change-a-blob-storage-targets-namespace-path"></a>Blob 스토리지 대상의 네임스페이스 경로 변경

네임스페이스 경로는 클라이언트가 이 스토리지 대상을 탑재하는 데 사용하는 경로입니다. 자세한 내용은 [집계형 네임스페이스 계획](hpc-cache-namespace.md) 및 [집계형 네임 스페이스 설정](add-namespace-paths.md)을 읽어보세요.

Azure Blob 스토리지 대상의 경우 네임스페이스 경로만 업데이트할 수 있습니다. Azure Portal 또는 Azure CLI를 사용하여 변경합니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure HPC Cache의 **네임스페이스** 페이지를 사용합니다. 네임스페이스 페이지는 [집계형 네임스페이스 설정](add-namespace-paths.md)문서에 좀 더 자세히 설명되어 있습니다.

변경할 경로의 이름을 클릭하고 표시되는 편집 창에서 새 경로를 만듭니다.

![Blob 네임스페이스 경로를 클릭한 이후의 네임스페이스 페이지 스크린샷 - 오른쪽의 창에 편집 필드가 표시됨](media/update-namespace-blob.png)

변경 후에는 **확인** 을 클릭하여 스토리지 대상을 업데이트하거나 **취소** 를 클릭하여 변경 내용을 취소합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC Cache용 Azure CLI 설치](./az-cli-prerequisites.md).

Azure CLI를 사용하여 Blob 스토리지 대상의 네임스페이스를 변경하려면 [az hpc-cache blob-storage-target update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update) 명령을 사용합니다. `--virtual-namespace-path` 값만 변경할 수 있습니다.

  ```azurecli
  az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --virtual-namespace-path "/new-path"
  ```

---

## <a name="update-an-nfs-storage-target"></a>NFS 스토리지 대상 업데이트

NFS 스토리지 대상의 경우 가상 네임스페이스 경로를 변경 또는 추가하고, 네임스페이스 경로가 가리키는 NFS 내보내기 또는 하위 디렉터리 값을 변경하고, 사용 모델을 변경할 수 있습니다.

일부 유형의 사용자 지정 DNS 설정으로 캐시의 스토리지 대상에도 해당 IP 주소를 새로 고치는 컨트롤이 있습니다. (이러한 종류의 구성은 드물게 사용됩니다.)

자세한 내용은 다음과 같습니다.

* [집계형 네임스페이스 값 변경](#change-aggregated-namespace-values)(가상 네임스페이스 경로, 액세스 정책, 내보내기 및 하위 디렉터리 내보내기)
* [사용 모델 변경](#change-the-usage-model)
* [DNS 새로 고침](#update-ip-address-custom-dns-configurations-only)

### <a name="change-aggregated-namespace-values"></a>집계형 네임스페이스 값 변경

Azure Portal 또는 Azure CLI를 사용하여 클라이언트 연결 네임스페이스 경로, 스토리지 내보내기 및 내보내기 하위 디렉터리(사용되는 경우)를 변경할 수 있습니다.

하나의 스토리지 대상에서 유효한 경로를 여러 개 만드는 방법에 대한 미리 알림이 필요한 경우 [NFS 네임스페이스 경로 추가](add-namespace-paths.md#nfs-namespace-paths)의 지침을 참조하세요.

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure HPC Cache의 **네임스페이스** 페이지를 사용하여 네임스페이스 값을 업데이트합니다. 이 페이지는 [집계형 네임스페이스 설정](add-namespace-paths.md) 문서에 자세히 설명되어 있습니다.

![NFS 업데이트 페이지가 오른쪽에 열려 있는 포털 네임스페이스 페이지 스크린샷](media/update-namespace-nfs.png)

1. 변경할 경로의 이름을 클릭합니다.
1. 편집 창을 사용하여 새 가상 경로, 내보내기 또는 하위 디렉터리 값을 입력하거나 다른 액세스 정책을 선택할 수 있습니다.
1. 변경 후에는 **확인** 을 클릭하여 스토리지 대상을 업데이트하거나 **취소** 를 클릭하여 변경 내용을 취소합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC Cache용 Azure CLI 설치](./az-cli-prerequisites.md).

[az hpc-cache nfs-storage-target update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target) 명령의 ``--junction`` 옵션을 사용하여 네임스페이스 경로, NFS 내보내기 또는 내보내기 하위 디렉터리를 변경할 수 있습니다.

``--junction`` 매개 변수는 다음 값을 사용합니다.

* ``namespace-path`` - 클라이언트 연결 가상 파일 경로
* ``nfs-export`` - 클라이언트 연결 경로와 연결할 스토리지 시스템 내보내기
* ``target-path``(선택 사항) - 필요한 경우 내보내기의 하위 디렉터리

예: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

``--junction`` 문의 각 경로에 대해 세 가지 값을 모두 제공해야 합니다. 변경하지 않으려는 값의 경우 기존 값을 사용합니다.

캐시 이름, 스토리지 대상 이름 및 리소스 그룹도 모든 업데이트 명령에 필요합니다.

예제 명령:

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
  --name st-name --resource-group doc-rg0619 \
  --junction namespace-path="/new-path" nfs-export="/my-export" target-path="my-subdirectory"
```

---

### <a name="change-the-usage-model"></a>사용 모델 변경

사용 모델은 캐시가 데이터를 보존하는 방법에 영향을 줍니다. 자세히 알아보려면 [사용 모델 선택](hpc-cache-add-storage.md#choose-a-usage-model)을 참조하세요.

NFS 스토리지 대상에 대한 사용 모델을 변경하려면 다음 방법 중 하나를 사용합니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal의 **스토리지 대상** 페이지에서 사용 모델을 변경합니다. 변경할 스토리지 대상의 이름을 클릭합니다.

![NFS 스토리지 대상에 대한 편집 페이지의 스크린샷](media/edit-storage-nfs.png)

드롭다운 선택기를 사용하여 새 사용 모델을 선택합니다. **확인** 을 클릭하여 스토리지 대상을 업데이트하거나 **취소** 를 클릭하여 변경 내용을 취소합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC Cache용 Azure CLI 설치](./az-cli-prerequisites.md).

[az hpc-cache-storage-target update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-update) 명령을 사용합니다.

update 명령은 NFS 스토리지 대상을 추가하는 데 사용하는 명령과 거의 동일합니다. 자세한 내용과 예제는 [NFS 스토리지 대상 만들기](hpc-cache-add-storage.md#create-an-nfs-storage-target)를 참조하세요.

사용 모델을 변경하려면 ``--nfs3-usage-model`` 옵션을 업데이트합니다. 예: ``--nfs3-usage-model WRITE_WORKLOAD_15``

캐시 이름, 스토리지 대상 이름 및 리소스 그룹 값도 필요합니다.

사용 모델의 이름을 확인하려는 경우에는 [az hpc-cache usage-model list](/cli/azure/ext/hpc-cache/hpc-cache/usage-model#ext-hpc-cache-az-hpc-cache-usage-model-list) 명령을 사용합니다.

캐시가 중지되었거나 정상 상태가 아니면 캐시가 정상 상태가 된 후 업데이트가 적용됩니다.

---

### <a name="update-ip-address-custom-dns-configurations-only"></a>IP 주소 업데이트(사용자 지정 DNS 구성에만 해당)

캐시에서 기본이 아닌 DNS 구성을 사용하는 경우 백 엔드 DNS 변경으로 인해 NFS 스토리지 대상의 IP 주소가 변경될 수 있습니다. DNS 서버에서 백 엔드 스토리지 시스템의 IP 주소를 변경하는 경우 Azure HPC Cache는 스토리지 시스템에 대한 액세스 권한을 손실할 수 있습니다.

이러한 변경으로 인해 스토리지 사용할 수 없게 되므로 캐시의 사용자 지정 DNS 시스템 관리자와 협력하여 업데이트를 계획하는 것이 가장 좋습니다.

스토리지 대상의 DNS에서 제공하는 IP 주소를 업데이트해야 하는 경우 스토리지 대상 목록에 단추가 표시됩니다. **DNS 새로 고침** 을 클릭하여 새 IP 주소에 대한 사용자 지정 DNS 서버를 쿼리합니다.

![스토리지 대상 목록의 스크린샷 하나의 스토리지 대상의 경우 오른쪽 끝 열의 "..." 메뉴가 열리고 두 개의 옵션인 삭제 및 DNS 새로 고침이 표시됩니다.](media/refresh-dns.png)

성공하면 업데이트는 2분 이내에 수행됩니다. 한 번에 하나의 스토리지 대상만 새로 고칠 수 있습니다. 다른 작업을 시도하기 전에 이전 작업이 완료될 때까지 기다립니다.

## <a name="update-an-adls-nfs-storage-target-preview"></a>ADLS-NFS 스토리지 대상 업데이트(미리 보기)

NFS 대상과 마찬가지로 ADLS-NFS 스토리지 대상에 대한 네임스페이스 경로 및 사용 모델을 변경할 수 있습니다.

### <a name="change-an-adls-nfs-namespace-path"></a>ADLS-NFS 네임스페이스 경로 변경

Azure HPC Cache의 **네임스페이스** 페이지를 사용하여 네임스페이스 값을 업데이트합니다. 이 페이지는 [집계형 네임스페이스 설정](add-namespace-paths.md) 문서에 자세히 설명되어 있습니다.

![ADS-NFS 업데이트 페이지가 오른쪽에 열려 있는 포털 네임스페이스 페이지 스크린샷](media/update-namespace-adls.png)

1. 변경할 경로의 이름을 클릭합니다.
1. 편집 창을 사용하여 새 가상 경로를 입력하거나 액세스 정책을 업데이트합니다.
1. 변경 후에는 **확인** 을 클릭하여 스토리지 대상을 업데이트하거나 **취소** 를 클릭하여 변경 내용을 취소합니다.

### <a name="change-adls-nfs-usage-models"></a>ADLS-NFS 사용 모델 변경

ADLS-NFS 사용 모델에 대한 구성은 NFS 사용 모델 선택과 동일합니다. 위의 NFS 섹션에서 [사용 모델 변경](#change-the-usage-model)의 포털 지침을 읽습니다. ADLS-NFS 스토리지 대상을 업데이트하기 위한 추가 도구는 개발 중입니다.


## <a name="next-steps"></a>다음 단계

* 이러한 옵션에 대한 자세한 내용은 [스토리지 대상 추가](hpc-cache-add-storage.md)를 참조하세요.
* 가상 경로 사용에 대한 자세한 팁은 [집계형 네임스페이스 계획](hpc-cache-namespace.md)을 참조하세요.
