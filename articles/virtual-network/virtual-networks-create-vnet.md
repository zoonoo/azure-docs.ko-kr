<properties 
   pageTitle="VNet(가상 네트워크)을 만드는 방법"
   description="VNet(가상 네트워크)을 만드는 방법을 알아봅니다."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/08/2015"
   ms.author="telmos" />

# VNet(가상 네트워크)을 만드는 방법

VNet을 만들 경우 Vnet 내의 VM과 서비스는 인터넷을 거치지 않고도 서로 안전하게 통신할 수 있습니다. VNet을 다른 VNet 또는 온-프레미스 네트워크에 연결할 의도가 없는 경우 VPN 장치를 가져와 구성하거나, 다른 VNet 또는 로컬 네트워크에서 선택한 IP 주소를 조정하지 않아도 되므로 Azure VNet을 비교적 쉽고 빠르게 만들 수 있습니다.

>[AZURE.WARNING]나중에 다른 VNet 또는 온-프레미스 네트워크에 연결할 VNet을 만드는 데 이 절차를 사용하지 마세요. 보안 프레미스 간 연결 또는 하이브리드 연결을 만들려는 경우 [가상 네트워크 보안 프레미스 간 연결 정보](https://msdn.microsoft.com/library/azure/dn133798.aspx)를 참조하세요. 다른 VNet에 연결된 VNet을 만들려는 경우 [VNet 간 연결 구성](https://msdn.microsoft.com/library/azure/dn690122.aspx)을 참조하세요.

## VNet 구성

1. **Azure 관리 포털**에 로그인합니다.

1. 화면의 왼쪽 아래에서 **새로 만들기**를 클릭합니다. 탐색 창에서 **네트워크 서비스**를 클릭한 다음 **가상 네트워크**를 클릭합니다. **사용자 지정 만들기**를 클릭하여 구성 마법사를 시작합니다.

1. **가상 네트워크 정보** 페이지에서 아래 정보를 입력합니다.

	![가상 네트워크 세부 정보](./media/virtual-networks-create-vnet/IC736054.png)

	- **이름** - VNet 이름을 지정합니다. 예를 들어 *EastUSVNet*이라는 이름을 만들었습니다. 이름은 원하는 대로 만들 수 있습니다. VM 및 서비스를 배포할 때 이 VNet 이름을 사용하므로 너무 복잡하지 않은 이름을 사용하는 것이 좋습니다.

	- **위치** - 드롭다운 메뉴에서 위치(지역)를 선택합니다. 위치는 리소스(VM)를 이 VNet에 배포할 때 해당 리소스를 배치할 실제 위치와 직접적인 관련이 있습니다. 예를 들어 VM을 실제로 *미국 동부*에 배치하려는 경우 해당 위치 지역을 선택합니다. VNet을 만든 후에는 해당 VNet과 연결된 지역을 변경할 수 없습니다.

1. **DNS 서버 및 VPN 연결** 페이지에서는 아무 항목도 변경하지 않습니다. 화살표를 클릭하여 다음 페이지로 이동하세요. Azure에서는 기본적으로 VNet에 대한 기본 이름 확인 기능을 제공합니다. 이름 확인 요구 사항이 기본 Azure 이름 확인을 통해 처리할 수 있는 것보다 더 복잡할 수도 있습니다. 이 경우에는 나중에 DNS를 실행하는 가상 컴퓨터를 VNet에 추가할 수 있습니다. Azure 이름 확인 및 DNS에 대한 자세한 내용은 [이름 확인(DNS)](https://msdn.microsoft.com/library/azure/jj156088.aspx)을 참조하세요.

1. **가상 네트워크 주소 공간** 페이지에서 이 VNet에 사용할 주소 공간을 입력합니다. VM에 대해 특정 내부 IP 주소 범위를 사용해야 하거나 고정 DIP를 수신할 VM용 특정 서브넷을 만들려는 경우가 아니면 이 페이지의 항목을 변경할 필요가 없습니다. 여러 서브넷을 만들려는 경우에는 이 페이지에서 **서브넷 추가**를 클릭하면 됩니다.

	추가 정보:

	- 이 페이지의 범위에는 이 VNet에 VM을 배포할 때 해당 VM에서 수신할 동적 내부 IP 주소(DIP)가 포함됩니다. 이러한 IP 주소는 VNet 내 통신에만 사용되며, 인터넷 끝점용 IP 주소가 아닙니다.

	- 여기서는 프레미스 간 VPN 구성을 사용하여 이 개인 VNet을 온-프레미스 네트워크에 연결하지 않으므로 기존 온-프레미스 네트워크 IP 주소 범위로 이러한 설정을 조정할 필요가 없습니다. 나중에 프레미스 간 구성을 만들어야 할 수도 있다고 생각되는 경우 로컬 사이트에 이미 있는 범위를 사용하여 주소 공간을 지금 조정해야 라우팅 문제를 방지할 수 있습니다. 특히 중복된 주소 범위가 있는 경우 나중에 범위를 변경하는 작업이 약간 복잡할 수 있습니다.

	![주소 공간](./media/virtual-networks-create-vnet/IC716778.png)

1. **가상 네트워크 주소 공간** 페이지 오른쪽 아래의 확인 표시를 클릭하면 VNet이 생성됩니다. VNet이 생성되면 관리 포털의 **네트워크** 페이지에 있는 **상태** 아래에 **생성됨**이 나열됩니다.

1. VNet을 만들면 VNet 내에 VM 및 PaaS 인스턴스를 만들 수 있습니다. VNet 선택 옵션을 사용하려면 새 VM을 만들 때 **갤러리에서**를 선택해야 합니다. 기존에 이미 배포한 VM 및 PaaS 인스턴스를 단순히 새 VNet으로 이동할 수는 없습니다. 배포 중에 필요한 네트워크 구성 설정이 추가되기 때문입니다.

## VNet에 가상 컴퓨터 추가

VNet을 만든 후 새 VM을 해당 VNet에 추가할 수 있습니다. 먼저 VNet을 만든 후 VM을 배포하는 것이 중요합니다. VM을 배포한 후에는 다시 배포하지 않고 VNet으로 이동할 수는 없습니다. 관리 포털을 사용하여 VM을 만들 경우 VNet에 VM을 배포하는 인터페이스는 **새로 만들기/계산/가상 컴퓨터/갤러리에서**를 선택한 경우에만 사용할 수 있습니다. 마법사를 통해 VM을 만들 경우 **가상 컴퓨터 구성** 페이지에 **지역/선호도 그룹/가상 네트워크**가 표시됩니다. 드롭다운 목록에서 이미 만든 VNet을 선택합니다. 가상 컴퓨터 만들기에 대한 자세한 내용은 [Azure 가상 컴퓨터](../virtual-machines)를 참조하세요.

## 다음 단계

[VNet(가상 네트워크) 속성을 관리하는 방법](../virtual-networks-settings)

[VNet(가상 네트워크)에서 사용하는 DNS 서버를 관리하는 방법](../virtual-networks-manage-dns-in-vnet)

[가상 네트워크에서 공용 IP 주소를 사용하는 방법](../virtual-networks-public-ip-within-vnet)

[VNet(가상 네트워크)을 삭제하는 방법](../virtual-networks-delete-vnet)
 

<!---HONumber=62-->