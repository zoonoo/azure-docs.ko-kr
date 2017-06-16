---
title: "Azure Site Recovery 모범 사례 | Microsoft Docs"
description: "이 문서에서는 Azure Site Recovery 배포의 모범 사례를 설명합니다."
services: site-recovery
documentationCenter: 
author: rayne-wiselman"
manager: cfreeman
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/14/2017
ms.author: raynew
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: site-recovery-support-matrix-to-azure
ms.translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: a53e2a09fb2aabe96dd5347ea7509815d92bfdb8
ms.contentlocale: ko-kr
ms.lasthandoff: 03/15/2017

---

# <a name="get-ready-to-deploy-azure-site-recovery"></a>Azure Site Recovery 배포 준비

이 문서에서는 Azure Site Recovery 배포의 준비 방법을 설명합니다.

## <a name="protecting-hyper-v-virtual-machines"></a>Hyper-V 가상 컴퓨터 보호  

몇 가지 배포 방법으로 Hyper-v 가상 컴퓨터를 보호할 수 있습니다. Azure 또는 보조 데이터 센터에 온-프레미스 Hyper-VM을 복제할 수 있습니다. 배포마다 요구 사항이 다릅니다.

**요구 사항** | **Azure에 복제(VMM 있음)** | **Hyper-V VM을 Azure에 복제(VMM 없음)** | **Hyper-V VM을 Azure에 복제(VMM 없음)** | **세부 정보**
---|---|---|---|---
**VMM** | System Center 2012 R2에서 실행되는 VMM <br/><br/>하나 이상의 VMM 호스트 그룹을 포함하는 하나 이상의 VMM 클라우드 | 해당 없음 | 최신 업데이트가 설치된 System Center 2012 SP1 이상에서 실행 중인 기본 및 보조 사이트의 VMM 서버 <br/><br/> 각 VMM 서버에 클라우드가 하나 이상 있습니다. 클라우드에는 Hyper-V 용량 프로필이 설정되어 있어야 합니다.<br/><br/> 원본 클라우드에는 VMM 호스트 그룹이 하나 이상 있어야 합니다. | 선택 사항입니다. Azure에 Hyper-V 가상 컴퓨터를 복제하기 위해 System Center VMM 배포할 필요는 없지만, 배포할 경우 VMM 서버가 제대로 설정되어 있는지 확인해야 합니다. 올바른 VMM 버전 실행하고 있으며 클라우드가 설정되었는지 확인하는 것이 여기에 포함됩니다.
**Hyper-V** | Windows Server 2012 R2 이상을 실행하는 온-프레미스 사이트에서 하나 이상의 Hyper-V 호스트 서버 | 최신 업데이트가 설치되고 인터넷에 연결된 Windows Server 2012 이상을 실행하는 원본 및 대상 사이트에서 하나 이상의 Hyper-V 서버<br/><br/> Hyper-V 서버는 VMM 클라우드의 호스트 그룹에 있어야 합니다. | 최신 업데이트가 설치되고 인터넷에 연결된 Windows Server 2012 이상을 실행하는 원본 및 대상 사이트에서 하나 이상의 Hyper-V 서버<br/><br/> Hyper-V 서버는 VMM 클라우드의 호스트 그룹에 있어야 합니다.  |
**가상 컴퓨터** | 원본 Hyper-V 호스트 서버에서 하나 이상의 VM | 원본 VMM 클라우드의 Hyper-V 호스트 서버에서 하나 이상의 VM | 원본 VMM 클라우드의 Hyper-V 호스트 서버에서 하나 이상의 VM. |  Azure에 복제하는 VM은 Azure 가상 컴퓨터 필수 조건에 부합해야 합니다.
**Azure 계정** | Site Recovery 서비스를 위한 Azure 계정 및 구독이 필요합니다. | Site Recovery 서비스를 위한 Azure 계정 및 구독이 필요합니다. | 해당 없음 | 계정이 없는 경우 무료 평가판으로 시작하세요.
**Azure 저장소** | 지역에서 복제가 활성화된 Azure 저장소 계정에 대한 구독이 필요합니다. | 지역에서 복제가 활성화된 Azure 저장소 계정에 대한 구독이 필요합니다. | 해당 없음 | 계정은 Azure Site Recovery 자격 증명 모음과 동일한 지역에 있고 동일한 구독과 연결되어야 합니다.
**네트워킹** | 동일한 Azure 네트워크에서 장애 조치되는 모든 컴퓨터가 자신이 속한 복구 계획에 관계없이 서로 연결할 수 있도록 네트워크 매핑을 설정할 수 있습니다. 또한 네트워크 게이트웨이가 대상 Azure 네트워크에서 설정된 경우 가상 컴퓨터가 다른 온-프레미스 가상 컴퓨터에 연결할 수 있습니다. 동일한 복구 계획에서 장애 조치되는 네트워크 매핑 컴퓨터를 설정하지 않는 경우 연결 가능합니다. | 해당 없음 |  <br/><br/>장애 조치 후 가상 컴퓨터가 적합한 네트워크에 연결되고 복제본 가상 컴퓨터가 Hyper-V 호스트 서버에 최적으로 배치될 수 있게 네트워크 매핑을 설정합니다. 네트워크 매핑을 구성하지 않으면 장애 조치 후 복제된 컴퓨터가 어떤 VM 네트워크에도 연결되지 않습니다. |  VMM을 통해 네트워크 매핑을 설정하려면 VMM 논리적 및 VM 네트워크가 정확히 구성되었는지 확인해야 합니다.
**공급자 및 에이전트** | 배포 중에 VMM 서버에 Azure Site Recovery 공급자를 설치합니다. VMM 클라우드에 위치한 Hyper-V 호스트 서버에서는 Azure Recovery Services 에이전트를 설치합니다. | 배포 중에 Hyper-V 호스트 서버나 클러스터에서 Azure Site Recovery 공급자와 Azure Recovery Services 에이전트를 모두 설치합니다.| 배포 중에 VMM 서버에 Azure Site Recovery 공급자를 설치합니다. VMM 클라우드에 위치한 Hyper-V 호스트 서버에서는 Azure Recovery Services 에이전트를 설치합니다. | 암호화된 HTTPS 연결을 사용하여 공급자와 에이전트가 인터넷을 통해 사이트 복구에 연결합니다. 방화벽 예외를 추가하거나 공급자 연결을 위한 특정 프록시를 만들지 않아도 됩니다.
**인터넷 연결** | VMM 서버만 인터넷 연결이 필요합니다. | Hyper-V 호스트 서버만 인터넷 연결이 필요합니다. | VMM 서버만 인터넷 연결이 필요합니다. | 가상 컴퓨터에는 어떤 항목도 설치되지 않아도 되며 인터넷에 직접 연결하지 않습니다.



## <a name="protect-vmware-virtual-machines-or-physical-servers"></a>VMware 가상 컴퓨터 또는 물리적 서버 보호

몇 가지 배포 방법으로 VMware 가상 컴퓨터 또는 Windows/Linux 물리적 서버를 보호할 수 있습니다. Azure 또는 보조 데이터센터에 복제할 수 있습니다. 배포마다 요구 사항이 다릅니다.

**요구 사항** | **Azure에 VMware VM/물리적 서버 복제** |  *보조 사이트에 VMware VM/물리적 서버 복제**  
---|---|---
**기본 사이트** | **프로세스 서버**: 전용 Windows 서버(실제 또는 가상) | **프로세스 서버**: 전용 Windows 서버(실제 또는 VMware 가상 컴퓨터)<br/><br/>  
**보조 온-프레미스 사이트** | 해당 없음 | **구성 서버**: 전용 Windows 서버(실제 또는 가상) <br/><br/> **마스터 대상 서버**: 전용 서버(실제 또는 가상) Windows 컴퓨터를 보호하기 위해 Windows 또는 Linux 컴퓨터를 보호하기 위해 Linux를 구성합니다.
**Azure** | **구독**: Site Recovery 서비스를 위한 구독이 필요합니다. <br/><br/> **저장소 계정**: 지역에서 복제를 사용하는 저장소 계정이 필요합니다. 계정은 Site Recovery 자격 증명 모음과 동일한 지역에 있고 동일한 구독과 연결되어야 합니다. <br/><br/> **구성 서버**: Azure VM으로 구성 서버를 설정해야 합니다. <br/><br/> **마스터 대상 서버**: Azure VM으로 마스터 대상 서버를 설정해야 합니다. <br/><br/> Windows 컴퓨터를 보호하기 위해 Windows 또는 Linux 컴퓨터를 보호하기 위해 Linux를 구성합니다.<br/><br/> **Azure 가상 네트워크**: 구성 서버와 마스터 대상 서버를 배포할 Azure 가상 네트워크가 필요합니다. 이 네트워크는 Azure Site Recovery 자격 증명 모음과 동일한 구독 및 지역에 있어야 합니다. | 해당 없음  
**가상 컴퓨터/물리적 서버** | VMware 가상 컴퓨터 또는 물리적 Windows/Linux 서버가 하나 이상 필요합니다.<br/><br/>배포하는 동안 이동성 서비스가 각 컴퓨터에 설치됩니다.| 하나 이상의 VMware VM 또는 물리적 Windows/Linux 서버.<br/><br/> 배포 하는 동안 통합 에이전트가 각 컴퓨터에 설치됩니다.




## <a name="azure-virtual-machine-requirements"></a>Azure 가상 컴퓨터 요구 사항

Azure에서 지원하는 운영 체제를 실행하는 가상 컴퓨터와 물리적 서버를 복제하기 위해 Site Recovery를 배포할 수 있습니다. 여기에는 대부분 버전의 Windows 및 Linux가 포함됩니다. 보호할 온-프레미스 가상 컴퓨터가 Azure 요구 사항을 준수하는지 확인해야 합니다.


## <a name="optimizing-your-deployment"></a>배포 최적화

다음 팁을 사용하면 배포의 최적화 및 크기 조정에 도움이 됩니다.

- **운영 체제 볼륨 크기**: Azure에 가상 컴퓨터를 복제하는 경우 운영 체제 볼륨이 1TB 미만이어야 합니다. 이것보다 더 많은 볼륨이 있는 경우 배포를 시작하기 전에 볼륨을 다른 디스크로 수동으로 이동할 수 있습니다.
- **데이터 디스크 크기**: Azure에 복제하는 경우 최대 1TB의 각 가상 컴퓨터에서 최대 32 개의 데이터 디스크를 사용할 수 있습니다. ~32 TB 가상 컴퓨터를 효과적으로 복제 및 장애 조치할 수 있습니다.
- **복구 계획 제한**: 사이트 복구는 수천 개의 가상 컴퓨터로 확장할 수 있습니다. 복구 계획은 다 함께 장애 조치되어야 하는 응용 프로그램에 대한 모델로 설계되었으므로 복구 계획에서 컴퓨터의 수를 50으로 제한할 수 있습니다.
- **Azure 서비스 제한**: 모든 Azure 구독은 코어, 클라우드 서비스 등에 대한 일련의 기본 제한값이 제공됩니다. 구독에서 리소스의 가용성을 확인하기 위해 테스트 장애 조치(Failover)를 실행하는 것이 좋습니다. Azure 지원을 통해 이러한 제한을 수정할 수 있습니다.
- **용량 계획**: 크기 조정 및 성능을 위한 계획입니다.
- **복제 대역폭**: 복제 대역폭이 부족한 경우 다음에 유의하십시오.
    - **Express 경로**: 사이트 복구는 Riverbed와 같은 Azure Express 경로 및 WAN 최적화 프로그램과 함께 작동합니다.
    - **복제 트래픽**: 사이트 복구는 전체 VHD가 아닌 데이터 블록만 사용하여 스마트 초기 복제를 수행합니다. 복제가 진행 중일 때는 변경 내용만 복제됩니다.
    - **네트워크 트래픽**: 대상 IP 주소 및 포트 기반 정책을 포함하는 Windows QoS를 설정하여 복제에 사용된 네트워크 트래픽을 제어할 수 있습니다.  또한 Azure 백업 에이전트를 사용하여 Azure Site Recovery에 복제하는 경우. 해당 에이전트에 대한 제한을 구성할 수 있습니다.
- **RTO**: Site Recovery를 통해 예상할 수 있는 복제 시간 개체(RTO)를 측정하고 싶다면 테스트 장애 조치를 실행하고 작업 완료까지 시간이 얼마나 걸리는지 분석합니다. Azure에 장애 조치하는 경우 최선의 RTO를 위해 Azure 자동화 및 복구 계획과 통합하여 모든 수동 작업을 자동화하는 것이 좋습니다.
- **RPO**: Azure에 복제하는 경우 사이트 복구는 가까운 동기 복구 지점 목표 (RPO)를 지원 합니다. 이때 데이터 센터와 Azure 사이에서 충분한 대역폭을 가정합니다.

