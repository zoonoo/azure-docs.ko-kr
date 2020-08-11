---
title: Blob 스냅숏
titleSuffix: Azure Storage
description: Blob의 읽기 전용 스냅숏을 만들어 지정 된 시간에 blob 데이터를 백업 하는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/02/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 24118e6ae5c31399ce5d33361dd60e3a08424681
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88055771"
---
# <a name="blob-snapshots"></a>Blob 스냅숏

스냅샷은 특정 시점에 생성된 Blob의 읽기 전용 버전입니다.

> [!NOTE]
> Blob 버전 관리 (미리 보기)는 blob의 기록 복사본을 유지 관리 하는 다른 방법을 제공 합니다. 자세한 내용은 [Blob 버전 관리 (미리 보기)](versioning-overview.md)를 참조 하세요.

## <a name="about-blob-snapshots"></a>Blob 스냅숏 정보

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

Blob URI에 스냅샷이 만들어진 시점의 시간을 나타내는 Blob URI에 추가된 **DateTime** 값이 있다는 점을 제외하고 Blob의 스냅샷은 해당 Blob와 동일합니다. 예를 들어 페이지 Blob URI가 `http://storagesample.core.blob.windows.net/mydrives/myvhd`이면 스냅샷 URI는 `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`과 유사합니다.

> [!NOTE]
> 모든 스냅샷은 기본 Blob의 URI를 공유합니다. 기본 Blob와 스냅샷의 유일한 차이는 추가된 **DateTime** 값입니다.
>

Blob 하나에 여러 스냅샷이 있을 수 있습니다. 스냅숏은 독립적으로 또는 기본 blob에 대 한 Blob 삭제 작업의 일부로 명시적으로 삭제 될 때까지 유지 됩니다. 기본 Blob와 연결된 스냅샷을 열거하여 현재 스냅샷을 추적할 수 있습니다.

Blob의 스냅샷을 만들면 blob의 시스템 속성이 같은 값으로 스냅샷에 복사됩니다. 만들 때 스냅샷에 대한 별도의 메타데이터를 지정하지 않으면 기본 Blob의 메타데이터가 스냅샷에 복사됩니다. 스냅샷을 만든 후에 읽기, 복사 또는 삭제할 수 있지만 수정할 수 없습니다.

기본 blob와 연결된 모든 임대는 스냅샷에 영향을 주지 않습니다. 스냅샷에 대해 임대를 가져올 수 없습니다.

VM 디스크에 대한 현재 정보 및 상태를 저장하는 데 VHD 파일을 사용합니다. VM 내에서 디스크를 분리하거나 VM을 종료하고, 해당 VHD 파일의 스냅샷을 만들 수 있습니다. 해당 스냅샷 파일을 나중에 사용하여 해당 시점에 VHD 파일을 검색하여 VM을 다시 만들 수 있습니다.

## <a name="understand-how-snapshots-accrue-charges"></a>스냅샷 요금 청구 방법 이해

Blob의 읽기 전용 복사본인 스냅샷을 만들면 계정에 데이터 스토리지 요금이 추가로 부과될 수 있습니다. 애플리케이션을 디자인할 때는 비용을 최소화할 수 있도록 이러한 요금 발생 방식을 파악하는 것이 중요합니다.

### <a name="important-billing-considerations"></a>청구 관련 중요 고려 사항

아래 목록에는 스냅샷을 만들 때 고려할 주요 사항이 나와 있습니다.

- 스토리지 계정에서는 고유 블록이나 페이지가 Blob에 있는지 혹은 스냅샷에 있는지에 관계없이 요금이 발생합니다. 스냅샷의 기준이 되는 Blob을 업데이트할 때까지는 해당 Blob와 연결된 스냅샷에 대해 계정에 추가 요금이 부과되지 않습니다. 기본 Blob를 업데이트한 후에 해당 스냅샷과 달라집니다. 이 경우에 Blob 또는 스냅샷 각각에서 고유한 블록이나 페이지에 대한 요금이 청구됩니다.
- 블록 Blob 내의 블록을 바꾸고 나면 해당 블록은 이후부터 고유 블록으로 요금이 청구됩니다. 블록의 ID와 데이터가 스냅샷에서와 같더라도 마찬가지입니다. 블록을 다시 커밋하면 스냅샷의 해당 항목과 달라지므로 해당 데이터에 대해 요금이 부과됩니다. 같은 데이터로 업데이트하는 페이지 Blob 내 페이지의 경우에도 마찬가지입니다.
- [UploadFromFile] [dotnet_UploadFromFile], [UploadText] [dotnet_UploadText], [UploadFromStream] [dotnet_UploadFromStream] 또는 [UploadFromByteArray] [dotnet_UploadFromByteArray] 메서드를 호출 하 여 블록 blob을 바꾸면 blob의 모든 블록이 바뀝니다. 해당 Blob에 스냅샷이 연결되어 있으면 스냅샷과 기본 Blob의 모든 블록이 달라지므로 두 Blob의 모든 블록에 대해 요금이 부과됩니다. 이는 기본 Blob와 스냅샷의 데이터가 동일하게 유지되더라도 마찬가지입니다.
- Azure Blob service에서는 두 블록이 같은 데이터를 포함하는지를 확인할 수 없습니다. 업로드/커밋되는 각 블록은 데이터와 블록 ID가 같더라도 고유한 블록으로 처리됩니다. 고유한 블록에 대해서는 비용이 부과되므로 스냅샷이 포함된 Blob을 업데이트하면 고유한 블록이 추가로 생성되어 추가 비용이 발생함을 고려해야 합니다.

### <a name="minimize-cost-with-snapshot-management"></a>스냅샷 관리 비용을 최소화

추가 비용을 방지하기 위해서는 스냅샷을 신중하게 관리하는 것이 좋습니다. 스냅샷 스토리지으로 인해 발생하는 비용을 최소화하려면 다음 모범 사례를 따를 수 있습니다.

- 애플리케이션 디자인상 스냅샷을 유지해야 하는 경우가 아니면, Blob을 업데이트할 때마다 같은 데이터로 업데이트하더라도 해당 Blob에 연결된 스냅샷을 삭제한 후에 다시 만듭니다. Blob의 스냅샷을 삭제한 후에 다시 만들면 Blob와 스냅샷이 달라지지 않습니다.
- Blob에 대 한 스냅숏을 유지 관리 하는 경우 [UploadFromFile] [dotnet_UploadFromFile], [UploadText] [dotnet_UploadText], [UploadFromStream] [dotnet_UploadFromStream] 또는 [UploadFromByteArray] [dotnet_UploadFromByteArray]를 호출 하 여 blob을 업데이트 하지 마십시오. 이러한 메서드는 Blob의 모든 블록을 바꾸어 기본 Blob 및 해당 스냅샷이 심각하게 달라집니다. 대신 [PutBlock] [dotnet_PutBlock] 및 [PutBlockList] [dotnet_PutBlockList] 메서드를 사용 하 여 가능한 최소 블록 수를 업데이트 합니다.

### <a name="snapshot-billing-scenarios"></a>스냅샷 청구 시나리오

다음 시나리오에서는 블록 Blob와 해당 스냅샷에 대해 비용이 청구되는 방법을 보여 줍니다.

#### <a name="scenario-1"></a>시나리오 1

시나리오 1에서는 스냅샷을 생성한 후 기본 Blob을 업데이트하지 않았으므로 고유 블록 1, 2, 3에 대해서만 비용이 청구됩니다.

![Azure Storage 리소스](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>시나리오 2

시나리오 2에서는 기본 Blob은 업데이트되었지만 스냅샷은 업데이트되지 않았습니다. 블록 3이 업데이트되고 동일한 데이터와 동일한 ID를 가지고 있지만 스냅샷의 블록 3과 같지는 않습니다. 따라서 4개 블록에 대한 요금이 계정에 청구됩니다.

![Azure Storage 리소스](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>시나리오 3

시나리오 3에서는 기본 Blob은 업데이트되었지만 스냅샷은 업데이트되지 않았습니다. 기본 Blob에서 블록 3이 블록 4로 바뀌었지만 스냅샷은 계속 블록 3을 반영합니다. 따라서 4개 블록에 대한 요금이 계정에 청구됩니다.

![Azure Storage 리소스](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>시나리오 4

시나리오 4에서는 기본 Blob이 완전히 업데이트되었으며 원래 블록을 하나도 포함하지 않습니다. 따라서 8개 고유 블록 모두에 대한 요금이 계정에 청구됩니다. 이 시나리오는 [UploadFromFile] [dotnet_UploadFromFile], [UploadText] [dotnet_UploadText], [UploadFromStream] [dotnet_UploadFromStream] 또는 [UploadFromByteArray] [dotnet_UploadFromByteArray]와 같은 업데이트 메서드를 사용 하는 경우에 발생할 수 있습니다. 이러한 메서드는 blob의 모든 콘텐츠를 대체 합니다.

![Azure Storage 리소스](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>다음 단계

- [.NET에서 Blob 스냅샷 만들기 및 관리](snapshots-manage-dotnet.md)
- [증분 스냅샷을 사용하여 Azure 관리되지 않는 VM 디스크 백업](../../virtual-machines/windows/incremental-snapshots.md)
