---
title: 가상 컴퓨터 및 디스크 성능-Linux
description: 가상 컴퓨터 및 연결 된 디스크가 Linux의 성능에 대해 조합 하 여 작동 하는 방법에 대해 자세히 알아보세요.
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 2c48672bcfd8c552b36e3ae0807135924669c1d9
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591857"
---
# <a name="virtual-machine-and-disk-performance-linux"></a>가상 컴퓨터 및 디스크 성능 (Linux)
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>가상 컴퓨터 캐시 되지 않은 및 캐시 된 제한
Premium storage 및 premium storage 캐싱에 사용 하도록 설정 된 가상 컴퓨터에는 두 가지 저장소 대역폭 제한이 있습니다. 예를 들어 Standard_D8s_v3 가상 머신을 살펴보겠습니다. [Dsv3 시리즈](../dv3-dsv3-series.md) 및 Standard_D8s_v3에 대 한 설명서는 다음과 같습니다.

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

이 가상 머신과 IO 활동을 만드는 디스크 조합에서 벤치마킹 테스트를 실행 해 보겠습니다. Azure에서 저장소 IO를 벤치 마크 하는 방법을 알아보려면 [Azure 디스크 저장소에서 응용 프로그램 벤치 마크](disks-benchmarks.md)를 참조 하세요. 벤치마킹 도구에서 VM 및 디스크 조합이 22800 IOPS를 달성할 수 있음을 확인할 수 있습니다.

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]
