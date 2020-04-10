---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a5e0e459800e7cb57672518597f3d04a74f53118
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008646"
---
현재 울트라 디스크에는 다음과 같은 추가 제한 사항이 있습니다.

현재 울트라 디스크에서 사용할 수 있는 유일한 인프라 중복 옵션은 가용성 영역입니다. 다른 중복 옵션을 사용하는 VM은 울트라 디스크를 연결할 수 없습니다.

다음 표에서는 울트라 디스크를 사용할 수 있는 영역과 해당 가용성 옵션을 간략하게 설명합니다.

> [!NOTE]
> 이러한 리전 내의 일부 가용성 영역은 울트라 디스크를 제공하지 않습니다.

|영역  |인프라 중복성 없음  |가용성 영역  |
|---------|---------|---------|
|미국 서부     |예         |예         |
|미국 서부 2    |예         |예         |
|미국 동부     |예         |예         |
|미국 동부 2     |예         |예         |
|동남아시아     |예         |예         |
|북유럽     |예         |예         |
|서유럽     |예         |예         |
|영국 남부     |예         |예         |

- 다음 VM 시리즈에서만 지원됩니다.
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- 울트라 디스크가 지원되는 모든 지역에서 모든 VM 크기를 사용할 수 있는 것은 아닙니다.
- 데이터 디스크로만 사용할 수 있으며 4k 물리적 섹터 크기만 지원합니다. 울트라 디스크의 4K 기본 섹터 크기로 인해 울트라 디스크와 호환되지 않는 일부 응용 프로그램이 있습니다. 한 가지 예로 울트라 디스크를 지원하기 위해 릴리스 12.2 이상이 필요한 Oracle Database가 있습니다.  
- 빈 디스크로만 만들 수 있음  
- 현재 디스크 스냅숏, VM 이미지, 가용성 집합, Azure 전용 호스트 또는 Azure 디스크 암호화를 지원하지 않습니다.
- 현재 Azure 백업 또는 Azure 사이트 복구와의 통합을 지원하지 않습니다.
- GA VM의 IOPS의 현재 최대 제한은 80,000입니다.

Azure 울트라 디스크는 기본적으로 구독당 리전당 최대 16TiB를 제공하지만 울트라 디스크는 요청에 따라 더 높은 용량을 지원합니다. 용량 증가를 요청하려면 Azure 지원에 문의하십시오.