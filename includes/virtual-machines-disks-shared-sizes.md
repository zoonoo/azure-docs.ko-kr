---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 34699ed89e79448d66343021dd624cb872d0172d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471705"
---
지금은 프리미엄 SSD만 공유 디스크를 사용할 수 있습니다. 이 기능을 지원하는 디스크 크기는 P15 이상입니다. 디스크 크기에 따라 `maxShares` `maxShares` 다른 제한이 있을 수 있으며 값을 설정할 때 초과할 수 없습니다.

각 디스크에 대해 디스크를 `maxShares` 동시에 공유할 수 있는 최대 노드 수를 나타내는 값을 정의할 수 있습니다. 예를 들어 2노드 장애 조치 클러스터를 설정하려는 경우 을 `maxShares=2`설정합니다. 최대값은 상한입니다. 노드 수가 지정된 `maxShares` 값보다 작을 경우 노드는 클러스터를 결합하거나(디스크 마운트 또는 마운트 해제) 나갈 수 있습니다.

> [!NOTE]
> 디스크가 `maxShares` 모든 노드에서 분리된 경우에만 값을 설정하거나 편집할 수 있습니다.

다음 표에서는 디스크 `maxShares` 크기별로 허용되는 최대 값을 보여 줍니다.

|디스크 크기  |최대 공유 제한  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

디스크의 IOPS 및 대역폭 제한은 값의 `maxShares` 영향을 받지 않습니다. 예를 들어 P15 디스크의 최대 IOPS는 maxShares = 1 또는 maxShares > 1인지 여부에 관계없이 1100입니다.