---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 10/12/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 8eff9da82fdfa5749fd1c2bc04652d5c8ce8dfd2
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518075"
---
![R = 22.8 k가 강조 표시 된 f i o 출력의 스크린샷](media/vm-disk-performance/utilization-metrics-example/fio-output.jpg)

Standard_D8s_v3는 총 28600 IOPS를 달성할 수 있습니다. 메트릭을 사용 하 여 진행 상황을 조사 하 고 저장소 IO 병목 상태를 식별 하겠습니다. 왼쪽 창에서 **메트릭**을 선택 합니다.

![왼쪽 창에 강조 표시 된 메트릭을 보여 주는 스크린샷](media/vm-disk-performance/utilization-metrics-example/metrics-menu.jpg)

먼저 **VM 캐시 된 IOPS 사용 백분율** 메트릭을 살펴보겠습니다.

![V M 캐시 된 I O P S가 사용 된 비율을 보여 주는 스크린샷](media/vm-disk-performance/utilization-metrics-example/vm-cached.jpg)

이 메트릭은 VM의 캐시 된 IOPS에 할당 된 16000 IOPS의 61%가 사용 됨을 나타냅니다. 이 백분율은 100%가 아니기 때문에 캐시 된 디스크와 저장소 IO 병목 현상이 없음을 의미 합니다. 이제 **VM 캐시 되지 않은 IOPS 사용 백분율** 메트릭을 살펴보겠습니다.

![V M 캐시 되지 않은 I O P S 사용 백분율을 보여 주는 스크린샷](media/vm-disk-performance/utilization-metrics-example/vm-uncached.jpg)

이 메트릭은 100%입니다. VM의 캐시 되지 않은 IOPS에 할당 된 모든 12800 IOPS를 사용 하 고 있음을 알려 줍니다. 이 문제를 해결할 수 있는 한 가지 방법은 추가 IO를 처리할 수 있는 더 큰 크기로 VM의 크기를 변경 하는 것입니다. 그러나이 작업을 수행 하기 전에 연결 된 디스크를 살펴보고 표시 되는 IOPS 수를 확인 하겠습니다. Os 디스크 **IOPS를 사용한 비율**을 확인 하 여 os 디스크를 확인 합니다.

![O S 디스크 i/o가 사용 된 비율을 보여 주는 스크린샷](media/vm-disk-performance/utilization-metrics-example/os-disk.jpg)

이 메트릭은이 P30 OS 디스크에 대해 프로 비전 된 5000 IOPS의 약 90%가 사용 됨을 나타냅니다. 이 백분율은 OS 디스크에 병목 현상이 없음을 의미 합니다. 이제 **사용 된 데이터 디스크 IOPS**를 살펴보면 VM에 연결 된 데이터 디스크를 확인 합니다.

![데이터 디스크 i/o가 사용 된 비율을 보여 주는 스크린샷](media/vm-disk-performance/utilization-metrics-example/data-disks-no-splitting.jpg)

이 메트릭은 연결 된 모든 디스크에서 사용 된 평균 IOPS가 42%를 차지 함을 나타냅니다. 이 백분율은 디스크에 사용 되는 IOPS를 기준으로 계산 되며 호스트 캐시에서 제공 되지 않습니다. 이러한 메트릭에 대 한 *분할* 을 적용 하 고 LUN 값을 분할 하 여이 메트릭을 자세히 살펴보겠습니다.

![분할으로 사용 된 데이터 디스크 i/o를 보여 주는 스크린샷](media/vm-disk-performance/utilization-metrics-example/data-disks-splitting.jpg)

이 메트릭은 LUN 3에 연결 된 데이터 디스크와 2가 프로 비전 된 IOPS의 약 85%를 사용 하 고 있음을 나타냅니다. 다음은 VM 및 디스크 아키텍처에서 IO가 표시 되는 모양에 대 한 다이어그램입니다.

![저장소 i/o 메트릭 예의 다이어그램입니다.](media/vm-disk-performance/utilization-metrics-example/metrics-diagram.jpg)
