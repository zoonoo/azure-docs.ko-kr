---
title: Azure VMware 솔루션에서 NSX 네트워크 구성 요소 구성
description: Azure VMware 솔루션 콘솔을 사용하여 NSX-T 네트워크 세그먼트를 구성하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: a4702efe8ea26234d074f421d2e6eab871eba5f5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105733100"
---
# <a name="configure-nsx-network-components-in-azure-vmware-solution"></a>Azure VMware 솔루션에서 NSX 네트워크 구성 요소 구성

Azure VMware Solution 프라이빗 클라우드는 기본적으로 SDDC(소프트웨어 정의 네트워크)로 NSX-T와 함께 제공됩니다. **활성/활성** 모드의 NSX-T 계층 0 게이트웨이와 활성/대기 모드의 기본 NSX-T 계층 1 게이트웨이로 사전 프로비전됩니다.  이러한 게이트웨이를 통해 세그먼트(논리적 스위치)를 연결하고 동-서 및 북-남 연결을 제공할 수 있습니다. 

Azure VMware 솔루션 프라이빗 클라우드를 배포하면 **워크로드 네트워킹** 의 Azure VMware Solution 콘솔에서 필요한 NSX-T 개체를 구성할 수 있습니다.  이 콘솔은 VMware 관리자가 매일 필요로 하고 NSX-T에 익숙하지 않은 사용자를 대상으로 하는 NSX-T 작업의 단순화된 보기를 제공합니다.  

Azure VMware 솔루션 콘솔에서 NSX-T 구성 요소를 구성하는 4가지 옵션이 있습니다.
- **세그먼트** - NSX-T Manager 및 vCenter에 표시되는 세그먼트를 만들 수 있습니다.
- **DHCP** - DHCP를 사용하려는 경우 DHCP 서버 또는 DHCP 릴레이를 만듭니다.
- **포트 미러링** – 네트워크 문제를 해결하는 데 도움이 되도록 포트 미러링을 만듭니다.
- **DNS** – 확인을 위해 지정된 DNS 서버에 DNS 요청을 보내는 DNS 전달자를 만듭니다.  

>[!NOTE]
>설명된 고급 설정 및 기타 NSX-T 기능을 사용할 수 있는 NSX-T Manager 콘솔에는 계속 액세스할 수 있습니다. 
 
:::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking.png" alt-text="NSX-T 구성을 위한 Azure VMware 솔루션 콘솔의 4가지 옵션을 보여 주는 스크린샷입니다.":::

## <a name="prerequisites"></a>사전 요구 사항
Azure VMware 솔루션 프라이빗 클라우드로 만들어지거나 마이그레이션된 VM(가상 머신)은 세그먼트에 연결되어야 합니다. 

## <a name="create-an-nsx-t-segment-in-the-azure-portal"></a>Azure Portal에서 NSX-T 세그먼트 만들기
Azure Portal의 Azure VMware Solution 콘솔에서 NSX-T 세그먼트를 만들고 구성할 수 있습니다.  이러한 세그먼트는 기본 계층 1 게이트웨이에 연결되며 세그먼트의 워크로드는 동-서 및 북-남 연결을 제공합니다. 세그먼트가 생성되면 NSX-T Manager 및 vCenter에 표시됩니다.

>[!NOTE]
>DHCP를 사용하려는 경우 NSX-T 세그먼트를 만들고 구성하기 전에 [DHCP 서버 또는 DHCP 릴레이를 구성](#create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal)해야 합니다.

1. Azure VMware Solution 프라이빗 클라우드의 **워크로드 네트워킹** 에서 **세그먼트** > **추가** 를 선택합니다. 새 논리 세그먼트에 대한 세부 정보를 제공하고 **확인** 을 선택합니다.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-new-nsxt-segment.png" alt-text="새 세그먼트 추가 방법을 보여 주는 스크린샷입니다.":::
   
   - **세그먼트 이름** - vCenter에 표시되는 논리 스위치의 이름입니다.
   - **서브넷 게이트웨이** - 서브넷 마스크가 있는 논리 스위치의 서브넷 게이트웨이 IP 주소입니다. VM은 논리 스위치에 연결되고, 이 스위치에 연결된 모든 VM은 동일한 서브넷에 속합니다.  또한 이 논리 세그먼트에 연결된 모든 VM은 동일한 세그먼트의 IP 주소를 가져야 합니다.
   - **DHCP**(선택 사항) - 논리적 세그먼트의 DHCP 범위입니다. [ DHCP 서버 또는 DHCP 릴레이](#create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal)는 세그먼트에서 DHCP를 사용하도록 구성되어야 합니다.  
   - **연결된 게이트웨이** - *기본적으로 선택되어 있으며 읽기 전용입니다.*  계층 1 게이트웨이 및 세그먼트 정보 유형입니다. 
      - **T1** - NSX-T Manager의 계층 1 게이트웨이 이름입니다. Azure VMware Solution 프라이빗 클라우드는 활성/활성 모드의 NSX-T 계층 0 게이트웨이 및 활성/대기 모드의 기본 NSX-T 계층 1 게이트웨이와 함께 제공됩니다.  Azure VMware Solution 콘솔을 통해 생성된 세그먼트는 기본 계층 1 게이트웨이에만 연결되며 이 세그먼트의 워크로드는 동-서 및 북-남 연결을 제공합니다. NSX-T Manager를 통해 더 많은 계층 1 게이트웨이를 만들 수 있습니다. NSX-T Manager 콘솔에서 생성된 계층 1 게이트웨이는 Azure VMware Solution 콘솔에 표시되지 않습니다. 
      - **유형** - Azure VMware Solution에서 지원하는 오버레이 세그먼트입니다.

   이제 세그먼트가 Azure VMware Solution 콘솔, NSX-T Manger 및 vCenter에 표시됩니다.

## <a name="create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal"></a>Azure Portal에서 DHCP 서버 또는 DHCP 릴레이 만들기
Azure Portal에서 Azure VMware Solution 콘솔을 통해 직접 DHCP 서버 또는 릴레이를 만들 수 있습니다. DHCP 서버 또는 릴레이는 Azure VMware Solution을 배포할 때 생성되는 계층 1 게이트웨이에 연결됩니다. DHCP 범위를 제공한 모든 세그먼트는 이 DHCP의 일부가 됩니다.  DHCP 서버 또는 DHCP 릴레이를 만든 후 이를 사용하려면 세그먼트 수준에서 서브넷 또는 범위를 정의해야 합니다.

1. Azure VMware Solution 프라이빗 클라우드의 **워크로드 네트워킹** 에서 **DHCP** > **추가** 를 선택합니다.

2. **DHCP 서버** 또는  **릴레이** 를 선택한 후 서버 또는 릴레이의 이름과 3개의 IP 주소를 제공합니다. 

   >[!NOTE]
   >DHCP 릴레이의 경우 성공적인 구성을 위해 하나의 IP 주소만 필요합니다.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-dhcp-server-relay.png" alt-text="Azure VMware Solutions에서 DHCP 서버 또는 DHCP 릴레이를 추가하는 방법을 보여 주는 스크린샷입니다.":::

4. [논리 세그먼트에 DHCP 범위를 제공](#create-an-nsx-t-segment-in-the-azure-portal)하여 DHCP 구성을 완료한 후 **확인** 을 선택합니다.
 
## <a name="configure-port-mirroring-in-the-azure-portal"></a>Azure Portal에서 포트 미러링 구성
한 네트워크 스위치 포트에서 다른 네트워크 스위치 포트로 각 패킷의 복사본을 전달하는 것과 관련된 네트워크 트래픽을 모니터링하도록 포트 미러링을 구성할 수 있습니다. 이 옵션은 미러된 데이터를 수신하는 포트에 프로토콜 분석기를 배치합니다. 원본, VM 또는 VM 그룹의 트래픽을 분석한 후 정의된 대상으로 보냅니다. 

Azure VMware Solution 콘솔에서 포트 미러링을 설정하려면 다음을 수행합니다.

* [1단계. 원본 및 대상 VM 또는 VM 그룹 만들기](#step-1-create-source-and-destination-vms-or-vm-groups) – 원본 그룹에는 트래픽이 미러되는 단일 VM 또는 여러 VM이 있습니다.

* [2단계. 포트 미러링 프로필 만들기](#step-2-create-a-port-mirroring-profile) – 원본 및 대상 VM 그룹에 대한 트래픽 방향을 정의합니다.

### <a name="step-1-create-source-and-destination-vms-or-vm-groups"></a>1단계. 원본 및 대상 VM 또는 VM 그룹 만들기

이 단계에서는 원본 VM 그룹과 대상 VM 그룹을 만듭니다.

1. Azure VMware Solution 프라이빗 클라우드의 **워크로드 네트워킹** 에서 **포트 미러링** > **VM 그룹** > **추가** 를 선택합니다.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-port-mirroring-vm-groups.png" alt-text="포트 미러링을 위한 VM 그룹을 만드는 방법을 보여 주는 스크린샷입니다.":::

1. 새 VM 그룹의 이름을 입력하고 목록에서 원하는 VM을 선택한 다음 **확인** 을 선택합니다.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-vm-group.png" alt-text="VM 그룹에 추가할 VM 목록을 보여 주는 스크린샷입니다.":::

1. 이러한 단계를 반복하여 대상 VM 그룹을 만듭니다.

### <a name="step-2-create-a-port-mirroring-profile"></a>2단계. 포트 미러링 프로필 만들기

이 단계에서는 원본 및 대상 VM 그룹의 트래픽 방향에 대한 프로필을 정의합니다.

>[!NOTE]
>원본 및 대상 VM 그룹이 모두 생성되었는지 확인합니다.

1. **포트 미러링** > **추가** 를 선택한 후 다음을 제공합니다.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-port-mirroring-profile.png" alt-text="포트 미러링 프로필에 필요한 정보를 보여 주는 스크린샷입니다.":::

   - **포트 미러링 이름** -프로필에 대한 설명이 포함된 이름입니다.
   - **방향** -수신, 송신 또는 양방향 중에서 선택합니다.
   - **원본** - 원본 VM 그룹을 선택합니다.
   - **대상** - 대상 VM 그룹을 선택합니다.
   - **설명** -포트 미러링에 대한 설명을 입력합니다.

1. **확인** 을 선택하여 프로필을 완료합니다. 

   프로필 및 VM 그룹이 Azure VMware Solution 콘솔에 표시됩니다.

## <a name="configure-a-dns-forwarder-in-the-azure-portal"></a>Azure Portal에서 DNS 전달자 구성
특정 DNS 요청이 확인을 위해 지정된 DNS 서버로 전달되는 DNS 전달자를 구성합니다.  DNS 전달자는 **기본 DNS 영역** 및 최대 3개의 **FQDN 영역** 에 연결됩니다.

>[!TIP]
>[ NSX-T Manager 콘솔을 사용하여 DNS 전달자를 구성](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.5/administration/GUID-A0172881-BB25-4992-A499-14F9BE3BE7F2.html)할 수도 있습니다.

Azure VMware Solution 콘솔에서 DNS 전달자를 설정하려면 다음을 수행합니다.

* [1단계. 기본 DNS 영역 및 FQDN 영역 구성](#step-1-configure-a-default-dns-zone-and-fqdn-zone) – DNS 쿼리를 받으면 DNS 전달자는 도메인 이름을 FQDN DNS 영역의 도메인 이름과 비교합니다. 

* [2단계. DNS 서비스 구성](#step-2-configure-dns-service) - DNS 전달자 서비스를 구성합니다.

### <a name="step-1-configure-a-default-dns-zone-and-fqdn-zone"></a>1단계. 기본 DNS 영역 및 FQDN 영역 구성
업스트림 서버에 DNS 쿼리를 보내도록 기본 DNS 영역 및 FQDN 영역을 구성합니다.  DNS 쿼리가 수신되면 DNS 전달자는 쿼리의 도메인 이름을 FQDN DNS 영역의 도메인 이름과 비교합니다. 일치하는 항목이 발견되면 FQDN DNS 영역에 지정된 DNS 서버로 쿼리가 전달됩니다. 일치하는 항목이 발견되지 않으면 기본 DNS 영역에 지정된 DNS 서버로 쿼리가 전달됩니다.

>[!NOTE]
>FQDN 영역을 구성하기 전에 기본 DNS 영역을 정의해야 합니다. 

1. Azure VMware Solution 프라이빗 클라우드의 **워크로드 네트워킹** 에서 **DNS** > **DNS 영역** > **추가** 를 선택합니다.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-dns-zones.png" alt-text="DNS 영역 및 DNS 서비스를 추가하는 방법을 보여 주는 스크린샷입니다.":::

1. **기본 DNS 영역** 을 선택하고 다음을 제공합니다.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-zones.png" alt-text="기본 DNS 영역을 추가하는 방법을 보여 주는 스크린샷입니다.":::

   1. DNS 영역의 이름입니다.

   1. 최대 3개의 **8.8.8.8** 형식의 DNS 서버 IP 주소입니다.

1. **FQDN 영역** 을 선택하고 다음을 제공합니다.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-fqdn-zone.png" alt-text="FQDN 영역을 추가하는 방법을 보여 주는 스크린샷입니다.":::

   1. DNS 영역의 이름입니다.

   1. FQDN 도메인입니다.

   1. 최대 3개의 **8.8.8.8** 형식의 DNS 서버 IP 주소입니다.

1. **확인** 을 선택하여 기본 DNS 영역 및 DNS 서비스 추가를 완료합니다.

### <a name="step-2-configure-dns-service"></a>2단계. DNS 서비스 구성

1. **DNS 서비스** 탭을 선택하고 **추가** 를 선택합니다. 세부 정보를 입력하고 **확인** 을 선택합니다.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-service.png" alt-text="DNS 서비스에 필요한 정보를 보여 주는 스크린샷입니다.":::

   >[!TIP]
   >**계층 1 게이트웨이** 는 기본적으로 선택되어 있으며 Azure VMware Solution을 배포할 때 생성된 게이트웨이를 반영합니다.

   DNS 서비스가 추가되었습니다.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-service-success.png" alt-text="DNS 서비스가 추가되었음을 보여 주는 스크린샷입니다.":::

