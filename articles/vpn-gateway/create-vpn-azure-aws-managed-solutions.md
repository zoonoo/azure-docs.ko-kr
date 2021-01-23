---
title: 관리 솔루션을 사용 하 여 Azure와 AWS 간의 VPN 만들기
description: Vm 또는 어플라이언스 대신 관리 솔루션을 사용 하 여 Azure와 AWS 간에 VPN 연결을 만드는 방법입니다.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: ricmmartins
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 01/22/2021
ms.author: ricmart
ms.openlocfilehash: a0655ce1d2e9939981bb4fd3280af80e359ea1e1
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737747"
---
# <a name="create-a-vpn-connection-between-azure-and-aws-using-managed-solutions"></a>관리 솔루션을 사용 하 여 Azure와 AWS 간 VPN 연결 만들기

관리 솔루션을 사용 하 여 Azure와 AWS 간에 연결을 설정할 수 있습니다. 이전에는 응답자 역할을 하는 어플라이언스 또는 VM을 사용 해야 했습니다. 이제 가상 머신과 같은 IaaS 리소스를 관리 하는 것에 대해 걱정할 필요 없이 AWS 가상 사설망을 Azure VPN Gateway에 직접 연결할 수 있습니다. 이 문서는 관리 되는 솔루션을 사용 하 여 Azure와 AWS 간에 VPN 연결을 만드는 데 도움이 됩니다.

:::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/diagram.png" alt-text="아키텍처 다이어그램":::

## <a name="configure-azure"></a>Azure 구성

### <a name="configure-a-virtual-network"></a>가상 네트워크 구성

가상 네트워크를 구성 합니다. 자세한 내용은 [Virtual Network 빠른](../virtual-network/quick-create-portal.md)시작을 참조 하세요.

이 문서에서 사용 되는 예제 값은 다음과 같습니다.

* **리소스 그룹:** rg-azure-aws
* **지역:** 미국 동부
* **가상 네트워크 이름:** vnet-azure
* **IPv4 주소 공간:** 172.10.0.0/16
* **서브넷 이름:** 서브넷-01
* **서브넷 주소 범위:** 172.10.1.0/24

### <a name="create-a-vpn-gateway"></a>VPN 게이트웨이 만들기

가상 네트워크에 대 한 VPN gateway를 만듭니다. 지침은 [자습서: VPN Gateway 만들기 및 관리](tutorial-create-gateway-portal.md)를 참조 하세요.

이 문서에서 사용 되는 예제 값 및 설정은 다음과 같습니다.

* **게이트웨이 이름:** vpn-azure-aws
* **지역:** 미국 동부
* **게이트웨이 유형:** VPN
* **VPN 유형:** 경로 기반
* **SKU:** VpnGw1
* **생성:** 1 세대
* **가상 네트워크:** 게이트웨이를 만들려는 VNet 이어야 합니다.
* **게이트웨이 서브넷 주소 범위:** 172.10.0.0/27
* **공용 IP 주소:** 새로 만들기
* **공용 IP 주소 이름:** pip-vpn-azure-aws
* **활성-활성 모드를 사용 하도록 설정 합니다.** 하지
* **BGP 구성:** 하지

예:

:::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/summary.png" alt-text="가상 네트워크 게이트웨이 요약":::

## <a name="configure-aws"></a>AWS 구성

1. 가상 사설 클라우드 (VPC)를 만듭니다.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpc.png" alt-text="VPC 정보 만들기":::

1. VPC (가상 네트워크) 내에 서브넷을 만듭니다.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-subnet-vpc.png" alt-text="서브넷 만들기":::

1. Azure VPN Gateway의 공용 IP 주소를 가리키는 고객 게이트웨이를 만듭니다. **고객 게이트웨이** 는 고객 게이트웨이 장치 (이 경우 Azure VPN Gateway)에 대 한 AWS 정보를 포함 하는 AWS 리소스입니다.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-customer-gw.png" alt-text="고객 게이트웨이 만들기":::

1. 가상 개인 게이트웨이를 만듭니다.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpg.png" alt-text="가상 개인 게이트웨이 만들기":::

1. VPC에 가상 개인 게이트웨이를 연결 합니다.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-vpg.png" alt-text="VPG을 VPC에 연결 합니다.":::

1. VPC를 선택 합니다.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attaching-vpg.png" alt-text="연결":::

1. 사이트 간 VPN 연결을 만듭니다.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpn-connection.png" alt-text="VPN 연결 만들기":::

1. 라우팅 옵션을 **정적** 으로 설정 하 고 Azure 서브넷-01 접두사 **(172.10.1.0/24)** 를 가리킵니다.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/set-static-route.png" alt-text="정적 경로 설정":::

1. 옵션을 채운 후에는 연결을 **만듭니다** .

1. 구성 파일을 다운로드 합니다. 올바른 구성을 다운로드 하려면 Azure가 유효한 옵션이 아니기 때문에 공급 업체, 플랫폼 및 소프트웨어를 **일반** 으로 변경 합니다.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/download-config.png" alt-text="구성 다운로드":::

1. 구성 파일에는 AWS에서 만든 두 IPsec 터널 각각에 대 한 사전 공유 키와 공용 IP 주소가 포함 되어 있습니다.

   **터널 1**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-1.png" alt-text="터널 1":::

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-1-config.png" alt-text="터널 1 구성":::

   **터널 2**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-2.png" alt-text="터널 2":::

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-2-config.png" alt-text="터널 2 구성":::

1. 터널을 만든 후에는 다음 예제와 유사한 내용이 표시 됩니다.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-connection-details.png" alt-text="AWS VPN 연결 정보":::

## <a name="create-local-network-gateway"></a>로컬 네트워크 게이트웨이 만들기

Azure에서 로컬 네트워크 게이트웨이는 일반적으로 온-프레미스 위치를 나타내는 Azure 리소스입니다. 온-프레미스 VPN 장치에 연결 하는 데 사용 되는 정보로 채워집니다. 그러나이 구성에서는 로컬 네트워크 게이트웨이가 만들어지고 AWS 가상 사설망 연결 정보로 채워집니다. Azure 로컬 네트워크 게이트웨이에 대 한 자세한 내용은 [azure VPN Gateway 설정](vpn-gateway-about-vpn-gateway-settings.md#lng)을 참조 하세요.

Azure에서 로컬 네트워크 게이트웨이를 만듭니다. 단계는 [로컬 네트워크 게이트웨이 만들기](tutorial-site-to-site-portal.md#LocalNetworkGateway)를 참조 하세요.

다음 값을 지정합니다.

* **이름:** 이 예제에서는 aws를 사용 합니다.
* **끝점:** IP 주소
* **IP 주소:** AWS 가상 개인 게이트웨이의 공용 IP 주소와 VPC CIDR 접두사 이전에 다운로드 한 구성 파일에서 공용 IP 주소를 찾을 수 있습니다.

AWS는 고가용성을 위해 두 개의 IPsec 터널을 만듭니다. 다음 예제에서는 IPsec 터널 #1의 공용 IP 주소를 보여 줍니다.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/local-network-gateway.png" alt-text="로컬 네트워크 게이트웨이":::

## <a name="create-vpn-connection"></a>VPN 연결 만들기

이 섹션에서는 Azure 가상 네트워크 게이트웨이와 AWS 게이트웨이 간의 VPN 연결을 만듭니다.

1. Azure 연결을 만듭니다. 연결을 만드는 단계는 [VPN 연결 만들기](tutorial-site-to-site-portal.md#CreateConnection)를 참조 하세요.

   다음 예제에서는 이전에 다운로드 한 구성 파일에서 공유 키를 가져왔습니다. 이 예제에서는 AWS에 의해 생성 되 고 구성 파일에 설명 된 IPsec 터널 #1 값을 사용 합니다.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-connection.png" alt-text="Azure connection 개체":::

1. 연결을 봅니다. 몇 분 후에 연결이 설정 됩니다.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/connection-established.png" alt-text="작업 연결":::

1. AWS IPsec 터널 #1 **작동** 하는지 확인 합니다.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-connection-established.png" alt-text="AWS 터널이 작동 하는지 확인":::

1. VPC 연결 된 경로 테이블을 편집 합니다.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/edit-aws-route.png" alt-text="경로 편집":::

1. Azure 서브넷에 경로를 추가 합니다. 이 경로는 VPC 게이트웨이를 통해 이동 합니다.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/save-aws-route.png" alt-text="경로 구성 저장":::

## <a name="configure-second-connection"></a>두 번째 연결 구성

이 섹션에서는 고가용성을 보장 하기 위해 두 번째 연결을 만듭니다.

1. AWS에서 IPsec 터널 #2의 공용 IP 주소를 가리키는 다른 로컬 네트워크 게이트웨이를 만듭니다. 대기 게이트웨이입니다.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-lng-standby.png" alt-text="로컬 네트워크 게이트웨이 만들기":::

1. Azure에서 AWS로 두 번째 VPN 연결을 만듭니다.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-connection-standby.png" alt-text="대기 로컬 네트워크 게이트웨이 연결 만들기":::

1. Azure VPN gateway 연결을 확인 합니다.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-tunnels.png" alt-text="Azure 연결 상태":::

1. AWS 연결을 확인 합니다. 이 예제에서는 연결이 설정 된 것을 볼 수 있습니다.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-tunnels.png" alt-text="AWS 연결 상태":::

## <a name="to-test-connections"></a>연결을 테스트 하려면

1. AWS의 VPC에 **인터넷 게이트웨이** 를 추가 합니다. 인터넷 게이트웨이는 Amazon VPN과 인터넷 간의 논리적 연결입니다. 이 리소스를 사용 하면 인터넷을 통해 AWS 공용 IP에서 테스트 VM을 통해 연결할 수 있습니다. 이 리소스는 VPN 연결에 필요 하지 않습니다. 테스트에만 사용 됩니다.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-igw.png" alt-text="인터넷 게이트웨이 만들기":::

1. **VPC에 연결을** 선택 합니다.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-igw.png" alt-text="VPC에 인터넷 게이트웨이 연결":::

1. VPC를 선택 하 고 **인터넷 게이트웨이를 연결** 합니다.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-igw-2.png" alt-text="게이트웨이 연결":::

1. 인터넷 게이트웨이를 통해 **0.0.0.0/0** (인터넷)에 연결할 수 있는 경로를 만듭니다.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/allow-internet-igw.png" alt-text="게이트웨이를 통해 경로 구성":::

1. Azure에서 경로가 자동으로 만들어집니다. **Vm > 네트워킹 > 네트워크 인터페이스 > 유효 경로** 를 선택 하 여 Azure vm에서 경로를 확인할 수 있습니다. 2 개의 경로, 연결당 1 개의 경로를 볼 수 있습니다.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-effective-routes.png" alt-text="유효 경로를 확인 합니다.":::

1. Azure의 Linux VM에서 테스트할 수 있습니다. 결과는 다음 예제와 유사 하 게 표시 됩니다.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-overview.png" alt-text="Linux VM에서 Azure 개요":::

1. AWS의 Linux VM에서이를 테스트할 수도 있습니다. 결과는 다음 예제와 유사 하 게 표시 됩니다.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-overview.png" alt-text="Linux VM에서 AWS 개요":::

1. Azure VM에서 연결을 테스트 합니다.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-ping.png" alt-text="Azure에서 테스트 Ping":::

1. AWS VM에서 연결을 테스트 합니다.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-ping.png" alt-text="AWS에서 테스트 Ping":::

## <a name="next-steps"></a>다음 단계

IKEv2 AWS 지원에 대 한 자세한 내용은 [AWS 문서](https://aws.amazon.com/about-aws/whats-new/2019/02/aws-site-to-site-vpn-now-supports-ikev2/)를 참조 하세요.
