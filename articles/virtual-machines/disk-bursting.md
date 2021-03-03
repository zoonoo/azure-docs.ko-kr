---
title: 관리 디스크 버스팅
description: Azure 디스크 및 Azure virtual machines의 디스크 버스트에 대해 알아봅니다.
author: albecker1
ms.author: albecker
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 4024d2b1357f3dda8216e9ebdd2055b28b064d33
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677481"
---
# <a name="managed-disk-bursting"></a>관리 디스크 버스팅
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

Azure [Premium ssd](disks-types.md#premium-ssd) 는 두 가지 버스트 모델을 제공 합니다.

- 요구가 현재 용량을 초과 하는 경우 디스크를 버스트 하는 주문형 버스트 모델 (미리 보기)입니다. 이 모델은 디스크가 버스트 될 때마다 추가 요금을 발생 시킵니다. 크레딧이 아닌 버스트는 512 GiB 보다 큰 디스크 에서만 사용할 수 있습니다.
- 신용 기반 모델입니다 .이 모델을 사용 하면 버스트 크레딧을 신용 버킷에 축적 한 경우에만 디스크가 버스트 됩니다. 이 모델은 디스크가 버스트 될 때 추가 요금이 발생 하지 않습니다. 신용 기반 버스트는 512 GiB 이상 디스크 에서만 사용할 수 있습니다.

또한 [관리 디스크의 성능 계층을 변경할 수](disks-change-performance.md)있으며,이는 워크 로드를 버스트로 실행 하는 경우에 적합 합니다.

|  |신용 기반 버스트  |주문형 버스트  |성능 계층 변경  |
|---------|---------|---------|---------|
| 시나리오|단기 크기 조정 (30 분 이내)에 이상적입니다.|단기 크기 조정에 적합 합니다 (시간이 제한 되지 않음).|작업 부하가 지속적으로 지속적으로 실행 되는 경우에 적합 합니다.|
|비용     |무료         |비용은 가변적입니다. 자세한 내용은 [청구](#billing) 섹션을 참조 하세요.        |각 성능 계층의 비용은 고정 되어 있습니다. 자세한 내용은 [Managed Disks 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/) 을 참조 하세요.         |
|가용성     |프리미엄 Ssd 512 GiB에만 사용할 수 있습니다.         |512 GiB 보다 큰 프리미엄 Ssd에만 사용할 수 있습니다.         |모든 프리미엄 SSD 크기에서 사용할 수 있습니다.         |
|사용 여부     |적격 디스크에서 기본적으로 사용 하도록 설정 됩니다.         |사용자가 사용 하도록 설정 해야 합니다.         |사용자는 계층을 수동으로 변경 해야 합니다.         |

## <a name="common-scenarios"></a>일반적인 시나리오
다음 시나리오는 버스트에서 크게 이점을 누릴 수 있습니다.
- **시작 시간 개선**  – 버스트를 사용 하면 인스턴스가 매우 빠른 속도로 시작 됩니다. 예를 들어 premium 사용 Vm에 대 한 기본 OS 디스크는 P4 디스크 이며, 최대 120 IOPS 및 250MB/s의 프로 비전 된 성능입니다. 버스트를 사용 하는 경우 P4는 최대 3500 IOPS 및 170 m b/초까지 이동 하 여 6X까지 가속화할 수 있습니다.
- **일괄 처리 작업 처리** – 일부 응용 프로그램 워크 로드는 본질적으로 순환 됩니다. 이러한 시간에는 대부분의 시간에 대 한 기본 성능이 필요 하며, 짧은 기간 동안 성능이 더 높습니다. 이에 대 한 예는 적은 양의 디스크 트래픽이 필요한 일별 트랜잭션을 처리 하는 회계 프로그램입니다. 이 프로그램은 매월 끝에 훨씬 더 많은 양의 디스크 트래픽이 필요한 보고서를 조정 하는 것을 완료 합니다.
- **트래픽** 급증 – 웹 서버와 해당 응용 프로그램은 언제 든 지 트래픽을 발생 시킬 수 있습니다. 웹 서버가 버스트를 사용 하는 Vm 또는 디스크에 의해 지원 되는 경우 서버는 트래픽 급증을 처리 하는 데 더 적합 합니다. 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]

## <a name="next-steps"></a>다음 단계

주문형 버스트를 사용 하도록 설정 하려면 [주문형 버스트 사용](disks-enable-bursting.md)을 참조 하세요.
버스트 리소스를 파악 하는 방법을 알아보려면 [디스크 버스트 메트릭](disks-metrics.md)을 참조 하세요.
