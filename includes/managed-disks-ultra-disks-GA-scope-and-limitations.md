---
title: 파일 포함
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a5e0e459800e7cb57672518597f3d04a74f53118
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008646"
---
지금은 ultra disks에는 다음과 같은 추가 제한 사항이 있습니다.

현재 ultra disks에 사용할 수 있는 인프라 중복성 옵션은 가용성 영역 뿐입니다. 다른 중복성 옵션을 사용 하는 Vm은 ultra disk를 연결할 수 없습니다.

다음 표에서는에서 사용 가능한 ultra 디스크와 해당 가용성 옵션을 간략하게 설명 합니다.

> [!NOTE]
> 이러한 지역 내의 일부 가용성 영역은 ultra disks를 제공 하지 않습니다.

|영역  |인프라 중복성 없음  |가용성 영역  |
|---------|---------|---------|
|미국 서부     |예         |아니요         |
|미국 서부 2    |아니요         |예         |
|미국 동부     |아니요         |예         |
|미국 동부 2     |아니요         |예         |
|동남 아시아     |아니요         |예         |
|북유럽     |아니요         |예         |
|서유럽     |아니요         |예         |
|영국 남부     |아니요         |예         |

- 는 다음 VM 시리즈 에서만 지원 됩니다.
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- 모든 VM 크기를 모든 지원 되는 지역에서 ultra disks에 사용할 수 있는 것은 아닙니다.
- 는 데이터 디스크로만 사용할 수 있으며 4k 물리적 섹터 크기만 지원 합니다. Ultra Disk의 4K 기본 섹터 크기로 인해 일부 응용 프로그램은 ultra disks와 호환 되지 않습니다. 한 가지 예는 Oracle Database 하는 것입니다 .이 경우에는 ultra disks를 지원 하기 위해 릴리스 12.2 이상이 필요 합니다.  
- 빈 디스크로만 만들 수 있음  
- 현재 디스크 스냅숏, VM 이미지, 가용성 집합, Azure 전용 호스트 또는 Azure disk encryption을 지원 하지 않습니다.
- 는 현재 Azure Backup 또는 Azure Site Recovery와의 통합을 지원 하지 않습니다.
- GA Vm에서 현재 IOPS의 최대 제한은 8만입니다.

Azure ultra disks는 기본적으로 구독 당 최대 16 개의 TiB을 제공 하지만 ultra disks는 요청에 따라 더 많은 용량을 지원 합니다. 용량 증가를 요청 하려면 Azure 지원에 문의 하세요.