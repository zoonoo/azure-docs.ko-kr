---
title: Azure 디스크 저장소에서 응용 프로그램 벤치 마크
description: Azure에서 애플리케이션을 벤치마킹하는 프로세스를 알아봅니다.
author: roygara
ms.author: rogarana
ms.date: 01/29/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: bfda14acc2e50005e25faafa3037805af871c1df
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99094599"
---
# <a name="benchmark-a-disk"></a>디스크 벤치마크

벤치마킹은 애플리케이션에서 다양한 작업을 시뮬레이션하고 각 작업에 대한 애플리케이션 성능을 측정하는 과정입니다. [고성능을 위한 디자인 문서](premium-storage-performance.md)에 설명된 단계를 사용하여 애플리케이션 성능 요구 사항을 수집했습니다. 응용 프로그램을 호스트 하는 Vm에서 벤치마킹 도구를 실행 하 여 응용 프로그램이 premium Ssd로 달성할 수 있는 성능 수준을 결정할 수 있습니다. 이 문서에서는 Azure premium Ssd로 프로 비전 된 Standard_D8ds_v4 VM을 벤치마킹 하는 예제를 제공 합니다.

Windows 및 Linux에 대해 각각 일반적인 벤치마킹 도구 DiskSpd 및 FIO를 사용 했습니다. 이러한 도구는 작업과 같은 프로덕션을 시뮬레이션하는 여러 스레드를 생성하고 시스템 성능을 측정합니다. 도구를 사용하여 일반적으로 애플리케이션에 대해 변경할 수 없는 블록 크기 및 큐 크기와 같은 매개 변수를 구성할 수도 있습니다. 이렇게 하면 다양 한 유형의 응용 프로그램 작업에 대해 프리미엄 Ssd로 프로 비전 된 높은 규모의 VM에서 최대 성능을 향상 시킬 수 있습니다. 각 벤치마킹 도구에 대해 자세히 알아보려면 [Diskspd](https://github.com/Microsoft/diskspd/wiki/) 및 [fio](http://freecode.com/projects/fio)를 방문 합니다.

아래 예제를 수행 하려면 Standard_D8ds_v4 만들고 4 개의 프리미엄 Ssd를 VM에 연결 합니다. 네 개의 디스크 중에서 호스트 캐싱을 사용 하 여 3을 "없음"으로 구성 하 고 NoCacheWrites 라는 볼륨으로 스트라이프 합니다. 나머지 디스크에 “ReadOnly”로 호스트 캐싱을 구성하고 이 디스크를 사용하여 CacheReads라는 볼륨을 만듭니다. 이 설정을 사용 하 여 Standard_D8ds_v4 VM에서 최대 읽기 및 쓰기 성능을 볼 수 있습니다. Premium Ssd를 사용 하 여 Standard_D8ds_v4을 만드는 방법에 대 한 자세한 단계는 [고성능을 위한 디자인](premium-storage-performance.md)을 참조 하세요.

[!INCLUDE [virtual-machines-disks-benchmarking](../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>다음 단계

[고성능을 위한 디자인](premium-storage-performance.md)에 대 한 문서를 진행 합니다.

이 문서에서는 프로토타입에 대 한 기존 응용 프로그램과 유사한 검사 목록을 만듭니다. 벤치마킹 도구를 사용하여 작업을 시뮬레이션하고 프로토타입 애플리케이션의 성능을 측정할 수 있습니다. 이렇게 하면 애플리케이션 성능 요구 사항과 일치하거나 능가할 수 있는 디스크 제품을 확인할 수 있습니다. 그런 다음, 프로덕션 애플리케이션에 대해 동일한 지침을 구현할 수 있습니다.
