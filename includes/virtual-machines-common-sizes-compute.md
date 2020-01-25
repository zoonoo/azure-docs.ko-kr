---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 16a0fa29c067262f1794528b16abfce662d05605
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748840"
---
<!-- F-series, Fs-series* -->

계산에 최적화 된 VM 크기는 CPU 대 메모리 비율이 높습니다. 이러한 크기는 보통 트래픽 웹 서버, 네트워크 어플라이언스, 일괄 처리 프로세스 및 응용 프로그램 서버에 적합 합니다. 이 문서에서는 vCPUs, 데이터 디스크 및 Nic의 수에 대 한 정보를 제공 합니다. 또한이 그룹의 각 크기에 대 한 저장소 처리량 및 네트워크 대역폭에 대 한 정보도 포함 됩니다.

Fsv2 시리즈는 Intel® Xeon® 플래티넘 8168 프로세서를 기반으로 합니다. 3\.4 g h z의 모든 코어 터보 클록 속도와 3.7 g h z의 최대 싱글 코어 터보 주파수를 제공 합니다. Intel® AVX-512 지침은 Intel 확장 가능한 프로세서에 새로 있습니다. 이러한 지침은 단일 및 배정밀도 부동 소수점 연산에 대 한 벡터 처리 워크 로드에 대해 최대 2 배의 성능 향상을 제공 합니다. 즉, 계산 워크 로드에 대해 매우 빠릅니다.

시간당 가격이 더 낮은 Fsv2 시리즈는 vCPU당 ACU(Azure 컴퓨팅 단위)를 기준으로 하는 Azure 포트폴리오에서 가격 대비 성능이 가장 좋습니다.

## <a name="fsv2-series-sup1sup"></a>Fsv2 시리즈 <sup>1</sup>

ACU: 195~210

Premium Storage: 지원됨

Premium Storage 캐싱: 지원 됨

| 크기             | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시된 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) |
|------------------|--------|-------------|----------------|----------------|--------------------------|--------------------------|-------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4000/31(32)           | 3200/47                | 2 / 875                 |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8000/63(64)           | 6400/95                | 2 / 1750               |
| Standard_F8s_v2  | 8      | 16          | 64             | 16             | 16000/127(128)        | 12800/190              | 4 / 3500               |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000/255(256)        | 25600/380              | 4 / 7000               |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000/512(512)        | 51200/750              | 8 / 14000              |
| Standard_F48s_v2 | 48     | 96          | 384            | 32             | 96000/768 (768)        | 76800 / 1100             | 8 / 21000              |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000/1024(1024)     | 80000/1100             | 8 / 28000              |
| Standard_F72s_v2<sup>2,&nbsp;3</sup> | 72 | 144 | 576         | 32             | 144000/1152(1520)     | 80000/1100             | 8 / 30000              |

<sup>1</sup> Fsv2 시리즈 Vm은 Intel® 하이퍼 스레딩 기술을 기능 합니다.

<sup>2</sup> vcpu를 64 이상 사용 하려면 지원 되는 다음 게스트 운영 체제 중 하나가 필요 합니다.
- Windows Server 2016 이상
- Ubuntu 16.04 LTS 이상, Azure에서 조정 된 커널 (4.15 kernel 이상)
- SLES 12 SP2 이상
- Microsoft에서 제공 하는 LIS package 4.3.1 이상이 설치 된 RHEL 또는 CentOS 버전 6.7 ~ 6.10
- RHEL 또는 CentOS 버전 7.3, Microsoft에서 제공 하는 LIS 패키지 4.2.1 이상 설치 됨
- RHEL 또는 CentOS 버전 7.6 이상
- UEK4 이상을 사용 하 여 Oracle Linux
- Debian 9 (backports 커널, Debian 10 이상)
- 4\.14 커널을 CoreOS 이상

<sup>3</sup> 인스턴스는 단일 고객 전용의 하드웨어에 격리되어 있습니다.
