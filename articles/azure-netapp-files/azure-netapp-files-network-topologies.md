---
title: Azure NetApp 파일 네트워크 계획에 대한 지침 | 마이크로 소프트 문서
description: Azure NetApp 파일을 사용하여 효과적인 네트워크 아키텍처를 설계하는 데 도움이 되는 지침을 설명합니다.
services: azure-netapp-files
documentationcenter: ''
author: ram-kakani
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: b-juche
ms.openlocfilehash: 12be766f36a0901079a5a26f20ea7dacc75268de
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667870"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Azure NetApp Files 네트워크 계획 지침

네트워크 아키텍처 계획은 모든 응용 프로그램 인프라를 설계하는 핵심 요소입니다. 이 문서에서는 Azure NetApp 파일의 풍부한 기능을 활용할 수 있도록 워크로드에 적합한 네트워크 아키텍처를 설계하는 데 도움이 됩니다.

Azure NetApp 파일 볼륨은 Azure 가상 네트워크 내에서 [위임된 서브넷이라는](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) 특수 목적 서브넷에 포함되도록 설계되었습니다. 따라서 필요에 따라 VNet, 동일한 리전의 피어있는 VNet 또는 가상 네트워크 게이트웨이(ExpressRoute 또는 VPN 게이트웨이)를 통해 온-프레미스에서 직접 볼륨에 액세스할 수 있습니다. 서브넷은 Azure NetApp 파일 전용이며 다른 Azure 서비스 또는 인터넷에 연결되지 않습니다.

## <a name="considerations"></a>고려 사항  

Azure NetApp 파일 네트워크를 계획할 때 몇 가지 고려 사항을 이해해야 합니다.

### <a name="constraints"></a>제약 조건

아래 기능은 현재 Azure NetApp 파일에 대해 지원되지 않습니다. 

* 위임된 서브넷에 적용된 NSG(네트워크 보안 그룹)
* Azure NetApp 파일 서브넷으로 주소 접두사가 있는 사용자 정의 경로(DR)
* Azure NetApp 파일 인터페이스의 Azure 정책(예: 사용자 지정 이름 지정 정책)
* Azure NetApp 파일 트래픽에 대한 로드 밸로드마저
* Azure NetApp 파일은 Azure 가상 WAN에서 지원되지 않습니다.

Azure NetApp 파일에는 다음 네트워크 제한이 적용됩니다.

* Azure NetApp 파일(피어VNet 포함)이 있는 VNet에서 사용 중인 IP 수는 1000을 초과할 수 없습니다. 우리는 고객 규모의 요구를 충족하기 위해이 제한을 증가하기 위해 노력하고 있습니다. 
* 각 Azure Virtual Network(VNet)에서 하나의 서브넷만 Azure NetApp Files에 위임할 수 있습니다.


### <a name="supported-network-topologies"></a>지원되는 네트워크 토폴로지

다음 표는 Azure NetApp 파일에서 지원하는 네트워크 토폴로지에 대해 설명합니다.  또한 지원되지 않는 토폴로지에 대한 해결 방법을 설명합니다. 

|    토폴로지    |    지원됩니다.    |     해결 방법    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    로컬 VNet의 볼륨에 대한 연결    |    예    |         |
|    피어가 있는 VNet(동일한 리전)의 볼륨에 대한 연결    |    예    |         |
|    피어링된 VNet의 볼륨에 대한 연결(교차 지역 또는 전역 피어링)    |    예    |    None    |
|    ExpressRoute 게이트웨이를 통한 볼륨에 대한 연결    |    예    |         |
|    익스프레스루트 게이트웨이를 통한 스포크 VNet 의 볼륨및 게이트웨이 전송을 통한 VNet 피어링으로 온-프레미스에서 볼륨으로 연결    |    예    |        |
|    VPN 게이트웨이를 통해 스포크 VNet의 볼륨으로 온-프레미스에서 볼륨으로 연결    |    예    |         |
|    VPN 게이트웨이를 통한 스포크 VNet 및 게이트웨이 전송을 통한 VNet 피어링의 온-프레미스에서 볼륨으로 의 연결    |    예    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Azure NetApp 파일 볼륨에 대한 가상 네트워크

이 섹션에서는 가상 네트워크 계획에 도움이 되는 개념에 대해 설명합니다.

### <a name="azure-virtual-networks"></a>Azure 가상 네트워크

Azure NetApp 파일 볼륨을 프로비전하기 전에 Azure 가상 네트워크(VNet)를 만들거나 구독에 이미 있는 가상 네트워크를 사용해야 합니다. VNet은 볼륨의 네트워크 경계를 정의합니다.  가상 네트워크 만들기에 대한 자세한 내용은 [Azure 가상 네트워크 설명서를](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)참조하십시오.

### <a name="subnets"></a>서브넷

서브넷은 가상 네트워크를 Azure 리소스에서 사용할 수 있는 별도의 주소 공간으로 분할합니다.  Azure NetApp 파일 볼륨은 위임된 서브넷이라는 특수 목적 [서브넷에](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)포함되어 있습니다. 

서브넷 위임은 서브넷에서 서비스별 리소스를 만들기 위해 Azure NetApp Files 서비스에 대한 명시적 권한을 부여합니다.  서비스를 배포할 때 고유 식별자를 사용합니다. 이 경우 Azure NetApp 파일에 대한 연결을 사용하도록 설정하기 위해 네트워크 인터페이스가 만들어집니다.

새 VNet을 사용하는 경우 서브넷을 만들고 서브넷을 Azure NetApp 파일에 대한 서브넷 위임의 지침에 따라 [Azure NetApp 파일에 위임할](azure-netapp-files-delegate-subnet.md)수 있습니다. 다른 서비스에 아직 위임되지 않은 기존 빈 서브넷을 위임할 수도 있습니다.

VNet이 다른 VNet으로 피어있는 경우 VNet 주소 공간을 확장할 수 없습니다. 따라서 VNet 주소 공간 내에서 새 위임된 서브넷을 만들어야 합니다. 주소 공간을 확장해야 하는 경우 주소 공간을 확장하기 전에 VNet 피어링을 삭제해야 합니다.

### <a name="udrs-and-nsgs"></a>DRS 및 NMG

Azure NetApp 파일에 대해 위임된 서브넷에서는 사용자 정의 경로(DR) 및 네트워크 보안 그룹(NSG)이 지원되지 않습니다.

해결 방법을 사용하면 Azure NetApp Files 위임된 서브넷의 트래픽을 허용하거나 거부하는 다른 서브넷에 NSG를 적용할 수 있습니다.  

## <a name="azure-native-environments"></a>Azure 네이티브 환경

다음 다이어그램은 Azure 네이티브 환경을 보여 줍니다.

![Azure 네이티브 네트워킹 환경](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>로컬 VNet

기본 시나리오는 동일한 VNet의 가상 시스템(VM)에서 Azure NetApp 파일 볼륨을 만들거나 연결하는 것입니다. 위의 다이어그램에서 VNet 2의 경우 볼륨 1은 위임된 서브넷에서 만들어지며 기본 서브넷의 VM 1에 탑재할 수 있습니다.

### <a name="vnet-peering"></a>VNet 피어링

동일한 지역에 서로의 리소스에 액세스해야 하는 추가 VNet이 있는 경우 [VNet 피어링을 사용하여 VNet을](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 연결하여 Azure 인프라를 통한 보안 연결을 활성화할 수 있습니다. 

위의 다이어그램에서 VNet 2 및 VNet 3을 고려하십시오. VM 1이 VM 2 또는 볼륨 2에 연결해야 하거나 VM 2가 VM 1 또는 볼륨 1에 연결해야 하는 경우 VNet 2와 VNet 3 간에 VNet 피어링을 사용하도록 설정해야 합니다. 

또한 VNet 1이 VNet 2로 피어되고 VNet 2가 동일한 리전에서 VNet 3을 피어로 피어인 시나리오를 고려합니다. VNet 1의 리소스는 VNet 2의 리소스에 연결할 수 있지만 VNet 1및 VNet 3을 피어로 지정하지 않는 한 VNet 3의 리소스에 연결할 수 없습니다. 

위의 다이어그램에서 VM 3은 볼륨 1에 연결할 수 있지만 VM 4는 볼륨 2에 연결할 수 없습니다.  그 이유는 스포크 VNet이 피어링되지 않고 _VNet 피어링을 통해 전송 라우팅이 지원되지 않기 때문입니다._

## <a name="hybrid-environments"></a>하이브리드 환경

다음 다이어그램은 하이브리드 환경을 보여 줍니다. 

![하이브리드 네트워킹 환경](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

하이브리드 시나리오에서 온-프레미스 데이터 센터의 응용 프로그램은 Azure의 리소스에 액세스해야 합니다.  데이터 센터를 Azure로 확장하거나 Azure 네이티브 서비스를 사용하거나 재해 복구를 위해 사용할 지 여부입니다. 사이트 간 VPN 또는 ExpressRoute를 통해 여러 리소스를 Azure의 리소스에 연결하는 방법에 대한 자세한 내용은 [VPN 게이트웨이 계획 옵션을](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable) 참조하십시오.

하이브리드 허브 스포크 토폴로지에서 Azure의 허브 VNet은 온-프레미스 네트워크에 대한 연결의 중심 지점 역할을 합니다. 스포크는 허브를 피어로 피어인 VNet이며 워크로드를 격리하는 데 사용할 수 있습니다.

구성에 따라 온-프레미스 리소스를 허브 및 대변인의 리소스에 연결할 수 있습니다.

위에 설명된 토폴로지에서 온-프레미스 네트워크는 Azure의 허브 VNet에 연결되며 허브 VNet을 피어로 피어인 동일한 지역에 2개의 스포크 VNet이 있습니다.  이 시나리오에서는 Azure NetApp 파일 볼륨에 대 한 지원 되는 연결 옵션은 다음과 같습니다.

* 온-프레미스 리소스 VM 1 및 VM 2는 사이트 간 VPN 또는 ExpressRoute 회로를 통해 허브의 볼륨 1에 연결할 수 있습니다. 
* 온-프레미스 리소스 VM 1 및 VM 2는 사이트 간 VPN 및 지역 Vnet 피어링을 통해 볼륨 2 또는 볼륨 3에 연결할 수 있습니다.
* 허브 VNet의 VM 3은 스포크 VNet 1의 볼륨 2와 스포크 VNet 2의 볼륨 3에 연결할 수 있습니다.
* 스포크 VNet 2의 스포크 VNet 1 및 VM 5의 VM 4는 허브 VNet의 볼륨 1에 연결할 수 있습니다.
* 스포크 VNet 1의 VM 4는 스포크 VNet 2에서 볼륨 3에 연결할 수 없습니다. 또한 스포크 VNet2의 VM 5는 스포크 VNet 1에서 볼륨 2에 연결할 수 없습니다. 스포크 VNet이 피어링되지 않고 _VNet 피어링을 통해 전송 라우팅이 지원되지 않기_때문에 이러한 경우입니다.
* 위의 아키텍처에서 스포크 VNET에도 게이트웨이가 있는 경우 허브의 게이트웨이를 통해 연결되는 온프레마에서 ANF 볼륨에 대한 연결이 손실됩니다. 의도적으로 스포크 VNet의 게이트웨이에 기본 설정이 제공되므로 해당 게이트웨이를 통해 연결하는 컴퓨터만 ANF 볼륨에 연결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure NetApp Files에 서브넷 위임](azure-netapp-files-delegate-subnet.md)
