---
title: Azure HPC 캐시 집계 네임 스페이스 구성
description: Azure HPC 캐시를 사용 하 여 백 엔드 저장소에 대 한 클라이언트 연결 경로를 만드는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 090e3f93d025fe87ad5b89a98193574595f3d632
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91614630"
---
# <a name="set-up-the-aggregated-namespace"></a>집계 된 네임 스페이스 설정

저장소 대상을 만든 후에는이에 대 한 네임 스페이스 경로도 만들어야 합니다. 클라이언트 컴퓨터는 백 엔드 저장소에 직접 연결 하는 대신 이러한 가상 경로를 사용 하 여 캐시를 통해 파일에 액세스 합니다. 이 시스템을 통해 캐시 관리자는 클라이언트 지침을 다시 작성할 필요 없이 백 엔드 저장소 시스템을 변경할 수 있습니다.

이 기능에 대 한 자세한 내용은 [집계 된 네임 스페이스 계획](hpc-cache-namespace.md) 을 참조 하세요.

Azure Portal의 **네임 스페이스** 페이지에는 클라이언트가 캐시를 통해 데이터에 액세스 하는 데 사용 하는 경로가 표시 됩니다. 이 페이지를 사용 하 여 네임 스페이스 경로를 만들거나 제거 하거나 변경할 수 있습니다. 또한 Azure CLI를 사용 하 여 네임 스페이스 경로를 구성할 수 있습니다.

모든 기존 클라이언트 지향 경로는 **네임 스페이스** 페이지에 나열 됩니다. 저장소 대상에 경로가 없으면 표에 표시 되지 않습니다.

화살표를 클릭 하 여 테이블 열을 정렬 하 고 캐시의 집계 된 네임 스페이스를 더 잘 이해할 수 있습니다.

![테이블에 두 개의 경로가 있는 포털 네임 스페이스 페이지의 스크린샷 열 머리글: 네임 스페이스 경로, 저장소 대상, 내보내기 경로 및 하위 디렉터리를 내보냅니다. 첫 번째 열의 항목은 클릭할 수 있는 링크입니다. 위쪽 단추: 네임 스페이스 경로 추가, 새로 고침, 삭제](media/namespace-page.png)

## <a name="add-or-edit-client-facing-namespace-paths"></a>클라이언트 지향 네임 스페이스 경로 추가 또는 편집

클라이언트에서 저장소 대상에 액세스 하려면 먼저 네임 스페이스 경로를 하나 이상 만들어야 합니다. 클라이언트 액세스에 대 한 자세한 내용은 [AZURE HPC 캐시 탑재](hpc-cache-mount.md) 를 참조 하세요.

### <a name="blob-namespace-paths"></a>Blob 네임 스페이스 경로

Azure Blob 저장소 대상에는 네임 스페이스 경로가 하나만 있을 수 있습니다.

아래 지침에 따라 Azure Portal 또는 Azure CLI 경로를 설정 하거나 변경 합니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 **네임 스페이스** 설정 페이지를 로드 합니다. 이 페이지에서 네임 스페이스 경로를 추가, 변경 또는 삭제할 수 있습니다.

* **새 경로를 추가 합니다.** 위쪽에서 **+ 추가** 단추를 클릭 하 고 편집 패널에 정보를 입력 합니다.

  * 드롭다운 목록에서 저장소 대상을 선택 합니다. 이 스크린샷에서 blob 저장소 대상은 이미 네임 스페이스 경로를 포함 하 고 있으므로 선택할 수 없습니다.

    ![저장소 대상 선택 기가 노출 된 새 네임 스페이스 편집 필드의 스크린샷](media/namespace-select-storage-target.png)

  * Azure Blob 저장소 대상의 경우 내보내기 및 하위 디렉터리 경로는 자동으로로 설정 됩니다 ``/`` .

* **기존 경로 변경:** 네임 스페이스 경로를 클릭 합니다. 편집 패널이 열리고 경로를 수정할 수 있습니다.

  ![Blob 네임 스페이스 경로를 클릭 한 후 네임 스페이스 페이지의 스크린샷-오른쪽의 창에 편집 필드가 표시 됩니다.](media/edit-namespace-blob.png)

* **네임 스페이스 경로를 삭제 합니다.** 경로 왼쪽에 있는 확인란을 선택 하 고 **삭제** 단추를 클릭 합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Azure CLI 사용 하는 경우 저장소 대상을 만들 때 네임 스페이스 경로를 추가 해야 합니다. 자세한 내용은 [새 Azure Blob 저장소 대상 추가를](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-azure-blob-storage-target) 참조 하세요.

대상의 네임 스페이스 경로를 업데이트 하려면 [az hpc-cache blob-storage-target update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update) 명령을 사용 합니다. Update 명령에 대 한 인수는 컨테이너 이름이 나 저장소 계정을 전달 하지 않는다는 점을 제외 하 고 create 명령의 인수와 유사 합니다.

Azure CLI를 사용 하 여 blob 저장소 대상에서 네임 스페이스 경로를 삭제할 수 없지만 경로를 다른 값으로 덮어쓸 수 있습니다.

---

### <a name="nfs-namespace-paths"></a>NFS 네임 스페이스 경로

각 경로가 동일한 저장소 시스템의 다른 내보내기 또는 하위 디렉터리를 나타내는 한 NFS 저장소 대상에는 여러 개의 가상 경로가 있을 수 있습니다.

NFS 저장소 대상에 대 한 네임 스페이스를 계획할 때 각 경로는 고유 해야 하며 다른 네임 스페이스 경로의 하위 디렉터리가 될 수 없다는 점을 명심 해야 합니다. 예를 들어를 호출 하는 네임 스페이스 경로가 있는 경우 ``/parent-a`` 및와 같은 네임 스페이스 경로를 만들 수 없습니다 ``/parent-a/user1`` ``/parent-a/user2`` . 이러한 디렉터리 경로는 네임 스페이스에서의 하위 디렉터리로 이미 액세스할 수 있습니다 ``/parent-a`` .

NFS 저장소 시스템에 대 한 모든 네임 스페이스 경로는 하나의 저장소 대상에 생성 됩니다. 대부분의 캐시 구성은 저장소 대상 당 최대 10 개의 네임 스페이스 경로를 지원할 수 있지만 대규모 구성은 최대 20 개까지 지원할 수 있습니다.

이 목록에는 구성 당 최대 네임 스페이스 경로 수가 표시 됩니다.

* 최대 2gb 처리량:

  * 3TB 캐시-10 개의 네임 스페이스 경로
  * 6TB 캐시-10 개의 네임 스페이스 경로
  * 23tb 캐시-20 개의 네임 스페이스 경로

* 최대 5 g b/초 처리량:

  * 6TB 캐시-10 개의 네임 스페이스 경로
  * 12tb 캐시-10 개의 네임 스페이스 경로
  * 24tb 캐시-20 개의 네임 스페이스 경로

* 최대 8gb/s 처리량:

  * 12tb 캐시-10 개의 네임 스페이스 경로
  * 24tb 캐시-10 개의 네임 스페이스 경로
  * 48 TB 캐시-20 개의 네임 스페이스 경로

각 NFS 네임 스페이스 경로에 대해 클라이언트 지향 경로, 저장소 시스템 내보내기 및 선택적으로 내보내기 하위 디렉터리를 제공 합니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 **네임 스페이스** 설정 페이지를 로드 합니다. 이 페이지에서 네임 스페이스 경로를 추가, 편집 또는 삭제할 수 있습니다.

* **새 경로를 추가 하려면:** 위쪽에서 **+ 추가** 단추를 클릭 하 고 편집 패널에 정보를 입력 합니다.
* **기존 경로를 변경 하려면:** 네임 스페이스 경로를 클릭 합니다. 편집 패널이 열리고 경로를 수정할 수 있습니다.
* **네임 스페이스 경로를 삭제 하려면:** 경로 왼쪽에 있는 확인란을 선택 하 고 **삭제** 단추를 클릭 합니다.

각 네임 스페이스 경로에 대해 다음 값을 입력 합니다.

* **네임 스페이스 경로** -클라이언트 지향 파일 경로입니다.

* **저장소 대상** -새 네임 스페이스 경로를 만드는 경우 드롭다운 메뉴에서 저장소 대상을 선택 합니다.

* **내보내기 경로** -NFS 내보내기의 경로를 입력 합니다. 내보내기 이름을 올바르게 입력 해야 합니다.-포털은이 필드의 구문에 대 한 유효성을 검사 하지만 변경 내용을 제출할 때까지 내보내기를 확인 하지 않습니다.

* **하위 디렉터리 내보내기** -이 경로를 통해 내보내기의 특정 하위 디렉터리를 탑재 하려면 여기에 입력 합니다. 그렇지 않으면이 필드를 비워 둡니다.

![오른쪽에 업데이트 페이지가 열려 있는 포털 네임 스페이스 페이지의 스크린샷](media/update-namespace-nfs.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Azure CLI 사용 하는 경우 저장소 대상을 만들 때 네임 스페이스 경로를 하나 이상 추가 해야 합니다. 자세한 내용은 [새 NFS 저장소 대상 추가를](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) 참조 하세요.

대상의 네임 스페이스 경로를 업데이트 하거나 추가 경로를 추가 하려면 [az hpc-cache nfs-target-target update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-update) 명령을 사용 합니다. 옵션을 사용 ``--junction`` 하 여 원하는 모든 네임 스페이스 경로를 지정 합니다.

업데이트 명령에 사용 되는 옵션은 저장소 시스템 정보 (IP 주소 또는 호스트 이름)를 전달 하지 않고 사용 모델을 선택 하는 것을 제외 하 고는 "만들기" 명령과 유사 합니다. 옵션의 구문에 대 한 자세한 내용은 [새 NFS 저장소 대상 추가](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) 를 참조 ``--junction`` 하세요.

---

## <a name="next-steps"></a>다음 단계

저장소 대상에 대 한 집계 된 네임 스페이스를 만든 후 캐시에 클라이언트를 탑재할 수 있습니다. 자세한 내용은 다음 문서를 참조 하세요.

* [Azure HPC Cache 마운트](hpc-cache-mount.md)
* [Azure Blob storage로 데이터 이동](hpc-cache-ingest.md)
