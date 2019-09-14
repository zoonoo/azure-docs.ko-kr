---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a416d1c6e813be558f034e15576c57efa6073788
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70968596"
---
**아웃바운드 데이터 전송**: [아웃바운드 데이터 전송](https://azure.microsoft.com/pricing/details/bandwidth/) (Azure 데이터 센터에서 데이터 전송) 시 대역폭 사용에 대해 청구가 발생합니다.

**트랜잭션**: 표준 관리형 디스크에서 수행하는 트랜잭션 수에 따라 요금이 청구됩니다. 표준 Ssd의 경우 256 KiB 처리량 보다 작거나 같은 각 i/o 작업은 단일 i/o 작업으로 간주 됩니다. KiB의 처리량이 256 보다 큰 i/o 작업은 크기 256 KiB의 여러 i/o로 간주 됩니다. 표준 Hdd 경우 각 IO 작업은 i/o 크기에 관계 없이 단일 트랜잭션으로 간주 됩니다.

트랜잭션 비용을 포함 하 여 Managed Disks에 대 한 가격 책정에 대 한 자세한 내용은 [가격 책정 Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks)를 참조 하세요.

### <a name="ultra-disk-vm-reservation-fee"></a>Ultra disk VM 예약 요금

Azure Vm은 ultra disks와 호환 되는지 여부를 나타내는 기능을 포함 합니다. 울트라 디스크 호환 VM은 컴퓨팅 VM 인스턴스와 블록 스토리지 배율 단위 간에 전용 대역폭 용량을 할당하여 성능을 최적화하고 대기 시간을 줄입니다. VM에 이 기능을 추가하면 울트라 디스크를 연결하지 않고 VM에서 울트라 디스크 기능을 활성화한 경우에만 적용되는 예약 요금이 발생합니다. 울트라 디스크 호환 VM에 울트라 디스크가 연결되면 이 요금이 적용되지 않습니다. 이 요금은 VM에서 프로비전되는 vCPU당 부과됩니다. 

> [!Note]
> [제약 된 코어 VM 크기](../articles/virtual-machines/linux/constrained-vcpu.md)의 경우 예약 요금은 제약 된 코어가 아닌 실제 vcpus 수를 기반으로 합니다. Standard_E32-8s_v3의 경우 예약 요금은 32 코어를 기준으로 합니다. 

울트라 디스크 가격 책정에 대 한 자세한 내용은 [Azure 디스크 가격 책정 페이지](https://azure.microsoft.com/pricing/details/managed-disks/) 를 참조 하세요.
