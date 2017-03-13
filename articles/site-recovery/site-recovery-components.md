---
title: "Site Recovery가 작동하는 방식 | Microsoft Docs"
description: "이 문서는 사이트 복구 아키텍처의 개요를 제공합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/21/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 22b50dd6242e8c10241b0626b48f8ef842b6b0fd
ms.openlocfilehash: c33ca9e5292096a0fd96d98da3e89d721463e903
ms.lasthandoff: 03/02/2017


---
# <a name="how-does-azure-site-recovery-work"></a>Azure Site Recovery 작동 방식

[Azure Site Recovery](site-recovery-overview.md) 서비스의 기본 아키텍처와 이를 작동하게 하는 구성 요소에 대해서는 이 문서를 읽어보세요.

이 문서의 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견을 게시합니다.


## <a name="replication-to-azure"></a>Azure로 복제

다음 사항을 Azure로 복제할 수 있습니다.
- **VMware**: [지원되는 호스트](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)에서 실행되는 온-프레미스 VMware VM. [지원되는 운영 체제](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)에서 실행되는 VMware VM을 복제할 수 있습니다.
- **Hyper-V**: [지원되는 호스트](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)에서 실행되는 온-프레미스 Hyper-V VM.
- **물리적 컴퓨터**: [지원되는 운영 체제](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)에서 Windows 또는 Linux를 실행하는 온-프레미스 물리적 서버. [Hyper-V 및 Azure에서 지원하는](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) 게스트 운영 체제를 실행하는 Hyper-V VM을 복제할 수 있습니다.

## <a name="vmware-replication-to-azure"></a>Azure로 VMware 복제

영역 | 구성 요소 | 세부 정보
--- | --- | ---
**Azure** | Azure에서 Azure 계정, Azure Storage 계정 및 Azure 네트워크가 필요합니다. | 저장소 및 네트워크는 Resource Manager 계정 또는 기존 계정일 수 있습니다.<br/><br/>  복제된 데이터를 저장소 계정에 저장하고 온-프레미스 사이트에서 장애 조치가 발생한 경우 복제된 데이터를 사용하여 Azure VM을 만듭니다. Azure VM을 만들 때 Azure 가상 네트워크에 연결합니다.
**구성 서버** | 모든 온-프레미스 구성 요소(단일 관리 서버구성 서버, 프로세스 서버, 마스터 대상 서버)를 실행하는 단일 관리 서버(VMWare VM) | 구성 서버는 온-프레미스와 Azure 간의 통신을 조정하여 데이터 복제를 관리합니다.
 **프로세스 서버**:  | 기본적으로 구성 서버에 설치합니다. | 복제 게이트웨이의 역할을 합니다. 복제 데이터를 수신하고 캐싱, 압축 및 암호화를 사용하여 최적화하며 복제 데이터를 Azure Storage로 전송합니다.<br/><br/> 또한 프로세스 서버는 보호되는 컴퓨터에서 모바일 서비스의 푸시 설치를 처리하며 VMware VM의 자동 복구를 수행합니다.<br/><br/> 배포가 늘어나면 프로세스 서버로 실행하는 별도의 추가 전용 서버를 추가하여 증가하는 복제 트래픽을 처리할 수 있습니다.
 **마스터 대상 서버** | 기본적으로 온-프레미스 구성 서버에 설치합니다. | Azure에서 장애 복구 중에 복제 데이터를 처리합니다.<br/><br/> 장애 복구 트래픽 볼륨이 높은 경우 장애 복구를 위해 별도 마스터 대상 서버를 배포할 수 있습니다.
**VMware 서버** | VMware VM은 vSphere ESXi 서버에서 호스트되며 호스트를 관리하는 vCenter 서버를 사용하는 것이 좋습니다. | VMware 서버를 사용자의 Recovery Services 자격 증명 모음에 추가합니다.<br/><br/> I
**복제된 컴퓨터** | 복제하려는 각 VMware VM에 모바일 서비스가 설치됩니다. 각 컴퓨터에 수동으로 설치되거나 프로세스 서버에서 강제 설치로 설치될 수 있습니다.

**그림 1: VMware에서 Azure 구성 요소로**

![구성 요소](./media/site-recovery-components/arch-enhanced.png)

### <a name="replication-process"></a>복제 프로세스

1. Azure 구성 요소 및 Recovery Services 자격 증명 모음을 포함하여 배포를 설정합니다. 자격 증명 모음에서 복제 원본 및 대상을 지정하고 구성 서버를 설정하며 VMware 서버를 추가하고 복제 정책을 만들고 모바일 서비스를 배포하고 복제를 사용하도록 설정하며 테스트 장애 조치를 실행합니다.
2.  컴퓨터는 복제 정책에 따라 복제를 시작하고 데이터의 초기 복사본은 Azure Storage로 복제됩니다.
4. Azure에 대한 델타 변경 내용의 복제는 초기 복제가 완료된 후에 시작됩니다. .hrl 파일에는 컴퓨터에 대한 추적된 변경 내용이 유지됩니다.
    - 컴퓨터를 복제하는 작업은 복제 관리를 위해 HTTPS 443 인바운드 포트에 대한 구성 서버와 통신합니다.
    - 컴퓨터를 복제하는 작업은 복제 데이터를 HTTPS 9443 인바운드 포트(구성될 수 있음)에 대한 프로세스 서버로 전달합니다.
    - 구성 서버는 HTTPS 443 아웃바운드 포트를 통해 Azure를 사용하는 복제 관리를 오케스트레이션합니다.
    - 프로세스 서버가 원본 컴퓨터에서 데이터를 수신하고 이를 최적화 및 암호화하며 443 아웃바운드 포트를 통해 Azure Storage로 전송합니다.
    - 다중 VM 일관성을 사용하도록 설정하면 복제 그룹의 컴퓨터는 20004 포트를 통해 서로 통신하게 됩니다. 다중 VM은 장애 조치 시(failover) 크래시 일관성 및 앱 일관성 복구 지점을 공유하는 복제 그룹에 여러 컴퓨터를 그룹화하는 경우 사용됩니다. 이 기능은 컴퓨터가 동일한 워크로드를 실행하고 일관성을 유지해야 할 경우에 유용합니다.
5. 트래픽은 인터넷을 통해 Azure Storage 공용 끝점에 복제됩니다. Azure ExpressRoute [공용 피어링](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-circuit-peerings#public-peering)을 사용할 수도 있습니다. 온-프레미스 사이트에서 Azure로의 사이트 간 VPN을 통한 트래픽 복제는 지원되지 않습니다.

**그림 2: VMware에서 Azure 복제로**

![향상된](./media/site-recovery-components/v2a-architecture-henry.png)

### <a name="failover-and-failback"></a>장애 조치 및 장애 복구

1. 예상대로 작동하는 테스트 장애 조치(failover)를 확인한 후에 필요에 따라 Azure에 계획되지 않은 장애 조치(failover)를 실행할 수 있습니다. 계획된 장애 조치는 지원되지 않습니다.
2. 단일 컴퓨터에 장애 조치를 수행하거나 [복구 계획](site-recovery-create-recovery-plans.md)을 만들어서 여러 VM의 장애 조치를 실행할 수 있습니다.
3. 장애 조치를 실행하면 Azure에 복제본 VM이 만들어집니다. 복제본 Azure VM에서 워크로드에 액세스하려면 장애 조치를 커밋합니다.
4. 기본 온-프레미스 사이트를 다시 사용할 수 있는 경우 장애 복구를 수행할 수 있습니다. 장애 복구 인프라를 설정하고 보조 사이트에서 기본 사이트에 컴퓨터를 복제하기 시작하며 보조 사이트에서 계획되지 않은 장애 조치를 실행합니다. 이 장애 조치를 커밋한 후에 데이터가 다시 온-프레미스로 돌아오면 Azure에 다시 복제를 사용하도록 설정해야 합니다. [자세히 알아보기](site-recovery-failback-azure-to-vmware.md)

몇 가지 장애 복구 요구 사항이 있습니다.


- **Azure에서 임시 프로세스 서버**: 장애 조치 후 Azure에서 장애 복구하려면 Azure에서 복제를 처리하기 위해 프로세스 서버로 구성된 Azure VM을 설정해야 합니다. 장애 복구를 완료한 후 이 VM을 삭제할 수 있습니다.
- **VPN 연결**: 장애 복구를 수행하려면 Azure 네트워크에서 온-프레미스 사이트로 VPN 연결(또는 Azure Express 경로) 설정이 필요합니다.
- **별도의 온-프레미스 마스터 대상 서버**: 온-프레미스 마스터 대상 서버는 장애 복구를 처리합니다. 마스터 대상 서버는 기본적으로 관리 서버에 설치되지만 대량의 트래픽 볼륨을 장애 복구하는 경우 이 목적으로 별도의 온-프레미스 마스터 대상 서버를 설정해야 합니다.
- **장애 복구 정책**: 온-프레미스 사이트에 다시 복제하려면 장애 복구 정책이 필요합니다. 이 정책은 복제 정책을 만들 때 자동으로 생성됩니다.

**그림 3: VMware/물리적 장애 복구**

![장애 복구](./media/site-recovery-components/enhanced-failback.png)

## <a name="physical-server-replication-to-azure"></a>Azure에 실제 서버 복제

이 복제 시나리오도 [VMware에서 Azure](#vmware-replication-to-azure)와 동일한 구성 요소와 프로세스를 사용합니다. 단, 다음과 같은 차이점이 있습니다.

- VMware VM 대신 구성 서버에 대한 물리적 서버를 사용할 수 있습니다.
- 장애 복구를 위해 온-프레미스 VMware 인프라가 필요합니다. 실제 컴퓨터로 장애 복구를 수행할 수 없습니다.

## <a name="hyper-v-replication-to-azure"></a>Azure로 Hyper-V 복제

**영역** | **구성 요소** | **세부 정보**
--- | --- | ---
**Azure** | Azure에서는 Microsoft Azure 계정, Azure Storage 계정 및 Azure 네트워크가 필요합니다. | 저장소 및 네트워크는 Resource Manager 기반 계정 또는 기존 계정일 수 있습니다.<br/><br/> 복제된 데이터를 저장소 계정에 저장하고 온-프레미스 사이트에서 장애 조치가 발생한 경우 복제된 데이터를 사용하여 Azure VM을 만듭니다.<br/><br/> Azure VM을 만들 때 Azure 가상 네트워크에 연결합니다.
**VMM 서버** | VMM 클라우드에 있는 Hyper-V 호스트 | VMM 클라우드에서 Hyper-V 호스트를 관리하는 경우 Recovery Services 자격 증명 모음에 VMM 서버를 등록합니다.<br/><br/> VMM 서버에서 Site Recovery Provider를 설치하여 Azure로 복제를 오케스트레이션합니다.<br/><br/> 논리 및 VM 네트워크를 설정하여 네트워크 매핑을 구성해야 합니다. VM 네트워크는 클라우드와 연결된 논리 네트워크에 연결되어야 합니다.
**Hyper-V 호스트** | Hyper-V 서버는 VMM 서버를 사용하거나 사용하지 않고 배포할 수 있습니다. | VMM 서버가 없는 경우 Site Recovery 복제를 오케스트레이션하기 위해 Site Recovery Provider가 인터넷을 통해 호스트에 설치됩니다. VMM 서버가 있는 경우, 공급자는 호스트가 아니라 서버에 설치됩니다.<br/><br/> Recovery Services 에이전트는 호스트에 설치되어 데이터 복제를 처리합니다.<br/><br/> 공급자 및 에이전트로부터의 통신은 모두 보호 및 암호화됩니다. Azure 저장소에 복제된 데이터도 암호화됩니다.
**Hyper-V VM** | Hyper-V 호스트 서버에 하나 이상의 VM이 필요합니다. | 명시적으로 VM에 설치해야 하는 것은 없음


### <a name="replication-process"></a>복제 프로세스

1. Azure 구성 요소를 설정합니다. Site Recovery 배포를 시작하기 전에 저장소 및 네트워크 계정을 설정하는 것이 좋습니다.
2. Site Recovery에 대한 복제 서비스 자격 증명 모음을 만들고 다음을 포함한 자격 증명 모음 설정을 구성합니다.
    - 원본 및 대상 설정. VMM 클라우드에서 Hyper-V 호스트를 관리하지 않으면 대상의 경우 Hyper-V 사이트 컨테이너를 만들고 여기에 Hyper-V 호스트를 추가합니다. Hyper-V 호스트가 VMM에서 관리하는 경우 원본은 VMM 클라우드입니다. 대상은 Azure입니다.
    - Azure Site Recovery 공급자 및 Microsoft Azure Recovery Services 에이전트 설치 VMM을 사용하는 경우 여기에 공급자가 설치되고 각 Hyper-V 호스트에 에이전트가 설치됩니다. VMM이 없는 경우 공급자와 에이전트는 각 호스트에 설치됩니다.
    - Hyper-V 사이트 또는 VMM 클라우드에 대한 복제 정책을 만듭니다. 정책은 사이트 또는 클라우드의 호스트에 있는 모든 VM에 적용됩니다.
    - Hyper-V VM에 복제를 사용하도록 설정합니다. 초기 복제는 복제 정책 설정에 따라 발생합니다.
4. 데이터 변경을 추적하고 Azure에 대한 델타 변경 내용의 복제를 초기 복제가 완료된 후에 시작합니다. .hrl 파일에는 항목에 대한 추적된 변경 내용이 유지됩니다.
5. 모든 것이 제대로 작동하는지 확인할 수 있도록 테스트 장애 조치를 실행합니다.

### <a name="failover-and-failback-process"></a>장애 조치 및 장애 복구 프로세스

1. 온-프레미스 Hyper-V VM에서 Azure로 계획된 또는 계획되지 않은 [장애 조치](site-recovery-failover.md)를 실행할 수 있습니다. 계획된 장애 조치를 실행할 경우 데이터 손실을 방지하기 위해 원본 VM이 종료됩니다.
2. 단일 컴퓨터에 장애 조치를 수행하거나 [복구 계획](site-recovery-create-recovery-plans.md)을 만들어서 여러 컴퓨터의 장애 조치를 오케스트레이션할 수 있습니다.
4. 장애 조치를 실행한 후에 Azure에서 만들어진 복제본 VM이 표시되어야 합니다. 필요한 경우 VM에 공용 IP 주소를 할당할 수 있습니다.
5. 그런 다음 복제본 Azure VM에서 워크로드에 액세스하려면 장애 조치를 커밋합니다.
6. 기본 온-프레미스 사이트를 다시 사용할 수 있는 경우 [장애 복구](site-recovery-failback-from-azure-to-hyper-v.md)를 수행할 수 있습니다. Azure에서 기본 사이트로 계획된 장애 조치를 시작합니다. 계획된 장애 조치의 경우 데이터가 손실되지 않도록 동일한 VM 또는 대체 위치에 장애 복구를 수행하고 Azure와 온-프레미스 간의 변경 내용을 동기화할 수 있습니다. 온-프레미스에 VM이 만들어지면 장애 조치를 커밋합니다.

**그림 4: Hyper-V 사이트에서 Azure로 복제**

![구성 요소](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

**그림 5: VMM 클라우드의 Hyper-V에서 Azure로 복제**

![구성 요소](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)


## <a name="replication-to-a-secondary-site"></a>보조 사이트로 복제

다음 사항을 보조 사이트로 복제할 수 있습니다.

- **VMware**: [지원되는 호스트](site-recovery-support-matrix-to-sec-site.md#on-premises-servers)에서 실행되는 온-프레미스 VMware VM. [지원되는 운영 체제](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions)에서 실행되는 VMware VM을 복제할 수 있습니다.
- **물리적 컴퓨터**: [지원되는 운영 체제](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions)에서 Windows 또는 Linux를 실행하는 온-프레미스 물리적 서버.
- **Hyper-V**: VMM 클라우드에서 관리되는 [지원되는 Hyper-V 호스트](site-recovery-support-matrix-to-sec-site.md#on-premises-servers)에서 실행되는 온-프레미스 Hyper-V VM. [지원되는 호스트](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers). [Hyper-V 및 Azure에서 지원하는](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) 게스트 운영 체제를 실행하는 Hyper-V VM을 복제할 수 있습니다.


## <a name="vmware-vmphysical-server-replication-to-a-secondary-site"></a>보조 사이트에 VMware VM/물리적 서버 복제

### <a name="components"></a>구성 요소

**영역** | **구성 요소** | **세부 정보**
--- | --- | ---
**Azure** | InMage Scout를 사용하여 이 시나리오를 배포합니다. | InMage Scout를 얻으려면 Azure 구독이 필요합니다.<br/><br/> Recovery Services 자격 증명 모음을 만든 후에 InMage Scout를 다운로드하고 최신 업데이트를 설치하여 배포를 설정합니다.
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

**그림 6: VMware에서 VMware로 복제**

![VMware 간](./media/site-recovery-components/vmware-to-vmware.png)



## <a name="hyper-v-vm-replication-to-a-secondary-site"></a>보조 사이트로 Hyper-V VM 복제


**영역** | **구성 요소** | **세부 정보**
--- | --- | ---
**Azure** | Microsoft Azure 계정이 있어야 합니다. |
**VMM 서버** | VMM 서버는 기본 사이트에서&1;개, 보조 사이트에서&1;개를 사용하는 것이 좋습니다. | 각 VMM 서버는 인터넷에 연결되어야 합니다.<br/><br/> 각 서버에는 Hyper-V 기능 프로필 집합을 가진 하나 이상의 VMM 사설 클라우드가 있어야 합니다.<br/><br/> VMM 서버에 Azure Site Recovery 공급자를 설치합니다. 공급자는 인터넷을 통해 사이트 복구 서비스에서의 복제를 조정 및 오케스트레이션합니다. 공급자와 Azure 간의 통신은 모두 안전하고 암호화됩니다.
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

**그림 7: VMM에서 VMM 복제로**

![온-프레미스 간](./media/site-recovery-components/arch-onprem-onprem.png)

### <a name="failover-and-failback"></a>장애 조치 및 장애 복구

1. 온-프레미스 사이트 간에 계획된 또는 계획되지 않은 [장애 조치](site-recovery-failover.md)를 실행할 수 있습니다. 계획된 장애 조치를 실행할 경우 데이터 손실을 방지하기 위해 원본 VM이 종료됩니다.
2. 단일 컴퓨터에 장애 조치를 수행하거나 [복구 계획](site-recovery-create-recovery-plans.md)을 만들어서 여러 컴퓨터의 장애 조치를 오케스트레이션할 수 있습니다.
4. 보조 사이트에 계획되지 않은 장애 조치를 수행했다면 장애 조치 후에 보조 위치에 있는 장애 조치 컴퓨터는 보호 또는 복제하도록 설정되지 않습니다. 계획된 장애 조치를 실행했으면 장애 조치 후에 보조 위치에 있는 컴퓨터가 보호됩니다.
5. 그런 다음 장애 조치를 커밋하여 복제본 VM에서 워크로드에 액세스합니다.
6. 기본 사이트를 사용할 수 있는 경우 역방향 복제를 시작하여 보조 사이트에서 기본 사이트로 복제합니다. 역방향 복제는 가상 컴퓨터를 보호된 상태로 가져오지만 보조 데이터 센터는 여전히 활성 위치입니다.
7. 기본 사이트를 활성 위치로 다시 만들려면 다른 역방향 복제 후에 보조 사이트에서 기본 사이트로 계획된 장애 조치를 시작합니다.


## <a name="hyper-v-replication-workflow"></a>Hyper-V 복제 워크플로

**워크플로 단계** | **작업**
--- | ---
1. **보호 사용** | Hyper-V VM에 보호를 사용하도록 설정한 후에 컴퓨터 필수 구성 요소를 준수하는지 확인하기 위해 **보호 사용** 작업이 시작됩니다. 이 작업은 다음과 같은 두 메서드를 호출합니다.<br/><br/> 구성한 설정을 사용하여 복제를 설정하는 [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx)입니다.<br/><br/> 전체 VM 복제를 초기화하는 [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx)입니다.
2. **초기 복제** |  모두 보조 위치에 복제될 때까지 가상 컴퓨터 스냅숏을 만들고 가상 하드 디스크를 하나씩 복제합니다.<br/><br/> 이 작업의 완료 시간은 VM 크기 및 네트워크 대역폭 및 초기 복제 방법에 따라 결정됩니다.<br/><br/> 초기 복제 진행 중에 디스크가 변경될 경우, 디스크와 동일한 폴더에 있는 Hyper-V 복제 로그(.hrl)를 통해 Hyper-V Replica Replication Tracker가 이러한 변경 내용을 추적합니다.<br/><br/> 각 디스크에는 보조 저장소로 전송되는 .hrl 파일이 연결되어 있습니다.<br/><br/> 초기 복제 진행 중에는 스냅숏과 로그 파일이 디스크 리소스를 사용합니다. 초기 복제가 완료되면 VM 스냅숏이 삭제되고 로그의 델타 디스크 변경 내용이 동기화 및 병합됩니다.
3. **보호 완료** | 초기 복제를 완료한 후에 **보호 완료** 작업에서 가상 컴퓨터를 보호하도록 네트워크 및 기타 복제 후 설정을 구성합니다.<br/><br/> Azure에 복제할 경우 장애 조치를 위해 가상 컴퓨터에 대한 설정을 조정해야 할 수 있습니다.<br/><br/> 이 시점에서 테스트 장애 조치(Failover)를 실행하여 모든 것이 예상대로 작동하는지 확인할 수 있습니다.
4. **복제** | 초기 복제 후에는 복제 설정에 따라 델타 동기화가 시작됩니다.<br/><br/> **복제 실패**: 델타 복제에 실패했고 전체 복제에는 대역폭이나 시간이 많이 소모될 경우 다시 동기화가 발생합니다. 예를 들어 .hrl 파일이 디스크 크기에 50%에 달한다면 VM이 다시 동기화되도록 표시됩니다. 다시 동기화는 원본 및 대상 가상 컴퓨터 디스크의 체크섬을 계산하고 델타만 전송하므로 보내는 데이터 크기가 최소화됩니다. 다시 동기화가 완료되면 델타 복제가 다시 시작됩니다. 기본적으로 다시 동기화는 업무 시간 이외에 실행되도록 예약되나 수동으로 가상 컴퓨터를 다시 동기화할 수 있습니다.<br/><br/> **복제 오류**: 복제 오류가 발생한 경우 기본 제공 재시도가 있습니다. 복구할 수 없는 오류(예: 인증 또는 권한 오류)가 발생했거나 복제 컴퓨터가 잘못된 상태인 경우 재시도를 수행하지 않습니다. 복구할 수 없는 오류(예: 네트워크 오류, 디스크 용량/메모리 부족)의 경우 재시도 간의 간격을 늘려가며 재시도를 수행합니다(예: 1, 2, 4, 8, 10분 후 30분마다).
5. **계획된/계획되지 않은 장애 조치** | 필요한 경우 계획된 또는 계획되지 않은 장애 조치를 실행할 수 있습니다.<br/><br/> 계획된 장애 조치를 실행할 경우 데이터 손실을 방지하기 위해 원본 VM이 종료됩니다.<br/><br/> 복제본 VM이 만들어진 후에는 커밋 대기 중 상태에 배치됩니다. 원본 VM을 커밋하여 장애 조치를 완료해야 합니다.<br/><br/> 기본 사이트가 실행된 후에 사용 가능한 경우 기본 사이트에 다시 장애 복구할 수 있습니다.


**그림 8: Hyper-V 워크플로**

![워크플로](./media/site-recovery-components/arch-hyperv-azure-workflow.png)




## <a name="next-steps"></a>다음 단계

[필수 구성 요소 확인](site-recovery-prereq.md)

