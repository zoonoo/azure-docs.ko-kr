---
title: 포함 파일
description: 포함 파일
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 07/06/2018
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: c16483f4b8ef160c78ff95582faf54c9a9d24a04
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57964225"
---
이 문서에서는 이전 세대의 가상 머신 크기에 대한 정보를 제공합니다. 이러한 크기를 계속 사용할 수 있지만 새로운 세대도 사용 가능합니다.


## <a name="ds-series"></a>DS 시리즈

ACU: 160-250 <sup>1</sup>

Premium Storage:  지원됨

Premium Storage 캐싱:  지원됨

| 크기 | vCPU | 메모리: GiB | 임시 저장소(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3.5 |7 |4 |4,000/32(43) |3,200/32 |2 / 500 |
| Standard_DS2 |2 |7 |14 |8 |8,000/64(86) |6,400/64 |2 / 1000 |
| Standard_DS3 |4 |14 |28 |16 |16,000/128(172) |12,800/128 |4 / 2000 |
| Standard_DS4 |8 |28 |56 |32 |32,000/256(344) |25,600/256 |8 / 4000 |

<sup>1</sup> VM 제품군 다음 CPU의 중 하나에서 실행할 수 있습니다. 2.2 GHz Intel Xeon® E5 2660 v2 2.4ghz Intel Xeon® 2673 E5 v3 (Haswell) 또는 2.3ghz Intel XEON® E5 2673 v4 (Broadwell)
<br>

## <a name="ds-series---memory-optimized"></a>DS 시리즈 - 메모리에 최적화

ACU: 160-250 <sup>1,2</sup>

Premium Storage:  지원됨

Premium Storage 캐싱:  지원됨

| 크기 | vCPU | 메모리: GiB | 임시 저장소(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |8 |8,000/64(72) |6,400/64 |2 / 1000 |
| Standard_DS12 |4 |28 |56 |16 |16,000/128(144) |12,800/128 |4 / 2000 |
| Standard_DS13 |8 |56 |112 |32 |32,000/256(288) |25,600/256 |8 / 4000 |
| Standard_DS14 |16 |112 |224 |64 |64,000/512(576) |51,200/512 |8 / 8000 |

<sup>1</sup> DS 시리즈 VM에서 제공 가능한 최대 디스크 처리량(IOPS 또는 MBps)은 연결된 디스크의 수, 크기 및 스트라이핑에 따라 제한될 수 있습니다.  자세한 내용은 [고성능을 위한 디자인](../articles/virtual-machines/windows/premium-storage-performance.md)을 참조하세요.

<sup>2</sup> VM 제품군 다음 CPU의 중 하나에서 실행할 수 있습니다. 2.2 GHz Intel Xeon® E5 2660 v2 2.4ghz Intel Xeon® 2673 E5 v3 (Haswell) 또는 2.3ghz Intel XEON® E5 2673 v4 (Broadwell)

## <a name="d-series"></a>D 시리즈 

ACU: 160-250 <sup>1</sup>

Premium Storage:  지원되지 않음

Premium Storage 캐싱:  지원되지 않음

| 크기         | vCPU | 메모리: GiB | 임시 저장소(SSD) GiB | 최대 임시 스토리지 처리량: IOPS/읽기 MBps/쓰기 MBps | 최대 데이터 디스크/처리량: IOPS | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1  | 1         | 3.5         | 50             | 3000 / 46 / 23                                           | 4 / 4x500                         | 2 / 500                 |
| Standard_D2  | 2         | 7           | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1000                     |
| Standard_D3  | 4         | 14          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 2000                     |
| Standard_D4  | 8         | 28          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 4000                     |

<sup>1</sup> VM 제품군 다음 CPU의 중 하나에서 실행할 수 있습니다. 2.2 GHz Intel Xeon® E5 2660 v2 2.4ghz Intel Xeon® 2673 E5 v3 (Haswell) 또는 2.3ghz Intel XEON® E5 2673 v4 (Broadwell)
<br>

## <a name="d-series---memory-optimized"></a>D 시리즈 - 메모리에 최적화

ACU: 160-250 <sup>1</sup>

Premium Storage:  지원되지 않음

Premium Storage 캐싱:  지원되지 않음

| 크기         | vCPU | 메모리: GiB | 임시 저장소(SSD) GiB | 최대 임시 스토리지 처리량: IOPS/읽기 MBps/쓰기 MBps | 최대 데이터 디스크/처리량: IOPS | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11 | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1000                     |
| Standard_D12 | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 2000                     |
| Standard_D13 | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 4000                     |
| Standard_D14 | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 8000                |

<sup>1</sup> VM 제품군 다음 CPU의 중 하나에서 실행할 수 있습니다. 2.2 GHz Intel Xeon® E5 2660 v2 2.4ghz Intel Xeon® 2673 E5 v3 (Haswell) 또는 2.3ghz Intel XEON® E5 2673 v4 (Broadwell)
<br>

## <a name="a-series---compute-intensive-instances"></a>A-시리즈 - 계산 집약적 인스턴스

ACU: 225

Premium Storage:  지원되지 않음

Premium Storage 캐싱:  지원되지 않음

A8-A11 및 H 시리즈는 *계산 집약적 인스턴스*라고도 합니다. 이러한 크기를 실행하는 하드웨어는 고성능 컴퓨팅(HPC) 클러스터 애플리케이션, 모델링 및 시뮬레이션을 포함하는 계산 집약적 및 네트워크 집약적 애플리케이션을 위해 디자인되고 최적화되었습니다. A8-A11 시리즈는 Intel Xeon E5-2670 @ 2.6 GHZ를 사용하고 H 시리즈는 Intel Xeon E5-2667 v3 @ 3.2 GHz를 사용합니다.  

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(HDD): GiB | 최대 데이터 디스크 수 | 최대 데이터 디스크 처리량: IOPS | 최대 NIC 수|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 <sup>1</sup> |8 |56 |382 |32 |32x500 |2 |
| Standard_A9 <sup>1</sup> |16 |112 |382 |64 |64x500 |4 |
| Standard_A10 |8 |56 |382 |32 |32x500 |2  |
| Standard_A11 |16 |112 |382 |64 |64x500 |4 |

<sup>1</sup>MPI 애플리케이션의 경우 초단기 대기 시간 및 고대역폭을 제공하는 FDR InfiniBand 네트워크를 통해 전용 RDMA 백 엔드 네트워크를 사용할 수 있습니다.

<br>

## <a name="a-series"></a>A 시리즈

ACU: 50-100

Premium Storage:  지원되지 않음

Premium Storage 캐싱:  지원되지 않음

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(HDD): GiB | 최대 데이터 디스크 수 | 최대 데이터 디스크 처리량: IOPS | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps)  |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0 <sup>1</sup> |1 |0.768 |20 |1 |1x500 |2 / 100 |
| Standard_A1 |1 |1.75 |70 |2 |2x500 |2 / 500  |
| Standard_A2 |2 |3.5 |135 |4 |4x500 |2 / 500 |
| Standard_A3 |4 |7 |285 |8 |8x500 |2 / 1000 |
| Standard_A4 |8 |14 |605 |16 |16x500 |4 / 2000 |
| Standard_A5 |2 |14 |135 |4 |4x500 |2 / 500 |
| Standard_A6 |4 |28 |285 |8 |8x500 |2 / 1000 |
| Standard_A7 |8 |56 |605 |16 |16x500 |4 / 2000 |

<br>

<sup>1</sup> A0 크기는 실제 하드웨어에서 과도하게 구독됩니다. 이 특정 크기만 다른 고객 배포가 실행 중인 워크로드의 성능에 영향을 줄 수 있습니다. 상대적인 성능은 예상 기준으로 아래에 대략적으로 나와 있으며 약 15%의 변동성이 적용됩니다.

### <a name="standard-a0---a4-using-cli-and-powershell"></a>CLI 및 PowerShell을 사용하는 Standard A0-A4

클래식 배포 모델에서는 CLI와 PowerShell 간에 일부 VM 크기 이름이 약간 다릅니다.

* Standard_A0은 ExtraSmall 
* Standard_A1은 Small
* Standard_A2는 Medium
* Standard_A3은 Large
* Standard_A4는 ExtraLarge

## <a name="basic-a"></a>Basic A

Premium Storage:  지원되지 않음

Premium Storage 캐싱:  지원되지 않음

기본 계층 크기는 부하 분산, 크기 자동 조정 또는 메모리 집약 가상 머신이 필요하지 않은 개발 워크로드 및 기타 애플리케이션에 기본적으로 사용됩니다.

|크기–Size\Name | vCPU |메모리|NIC(최대)|최대 임시 디스크 크기 |최대 데이터 디스크(각 1023GB)|최대 IOPS(디스크당 300)|
|---|---|---|---|---|---|---|
|A0\Basic_A0|1|768 MB|2| 20GB|1|1x300|
|A1\Basic_A1|1|1.75 GB|2| 40GB |2|2x300|
|A2\Basic_A2|2|3.5 GB|2| 60GB|4|4x300|
|A3\Basic_A3|4|7 GB|2| 120GB |8|8x300|
|A4\Basic_A4|8|14 GB|2| 240GB |16|16x300|
 


