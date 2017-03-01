---
title: "Managed Disks로 Azure VM 마이그레이션 | Microsoft Docs"
description: "저장소 계정에서 관리되지 않는 디스크를 사용하여 만든 Azure 가상 컴퓨터를 마이그레이션하여 Managed Disks를 사용합니다."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 44c1f6ddac328516d707cfe5d328e02e50652e5b
ms.openlocfilehash: e66857cf6cc05aae2fa102173a2958564ec936e6
ms.lasthandoff: 02/16/2017


---

# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Azure VM을 Azure의 Managed Disks로 마이그레이션

Azure Managed Disks는 저장소 계정을 별도로 관리하지 않아도 되기 때문에 저장소 관리를 간소화합니다.  또한 기존 Azure VM을 Managed Disks에 마이그레이션하여 가용성 집합에서 VM의 안정성을 향상시킬 수 있습니다. 가용성 집합에서 여러 VM의 디스크는 단일 실패 지점을 방지하기 위해 충분히 서로 격리되어야 합니다. 서로 다른 저장소 배율 단위(스탬프)인 자동 가용성 집합에서 다른 VM의 디스크를 자동으로 배치합니다. 그러면 하드웨어 및 소프트웨어 오류로 인해 발생한 단일 저장소 배율 단위 오류의 영향을 제한합니다. 필요에 따라 두 가지 유형의 저장소 옵션 중에 하나를 선택할 수 있습니다. 
 
- [프리미엄 Managed Disks](../storage/storage-premium-storage.md)는 I/O 집약적인 워크로드를 실행하는 가상 컴퓨터에 대한 고성능의 짧은 대기 시간 디스크 지원을 제공하는 반도체 드라이브(SSD) 기반 저장소 미디어입니다. 프리미엄 Managed Disks로 마이그레이션하여 이러한 디스크의 속도와 성능 혜택을 활용할 수 있습니다.

- [표준 Managed Disks](../storage/storage-standard-storage.md)는 하드 디스크 드라이브(HDD) 기반 저장소 미디어이며 개발/테스트를 비롯하여 성능 변화에 덜 민감하고 자주 발생되지 않는 액세스 워크로드에 가장 적합합니다. 

다음과 같은 시나리오에서 Managed Disks를 마이그레이션할 수 있습니다.

| 마이그레이션...                                            | 문서 링크                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 관리되지 않는 디스크에서 Managed Disks를 사용하는 가용성 집합의 VM   | [가용성 집합에서 VM을 변환하여 Managed Disks 사용](virtual-machines-windows-convert-unmanaged-to-managed-disks.md#convert-vms-in-an-availability-set-to-managed-disks-in-a-managed-availability-set)                                                                        |
| 관리되지 않는 프리미엄 디스크에서 프리미엄 Managed Disks로   | [기존 Azure VM을 같은 저장소 유형의 Managed Disks로 변환](virtual-machines-windows-convert-unmanaged-to-managed-disks.md#convert-existing-azure-vms-to-managed-disks-of-the-same-storage-type)                                                                         |
| 관리되지 않는 표준 디스크에서 표준 Managed Disks로 | [기존 Azure VM을 같은 저장소 유형의 Managed Disks로 변환](virtual-machines-windows-convert-unmanaged-to-managed-disks.md#convert-existing-azure-vms-to-managed-disks-of-the-same-storage-type)                                                                         |
| 관리되지 않는 표준 디스크에서 프리미엄 Managed Disks로  | [관리되지 않는 표준 디스크를 사용하는 기존 Azure VM을 프리미엄 Managed Disks로 마이그레이션](virtual-machines-windows-convert-unmanaged-to-managed-disks.md#migrate-existing-azure-vms-using-standard-unmanaged-disks-to-premium-managed-disks)                            |
| Managed Disks의 클래식에서 Resource Manager로의 단일 VM     | [단일 VM 마이그레이션](virtual-machines-windows-migrate-single-classic-to-resource-manager.md)  | 
| Managed Disks의 클래식에서 Resource Manager로의 VNet에 있는 모든 VM     | [클래식에서 Resource Manager로 IaaS 리소스를 마이그레이션](virtual-machines-windows-ps-migration-classic-resource-manager.md)한 다음 [관리되지 않는 디스크에서 Managed Disks로 VM 변환](virtual-machines-windows-convert-unmanaged-to-managed-disks.md) | 





## <a name="plan-for-the-conversion-to-managed-disks"></a>Managed Disks로 변환 계획 수립

이 섹션을 통해 VM 및 디스크 유형에 대한 최선의 결정을 내릴 수 있습니다.


## <a name="location"></a>위치

Azure Managed Disks를 사용할 수 있는 위치를 선택합니다. 프리미엄 Managed Disks를 이동하는 경우에도 이동하려고 계획한 지역에서 Premium Storage를 사용할 수 있는지 확인합니다. 사용 가능한 위치에 대한 최신 정보는 [지역별 Azure 서비스](https://azure.microsoft.com/regions/#services)를 참조하세요.

## <a name="vm-sizes"></a>VM 크기

프리미엄 Managed Disks를 마이그레이션하는 경우 VM 크기를 VM이 위치한 지역에서 제공하는 Premium Storage 지원 가능 크기로 업데이트해야 합니다. Premium Storage를 사용할 수 있는 VM 크기를 검토합니다. Azure VM 크기 사양은 [가상 컴퓨터의 크기](virtual-machines-windows-sizes.md)에 나열되어 있습니다.
프리미엄 저장소와 작동하는 가상 컴퓨터의 성능 특징을 검토하고 워크로드에 가장 적합한 VM 크기를 선택합니다. VM에서 디스크 트래픽을 제어하기에 충분한 대역폭을 사용할 수 있는지 확인합니다.

## <a name="disk-sizes"></a>디스크 크기

**프리미엄 Managed Disks**

VM에서 사용할 수 있는 프리미엄 Managed Disks에는 세 종류가 있으며 각 종류에는 특정 IOP 및 처리량 제한이 있습니다. 용량, 성능, 확장성 및 최대 로드 측면에서 응용 프로그램의 필요에 따라 VM에 대한 프리미엄 디스크 유형을 선택할 때 이 제한을 고려해야 합니다.

| 프리미엄 디스크 유형  | P10               | P20               | P30               |
|---------------------|-------------------|-------------------|-------------------|
| 디스크 크기           | 128GB            | 512GB            | 1024GB(1TB)    |
| 디스크당 IOPS       | 500               | 2300              | 5000              |
| 디스크당 처리량 | 초당&100;MB | 초당&150;MB | 초당&200;MB |

**표준 Managed Disks**

VM에서 사용할 수 있는 표준 Managed Disks에는 다섯 가지 종류가 있습니다. 각각은 용량이 다르지만 동일한 IOPS 및 처리량이 제한됩니다. 응용 프로그램의 용량 요구 사항에 따라 표준 Managed Disks의 종류를 선택합니다.

| 표준 디스크 유형  | S4               | S6               | S10              | S20              | S30              |
|---------------------|------------------|------------------|------------------|------------------|------------------|
| 디스크 크기           | 30GB            | 64GB            | 128GB           | 512GB           | 1024GB(1TB)   |
| 디스크당 IOPS       | 500              | 500              | 500              | 500              | 500              |
| 디스크당 처리량 | 60 MB per second | 60 MB per second | 60 MB per second | 60 MB per second | 60 MB per second |

## <a name="disk-caching-policy"></a>디스크 캐싱 정책 

**프리미엄 Managed Disks**

기본적으로 디스크 캐싱 정책은 VM에 연결된 프리미엄 운영 체제 디스크에 대한 *읽기 / 쓰기* 및 모든 프리미엄 데이터 디스크에 대한 *읽기 전용*입니다. 응용 프로그램의 IO에 대한 최적의 성능을 얻으려면 이 구성 설정이 좋습니다. 쓰기가 많거나 쓰기 전용인 디스크의 경우(예: SQL Server 로그 파일) 더 나은 응용 프로그램 성능을 얻기 위해 디스크 캐싱을 사용하지 않도록 설정합니다.

## <a name="pricing"></a>가격

[Managed Disks에 대한 가격 책정](https://azure.microsoft.com/en-us/pricing/details/managed-disks/)을 검토합니다. 프리미엄 Managed Disks의 가격 책정은 관리되지 않는 프리미엄 디스크와 같습니다. 하지만 표준 Managed Disks의 가격 책정은 관리되지 않는 표준 디스크와 다릅니다.



## <a name="next-steps"></a>다음 단계

- [Managed Disks](../storage/storage-managed-disks-overview.md)에 대한 자세한 정보

