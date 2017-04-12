---
title: "Azure Storage 확장성 및 성능 목표 | Microsoft Docs"
description: "표준 및 프리미엄 저장소 계정에 대한 용량, 요청 속도 및 인바운드 및 아웃 바운드 대역폭을 포함한 Azure 저장소의 확장성 및 성능 목표를 알아보세요. Azure 저장소 서비스 각각의 파티션에 대한 성능 목표를 이해해 보세요."
services: storage
documentationcenter: na
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: be721bd3-159f-40a1-88c1-96418537fe75
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 03/07/2017
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: bffc77ba9e0413356ac366e85f2accb3c2087192
ms.lasthandoff: 04/03/2017


---
# <a name="azure-storage-scalability-and-performance-targets"></a>Azure 저장소 확장성 및 성능 목표
## <a name="overview"></a>개요
이 항목에서는 Microsoft Azure 저장소에 대한 확장성 및 성능을 설명합니다. 기타 Azure 제한 사항에 대한 요약은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)을 참조하세요.

> [!NOTE]
> 모든 저장소 계정은 새로운 플랫 네트워크 토폴로지에서 실행되고 작성된 시기에 관계 없이 아래에 설명된 확장성 및 성능 목표를 지원합니다. Azure 저장소 플랫 네트워크 아키텍처 및 확장성에 대한 자세한 내용은 [Microsoft Azure 저장소: 일관성과 가용성이 뛰어난 클라우드 저장소 서비스](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)를 참조하세요.
> 
> [!IMPORTANT]
> 여기에 나열된 확장성 및 성능 목표는 최첨단 목표이지만 달성할 수 있습니다. 모든 경우, 계정 사용량에 따라 달성된 요청 속도 및 대역폭은 저장된 개채의 크기 및 응용 프로그램이 수행한 작업 형태에 따릅니다. 해당 성능이 요구 사항을 충족시키는지 여부를 확인 하려면 서비스를 반드시 테스트해야 합니다. 가능하면 트래픽 속도가 갑자기 증가하지 않고 파티션 간의 트래픽이 적절하게 분산되도록 보장합니다.
> 
> 응용 프로그램이 파티션의 작업 처리 가능한 제한에 도달하면 Azure 저장소는 오류 코드 503 (서버 작업 중) 또는 오류 코드 500 (작업 시간 초과) 응답을 반송하기 시작합니다. 이런 경우 응용 프로그램은 재시도를 위한 지수 백오프 정책을 사용해야 합니다. 지수 백오프는 파티션에 대한 부하를 감소시키고 해당 파티션에 트래픽의 급증을 완화할 수 있습니다.
> 
> 

응용 프로그램의 요구가 단일 저장소 계정의 확장성 목표를 초과하는 경우 여러 저장소 계정을 사용하도록 응용 프로그램을 빌드하고 데이터를 이러한 저장소 계정에 분할합니다. 볼륨 가격에 대한 자세한 내용은 [Azure 저장소 가격 책정](https://azure.microsoft.com/pricing/details/storage/) 을 참조하세요.

## <a name="scalability-targets-for-blobs-queues-tables-and-files"></a>Blob, 큐, 테이블 및 파일에 대한 확장성 목표
[!INCLUDE [azure-storage-limits](../../includes/azure-storage-limits.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
## <a name="scalability-targets-for-virtual-machine-disks"></a>가상 컴퓨터 디스크에 대한 확장성 목표
[!INCLUDE [azure-storage-limits-vm-disks](../../includes/azure-storage-limits-vm-disks.md)]

자세한 내용은 [Windows VM 크기](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 또는 [Linux VM 크기](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

## <a name="managed-virtual-machine-disks"></a>관리되는 가상 컴퓨터 디스크

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../includes/azure-storage-limits-vm-disks-managed.md)]

## <a name="unmanaged-virtual-machine-disks"></a>관리되지 않는 가상 컴퓨터 디스크
[!INCLUDE [azure-storage-limits-vm-disks-standard](../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="scalability-targets-for-azure-resource-manager"></a>Azure 리소스 관리자에 대한 확장성 목표
[!INCLUDE [azure-storage-limits-azure-resource-manager](../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="partitions-in-azure-storage"></a>Azure 저장소의 파티션
Azure 저장소(blob, 메시지, 엔터티 및 파일)에 저장 된 데이터를 보유한 모든 개체는 파티션에 속하며 파티션 키로 식별됩니다. 파티션은 Azure 저장소 부하가 어떻게 서버의 blob, 메시지, 엔터티 및 파일을 해당 개체의 트래픽 요구를 충족하도록 분산할지 결정합니다. 파티션 키는 고유하며 blob, 메시지 또는 엔터티를 찾는 데 사용됩니다.

위의 테이블에 표시된 [표준 저장소 계정의 확장성 목표](#standard-storage-accounts) 는 각 서비스에 대한 단일 파티션의 성능 목표를 나열합니다.

파티션은 각 저장소 서비스의 부하 분산 및 확장성에 다음과 같은 방식으로 영향을 줍니다:

* **Blob**: blob의 파티션 키는 계정 이름 + 컨테이너 이름 + blob 이름입니다. 이는 blob의 로드에 필요한 경우 각 blob에 자체 파티션을 둘 수 있음을 의미합니다. 액세스를 확장하기 위해 blob을 여러 서버에 분산시킬 수 있지만 단일 blob은 단일 서버에 의해서만 처리될 수 있습니다. Blob 컨테이너에서 blob를 논리적으로 그룹화하는 반면 해당 그룹화에는 파티션이 적용되지 않습니다.
* **파일**: 파일에 대한 파티션 키는 계정 이름 + 파일 공유 이름입니다. 즉, 파일 공유의 모든 파일도 단일 파티션에 포함됩니다.
* **메시지**: 메시지의 파티션 키는 계정 이름 + 큐 이름이므로 큐에 있는 모든 메시지를 단일 파티션으로 그룹화하고 단일 서버를 통해 제공합니다. 서로 다른 큐는 부하를 분산하는 다른 서버에 의해 처리될 수 있지만 하나의 저장소 계정은 다수의 큐를 가질 수 있습니다.
* **엔터티**: 엔터티의 파티션 키는 계정 이름 + 테이블 이름 + 파티션 키이며, 여기서 파티션 키는 사용자 정의가 필요한 **PartitionKey** 속성의 값입니다. 동일한 파티션 키 값을 가진 모든 엔터티는 같은 파티션으로 그룹화 되며 같은 파티션 서버에 의해 처리됩니다. 이는 응용 프로그램 디자인에서 이해해야 할 중요한 사항입니다. 응용 프로그램은 엔터티를 단일 파티션으로 그룹화하는 데이터 액세스 이점과 여러 파티션에서 엔터티를 분산시키는 경우의 확장성 이점을 조정해야 합니다.  

테이블의 엔터티 그룹을 단일 파티션으로 그룹화하는 경우 가장 큰 장점은 하나의 파티션이 단일 서버에 존재하므로 동일한 파티션의 엔터티에서 원자성 배치 작업을 수행할 수 있다는 점입니다. 따라서 엔터티 그룹에 대해 배치 작업을 수행하려는 경우 동일한 파티션 키를 가진 엔터티를 그룹화하는 것이 좋습니다. 

반면, 동일한 테이블에 있지만 파티션 키가 서로 다른 엔터티는 서로 다른 서버에 부하를 분산시켜 확장성을 높일 수 있습니다.

테이블 분할 전략 디자인에 대한 자세한 권장 사항은 [여기](https://msdn.microsoft.com/library/azure/hh508997.aspx)에서 확인할 수 있습니다.

## <a name="see-also"></a>참고 항목
* [저장소 가격 정보](https://azure.microsoft.com/pricing/details/storage/)
* [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)
* [프리미엄 저장소: Azure 가상 컴퓨터 작업을 위한 고성능 저장소](storage-premium-storage.md)
* [Azure 저장소에서 복제](storage-redundancy.md)
* [Microsoft Azure 저장소 성능 및 확장성 검사 목록](storage-performance-checklist.md)
* [Microsoft Azure 저장소: 일관성과 가용성이 뛰어난 클라우드 저장소 서비스](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)


