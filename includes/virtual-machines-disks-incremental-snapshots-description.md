---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4d2c0a02a48c0e04b501f136f66c28b3f532e2a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79485992"
---
증분 스냅숏은 관리되는 디스크에 대한 시간 백업으로, 촬영 할 때 마지막 스냅숏 이후의 모든 변경 내용으로만 구성됩니다. 증분 스냅샷을 다운로드하거나 사용하지 않으면 전체 VHD가 사용됩니다. 관리되는 디스크 스냅숏에 대한 이 새로운 기능을 사용하면 원하는 경우가 아니면 각 개별 스냅숏에 전체 디스크를 저장할 필요가 없으므로 비용 효율성을 높일 수 있습니다. 일반 스냅숏과 마찬가지로 증분 스냅숏을 사용하여 전체 관리 디스크를 만들거나 일반 스냅숏을 만들 수 있습니다.

증분 스냅숏과 일반 스냅숏 사이에는 몇 가지 차이점이 있습니다. 증분 스냅샷은 디스크의 저장 유형에 관계없이 항상 표준 HDD 스토리지를 사용하지만 일반 스냅샷은 프리미엄 SSD를 사용할 수 있습니다. 프리미엄 저장소에서 일반 스냅숏을 사용하여 VM 배포를 확장하는 경우 [공유 이미지 갤러리의](../articles/virtual-machines/linux/shared-image-galleries.md)표준 저장소에 사용자 지정 이미지를 사용하는 것이 좋습니다. 그것은 당신이 낮은 비용으로 더 큰 규모를 달성하는 데 도움이됩니다. 또한 증분 스냅숏은 [ZRS(영역 중복 저장소)를](../articles/storage/common/storage-redundancy-zrs.md) 통해 더 나은 안정성을 제공할 수 있습니다. 선택한 리전에서 ZRS를 사용할 수 있는 경우 증분 스냅숏에서 ZRS를 자동으로 사용합니다. 리전에서 ZRS를 사용할 수 없는 경우 스냅숏은 기본적으로 [LRS(로컬 중복 저장소)로](../articles/storage/common/storage-redundancy-lrs.md) 설정됩니다. 이 동작을 재정의하고 수동으로 하나를 선택할 수 있지만 권장하지는 않습니다.

증분 스냅숏은 관리되는 디스크에서만 사용할 수 있는 차등 기능도 제공합니다. 이를 통해 동일한 관리 디스크의 두 증분 스냅숏 간에 블록 수준까지 변경 내용을 얻을 수 있습니다. 이 기능을 사용하여 여러 지역에서 스냅샷을 복사할 때 데이터 공간을 줄일 수 있습니다.  예를 들어 첫 번째 증분 스냅숏을 다른 지역의 기본 Blob로 다운로드할 수 있습니다. 후속 증분 스냅숏의 경우 마지막 스냅숏 이후의 변경 내용만 기본 Blob에 복사할 수 있습니다. 변경 내용을 복사한 후 다른 지역의 디스크 백업 지점을 나타내는 기본 Blob에서 스냅숏을 만들 수 있습니다. 기본 Blob또는 다른 지역의 기본 Blob의 스냅숏에서 디스크를 복원할 수 있습니다.

:::image type="content" source="media/virtual-machines-disks-incremental-snapshots-description/incremental-snapshot-diagram.png" alt-text="여러 지역에서 복사된 증분 스냅숏을 묘사한 다이어그램입니다. 스냅숏은 각 스냅숏당 페이지 Blob을 형성할 때까지 다양한 API 호출을 합니다.":::