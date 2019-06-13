---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 04/26/2019
ms.author: azcspmt;jonbeck;cynthn;amverma
ms.custom: include file
ms.openlocfilehash: 9b4bdee19c883252e7de140ac7b19babd43d1df8
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755191"
---
Azure H 시리즈 virtual machines (Vm)는 다양 한 실제 HPC 워크 로드에 대 한 효율성 비용과 리더십 수준의 성능, MPI 확장성을 제공 하도록 설계 되었습니다.

HB 시리즈 VM은 유체 역학, 명시적 유한 요소 분석 및 날씨 모델링과 같이 메모리 대역폭으로 구동되는 응용 프로그램에 최적화되었습니다. HB VM에는 60개의 AMD EPYC 7551 프로세서 코어 및 CPU 코어당 4GB의 RAM이 탑재되어 있으며 하이퍼스레딩은 없습니다. AMD EPYC 플랫폼은 260GB/초 이상의 메모리 대역폭을 제공합니다.

HC 시리즈 Vm 기반 계산 화학 암시적 유한 요소 분석, 분자 dynamics 등의 밀도 계산 하 여 응용 프로그램에 대해 최적화 됩니다. HC VM에는 44개의 Intel Xeon Platinum 8168 프로세서 코어 및 CPU 코어당 8GB의 RAM이 탑재되어 있으며 하이퍼스레딩은 없습니다. Intel Xeon 플래티넘 플랫폼 Intel의 소프트웨어 도구 Intel 수학 커널 라이브러리 등의 풍부한 에코 시스템을 지원합니다.

HB와 HC Vm 기능 100gb/sec는 비차단 fat에서 Mellanox EDR InfiniBand RDMA의 일관 된 성능에 대 한 구성을 트리. HB 및 HC Vm 모든 MPI 형식 및 버전 뿐만 아니라 RDMA 동사에도 지원 되는 표준 Mellanox/OFED 드라이버를 지원 합니다.

H 시리즈 Vm은 응용 프로그램의 높은 CPU 주파수 또는 핵심 요구 사항에 따라 큰 메모리 최적화 됩니다. H 시리즈 Vm 기능 8 또는 16 Intel Xeon E5 2667 v3 프로세서 코어, 7 또는 14 GB 당 CPU 코어, ram 및 하이퍼 스레딩이 없습니다. H 시리즈 기능 56gb/sec는 비차단 fat에서 Mellanox FDR InfiniBand RDMA의 일관 된 성능에 대 한 구성을 트리. H 시리즈 Vm을 지원 하며 Intel MPI 5.x 및 MS-MPI 합니다.

## <a name="hb-series"></a>HB 시리즈

ACU: 199-216

Premium Storage: 지원됨

Premium Storage 캐싱: 지원됨

| 크기 | vCPU | 프로세서 | 메모리(GB) | 메모리 대역폭 GB/s | 기본 CPU 주파수 (GHz) | 모든 코어 빈도 (GHz, 최고) | 단일 코어 빈도 (GHz, 최고) | RDMA 성능 (GB/s) | MPI 지원 | 임시 저장소 (GB) | 최대 데이터 디스크 수 | 최대 이더넷 Nic |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | 모두 | 700 | 4 | 1 |

<br>

## <a name="hc-series"></a>HC 시리즈

ACU: 297-315

Premium Storage: 지원됨

Premium Storage 캐싱: 지원됨


| 크기 | vCPU | 프로세서 | 메모리(GB) | 메모리 대역폭 GB/s | 기본 CPU 주파수 (GHz) | 모든 코어 빈도 (GHz, 최고) | 단일 코어 빈도 (GHz, 최고) | RDMA 성능 (GB/s) | MPI 지원 | 임시 저장소 (GB) | 최대 데이터 디스크 수 | 최대 이더넷 Nic |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon 플래티넘 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | 모두 | 700 | 4 | 1 |


<br>

## <a name="h-series"></a>H 시리즈

ACU: 290-300

Premium Storage:  지원되지 않음

Premium Storage 캐싱:  지원되지 않음

| 크기 | vCPU | 프로세서 | 메모리(GB) | 메모리 대역폭 GB/s | 기본 CPU 주파수 (GHz) | 모든 코어 빈도 (GHz, 최고) | 단일 코어 빈도 (GHz, 최고) | RDMA 성능 (GB/s) | MPI 지원 | 임시 저장소 (GB) | 최대 데이터 디스크 수 | 최대 이더넷 Nic |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 | 8 | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16 | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 |  - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H8m | 8 | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |

<sup>1</sup> MPI 응용 프로그램에 대 한 전용된 RDMA 백 엔드 네트워크 FDR InfiniBand 네트워크에서 사용 하도록 설정 합니다.

<br>
