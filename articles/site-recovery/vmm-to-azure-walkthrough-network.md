---
title: "Azure Site Recovery를 사용하여 Azure로의 Hyper-V(System Center VMM 포함) 복제를 위한 네트워킹 계획 | Microsoft 문서"
description: "이 문서에서는 Azure로 Hyper-V VM(VMM 포함)을 복제하는 데 필요한 네트워킹 계획을 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 41c3c83e-6b1a-496a-8179-498c552ef0c7
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: ef87cd82b021e40f0da05142878daff245cd9c62
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="step-4-plan-networking-for-hyper-v-with-vmm-to-azure-replication"></a>4단계: Azure로의 Hyper-V(VMM 포함) 복제를 위한 네트워킹 계획

전체 배포를 진행하려는 경우 [용량 계획](vmm-to-azure-walkthrough-capacity.md)을 수행한 후 이 문서의 내용을 참조하여 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용해 System Center Virtual Machine Manager(VMM) 클라우드의 온-프레미스 Hyper-V VM을 Azure로 복제할 때의 네트워크 계획 고려 사항에 대해 알아볼 수 있습니다.

이 문서의 하단에서 의견을 게시하거나 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 질문하세요.


## <a name="network-mapping-for-replication-to-azure"></a>Azure로의 복제를 위한 네트워크 매핑

네트워크 매핑은 Hyper-V VM(VMM에서 관리됨)을 Azure로 복제할 때 사용됩니다. 네트워크 매핑은 원본 VMM 서버의 VM 네트워크와 대상 Azure 네트워크 사이를 매핑합니다. 매핑은 다음을 수행합니다.

- **네트워크 연결** - 장애 조치(failover) 후에 복제된 모든 Azure VM이 매핑된 Azure 네트워크에 연결됩니다. 동일한 네트워크에서 장애 조치되는 모든 컴퓨터가 서로 연결될 수 있으며, 이는 서로 다른 복구 계획에서 장애 조치된 경우에도 마찬가지입니다.
- **네트워크 게이트웨이** - 대상 Azure 네트워크에 네트워크 게이트웨이가 설정되어 있으면 VM은 매핑된 온-프레미스 네트워크의 다른 온-프레미스 가상 컴퓨터에 연결할 수 있습니다.

### <a name="prepare-vmm-for-network-mapping"></a>네트워크 매핑용 VMM 준비

다음과 같이 네트워크 매핑용 VMM을 준비합니다.

1. VMM이 없으면 Hyper-V 호스트가 있는 클라우드와 연결된 [VMM 논리 네트워크](https://docs.microsoft.com/system-center/vmm/network-logical)를 준비합니다.
2. VMM이 없으면 위에서 준비한 논리 네트워크에 연결된 [VM 네트워크](https://docs.microsoft.com/system-center/vmm/network-virtual)를 만듭니다.
3. VMM 클라우드의 Hyper-V 호스트 서버/클러스터에 있는 VM을 VM 네트워크에 연결합니다.

 
다음 사항에 유의하세요. 
- 원본 VM 네트워크에 추가된 새 VM은 복제된 후에 매핑된 Azure 네트워크에 연결됩니다.
- 대상 네트워크에 여러 서브넷이 있고 이 서브넷 중 하나의 이름이 원본 가상 컴퓨터가 있는 서브넷과 같으면 복제 가상 컴퓨터가 장애 조치(failover) 후에 대상 서브넷에 연결됩니다.
- 일치하는 이름을 가진 대상 서브넷이 없으면 가상 컴퓨터가 네트워크의 첫 번째 서브넷에 연결됩니다.
- 시나리오를 배포하면서 Azure 네트워크를 준비하고 네트워크 매핑을 설정합니다.

## <a name="connecting-to-azure-vms-after-failover"></a>장애 조치(failover) 후 Azure VM에 연결

복제 및 장애 조치(failover) 전략을 계획할 경우 주요 질문 중 하나는 장애 조치(failover) 후에 Azure VM에 어떻게 연결하는가에 대한 질문입니다. 복제본 Azure VM에 대한 네트워크 전략을 설계할 때 몇 가지 선택 항목이 있습니다.

- **다른 IP 주소 사용**: 복제된 Azure VM 네트워크에 다른 IP 주소 범위를 사용하도록 선택할 수 있습니다. 이 시나리오에서 VM은 장애 조치(failover) 후에 새 IP 주소를 갖게 되며 DNS 업데이트가 필요합니다.
- **동일한 IP 주소 유지**: 기본 온-프레미스 네트워크에 있는 것과 동일한 IP 주소 범위를 장애 조치(failover) 후 Azure 네트워크에 대해 사용하려고 합니다.  동일한 IP 주소를 유지하면 장애 조치(failover) 후 네트워크 관련 문제가 줄어들어 복구가 간소화됩니다. 그러나 Azure에 복제하는 경우 장애 조치 후에 경로를 IP 주소의 새 위치로 업데이트해야 합니다.


## <a name="retain-ip-addresses"></a>IP 주소 유지

Site Recovery는 Azure에 장애 조치할 경우 서브넷 장애 조치를 사용하여 고정 IP 주소를 유지하는 기능을 제공합니다.

서브넷 장애 조치를 사용하는 경우 특정 서브넷은 사이트 1 또는 사이트 2에 있으며 두 사이트에 동시에 존재하지는 않습니다. 장애 조치(failover) 시 IP 주소 공간을 유지 관리하기 위해 라우터 인프라에 대해 프로그래밍 방식으로 정렬하여 한 사이트에서 다른 사이트로 서브넷을 이동할 수 있습니다. 장애 조치(failover) 중에 서브넷은 연결되어 있는 보호된 VM과 함께 이동합니다. 주요 단점은 오류 발생 시 전체 서브넷을 이동해야 한다는 점입니다.



### <a name="failover-example"></a>장애 조치 예제

Azure로 장애 조치(failover)에 관한 예제를 살펴보겠습니다.

- Woodgrove Bank라는 가상의 회사에는 비즈니스 앱을 호스팅하는 온-프레미스 인프라가 있습니다. 모바일 응용 프로그램은 Azure에서 호스팅됩니다.
- Azure 및 온-프레미스 서버에서 Woodgrove Bank VM 간의 연결은 온-프레미스 에지 네트워크와 Azure 가상 네트워크 간에 있는 사이트 간(VPN) 연결에서 제공됩니다.
- 이 VPN은 Azure에서 회사의 가상 네트워크가 온-프레미스 네트워크의 확장으로 나타난다는 것을 의미합니다.
- Woodgrove는 Site Recovery를 사용하여 온-프레미스 워크로드를 Azure로 복제하려고 합니다.
 - Woodgrove는 하드 코딩된 IP 주소에 종속된 응용 프로그램 및 구성을 처리해야 하므로 Azure로 장애 조치(failover)한 이후 해당 응용 프로그램의 IP 주소를 유지해야 합니다.
 - Woodgrove는 172.16.1.0/24, 172.16.2.0/24의 IP 주소를 Azure에서 실행 중인 해당 리소스에 할당합니다.


Woodgrove가 IP 주소를 유지하는 동시에 해당 VM을 Azure로 복제할 수 있으려면 회사는 다음을 수행해야 합니다.

1. Azure 가상 네트워크를 만듭니다. 응용 프로그램이 원활하게 장애 조치(failover)할 수 있도록 온-프레미스 네트워크를 확장해야 합니다.
2. Azure를 사용하면 Azure에서 만든 가상 네트워크에 대한 지점 및 사이트 간 연결뿐만 아니라 사이트 간 VPN 연결을 추가할 수 있습니다.
3. 사이트 간 연결을 설정하는 경우 Azure 네트워크에서 IP 주소 범위가 온-프레미스 IP 주소 범위와 다른 경우에만 온-프레미스 위치(로컬 네트워크)에 트래픽을 라우팅할 수 있습니다.
    - 이는 Azure는 확대 서브넷을 지원하지 않기 때문입니다. 따라서 온-프레미스에 서브넷 192.168.1.0/24가 있는 경우 Azure 네트워크에 로컬 네트워크 192.168.1.0/24를 추가할 수 없습니다.
    - 이렇게 예측하는 이유는 Azure가 서브넷에 활성 VM이 없고 해당 서브넷이 재해 복구용으로만 만들어진다는 것을 모르기 때문입니다.
    - Azure 네트워크 외부에서 네트워크 트래픽을 제대로 라우팅할 수 있으려면 네트워크 및 로컬 네트워크의 서브넷이 충돌하지 않아야 합니다.

![서브넷 장애 조치(failover) 전](./media/vmm-to-azure-walkthrough-network/network-design7.png)

### <a name="before-failover"></a>장애 조치(failover) 전

1. 추가 네트워크(예: 복구 네트워크)를 만듭니다. 이것은 장애 조치 VM이 생성되는 네트워크입니다.
2. VM에 사용할 IP 주소가 장애 조치(failover) 후에 유지되는지 확인하려면 VM 속성 > **구성**에서 온-프레미스 VM과 동일한 IP 주소를 지정하고 **저장**을 클릭합니다.
3. VM이 장애 조치되는 경우 Azure Site Recovery는 가상 컴퓨터에 제공된 IP 주소를 할당합니다.

    ![네트워크 속성](./media/vmm-to-azure-walkthrough-network/network-design8.png)

4. 장애 조치가 트리거되고 Azure에서 필수 IP 주소로 VM이 생성된 후 [Vnet 대 Vnet 연결](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)을 사용하여 네트워크에 연결할 수 있습니다. 이 작업은 스크립팅될 수 있습니다.
5. 이제 192.168.1.0/24가 Azure로 이동되었음을 반영하도록 경로를 적절하게 수정해야 합니다.

    ![서브넷 장애 조치(failover) 후](./media/vmm-to-azure-walkthrough-network/network-design9.png)

### <a name="after-failover"></a>장애 조치(failover) 후

위에서 설명한 대로 Azure 네트워크가 없는 경우 장애 조치(failover) 후 기본 사이트와 Azure 간의 사이트 간 VPN 연결을 만들 수 있습니다.

## <a name="change-ip-addresses"></a>IP 주소 변경

이 [블로그 게시물](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/)은 장애 조치(failover) 후 IP 주소를 유지할 필요가 없을 때 Azure 네트워킹 인프라를 설정하는 방법을 설명합니다. 응용 프로그램 설명으로 시작하여 온-프레미스 및 Azure의 네트워킹을 설정하는 방법을 찾고, 장애 조치(failover)를 실행하는 방법에 대한 정보로 마무리합니다.  

## <a name="next-steps"></a>다음 단계

[5단계: Azure 준비](vmm-to-azure-walkthrough-prepare-azure.md)로 이동합니다.
