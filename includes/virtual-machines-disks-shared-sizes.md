---
title: 파일 포함
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/06/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0b185d545e129c941d5df2e8ce86ee684174b666
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008351"
---
지금은 ultra disks 및 premium Ssd만 공유 디스크를 사용 하도록 설정할 수 있습니다. 다른 디스크 크기는 다른 `maxShares` 제한 `maxShares` 값을 가질 수 있습니다. 값을 설정 하는 경우에는이 값을 초과할 수 없습니다. Premium Ssd의 경우 디스크 공유를 지 원하는 디스크 크기는 P15 이상입니다.

각 디스크에 대해 디스크를 동시에 `maxShares` 공유할 수 있는 최대 노드 수를 나타내는 값을 정의할 수 있습니다. 예를 들어 2 노드 장애 조치 (failover) 클러스터를 설정 하려는 경우를 설정 `maxShares=2`합니다. 최대값은 상한 값입니다. 노드 수가 지정 `maxShares` 된 값 보다 적으면 노드가 클러스터를 연결 하거나 탈퇴할 수 있습니다 (디스크를 탑재 또는 분리).

> [!NOTE]
> 디스크가 `maxShares` 모든 노드에서 분리 된 경우에만 값을 설정 하거나 편집할 수 있습니다.

### <a name="premium-ssd-ranges"></a>프리미엄 SSD 범위

다음 표에서는 프리미엄 디스크 크기에 대해 `maxShares` 허용 되는 최대 값을 보여 줍니다.

|디스크 크기  |maxShares 제한  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

디스크에 대 한 IOPS 및 대역폭 제한은 값의 `maxShares` 영향을 받지 않습니다. 예를 들어 P15 디스크의 최대 IOPS는 maxShares = 1 또는 maxShares > 1 인지 여부를 1100입니다.

### <a name="ultra-disk-ranges"></a>Ultra disk 범위

최 솟 `maxShares` 값은 1 이지만 최대값 `maxShares` 은 5입니다. Ultra disks에는 크기 제한이 없습니다. 모든 크기의 `maxShares`경우 최대 값을 포함 하 여 모든 값을 사용할 수 있습니다.