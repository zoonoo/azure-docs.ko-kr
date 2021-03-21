---
title: Azure VMware 솔루션에서 NSX 네트워크 구성 요소 구성
description: Azure VMware 솔루션 콘솔을 사용 하 여 NSX-T 네트워크 세그먼트를 구성 하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: 0478582a9bc4fb77a1784c27ec4f5c302d6b89fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101716991"
---
# <a name="configure-nsx-network-components-in-azure-vmware-solution"></a>Azure VMware 솔루션에서 NSX 네트워크 구성 요소 구성

Azure VMware 솔루션 사설 클라우드는 기본적으로 NSX-T를 소프트웨어 정의 네트워크 (SDDC)로 제공 합니다. 활성/활성 모드의 NSX 게이트웨이 및 활성/대기 모드의 기본 NSX-T 계층-1 게이트웨이로 미리 프로 비전 됩니다.  이러한 게이트웨이를 사용 하면 세그먼트 (논리 스위치)를 연결 하 고 East-West 및 North-South 연결을 제공할 수 있습니다. 

Azure VMware 솔루션 사설 클라우드를 배포한 후에는 **워크 로드 네트워킹** 의 Azure vmware solution console에서 필요한 NSX 개체를 구성할 수 있습니다.  이 콘솔은 VMware 관리자가 매일 필요로 하 고 NSX에 익숙하지 않은 사용자를 대상으로 하는 NSX 작업의 단순화 된 보기를 제공 합니다.  

Azure VMware 솔루션 콘솔에서 NSX 구성 요소를 구성 하는 4 가지 옵션이 있습니다.
- **세그먼트** -NSX Manager 및 vCenter에 표시 되는 세그먼트를 만듭니다.
- **Dhcp-dhcp** 를 사용 하려는 경우 dhcp 서버 또는 dhcp 릴레이를 만듭니다.
- **포트 미러링** – 포트 미러링을 만들어 네트워크 문제를 해결 하는 데 도움을 줍니다.
- **Dns** – 확인을 위해 dns 요청을 지정 된 dns 서버에 전송 하는 dns 전달자를 만듭니다.  

>[!NOTE]
>설명 된 고급 설정과 기타 NSX 기능을 사용할 수 있는 NSX Manager 콘솔에 대 한 액세스 권한이 계속 제공 됩니다. 
 
:::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking.png" alt-text="NSX 구성에 대 한 Azure VMware 솔루션 콘솔의 네 가지 옵션을 보여 주는 스크린샷":::

## <a name="prerequisites"></a>필수 구성 요소
Azure VMware 솔루션 사설 클라우드로 만들어지거나 마이그레이션된 Vm (가상 머신)은 세그먼트에 연결 되어야 합니다. 

## <a name="create-an-nsx-t-segment-in-the-azure-portal"></a>Azure Portal에서 NSX-T 세그먼트를 만듭니다.
Azure Portal의 Azure VMware Solution console에서 NSX-T 세그먼트를 만들고 구성할 수 있습니다.  이러한 세그먼트는 기본 계층 1 게이트웨이에 연결 되며 이러한 세그먼트에 대 한 워크 로드는 East-West 및 North-South 연결을 가져옵니다. 세그먼트를 만들면 NSX-T 관리자 및 vCenter에 표시 됩니다.

>[!NOTE]
>DHCP를 사용 하려는 경우 NSX-T 세그먼트를 만들고 구성 하기 전에 dhcp [서버 또는 dhcp 릴레이를 구성](#create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal) 해야 합니다.

1. Azure VMware 솔루션 사설 클라우드의 **워크 로드 네트워킹** 에서 **세그먼트**  >  **추가** 를 선택 합니다. 새 논리 세그먼트에 대 한 세부 정보를 제공 하 고 **확인을** 선택 합니다.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-new-nsxt-segment.png" alt-text="새 세그먼트를 추가 하는 방법을 보여 주는 스크린샷":::
   
   - **세그먼트 이름** -vCenter에 표시 되는 논리 스위치의 이름입니다.
   - 서브넷 **게이트웨이** -서브넷 마스크를 사용 하는 논리 스위치의 서브넷에 대 한 게이트웨이 IP 주소입니다. Vm은 논리 스위치에 연결 되 고이 스위치에 연결 하는 모든 Vm은 동일한 서브넷에 속합니다.  또한이 논리적 세그먼트에 연결 된 모든 Vm은 동일한 세그먼트의 IP 주소를 전달 해야 합니다.
   - **Dhcp** (옵션)-논리적 세그먼트의 dhcp 범위 Dhcp [서버 또는 dhcp 릴레이](#create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal) 는 세그먼트에서 dhcp를 사용 하도록 구성 되어야 합니다.  
   - **연결 된 게이트웨이**  -  *기본적으로 선택 되며 읽기 전용으로 선택 됩니다.*  계층 1 게이트웨이 및 세그먼트 정보 유형입니다. 
      - **T1** -NSX Manager에서 계층 1 게이트웨이의 이름입니다. Azure VMware 솔루션 사설 클라우드는 능동/능동 모드의 NSX 게이트웨이 및 활성/대기 모드의 기본 NSX-T 계층-1 게이트웨이로 제공 됩니다.  Azure VMware 솔루션 콘솔을 통해 만든 세그먼트는 기본 계층 1 게이트웨이에만 연결 되며 이러한 세그먼트의 워크 로드는 East-West 및 North-South 연결을 가져옵니다. NSX Manager를 통해 더 많은 계층 1 게이트웨이를 만들 수 있습니다. NSX-T 관리자 콘솔에서 만든 계층 1 게이트웨이는 Azure VMware 솔루션 콘솔에 표시 되지 않습니다. 
      - **유형** -오버레이 세그먼트가 Azure VMware 솔루션에서 지원 됩니다.

   이제 세그먼트가 Azure VMware Solution console, NSX-T 관리자 및 vCenter에 표시 됩니다.

## <a name="create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal"></a>Azure Portal에서 DHCP 서버 또는 DHCP 릴레이 만들기
Azure Portal에서 Azure VMware 솔루션 콘솔을 통해 직접 DHCP 서버 또는 릴레이를 만들 수 있습니다. DHCP 서버 또는 릴레이가 Azure VMware 솔루션을 배포할 때 생성 되는 계층 1 게이트웨이에 연결 합니다. DHCP 범위를 제공한 모든 세그먼트는이 DHCP의 일부가 됩니다.  DHCP 서버 또는 DHCP 릴레이를 만든 후에는이를 사용할 수 있도록 세그먼트 수준에서 서브넷 또는 범위를 정의 해야 합니다.

1. Azure VMware 솔루션 사설 클라우드의 **워크 로드 네트워킹** 에서 **DHCP**  >  **추가** 를 선택 합니다.

2. **Dhcp 서버** 또는 **dhcp 릴레이** 를 선택한 다음 서버 또는 릴레이의 이름과 3 개의 IP 주소를 제공 합니다. 

   >[!NOTE]
   >DHCP 릴레이의 경우 성공적인 구성에 하나의 IP 주소만 필요 합니다.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-dhcp-server-relay.png" alt-text="Azure VMware 솔루션에서 DHCP 서버 또는 DHCP 릴레이를 추가 하는 방법을 보여 주는 스크린샷":::

4. [논리 세그먼트에 dhcp 범위를 제공](#create-an-nsx-t-segment-in-the-azure-portal) 하 여 dhcp 구성을 완료 한 다음 **확인을** 선택 합니다.
 
## <a name="configure-port-mirroring-in-the-azure-portal"></a>Azure Portal에서 포트 미러링 구성
네트워크 스위치 포트 간에 각 패킷의 복사본을 전달 하는 작업을 포함 하는 네트워크 트래픽을 모니터링 하도록 포트 미러링을 구성할 수 있습니다. 이 옵션은 미러된 데이터를 수신 하는 포트에 프로토콜 분석기를 배치 합니다. 원본, VM 또는 Vm 그룹에서 트래픽을 분석 한 다음 정의 된 대상으로 보냅니다. 

Azure VMware 솔루션 콘솔에서 포트 미러링을 설정 하려면 다음을 수행 합니다.

* [1 단계. 원본 및 대상 Vm 또는 VM 그룹 만들기](#step-1-create-source-and-destination-vms-or-vm-groups) – 원본 그룹에는 트래픽을 미러링 하는 단일 vm 또는 여러 vm이 있습니다.

* [2 단계. 포트 미러링 프로필 만들기](#step-2-create-a-port-mirroring-profile) – 원본 및 대상 VM 그룹의 트래픽 방향을 정의 합니다.

### <a name="step-1-create-source-and-destination-vms-or-vm-groups"></a>1단계. 원본 및 대상 Vm 또는 VM 그룹 만들기

이 단계에서는 원본 VM 그룹과 대상 VM 그룹을 만듭니다.

1. Azure VMware 솔루션 사설 클라우드의 **워크 로드 네트워킹** 에서 **포트 미러링**  >  **VM 그룹**  >  **추가** 를 선택 합니다.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-port-mirroring-vm-groups.png" alt-text="포트 미러링을 위한 VM 그룹을 만드는 방법을 보여 주는 스크린샷":::

1. 새 VM 그룹의 이름을 입력 하 고 목록에서 원하는 Vm을 선택한 다음 **확인** 을 클릭 합니다.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-vm-group.png" alt-text="VM 그룹에 추가할 Vm 목록을 보여 주는 스크린샷":::

1. 이러한 단계를 반복 하 여 대상 VM 그룹을 만듭니다.

### <a name="step-2-create-a-port-mirroring-profile"></a>2단계. 포트 미러링 프로필 만들기

이 단계에서는 원본 및 대상 VM 그룹의 트래픽 방향에 대 한 프로필을 정의 합니다.

>[!NOTE]
>원본 및 대상 VM 그룹이 모두 만들어졌는지 확인 합니다.

1. **포트 미러링**  >  **추가** 를 선택 하 고 다음을 제공 합니다.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-port-mirroring-profile.png" alt-text="포트 미러링 프로필에 필요한 정보를 보여 주는 스크린샷":::

   - **포트 미러링 이름** -프로필에 대 한 설명이 포함 된 이름입니다.
   - **방향** -수신, 송신 또는 양방향에서 선택 합니다.
   - **원본** -원본 VM 그룹을 선택 합니다.
   - **대상** -대상 VM 그룹을 선택 합니다.
   - **설명** -포트 미러링에 대 한 설명을 입력 합니다.

1. **확인** 을 선택 하 여 프로필을 완료 합니다. 

   프로필 및 VM 그룹은 Azure VMware 솔루션 콘솔에 표시 됩니다.

## <a name="configure-a-dns-forwarder-in-the-azure-portal"></a>Azure Portal에서 DNS 전달자 구성
확인을 위해 특정 DNS 요청이 지정 된 DNS 서버에 전달 되는 DNS 전달자를 구성 합니다.  DNS 전달자는 **기본 dns 영역** 및 최대 3 개의 **FQDN 영역** 에 연결 됩니다.

>[!TIP]
>[NSX-T 관리자 콘솔을 사용 하 여 DNS 전달자를 구성할](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.5/administration/GUID-A0172881-BB25-4992-A499-14F9BE3BE7F2.html)수도 있습니다.

Azure VMware 솔루션 콘솔에서 DNS 전달자를 설정 하려면 다음을 수행 합니다.

* [1 단계. 기본 DNS 영역 및 FQDN 영역 구성](#step-1-configure-a-default-dns-zone-and-fqdn-zone) – dns 쿼리를 받으면 dns 전달자는 도메인 이름을 FQDN DNS 영역에 있는 도메인 이름과 비교 합니다. 

* [2 단계. DNS 서비스 구성](#step-2-configure-dns-service) -dns 전달자 서비스를 구성 합니다.

### <a name="step-1-configure-a-default-dns-zone-and-fqdn-zone"></a>1단계. 기본 DNS 영역 및 FQDN 영역 구성
업스트림 서버에 DNS 쿼리를 보내도록 기본 DNS 영역 및 FQDN 영역을 구성 합니다.  Dns 쿼리를 받으면 DNS 전달자는 쿼리의 도메인 이름을 FQDN DNS 영역의 도메인 이름과 비교 합니다. 일치 하는 항목이 발견 되 면 쿼리가 FQDN DNS 영역에 지정 된 DNS 서버로 전달 됩니다. 일치 항목을 찾을 수 없는 경우 쿼리는 기본 DNS 영역에 지정 된 DNS 서버로 전달 됩니다.

>[!NOTE]
>FQDN 영역을 구성 하기 전에 기본 DNS 영역을 정의 해야 합니다. 

1. Azure VMware 솔루션 사설 클라우드의 **워크 로드 네트워킹** 에서 **dns**  >  **dns 영역**  >  **추가** 를 선택 합니다.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-dns-zones.png" alt-text="DNS 영역 및 DNS 서비스를 추가 하는 방법을 보여 주는 스크린샷":::

1. **기본 DNS 영역** 을 선택 하 고 다음을 제공 합니다.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-zones.png" alt-text="기본 DNS 영역을 추가 하는 방법을 보여 주는 스크린샷":::

   1. DNS 영역의 이름입니다.

   1. **8.8.8.8** 형식으로 최대 3 개의 DNS 서버 IP 주소

1. **FQDN 영역** 을 선택 하 고 다음을 제공 합니다.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-fqdn-zone.png" alt-text="FQDN 영역을 추가 하는 방법을 보여 주는 스크린샷 ":::

   1. DNS 영역의 이름입니다.

   1. FQDN 도메인입니다.

   1. **8.8.8.8** 형식으로 최대 3 개의 DNS 서버 IP 주소

1. **확인** 을 선택 하 여 기본 dns 영역 및 dns 서비스 추가를 완료 합니다.

### <a name="step-2-configure-dns-service"></a>2단계. DNS 서비스 구성

1. **DNS 서비스** 탭을 선택 하 고 **추가** 를 선택 합니다. 세부 정보를 입력 하 고 **확인을** 선택 합니다.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-service.png" alt-text="DNS 서비스에 필요한 정보를 보여 주는 스크린샷":::

   >[!TIP]
   >**계층 1 게이트웨이** 는 기본적으로 선택 되며 Azure VMware 솔루션을 배포할 때 생성 된 게이트웨이를 반영 합니다.

   DNS 서비스를 추가 했습니다.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-service-success.png" alt-text="DNS 서비스가 추가 되었음을 보여 주는 스크린샷":::

