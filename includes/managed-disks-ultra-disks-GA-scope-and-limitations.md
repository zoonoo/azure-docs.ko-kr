---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: eeda43b972e30c573743c8c59f75cfae61d7ee85
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94413147"
---
지금은 ultra disks에는 다음과 같은 추가 제한 사항이 있습니다.

현재 ultra disks에 사용할 수 있는 인프라 중복성 옵션은 가용성 영역 뿐입니다. 다른 중복성 옵션을 사용 하는 Vm은 ultra disk를 연결할 수 없습니다.

다음 표에서는에서 사용 가능한 ultra 디스크와 해당 가용성 옵션을 간략하게 설명 합니다.

> [!NOTE]
> 다음 목록의 영역에 ultra disk 지원 가능 영역이 없으면 해당 지역의 Vm은 ultra disk를 연결 하기 위해 인프라 중복성 옵션 없이 배포 되어야 합니다.

|영역  |중복성 옵션  |
|---------|---------|
|브라질 남부     |단일 Vm만 (가용성 집합 및 가상 머신 확장 집합은 지원 되지 않음)|
|인도 중부     |단일 Vm만 (가용성 집합 및 가상 머신 확장 집합은 지원 되지 않음)|
|동아시아     |단일 Vm만 (가용성 집합 및 가상 머신 확장 집합은 지원 되지 않음)|
|독일 중서부     |단일 Vm만 (가용성 집합 및 가상 머신 확장 집합은 지원 되지 않음)|
|한국 중부     |단일 Vm만 (가용성 집합 및 가상 머신 확장 집합은 지원 되지 않음)|
|미국 중남부    |단일 Vm만 (가용성 집합 및 가상 머신 확장 집합은 지원 되지 않음)|
|US Gov 애리조나     |단일 Vm만 (가용성 집합 및 가상 머신 확장 집합은 지원 되지 않음)|
|US Gov 버지니아     |단일 Vm만 (가용성 집합 및 가상 머신 확장 집합은 지원 되지 않음)|
|US Gov 텍사스     |단일 Vm만 (가용성 집합 및 가상 머신 확장 집합은 지원 되지 않음)|
|미국 서부     |단일 Vm만 (가용성 집합 및 가상 머신 확장 집합은 지원 되지 않음)        |
|오스트레일리아 중부    |단일 Vm만 (가용성 집합 및 가상 머신 확장 집합은 지원 되지 않음)|
|오스트레일리아 동부     |3 개의 가용성 영역         |
|동남아시아    |3 개의 가용성 영역        |
|캐나다 중부 *     |3 개의 가용성 영역          |
|미국 중부     |3 개의 가용성 영역          |
|미국 동부     |3 개의 가용성 영역          |
|미국 동부 2     |3 개의 가용성 영역         |
|프랑스 중부    |2 개의 가용성 영역        |
|일본 동부    |3 개의 가용성 영역        |
|북유럽    |3 개의 가용성 영역        |
|영국 남부    |3 개의 가용성 영역        |
|서유럽    | 3 개의 가용성 영역|
|미국 서부 2    |3 개의 가용성 영역|

\* 이 지역의 가용성 영역에 대 한 액세스 권한을 얻으려면 Azure 지원에 문의 하세요.

- 는 다음 VM 시리즈 에서만 지원 됩니다.
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [Easv4](../articles/virtual-machines/eav4-easv4-series.md#easv4-series)
    - [Edsv4](../articles/virtual-machines/edv4-edsv4-series.md#edsv4-series)
    - [Esv4](../articles/virtual-machines/ev4-esv4-series.md#esv4-series)
    - [DSv3](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series)
    - [Dasv4](../articles/virtual-machines/dav4-dasv4-series.md#dasv4-series)
    - [Ddsv4](../articles/virtual-machines/ddv4-ddsv4-series.md#ddsv4-series)
    - [Dsv4](../articles/virtual-machines/dv4-dsv4-series.md#dsv4-series)
    - [FSv2](../articles/virtual-machines/fsv2-series.md)
    - [LSv2](../articles/virtual-machines/lsv2-series.md)
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- 모든 VM 크기를 모든 지원 되는 지역에서 ultra disks에 사용할 수 있는 것은 아닙니다.
- 데이터 디스크로만 사용할 수 있습니다. 
- 기본적으로 4k 물리적 섹터 크기를 지원 합니다. 512E 섹터 크기는 일반적으로 사용할 수 있는 제품으로 사용할 수 있지만, [등록](https://aka.ms/ultradisk512e)해야 합니다. 대부분의 응용 프로그램은 4k 섹터 크기와 호환 되지만 일부는 512 바이트 섹터 크기를 요구 합니다. 한 가지 예는 4k 기본 디스크를 지원 하기 위해 릴리스 12.2 이상이 필요한 Oracle Database입니다. 이전 버전의 Oracle DB의 경우 512 바이트 섹터 크기가 필요 합니다.
- 빈 디스크로만 만들 수 있습니다.
- 는 현재 디스크 스냅숏, VM 이미지, 가용성 집합, Azure 전용 호스트 또는 Azure disk encryption을 지원 하지 않습니다.
- 는 현재 Azure Backup 또는 Azure Site Recovery와의 통합을 지원 하지 않습니다.
- 캐시 되지 않은 읽기와 캐시 되지 않은 쓰기만 지원 합니다.
- GA Vm에서 현재 IOPS의 최대 제한은 8만입니다.

Azure ultra disks는 기본적으로 구독 당 최대 16 개의 TiB을 제공 하지만 ultra disks는 요청에 따라 더 많은 용량을 지원 합니다. 용량 증가를 요청 하려면 Azure 지원에 문의 하세요.
