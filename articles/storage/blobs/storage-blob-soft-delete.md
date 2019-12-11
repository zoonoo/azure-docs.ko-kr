---
title: Azure Storage blob에 대 한 일시 삭제 | Microsoft Docs
description: 이제 Azure Storage는 blob 개체에 대해 일시 삭제를 제공 하므로, 응용 프로그램 또는 다른 저장소 계정 사용자가 실수로 수정 하거나 삭제 하는 경우 더 쉽게 데이터를 복구할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 15db96824336c92611b9e1113c42c621f6508744
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978120"
---
# <a name="soft-delete-for-azure-storage-blobs"></a>Azure Storage blob에 대 한 일시 삭제

이제 Azure Storage는 blob 개체에 대해 일시 삭제를 제공 하므로, 응용 프로그램 또는 다른 저장소 계정 사용자가 실수로 수정 하거나 삭제 하는 경우 더 쉽게 데이터를 복구할 수 있습니다.

## <a name="how-soft-delete-works"></a>일시 삭제 작동 방식

일시 삭제를 사용 하도록 설정 하면 blob 또는 blob 스냅숏이 삭제 될 때 데이터를 저장 하 고 복구할 수 있습니다. 이 보호는 덮어쓰기의 결과로 지워지는 blob 데이터로 확장 됩니다.

데이터가 삭제 되 면 영구적으로 삭제 되는 대신 일시 삭제 된 상태로 전환 됩니다. 일시 삭제가 설정 된 상태에서 데이터를 덮어쓰면 일시 삭제 된 스냅숏이 생성 되어 덮어쓴 데이터의 상태를 저장 합니다. 일시 삭제 된 개체는 명시적으로 나열 되지 않은 경우 보이지 않습니다. Megadhatja azt az időtartamot, ameddig visszaállíthatók a helyreállítható módon törölt adatok, és amely után véglegesen törlődnek.

일시 삭제는 이전 버전과 호환 되므로 응용 프로그램을 변경할 필요가 없으므로이 기능의 보호를 활용할 수 있습니다. 그러나 [데이터 복구](#recovery) 는 새로운 **삭제 취소 Blob** API를 도입 합니다.

### <a name="configuration-settings"></a>Konfigurációs beállítások

새 계정을 만들 때 일시 삭제는 기본적으로 해제 되어 있습니다. 소프트 삭제는 기존 저장소 계정에 대해서도 기본적으로 해제 되어 있습니다. 저장소 계정의 수명 동안 언제 든 지 기능을 설정 하거나 해제할 수 있습니다.

기능이 꺼져 있으면 일시 삭제 된 데이터가 이전에 설정 되었을 때 일시 삭제 된 데이터가 저장 되었다고 가정 하 고 기능이 꺼져 있으면 일시 삭제 된 데이터에 액세스 하 고 복구할 수 있습니다. 일시 삭제를 설정 하는 경우에도 보존 기간을 구성 해야 합니다.

보존 기간은 일시 삭제 된 데이터가 저장 되 고 복구에 사용할 수 있는 시간을 나타냅니다. 명시적으로 삭제 된 blob 및 blob 스냅숏의 경우 데이터를 삭제할 때 보존 기간 시계가 시작 됩니다. 데이터를 덮어쓸 때 일시 삭제 기능으로 생성 된 일시 삭제 된 스냅숏의 경우 스냅숏이 생성 될 때 클록이 시작 됩니다. 현재는 1 일에서 365 일 사이에 일시 삭제 된 데이터를 유지할 수 있습니다.

언제 든 지 일시 삭제 보존 기간을 변경할 수 있습니다. 업데이트 된 보존 기간은 새로 삭제 된 데이터에만 적용 됩니다. 이전에 삭제 된 데이터는 해당 데이터를 삭제할 때 구성 된 보존 기간에 따라 만료 됩니다. 일시 삭제 된 개체를 삭제 하려고 하면 만료 시간에 영향을 주지 않습니다.

### <a name="saving-deleted-data"></a>삭제 된 데이터 저장

일시 삭제는 blob 또는 blob 스냅숏을 삭제 하거나 덮어쓰는 대부분의 경우 데이터를 유지 합니다.

Blob 넣기, **블록**배치, **블록 목록 배치**또는 **blob 복사** **를 사용 하**여 blob을 덮어쓰는 경우 쓰기 작업 전에 blob의 상태 스냅숏이 자동으로 생성 됩니다. 이 스냅숏은 일시 삭제 된 스냅숏입니다. 일시 삭제 된 개체가 명시적으로 나열 되지 않은 경우에는 표시 되지 않습니다. 일시 삭제 된 개체를 나열 하는 방법에 대 한 자세한 내용은 [복구](#recovery) 섹션을 참조 하세요.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

*일시 삭제 된 데이터는 회색 이며 활성 데이터는 파란색입니다. 이전 데이터 아래에 더 최근에 작성 된 데이터가 표시 됩니다. B0을 B1로 덮어쓰면 일시 삭제 된 B0 스냅숏이 생성 됩니다. B2를 B2로 덮어쓰면 B1의 일시 삭제 된 스냅숏이 생성 됩니다.*

> [!NOTE]  
> 일시 삭제는 대상 blob 계정에 대해 설정 된 복사 작업의 경우에만 덮어쓰기 보호를 사용할 수 있습니다.

> [!NOTE]  
> 일시 삭제는 보관 계층의 blob에 대 한 덮어쓰기 보호를 사용할 수 없습니다. 모든 계층의 새 blob을 사용 하 여 보관의 blob을 덮어쓰면 덮어쓴 blob이 영구적으로 만료 됩니다.

스냅숏에서 **Blob 삭제** 를 호출 하면 해당 스냅숏은 일시 삭제 된 것으로 표시 됩니다. 새 스냅숏은 생성 되지 않습니다.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

*일시 삭제 된 데이터는 회색 이며 활성 데이터는 파란색입니다. 이전 데이터 아래에 더 최근에 작성 된 데이터가 표시 됩니다. **Snapshot Blob** 이 호출 되 면 B0은 스냅숏이 되 고 B1은 blob의 활성 상태입니다. B0 스냅숏이 삭제 되 면 일시 삭제로 표시 됩니다.*

기본 blob (자체 스냅숏이 아닌 모든 blob)에서 **Blob 삭제** 를 호출 하면 해당 blob이 일시 삭제로 표시 됩니다. 이전 동작과 일치 하므로 활성 스냅숏이 있는 blob에서 **Blob 삭제** 를 호출 하면 오류가 반환 됩니다. 일시 삭제 된 스냅숏이 있는 blob에서 **Blob 삭제** 를 호출 하면 오류가 반환 되지 않습니다. 일시 삭제가 설정 된 경우에도 단일 작업에서 blob 및 해당 스냅숏을 모두 삭제할 수 있습니다. 이렇게 하면 기본 blob 및 스냅숏이 일시 삭제 됨으로 표시 됩니다.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

*일시 삭제 된 데이터는 회색 이며 활성 데이터는 파란색입니다. 이전 데이터 아래에 더 최근에 작성 된 데이터가 표시 됩니다. 여기서 **Delete Blob** 호출은 B2와 연결 된 모든 스냅숏을 삭제 합니다. 활성 blob, B2 및 연결 된 모든 스냅숏이 일시 삭제로 표시 됩니다.*

> [!NOTE]  
> 일시 삭제 된 blob을 덮어쓰는 경우 쓰기 작업 전에 blob 상태의 일시 삭제 된 스냅숏이 자동으로 생성 됩니다. 새 blob는 덮어쓴 blob의 계층을 상속 합니다.

일시 삭제는 컨테이너 또는 계정 삭제의 경우 나 blob 메타 데이터 및 blob 속성을 덮어쓰는 경우에는 데이터를 저장 하지 않습니다. 저장소 계정이 잘못 된 삭제 되지 않도록 보호 하기 위해 Azure Resource Manager를 사용 하 여 잠금을 구성할 수 있습니다. 자세한 내용은 [예기치 않은 변경을 방지 하기 위해 리소스 잠금](../../azure-resource-manager/resource-group-lock-resources.md) Azure Resource Manager 문서를 참조 하세요.

다음 표에서는 일시 삭제가 설정 된 경우 예상 되는 동작에 대해 자세히 설명 합니다.

| REST API 작업 | Erőforrás típusa | Leírás | 동작 변경 |
|--------------------|---------------|-------------|--------------------|
| [Törlés](/rest/api/storagerp/StorageAccounts/Delete) | Fiók | 저장소 계정에 포함 된 모든 컨테이너와 blob을 포함 하는 저장소 계정을 삭제 합니다.                           | 변경 되지 않았습니다. 삭제 된 계정의 컨테이너와 blob은 복구할 수 없습니다. |
| [Tároló törlése](/rest/api/storageservices/delete-container) | Tároló | 컨테이너에 포함 된 모든 blob을 포함 하 여 컨테이너를 삭제 합니다. | 변경 되지 않았습니다. 삭제 된 컨테이너의 blob은 복구할 수 없습니다. |
| [Put Blob](/rest/api/storageservices/put-blob) | 블록, 추가 및 페이지 Blob | 컨테이너 내에서 새 blob을 만들거나 기존 blob를 바꿉니다. | 기존 blob을 바꾸는 데 사용 하는 경우 호출 이전의 blob 상태 스냅숏이 자동으로 생성 됩니다. 이는 동일한 유형의 blob (블록, 추가 또는 페이지)로 대체 되는 경우에만 이전에 일시 삭제 된 blob에도 적용 됩니다. 다른 유형의 blob으로 대체 되는 경우 기존의 모든 일시 삭제 된 데이터는 영구적으로 만료 됩니다. |
| [Delete Blob](/rest/api/storageservices/delete-blob) | 블록, 추가 및 페이지 Blob | Blob 또는 blob 스냅숏을 삭제 하도록 표시 합니다. Blob 또는 스냅숏은 나중에 가비지 수집 중에 삭제 됩니다. | Blob 스냅숏을 삭제 하는 데 사용 하는 경우 해당 스냅숏은 일시 삭제 됨으로 표시 됩니다. Blob을 삭제 하는 데 사용 하는 경우 해당 blob은 일시 삭제 된 것으로 표시 됩니다. |
| [Copy Blob](/rest/api/storageservices/copy-blob) | 블록, 추가 및 페이지 Blob | 원본 blob을 동일한 저장소 계정 또는 다른 저장소 계정에 있는 대상 blob에 복사 합니다. | 기존 blob을 바꾸는 데 사용 하는 경우 호출 이전의 blob 상태 스냅숏이 자동으로 생성 됩니다. 이는 동일한 유형의 blob (블록, 추가 또는 페이지)로 대체 되는 경우에만 이전에 일시 삭제 된 blob에도 적용 됩니다. 다른 유형의 blob으로 대체 되는 경우 기존의 모든 일시 삭제 된 데이터는 영구적으로 만료 됩니다. |
| [블록 배치](/rest/api/storageservices/put-block) | Blokkblobok | 블록 blob의 일부로 커밋할 새 블록을 만듭니다. | 활성 상태인 blob에 블록을 커밋하는 데 사용 되는 경우 변경 내용이 없습니다. 일시 삭제 된 blob에 대 한 블록을 커밋하는 데 사용 하는 경우 새 blob이 만들어지고 일시 삭제 된 blob의 상태를 캡처하기 위해 스냅숏이 자동으로 생성 됩니다. |
| [블록 목록 배치](/rest/api/storageservices/put-block-list) | Blokkblobok | 블록 blob을 구성 하는 블록 Id 집합을 지정 하 여 blob을 커밋합니다. | 기존 blob을 바꾸는 데 사용 하는 경우 호출 이전의 blob 상태 스냅숏이 자동으로 생성 됩니다. 이는 블록 Blob 인 경우에만 이전에 일시 삭제 된 blob에도 적용 됩니다. 다른 유형의 blob으로 대체 되는 경우 기존의 모든 일시 삭제 된 데이터는 영구적으로 만료 됩니다. |
| [페이지 배치](/rest/api/storageservices/put-page) | Lapblobok | 페이지 Blob에 페이지 범위를 씁니다. | 변경 되지 않았습니다. 이 작업을 사용 하 여 덮어쓰거나 지운 페이지 Blob 데이터는 저장 되지 않으므로 복구할 수 없습니다. |
| [블록 추가](/rest/api/storageservices/append-block) | Hozzáfűző blobok | 추가 Blob의 끝에 데이터 블록을 씁니다. | 변경 되지 않았습니다. |
| [Blob 속성 설정](/rest/api/storageservices/set-blob-properties) | 블록, 추가 및 페이지 Blob | Blob에 대해 정의 된 시스템 속성의 값을 설정 합니다. | 변경 되지 않았습니다. 덮어쓴 blob 속성은 복구할 수 없습니다. |
| [Blob 메타 데이터 설정](/rest/api/storageservices/set-blob-metadata) | 블록, 추가 및 페이지 Blob | 지정 된 blob에 대 한 사용자 정의 메타 데이터를 하나 이상의 이름-값 쌍으로 설정 합니다. | 변경 되지 않았습니다. 덮어쓸 blob 메타 데이터를 복구할 수 없습니다. |

페이지 Blob의 범위를 덮어쓰거나 지우도록 "페이지 배치"를 호출 하면 스냅숏이 자동으로 생성 되지 않는다는 것을 알 수 있습니다. 가상 컴퓨터 디스크는 페이지 Blob에 의해 지원 되며 **Put 페이지** 를 사용 하 여 데이터를 작성 합니다.

### <a name="recovery"></a>Helyreállítás

일시 삭제 된 기본 blob에서 [blob 삭제 취소](/rest/api/storageservices/undelete-blob) 작업을 호출 하면 해당 작업 및 연결 된 모든 일시 삭제 된 스냅숏이 활성으로 복원 됩니다. 활성 기본 blob에 대 한 `Undelete Blob` 작업을 호출 하면 연결 된 모든 일시 삭제 된 스냅숏이 활성으로 복원 됩니다. 스냅숏이 활성으로 복원 되 면 사용자가 생성 한 스냅숏 처럼 보입니다. 기본 blob을 덮어쓰지 않습니다.

Blob을 일시 삭제 된 특정 스냅숏으로 복원 하려면 기본 blob에서 `Undelete Blob`를 호출 하면 됩니다. 그런 다음 현재-활성 blob을 통해 스냅숏을 복사할 수 있습니다. 새 blob에 스냅숏을 복사할 수도 있습니다.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

*일시 삭제 된 데이터는 회색 이며 활성 데이터는 파란색입니다. 이전 데이터 아래에 더 최근에 작성 된 데이터가 표시 됩니다. 여기서는 blob B에서 **삭제 취소 blob** 을 호출 하 여 기본 Blob, B1 및 연결 된 모든 스냅숏을 복원 합니다. 여기에서는 B0만 활성으로 복원 합니다. 두 번째 단계에서는 B0이 기본 blob을 통해 복사 됩니다. 이 복사 작업은 B1의 일시 삭제 된 스냅숏을 생성 합니다.*

일시 삭제 된 blob 및 blob 스냅숏을 보려면 **목록 blob**에 삭제 된 데이터를 포함 하도록 선택할 수 있습니다. 일시 삭제 된 기본 blob만 표시 하거나 일시 삭제 된 blob 스냅숏을 포함 하도록 선택할 수 있습니다. 모든 일시 삭제 된 데이터에 대해 데이터가 삭제 된 시간 및 데이터가 영구적으로 만료 되기 전 까지의 일 수를 볼 수 있습니다.

### <a name="example"></a>Példa

다음은 일시 삭제가 설정 된 경우 *HelloWorld* 라는 blob을 업로드, 덮어쓰기, 스냅숏, 삭제 및 복원 하는 .net 스크립트의 콘솔 출력입니다.

```bash
Upload:
- HelloWorld (is soft deleted: False, is snapshot: False)

Overwrite:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Snapshot:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Delete (including snapshots):
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: False)

Undelete:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Copy a snapshot over the base blob:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)
```

이 출력을 생성 한 응용 프로그램에 대 한 포인터는 [다음 단계](#next-steps) 섹션을 참조 하세요.

## <a name="pricing-and-billing"></a>Árak és számlázás

모든 일시 삭제 된 데이터는 활성 데이터와 동일한 속도로 청구 됩니다. 구성 된 보존 기간 후에 영구적으로 삭제 되는 데이터에 대해서는 요금이 청구 되지 않습니다. 스냅숏에 대해 자세히 알아보고 요금을 부과 하는 방법에 대 한 자세한 내용은 [스냅숏이 요금을 계산 하는 방법 이해](storage-blob-snapshots.md)를 참조 하세요.

스냅숏을 자동으로 생성 하는 것과 관련 된 트랜잭션은 청구 되지 않습니다. 쓰기 작업에 대 한 속도에서 **삭제 취소 Blob** 트랜잭션에 대 한 요금이 청구 됩니다.

일반적인 Azure Blob Storage 가격에 대 한 자세한 내용은 [Azure Blob Storage 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/blobs/)를 참조 하세요.

일시 삭제를 처음 켤 때 기능이 요금 청구에 미치는 영향을 보다 잘 이해 하려면 작은 보존 기간을 사용 하는 것이 좋습니다.

## <a name="get-started"></a>Az első lépések

다음 단계는 일시 삭제를 시작 하는 방법을 보여 줍니다.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Azure Portal를 사용 하 여 저장소 계정의 blob에 대해 일시 삭제를 사용 하도록 설정 합니다.

1. [Azure Portal](https://portal.azure.com/)에서 저장소 계정을 선택 합니다. 

2. **Blob Service**에서 **데이터 보호** 옵션으로 이동 합니다.

3. **Blob 일시 삭제** 아래에서 **사용** 을 클릭 합니다.

4. **보존 정책** 에서 *보존할* 일 수를 입력 합니다.

5. **저장** 단추를 선택 하 여 데이터 보호 설정 확인

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

일시 삭제 된 blob을 보려면 삭제 된 **Blob 표시** 확인란을 선택 합니다.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

지정 된 blob에 대해 일시 삭제 된 스냅숏을 보려면 blob을 선택 하 고 **스냅숏 보기**를 클릭 합니다.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

**삭제 된 스냅숏 표시** 확인란이 선택 되어 있는지 확인 합니다.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

일시 삭제 된 blob 또는 스냅숏을 클릭 하면 새 blob 속성을 확인할 수 있습니다. 이는 개체가 삭제 된 시간과 blob 또는 blob 스냅숏이 영구적으로 만료 될 때까지 남은 일 수를 표시 합니다. 일시 삭제 된 개체가 스냅숏이 아닌 경우 삭제를 취소 하는 옵션도 있습니다.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

삭제 취소 a blob은 연결 된 모든 스냅숏을 삭제 취소 합니다. 활성 blob에 대해 일시 삭제 된 스냅숏을 삭제 취소 하려면 blob을 클릭 하 고 **모든 스냅숏 삭제 취소**를 선택 합니다.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Blob의 스냅숏을 삭제 취소 한 후에는 **승격** 을 클릭 하 여 루트 blob에 대 한 스냅숏을 복사 하 여 blob을 스냅숏으로 복원할 수 있습니다.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

일시 삭제를 사용 하도록 설정 하려면 blob 클라이언트의 서비스 속성을 업데이트 합니다. 다음 예에서는 구독의 계정 하위 집합에 대해 일시 삭제를 사용 하도록 설정 합니다.

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```
다음 명령을 사용 하 여 일시 삭제가 설정 되었는지 확인할 수 있습니다.

```powershell
$MatchingAccounts | Get-AzStorageServiceProperty -ServiceType Blob
```

실수로 삭제 된 blob을 복구 하려면 해당 blob에 대 한 삭제 취소를 호출 합니다. 활성 및 일시 삭제 된 blob에 대 한 삭제 **취소 Blob**을 호출 하면 연결 된 모든 일시 삭제 된 스냅숏이 활성으로 복원 됩니다. 다음 예에서는 컨테이너의 모든 일시 삭제 된 blob 및 활성 blob에 대 한 삭제 취소를 호출 합니다.

```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
현재 일시 삭제 보존 정책을 찾으려면 다음 명령을 사용 합니다.

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

# <a name="clitabazure-cli"></a>[Parancssori felület](#tab/azure-CLI)

일시 삭제를 사용 하도록 설정 하려면 blob 클라이언트의 서비스 속성을 업데이트 합니다.

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

일시 삭제가 설정 되었는지 확인 하려면 다음 명령을 사용 합니다. 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

# <a name="pythontabpython"></a>[Python](#tab/python)

일시 삭제를 사용 하도록 설정 하려면 blob 클라이언트의 서비스 속성을 업데이트 합니다.

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(
    account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(
    delete_retention_policy=DeleteRetentionPolicy(enabled=True, days=7))
```

# <a name="nettabnet"></a>[.NET](#tab/net)

일시 삭제를 사용 하도록 설정 하려면 blob 클라이언트의 서비스 속성을 업데이트 합니다.

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

실수로 삭제 된 blob을 복구 하려면 해당 blob에 대 한 삭제 취소를 호출 합니다. 활성 및 일시 삭제 된 blob에 대 한 삭제 **취소 Blob**을 호출 하면 연결 된 모든 일시 삭제 된 스냅숏이 활성으로 복원 됩니다. 다음 예에서는 컨테이너의 모든 일시 삭제 된 blob 및 활성 blob에 대 한 삭제 취소를 호출 합니다.

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

특정 blob 버전으로 복구 하려면 먼저 blob에 대 한 삭제 취소를 호출한 다음 blob에 대해 원하는 스냅숏을 복사 합니다. 다음 예제에서는 가장 최근에 생성 된 스냅숏으로 블록 blob을 복구 합니다.

```csharp
// Undelete
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container prefixed by the blob name
IEnumerable<IListBlobItem> allBlobVersions = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Restore the most recently generated snapshot to the active blob    
CloudBlockBlob copySource = allBlobVersions.First(version => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)version).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```

---

## <a name="special-considerations"></a>특별 고려 사항

응용 프로그램 또는 다른 저장소 계정 사용자가 실수로 데이터를 수정 하거나 삭제할 가능성이 있는 경우 일시 삭제를 켜는 것이 좋습니다. 자주 덮어쓴 데이터에 대해 일시 삭제를 사용 하도록 설정 하면 blob을 나열할 때 저장소 용량 요금이 증가 하 고 대기 시간이 길어질 수 있습니다. 일시 삭제를 사용 하지 않도록 설정 된 별도의 저장소 계정에 자주 덮어쓴 데이터를 저장 하 여 이러한 추가 비용 및 대기 시간을 줄일 수 있습니다. 

## <a name="faq"></a>Gyakori kérdések

### <a name="for-which-storage-services-can-i-use-soft-delete"></a>일시 삭제를 사용할 수 있는 저장소 서비스는 무엇 인가요?

현재 일시 삭제는 blob (개체) 저장소에 대해서만 사용할 수 있습니다.

### <a name="is-soft-delete-available-for-all-storage-account-types"></a>일시 삭제는 모든 저장소 계정 유형에 사용할 수 있나요?

예, 일시 삭제는 Blob storage 계정 뿐만 아니라 범용 (GPv1 및 GPv2) 저장소 계정의 blob에 대해서만 사용할 수 있습니다. Standard 및 premium 계정 유형이 모두 지원 됩니다. 일시 삭제는 관리 되지 않는 디스크에 사용할 수 있으며,이는 페이지 blob입니다. 관리 디스크에는 일시 삭제를 사용할 수 없습니다.

### <a name="is-soft-delete-available-for-all-storage-tiers"></a>일시 삭제는 모든 저장소 계층에 사용할 수 있나요?

예, 일시 삭제는 핫, 쿨 및 보관을 비롯 한 모든 저장소 계층에 사용할 수 있습니다. 그러나 일시 삭제는 보관 계층의 blob에 대 한 덮어쓰기 보호를 사용할 수 없습니다.

### <a name="can-i-use-the-set-blob-tier-api-to-tier-blobs-with-soft-deleted-snapshots"></a>Blob 계층 API 집합을 사용 하 여 일시 삭제 된 스냅숏이 있는 blob 계층을 만들 수 있나요?

Igen. 일시 삭제 된 스냅숏은 원래 계층에 남아 있지만 기본 blob는 새 계층으로 이동 됩니다. 

### <a name="premium-storage-accounts-have-a-per-blob-snapshot-limit-of-100-do-soft-deleted-snapshots-count-toward-this-limit"></a>Premium storage 계정에는 blob 당 스냅숏 제한인 100이 있습니다. 일시 삭제 된 스냅숏 수가이 한도에 도달 하나요?

아니요. 일시 삭제 된 스냅숏은이 제한에 계산 되지 않습니다.

### <a name="can-i-turn-on-soft-delete-for-existing-storage-accounts"></a>기존 저장소 계정에 대해 일시 삭제를 켤 수 있나요?

예, 일시 삭제는 기존 및 새 저장소 계정 모두에 대해 구성할 수 있습니다.

### <a name="if-i-delete-an-entire-account-or-container-with-soft-delete-turned-on-will-all-associated-blobs-be-saved"></a>일시 삭제가 설정 된 상태에서 전체 계정 또는 컨테이너를 삭제 하면 연결 된 모든 blob이 저장 됩니까?

아니요. 전체 계정 또는 컨테이너를 삭제 하면 연결 된 모든 blob이 영구적으로 삭제 됩니다. 저장소 계정이 실수로 삭제 되지 않도록 보호 하는 방법에 대 한 자세한 내용은 [리소스를 잠가 예기치 않은 변경 방지](../../azure-resource-manager/resource-group-lock-resources.md)를 참조 하세요.

### <a name="can-i-view-capacity-metrics-for-deleted-data"></a>삭제 된 데이터에 대 한 용량 메트릭을 볼 수 있나요?

일시 삭제 된 데이터는 총 저장소 계정 용량의 일부로 포함 됩니다. 저장소 용량 추적 및 모니터링에 대 한 자세한 내용은 [스토리지 분석](../common/storage-analytics.md)를 참조 하세요.

### <a name="if-i-turn-off-soft-delete-will-i-still-be-able-to-access-soft-deleted-data"></a>일시 삭제를 해제 한 경우에도 일시 삭제 된 데이터에 계속 액세스할 수 있나요?

예, 일시 삭제가 해제 된 경우에도 만료 되지 않은 일시 삭제 된 데이터에 액세스 하 고 복구할 수 있습니다.

### <a name="can-i-read-and-copy-out-soft-deleted-snapshots-of-my-blob"></a>Blob의 일시 삭제 된 스냅숏을 읽고 복사할 수 있나요?  

예, 하지만 blob에서 삭제 취소를 먼저 호출 해야 합니다.

### <a name="is-soft-delete-available-for-all-blob-types"></a>일시 삭제는 모든 blob 유형에 사용할 수 있나요?

예, 일시 삭제는 블록 blob, 추가 blob 및 페이지 blob에 사용할 수 있습니다.

### <a name="is-soft-delete-available-for-virtual-machine-disks"></a>가상 컴퓨터 디스크에 대해 일시 삭제를 사용할 수 있나요?  

일시 삭제는 프리미엄 및 표준 관리 되지 않는 디스크 모두에 사용할 수 있습니다. 이러한 디스크는 내부적으로 페이지 blob입니다. 일시 삭제는 **Blob 삭제**, **Blob 배치**, **블록 목록**배치, **블록 배치** 및 **blob 복사** 작업을 통해 삭제 된 데이터를 복구 하는 데만 도움이 됩니다. **Put 페이지** 호출로 덮어쓴 데이터는 복구할 수 없습니다.

Azure 가상 머신은 **Put 페이지**호출을 사용 하 여 관리 되지 않는 디스크에 기록 되므로, 일시 삭제를 사용 하 여 azure VM에서 관리 되지 않는 디스크에 대 한 쓰기를 실행 취소 하는 것은 지원 되지 않습니다.

### <a name="do-i-need-to-change-my-existing-applications-to-use-soft-delete"></a>일시 삭제를 사용 하도록 기존 응용 프로그램을 변경 해야 하나요?

사용 중인 API 버전에 관계 없이 일시 삭제를 활용할 수 있습니다. 그러나 일시 삭제 된 blob 및 blob 스냅숏을 나열 하 고 복구 하려면 [REST API 이상의 저장소 서비스](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) 버전 2017-07-29을 사용 해야 합니다. Microsoft에서 항상 최신 버전의 Azure Storage API를 사용 하는 것이 좋습니다.

## <a name="next-steps"></a>Következő lépések

* [.NET 샘플 코드](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)
* [A Blob szolgáltatás REST API-ja](/rest/api/storageservices/blob-service-rest-api)
* [Azure Storage 복제](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [RA를 사용 하 여 항상 사용 가능한 응용 프로그램 디자인-GRS](../common/storage-designing-ha-apps-with-ragrs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Azure Storage의 재해 복구 및 저장소 계정 장애 조치 (failover) (미리 보기)](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
