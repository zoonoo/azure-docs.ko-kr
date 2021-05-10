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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92518074"
---
이 문서는 디스크 성능 및 Azure Virtual Machines와 Azure 디스크 결합 시 작동 방식을 파악하는 데 도움이 됩니다. 또한 디스크 IO의 병목 상태 진단 방법과 성능 최적화를 위해 수행할 수 있는 변경 사항에 대해 설명합니다.

## <a name="how-does-disk-performance-work"></a>디스크 성능은 어떻게 작동하나요?
Azure Virtual Machine은 가상 머신 유형 및 크기에 따라 IOPS(초당 입력/출력 작업 수) 및 처리량 성능 제한이 있습니다. OS 디스크와 데이터 디스크를 가상 머신에 연결할 수 있습니다. 디스크에는 자체 IOPS 및 처리량 제한이 있습니다.

가상 머신 또는 연결된 디스크에 할당된 것보다 더 많은 IOPS 또는 처리량을 요청하는 경우 애플리케이션의 성능이 제한됩니다. 제한되면 애플리케이션의 성능이 최적화되지 않습니다. 이는 대기 시간 증가와 같은 부정적인 결과를 초래할 수 있습니다. 이 개념 설명을 위해 몇 가지 예제를 살펴보겠습니다. 이 예제를 쉽게 따라갈 수 있도록 IOPS만 보겠습니다. 그러나 동일한 논리가 처리량에 적용됩니다.

## <a name="disk-io-capping"></a>디스크 IO 제한

**설정:**

- Standard_D8s_v3
  - 캐시되지 않은 IOPS: 12,800
- E30 OS 디스크
  - IOPS: 500
- E30 데이터 디스크 2개 × 2
  - IOPS: 500

![디스크 수준 제한을 보여주는 다이어그램.](media/vm-disk-performance/disk-level-throttling.jpg)

가상 머신에서 실행되는 애플리케이션이 가상 머신에 10,000 IOPS가 필요한 요청을 합니다. Standard_D8s_v3 가상 머신은 최대 12,800 IOPS를 실행할 수 있기 때문에 이 모든 것이 VM에서 허용됩니다.

10,000 IOPS 요청은 서로 다른 디스크에 대한 3개의 요청으로 분류됩니다.

- 운영 체제 디스크에 1,000 IOPS가 요청됩니다.
- 각 데이터 디스크에 4,500 IOPS가 요청됩니다.

연결된 모든 디스크는 E30 디스크이며 500 IOPS만 처리할 수 있습니다. 따라서 각각 500 IOPS로 다시 응답합니다. 애플리케이션의 성능은 연결된 디스크에 의해 제한되며 1,500 IOPS만 처리할 수 있습니다. 프리미엄 SSD P30 디스크와 같이 더 나은 성능의 디스크를 사용하는 경우 애플리케이션은 10,000 IOPS에서 최고 성능으로 작동할 수 있습니다.

## <a name="virtual-machine-io-capping"></a>가상 머신 IO 제한

**설정:**

- Standard_D8s_v3
  - 캐시되지 않은 IOPS: 12,800
- P30 OS 디스크
  - IOPS: 5,000
- P30 데이터 디스크 2개 × 2
  - IOPS: 5,000

![가상 머신 수준 제한을 보여주는 다이어그램.](media/vm-disk-performance/vm-level-throttling.jpg)

가상 머신에서 실행되는 애플리케이션이 15,000 IOPS가 필요한 요청을 합니다. 아쉽게도 Standard_D8s_v3 가상 머신은 12,800 IOPS를 처리하기 위해서만 프로비전됩니다. 애플리케이션은 가상 머신 한도로 제한되며 할당된 12,800 IOPS를 할당해야 합니다.

요청된 12,800 IOPS는 다른 디스크에 대한 세 가지 요청으로 분류됩니다.

- 운영 체제 디스크에 4,267 IOPS가 요청됩니다.
- 각 데이터 디스크에 4,266 IOPS가 요청됩니다.

연결된 디스크는 모두 5,000 IOPS를 처리할 수 있는 P30 디스크입니다. 따라서 요청한 양으로 다시 응답합니다.
