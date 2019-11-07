---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c847b110cb821ebe3ca7e706fcaec592dcc58fa8
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73600968"
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
- 데이터 디스크로만 사용 가능하며 4k 물리적 섹터 크기만 지원함  
- 빈 디스크로만 만들 수 있음  
- 디스크 스냅숏, VM 이미지, 가용성 집합 및 Azure disk encryption은 아직 지원 하지 않습니다.
- Azure Backup 또는 Azure Site Recovery와의 통합을 아직 지원 하지 않습니다.
- GA Vm에서 현재 IOPS의 최대 제한은 8만입니다.