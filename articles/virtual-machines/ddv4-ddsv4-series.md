---
title: Ddv4 및 Ddsv4 시리즈 - Azure Virtual Machines
description: Dv4, Ddv4, Dsv4 및 Ddsv4 시리즈 VM에 대한 사양입니다.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: 42b42a7477326196546ad445367691192f00569a
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84263248"
---
# <a name="ddv4-and-ddsv4-series"></a>Ddv4 및 Ddsv4 시리즈

Ddv4 및 Ddsv4 시리즈는 하이퍼 스레드 구성의 Intel&reg; Xeon&reg; Platinum 8272CL(Cascade Lake) 프로세서에서 실행되어 대부분의 범용 워크로드에 더 나은 가치 제안을 제공합니다. 모든 코어가 3.4GHz의 터보 클록 속도로 유지되며 [Intel&reg; Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel&reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) 및 [Intel&reg; Advanced Vector Extensions 512(Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)를 갖추고 있습니다. 이 새로운 VM 크기는 [2세대 VM](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2)이 포함된 [Dv3/Dsv3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series)에 비해 로컬 스토리지가 50% 확장되고, 읽기 쓰기 로컬 디스크 IOPS가 향상됩니다.

D 시리즈 사용 사례로는 엔터프라이즈급 애플리케이션, 관계형 데이터베이스, 메모리 내 캐싱 및 분석 등이 있습니다.

> [!IMPORTANT]
> Ddv4 또는 Ddsv4 시리즈를 사용하여 새 Virtual Machine을 배포하고 Linux Image를 사용할 계획이라면 RHEL 8.x, CentOS 8.x 또는 Oracle 7.x 이상이 필요합니다. RHEL 7.x, CentOS 7.x 또는 Orcale 6.x을 사용할 경우 Kernel Panic 오류가 발생합니다. Microsoft는 수정 사항을 적극적으로 배포하고 있습니다. RHEL, CentOS 및 Oracle만 해당합니다. 

## <a name="ddv4-series"></a>Ddv4 시리즈

Ddv4 시리즈 크기는 Intel&reg; Xeon&reg; Platinum 8272CL(Cascade Lake)에서 실행됩니다. Ddv4 시리즈는 대부분의 프로덕션 워크로드에 대한 vCPU, 메모리 및 임시 디스크의 조합을 제공합니다.

새 Ddv4 VM 크기는 빠르고, 더 큰 로컬 SSD 스토리지(최대 2,400GiB)를 포함하며, 임시 스토리지에 대한 빠른 읽기/쓰기를 필요로 하는 애플리케이션이나 캐시 또는 임시 파일에 대한 임시 스토리지를 필요로 하는 애플리케이션처럼 짧은 대기 시간과 고속 로컬 스토리지를 활용하는 애플리케이션용으로 설계되었습니다. 표준 SSD 및 표준 HDD 스토리지를 Ddv4 VM에 연결할 수 있습니다. 원격 데이터 디스크 스토리지는 가상 머신과 별도로 비용이 청구됩니다.

ACU: 195-210

Premium Storage:  지원되지 않음

Premium Storage 캐싱:  지원되지 않음

실시간 마이그레이션: 지원됨

메모리 보존 업데이트: 지원됨

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 스토리지 처리량: IOPS/MBps | 최대 NIC 수/예상 네트워크 대역폭(Mbps) |
|---|---|---|---|---|---|---|
| Standard_D2d_v4 | 2 | 8 | 75 | 4 | 19000/120 | 2/1000 |
| Standard_D4d_v4 | 4 | 16 | 150 | 8 | 38500/242 | 2/2000 |
| Standard_D8d_v4 | 8 | 32 | 300 | 16 | 77000/485 | 4/4000 |
| Standard_D16d_v4 | 16 | 64 | 600 | 32 | 154000/968 | 8/8000 |
| Standard_D32d_v4 | 32 | 128 | 1200 | 32 | 308000/1936 | 8/16000 |
| Standard_D48d_v4 | 48 | 192 | 1800 | 32 | 462000/2904 | 8/24000 |
| Standard_D64d_v4 | 64 | 256 | 2400 | 32 | 615000/3872 | 8/30000 |

## <a name="ddsv4-series"></a>Ddsv4 시리즈

Ddsv4 시리즈는 Intel&reg; Xeon&reg; Platinum 8272CL(Cascade Lake)에서 실행됩니다. Ddsv4 시리즈는 대부분의 프로덕션 워크로드에 대한 vCPU, 메모리 및 임시 디스크의 조합을 제공합니다.

새 Ddsv4 VM 크기는 빠르고, 더 큰 로컬 SSD 스토리지(최대 2,400GiB)를 포함하며, 임시 스토리지에 대한 빠른 읽기/쓰기를 필요로 하는 애플리케이션이나 캐시 또는 임시 파일에 대한 임시 스토리지를 필요로 하는 애플리케이션처럼 짧은 대기 시간과 고속 로컬 스토리지를 활용하는 애플리케이션용으로 설계되었습니다. 

 > [!NOTE]
 >Ddsv4 크기의 가격 및 요금 청구 기준은 Ddv4 시리즈와 동일합니다.

ACU: 195-210

Premium Storage:  지원됨

Premium Storage 캐싱:  지원됨

실시간 마이그레이션: 지원됨

메모리 보존 업데이트: 지원됨

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 스토리지 처리량: IOPS/MBps | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 NIC 수/예상 네트워크 대역폭(Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2ds_v4 | 2 | 8 | 75 | 4 | 19000/120(50) | 3000/48 | 2/1000 |
| Standard_D4ds_v4 | 4 | 16 | 150 | 8 | 38500/242(100) | 6400/96 | 2/2000 |
| Standard_D8ds_v4 | 8 | 32 | 300 | 16 | 77000/485(200) | 12800/192 | 4/4000 |
| Standard_D16ds_v4 | 16 | 64 | 600 | 32 | 154000/968(400) | 25600/384 | 8/8000 |
| Standard_D32ds_v4 | 32 | 128 | 1200 | 32 | 308000/1936(800) | 51200/768 | 8/16000 |
| Standard_D48ds_v4 | 48 | 192 | 1800 | 32 | 462000/2904(1200) | 76800/1152 | 8/24000 |
| Standard_D64ds_v4 | 64 | 256 | 2400 | 32 | 615000/3872(1600) | 80000/1200 | 8/30000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
