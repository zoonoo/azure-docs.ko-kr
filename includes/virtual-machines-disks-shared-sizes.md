---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/06/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0b185d545e129c941d5df2e8ce86ee684174b666
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008351"
---
지금은 울트라 디스크와 프리미엄 SSD만 공유 디스크를 사용할 수 있습니다. 디스크 크기에 따라 `maxShares` `maxShares` 다른 제한이 있을 수 있으며 값을 설정할 때 초과할 수 없습니다. 프리미엄 SSD의 경우 디스크 공유를 지원하는 디스크 크기는 P15 이상입니다.

각 디스크에 대해 디스크를 `maxShares` 동시에 공유할 수 있는 최대 노드 수를 나타내는 값을 정의할 수 있습니다. 예를 들어 2노드 장애 조치 클러스터를 설정하려는 경우 을 `maxShares=2`설정합니다. 최대값은 상한입니다. 노드 수가 지정된 `maxShares` 값보다 작을 경우 노드는 클러스터를 결합하거나(디스크 마운트 또는 마운트 해제) 나갈 수 있습니다.

> [!NOTE]
> 디스크가 `maxShares` 모든 노드에서 분리된 경우에만 값을 설정하거나 편집할 수 있습니다.

### <a name="premium-ssd-ranges"></a>프리미엄 SSD 범위

다음 표에서는 프리미엄 디스크 크기에 `maxShares` 대해 허용되는 최대 값을 보여 줍니다.

|디스크 크기  |최대 공유 제한  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

디스크의 IOPS 및 대역폭 제한은 값의 `maxShares` 영향을 받지 않습니다. 예를 들어 P15 디스크의 최대 IOPS는 maxShares = 1 또는 maxShares > 1인지 여부에 관계없이 1100입니다.

### <a name="ultra-disk-ranges"></a>울트라 디스크 범위

최소값은 `maxShares` 1, 최대값은 `maxShares` 5입니다. 울트라 디스크에는 크기 제한이 없으며 모든 크기의 울트라 디스크는 `maxShares`최대 값을 포함하여 모든 값을 사용할 수 있습니다.