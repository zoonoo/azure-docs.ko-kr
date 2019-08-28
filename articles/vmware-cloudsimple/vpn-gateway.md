---
title: CloudSimple의 Azure VMware 솔루션-VPN gateway 설정
description: 지점 및 사이트 간 vpn gateway 및 사이트 간 VPN gateway를 설정 하 고 온-프레미스 네트워크와 CloudSimple 사설 클라우드 간에 연결을 만드는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a61e789bb40d8263d293c70a67567ea61d5d2652
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972599"
---
# <a name="set-up-vpn-gateways-on-cloudsimple-network"></a>CloudSimple 네트워크에서 VPN 게이트웨이 설정

VPN 게이트웨이를 사용 하면 온-프레미스 네트워크와 클라이언트 컴퓨터에서 원격으로 CloudSimple 네트워크에 연결할 수 있습니다. 온-프레미스 네트워크와 CloudSimple 네트워크 간의 VPN 연결은 사설 클라우드의 vCenter 및 워크 로드에 대 한 액세스를 제공 합니다. CloudSimple은 사이트 간 VPN 및 지점 및 사이트 간 VPN 게이트웨이를 모두 지원 합니다.

## <a name="vpn-gateway-types"></a>VPN gateway 유형

* **사이트 간 VPN** 연결을 사용 하 여 온-프레미스 서비스에 액세스 하도록 사설 클라우드 워크 로드를 설정할 수 있습니다. 또한 온-프레미스 Active Directory를 사설 클라우드 vCenter에 인증 하기 위한 id 소스로 사용할 수 있습니다.  현재는 **정책 기반 VPN** 유형만 지원 됩니다.
* **지점 및 사이트 간 VPN** 연결은 컴퓨터에서 사설 클라우드에 연결 하는 가장 간단한 방법입니다. 지점 및 사이트 간 VPN 연결을 사용 하 여 원격으로 사설 클라우드에 연결 합니다. 지점 및 사이트 간 VPN 연결에 대 한 클라이언트를 설치 하는 방법에 대 한 자세한 내용은 [사설 클라우드에 대 한 vpn 연결 구성](set-up-vpn.md)을 참조 하세요.

한 지역에서 지점 및 사이트 간 VPN gateway와 사이트 간 VPN 게이트웨이 하나를 만들 수 있습니다.

## <a name="automatic-addition-of-vlansubnets"></a>VLAN/서브넷 자동 추가

CloudSimple VPN gateway는 VPN 게이트웨이에 VLAN/서브넷을 추가 하기 위한 정책을 제공 합니다.  정책을 사용 하면 관리 VLAN/서브넷 및 사용자 정의 VLAN/서브넷에 대해 다른 규칙을 지정할 수 있습니다.  관리 VLAN/서브넷에 대 한 규칙은 사용자가 만드는 모든 새 사설 클라우드에 적용 됩니다.  사용자 정의 Vlan/서브넷에 대 한 규칙을 사용 하 여 새 VLAN/서브넷을 사이트 간 VPN 게이트웨이의 기존 또는 새 사설 클라우드에 자동으로 추가할 수 있습니다. 각 연결에 대 한 정책을 정의 합니다.

VPN gateway에 Vlan/서브넷을 추가 하는 정책은 사이트 간 VPN 및 지점 및 사이트 간 VPN gateway에 모두 적용 됩니다.

## <a name="automatic-addition-of-users"></a>사용자 자동 추가

지점 및 사이트 간 VPN gateway를 사용 하 여 새 사용자에 대 한 자동 추가 정책을 정의할 수 있습니다. 기본적으로 구독의 모든 소유자와 참가자에 게는 CloudSimple 포털에 대 한 액세스 권한이 있습니다.  사용자는 CloudSimple 포털을 처음 시작 하는 경우에만 생성 됩니다.  **자동으로 규칙 추가** 를 선택 하면 새 사용자가 지점 및 사이트 간 VPN 연결을 사용 하 여 cloudsimple 네트워크에 액세스할 수 있습니다.

## <a name="set-up-a-site-to-site-vpn-gateway"></a>사이트 간 VPN gateway 설정

1. [CloudSimple 포털에 액세스](access-cloudsimple-portal.md) 하 고 **네트워크**를 선택 합니다.
2. **VPN Gateway**를 선택 합니다.
3. **새 VPN Gateway**을 클릭 합니다.

    ![VPN 게이트웨이 만들기](media/create-vpn-gateway.png)

4. **게이트웨이 구성**의 경우 다음 설정을 지정 하 고 **다음**을 클릭 합니다.

    * 게이트웨이 유형으로 **사이트 간 VPN** 을 선택 합니다.
    * 게이트웨이를 식별 하는 이름을 입력 합니다.
    * CloudSimple 서비스가 배포 되는 Azure 위치를 선택 합니다.
    * 필요에 따라 고가용성을 사용 하도록 설정 합니다.

    ![사이트 간 VPN 게이트웨이 만들기](media/create-vpn-gateway-s2s.png)

    > [!WARNING]
    > 고가용성을 사용 하도록 설정 하려면 온-프레미스 VPN 장치가 두 개의 IP 주소에 대 한 연결을 지원 해야 합니다. VPN gateway를 배포한 후에는이 옵션을 사용 하지 않도록 설정할 수 없습니다.

5. 온-프레미스 네트워크에서 첫 번째 연결을 만들고 **다음** 을 클릭 합니다.

    * 연결을 식별 하는 이름을 입력 합니다.
    * 피어 IP의 경우 온-프레미스 VPN 게이트웨이의 공용 IP 주소를 입력 합니다.
    * 온-프레미스 VPN 게이트웨이의 피어 식별자를 입력 합니다.  피어 식별자는 일반적으로 온-프레미스 VPN 게이트웨이의 공용 IP 주소입니다.  게이트웨이에서 특정 식별자를 구성한 경우에는 식별자를 입력 합니다.
    * 온-프레미스 VPN 게이트웨이에서 연결에 사용할 공유 키를 복사 합니다.  기본 공유 키를 변경 하 고 새 키를 지정 하려면 편집 아이콘을 클릭 합니다.
    * **온-프레미스 접두사**의 경우 cloudsimple 네트워크에 액세스 하는 온-프레미스 CIDR 접두사를 입력 합니다.  연결을 만들 때 여러 CIDR 접두사를 추가할 수 있습니다.

    ![사이트 간 VPN 게이트웨이 연결 만들기](media/create-vpn-gateway-s2s-connection.png)

6. 온-프레미스 네트워크에서 액세스 되는 사설 클라우드 네트워크에서 VLAN/서브넷을 사용 하도록 설정 하 고 **다음**을 클릭 합니다.

    * 관리 VLAN/서브넷을 추가 하려면 **사설 클라우드의 관리 vlan/서브넷 추가**를 사용 하도록 설정 합니다.  관리 서브넷은 vMotion 및 vSAN 서브넷에 필요 합니다.
    * VMotion 서브넷을 추가 하려면 **사설 클라우드의 vMotion 네트워크 추가**를 사용 하도록 설정 합니다.
    * VSAN 서브넷을 추가 하려면 **사설 클라우드의 vSAN 서브넷 추가**를 사용 하도록 설정 합니다.
    * 특정 Vlan을 선택 하거나 선택 취소 합니다.

    ![연결 만들기](media/create-vpn-gateway-s2s-connection-vlans.png)

7. 설정을 검토 하 고 **제출**을 클릭 합니다.

    ![사이트 간 VPN gateway 검토 및 만들기](media/create-vpn-gateway-s2s-review.png)

## <a name="create-point-to-site-vpn-gateway"></a>지점 및 사이트 간 VPN 게이트웨이 만들기

1. [CloudSimple 포털에 액세스](access-cloudsimple-portal.md) 하 고 **네트워크**를 선택 합니다.
2. **VPN Gateway**를 선택 합니다.
3. **새 VPN Gateway**을 클릭 합니다.

    ![VPN 게이트웨이 만들기](media/create-vpn-gateway.png)

4. **게이트웨이 구성**의 경우 다음 설정을 지정 하 고 **다음**을 클릭 합니다.

    * **지점 및 사이트 간 VPN** 을 게이트웨이 유형으로 선택 합니다.
    * 게이트웨이를 식별 하는 이름을 입력 합니다.
    * CloudSimple 서비스가 배포 되는 Azure 위치를 선택 합니다.
    * 지점 및 사이트 간 게이트웨이에 대 한 클라이언트 서브넷을 지정 합니다.  연결 하면 클라이언트 서브넷에서 DHCP 주소가 제공 됩니다.

5. **연결/사용자**에 대해 다음 설정을 지정 하 고 **다음**을 클릭 합니다.

    * 모든 현재 및 이후 사용자가 지점 및 사이트 간 게이트웨이를 통해 사설 클라우드에 액세스 하도록 자동으로 허용 하려면 **모든 사용자를 자동으로 추가**를 선택 합니다. 옵션을 선택 하면 사용자 목록의 모든 사용자가 자동으로 선택 됩니다. 목록에서 개별 사용자의 선택을 취소 하 여 자동 옵션을 재정의할 수 있습니다.
    * 개별 사용자를 선택 하려면 사용자 목록에서 확인란을 클릭 합니다.

6. Vlan/서브넷 섹션에서 게이트웨이 및 연결에 대 한 관리 및 사용자 Vlan/서브넷을 지정할 수 있습니다.

    * **자동으로 추가** 옵션은 게이트웨이의 글로벌 정책을 설정 합니다. 설정은 현재 게이트웨이에 적용 됩니다. 설정은 **선택** 영역에서 재정의할 수 있습니다.
    * **사설 클라우드의 관리 vlan/서브넷 추가**를 선택 합니다. 
    * 모든 사용자 정의 Vlan/서브넷을 추가 하려면 **사용자 정의 vlan/서브넷 추가**를 클릭 합니다.
    * **Select** 설정은 **자동 추가**에서 전역 설정을 재정의 합니다.

7. **다음** 을 클릭 하 여 설정을 검토 합니다. 편집 아이콘을 클릭 하 여 변경 합니다.
8. **만들기** 를 클릭 하 여 VPN gateway를 만듭니다.

### <a name="client-subnet-and-protocols-for-point-to-site-vpn-gateway"></a>지점 및 사이트 간 VPN gateway에 대 한 클라이언트 서브넷 및 프로토콜

지점 및 사이트 간 VPN gateway는 TCP 및 UDP 연결을 허용 합니다.  TCP 또는 UDP 구성을 선택 하 여 컴퓨터에서 연결할 때 사용할 프로토콜을 선택 합니다.

구성 된 클라이언트 서브넷은 TCP 및 UDP 클라이언트 모두에 사용 됩니다.  CIDR 접두사는 TCP와 UDP 클라이언트용으로 각각 하나씩 두 개의 서브넷으로 나뉩니다. 동시에 연결 하는 VPN 사용자 수를 기준으로 접두사 마스크를 선택 합니다.  

다음 표에서는 접두사 마스크에 대 한 동시 클라이언트 연결 수를 보여 줍니다.

| 접두사 마스크 | /24 | /25 | /26 | /27 | /28 |
|-------------|-----|-----|-----|-----|-----|
| 동시 TCP 연결 수 | 124 | 60 | 28 | 12 | 4 |
| 동시 UDP 연결 수 | 124 | 60 | 28 | 12 | 4 |

지점 및 사이트 간 VPN을 사용 하 여 연결 하려면 [지점 및 사이트 간 vpn을 사용 하 여 CloudSimple에 연결](set-up-vpn.md#connect-to-cloudsimple-using-point-to-site-vpn)을 참조 하세요.
