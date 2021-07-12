---
title: Dv4 및 Dsv4 시리즈 - Azure Virtual Machines
description: Dv4 및 Dsv4 시리즈 VM 사양입니다.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 07ce54e796cdda3cf7661036ba62ee2b6c5a826a
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110454038"
---
# <a name="dv4-and-dsv4-series"></a>Dv4 및 Dsv4 시리즈

Dv4 및 Dsv4 시리즈는 하이퍼 스레드 구성의 Intel &reg;Xeon&reg; Platinum 8272CL(Cascade Lake) 프로세서에서 실행되며 대부분의 범용 워크로드에 대해 더 나은 가치 제안을 제공합니다. 3\.4GHz의 모든 코어 터보 클록 속도를 제공합니다. 

> [!NOTE]
> 질문과 대답은  [로컬 임시 디스크가 없는 Azure VM 크기](azure-vms-no-temp-disk.md)를 참조하세요.
## <a name="dv4-series"></a>Dv4 시리즈

Dv4 시리즈 크기는 Intel&reg; Xeon&reg; Platinum 8272CL(Cascade Lake)에서 실행됩니다. Dv4 시리즈 크기는 대부분의 프로덕션 워크로드에 대해 vCPU, 메모리 및 원격 스토리지 옵션의 조합을 제공합니다. Dv4 시리즈 VM은 [Intel&reg; 하이퍼 스레딩 기술](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)을 특징으로 합니다.

원격 데이터 디스크 스토리지는 가상 머신과 별도로 비용이 청구됩니다. Premium Storage 디스크를 사용하려면 Dsv4 크기를 사용합니다. Dsv4 크기의 가격 및 요금 청구 기준은 Dv4 시리즈와 동일합니다.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): 지원되지 않음<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원되지 않음<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원됨<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원됨<br>
[VM 생성 지원](generation-2.md): 1세대 및 2세대<br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원됨(*최소 4개의 vCPU 필요*)<br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원되지 않음 <br>
<br>

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 NIC 수|예상 네트워크 대역폭(Mbps) |
|---|---|---|---|---|---|---|
| Standard_D2_v4 | 2 | 8 | 원격 스토리지 전용 | 4 | 2|1000 |
| Standard_D4_v4 | 4 | 16  | 원격 스토리지 전용 | 8 | 2|2000 |
| Standard_D8_v4 | 8 | 32 | 원격 스토리지 전용 | 16 | 4|4000 |
| Standard_D16_v4 | 16 | 64 | 원격 스토리지 전용 | 32 | 8|8000 |
| Standard_D32_v4 | 32 | 128 | 원격 스토리지 전용 | 32 | 8|16000 |
| Standard_D48_v4 | 48 | 192 | 원격 스토리지 전용 | 32 | 8|24000 |
| Standard_D64_v4 | 64 | 256 | 원격 스토리지 전용 | 32 | 8|30000 |

## <a name="dsv4-series"></a>Dsv4 시리즈

Dsv4 시리즈 크기는 Intel &reg;Xeon&reg; Platinum 8272CL(Cascade Lake)에서 실행됩니다. Dv4 시리즈 크기는 대부분의 프로덕션 워크로드에 대해 vCPU, 메모리 및 원격 스토리지 옵션의 조합을 제공합니다. Dsv4 시리즈 VM은 [Intel&reg; 하이퍼 스레딩 기술](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)을 특징으로 합니다. 원격 데이터 디스크 스토리지는 가상 머신과 별도로 비용이 청구됩니다.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): 지원됨<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원됨<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원됨<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원됨<br>
[VM 생성 지원](generation-2.md): 1세대 및 2세대<br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원됨(*최소 4개의 vCPU 필요*)<br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원되지 않음 <br>
<br>

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 NIC 수|예상 네트워크 대역폭(Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2s_v4 | 2 | 8  | 원격 스토리지 전용 | 4 | 3200/48 | 2|1000 |
| Standard_D4s_v4 | 4 | 16 | 원격 스토리지 전용 | 8 | 6400/96 | 2|2000 |
| Standard_D8s_v4 | 8 | 32 | 원격 스토리지 전용 | 16 | 12800/192 | 4|4000 |
| Standard_D16s_v4 | 16 | 64  | 원격 스토리지 전용 | 32 | 25600/384 | 8|8000 |
| Standard_D32s_v4 | 32 | 128 | 원격 스토리지 전용 | 32 | 51200/768 | 8|16000 |
| Standard_D48s_v4 | 48 | 192 | 원격 스토리지 전용 | 32 | 76800/1152 | 8|24000 |
| Standard_D64s_v4 | 64 | 256 | 원격 스토리지 전용 | 32 | 80000/1200 | 8|30000 |
