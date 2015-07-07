<properties 
   pageTitle="VNet(가상 네트워크)에서 사용하는 DNS 서버 관리"
   description="VNet(가상 네트워크)에서 DNS 서버를 추가 및 제거하는 방법을 알아봅니다."
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

# VNet(가상 네트워크)에서 사용하는 DNS 서버 관리

관리 포털 또는 네트워크 구성 파일에서 VNet에 사용된 DNS 서버 목록을 관리할 수 있습니다. 각 VNet에 대해 최대 12대의 DNS 서버를 추가할 수 있습니다. DNS 서버를 지정할 경우 사용자 환경에 대해 올바른 순서로 DNS 서버를 나열했는지 확인하는 것이 중요합니다. DNS 서버 목록은 라운드 로빈 방식으로 작동하지 않으며, 지정된 순서로 사용됩니다. 목록의 첫 번째 DNS 서버에 연결할 수 있는 경우 클라이언트는 DNS 서버가 적절하게 작동하는지 여부와 관계없이 해당 DNS 서버를 사용합니다. 가상 네트워크에 대한 DNS 서버 순서를 변경하려면 목록에서 DNS 서버를 제거하고 원하는 순서로 다시 추가합니다.

>[AZURE.WARNING]DNS 목록을 업데이트한 후에는 새 DNS 서버 설정을 선택할 수 있도록 가상 네트워크에 있는 가상 컴퓨터를 다시 시작해야 합니다. 가상 컴퓨터는 다시 시작될 때까지 현재 구성을 계속 사용합니다.

## 관리 포털을 사용하여 가상 네트워크의 DNS 서버 목록 편집

1. **관리 포털**에 로그온합니다.

1. 탐색 창에서 **네트워크**를 클릭한 후 **이름** 열의 가상 네트워크 이름을 클릭합니다.

1. **Configure**를 클릭합니다.

1. **DNS 서버**에서 다음을 구성할 수 있습니다.

	- **새 DNS 서버를 등록(추가)하려면** - 간단히 상자에 이름 및 IP 주소를 입력합니다. 그러면 가상 네트워크 DNS 서버 목록에 DNS 서버가 추가되며 또한 Azure에 DNS 서버가 등록됩니다.

	- **이전에 등록한 DNS 서버를 추가하려면** - DNS 서버를 Azure에 이미 등록한 경우 미리 채워진 목록에서 선택할 수 있습니다.

	- **가상 네트워크에서 DNS 서버를 제거하려면** - 제거할 서버 옆에 있는 X를 클릭합니다. 그러면 이 가상 네트워크 목록에서만 서버가 제거됩니다. DNS 서버는 다른 가상 네트워크에서 사용할 수 있도록 Azure에 등록된 상태로 유지됩니다. 구독에서 DNS 서버를 삭제하려면 **네트워크 -> DNS서버** 페이지로 이동합니다.

	- **DNS 서버의 순서를 변경하려면** - 목록에 있는 DNS 서버를 모두 제거한 후 원하는 순서로 다시 추가합니다. 이 목록은 라운드 로빈 DNS 목록이 아닙니다.

	- **DNS 서버 이름을 바꾸려면** - 목록에서 DNS 서버를 강조 표시한 후 새 이름을 입력합니다. 그러면 새 DNS 서버가 Azure에 등록될 뿐만 아니라 가상 네트워크의 DNS 서버 목록에도 추가됩니다. 이전 DNS 서버 및 해당 IP 주소도 Azure에 계속 등록되어 있습니다. 해당 서버를 다른 가상 네트워크에 사용하지 않는 경우 **DNS 서버** 페이지에서 삭제할 수 있습니다.

1. 새로운 DNS 서버 구성을 저장하려면 페이지 맨 아래에 있는 **저장**을 클릭합니다.

1. 가상 네트워크에 있는 가상 컴퓨터에서 새로운 DNS 설정을 가져올 수 있도록 하려면 해당 가상 컴퓨터를 다시 시작합니다.

## 네트워크 구성 파일을 사용하여 DNS 서버 목록 편집

네트워크 구성 파일을 사용하여 DNS 서버 목록을 편집하려면 먼저 관리 포털에서 구성 설정을 내보냅니다. 그런 다음 네트워크 구성 파일을 편집하고 관리 포털을 통해 다시 가져옵니다. 다음은 이 프로세스를 완료하기 위한 대략적인 단계 목록입니다.

1. 가상 네트워크 설정을 네트워크 구성 파일로 내보냅니다. 네트워크 구성 설정을 내보내는 단계에 대한 자세한 내용은 [가상 네트워크 설정을 네트워크 구성 파일로 내보내기](https://msdn.microsoft.com/library/azure/dn133804.aspx)를 참조하세요.

1. 가상 네트워크에 대한 DNS 서버 정보를 지정합니다. DNS 서버를 지정하는 방법에 대한 자세한 내용은 [가상 네트워크 구성 파일에서 DNS 서버 지정](https://msdn.microsoft.com/library/windowsazure/jj156098.aspx)을 참조하세요. 네트워크 구성 파일에 대한 자세한 내용은 [Azure 가상 네트워크 구성 스키마](https://msdn.microsoft.com/library/azure/jj157100.aspx) 및 [네트워크 구성 파일을 사용하여 가상 네트워크 구성](https://msdn.microsoft.com/library/azure/jj156097.aspx)을 참조하세요.

1. 네트워크 구성 파일을 가져옵니다. 네트워크 구성 파일을 가져오는 단계에 대한 자세한 내용은 [네트워크 구성 파일 가져오기](https://msdn.microsoft.com/library/azure/jj156213.aspx)를 참조하세요.

1. 가상 네트워크에 있는 가상 컴퓨터에서 새로운 DNS 설정을 가져올 수 있도록 하려면 해당 가상 컴퓨터를 다시 시작합니다.

## 다음 단계

[VNet(가상 네트워크) 속성을 관리하는 방법](../virtual-networks-settings)

[가상 네트워크에서 공용 IP 주소를 사용하는 방법](../virtual-networks-public-ip-within-vnet)

[VNet(가상 네트워크)을 삭제하는 방법](../virtual-networks-delete-vnet)

<!---HONumber=62-->