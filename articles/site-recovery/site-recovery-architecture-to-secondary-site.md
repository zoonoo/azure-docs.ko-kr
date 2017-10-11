---
title: "Azure Site Recovery에서 보조 온-프레미스 사이트에 대한 온-프레미스 컴퓨터 복제가 작동하는 방법 | Microsoft Docs"
description: "이 문서에서는 Azure Site Recovery 서비스를 사용하여 온-프레미스 VM 및 실제 서버를 보조 사이트로 복제할 때 사용되는 구성 요소 및 아키텍처 개요를 제공합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/29/2017
ms.author: raynew
ms.openlocfilehash: fca95c63964b955db7ddfbe53250702cc8af122e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="how-does-on-premises-machine-replication-to-a-secondary-site-work-in-site-recovery"></a>Site Recovery에서 보조 사이트에 대한 온-프레미스 컴퓨터 복제가 작동하는 방법

이 문서에서는 Azure Portal에서 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 온-프레미스 가상 컴퓨터 및 실제 서버를 Azure에 복제할 때 포함된 구성 요소 및 프로세스를 설명합니다.

다음 사항을 보조 온-프레미스 사이트로 복제할 수 있습니다.
- Site Recovery는 System Center VMM(Virtual Machine Manager)의 사용과 상관없이 관리되는 Hyper-V 클러스터 및 독립 실행형 호스트의 온-프레미스 Hyper-V VM
- 온-프레미스 VMware VM 및 Windows/Linux 물리적 서버 이 시나리오에서 복제는 Scout에 의해 관리됩니다.

이 문서의 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견을 게시합니다.

## <a name="replicate-hyper-v-vms-to-a-secondary-on-premises-site"></a>보조 온-프레미스 사이트에 Hyper-V VM 복제


### <a name="architectural-components"></a>아키텍처 구성 요소

Hyper-V VM을 보조 사이트에 복제하는 데 필요한 사항은 다음과 같습니다.

**구성 요소** | **위치** | **세부 정보**
--- | --- | ---
**Azure** | Microsoft에서는 계정이 필요합니다. |
**VMM 서버** | VMM 서버는 기본 사이트에서 1개, 보조 사이트에서 1개를 사용하는 것이 좋습니다. | 각 VMM 서버는 인터넷에 연결되어야 합니다.<br/><br/> 각 서버에는 Hyper-V 기능 프로필 집합을 가진 하나 이상의 VMM 사설 클라우드가 있어야 합니다.<br/><br/> VMM 서버에 Azure Site Recovery 공급자를 설치합니다. 공급자는 인터넷을 통해 사이트 복구 서비스에서의 복제를 조정 및 오케스트레이션합니다. 공급자와 Azure 간의 통신은 모두 안전하고 암호화됩니다.
**Hyper-V 서버** |  기본 및 보조 VMM 클라우드에 있는 하나 이상의 Hyper-V 호스트 서버.<br/><br/> 서버는 인터넷에 연결되어야 합니다.<br/><br/> Kerberos 또는 인증서 인증을 사용하여 LAN 또는 VPN을 통해 기본 및 보조 Hyper-V 호스트 서버 간에 데이터가 복제됩니다.  
**Hyper-V VM** | 원본 Hyper-V 호스트 서버에 있음. | 원본 호스트 서버에는 복제하려는 VM이 하나 이상 있어야 합니다.

### <a name="replication-process"></a>복제 프로세스

1. Azure 계정을 설정합니다.
2. Site Recovery에 대한 복제 서비스 자격 증명 모음을 만들고 다음을 포함한 자격 증명 모음 설정을 구성합니다.

    - 복제 원본 및 대상(기본 및 보조 사이트)
    - Azure Site Recovery 공급자 및 Microsoft Azure Recovery Services 에이전트 설치 공급자는 VMM 서버 및 각 Hyper-V 호스트의 에이전트에 설치됩니다.
    - 원본 VMM 클라우드에 대한 복제 정책을 만듭니다. 정책은 클라우드의 호스트에 있는 모든 VM에 적용됩니다.
    - Hyper-V VM에 복제를 사용하도록 설정합니다. 초기 복제는 복제 정책 설정에 따라 발생합니다.
4. 데이터 변경을 추적하고 델타 변경 내용의 복제를 초기 복제가 완료된 후에 시작합니다. .hrl 파일에는 항목에 대한 추적된 변경 내용이 유지됩니다.
5. 모든 것이 제대로 작동하는지 확인할 수 있도록 테스트 장애 조치를 실행합니다.

**그림 1: VMM에서 VMM 복제로**

![온-프레미스 간](./media/site-recovery-components/arch-onprem-onprem.png)

### <a name="failover-and-failback-process"></a>장애 조치 및 장애 복구 프로세스

1. 온-프레미스 사이트 간에 계획된 또는 계획되지 않은 [장애 조치](site-recovery-failover.md)를 실행할 수 있습니다. 계획된 장애 조치를 실행할 경우 데이터 손실을 방지하기 위해 원본 VM이 종료됩니다.
2. 단일 컴퓨터에 장애 조치를 수행하거나 [복구 계획](site-recovery-create-recovery-plans.md)을 만들어서 여러 컴퓨터의 장애 조치를 오케스트레이션할 수 있습니다.
4. 보조 사이트에 계획되지 않은 장애 조치를 수행했다면 장애 조치 후에 보조 위치에 있는 장애 조치 컴퓨터는 보호 또는 복제하도록 설정되지 않습니다. 계획된 장애 조치를 실행했으면 장애 조치 후에 보조 위치에 있는 컴퓨터가 보호됩니다.
5. 그런 다음 장애 조치를 커밋하여 복제본 VM에서 워크로드에 액세스합니다.
6. 기본 사이트를 사용할 수 있는 경우 역방향 복제를 시작하여 보조 사이트에서 기본 사이트로 복제합니다. 역방향 복제는 가상 컴퓨터를 보호된 상태로 가져오지만 보조 데이터 센터는 여전히 활성 위치입니다.
7. 기본 사이트를 활성 위치로 다시 만들려면 다른 역방향 복제 후에 보조 사이트에서 기본 사이트로 계획된 장애 조치를 시작합니다.




## <a name="replicate-vmware-vmsphysical-servers-to-a-secondary-site"></a>보조 사이트에 VMware VM/물리적 서버 복제

InMage Scout을 사용하고 이러한 아키텍처 구성 요소를 사용하여 VMware VM 또는 물리적 서버에서 보조 사이트로 복제합니다.


### <a name="architectural-components"></a>아키텍처 구성 요소

**구성 요소** | **위치** | **세부 정보**
--- | --- | ---
**Azure** | InMage Scout. | InMage Scout를 얻으려면 Azure 구독이 필요합니다.<br/><br/> Recovery Services 자격 증명 모음을 만든 후에 InMage Scout를 다운로드하고 최신 업데이트를 설치하여 배포를 설정합니다.
**프로세스 서버** | 기본 사이트에 있음 | 프로세스 서버를 배포하여 캐시, 압축 및 데이터 최적화를 처리합니다.<br/><br/> 또한 보호하려는 컴퓨터에 대해 통합 에이전트의 푸시 설치를 처리합니다.
**구성 서버** | 보조 사이트에 있음 | 구성 서버는 관리 웹 사이트나 vContinuum 콘솔을 사용하여 배포를 관리, 구성 및 모니터링합니다.
**vContinuum 서버** | 선택 사항입니다. 구성 서버와 동일한 위치에 설치됩니다. | 보호되는 환경을 관리 및 모니터링하기 위한 콘솔을 제공합니다.
**마스터 대상 서버** | 보조 사이트에 있음 | 마스터 대상 서버는 복제된 데이터를 보유합니다. 프로세스 서버로부터 데이터를 수신하고 보조 사이트에 복제본 컴퓨터를 만들며 데이터 보존 지점을 유지합니다.<br/><br/> 필요한 마스터 대상 서버의 수는 보호하는 컴퓨터의 수에 따라 다릅니다.<br/><br/> 기본 사이트로 다시 장애 복구할 경우 해당 기본 사이트에도 마스터 대상 서버가 필요합니다. 통합 에이전트는 이 서버에 설치됩니다.
**VMware ESX/ESXi 및 vCenter 서버** |  ESX/ESXi 호스트에서 호스트되는 VM. vCenter 서버로 관리되는 호스트. | VMware VM을 복제하려면 VMware 인프라가 필요합니다.
**VM/물리적 서버** |  복제하려는 VMware VM 또는 물리적 서버에 설치되는 통합 에이전트. | 에이전트는 모든 구성 요소 간의 통신 공급자 역할을 합니다.


### <a name="replication-process"></a>복제 프로세스

1. 각 사이트(구성, 프로세스, 마스터 대상)에 구성 요소 서버를 설정하고 복제할 컴퓨터에 통합 에이전트를 설치합니다.
2. 초기 복제 후 각 컴퓨터에서 에이전트는 델타 복제 변경을 프로세스 서버로 보냅니다.
3. 프로세스 서버가 데이터를 최적화하고 보조 사이트의 마스터 대상 서버로 보냅니다. 구성 서버는 복제 프로세스를 관리합니다.

**그림 2: VMware에서 VMware로 복제**

![VMware 간](./media/site-recovery-components/vmware-to-vmware.png)


## <a name="next-steps"></a>다음 단계

[지원 매트릭스](site-recovery-support-matrix-to-sec-site.md)를 검토합니다.
