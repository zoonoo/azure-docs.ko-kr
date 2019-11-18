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
ms.openlocfilehash: 489ac7fa37c10a27de971151f0be35c647d2186f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74116715"
---
Azure HPC에 최적화 된 Vm (가상 머신)은 다양 한 실제 응용 프로그램에 대 한 리더십 클래스 성능, MPI 확장성 및 비용 효율성을 제공 하도록 설계 되었습니다.
 
## <a name="infiniband-networking-for-large-scale-hpc"></a>대규모 HPC 용 InfiniBand 네트워킹
HBv2 Vm 기능 200 g b/초 Mellanox HDR InfiniBand, HB와 HC Vm 모두 100 g b/초 Mellanox EDR InfiniBand. 이러한 각 VM 유형은 최적화 되 고 일관 된 RDMA 성능을 위해 차단 되지 않는 fat 트리에 연결 됩니다.

HBv2 Vm은 적응 라우팅 및 DCT (표준 RC 및 UD 전송에 대 한 추가)를 지원 합니다. 이러한 기능은 응용 프로그램의 성능, 확장성 및 일관성을 향상 시키고 이러한 기능을 사용 하는 것이 좋습니다.  

HBv2, HB 및 HC Vm은 표준 Mellanox/OFED 드라이버를 지원 합니다. 따라서 모든 RDMA 동사 및 MPI 형식이 지원 됩니다. 이러한 각 VM 유형은 응용 프로그램 성능 향상을 위해 MPI collectives의 하드웨어 기반 오프 로드도 지원 합니다.
 
원본 H 시리즈 Vm의 기능 56 g b/초 Mellanox InfiniBand. H 시리즈에서 InfiniBand 인터페이스를 활용 하기 위해 고객은 Azure Marketplace에서이 VM 유형과 관련 된 공식적으로 지원 되는 이미지를 사용 하 여 배포 해야 합니다. 


## <a name="hbv2-series"></a>HBv2 시리즈
HBv2 시리즈 Vm은 유체 dynamics, 유한 요소 분석 및 저수지 시뮬레이션 등 메모리 대역폭을 기반으로 하는 응용 프로그램에 최적화 되어 있습니다. HBv2 Vm 기능 120 AMD EPYC 7742 프로세서 코어, CPU 코어 당 4gb RAM 및 동시 다중 스레딩은 없습니다. 각 HBv2 VM은 최대 340 g b/초 메모리 대역폭을 제공 하 고 최대 4 테라플롭 FP64 compute를 제공 합니다. 

Premium Storage: 지원됨

| 크기 | vCPU | 프로세서 | 메모리(GB) | 메모리 대역폭 (GB/초) | 기본 CPU 빈도 (GHz) | 모든 코어 빈도 (GHz, 최고) | 단일 코어 빈도 (GHz, 최고) | RDMA 성능 (Gb/s) | MPI 지원 | 임시 저장소 (GB) | 최대 데이터 디스크 수 | 최대 이더넷 Nic |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs | 120 | AMD EPYC 7742 | 480 | 350 | 2.45 | 2.45 | 3.4 | 200 | 모두 | 480 + 960 | 8 | 1 |

<br>

## <a name="hb-series"></a>HB 시리즈
HB 시리즈 VM은 유체 역학, 명시적 유한 요소 분석 및 날씨 모델링과 같이 메모리 대역폭으로 구동되는 애플리케이션에 최적화되었습니다. HB Vm 기능 60 AMD EPYC 7551 프로세서 코어, CPU 코어 당 4gb RAM 및 동시 다중 스레딩은 없습니다. HB VM은 최대 260 g b/초의 메모리 대역폭을 제공 합니다.  

ACU: 199-216

Premium Storage: 지원됨

Premium Storage 캐싱: 지원됨

| 크기 | vCPU | 프로세서 | 메모리(GB) | 메모리 대역폭 (GB/초) | 기본 CPU 빈도 (GHz) | 모든 코어 빈도 (GHz, 최고) | 단일 코어 빈도 (GHz, 최고) | RDMA 성능 (Gb/s) | MPI 지원 | 임시 저장소 (GB) | 최대 데이터 디스크 수 | 최대 이더넷 Nic |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | 모두 | 700 | 4 | 1 |

<br>

## <a name="hc-series"></a>HC 시리즈
HC 시리즈 Vm은 암시적 유한 요소 분석, 분자 dynamics 및 계산 연금술과 같이 조밀한 계산으로 구동 되는 응용 프로그램에 최적화 되어 있습니다. HC VM에는 44개의 Intel Xeon Platinum 8168 프로세서 코어 및 CPU 코어당 8GB의 RAM이 탑재되어 있으며 하이퍼스레딩은 없습니다. Intel Xeon Platinum 플랫폼은 intel 수학 커널 라이브러리와 같은 소프트웨어 도구에 대 한 다양 한 소프트웨어 에코 시스템을 지원 합니다. 

ACU: 297-315

Premium Storage: 지원됨

Premium Storage 캐싱: 지원됨


| 크기 | vCPU | 프로세서 | 메모리(GB) | 메모리 대역폭 (GB/초) | 기본 CPU 빈도 (GHz) | 모든 코어 빈도 (GHz, 최고) | 단일 코어 빈도 (GHz, 최고) | RDMA 성능 (Gb/s) | MPI 지원 | 임시 저장소 (GB) | 최대 데이터 디스크 수 | 최대 이더넷 Nic |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon 플래티넘 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | 모두 | 700 | 4 | 1 |


<br>

## <a name="h-series"></a>H 시리즈
H 시리즈 Vm은 높은 CPU 주파수 또는 핵심 요구 사항 당 대용량 메모리를 기반으로 하는 응용 프로그램에 최적화 되어 있습니다. H 시리즈 Vm은 8 또는 16 Intel Xeon E5 2667 v3 프로세서 코어, CPU 코어 당 최대 14gb RAM, 하이퍼스레딩을 갖지 않습니다. H 시리즈 VM은 최대 80 g b/초의 메모리 대역폭을 제공 합니다.

ACU: 290-300

Premium Storage: 지원되지 않음

Premium Storage 캐싱: 지원되지 않음

| 크기 | vCPU | 프로세서 | 메모리(GB) | 메모리 대역폭 (GB/초) | 기본 CPU 빈도 (GHz) | 모든 코어 빈도 (GHz, 최고) | 단일 코어 빈도 (GHz, 최고) | RDMA 성능 (Gb/s) | MPI 지원 | 임시 저장소 (GB) | 최대 데이터 디스크 수 | 최대 이더넷 Nic |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 | 8 | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16 | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 |  - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H8m | 8 | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |

<sup>1</sup> MPI 응용 프로그램의 경우 Fdr InfiniBand 네트워크를 통해 전용 RDMA 백 엔드 네트워크를 사용할 수 있습니다.

<br>
