---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 04/02/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: ec718449e9f9db3f816d327f3d2483de813d755c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391264"
---
<!-- F-series, Fs-series* -->

컴퓨팅 최적화 VM 크기는 높은 CPU 대 메모리 비율을 가지며 중간 규모의 트래픽 웹 서버, 네트워크 어플라이언스, 일괄 처리 프로세스 및 애플리케이션 서버에 적합합니다. 이 문서에서는 이 그룹화에서 각 크기에 대한 저장소 처리량 및 네트워크 대역폭뿐만 아니라 vCPU, 데이터 디스크 및 NIC의 수에 대한 정보를 제공합니다.

Fsv2 시리즈는 Intel® Xeon® Platinum 8168 프로세서를 기반으로 하며, 3.4GHz의 일관적인 올 코어 터보 클록 속도와 최대 3.7GHz의 싱글 코어 터보 주파수를 제공합니다. Intel 확장 가능한 프로세서에서 새로 도입된 Intel® AVX-512 지침은 단일 및 이중 정밀도 부동 소수점 연산에서 벡터 처리 워크로드에 대한 성능을 최대 2배로 향상시킵니다. 즉, 계산 워크로드를 빠르게 처리합니다. 

시간당 가격이 더 낮은 Fsv2 시리즈는 vCPU당 ACU(Azure 컴퓨팅 단위)를 기준으로 하는 Azure 포트폴리오에서 가격 대비 성능이 가장 좋습니다.

## <a name="fsv2-series-sup1sup"></a>Fsv2 시리즈 <sup>1</sup>

ACU: 195 - 210

Premium Storage:  지원됨

Premium Storage 캐싱:  지원됨

| 크기             | vCPU 수 | 메모리: GiB | 임시 저장소(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) |
|------------------|--------|-------------|----------------|----------------|--------------------------|--------------------------|-------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4000/31(32)           | 3200/47                | 2 / 875                 |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8000/63(64)           | 6400/95                | 2 / 1750               |
| Standard_F8s_v2  | 8      | 16          | 64             | 16             | 16000/127(128)        | 12800/190              | 4 / 3500               |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000/255(256)        | 25600/380              | 4 / 7000               |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000/512(512)        | 51200/750              | 8 / 14000              |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000/1024(1024)     | 80000/1100             | 8 / 28000              |
| Standard_F72s_v2<sup>2, 3</sup> | 72 | 144 | 576         | 32             | 144000/1152(1520)     | 80000/1100             | 8 / 30000              |


<sup>1</sup> Fsv2 시리즈 VM은 Intel® 하이퍼 스레딩 기술 제공

<sup>2</sup> 64개를 초과하는 vCPU에는 지원되는 게스트 OS인 Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 및 LIS 4.2.1을 사용하는 Red Hat Enterprise Linux, CentOS 7.3 또는 Oracle Linux 7.3 중 하나가 필요합니다.

<sup>3</sup> 인스턴스는 단일 고객 전용의 하드웨어에 격리되어 있습니다.