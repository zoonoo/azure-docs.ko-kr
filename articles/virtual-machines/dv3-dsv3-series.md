---
title: Dv3 및 Dsv3 시리즈
description: Dv3 및 Dsv3 시리즈 Vm에 대 한 사양입니다.
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: jushiman
ms.openlocfilehash: 3ac90d3cabc179ecd1c1e421fd026a584f7158e3
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368573"
---
# <a name="dv3-and-dsv3-series"></a>Dv3 및 Dsv3 시리즈

Dv3 시리즈는 Intel® Xeon® 플래티넘 827272CL (Cascade Lake)에서 실행 됩니다. Intel® Xeon® 8171M (Skylake), Intel® Xeon® E5-2673 v4 2.3 g h z (Broadwell) 또는 하이퍼 스레드 구성의 Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) 프로세서를 제공 하므로 대부분의 범용 워크 로드에 대해 더 나은 가치를 제공 합니다. 하이퍼 스레딩으로 이동하기 위해 디스크 및 네트워크 제한이 코어 단위로 조정되는 동안 메모리가 확장되었습니다(~ 3.5GiB/vCPU에서 4GiB/vCPU로). Dv3 시리즈는 더 이상 D/Dv2 시리즈의 높은 메모리 VM 크기를 갖지 않으며, 메모리 최적화 [Ev3 및 Esv3 시리즈](ev3-esv3-series.md)로 이동되었습니다.

예제 D 시리즈 사용 사례에는 엔터프라이즈급 응용 프로그램, 관계형 데이터베이스, 메모리 내 캐싱 및 분석이 포함 됩니다.

## <a name="dv3-series"></a>Dv3 시리즈

Dv3 시리즈 크기는 Intel® Xeon® 플래티넘 8272CL (케스케이드 Lake), Intel® Xeon® 8171M 2.1 g h z (Skylake), Intel® Xeon® E5-2673 v4 2.3 g h z (Broadwell) 또는 intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) 프로세서에서 실행 되는 [intel &reg; 터보 부스트 기술 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)입니다. Dv3 시리즈 크기는 대부분의 프로덕션 워크로드에 적합한 vCPU, 메모리 및 임시 스토리지의 조합을 제공합니다.

데이터 디스크 스토리지는 가상 머신과 별도로 비용이 청구됩니다. Premium Storage 디스크를 사용하려면 Dsv3 크기를 사용합니다. Dsv3 크기의 가격 및 요금 청구 기준은 Dv3 시리즈와 동일합니다.

Dv3 시리즈 Vm은 Intel® Hyper-Threading 기술을 기능 합니다.

[Acu](acu.md): 160-190<br>
[Premium Storage](premium-storage-performance.md): 지원 되지 않음<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원 되지 않음<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원 됨<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원 됨<br>
[VM 생성 지원](generation-2.md): 1 세대<br>
<br>

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 임시 저장소 처리량: IOPS/읽기 MBps/쓰기 MBps | 최대 Nic/네트워크 대역폭 |
|---|---|---|---|---|---|---|
| Standard_D2_v3  | 2  | 8   | 50   | 4  | 3000/46/23     | 2/1000  |
| Standard_D4_v3  | 4  | 16  | 100  | 8  | 6000/93/46     | 2/2000  |
| Standard_D8_v3  | 8  | 32  | 200  | 16 | 12000/187/93   | 4/4000  |
| Standard_D16_v3 | 16 | 64  | 400  | 32 | 24000/375/187  | 8/8000  |
| Standard_D32_v3 | 32 | 128 | 800  | 32 | 48000/750/375  | 8/16000 |
| Standard_D48_v3 | 48 | 192 | 1200 | 32 | 96000/1000/500 | 8/24000 |
| Standard_D64_v3 | 64 | 256 | 1600 | 32 | 96000/1000/500 | 8/30000 |

## <a name="dsv3-series"></a>Dsv3 시리즈

Dsv3 시리즈 크기는 intel® Xeon® 플래티넘 8272CL (케스케이드 Lake), Intel® Xeon® 8171M 2.1 g h z (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) 또는 intel [ &reg; 터보 부스트 기술 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html) 을 사용 하는 intel® xeon® e5-2673 v3 2.4 Ghz (haswell) 프로세서에서 실행 되며 premium storage를 사용 합니다. Dsv3 시리즈 크기는 대부분의 프로덕션 워크로드에 적합한 vCPU, 메모리 및 임시 스토리지의 조합을 제공합니다.

Dsv3 시리즈 Vm은 Intel® Hyper-Threading 기술을 기능 합니다.

[Acu](acu.md): 160-190<br>
[Premium Storage](premium-storage-performance.md): 지원 됨<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원 됨<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원 됨<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원 됨<br>
[VM 생성 지원](generation-2.md): 1 세대 및 2 세대<br>
<br>

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 저장소 처리량: IOPS/MBps (GiB의 캐시 크기) | 최대 버스트 캐시 및 임시 저장소 처리량: IOPS/MBps<sup>1</sup> | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 버스트 캐시 되지 않은 디스크 처리량: IOPS/MBps<sup>1</sup> | 최대 Nic/예상 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_D2s_v3  | 2  | 8   | 16  | 4  | 4000/32 (50)       | 4000/100    |3200/48    | 4000/100   | 2/1000  |
| Standard_D4s_v3  | 4  | 16  | 32  | 8  | 8000/64 (100)      | 8000/200    |6400/96    | 8000/200   | 2/2000  |
| Standard_D8s_v3  | 8  | 32  | 64  | 16 | 16000/128 (200)    | 16000/400   |12800/192  | 16000/400  | 4/4000  |
| Standard_D16s_v3 | 16 | 64  | 128 | 32 | 32000/256 (400)    | 32000/800   |25600/384  | 32000/800  | 8/8000  |
| Standard_D32s_v3 | 32 | 128 | 256 | 32 | 64000/512 (800)    | 64000/1600  |51200/768  | 64000/1600 | 8/16000 |
| Standard_D48s_v3 | 48 | 192 | 384 | 32 | 96000/768 (1200)   | 96000/2000  |76800/1152 | 80000/2000 | 8/24000 |
| Standard_D64s_v3 | 64 | 256 | 512 | 32 | 128000/1024 (1600) | 128000/2000 |80000/1200 | 80000/2000 | 8/30000 |

<sup>1</sup>  Dsv3 시리즈 vm은 디스크 성능을 [버스트](linux/disk-bursting.md) 하 고 한 번에 최대 30 분 동안 최대 버스트를 얻을 수 있습니다.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>기타 크기 및 정보

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)
- [요금 계산기](https://azure.microsoft.com/pricing/calculator/).
- 디스크 유형에 대 한 자세한 내용은 [Azure에서 사용할 수 있는 디스크 유형](disks-types.md) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
