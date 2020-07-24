---
title: 파일 포함
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4f40a821a0a639fa117dc0844146f28d887166e1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102832"
---
증분 스냅숏은 관리 디스크의 지정 시간 백업으로, 마지막 스냅숏 이후의 변경 내용 으로만 구성 됩니다. 증분 스냅숏에서 디스크를 복원 하는 경우 시스템은 증분 스냅숏을 만들 때 디스크의 지정 시간 백업을 나타내는 전체 디스크를 재구성 합니다. 관리 디스크 스냅숏에 대 한이 새로운 기능을 사용 하면를 선택 하지 않는 한 개별 스냅숏이 있는 전체 디스크를 저장할 필요가 없기 때문에 더욱 비용 효율적일 수 있습니다. 전체 스냅숏과 마찬가지로 증분 스냅숏은 전체 관리 디스크 또는 전체 스냅숏을 만드는 데 사용할 수 있습니다.

증분 스냅숏과 전체 스냅숏 간에는 몇 가지 차이점이 있습니다. 증분 스냅숏은 디스크의 저장소 유형과 관계 없이 항상 표준 Hdd 저장소를 사용 하는 반면, 전체 스냅숏은 프리미엄 Ssd를 사용할 수 있습니다. Premium Storage에서 전체 스냅숏을 사용 하 여 VM 배포를 강화 하는 경우 [공유 이미지 갤러리](../articles/virtual-machines/linux/shared-image-galleries.md)의 standard Storage에서 사용자 지정 이미지를 사용 하는 것이 좋습니다. 이는 저렴 한 비용으로 더 광범위 한 확장을 실현 하는 데 도움이 됩니다. 또한 증분 스냅숏은 ZRS ( [영역 중복 저장소](../articles/storage/common/storage-redundancy-zrs.md) )를 사용 하 여 더 높은 안정성을 제공할 수 있습니다. 선택한 지역에서 ZRS을 사용할 수 있는 경우 증분 스냅숏은 ZRS을 자동으로 사용 합니다. ZRS를 지역에서 사용할 수 없는 경우 스냅숏은 기본적으로 LRS ( [로컬 중복 저장소](../articles/storage/common/storage-redundancy-lrs.md) )로 사용 됩니다. 이 동작을 재정의 하 고 하나를 수동으로 선택할 수 있지만 권장 되지는 않습니다.

또한 증분 스냅숏은 관리 디스크에만 사용할 수 있는 차등 기능을 제공 합니다. 이를 통해 동일한 관리 디스크의 두 증분 스냅숏 간의 변경 내용을 블록 수준까지 가져올 수 있습니다. 이 기능을 사용 하 여 지역 간에 스냅숏을 복사할 때 데이터 공간을 줄일 수 있습니다.  예를 들어 첫 번째 증분 스냅숏을 다른 지역의 기본 blob으로 다운로드할 수 있습니다. 후속 증분 스냅숏의 경우 마지막 스냅숏 이후의 변경 내용만 기본 blob에 복사할 수 있습니다. 변경 내용을 복사한 후에는 다른 지역에 있는 디스크의 지정 시간 백업을 나타내는 기본 blob에 대 한 스냅숏을 만들 수 있습니다. 기본 blob 또는 다른 지역에 있는 기본 blob의 스냅숏에서 디스크를 복원할 수 있습니다.

:::image type="content" source="media/virtual-machines-disks-incremental-snapshots-description/incremental-snapshot-diagram.png" alt-text="지역에 걸쳐 복사 되는 증분 스냅숏을 보여 주는 다이어그램입니다. 스냅숏은 최종적으로 각 스냅숏으로 페이지 blob을 형성할 때까지 다양 한 API 호출을 수행 합니다.":::

증분 스냅숏은 사용 된 크기에 대해서만 청구 됩니다. [Azure 사용량 보고서](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)를 살펴보면 사용 중인 스냅숏의 크기를 찾을 수 있습니다. 예를 들어 스냅샷의 사용된 데이터 크기가 10GiB이면 **일별** 사용량 보고서에 10GiB/(31일) = 0.3226GiB가 소비량으로 표시됩니다.
