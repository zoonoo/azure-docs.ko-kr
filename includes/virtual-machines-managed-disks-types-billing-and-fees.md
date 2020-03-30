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
ms.openlocfilehash: 2303d36e93cecfca03894a8b0e55458c03b13d78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73412989"
---
**아웃바운드 데이터 전송**: [아웃바운드 데이터 전송](https://azure.microsoft.com/pricing/details/bandwidth/) (Azure 데이터 센터에서 데이터 전송) 시 대역폭 사용량에 대해 청구가 발생합니다.

**트랜잭션**: 표준 관리 디스크에서 수행하는 트랜잭션 수에 대한 요금이 청구됩니다. 표준 SSD의 경우 처리량의 256KiB 이하의 각 I/O 작업은 단일 I/O 작업으로 간주됩니다. 처리량의 256KiB보다 큰 I/O 작업은 크기 256 KiB의 다중 I/O로 간주됩니다. 표준 HDD의 경우 각 IO 작업은 I/O 크기에 관계없이 단일 트랜잭션으로 간주됩니다.

트랜잭션 비용을 포함하여 관리 디스크의 가격 책정에 대한 자세한 내용은 [관리 디스크 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks)을 참조하십시오.

### <a name="ultra-disk-vm-reservation-fee"></a>울트라 디스크 VM 예약 수수료

Azure VM에는 울트라 디스크와 호환되는지 나타내는 기능이 있습니다. 울트라 디스크 호환 VM은 컴퓨팅 VM 인스턴스와 블록 스토리지 배율 단위 간에 전용 대역폭 용량을 할당하여 성능을 최적화하고 대기 시간을 줄입니다. VM에 이 기능을 추가하면 울트라 디스크를 연결하지 않고 VM에서 울트라 디스크 기능을 활성화한 경우에만 적용되는 예약 요금이 발생합니다. 울트라 디스크 호환 VM에 울트라 디스크가 연결되면 이 요금이 적용되지 않습니다. 이 요금은 VM에서 프로비전되는 vCPU당 부과됩니다. 

> [!Note]
> [제한된 코어 VM 크기의](../articles/virtual-machines/linux/constrained-vcpu.md)경우 예약 수수료는 제한된 코어가 아닌 실제 vCPU 수를 기준으로 합니다. Standard_E32 8s_v3 경우 32코어를 기준으로 예약 수수료가 부과됩니다. 

울트라 디스크 가격 세부 정보는 [Azure 디스크 가격 책정 페이지를](https://azure.microsoft.com/pricing/details/managed-disks/) 참조하십시오.

### <a name="azure-disk-reservation"></a>Azure 디스크 예약

디스크 예약은 1년 동안 디스크 스토리지를 할인된 가격으로 구매할 수 있는 옵션으로 총 비용을 절감할 수 있습니다. 디스크 예약을 구매할 때 대상 리전에서 특정 디스크 SKU(예: 미국 동부 2 지역의 10P30(1TiB) 프리미엄 SSD를 1년 동안 선택합니다. 예약 환경은 VM(예약된 가상 시스템) 인스턴스와 유사합니다. VM 및 디스크 예약을 번들로 묶어 비용을 최대화할 수 있습니다. 현재 Azure Disks 예약은 모든 생산 지역에서 P30(1TiB)에서 P80(32TiB)까지 프리미엄 SSD SUS에 대한 1년 약정 플랜을 제공합니다. 예약 디스크 가격 책정에 대한 자세한 내용은 [Azure 디스크 가격 책정 페이지를](https://azure.microsoft.com/pricing/details/managed-disks/)참조하십시오.