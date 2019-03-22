---
title: Azure Stack ExpressRoute를 사용 하 여 Azure에 연결
description: Azure Stack에서 가상 네트워크 ExpressRoute를 사용 하 여 Azure에서 가상 네트워크를 연결 하는 방법을 알아봅니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: 0ebd17eca363d7fc02daeb851bb24b8d1d307efc
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339604"
---
# <a name="connect-azure-stack-to-azure-using-azure-expressroute"></a>Azure Stack Azure ExpressRoute를 사용 하 여 Azure에 연결

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

이 문서는 Azure Stack virtual network를 사용 하 여 Azure virtual network에 연결 하는 방법에 설명 합니다는 [Microsoft Azure ExpressRoute](/azure/expressroute/) 직접 연결 합니다.

이 문서를 자습서로 사용 하 고 동일한 테스트 환경을 설정 하는 예제를 사용 하 여 수 있습니다. 또는 고유한 ExpressRoute 환경을 설정 하는 과정을 안내 하는 연습으로 문서를 사용할 수 있습니다.

## <a name="overview-assumptions-and-prerequisites"></a>개요, 가정 및 필수 구성 요소

Azure ExpressRoute를 사용 하면 연결 공급자가 제공 된 개인 연결을 통해 Microsoft 클라우드로 온-프레미스 네트워크를 확장할 수 있습니다. 공용 인터넷을 통해 ExpressRoute는 VPN 연결 되지 않습니다.

Azure ExpressRoute에 대 한 자세한 내용은 참조는 [ExpressRoute 개요](../expressroute/expressroute-introduction.md)합니다.

### <a name="assumptions"></a>가정

이 문서에 있다고 가정 합니다.

* Azure에 대 한 실무 지식이 해야합니다.
* Azure Stack에 대 한 기본 이해를 해야합니다.
* 네트워킹에 대 한 기본 이해를 해야합니다.

### <a name="prerequisites"></a>필수 조건

Azure Stack 및 ExpressRoute를 사용 하 여 Azure에 연결 하려면 다음 요구 사항을 충족 해야 합니다.

* 프로 비전 [ExpressRoute 회로](../expressroute/expressroute-circuit-peerings.md) 를 통해 한 [연결 공급자](../expressroute/expressroute-locations.md)합니다.
* Azure에서 Vnet 및 ExpressRoute 회로 만들려는 Azure 구독.
* 라우터:
  * 해당 LAN 인터페이스와 Azure Stack 테 넌 트 게이트웨이 간의 사이트 간 VPN 연결을 지원 합니다.
  * Azure Stack 배포에 둘 이상의 테 넌 트 있으면 여러 Vrf (가상 라우팅 및 전달을) 만들기를 지원 합니다.
* 있는 라우터:
  * WAN 포트를 ExpressRoute 회로에 연결 합니다.
  * LAN 포트는 Azure Stack 테 넌 트 게이트웨이를 연결 합니다.

### <a name="expressroute-network-architecture"></a>ExpressRoute 네트워크 아키텍처

다음 다이어그램에서는 Azure Stack 및 Azure 환경에서 ExpressRoute 설정을 마친 후이 문서의 예제를 사용 하 여:

*그림 1. ExpressRoute network*

![ExpressRoute 네트워크](media/azure-stack-connect-expressroute/Conceptual.png)

다음 다이어그램은 여러 테 넌 트에 연결할 ExpressRoute 라우터를 통해 Azure Stack 인프라에서 Azure Microsoft edge에서 보여줍니다.

*그림 2. 다중 테 넌 트 연결*

![ExpressRoute 사용 하 여 다중 테 넌 트 연결](media/azure-stack-connect-expressroute/Architecture.png)

이 문서의 예제에서는 Azure Stack ExpressRoute 개인 피어 링을 사용 하 여 Azure에 연결할 그림 2에에서 표시 된 동일한 다중 테 넌 트 아키텍처를 사용 합니다. 연결이 완료 되는 ExpressRoute 라우터를 Azure Stack에서 가상 네트워크 게이트웨이에서 사이트 간 VPN 연결을 사용 하 여 합니다.

이 문서의 단계는 Azure에서 해당 Vnet에 Azure Stack에서 두 명의 다른 테 넌 트에서 두 개의 Vnet 간에 종단 간 연결을 만드는 방법을 보여 줍니다. 두 테 넌 트를 설정 하는 것은 선택 사항입니다. 또한 단일 테 넌 트에 대 한이 단계를 사용할 수 있습니다.

## <a name="configure-azure-stack"></a>Azure Stack 구성

첫 번째 테 넌 트에 대 한 Azure Stack 환경을 설정 하는 가이드로 다음 다이어그램의 단계를 사용 합니다. 둘 이상의 테 넌 트를 설정 하는 경우 다음이 단계를 반복 합니다.

>[!NOTE]
>다음이 단계 Azure Stack 포털을 사용 하 여 리소스를 만드는 방법을 보여주지만 PowerShell을 사용할 수도 있습니다.

![Azure Stack 네트워크 설정](media/azure-stack-connect-expressroute/image2.png)

### <a name="before-you-begin"></a>시작하기 전에

Azure Stack 구성 시작 하기 전에 다음을 수행 해야 합니다.

* Azure Stack 통합 시스템 배포를 또는 Azure Stack 개발 키트 (ASDK) 배포 합니다. ASDK 배포에 대 한 자세한 내용은 참조는 [Azure Stack 개발 키트 배포 빠른 시작](azure-stack-deploy-overview.md)합니다.
* 사용자가 구독할 수 있는 Azure Stack에서 제품입니다. 자세한 내용은 [계획, 제품 및 구독](azure-stack-plan-offer-quota-overview.md)합니다.

### <a name="create-network-resources-in-azure-stack"></a>Azure Stack에서 네트워크 리소스 만들기

테 넌 트에 대 한 Azure Stack에서 필요한 네트워크 리소스를 만들려면 다음 절차를 사용 합니다.

#### <a name="create-the-virtual-network-and-vm-subnet"></a>가상 네트워크 및 VM 서브넷 만들기

1. 사용자 (테 넌 트) 계정 사용 하 여 사용자 포털에 로그인 합니다.

2. 포털에서 선택 **+ 리소스 만들기**합니다.

3. 아래 **Azure Marketplace**를 선택 **네트워킹**합니다.

4. 아래 **추천**를 선택 **Virtual network**합니다.

5. 아래 **가상 네트워크 만들기**, 해당 필드에는 다음 표에 표시 된 값을 입력 합니다.

   |필드  |값  |
   |---------|---------|
   |이름     |Tenant1VNet1         |
   |주소 공간     |10.1.0.0/16|
   |서브넷 이름     |Sub1 Tenant1|
   |서브넷 주소 범위     |10.1.1.0/24|

6. 이전에 채워진 만든 구독이 표시 되어야 합니다 **구독** 필드입니다. 나머지 필드:

    * 아래 **리소스 그룹**를 선택 **새로 만들기** 새 리소스 그룹을 만들거나 이미 있는 경우 하나를 선택 하려면 **기존 항목 사용**합니다.
    * 기본 확인 **위치**합니다.
    * **만들기**를 클릭합니다.
    * (선택 사항) 클릭 **대시보드에 고정**합니다.

#### <a name="create-the-gateway-subnet"></a>게이트웨이 서브넷 만들기

1. 아래 **가상 네트워크**를 선택 **Tenant1VNet1**합니다.
1. **설정**에서 **서브넷**을 선택합니다.
1. 선택 **+ 게이트웨이 서브넷** 가상 네트워크에 게이트웨이 서브넷을 추가 합니다.
1. 서브넷의 이름은 **GatewaySubnet**이라고 기본으로 설정됩니다. 게이트웨이 서브넷은 특별 한 경우 및 제대로 작동 하려면이 이름을 사용 해야 합니다.
1. 있는지 확인 합니다 **주소 범위** 됩니다 **10.1.0.0/24**합니다.
1. **확인**을 클릭하여 게이트웨이 서브넷을 만듭니다.

#### <a name="create-the-virtual-network-gateway"></a>가상 네트워크 게이트웨이 만들기

1. Azure Stack 사용자 포털에서 클릭 **+ 리소스 만들기**합니다.
1. 아래 **Azure Marketplace**를 선택 **네트워킹**합니다.
1. 네트워크 리소스 목록에서 **가상 네트워크 게이트웨이**를 선택합니다.
1. 에 **이름을** 필드를 입력 합니다 **GW1**합니다.
1. **가상 네트워크**를 선택합니다.
1. 선택 **Tenant1VNet1** 드롭 다운 목록에서.
1. 선택 **공용 IP 주소**, 한 다음 **공용 IP 주소 선택**를 클릭 하 고 **새로 만들기**합니다.
1. 에 **이름을** 필드에 입력 **GW1-PiP**를 클릭 하 고 **확인**합니다.
1. **VPN 종류**에 **Route-based**(경로 기반)이 기본으로 선택되어 있습니다. 이 설정을 유지합니다.
1. **구독** 및 **위치**가 올바른지 확인합니다. **만들기**를 클릭합니다.

#### <a name="create-the-local-network-gateway"></a>로컬 네트워크 게이트웨이 만들기

로컬 네트워크 게이트웨이 리소스에 VPN 연결의 반대쪽 끝에 있는 원격 게이트웨이 식별합니다. 예를 들어 연결의 원격 끝에는 ExpressRoute 라우터의 LAN 하위 인터페이스입니다. 테 넌 트 1, 2, 그림에 표시 된 원격 주소 10.60.3.255는입니다.

1. 선택한 사용자 계정으로 Azure Stack 사용자 포털에 로그인 **+ 리소스 만들기**합니다.
1. 아래 **Azure Marketplace**를 선택 **네트워킹**합니다.
1. 리소스 목록에서 **로컬 네트워크 게이트웨이**를 선택합니다.
1. 에 **이름을** 필드에 입력 **ER-라우터-GW**합니다.
1. 에 대 한 합니다 **IP 주소** 필드, 그림 2를 참조 하세요. 테 넌 트 1에 대 한 ExpressRoute 라우터 LAN 하위 인터페이스의 IP 주소가 10.60.3.255 없습니다. 사용자 고유의 환경에 대 한 라우터의 해당 인터페이스의 IP 주소를 입력 합니다.
1. 에 **주소 공간** 필드를 Azure에 연결 하려는 Vnet의 주소 공간을 입력 합니다. 테 넌 트 1에 대 한 서브넷 *그림 2* 는 다음과 같습니다.

   * 192.168.2.0/24는 Azure에서 VNet 허브입니다.
   * 10.100.0.0/16 스포크에서 Azure VNet입니다.

   > [!IMPORTANT]
   > 이 예제에서는 Azure Stack 게이트웨이 및 ExpressRoute 라우터 간의 사이트 간 VPN 연결에 대 한 정적 경로 사용 하 고 있는지를 가정 합니다.

1. 확인 프로그램 **구독**를 **리소스 그룹**, 및 **위치** 올바른지 합니다. 그런 다음 **만들기**를 선택합니다.

#### <a name="create-the-connection"></a>연결 만들기

1. Azure Stack 사용자 포털에서 선택 **+ 리소스 만들기**합니다.
1. 아래 **Azure Marketplace**를 선택 **네트워킹**합니다.
1. 리소스 목록에서 **연결**을 선택합니다.
1. 아래 **기본 사항을**, 선택 **사이트-사이트 간 (IPSec)** 으로 **연결 형식**합니다.
1. 선택 된 **구독**를 **리소스 그룹**, 및 **위치**합니다. **확인**을 클릭합니다.
1. 아래 **설정을**를 선택 **가상 네트워크 게이트웨이**를 선택한 후 **GW1**합니다.
1. 선택 **로컬 네트워크 게이트웨이**를 선택한 후 **ER 라우터 GW**합니다.
1. 에 **연결 이름** 필드를 입력 합니다 **ConnectToAzure**합니다.
1. 에 **공유 키 (PSK)** 필드를 입력 합니다 **abc123** 선택한 후 **확인**합니다.
1. 아래 **요약**를 선택 **확인**합니다.

#### <a name="get-the-virtual-network-gateway-public-ip-address"></a>가상 네트워크 게이트웨이 공용 IP 주소 가져오기

가상 네트워크 게이트웨이 만든 후에 게이트웨이의 공용 IP 주소를 가져올 수 있습니다. 배포에 대 한 나중에 필요한 경우에이 주소의 참고를 확인 합니다. 배포에 따라이 주소는으로 사용 합니다 **내부 IP 주소**합니다.

1. Azure Stack 사용자 포털에서 선택 **모든 리소스**합니다.
1. 아래 **모든 리소스**에 가상 네트워크 게이트웨이 선택 **GW1** 예제에서.
1. 아래 **가상 네트워크 게이트웨이**를 선택 **개요** 리소스 목록에서. 선택할 수 있습니다 **속성**합니다.
1. 확인 하려는 IP 주소 아래에 나열 됩니다 **공용 IP 주소**합니다. 예제 구성에서는이 주소는 192.68.102.1입니다.

#### <a name="create-a-virtual-machine"></a>가상 머신 만들기

VPN 연결을 통한 데이터 트래픽이 테스트 가상 컴퓨터는 Azure Stack VNet의 데이터를 받고 보내는 데 필요 합니다. 가상 머신을 만들고 가상 네트워크에 VM 서브넷에 배포 합니다.

1. Azure Stack 사용자 포털에서 선택 **+ 리소스 만들기**합니다.
1. 아래 **Azure Marketplace**를 선택 **계산**합니다.
1. 가상 머신 이미지 목록에서 선택 합니다 **Windows Server 2016 Datacenter 평가판** 이미지입니다.

   >[!NOTE]
   >이 문서에 사용 되는 이미지를 사용할 수 없는 경우 다른 Windows Server 이미지를 제공 하 여 Azure Stack 운영자를 요청 합니다.

1. **가상 머신 만들기**를 선택 **기본 사항**를 입력 한 다음 **VM01** 으로 **이름**합니다.
1. 유효한 사용자 이름 및 암호를 입력 합니다. 생성 된 후 VM에 로그인 하려면이 계정을 사용 합니다.
1. 제공 된 **구독**, **리소스 그룹**, 및 **위치**합니다. **확인**을 선택합니다.
1. 아래 **크기 선택**,이 인스턴스에 대 한 가상 머신 크기를 선택한 후 **선택**합니다.
1. 아래 **설정을**, 있는지 확인 합니다.

   * 가상 네트워크가 **Tenant1VNet1**합니다.
   * 로 설정 된 서브넷 **10.1.1.0/24**합니다.

   기본 설정을 사용 하 고 클릭 **확인**합니다.

1. 아래 **요약**VM 구성을 검토 하 고 클릭 **확인**합니다.

자세한 테 넌 트를 추가 하려면 다음이 섹션에서 수행한 단계를 반복 합니다.

* [가상 네트워크 및 VM 서브넷 만들기](#create-the-virtual-network-and-vm-subnet)
* [게이트웨이 서브넷 만들기](#create-the-gateway-subnet)
* [가상 네트워크 게이트웨이 만들기](#create-the-virtual-network-gateway)
* [로컬 네트워크 게이트웨이 만들기](#create-the-local-network-gateway)
* [연결 만들기](#create-the-connection)
* [가상 머신 만들기](#create-a-virtual-machine)

테 넌 트 2를 예로 사용 하는 경우 겹침 방지 하려면 IP 주소를 변경 해야 합니다.

### <a name="configure-the-nat-virtual-machine-for-gateway-traversal"></a>게이트웨이 통과 위해 NAT 가상 머신 구성

> [!IMPORTANT]
> 이 섹션은 Azure Stack 개발 키트 (ASDK) 배포에만 해당 합니다. 다중 노드 배포에 대 한 NAT 필요 하지 않습니다.

Azure Stack 개발 키트 독립적 이며 실제 호스트를 배포할 네트워크에서 격리 합니다. VIP 네트워크에 게이트웨이가 연결 된 외부; 아닙니다. 네트워크 주소 변환 (NAT)를 수행 하는 라우터 뒤에 숨겨져 있습니다.

라우터는 Windows Server 가상 컴퓨터를 (AzS-BGPNAT01) 라우팅 및 원격 액세스 서비스 (RRAS) 역할을 실행 합니다. 양쪽 끝에 연결 하려면 사이트 간 VPN 연결을 사용 하려면 AzS-BGPNAT01 가상 머신에서 NAT를 구성 해야 합니다.

#### <a name="configure-the-nat"></a>NAT 구성

1. 관리자 계정으로 Azure Stack 호스트 컴퓨터에 로그인 합니다.
1. 복사 하 고 다음 PowerShell 스크립트를 편집 합니다. 대체 `your administrator password` 관리자 암호와 다음을 관리자 권한으로 PowerShell ISE에서 스크립트를 실행 합니다. 이 스크립트를 반환 하면 **외부 BGPNAT 주소**합니다.

   ```PowerShell
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "your administrator password" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "azs-bgpnat01" `
    -Password $Password
   ```

1. NAT를 구성 하려면 복사한 다음 PowerShell 스크립트를 편집 합니다. 대체 하는 스크립트를 편집 합니다 `External BGPNAT address` 및 `Internal IP address` 와 다음 예제 값:

   * 에 대 한 *외부 BGPNAT 주소* 10.10.0.62 사용
   * 에 대 한 *내부 IP 주소* 192.168.102.1 사용

   관리자 권한으로 PowerShell ISE에서 다음 스크립트를 실행 합니다.

   ```PowerShell
   $ExtBgpNat = 'External BGPNAT address'
   $IntBgpNat = 'Internal IP address'

   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 4499 `
      -PortEnd 4501}
   # Create a static NAT mapping to map the external address to the Gateway public IP address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 500 `
      -InternalPort 500}
   # Configure NAT traversal which uses port 4500 to  establish the complete IPSEC tunnel over NAT devices.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

## <a name="configure-azure"></a>Azure 구성

Azure Stack 구성를 마친 후에 Azure 리소스를 배포할 수 있습니다. 다음 그림에서 Azure 테 넌 트 가상 네트워크의 예를 보여 줍니다. Azure에서 VNet에 대 한 다른 이름 및 주소 지정 체계를 사용할 수 있습니다. 그러나 Azure 및 Azure Stack에서 Vnet의 주소 범위는 고유 해야 하며 겹치지 않아야 합니다.

*그림 3. Azure Vnet*

![Azure Vnet](media/azure-stack-connect-expressroute/AzureArchitecture.png)

Azure에서 배포한 리소스는 Azure Stack에 배포 된 리소스와 비슷합니다. 다음 구성 요소를 배포할 수 있습니다.

* 가상 네트워크 및 서브넷
* 게이트웨이 서브넷
* 가상 네트워크 게이트웨이
* 연결
* ExpressRoute 회로

Azure 네트워크 인프라 예제는 다음과 같이 구성 됩니다.

* (192.168.2.0/24) 표준 허브 및 스포크 (10.100.0.0./16) VNet 모델입니다. 허브-스포크 네트워크 토폴로지에 대 한 자세한 내용은 참조 하세요. [Azure에서 허브-스포크 네트워크 토폴로지 구현](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)합니다.
* 스포크 VNet에에서 배포 된 워크 로드 및 ExpressRoute 회로 허브 VNet에 연결 되어 있습니다.
* 두 Vnet은 VNet 피어 링을 사용 하 여 연결 됩니다.

### <a name="configure-the-azure-vnets"></a>Azure Vnet 구성

1. Azure 자격 증명을 사용 하 여 Azure portal에 로그인 합니다.
1. 허브 192.168.2.0/24 주소 범위를 사용 하 여 VNet을 만듭니다.
1. 192.168.2.0/25 주소 범위를 사용 하 여 서브넷을 만들고 192.168.2.128/27 주소 범위를 사용 하 여 게이트웨이 서브넷을 추가 합니다.
1. 만들 스포크 VNet 및 서브넷을 10.100.0.0/16를 사용 하 여 주소 범위입니다.

Azure에서 가상 네트워크를 만드는 방법에 대 한 자세한 내용은 참조 하세요. [가상 네트워크 만들기](../virtual-network/manage-virtual-network.md#create-a-virtual-network)합니다.

### <a name="configure-an-expressroute-circuit"></a>ExpressRoute 회로 구성 합니다.

1. ExpressRoute 필수 구성 요소를 검토 [ExpressRoute 필수 구성 요소 및 검사 목록](../expressroute/expressroute-prerequisites.md)합니다.

1. 단계를 따릅니다 [만들기 및 ExpressRoute 회로 수정](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) Azure 구독을 사용 하 여 ExpressRoute 회로 만드는 합니다.

   >[!NOTE]
   >가 ExpressRoute 회로를 설정할 수 있도록 서비스에 회로 서비스 키를 제공 합니다.

1. 단계에 따라 [만들기 ExpressRoute 회로의 피어 링 및 수정](../expressroute/expressroute-howto-routing-portal-resource-manager.md) ExpressRoute 회로에서 개인 피어 링을 구성 합니다.

### <a name="create-the-virtual-network-gateway"></a>가상 네트워크 게이트웨이 만들기

단계를 따릅니다 [PowerShell을 사용 하 여 ExpressRoute 용 가상 네트워크 게이트웨이 구성](../expressroute/expressroute-howto-add-gateway-resource-manager.md) 허브 VNet에서에서 ExpressRoute에 대 한 가상 네트워크 게이트웨이 만들려면.

### <a name="create-the-connection"></a>연결 만들기

허브 VNet ExpressRoute 회로 연결 하려면의 단계를 따릅니다 [가상 네트워크를 ExpressRoute 회로에 연결할](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)합니다.

### <a name="peer-the-vnets"></a>VNet 피어링

허브를 피어 링 및 Vnet의 단계를 사용 하 여 스포크 [Azure portal을 사용 하 여 가상 네트워크 피어 링 만들기](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md)합니다. VNet 피어 링을 구성할 때 다음 옵션을 사용할 수 있는지 확인 합니다.

* 스포크를 허브에서 **게이트웨이 전송 허용**합니다.
* 허브에 스포크 **원격 게이트웨이 사용**합니다.

### <a name="create-a-virtual-machine"></a>가상 머신 만들기

스포크 VNet으로 워크 로드 virtual machines를 배포 합니다.

추가 테 넌 트의 각 ExpressRoute 회로 통해 Azure에서 연결 하려는 Vnet이이 단계를 반복 합니다.

## <a name="configure-the-router"></a>라우터 구성

ExpressRoute 라우터를 구성 하기 위한 지침으로 다음 ExpressRoute 라우터 구성 다이어그램을 사용할 수 있습니다. 이 다이어그램에는 두 테 넌 트 (테 넌 트 1 및 테 넌 트 2)가 각 ExpressRoute 회로 사용 하 여 보여 줍니다. 각 테 넌 트 ExpressRoute 라우터의 LAN 및 WAN 쪽에서 자신의 VRF (가상 라우팅 및 전달을)에 연결 됩니다. 이 구성은 두 명의 테 넌 트 간에 종단 간 격리를 보장 합니다. 구성 예제에 따라 라우터 인터페이스에서 사용 된 IP 주소를 기록해 둡니다.

*그림 4. ExpressRoute 라우터 구성*

![ExpressRoute 라우터 구성](media/azure-stack-connect-expressroute/EndToEnd.png)

Azure Stack에서 사이트 간 VPN 연결을 종료 하는 IKEv2 VPN 및 BGP를 지 원하는 모든 라우터를 사용할 수 있습니다. ExpressRoute 회로 사용 하 여 Azure에 연결 하는 동일한 라우터 사용 됩니다.

다음 Cisco ASR 1000 시리즈 집계 서비스 라우터 구성 예제에 표시 하는 네트워크 인프라를 지원 합니다 *ExpressRoute 라우터 구성* 다이어그램.

```shell
ip vrf Tenant 1
 description Routing Domain for PRIVATE peering to Azure for Tenant 1
 rd 1:1
!
ip vrf Tenant 2
 description Routing Domain for PRIVATE peering to Azure for Tenant 2
 rd 1:5
!
crypto ikev2 proposal V2-PROPOSAL2
description IKEv2 proposal for Tenant 1
encryption aes-cbc-256
 integrity sha256
 group 2
crypto ikev2 proposal V4-PROPOSAL2
description IKEv2 proposal for Tenant 2
encryption aes-cbc-256
 integrity sha256
 group 2
!
crypto ikev2 policy V2-POLICY2
description IKEv2 Policy for Tenant 1
match fvrf Tenant 1
 match address local 10.60.3.255
 proposal V2-PROPOSAL2
description IKEv2 Policy for Tenant 2
crypto ikev2 policy V4-POLICY2
 match fvrf Tenant 2
 match address local 10.60.3.251
 proposal V4-PROPOSAL2
!
crypto ikev2 profile V2-PROFILE
description IKEv2 profile for Tenant 1
match fvrf Tenant 1
 match address local 10.60.3.255
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 1
!
crypto ikev2 profile V4-PROFILE
description IKEv2 profile for Tenant 2
 match fvrf Tenant 2
 match address local 10.60.3.251
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 2
!
crypto ipsec transform-set V2-TRANSFORM2 esp-gcm 256
 mode tunnel
crypto ipsec transform-set V4-TRANSFORM2 esp-gcm 256
 mode tunnel
!
crypto ipsec profile V2-PROFILE
 set transform-set V2-TRANSFORM2
 set ikev2-profile V2-PROFILE
!
crypto ipsec profile V4-PROFILE
 set transform-set V4-TRANSFORM2
 set ikev2-profile V4-PROFILE
!
interface Tunnel10
description S2S VPN Tunnel for Tenant 1
 ip vrf forwarding Tenant 1
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.211
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf Tenant 1
 tunnel protection ipsec profile V2-PROFILE
!
interface Tunnel20
description S2S VPN Tunnel for Tenant 2
 ip vrf forwarding Tenant 2
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.213
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf VNET3
 tunnel protection ipsec profile V4-PROFILE
!
interface GigabitEthernet0/0/1
 description PRIMARY ExpressRoute Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/1.100
description Primary WAN interface of Tenant 1
 description PRIMARY ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.1 255.255.255.252
!
interface GigabitEthernet0/0/1.102
description Primary WAN interface of Tenant 2
 description PRIMARY ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.17 255.255.255.252
!
interface GigabitEthernet0/0/2
 description BACKUP ExpressRoute Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/2.100
description Secondary WAN interface of Tenant 1
 description BACKUP ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.5 255.255.255.252
!
interface GigabitEthernet0/0/2.102
description Secondary WAN interface of Tenant 2
description BACKUP ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.21 255.255.255.252
!
interface TenGigabitEthernet0/1/0
 description Downlink to ---Port 1/47
 no ip address
!
interface TenGigabitEthernet0/1/0.211
 description LAN interface of Tenant 1
description Downlink to --- Port 1/47.211
 encapsulation dot1Q 211
 ip vrf forwarding Tenant 1
 ip address 10.60.3.255 255.255.255.254
!
interface TenGigabitEthernet0/1/0.213
description LAN interface of Tenant 2
 description Downlink to --- Port 1/47.213
 encapsulation dot1Q 213
 ip vrf forwarding Tenant 2
 ip address 10.60.3.251 255.255.255.254
!
router bgp 65530
 bgp router-id <removed>
 bgp log-neighbor-changes
 description BGP neighbor config and route advertisement for Tenant 1 VRF
 address-family ipv4 vrf Tenant 1
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.254 mask 255.255.255.254
  network 192.168.1.0 mask 255.255.255.252
  network 192.168.1.4 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65100
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 1
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.254 remote-as 4232570301
  neighbor 10.60.3.254 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.254 activate
  neighbor 10.60.3.254 route-map BLOCK-ALL out
  neighbor 192.168.1.2 remote-as 12076
  neighbor 192.168.1.2 description PRIMARY ER peer for Tenant 1 to Azure
  neighbor 192.168.1.2 ebgp-multihop 5
  neighbor 192.168.1.2 activate
  neighbor 192.168.1.2 soft-reconfiguration inbound
  neighbor 192.168.1.2 route-map Tenant 1-ONLY out
  neighbor 192.168.1.6 remote-as 12076
  neighbor 192.168.1.6 description BACKUP ER peer for Tenant 1 to Azure
  neighbor 192.168.1.6 ebgp-multihop 5
  neighbor 192.168.1.6 activate
  neighbor 192.168.1.6 soft-reconfiguration inbound
  neighbor 192.168.1.6 route-map Tenant 1-ONLY out
  maximum-paths 8
 exit-address-family
 !
description BGP neighbor config and route advertisement for Tenant 2 VRF
address-family ipv4 vrf Tenant 2
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.250 mask 255.255.255.254
  network 192.168.1.16 mask 255.255.255.252
  network 192.168.1.20 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65300
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 2
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.250 remote-as 4232570301
  neighbor 10.60.3.250 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.250 activate
  neighbor 10.60.3.250 route-map BLOCK-ALL out
  neighbor 192.168.1.18 remote-as 12076
  neighbor 192.168.1.18 description PRIMARY ER peer for Tenant 2 to Azure
  neighbor 192.168.1.18 ebgp-multihop 5
  neighbor 192.168.1.18 activate
  neighbor 192.168.1.18 soft-reconfiguration inbound
  neighbor 192.168.1.18 route-map VNET-ONLY out
  neighbor 192.168.1.22 remote-as 12076
  neighbor 192.168.1.22 description BACKUP ER peer for Tenant 2 to Azure
  neighbor 192.168.1.22 ebgp-multihop 5
  neighbor 192.168.1.22 activate
  neighbor 192.168.1.22 soft-reconfiguration inbound
  neighbor 192.168.1.22 route-map VNET-ONLY out
  maximum-paths 8
 exit-address-family
!
ip forward-protocol nd
!
ip as-path access-list 1 permit ^$
ip route vrf Tenant 1 10.1.0.0 255.255.0.0 Tunnel10
ip route vrf Tenant 2 10.1.0.0 255.255.0.0 Tunnel20
!
ip prefix-list BLOCK-ALL seq 5 deny 0.0.0.0/0 le 32
!
route-map BLOCK-ALL permit 10
 match ip address prefix-list BLOCK-ALL
!
route-map VNET-ONLY permit 10
 match as-path 1
!
```

## <a name="test-the-connection"></a>연결 테스트

사이트 간 연결 및 ExpressRoute 회로 설정한 후 연결을 테스트 합니다.

다음 ping 테스트를 수행 합니다.

* Azure VNet의 가상 컴퓨터 중 하나에 로그인 하 고 Azure Stack에서 만든 가상 컴퓨터를 ping 합니다.
* Azure Stack 및 Azure VNet에서 만든 가상 머신을 ping에서 만든 가상 컴퓨터 중 하나에 로그인 합니다.

>[!NOTE]
>사이트 간 및 ExpressRoute 연결을 통해 트래픽을 전송 하는 있는지, 모두 끝에서 가상 컴퓨터의 전용된 IP (DIP) 주소와 가상 컴퓨터의 VIP 주소가 아닌를 ping 해야 있습니다.

### <a name="allow-icmp-in-through-the-firewall"></a>방화벽을 통해에서 ICMP를 허용 합니다.

기본적으로 Windows Server 2016에는 방화벽을 통해 들어오는 ICMP 패킷을 허용 하지 않습니다. Ping 테스트에 사용 하는 모든 가상 컴퓨터에 대 한 들어오는 ICMP 패킷을 허용 해야 합니다. ICMP에 대 한 방화벽 규칙을 만들려면 관리자 권한 PowerShell 창에서 다음 cmdlet을 실행 합니다.

```PowerShell
# Create ICMP firewall rule.
New-NetFirewallRule `
  –DisplayName “Allow ICMPv4-In” `
  –Protocol ICMPv4
```

### <a name="ping-the-azure-stack-virtual-machine"></a>Azure Stack 가상 컴퓨터를 ping 합니다.

1. 테 넌 트 계정을 사용 하 여 Azure Stack 사용자 포털에 로그인 합니다.

1. 사용자가 만든 가상 컴퓨터를 찾아 가상 컴퓨터를 선택 합니다.

1. **연결**을 선택합니다.

1. 관리자 권한 PowerShell 또는 Windows 명령 프롬프트에서 입력 **ipconfig /all**합니다. 참고 IPv4 주소는 출력에 반환 합니다.

1. Azure VNet의 가상 컴퓨터에서 IPv4 주소를 ping 합니다.

   예제 환경에서는 IPv4 주소 10.1.1.x/24 서브넷 에서입니다. 사용자 환경에서 주소가 다를 수 있지만 테 넌 트 VNet 서브넷에 대해 만든 서브넷에 있어야 합니다.

### <a name="view-data-transfer-statistics"></a>데이터 전송 통계 보기

연결을 통해 전달 트래픽 양을 알아야 원한다 면 Azure Stack 사용자 포털에서이 정보를 찾을 수 있습니다. VPN 및 ExpressRoute 연결을 통해 ping 테스트 데이터 발생 여부 확인 하는 좋은 방법 이기도 합니다.

1. 테 넌 트 계정을 사용 하 여 Azure Stack 사용자 포털에 로그인 하 고 선택 **모든 리소스**합니다.
1. VPN Gateway에 대 한 리소스 그룹으로 이동 하 고 선택 합니다 **연결** 개체 유형입니다.
1. 선택 된 **ConnectToAzure** 목록에서 연결 합니다.
1. 아래 **연결** > **개요**에 대 한 통계를 볼 수 있습니다 **데이터를** 및 **데이터**입니다. 일부 0이 아닌 값이 표시 됩니다.

   ![데이터 및 데이터 출력](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>다음 단계

[Azure 및 Azure에 앱 배포 스택](azure-stack-solution-pipeline.md)
