---
title: "Azure Site Recovery를 사용하여 Azure로 Hyper-V(System Center VMM 없음) 복제를 위한 네트워킹 계획 | Microsoft Docs"
description: "이 문서에서는 Azure로 Hyper-V VM(VMM 없음)을 복제하는 데 필요한 네트워킹 계획을 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5ca12254-975d-48e8-a84d-422825dac9b2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: 4cacf3ca47b215bc23545d8a0b5c9b6fbd66a8df
ms.contentlocale: ko-kr
ms.lasthandoff: 06/23/2017


---

# <a name="step-4-plan-networking-for-hyper-v-to-azure-replication"></a>4단계: Azure로 Hyper-V 복제를 위한 네트워킹 계획

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 온-프레미스 Hyper-V VM(System Center VMM 없음)을 Azure로 복제할 때 네트워킹 계획 고려 사항을 설명합니다.

이 문서의 하단에서 의견을 게시하거나 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 질문하세요.


## <a name="connecting-to-replica-vms-after-failover"></a>장애 조치(failover) 후 복제본 VM에 연결

복제 및 장애 조치(failover) 전략을 계획할 경우 주요 질문 중 하나는 장애 조치(failover) 후에 Azure VM에 어떻게 연결하는가입니다. 복제본 Azure VM에 대한 네트워크 전략을 설계할 때 몇 가지 선택 항목이 있습니다.

- **다른 IP 주소**: 복제된 Azure VM 네트워크에 다른 IP 주소 범위를 사용하도록 선택할 수 있습니다. 이 시나리오에서 VM은 장애 조치(failover) 후에 새 IP 주소를 갖게 되며 DNS 업데이트가 필요합니다. [자세히 알아보기](site-recovery-test-failover-vmm-to-vmm.md#prepare-the-infrastructure-for-test-failover)
- **동일한 IP 주소**: 기본 온-프레미스 사이트에 있는 것과 동일한 IP 주소 범위를 장애 조치(failover) 후 Azure에서 사용할 수도 있습니다. 일반적인 시나리오에서 IP 주소의 새 위치를 사용하여 경로를 업데이트해야 합니다. 그러나 기본 사이트와 Azure 간 배포된 확대 VLAN이 있는 경우에는 가상 컴퓨터에 대한 IP 주소를 유지하는 것은 유효한 옵션이 됩니다. 동일한 IP 주소를 유지하면 장애 조치(failover) 후 네트워크 관련 문제가 줄어들어 복구가 간소화됩니다.


## <a name="retain-ip-addresses"></a>IP 주소 유지

재해 복구 관점에서 고정된 IP 주소를 사용하는 것은 가장 간단한 방법이긴 하나, 몇 가지 잠재적인 문제가 있습니다. Site Recovery는 Azure로 장애 조치할 때 서브넷 장애 조치(failover)를 사용하여 IP 주소를 유지하는 기능을 제공합니다.


### <a name="subnet-failover"></a>서브넷 장애 조치(failover)

이 시나리오에서 특정 서브넷은 사이트 1 또는 사이트 2에 있으며 두 사이트에 동시에 존재하지는 않습니다. 장애 조치(failover) 시 IP 주소 공간을 유지 관리하기 위해 라우터 인프라에 대해 프로그래밍 방식으로 정렬하여 한 사이트에서 다른 사이트로 서브넷을 이동할 수 있습니다. 장애 조치(failover) 도중 서브넷은 연결되어 있는 보호된 VM과 함께 이동합니다. 이 방법의 주요 단점은 오류 발생 시 전체 서브넷을 이동해야 한다는 점으로, 이는 장애 조치(failover)의 세부적인 고려 사항에 영향을 미칠 수 있습니다.


### <a name="failover-example"></a>장애 조치(failover) 예제

Azure로 장애 조치(failover)에 관한 예제를 살펴보겠습니다.

- Woodgrove Bank라는 가상의 회사에는 비즈니스 앱을 호스팅하는 온-프레미스 인프라가 있습니다. 모바일 응용 프로그램은 Azure에서 호스팅됩니다.
- Azure 및 온-프레미스 서버에서 Woodgrove Bank VM 간의 연결은 사이트 간(VPN) 온-프레미스 에지 네트워크와 Azure 가상 네트워크 간 연결에서 제공됩니다.
- 이 VPN은 Azure에서 회사의 가상 네트워크가 온-프레미스 네트워크의 확장으로 나타난다는 것을 의미합니다.
- Woodgrove는 Site Recovery를 사용하여 온-프레미스 워크로드를 Azure로 복제하려고 합니다.
 - Woodgrove는 하드 코드된 IP 주소에 종속된 응용 프로그램 및 구성을 다루어야 하므로 Azure로 장애 조치(failover)한 이후 해당 응용 프로그램의 IP 주소를 유지해야 합니다.
 - Woodgrove는 172.16.1.0/24, 172.16.2.0/24의 IP 주소를 Azure에서 실행 중인 해당 리소스에 할당합니다.


Woodgrove가 IP 주소를 유지하는 동시에 해당 VM을 Azure로 복제할 수 있으려면 회사는 다음을 수행해야 합니다.

1. Azure 가상 네트워크를 만듭니다. 응용 프로그램이 원활하게 장애 조치(failover)할 수 있도록 온-프레미스 네트워크를 확장해야 합니다.
2. Azure를 사용하면 Azure에서 만든 가상 네트워크에 대한 지점 및 사이트 간 연결뿐만 아니라 사이트 간 VPN 연결을 추가할 수 있습니다.
3. 사이트 간 연결을 설정하는 경우 Azure 네트워크에서 IP 주소 범위가 온-프레미스 IP 주소 범위와 다른 경우에만 온-프레미스 위치(로컬 네트워크)에 트래픽을 라우팅할 수 있습니다.
    - 이는 Azure는 확대 서브넷을 지원하지 않기 때문입니다. 따라서 온-프레미스에 서브넷 192.168.1.0/24가 있는 경우 Azure 네트워크에 로컬 네트워크 192.168.1.0/24를 추가할 수 없습니다.
    - 이렇게 예측하는 이유는 Azure가 서브넷에 활성 VM이 없고 해당 서브넷이 재해 복구용으로만 만들어진다는 것을 모르기 때문입니다.
    - Azure 네트워크 외부에서 네트워크 트래픽을 제대로 라우팅할 수 있으려면 네트워크 및 로컬 네트워크의 서브넷이 충돌하지 않아야 합니다.

![서브넷 장애 조치(failover) 전](./media/hyper-v-site-walkthrough-network/network-design7.png)

### <a name="before-failover"></a>장애 조치(failover) 전

1. 추가 네트워크(예: 복구 네트워크)를 만듭니다. 이것은 장애 조치 VM이 생성되는 네트워크입니다.
2. VM에 사용할 IP 주소가 장애 조치(failover) 후에 유지되는지 확인하려면 VM 속성 > **구성**에서 온-프레미스 VM과 동일한 IP 주소를 지정하고 **저장**을 클릭합니다.
3. VM이 장애 조치되는 경우 Azure Site Recovery는 가상 컴퓨터에 제공된 IP 주소를 할당합니다.

    ![네트워크 속성](./media/hyper-v-site-walkthrough-network/network-design8.png)

4. 장애 조치가 트리거되고 Azure에서 필수 IP 주소로 VM이 생성된 후 [Vnet 대 Vnet 연결](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)을 사용하여 네트워크에 연결할 수 있습니다. 이 작업은 스크립팅될 수 있습니다.
5. 이제 192.168.1.0/24가 Azure로 이동되었음을 반영하도록 경로를 적절하게 수정해야 합니다.

    ![서브넷 장애 조치(failover) 후](./media/hyper-v-site-walkthrough-network/network-design9.png)

### <a name="after-failover"></a>장애 조치(failover) 후

위에서 설명한 대로 Azure 네트워크가 없는 경우 장애 조치(failover) 후 기본 사이트와 Azure 간의 사이트 간 VPN 연결을 만들 수 있습니다.

## <a name="change-ip-addresses"></a>IP 주소 변경

이 [블로그 게시물](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/)은 장애 조치(failover) 후 IP 주소를 유지할 필요가 없을 때 Azure 네트워킹 인프라를 설정하는 방법을 설명합니다. 응용 프로그램 설명으로 시작하여 온-프레미스 및 Azure의 네트워킹을 설정하는 방법을 찾고, 장애 조치(failover)를 실행 하는 방법에 대한 정보로 마무리합니다.  

## <a name="next-steps"></a>다음 단계

[5단계: Azure 준비](hyper-v-site-walkthrough-prepare-azure.md)로 이동합니다.

