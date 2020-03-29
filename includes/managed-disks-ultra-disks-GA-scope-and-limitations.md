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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74935855"
---
현재 울트라 디스크에는 다음과 같은 추가 제한 사항이 있습니다.

- 리전별로 다양한 가용성 영역이 있는 다음 리전에서 지원됩니다.
    - 미국 동부 2
    - 미국 동부
    - 미국 서부 2
    - 동남아시아
    - 북유럽
    - 서유럽
    - 영국 남부 
- 가용성 영역에서만 사용할 수 있음(영역 외부의 가용성 집합 및 단일 VM 배포에는 울트라 디스크를 연결하는 기능이 없음)
- 다음 VM 시리즈에서만 지원됩니다.
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- 울트라 디스크가 지원되는 모든 지역에서 모든 VM 크기를 사용할 수 있는 것은 아닙니다.
- 데이터 디스크로만 사용할 수 있으며 4k 물리적 섹터 크기만 지원합니다. 울트라 디스크의 4K 기본 섹터 크기로 인해 울트라 디스크와 호환되지 않는 일부 응용 프로그램이 있습니다. 한 가지 예로 울트라 디스크를 지원하기 위해 릴리스 12.2 이상이 필요한 Oracle Database가 있습니다.  
- 빈 디스크로만 만들 수 있음  
- 아직 디스크 스냅숏, VM 이미지, 가용성 집합 및 Azure 디스크 암호화를 지원하지 않습니다.
- 아직 Azure 백업 또는 Azure 사이트 복구와의 통합을 지원하지 않습니다.
- GA VM의 IOPS의 현재 최대 제한은 80,000입니다.
- 울트라 디스크로 160,000 IOPS를 달성 할 수있는 VM의 제한된 미리보기에 참여하려면 .com으로 이메일을 보내주십시오. UltraDiskFeedback@microsoft