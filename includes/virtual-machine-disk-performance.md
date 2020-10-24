---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 4770ac0181c64ef800aa02ba87284c8add357e36
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518074"
---
이 문서는 Azure Virtual Machines 및 Azure 디스크를 결합할 때 디스크 성능과 작동 방식을 설명 합니다. 또한 디스크 IO 및 성능 최적화를 위해 수행할 수 있는 변경에 대 한 병목 상태를 진단 하는 방법에 대해서도 설명 합니다.

## <a name="how-does-disk-performance-work"></a>디스크 성능은 어떻게 작동 하나요?
Azure virtual machines에는 가상 머신 유형과 크기를 기준으로 IOPS (초당 입/출력 작업 수) 및 처리량 성능 제한이 있습니다. OS 디스크 및 데이터 디스크를 가상 컴퓨터에 연결할 수 있습니다. 디스크에는 자체 IOPS 및 처리량 제한이 있습니다.

응용 프로그램의 성능은 가상 컴퓨터 또는 연결 된 디스크에 할당 된 것 보다 더 많은 IOPS 또는 처리량을 요청 하는 경우에는 더 이상 사용할 수 없습니다. 이를 통해 응용 프로그램은 성능 저하를 경험 합니다. 이로 인해 지연 시간이 길어질 수 있습니다. 이러한 개념을 명확 하 게 설명 하는 몇 가지 예제를 살펴보겠습니다. 이러한 예제를 쉽게 수행할 수 있도록 IOPS를 확인 합니다. 그러나 동일한 논리가 처리량에 적용 됩니다.

## <a name="disk-io-capping"></a>디스크 IO 50,

**설치**

- Standard_D8s_v3
  - 캐시 되지 않은 IOPS: 12800
- E30 OS 디스크
  - IOPS: 500
- 2 개의 E30 데이터 디스크 × 2
  - IOPS: 500

![50, 디스크 수준을 보여 주는 다이어그램](media/vm-disk-performance/disk-level-throttling.jpg)

가상 컴퓨터에서 실행 되는 응용 프로그램은 가상 컴퓨터에 1만 IOPS를 요구 하는 요청을 수행 합니다. Standard_D8s_v3 가상 머신은 최대 12800 IOPS를 실행할 수 있기 때문에 VM에서 모든 것을 허용 합니다.

1만 IOPS 요청은 서로 다른 디스크에 대 한 세 가지 요청으로 분류 됩니다.

- 1000 IOPS가 운영 체제 디스크에 요청 되었습니다.
- 각 데이터 디스크에 4500 IOPS를 요청 합니다.

연결 된 모든 디스크는 E30 디스크 이며 500 IOPS만 처리할 수 있습니다. 따라서 각각 500 IOPS로 다시 응답 합니다. 응용 프로그램의 성능은 연결 된 디스크에 의해 제한 되며 1500 IOPS만 처리할 수 있습니다. 응용 프로그램은 프리미엄 SSD P30 디스크와 같이 성능이 더 우수한 디스크를 사용 하는 경우 1만 IOPS에서 최대 성능으로 작동할 수 있습니다.

## <a name="virtual-machine-io-capping"></a>가상 컴퓨터 IO 50,

**설치**

- Standard_D8s_v3
  - 캐시 되지 않은 IOPS: 12800
- P30 OS 디스크
  - IOPS: 5000
- 2 개의 P30 데이터 디스크 × 2
  - IOPS: 5000

![가상 컴퓨터 수준 50,를 보여 주는 다이어그램입니다.](media/vm-disk-performance/vm-level-throttling.jpg)

가상 머신에서 실행 되는 응용 프로그램은 15000 IOPS를 요구 하는 요청을 수행 합니다. 아쉽게도 Standard_D8s_v3 가상 머신은 12800 IOPS를 처리 하기 위해서만 프로 비전 됩니다. 응용 프로그램은 가상 머신 제한에 의해 제한 되며 할당 된 12800 IOPS를 할당 해야 합니다.

요청 된 12800 IOPS는 서로 다른 디스크에 대 한 세 가지 요청으로 분류 됩니다.

- 4267 IOPS가 운영 체제 디스크에 요청 되었습니다.
- 각 데이터 디스크에 4266 IOPS를 요청 합니다.

연결 된 모든 디스크는 5000 IOPS를 처리할 수 있는 P30 디스크입니다. 따라서 요청 된 금액으로 다시 응답 합니다.
