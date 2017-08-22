---
title: "Azure Site Recovery를 사용하여 보조 VMM 사이트로의 Hyper-V 복제를 위한 필수 구성 요소 검토 | Microsoft 문서"
description: "Azure Site Recovery를 사용하여 보조 VMM 사이트로 Hyper-V VM을 복제하기 위한 필수 구성 요소에 대해 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 21ff0545-8be5-4495-9804-78ab6e24add6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: 7897a30bf1774609ca8e6037dabcd5fbf4151271
ms.contentlocale: ko-kr
ms.lasthandoff: 08/01/2017

---
# <a name="step-2-review-the-prerequisites-and-limitations-for-hyper-v-vm-replication-to-a-secondary-vmm-site"></a>2단계: 보조 VMM 사이트로의 Hyper-V VM 복제를 위한 필수 구성 요소 및 제한 사항 검토


[시나리오 아키텍처](vmm-to-vmm-walkthrough-architecture.md)를 검토한 후 이 문서의 내용에 따라 Azure Portal에서 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 System Center VMM(Virtual Machine Manager) 클라우드에서 관리되는 온-프레미스 Hyper-V VM(가상 컴퓨터)을 보조 사이트로 복제할 때의 배포 필수 구성 요소를 파악할 수 있습니다.

이 문서를 읽은 후에 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견을 게시합니다.


## <a name="prerequisites-and-limitations"></a>필수 구성 요소 및 제한 사항

**요구 사항** | **세부 정보**
--- | ---
**Azure** | [Microsoft Azure](http://azure.microsoft.com/) 구독<br/><br/> [평가판](https://azure.microsoft.com/pricing/free-trial/)으로 시작할 수 있습니다.<br/><br/> Site Recovery 가격 책정에 대해 [자세히 알아보세요](https://azure.microsoft.com/pricing/details/site-recovery/).<br/><br/> [Azure Site Recovery 가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/site-recovery/)의 지역 가용성에서 Site Recovery에 지원되는 지역을 확인하세요.
**VMM 서버** | VMM 서버는 기본 사이트에서 1개, 보조 사이트에서 1개를 사용하는 것이 좋습니다.<br/><br/> 단일 VMM 서버의 클라우드 간 복제가 지원됩니다.<br/><br/> VMM 서버는 최신 업데이트를 설치한 System Center 2012 SP1 이상을 실행해야 합니다.<br/><br/> VMM 서버는 인터넷에 연결되어야 합니다.
**VMM 클라우드** | 각 VMM 서버에는 하나 이상의 클라우드가 있어야 하고 모든 클라우드에 Hyper-V 용량 프로필이 설정되어 있어야 합니다. <br/><br/>클라우드에 하나 이상의 VMM 호스트 그룹이 있어야 합니다.<br/><br/> VMM 서버가 하나만 있는 경우 기본 및 보조 역할을 수행하도록 둘 이상의 클라우드가 필요합니다.
**Hyper-V** | Hyper-V 서버는 Hyper-V 역할로 Windows Server 2012 이상을 실행해야 하며 최신 업데이트가 설치되어 있어야 합니다.<br/><br/> Hyper-V 서버에 VM이 하나 이상 있어야 합니다.<br/><br/>  Hyper-V 호스트 서버는 기본 및 보조 VMM 클라우드의 호스트 그룹에 있어야 합니다.<br/><br/> Windows Server 2012 R2의 클러스터에서 Hyper-V를 실행하는 경우 [업데이트 2961977](https://support.microsoft.com/kb/2961977)을 설치합니다.<br/><br/> Windows Server 2012의 클러스터에서 Hyper-V를 실행하는 경우 고정 IP 주소 기반 클러스터가 있으면 클러스터 브로커가 자동으로 만들어지지 않습니다. 클러스터 브로커를 수동으로 구성합니다. [자세히 알아보기](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).<br/><br/> Hyper-V 서버는 인터넷에 연결되어야 합니다.




## <a name="next-steps"></a>다음 단계

[3단계: 네트워킹 계획](vmm-to-vmm-walkthrough-network.md)으로 이동합니다.

