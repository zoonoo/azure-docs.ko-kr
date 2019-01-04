---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 050308e1c8de160f1671ded991e550087299ae2f
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285739"
---
# <a name="ultra-ssd-preview-managed-disks-for-azure-virtual-machine-workloads"></a>Azure Virtual Machine 워크로드용 Ultra SSD(미리 보기) Managed Disks

Azure Ultra SSD(미리 보기)는Azure IaaS VM에 대한 높은 처리량, 높은 IOP 및 일관된 짧은 대기 시간 디스크 저장소를 제공합니다. 이 새 제품은 당사의 기존 디스크 제품과 동일한 가용성 수준에서 최상의 성능을 제공합니다. Ultra SSD의 추가 혜택에는 가상 머신을 다시 시작할 필요없이 워크로드와 함께 디스크의 성능을 동적으로 변경하는 기능이 포함됩니다. Ultra SSD는 SAP HANA, 최상위 계층 데이터베이스 및 트랜잭션 작업이 많은 워크로드와 같은 데이터 집약적인 워크로드에 적합합니다.

## <a name="ultra-ssd-features"></a>Ultra SSD 기능

**Managed Disks**: Ultra SSD는 Managed Disks로만 사용할 수 있습니다. Ultra SSD는 관리되지 않는 디스크 또는 페이지 Blob으로 배포될 수 없습니다. Managed Disks를 만들 때 디스크 sku 유형을 UltraSSD_LRS로 지정하고, 필요한 디스크 크기, IOPS 및 처리량을 지정하면 Azure가 알아서 디스크를 만들고 관리해줍니다.  

**Virtual Machines**: Ultra SSD는 Azure Virtual Machine SKU가 지원되는 모든 프리미엄 SSD와 함께 작동하도록 설계되었지만 현재 미리 보기로 제공되고 있으므로 VM 크기가 ES/DS v3로 지정됩니다.

**동적 성능 구성**: Ultra SSD를 통해 가상 머신을 다시 시작할 필요없이 워크로드 요구 사항과 함께 디스크의 성능(IOPS 및 처리량)을 동적으로 변경할 수 있습니다.

## <a name="scalability-and-performance-targets"></a>확장성 및 성능 대상

Ultra SSD를 프로비전하는 경우 디스크의 용량 및 성능을 독립적으로 구성하는 옵션이 있습니다. Ultra SSD는 4GiB에서 최대 64Tib의 다양한 고정된 크기로 제공되며 IOPS 및 처리량을 독립적으로 구성할 수 있는 유연한 성능 구성 모델을 제공합니다. Ultra SSD는 데이터 디스크로만 활용할 수 있습니다. OS 디스크로 프리미엄 SSD를 사용하는 것이 좋습니다.

Ultra SSD의 일부 키 기능은 다음과 같습니다.

- 디스크 용량: Ultra SSD는 4GiB에서 최대 64TiB의 다양한 디스크 크기를 제공합니다.
- 디스크 IOPS: Ultra SSD는 디스크당 최대 160K IOPS의 300IOPS/GiB의 IOPS 제한을 지원합니다. 프로비전한 IOPS를 달성하려면 선택한 디스크 IOPS가 VM IOPS보다 작은지 확인합니다. 최소 디스크 IOPS는 100IOPS입니다.
- 디스크 처리량: Ultra SSD를 사용하여 단일 디스크의 처리량 한도는 디스크당 최대 2000MBps로 각 프로비전된 IOPS에 대해 256KiB/s입니다(여기서 MBps = 초당 10^6바이트). 최소 디스크 처리량은 1MiB입니다.

다음 표에는 다양한 디스크 크기에 지원되는 다양한 구성이 개략적으로 나와 있습니다.  

### <a name="ultra-ssd-managed-disk-offerings"></a>Ultra SSD Managed Disks 제품

|디스크 크기(GiB)  |IOPS 용량  |처리량 용량(MBps)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9,600         |2,000         |
|64     |19,200         |2,000         |
|128     |38,400         |2,000         |
|256     |76,800         |2,000         |
|512     |80,000         |2,000         |
|1,024-65,536(1TiB의 단위로 증가하는 이 범위의 크기)     |160,000         |2,000         |

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

Ultra SSD를 사용하는 경우 다음과 같은 청구 고려 사항이 적용됩니다.

- Managed Disks 크기
- Managed Disks 프로비전된 IOPS
- Managed Disks 프로비전된 처리량
- Ultra SSD VM 예약 요금

### <a name="managed-disk-size"></a>Managed Disks 크기

Managed Disks는 새 Azure VM을 프로비전하는 동안 선택한 VM 크기에 따라 요금이 청구됩니다. Azure는 프로비전된 크기(반올림됨)를 가장 가까운 디스크 크기 옵션에 매핑합니다. 제공되는 디스크 크기에 대한 자세한 내용은 위의 확장성 및 성능 목표 섹션에 있는 표를 참조하세요. 각각의 디스크는 지원되는 프로비전된 디스크 크기에 매핑되고 시간 단위로 적절하게 요금이 청구됩니다. 예를 들어 Ultra SSD 디스크 200GiB를 프로비전하고 20시간 동안 사용한 후 삭제한 경우 디스크 크기 제품 256GiB로 매핑되고 256GiB를 20시간 동안 사용한 만큼 요금이 청구됩니다. 이러한 청구는 디스크에 실제로 기록된 데이터의 볼륨과 관계없이 사용된 계산 시간을 기반으로 한 것입니다.

### <a name="managed-disk-provisioned-iops"></a>Managed Disks 프로비전된 IOPS

IOPS는 애플리케이션이 1초 동안 디스크로 보내는 요청의 수입니다. 입출력 작업은 순차 읽기 또는 쓰기나 임의 읽기 또는 쓰기가 될 수 있습니다. VM에 연결된 디스크 수 또는 디스크 크기를 기반으로 시간별 평균 IOPS 수를 기준으로 요금이 청구됩니다. 제공되는 디스크 IOPS에 대한 자세한 내용은 위의 확장성 및 성능 목표 섹션에 있는 표를 참조하세요.

### <a name="managed-disk-provisioned-throughput"></a>Managed Disks 프로비전된 처리량

처리량은 애플리케이션이 바이트/초로 측정된 지정된 간격의 디스크에 보내는 데이터의 양입니다. 애플리케이션이 대규모 입력/출력 작업을 수행하는 경우 높은 처리량이 필요합니다.  

다음 수식에 표시된 것처럼 처리량과 IOPS 간에 관계가 있습니다. IOPS x IO 크기 = 처리량

따라서 애플리케이션에 필요한 최적의 처리량 및 IOPS 값을 결정하는 것이 중요합니다. 하나를 최적화하려고 할 때 다른 하나도 영향을 받습니다. 16KiB IO 크기에 해당하는 처리량으로 시작하고, 처리량이 더 필요한 경우 조정하는 것이 좋습니다.

Ultra SSD에서 지원되는 디스크 처리량에 대한 자세한 내용은 위의 확장성 및 성능 목표 섹션에 있는 표를 참조하세요. 디스크 크기 및 IOPS와 마찬가지로 프로비전된 처리량은 프로비전된 MBps당 시간 단위로 요금이 청구됩니다.

### <a name="ultra-ssd-vm-reservation-fee"></a>Ultra SSD VM 예약 요금

VM이 Ultra SSD 호환 가능함을 나타내는 VM에서 기능을 도입합니다. Ultra SSD 호환 VM은 계산 VM 인스턴스와 블록 저장소 배율 단위 간에 전용 대역폭 용량을 할당하여 성능을 최적화하고 대기 시간을 줄입니다. VM에 이 기능을 추가하면 Ultra SSD 디스크를 연결하지 않고 VM에서 Ultra SSD 기능을 설정한 경우에만 적용되는 예약 요금이 발생합니다. Ultra SSD 디스크가 Ultra SSD 호환 VM에 연결되면 이 요금은 적용되지 않습니다. 이 요금은 VM에서 프로비전되는 vCPU당 부과됩니다.

제한된 미리 보기에서 사용 가능한 새 Ultra SSD 디스크 가격 세부 정보는 [Azure Disks 가격 책정 페이지](https://azure.microsoft.com/pricing/details/managed-disks/)를 참조하세요.

### <a name="ultra-ssd-preview-scope-and-limitations"></a>Ultra SSD 미리 보기 범위 및 제한 사항

미리 보기에서 Ultra SSD 디스크:

- 단일 가용성 영역의 미국 동부 2에서 처음에 지원될 예정  
- 가용성 영역으로만 사용할 수 있음(가용성 집합 및 영역 외부의 단일 VM 배포는 Ultra SSD 디스크를 연결하는 기능이 없음)
- ES/DS v3 VM에서만 지원됨
- 데이터 디스크로만 사용 가능하며 4k 물리적 섹터 크기만 지원함  
- 빈 디스크로만 만들 수 있음  
- 현재 Resource Manager 템플릿, CLI 및 Python SDK만을 사용하여 배포할 수 있습니다.
- 디스크 스냅숏, VM 이미지, 가용성 집합, Virtual Machine Scale Sets 및 Azure Disk Encryption을 (아직) 지원하지 않습니다.
- Azure Backup 또는 Azure Site Recovery와의 통합을 (아직) 지원하지 않습니다.
-  [대부분의 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)와 마찬가지로, 이 기능은 GA(일반 공금) 전에는 프로덕션 워크로드에 사용할 수 없습니다.
