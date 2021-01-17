---
title: 가상 머신 및 디스크 성능
description: 가상 컴퓨터 및 연결 된 디스크의 성능을 향상 하는 방법에 대해 자세히 알아보세요.
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 5e9f6ecc733eccf317e3013752ee2f5b0586ea78
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540636"
---
# <a name="virtual-machine-and-disk-performance"></a>가상 머신 및 디스크 성능
[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>가상 컴퓨터 캐시 되지 않은 및 캐시 된 제한
Premium storage 및 premium storage 캐싱에 사용 하도록 설정 된 가상 컴퓨터에는 두 가지 저장소 대역폭 제한이 있습니다. 예를 들어 Standard_D8s_v3 가상 머신을 살펴보겠습니다. [Dsv3 시리즈](dv3-dsv3-series.md) 및 Standard_D8s_v3에 대 한 설명서는 다음과 같습니다.

[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance-2.md)]

이 가상 머신과 IO 활동을 만드는 디스크 조합에서 벤치마킹 테스트를 실행 해 보겠습니다. Azure에서 저장소 IO를 벤치 마크 하는 방법을 알아보려면 [Azure 디스크 저장소에서 응용 프로그램 벤치 마크](disks-benchmarks.md)를 참조 하세요. 벤치마킹 도구에서 VM 및 디스크 조합이 22800 IOPS를 달성할 수 있음을 확인할 수 있습니다.

[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance-3.md)]
