---
title: Azure managed disks에 대 한 성능 계층
description: 관리 디스크의 성능 계층에 대해 알아봅니다.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 11/19/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 28980756ac9e41c9477d687ea9df608b512759e3
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94986786"
---
# <a name="performance-tiers-for-managed-disks"></a>관리 디스크에 대 한 성능 계층

Azure 관리 디스크의 성능은 성능 계층의 형태로 디스크를 만들 때 설정 됩니다. 성능 계층에는 관리 디스크의 IOPS 및 처리량이 결정 됩니다. 프로 비전 된 디스크 크기를 설정 하면 성능 계층이 자동으로 선택 됩니다. 성능 계층은 배포 시 또는 나중에 디스크 크기를 변경 하지 않고 변경할 수 있습니다.

성능 계층을 변경 하면 디스크의 버스트 기능을 사용 하지 않고 더 높은 수요를 준비 하 고 충족할 수 있습니다. 추가 성능이 필요한 기간에 따라 버스트를 사용 하는 대신 성능 계층을 변경 하는 것이 더 비용 효율적일 수 있습니다. 이는 휴일 쇼핑, 성능 테스트 또는 교육 환경 실행과 같이 일관 되 게 높은 수준의 성능이 일시적으로 필요한 이벤트에 적합 합니다. 이러한 이벤트를 처리 하기 위해 필요할 때까지 더 높은 성능 계층을 사용할 수 있습니다. 그런 다음 추가 성능이 더 이상 필요 하지 않은 경우 원래 계층으로 돌아갈 수 있습니다.

## <a name="how-it-works"></a>작동 방식

디스크를 처음 배포 하거나 프로 비전 할 때 해당 디스크의 기준 성능 계층은 프로 비전 된 디스크 크기에 따라 설정 됩니다. 원래 기준선 보다 높은 성능 계층을 사용 하 여 더 높은 수요를 충족할 수 있습니다. 더 이상 해당 성능 수준이 필요 하지 않은 경우 초기 기준 성능 계층으로 돌아갈 수 있습니다.

성능 계층이 변경 됨에 따라 청구는 변경 됩니다. 예를 들어 P10 disk (128 GiB)를 프로 비전 하는 경우 기준 성능 계층은 P10 (500 IOPS 및 100 MBps)로 설정 됩니다. P10 요금으로 청구 됩니다. 디스크 크기를 늘리지 않고 P50 (7500 IOPS 및 250 MBps)의 성능에 맞게 계층을 업그레이드할 수 있습니다. 업그레이드 하는 동안 P50 요금으로 청구 됩니다. 더 이상 성능이 더 이상 필요 하지 않은 경우 P10 계층으로 돌아갈 수 있습니다. 디스크는 P10 요금으로 다시 청구 됩니다.

| 디스크 크기 | 기준 성능 계층 | 로 업그레이드할 수 있습니다. |
|----------------|-----|-------------------------------------|
| 4 GiB | P1 | P2, P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 8 GiB | P2 | P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 16 GiB | P3 | P4, P6, P10, P15, P20, P30, P40, P50 | 
| 32GiB | P4 | P6, P10, P15, P20, P30, P40, P50 |
| 64GiB | P6 | P10, P15, P20, P30, P40, P50 |
| 128GiB | P10 | P15, P20, P30, P40, P50 |
| 256GiB | P15 | P20, P30, P40, P50 |
| 512GiB | P20 | P30, P40, P50 |
| 1TiB | P30 | P40, P50 |
| 2TiB | P40 | P50 |
| 4TiB | P50 | None |
| 8TiB | P60 |  P70, P80 |
| 16TiB | P70 | P80 |
| 32TiB | P80 | None |

청구 정보 [는 관리 디스크 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/)을 참조 하세요.

## <a name="restrictions"></a>제한 사항

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="next-steps"></a>다음 단계

성능 계층을 변경 하는 방법에 대 한 자세한 내용은 [포털](disks-performance-tiers-portal.md) 또는 [PowerShell/CLI](disks-performance-tiers.md) 문서를 참조 하세요.

