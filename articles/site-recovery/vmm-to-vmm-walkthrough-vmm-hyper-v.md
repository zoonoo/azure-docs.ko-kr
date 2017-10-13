---
title: "Azure Site Recovery를 사용하여 보조 사이트로 복제할 VMM 및 Hyper-V 설정 | Microsoft 문서"
description: "보조 VMM 사이트로 복제할 System Center VMM 서버 및 Hyper-V 호스트를 설정하는 방법을 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d0389e3b-3737-496c-bda6-77152264dd98
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 73bd1790c56ac52166f638de2e80c2a2cfb87e53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="step-4-set-up-vmm-and-hyper-v-for-hyper-v-vm-replication-to-a-secondary-site"></a>4단계: 보조 사이트로 Hyper-V VM을 복제하기 위한 VMM 및 Hyper-V 설정 

네트워킹을 준비한 후 Azure Portal에서 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 보조 사이트로 Hyper-V VM(가상 컴퓨터)을 복제하기 위한 System Center Virtual Machine Manager(VMM) 서버 및 Hyper-V 호스트를 설정합니다. 

이 문서를 읽은 후에 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견을 게시합니다.



## <a name="prepare-vmm-servers"></a>VMM 서버 준비 

배포를 준비하려면


1. VMM 서버가 [지원 요구 사항](site-recovery-support-matrix-to-sec-site.md#on-premises-servers) 및 [배포 필수 구성 요소](vmm-to-vmm-walkthrough-prerequisites.md)를 준수하는지 확인합니다.
2. VMM 서버가 인터넷에 연결되어 있으며 다음 URL에 액세스할 수 있는지 확인합니다.
    
    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - IP 주소 기반 방화벽 규칙이 있는 경우 해당 규칙이 Azure와의 통신을 허용하는지 확인합니다.
    - [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653) 및 HTTPS(443) 포트를 허용합니다.
    - 구독하는 Azure 지역과 미국 서부에 해당하는 IP 주소 범위를 허용하세요(Access Control 및 ID 관리에 사용됨).
3. [네트워크 매핑](vmm-to-vmm-walkthrough-network.md#prepare-for-network-mapping)용 VMM 서버를 준비했는지 확인합니다.


## <a name="prepare-hyper-v-hostsclusters"></a>Hyper-V 호스트/클러스터 준비

1. Hyper-V 호스트/클러스터가 [지원 요구 사항](site-recovery-support-matrix-to-sec-site.md#on-premises-servers) 및 [배포 필수 구성 요소](vmm-to-vmm-walkthrough-prerequisites.md)를 준수하는지 확인합니다.
2. [Hyper-V VM](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions)의 요구 사항을 확인합니다.
3. [네트워크](site-recovery-support-matrix-to-sec-site.md#network-configuration) 및 [저장소](site-recovery-support-matrix-to-sec-site.md#storage) 요구 사항을 확인합니다.
4. Hyper-V 호스트가 인터넷에 연결되어 있으며 다음 URL에 액세스할 수 있는지 확인합니다.
    
    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - IP 주소 기반 방화벽 규칙이 있는 경우 해당 규칙이 Azure와의 통신을 허용하는지 확인합니다.
    - [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653) 및 HTTPS(443) 포트를 허용합니다.
    - 구독하는 Azure 지역과 미국 서부에 해당하는 IP 주소 범위를 허용하세요(Access Control 및 ID 관리에 사용됨).

## <a name="prepare-for-single-server-deployment"></a>단일 서버 배포 준비


단일 VMM 서버만 있으면 이 문서의 설명에 따라 VMM 클라우드의 Hyper-V 호스트에 있는 VM을 [Azure](hyper-v-site-walkthrough-overview.md) 또는 보조 VMM 클라우드에 복제할 수 있습니다. 클라우드 간 복제는 원활하지 않으므로 첫 번째 옵션을 사용하는 것이 좋습니다.

클라우드 간에 복제할 경우 단일 독립 실행형 VMM 서버 또는 늘어난 Windows 클러스터에 배포된 단일 VMM 서버로 복제할 수 있습니다.

### <a name="replicate-with-a-standalone-vmm-server"></a>독립 실행형 VMM 서버로 복제

이 시나리오에서는 단일 VMM 서버를 기본 사이트에서 가상 컴퓨터로 배포하고 Site Recovery 및 Hyper-V 복제본을 사용하여 이 VM을 보조 사이트로 복제합니다.

1. **Hyper-V VM에 VMM을 설정합니다**. 이를 수행하는 경우 VMM에 사용되는 SQL Server 인스턴스를 동일한 VM에 배치하는 것이 좋습니다. 이렇게 하면 VM을 하나만 만들면 되므로 시간이 절약됩니다. SQL Server의 원격 인스턴스를 사용하려는 경우 중단이 발생하면 VMM을 복구하기 전에 해당 인스턴스를 복구해야 합니다.
2. **VMM 서버에 클라우드가 2개 이상 구성되도록 해야 합니다**. 하나의 클라우드는 복사할 VM을 포함하고, 다른 클라우드는 보조 위치로 사용됩니다. 보호하려는 VM이 포함된 클라우드는 [필수 구성 요소](#prerequisites)를 충족해야 합니다.
3. 이 문서에 설명된 대로 Site Recovery를 설정합니다. 자격 증명 모음에 VMM 서버를 만든 후 등록하고 복제 정책을 설정하고 복제를 사용하도록 설정합니다. 원본 및 대상 VMM 이름이 동일하게 지정됩니다. 초기 복제가 네트워크를 통해 진행되도록 지정합니다.
4. 네트워크 매핑을 설정하는 경우 기본 클라우드용 VM 네트워크를 보조 클라우드용 VM 네트워크에 매핑합니다.
5. Hyper-V 관리자 콘솔에서 VMM VM을 포함하는 Hyper-V 호스트의 Hyper-V 복제본을 사용하도록 설정하고 VM의 복제를 활성화합니다. Hyper-V 복제본 설정이 Site Recovery에 의해 무시되지 않도록, Site Recovery에 의해 보호되는 클라우드에 VMM 가상 컴퓨터를 추가하지 말아야 합니다.
6. 장애 조치(failover)에 대한 복구 계획을 생성하려면 원본과 대상에 동일한 VMM 서버를 사용합니다.
7. 전체 가동 중단 시 다음과 같이 장애 조치를 수행하고 복구합니다.

   1. 보조 사이트의 Hyper-V 관리자 콘솔에서 계획되지 않은 장애 조치를 실행하여 기본 VMM VM을 보조 사이트로 장애 조치(failover)합니다.
   2. VMM VM이 작동되어 실행되는지 확인하고 자격 증명 모음에서 계획되지 않은 장애 조치를 실행하여 기본 클라우드에서 보조 클라우드로 VM을 장애 조치(failover)합니다. 장애 조치(failover)를 커밋하고 필요한 경우 대체 복구 지점을 선택합니다.
   3. 계획되지 않은 장애 조치(failover)가 완료되면 기본 사이트에서 모든 리소스에 다시 액세스할 수 있습니다.
   4. 기본 사이트를 다시 사용할 수 있게 되면 보조 사이트에서 Hyper-V 관리자 콘솔을 통해 VMM VM에 대한 역방향 복제를 사용하도록 설정합니다. 그러면 보조에서 기본으로 VM에 대한 복제가 시작됩니다.
   5. 보조 사이트의 Hyper-V 관리자 콘솔에서 계획된 장애 조치를 실행하여 VMM VM을 기본 사이트로 장애 조치(failover)합니다. 장애 조치(failover)를 커밋합니다. 그런 후 역방향 복제를 사용하도록 설정하여 기본 사이트에서 보조 사이트로 VMM VM 복제를 다시 시작합니다.
   6. 복구 서비스 자격 증명 모음에서 워크로드 VM에 대한 역방향 복제를 사용하도록 설정하여 보조 복제본에서 주 복제본으로 복제를 시작합니다.
   7. 복구 서비스 자격 증명 모음에서 계획된 장애 조치를 실행하여 워크로드 VM을 기본 사이트로 장애 복구(failback)합니다. 장애 조치(failover)를 커밋하여 완료합니다. 그런 후 역방향 복제를 사용하도록 설정하여 기본 사이트에서 보조 사이트로의 워크로드 VM 복제를 시작합니다.

### <a name="replicate-with-a-stretched-vmm-cluster"></a>확대 VMM 클러스터로 복제

독립 실행형 VMM 서버를 보조 사이트로 복제되는 VM으로 배포하는 대신 VMM을 Windows 장애 조치(failover) 클러스터의 VM으로 배포하면 VMM의 가용성을 높일 수 있습니다. 그러면 워크로드 복원력이 제공되고 하드웨어 장애로부터 보호됩니다. Site Recovery와 함께 배포하기 위해서 VMM VM은 지리적으로 떨어져 있는 사이트에 걸친 확대 클러스터에 배포되어야 합니다. 다음을 수행합니다.

1. Windows 장애 조치(failover) 클러스터의 가상 컴퓨터에 VMM을 설치하고, 설치하는 동안 항상 서버를 실행할 수 있도록 옵션을 선택합니다.
2. 보조 사이트에 데이터베이스 복제본이 존재하도록 VMM에 사용되는 SQL Server 인스턴스는 SQL Server AlwaysOn 가용성 그룹과 함께 복제되어야 합니다.
3. 이 문서의 지침에 따라 자격 증명 모음을 만들고, 서버를 등록하고, 보호를 설정합니다. 복구 서비스 자격 증명 모음에서 클러스터의 각 VMM 서버를 등록해야 합니다. 이를 위해 활성 노드에 공급자를 설치하고 VMM 서버를 등록합니다. 그런 다음 다른 노드에 공급자를 설치합니다.
4. 중단이 발생하면 VMM 서버 및 해당 SQL Server 데이터베이스에 장애 조치가 수행되고 보조 사이트에서 액세스됩니다.



## <a name="next-steps"></a>다음 단계

[5단계: 자격 증명 모음 설정](vmm-to-vmm-walkthrough-create-vault.md)으로 이동합니다.