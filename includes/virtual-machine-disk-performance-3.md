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
ms.openlocfilehash: 14e74bfbcd087ccc1d8c5f2f10a8e44ed37cce84
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016549"
---
![메트릭 메뉴](media/vm-disk-performance/utilization-metrics-example/fio-output.jpg)

그러나 Standard_D8s_v3는 총 28600 IOPs를 달성할 수 있습니다. 메트릭을 사용 하면 진행 상황을 조사 하 여 저장소 IO 병목 상태를 파악할 수 있습니다. 먼저 메트릭 단추 왼쪽 메뉴를 찾아 선택 합니다.

![메트릭 메뉴](media/vm-disk-performance/utilization-metrics-example/metrics-menu.jpg)

먼저 **VM 캐시 된 IOPS 사용 백분율** 메트릭을 살펴보겠습니다.

![VM 캐시 된 IOPS 사용 비율](media/vm-disk-performance/utilization-metrics-example/vm-cached.jpg)

이 메트릭은 VM의 캐시 된 IOPs에 할당 된 16000 IOPs를 알려 주는 것입니다. 61%가 사용 중입니다. 즉, 저장소 IO 병목 상태가 100%가 아니기 때문에 캐시 된 디스크와 함께 사용 되지 않습니다. 이제 **VM 캐시 되지 않은 IOPS 사용 백분율** 메트릭을 살펴보겠습니다.

![VM 캐시 되지 않은 IOPS 사용 백분율](media/vm-disk-performance/utilization-metrics-example/vm-uncached.jpg)

이 메트릭은 100% 이며, VM의 캐시 되지 않은 IOPs에 할당 된 모든 12800 IOPs를 사용 하 고 있음을 알려 주십시오. 이를 수정할 수 있는 한 가지 방법은 VM의 크기를 추가 IO를 처리할 수 있는 더 큰 크기로 변경 하는 것입니다. 그러나이 작업을 수행 하기 전에 연결 된 디스크에서 표시 되는 IOPs 수를 살펴보겠습니다. 먼저 os 디스크 **IOPS를 사용**하 여 os 디스크를 살펴보겠습니다.

![OS 디스크 IOPS 사용 비율](media/vm-disk-performance/utilization-metrics-example/os-disk.jpg)

이 메트릭은이 P30 OS 디스크에 대해 프로 비전 된 5000 IOPs의 약 90%가 사용 되 고 있음을 알려 주는 것입니다. 즉, OS 디스크에서 병목 현상이 발생 하지 않습니다. 이제 **사용 된 데이터 디스크 IOPS**를 살펴보면 VM에 연결 된 데이터 디스크를 살펴보겠습니다.

![데이터 디스크 IOPS 사용 비율](media/vm-disk-performance/utilization-metrics-example/data-disks-no-splitting.jpg)

이 메트릭은 연결 된 모든 디스크에서 사용 된 평균 IOPs가 42%를 차지 함을 알려 주는 것입니다. 이 백분율은 디스크에 사용 되는 IOPs를 기준으로 계산 되며 호스트 캐시에서 제공 되지 않습니다. 이러한 메트릭에 대 한 **분할** 을 적용 하 고 LUN 값을 분할 하 여를 확인 하려면이 메트릭을 자세히 살펴보겠습니다.

![분할에서 사용 된 데이터 디스크 IOPS 비율](media/vm-disk-performance/utilization-metrics-example/data-disks-splitting.jpg)

이 메트릭은 LUN 3 및 2에 연결 된 데이터 디스크가 프로 비전 된 IOPs의 85%를 사용 하 고 있음을 알려 주는 것입니다. 다음은 VM 및 디스크 아키텍처에서 IO가 보이는 항목의 다이어그램입니다.

![저장소 IO 메트릭 예제 다이어그램](media/vm-disk-performance/utilization-metrics-example/metrics-diagram.jpg)
