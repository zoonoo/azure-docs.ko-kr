---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 07/07/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 66380e0bab0a83d086ebebb5e595908cecd11643
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89664142"
---
이 문서는 Azure Virtual Machines와 Azure 디스크를 결합 하는 경우 디스크 성능 및 작동 방식을 설명 하는 데 도움이 됩니다. 또한 디스크 IO 및 성능 최적화를 위해 수행할 수 있는 변경에 대 한 병목 상태를 진단 하는 방법에 대해서도 설명 합니다.

## <a name="how-does-disk-performance-work"></a>디스크 성능은 어떻게 작동 하나요?
Azure 가상 머신은 가상 머신 유형 및 크기에 따라 IOPS 및 처리량 성능 제한을 갖습니다. 가상 컴퓨터에 연결할 수 있는 OS 디스크 및 데이터 디스크에는 자체 IOPs 및 처리량 한도가 있습니다. 가상 컴퓨터에서 실행 중인 응용 프로그램이 가상 컴퓨터 또는 연결 된 디스크에 할당 된 것 보다 더 많은 IOPS 또는 처리량을 요청 하면 응용 프로그램의 성능이 저하 됩니다. 이 경우 응용 프로그램은 최적의 성능을 경험 하 고 대기 시간이 증가 하는 것과 같은 몇 가지 부정적인 결과를 발생 시킬 수 있습니다. 이를 구체화 하기 위해 몇 가지 예제를 실행 해 보겠습니다. 이러한 예제를 쉽게 수행할 수 있도록 하기 위해 IOPs만 볼 수 있지만 동일한 논리가 처리량에도 적용 됩니다.

## <a name="disk-io-capping"></a>디스크 IO 50,
설정:
- Standard_D8s_v3 
    - 캐시 되지 않은 IOPS: 12800
- E30 OS 디스크
    - IOPS: 500 
- 2 E30 데이터 디스크
    - IOPS: 500

![디스크 수준 50,](media/vm-disk-performance/disk-level-throttling.jpg)

가상 컴퓨터에서 실행 되는 응용 프로그램은 가상 컴퓨터에 1만 IOPs를 요구 하는 요청을 수행 합니다. Standard_D8s_v3 가상 머신은 최대 12800 IOPs를 실행할 수 있기 때문에 VM에서 모든 것을 허용 합니다. 이러한 1만 IOPs 요청은 서로 다른 디스크에 대 한 세 가지 요청으로 분류 됩니다. 1000 IOPs가 운영 체제 디스크에 대해 요청 되 고 4500 IOPs가 각 데이터 디스크에 요청 됩니다. 연결 된 모든 디스크는 E30 디스크 이며 500 IOPs만 처리할 수 있으므로 각각 500 IOPs로 다시 응답 합니다. 그러면 연결 된 디스크에 의해 응용 프로그램의 성능이 저하 되며 1500 IOPs만 처리할 수 있습니다. 프리미엄 SSD P30 디스크와 같이 사용이 더 나은 디스크를 사용 하는 경우 1만 IOPS의 최고 성능으로 작업할 수 있습니다.

## <a name="virtual-machine-io-capping"></a>가상 컴퓨터 IO 50,
설정:
- Standard_D8s_v3 
    - 캐시 되지 않은 IOPS: 12800
- P30 OS 디스크
    - IOPS: 5000 
- 2 P30 데이터 디스크 
    - IOPS: 5000

![가상 컴퓨터 수준 50,](media/vm-disk-performance/vm-level-throttling.jpg)

가상 머신에서 실행 되는 응용 프로그램은 15000 IOPs를 요구 하는 요청을 수행 합니다. 아쉽게도 Standard_D8s_v3 가상 머신은 12800 IOPs를 처리 하기 위해서만 프로 비전 됩니다. 이를 통해 응용 프로그램은 가상 머신 제한에 의해 제한 되며 할당 된 12800 IOPs를 할당 해야 합니다. 그러면 요청 된 12800 IOPs가 서로 다른 디스크에 대 한 세 가지 요청으로 나뉩니다. 4267 IOPs가 운영 체제 디스크에 대해 요청 되 고 4266 IOPs가 각 데이터 디스크에 요청 됩니다. 연결 된 모든 디스크는 5000 IOPs를 처리할 수 있는 P30 디스크 이므로 요청 된 양만큼 다시 응답 합니다.