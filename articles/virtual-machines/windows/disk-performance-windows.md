---
title: 가상 머신 및 디스크 성능
description: Vm 및 연결 된 디스크가 성능에 대해 조합 하 여 작동 하는 방법에 대 한 자세한 정보
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 88cf9c28a9d325d617f4b049015f0cd238a2fb31
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016462"
---
# <a name="virtual-machine-and-disk-performance"></a>가상 머신 및 디스크 성능
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>가상 컴퓨터 캐시 되지 않은 및 캐시 된 제한
 Premium storage를 사용 하도록 설정 되 고 프리미엄 저장소 캐싱이 설정 된 가상 컴퓨터에는 두 가지 저장소 대역폭 한도가 있습니다. 예를 들어 Standard_D8s_v3 가상 컴퓨터를 살펴보겠습니다. [Dsv3 시리즈](../dv3-dsv3-series.md) 와 Standard_D8s_v3에 대 한 설명서는 다음과 같습니다.

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

이 VM 및 디스크 조합에서 IO 작업을 만드는 벤치마킹 테스트를 실행 하 [고 Azure에서](disks-benchmarks.md)저장소 io를 벤치 마크 하는 방법에 대 한 모든 것을 알아보세요. 벤치마킹 도구에서 VM 및 디스크 조합이 22800 IOPS를 달성할 수 있음을 확인할 수 있습니다.

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]