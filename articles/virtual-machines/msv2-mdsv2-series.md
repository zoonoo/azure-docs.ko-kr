---
title: Msv2 시리즈 (미리 보기)-Azure Virtual Machines
description: Msv2 시리즈 Vm에 대 한 사양입니다.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: a7f4757467523837423d52998eb6b8204090e627
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102562574"
---
# <a name="msv2-and-mdsv2-series-medium-memory-preview"></a>Msv2 및 Mdsv2-시리즈 중간 메모리 (미리 보기)


> [!IMPORTANT]
> 에서 양식을 작성 하 여 미리 보기에 조인 합니다 **https://aka.ms/Mv2MedMemoryPreview** .  

Msv2 및 Mdsv2 Medium Memory VM 시리즈는 Intel® Xeon® Platinum 8280 (케스케이드 Lake) 프로세서를 모든 코어 기본 주파수 2.7 GHz 및 4.0 GHz 단일 코어 터보 빈도로 함께 제공 합니다. 이러한 Vm을 통해 고객은 로컬 디스크 및 디스크 없는 옵션을 사용 하 여 유연성을 높일 수 있습니다. 또한 고객은 4 TiB 메모리를 사용 하 여 최대 192 vCPU로 이동 하는 더 많은 CPU 및 메모리가 포함 된 새로운 격리 된 VM 크기 집합에 액세스할 수 있습니다. 


Msv2 및 Mdsv2 시리즈 Vm은 2 세대에 불과합니다. 지원 되는 2 세대 이미지의 하위 집합을 지원 합니다. Msv2 및 Mdsv2 시리즈에 대해 지원 되는 이미지의 전체 목록은 아래를 참조 하세요.  

- Windows Server 2019 이상
- SUSE Linux Enterprise Server 12 SP4 이상 또는 SUSE Linux Enterprise Server 15 SP1 이상
- Red Hat Enterprise Linux 7.6, 7.7, 8.1 이상 
- Oracle Enterprise Linux 7.7 이상

2 세대 가상 컴퓨터에 대 한 자세한 내용은 [Azure의 2 세대 vm에 대 한 지원](./generation-2.md)을 참조 하세요.



[Premium Storage](premium-storage-performance.md): 지원 됨<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원 됨<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원 되지 않음<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원 되지 않음<br>
[VM 생성 지원](generation-2.md): 2 세대<br>
[쓰기 가속기](./how-to-enable-write-accelerator.md): 지원 됨<br>
[가속 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원 됨<br>
[삭제 되는 OS 디스크](ephemeral-os-disks.md): 지원 되지 않음 <br>
<br>
 
## <a name="msv2-medium-memory-diskless"></a>Msv2 보통 메모리 디스크 없는 

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 NIC 수 | 예상 네트워크 대역폭 (Mbps) | 
|---|---|---|---|---|---|---|---|
| Standard_M32ms_v2 | 32 | 875 | 0 | 32 |  20000/500 | 8 | 8000 | 
| Standard_M64s_v2 | 64 | 1024 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M64ms_v2 | 64 | 1792 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M128s_v2 | 128 | 2048 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M128ms_v2 | 128 | 3892 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M192is_v2 | 192 | 2048 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M192ims_v2 | 192 | 4096 | 0 | 64 | 80000/2000 | 8 | 30000 | 

## <a name="mdsv2-medium-memory-with-disk"></a>디스크를 사용 하 여 보통 메모리 Mdsv2  

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 | 최대 캐시 및 임시 저장소 처리량: IOPS/MBps | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 NIC 수 | 예상 네트워크 대역폭 (Mbps) | 
|---|---|---|---|---|---|---|---|---|
| Standard_M32dms_v2 | 32 | 875 | 1024 | 32 | 40000/400 | 20000/500 | 8 | 8000 | 
| Standard_M64ds_v2 | 64 | 1024 | 2048 | 64 | 80000/800 | 40000/1000 | 8 | 16000 | 
| Standard_M64dms_v2 | 64 | 1792 | 2048 | 64 | 80000/800 | 40000/1000 | 8 | 16000 | 
| Standard_M128ds_v2 | 128 | 2048 | 4096 | 64 |160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M128dms_v2 | 128 | 3892 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M192ids_v2 | 192 | 2048 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M192idms_v2 | 192 | 4096 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>기타 크기 및 정보

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

가격 계산기: [가격 계산기](https://azure.microsoft.com/pricing/calculator/)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
