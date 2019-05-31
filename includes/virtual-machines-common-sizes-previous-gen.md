---
title: 포함 파일
description: 포함 파일
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 05/16/2019
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 464c7bcb510a2f6ab80fb11d722c241ec51a1b16
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391385"
---
이 섹션에서는 이전 세대의 가상 머신 크기에 정보를 제공합니다. 이러한 크기를 계속 사용할 수 있지만 새로운 세대도 사용 가능합니다. 

## <a name="f-series"></a>F 시리즈

F 시리즈는 2.4GHz Intel Xeon® E5-2673 v3(Haswell) 프로세서를 기반으로 하고, Intel Turbo Boost Technology 2.0을 채택하여 최대 3.1GHz의 클럭 속도를 달성할 수 있습니다. Dv2 시리즈의 VM과 동일한 CPU 성능입니다.  

F 시리즈 VM은 더 빠른 CPU를 요구하지만 많은 메모리나 vCPU당 임시 저장소가 필요하지 않은 워크로드에 적합합니다.  분석, 게임 서버, 웹 서버 및 배치 처리 등의 워크로드는 F 시리즈 값을 사용하면 도움이 됩니다.

ACU: 210 - 250

Premium Storage:  지원되지 않음

Premium Storage 캐싱:  지원되지 않음

| 크기         | vCPU | 메모리: GiB | 임시 저장소(SSD) GiB | 최대 임시 스토리지 처리량: IOPS/읽기 MBps/쓰기 MBps | 최대 데이터 디스크/처리량: IOPS | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000 / 46 / 23                                           | 4 / 4x500                         | 2 / 750                 |
| Standard_F2  | 2         | 4           | 32             | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1500                     |
| Standard_F4  | 4         | 8           | 64             | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 3000                     |
| Standard_F8  | 8         | 16          | 128            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 6000                     |
| Standard_F16 | 16        | 32          | 256            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000           |

## <a name="fs-series-sup1sup"></a>Fs 시리즈 <sup>1</sup>

Fs 시리즈는 Premium Storage 외에도 F 시리즈의 모든 기능을 제공합니다.

ACU: 210 - 250

Premium Storage:  지원됨

Premium Storage 캐싱:  지원됨

| 크기 | vCPU | 메모리: GiB | 임시 저장소(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |4 |4000 / 32 (12) |3200 / 48 |2 / 750 |
| Standard_F2s |2 |4 |8 |8 |8000 / 64 (24) |6400 / 96 |2 / 1500 |
| Standard_F4s |4 |8 |16 |16 |16000 / 128 (48) |12800 / 192 |4 / 3000 |
| Standard_F8s |8 |16 |32 |32 |32000 / 256 (96) |25600 / 384 |8 / 6000 |
| Standard_F16s |16 |32 |64 |64 |64000 / 512 (192) |51200 / 768 |8 / 12000 |

MBps = 초당 10^6바이트, GiB = 1024^3바이트

<sup>1</sup> Fs 시리즈 VM에서 제공 가능한 최대 디스크 처리량(IOPS 또는 MBps)은 연결된 디스크의 수, 크기 및 스트라이핑에 따라 제한될 수 있습니다.  자세한 내용은 [고성능을 위한 디자인](../articles/virtual-machines/windows/premium-storage-performance.md)을 참조하세요.  

## <a name="ls-series"></a>Ls 시리즈

Ls 시리즈는 [Intel® Xeon® 프로세서 E5 v3 제품군](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html)을 사용하여 최대 32개의 vCPU를 제공합니다. Ls 시리즈는 G/GS 시리즈와 CPU 성능이 동일하며 vCPU당 8GiB 메모리가 제공됩니다.

Ls 시리즈는 영구 데이터 디스크에서 달성할 수 있는 IOPS를 증가시키기 위해 로컬 캐시를 생성하도록 지원하지 않습니다. 높은 처리량 및 로컬 디스크의 IOPS Ls 시리즈 Vm 하기에 이상적인 단일 VM의 오류가 발생할 경우 지 속성을 달성 하기 위해 여러 Vm 간에 데이터를 복제 하는 Apache Cassandra 및 MongoDB와 같은 NoSQL 저장소입니다.

ACU: 180-240

Premium Storage:  지원됨

Premium Storage 캐싱:  지원되지 않음
 
| 크기          | vCPU | 메모리(GiB) | 임시 스토리지(GiB) | 최대 데이터 디스크 수 | 최대 임시 스토리지 처리량(IOPS/MBps) | 최대 캐시되지 않은 디스크 처리량(IOPS/MBps) | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20000 / 200 | 5000 / 125  | 2 / 4000  | 
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000 / 400 | 10000 / 250 | 4 / 8000  | 
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000 / 800 | 20000 / 500 | 8 / 16000 | 
| Standard_L32s&nbsp;<sup>1</sup> | 32   | 256  | 5630 | 64   | 160000 / 1600   | 40000 / 1000     | 8 / 20000 | 

Ls 시리즈 VM에서 가능한 최대 디스크 처리량은 연결된 디스크 수, 크기 및 스트라이핑에 따라 제한될 수 있습니다. 자세한 내용은 [고성능을 위한 디자인](../articles/virtual-machines/windows/premium-storage-performance.md)을 참조하세요.

<sup>1</sup> 인스턴스는 단일 고객 전용의 하드웨어에 격리되어 있습니다.

## <a name="nvv2-series-preview"></a>NVv2 시리즈(미리 보기)

**최신 권장 크기**: [NVv3 시리즈 (미리 보기)](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nvv2-series-preview)

NVv2 시리즈 가상 머신은 [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU 및 NVIDIA GRID 기술을 기반으로 하며 Intel Broadwell CPU를 탑재하고 있습니다. 이러한 가상 머신은 고객이 데이터를 시각화하고 결과를 시뮬레이트하여 보고 CAD에서 작업하거나 콘텐츠를 렌더링 및 스트림하려고 하는 GPU 가속 그래픽 애플리케이션 및 가상 데스크톱을 대상으로 합니다. 또한 이러한 가상 머신은 인코딩 및 렌더링과 같은 단정밀도 워크로드를 실행할 수 있습니다. NVv2 가상 머신은 Premium Storage를 지원하며 이전 NV 시리즈와 비교했을 때 두 배의 시스템 메모리(RAM)를 제공합니다.  

NVv2 인스턴스의 각 GPU에는 GRID 라이선스가 함께 제공됩니다. 이 라이선스가 있으면 NV 인스턴스를 단일 사용자를 위한 가상 워크스테이션으로 유연하게 사용하거나, 25명의 동시 사용자가 가상 애플리케이션 시나리오를 위해 VM에 연결할 수 있습니다.

| 크기 | vCPU | 메모리: GiB | 임시 저장소(SSD) GiB | GPU | GPU 메모리: GiB | 최대 데이터 디스크 수 | 최대 NIC 수 | 가상 워크스테이션 | 가상 애플리케이션 | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6s_v2 |6 |112 |320 | 1 | 8 | 12 | 4 | 1 | 25 |
| Standard_NV12s_v2 |12 |224 |640 | 2 | 16 | 24 | 8 | 2 | 50 |
| Standard_NV24s_v2 |24 |448 |1280 | 4 | 32 | 32 | 8 | 4 | 100 |

### <a name="standard-a0---a4-using-cli-and-powershell"></a>CLI 및 PowerShell을 사용하는 Standard A0-A4

클래식 배포 모델에서는 CLI와 PowerShell 간에 일부 VM 크기 이름이 약간 다릅니다.

* Standard_A0은 ExtraSmall
* Standard_A1은 Small
* Standard_A2는 Medium
* Standard_A3은 Large
* Standard_A4는 ExtraLarge
