---
title: 가상 컴퓨터 및 디스크 성능-Windows
description: Windows의 성능에 대 한 가상 컴퓨터 및 연결 된 디스크의 작동 방식에 대해 자세히 알아보세요.
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: dec8b0cc33a9fffa7cac1ac9261b3c38ef5a6449
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584122"
---
# <a name="virtual-machine-and-disk-performance-windows"></a>가상 컴퓨터 및 디스크 성능 (Windows)
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>가상 컴퓨터 캐시 되지 않은 및 캐시 된 제한
 Premium storage를 사용 하도록 설정 되 고 프리미엄 저장소 캐싱이 설정 된 가상 컴퓨터에는 두 가지 저장소 대역폭 한도가 있습니다. 예를 들어 Standard_D8s_v3 가상 컴퓨터를 살펴보겠습니다. [Dsv3 시리즈](../dv3-dsv3-series.md) 와 Standard_D8s_v3에 대 한 설명서는 다음과 같습니다.

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

이 VM 및 디스크 조합에서 IO 작업을 만드는 벤치마킹 테스트를 실행 하 [고 Azure에서](disks-benchmarks.md)저장소 io를 벤치 마크 하는 방법에 대 한 모든 것을 알아보세요. 벤치마킹 도구에서 VM 및 디스크 조합이 22800 IOPS를 달성할 수 있음을 확인할 수 있습니다.

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]