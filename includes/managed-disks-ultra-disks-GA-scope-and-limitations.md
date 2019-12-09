---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/18/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b819264895e35c6ef4fe9dc5263444dcac17eaa2
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935855"
---
지금은 ultra disks에는 다음과 같은 추가 제한 사항이 있습니다.

- 는 지역 마다 다양 한 가용성 영역을 사용 하 여 다음 지역에서 지원 됩니다.
    - 미국 동부 2
    - 미국 동부
    - 미국 서부 2
    - 동남 아시아
    - 북유럽
    - 서유럽
    - 영국 남부 
- 가용성 영역에서만 사용할 수 있음(영역 외부의 가용성 집합 및 단일 VM 배포에는 울트라 디스크를 연결하는 기능이 없음)
- 는 다음 VM 시리즈 에서만 지원 됩니다.
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- 모든 VM 크기를 모든 지원 되는 지역에서 ultra disks에 사용할 수 있는 것은 아닙니다.
- 는 데이터 디스크로만 사용할 수 있으며 4k 물리적 섹터 크기만 지원 합니다. Ultra Disk의 4K 기본 섹터 크기로 인해 일부 응용 프로그램은 ultra disks와 호환 되지 않습니다. 한 가지 예는 Oracle Database 하는 것입니다 .이 경우에는 ultra disks를 지원 하기 위해 릴리스 12.2 이상이 필요 합니다.  
- 빈 디스크로만 만들 수 있음  
- 디스크 스냅숏, VM 이미지, 가용성 집합 및 Azure disk encryption은 아직 지원 하지 않습니다.
- Azure Backup 또는 Azure Site Recovery와의 통합을 아직 지원 하지 않습니다.
- GA Vm에서 현재 IOPS의 최대 제한은 8만입니다.
- Ultra disks를 사용 하 여 16만 IOPS를 수행할 수 있는 제한 된 VM 미리 보기에 참여 하려면 UltraDiskFeedback@microsoft를 전자 메일로 보내세요.