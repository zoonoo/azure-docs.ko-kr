---
title: "Azure 프리미엄 및 표준 Managed Disks 개요 | Microsoft Docs"
description: "Azure VM 사용 시 저장소 계정을 처리해주는 Azure Managed Disks에 대한 개요"
services: storage
documentationcenter: na
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 272250b3-fd4e-41d2-8e34-fd8cc341ec87
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 6ec77968a0f264b8bf1fa56a23e4cc7faef614da
ms.lasthandoff: 03/17/2017


---

# <a name="azure-managed-disks-overview"></a>Azure Managed Disks 개요

Azure Managed Disks는 VM 디스크와 연결된 [저장소 계정](storage-introduction.md)을 관리하여 Azure IaaS VM의 디스크 관리를 간소화합니다. 필요한 디스크의 유형([프리미엄](storage-premium-storage.md) 또는 [표준](storage-standard-storage.md))과 크기만 지정하면 Azure가 알아서 디스크를 만들고 관리해줍니다.

>[!NOTE]
>Managed Disks가 있는 VM은 설치된 [VM 확장](../virtual-machines/virtual-machines-windows-extensions-features.md) 상태를 Azure 플랫폼에 보고하기 위해 포트 8443에서 아웃바운드 트래픽이 필요합니다. 이 포트의 가용성 없이 확장을 사용하여 VM을 프로비전하면 실패합니다. 또한 실행 중인 VM에 설치된 경우 확장의 배포 상태는 알 수 없습니다. 포트 8443 차단을 해제할 수 없는 경우 관리되지 않는 디스크를 사용해야 합니다. 이 문제를 해결하기 위한 작업이 활발히 진행되고 있습니다. 자세한 내용은 [IaaS VM 디스크에 대한 FAQ](storage-faq-for-disks.md#managed-disks-and-port-8443)를 참조하세요. 
>
>

## <a name="benefits-of-managed-disks"></a>관리 디스크의 이점

관리 디스크 사용의 일부 이점을 살펴보겠습니다.

### <a name="simple-and-scalable-vm-deployment"></a>간단하고 확장성 있는 VM 배포

Managed Disks는 배후에서 저장소를 처리해줍니다. 이전에는 Azure VM의 디스크(VHD 파일)를 갖기 위해 저장소 계정을 만들어야 했습니다. 확장하는 경우에는 디스크를 포함하는 저장소의 IOPS 제한을 초과하지 않도록 저장소 계정을 추가로 만들었는지 확인해야 했습니다. Managed Disks로 저장소를 처리하면 저장소 계정 한도(예: 20,000 IOPS/계정)로 인해 더 이상 제한을 받지 않습니다. 사용자 지정 이미지(VHD 파일)를 여러 저장소 계정에 복사할 필요도 없습니다. 중앙 위치(Azure 지역당 하나의 저장소 계정)에서 저장소 계정을 관리할 수 있고 이를 통해 구독에 수백 개의 VM을 만들 수 있습니다.

Managed Disks를 사용하면 구독에 VM **디스크**를 10,000개까지 만들 수 있고 따라서 단일 구독에 수천 개의 **VM**을 만들 수 있습니다. 이 기능을 사용하면 Marketplace 이미지를 사용하여 VMSS에 VM을 천 개까지 만들 수 있기 때문에 [Virtual Machine Scale Sets(VMSS)](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)의 확장성을 훨씬 더 높일 수도 있습니다.

### <a name="better-reliability-for-availability-sets"></a>가용성 집합에 대한 안정성 향상

Managed Disks는 단일 실패 지점을 피할 만큼 가용성 집합의 VM 디스크를 서로 충분히 격리시켜서 가용성 집합에 대해 향상된 안정성을 제공합니다. 이 기능은 디스크를 다른 저장소 배율 단위(스탬프)에 자동으로 배치하여 구현됩니다. 스탬프가 하드웨어 또는 소프트웨어 오류로 인해 실패하는 경우 해당 스탬프의 디스크가 있는 VM 인스턴스만 실패합니다. 예를 들어 응용 프로그램을 5개의 VM에서 실행 중이고 VM이 가용성 집합 내에 있다고 가정해 보겠습니다. 이러한 VM의 디스크는 동일한 스탬프에 저장되지 않습니다. 따라서 스탬프 하나가 작동이 중단되면 다른 응용 프로그램 인스턴스가 계속해서 실행됩니다.

### <a name="granular-access-control"></a>세부적인 액세스 제어

[Azure 역할 기반 액세스 제어(RBAC)](../active-directory/role-based-access-control-what-is.md)를 사용하여 관리 디스크에 대한 특정 권한을 한 명 이상의 사용자에게 할당할 수 있습니다. Managed Disks는 읽기, 쓰기(만들기/업데이트), 삭제, [SAS(공유 액세스 서명) URI](storage-dotnet-shared-access-signature-part-1.md) 검색 등 디스크에 대한 다양한 작업을 노출합니다. 업무를 수행하는 데 필요한 작업에만 액세스 권한을 부여할 수 있습니다. 예를 들어 관리 디스크를 저장소 계정에 복사하지 말아야 하는 경우에는 해당 관리 디스크에 대한 내보내기 작업에 액세스를 부여하지 않도록 선택할 수 있습니다. SAS URI를 사용하여 관리 디스크를 복사할 수 없도록 하기 위해 관리 디스크에 해당 권한을 부여하지 않도록 선택할 수 있습니다.

### <a name="azure-backup-service-support"></a>Azure Backup 서비스 지원 
Azure Backup 서비스를 Managed Disks와 함께 사용하여 시간 기반 백업, 손쉬운 VM 복원 및 백업 보존 정책을 사용하여 백업 작업을 만들 수 있습니다. Managed Disks는 복제 옵션으로 LRS(로컬 중복 저장소)만 지원하며 즉, 단일 지역 내에 데이터 복사본 3개를 유지합니다. 지역적 재해 복구를 위해 [Azure Backup 서비스](../backup/backup-introduction-to-azure-backup.md) 및 GRS 저장소 계정을 백업 자격 증명 모음으로 사용하여 VM 디스크를 다른 지역에 백업해야 합니다. 자세한 내용은 [Managed Disks로 VM에 Azure Backup 서비스 사용](../backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup)을 참조하세요. 

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구 

Managed Disks를 사용하는 경우 다음과 같은 청구 고려 사항이 적용됩니다.

* 저장소 유형

* 디스크 크기

* 트랜잭션 수

* 아웃바운드 데이터 전송

* 관리 디스크 스냅숏(전체 디스크 복사)

좀 더 자세히 살펴보겠습니다.

**저장소 유형:** Managed Disks에는 [프리미엄](storage-premium-storage.md)(SSD 기반) 및 [표준](storage-standard-storage.md)(HDD 기반)이라는 2개의 성능 계층이 제공됩니다. 관리 디스크에 대한 요금 청구는 디스크에 어떤 유형의 저장소를 선택했는지에 따라 달라집니다.


**디스크 크기**: 관리 디스크에 대한 요금 청구는 프로비전된 디스크 크기에 따라 달라집니다. Azure는 프로비전된 크기(올림)를 아래 테이블에 지정된 대로 가장 가까운 Managed Disks 옵션에 매핑합니다. 각각의 관리 디스크는 지원되는 프로비전된 크기 중 하나에 매핑되고 그에 따라 요금이 청구됩니다. 예를 들어 표준 관리 디스크를 만들고 프로비전된 크기를 200GB로 지정하면 S20 디스크 유형의 가격에 따라 요금이 청구됩니다.

프리미엄 관리 디스크에 사용 가능한 디스크 크기는 다음과 같습니다.

| **프리미엄 관리 <br>디스크 유형**  | **P10** | **P20** | **P30**        |
|------------------|---------|---------|----------------|
| 디스크 크기        | 128GB  | 512GB  | 1024GB(1TB) |

표준 관리 디스크에 사용 가능한 디스크 크기는 다음과 같습니다. 

| **표준 관리 <br>디스크 유형** | **S4**  | **S6**  | **S10**        | **S20** | **S30**        |
|------------------|---------|---------|----------------|---------|----------------|
| 디스크 크기        | 32GB   | 64GB   | 128GB  | 512GB  | 1024GB(1TB) |

**트랜잭션 수**: 표준 관리 디스크에서 수행하는 트랜잭션 수에 따라 요금이 청구됩니다. 프리미엄 관리 디스크에 대한 트랜잭션 비용은 없습니다.

**아웃바운드 데이터 전송**: [아웃바운드 데이터 전송](https://azure.microsoft.com/pricing/details/data-transfers/) (Azure 데이터 센터에서 데이터 전송) 시 대역폭 사용에 대해 청구가 발생합니다.

**Managed Disk 스냅숏(전체 디스크 복사)**: 관리 스냅숏은 표준 관리 디스크로 저장되는 관리 디스크의 읽기 전용 복사본입니다. 스냅숏을 사용하면 관리 디스크를 언제든지 백업할 수 있습니다. 이 스냅숏은 원본 디스크와 별도로 존재하고 새 Managed Disks를 만드는 데 사용될 수도 있습니다. 관리 스냅숏의 비용은 표준 관리 디스크의 비용과 동일합니다. 예를 들어 128GB 프리미엄 관리 디스크의 스냅숏을 만드는 경우 관리 스냅숏 비용은 128GB 표준 관리 디스크 비용과 같습니다.

[증분 스냅숏](storage-incremental-snapshots.md)은 현재 Managed Disks에 지원되지 않지만 나중에 지원될 예정입니다.

Managed Disks를 사용하여 스냅숏을 만드는 방법에 대해 자세히 알아보려면 다음 리소스를 참조하세요.

* [Windows에서 스냅숏을 사용하여 관리 디스크로 저장된 VHD 복사본 만들기](../virtual-machines/virtual-machines-windows-snapshot-copy-managed-disk.md)
* [Linux에서 스냅숏을 사용하여 관리 디스크로 저장된 VHD 복사본 만들기](../virtual-machines/linux/virtual-machines-linux-snapshot-copy-managed-disk.md)


Managed Disks 가격 책정에 대한 자세한 내용은 [Managed Disks 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks)을 참조하세요.

## <a name="images"></a>이미지

Managed Disks는 관리되는 사용자 지정 이미지 만들기도 지원합니다. 저장소 계정의 사용자 지정 VHD에서 이미지를 만들거나 일반화된(시스템에서 준비된) VM에서 직접 만들 수 있습니다. OS와 데이터 디스크를 모두 포함하여 VM과 연결된 모든 Managed Disks를 하나의 이미지에 캡처합니다. 이렇게 하면 저장소 계정을 복사하거나 관리할 필요 없이 사용자 지정 이미지를 사용하여 수백 개의 VM을 만들 수 있습니다.

이미지 만들기에 대한 내용은 다음 문서를 참조하세요.
* [Azure에서 일반화된 VM의 관리 이미지를 캡처하는 방법](../virtual-machines/virtual-machines-windows-capture-image-resource.md)
* [Azure CLI 2.0을 사용하여 Linux 가상 컴퓨터를 일반화하고 캡처하는 방법](../virtual-machines/virtual-machines-linux-capture-image.md)

## <a name="images-versus-snapshots"></a>이미지 및 스냅숏

“이미지”라는 단어가 VM과 함께 사용되는 경우가 많은데 이제 “스냅숏”이라는 단어도 볼 수 있습니다. 이 둘의 차이를 이해하는 것이 중요합니다. Managed Disks를 사용하면 할당이 취소되어 일반화된 VM의 이미지를 만들 수 있습니다. 이 이미지에는 VM에 연결된 모든 디스크가 포함됩니다. 이 이미지를 사용하여 새 VM을 만들 수 있고 여기에는 모든 디스크가 포함됩니다.

스냅숏은 스냅숏 생성 시점의 디스크 복사본입니다. 이것은 하나의 디스크에만 해당됩니다. VM의 디스크가 하나(OS)뿐인 경우 이 VM의 스냅숏 또는 이미지를 만들고 이 스냅숏 또는 이미지로부터 VM을 만들 수 있습니다.

VM의 디스크가 5개이고 스트라이프된 경우는 어떨까요? 각 디스크의 스냅숏을 만들 수 있지만 VM 내에서 디스크의 상태가 인식되지는 못하며 스냅숏은 해당되는 하나의 디스크에 대해서만 인식합니다. 이런 경우 스냅숏이 서로 조정될 필요가 있지만 현재 이 기능은 지원되지 않습니다.

## <a name="managed-disks-and-encryption"></a>Managed Disks 및 암호화

Managed Disks와 관련하여 논의할 두 종류의 암호화가 있습니다. 첫 번째는 저장소 서비스에서 수행하는 SSE(저장소 서비스 암호화)이고, 두 번째는 VM에 대한 OS 및 데이터 디스크에서 사용할 수 있는 Azure Disk Encryption입니다. 

### <a name="storage-service-encryption-sse"></a>SSE(저장소 서비스 암호화)

Azure Storage는 저장소 계정에 기록된 데이터의 자동 암호화를 지원합니다. 자세한 내용은 [미사용 데이터에 대한 Azure Storage 서비스 암호화](storage-service-encryption.md)를 참조하세요. 관리 디스크의 데이터는 어떻게 하나요? 현재 Managed Disks에 대한 저장소 서비스 암호화는 사용할 수 없지만 향후에 릴리스될 예정입니다. 그 동안은 암호화된 저장소 계정에 있는 VHD 파일을 사용하는 방법을 알아야 하고 자체적으로 암호화되도록 해야 합니다. 

SSE는 데이터가 저장소 계정에 기록된 대로 데이터를 암호화합니다. VHD 파일이 SSE로 암호화된 적이 있는 경우에는 해당 VHD 파일을 사용하여 Managed Disks를 사용하는 VM을 만들 수 없습니다. 암호화된 관리되지 않는 디스크를 관리 디스크로 변환할 수 없습니다. 마지막으로 해당 저장소 계정에서 암호화를 사용하지 않도록 설정하면 돌이킬 수 없고 VHD 파일 암호를 해독할 수 없습니다. 

암호화된 적이 있는 디스크를 사용하려면 우선 VHD 파일을 암호화된 적이 없는 저장소 계정에 복사해야 합니다. 그런 다음 Managed Disks로 VM을 만들고 만드는 동안 VHD 파일을 지정하거나 복사된 VHD 파일을 Managed Disks로 실행 중인 VM에 연결할 수 있습니다. 

### <a name="azure-disk-encryption-ade"></a>ADE(Azure Disk Encryption)

Azure Disk Encryption을 사용하면 IaaS 가상 컴퓨터에서 사용되는 OS 및 데이터 디스크를 암호화할 수 있습니다. 여기에 Managed Disks가 포함됩니다. Windows의 경우 업계 표준의 BitLocker 암호화 기술을 사용하여 드라이브가 암호화됩니다. Linux의 경우 DM-Crypt 기술을 사용하여 디스크가 암호화됩니다. 이 기술은 Azure 키 자격 증명 모음과 통합되어 디스크 암호화 키를 제어 및 관리할 수 있도록 합니다. 자세한 내용은 [Windows 및 Linux IaaS VM용 Azure Disk Encryption](../security/azure-security-disk-encryption.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Managed Disks에 대한 자세한 내용은 다음 문서를 참조하세요.

### <a name="get-started-with-managed-disks"></a>Managed Disks 시작 

* [Resource Manager 및 PowerShell을 사용하여 VM 만들기](../virtual-machines/virtual-machines-windows-ps-create.md)

* [Azure CLI 2.0을 사용하여 Linux VM 만들기](../virtual-machines/virtual-machines-linux-quick-create-cli.md)

* [PowerShell을 사용하여 관리 데이터 디스크를 Windows VM에 연결](../virtual-machines/virtual-machines-windows-attach-disk-ps.md)

* [관리 디스크를 Linux VM에 추가](../virtual-machines/virtual-machines-linux-quick-create-cli.md)

### <a name="compare-managed-disks-storage-options"></a>Managed Disks 저장소 옵션 비교 

* [프리미엄 저장소 및 디스크](storage-premium-storage.md)

* [표준 저장소 및 디스크](storage-standard-storage.md)

### <a name="operational-guidance"></a>운영 가이드

* [AWS 및 기타 플랫폼에서 Azure의 Managed Disks로 마이그레이션](../virtual-machines/virtual-machines-windows-on-prem-to-azure.md)

* [Azure VM을 Azure의 관리 디스크로 변환](../virtual-machines/virtual-machines-windows-migrate-to-managed-disks.md)

