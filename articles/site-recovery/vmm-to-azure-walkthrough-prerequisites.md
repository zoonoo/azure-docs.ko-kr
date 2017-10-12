---
title: "Azure Site Recovery를 사용하여 Azure로 Hyper-V(System Center VMM 포함) 복제를 위한 필수 구성 요소 검토 | Microsoft Docs"
description: "Azure Site Recovery를 사용하여 Azure에 VMM 클라우드의 온-프레미스 Hyper-V VM의 복제, 장애 조치(failover) 및 복구를 설정하기 위한 필수 구성 요소를 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a1c30fd5-c979-473c-af44-4f725ad3e3ba
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/24/2017
ms.author: raynew
ms.openlocfilehash: 47c178c66ec98fe5d333edd725b64465026e73ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="step-2-review-the-prerequisites-for-hyper-v-with-vmm-to-azure-replication"></a>2단계: Azure에 Hyper-V(VMM 포함) 복제를 위한 필수 구성 요소 검토

[시나리오 아키텍처](vmm-to-azure-walkthrough-architecture.md)를 검토한 후에는 이 문서를 읽고 배포 필수 구성 요소를 이해해야 합니다. 

## <a name="prerequisites-and-limitations"></a>필수 구성 요소 및 제한 사항

**요구 사항** | **세부 정보**
--- | ---
**Azure 계정** | [Microsoft Azure 계정](http://azure.microsoft.com/)이 있어야 합니다.
**Azure 저장소** | 복제된 데이터를 저장하려면 Azure 저장소 계정이 필요합니다.<br/><br/> 저장소 계정은 Azure Recovery Services 자격 증명 모음과 동일한 지역에 있어야 합니다.<br/><br/>[지역 중복 저장소](../storage/common/storage-redundancy.md#geo-redundant-storage) 또는 로컬 중복 저장소를 사용할 수 있습니다. 지역 중복 저장소를 사용하는 것이 좋습니다. 지역 중복 저장소를 통해 지역 정전이 발생하거나 주 지역을 복구할 수 없는 경우 데이터를 복원할 수 있습니다.<br/><br/> 표준 Azure Storage 계정을 사용하거나 Azure [Premium Storage](../storage/common/storage-premium-storage.md)를 사용할 수 있습니다. Premium Storage는 I/O를 많이 사용하는 워크로드를 호스트할 수 있으며 일관된 I/O 고성능과 짧은 대기 시간이 요구되는 VM에 일반적으로 사용됩니다. 복제된 데이터에 대해 Premium Storage를 사용하는 경우 표준 저장소 계정도 필요합니다. 표준 저장소 계정은 온-프레미스 데이터에 대한 지속적인 변화를 캡처하는 복제 로그를 저장합니다.
**Azure 네트워크** | 장애 조치(Failover) 후 Azure VM이 연결될 수 있도록 [Azure 네트워크](../virtual-network/virtual-network-get-started-vnet-subnet.md)가 필요합니다. Azure 네트워크는 Recovery Services 자격 증명 모음과 동일한 지역에 있어야 합니다.
**온-프레미스 VMM 서버** | System Center 2012 R2 이상에서 실행되는 하나 이상의 VMM 서버가 필요합니다.<br/><br/> 각 VMM 서버에 하나 이상의 사설 클라우드가 포함되어 있어야 합니다. 각 클라우드에 하나 이상의 호스트 그룹이 필요합니다.<br/><br/> VMM 서버는 인터넷 액세스가 필요합니다.
**온-프레미스 Hyper-V** | Hyper-V 호스트 서버는 Windows Server 2012 R2 이상(Hyper-V 역할 수행) 또는 Microsoft Hyper-V Server 2012 R2를 실행해야 합니다. 최신 업데이트를 설치해야 합니다.<br/><br/> Hyper-V 호스트는 VMM 클라우드의 VMM 호스트 그룹에 있어야 합니다.<br/><br/> 호스트에는 복제하려는 VM이 하나 이상 있어야 합니다.<br/><br/> Hyper-V 호스트는 직접 또는 프록시를 통해 Azure에 복제를 위한 인터넷에 연결되어야 합니다. [2961977](https://support.microsoft.com/kb/2961977) 문서에서 설명한 수정 프로그램이 Hyper-V 서버에 있어야 합니다.
**온-프레미스 Hyper-V VM** | 복제하려는 VM은 [지원되는 운영 체제](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)를 실행하고 [Azure 필수 조건](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)을 준수해야 합니다. 복제가 사용되도록 설정한 후에 VM 이름을 수정할 수 있습니다. 




## <a name="next-steps"></a>다음 단계

[3단계: 용량 계획](vmm-to-azure-walkthrough-capacity.md)으로 이동합니다.
