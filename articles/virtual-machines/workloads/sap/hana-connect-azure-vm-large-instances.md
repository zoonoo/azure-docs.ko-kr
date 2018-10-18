---
title: Azure(대규모 인스턴스)의 SAP HANA에 대한 가상 머신에서 연결 설정 | Microsoft Docs
description: Azure(대규모 인스턴스)의 SAP HANA에 대한 가상 머신에서 연결을 설정합니다.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f0e09e6dfce5d0ede525f461193866219b7f9429
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44167675"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Azure VM을 HANA 큰 인스턴스에 연결

이미 [Azure에서 SAP HANA(큰 인스턴스) 개요 및 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)에서 언급했듯이 Azure에서 SAP 응용 프로그램 계층을 사용하는 HANA 큰 인스턴스의 최소 배포는 다음과 같습니다.

![Azure(큰 인스턴스)에서 SAP HANA 및 온-프레미스로 연결된 Azure VNet](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Azure VNet 쪽을 자세히 살펴보면 다음에 대한 필요성을 인식하게 됩니다.

- SAP 응용 프로그램 계층의 VM을 배포하는 데 사용할 Azure VNet의 정의입니다.
- 이는 자동으로 Azure Vnet의 기본 서브넷이 VM을 배포하는 데 사용되는 실제 서브넷으로 정의됨을 의미합니다.
- 만들어진 Azure VNet에는 하나 이상의 VM 서브넷과 하나의 ExpressRoute 게이트웨이 서브넷이 있어야 합니다. 이러한 서브넷은 다음 섹션에서 지정하고 설명한 대로 IP 주소 범위가 할당되어야 합니다.

따라서 HANA 큰 인스턴스에 대한 Azure VNet 생성에 대해 자세히 살펴보겠습니다.

## <a name="creating-the-azure-vnet-for-hana-large-instances"></a>HANA 큰 인스턴스에 대한 Azure VNet 만들기

>[!Note]
>이 HANA 큰 인스턴스에 대한 Azure VNet은 Azure Resource Manager 배포 모델을 사용하여 만들어야 합니다. 일반적으로 클래식 배포 모델로 알려진 이전 Azure 배포 모델은 HANA 큰 인스턴스 솔루션에 지원되지 않습니다.

Azure Portal, PowerShell, Azure 템플릿 또는 Azure CLI를 사용하여 VNet을 만들 수 있습니다([Azure Portal을 사용하여 가상 네트워크 만들기](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network) 참조). 다음 예에서 Azure Portal을 통해 생성된 VNet에 대해 살펴봅니다.

Azure Portal을 통해 Azure VNet의 정의를 살펴보기 위해 몇 가지 정의와 그 정의가 다양한 IP 주소 범위에 나열된 것과 어떻게 관련되는지 살펴보겠습니다. **주소 공간**에 대해 이야기할 때 Azure VNet의 사용이 허용된 주소 공간을 의미합니다. 이 주소 공간은 VNet에서 BGP 경로 전파에 사용할 주소 범위이기도 합니다. 이 **주소 공간**은 여기에서 볼 수 있습니다.

![Azure Portal에 표시된 Azure VNet의 주소 공간](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

앞의 경우 10.16.0.0/16으로, Azure VNet은 상당히 크고 넓은 IP 주소 범위를 사용했습니다. 이 VNet 내의 후속 서브넷의 모든 IP 주소 범위가 '주소 공간' 내에 자체 범위를 포함할 수 있음을 의미합니다. 일반적으로는 Azure에서 단일 VNet에 대해서는 이러한 큰 주소 범위를 권장하지 않습니다. 한편, 한 단계 더 나아가 Azure VNet에 정의된 서브넷을 살펴보겠습니다.

![Azure VNet 서브넷 및 해당 IP 주소 범위](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

알다시피 VNet은 첫 번째 VM 서브넷(여기서는 'default'라고 함)과 'GatewaySubnet'이라는 서브넷을 포함합니다.
다음 섹션에서, 그림에서 'default'라고 불리는 서브넷의 IP 주소 범위를 **Azure VM 서브넷 IP 주소 범위**라고 합니다. 다음 섹션에서는 게이트웨이 서브넷의 IP 주소 범위를 **VNet 게이트웨이 서브넷 IP 주소 범위**라고 합니다. 

위의 두 그림에 설명된 경우에서 **VNet 주소 공간**은 **Azure VM 서브넷 IP 주소 범위** 및 **VNet 게이트웨이 서브넷 IP 주소 범위**를 모두 포함합니다. 

IP 주소 범위를 절약하거나 구체적으로 지정해야 하는 경우 VNet의 **VNet 주소 공간**을 각 서브넷에 사용되는 특정 범위로 제한할 수 있습니다. 이 경우 VNet의 **VNet 주소 공간**을 여기 표시된 것처럼 여러 특정 범위로 정의할 수 있습니다.

![두 공간이 있는 Azure VNet 주소 공간](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

이 경우 **VNet 주소 공간**에는 두 개의 공간이 정의되어 있습니다. 이 두 공간은 **Azure VM 서브넷 IP 주소 범위** 및 **VNet 게이트웨이 서브넷 IP 주소 범위**에 대해 정의된 IP 주소 범위와 동일합니다.

이러한 테넌트 서브넷(VM 서브넷)에 원하는 모든 명명 표준을 사용할 수 있습니다. 그러나 Azure(대형 인스턴스) ExpressRoute 회로 상의 SAP HANA에 연결하는 **각 VNet에 대한 하나 및 하나만의 게이트웨이 서브넷이 항상 있어야 합니다**. 그리고 ExpressRoute 게이트웨이가 제대로 배치되도록 하려면 **언제나 이 게이트웨이 서브넷의 이름이 "GatewaySubnet"이어야 합니다**.

> [!WARNING] 
> 게이트웨이 서브넷의 이름이 항상 "GatewaySubnet"이어야 하는 것이 아주 중요합니다.

연속되지 않은 주소 범위를 활용하더라도 여러 VM 서브넷을 사용할 수 있습니다. 그러나 이전에 언급했듯이, 이러한 주소 범위는 집계된 양식이나 VM 서브넷 및 게이트웨이 서브넷의 정확한 범위 목록으로 VNet의 **VNet 주소 공간**에 포함되어야 합니다.

HANA 큰 인스턴스에 연결되는 Azure VNet에 대한 중요한 사실 요약:

- HANA 큰 인스턴스의 초기 배포를 수행하는 경우 **VNet 주소 공간**을 Microsoft에 제출해야 합니다. 
- **VNet 주소 공간**은 Azure VM 서브넷 IP 주소 범위 및 VNet 게이트웨이 서브넷 IP 주소 범위에 대한 범위를 다루는 더 큰 범위일 수 있습니다.
- 또는 VM 서브넷 IP 주소 범위와 VNet 게이트웨이 서브넷 IP 주소 범위의 다양한 IP 주소 범위를 포함하는 여러 범위를 **VNet 주소 공간**으로 제출할 수도 있습니다.
- 정의된 **VNet 주소 공간**은 BGP 라우팅 전파에 사용됩니다.
- 게이트웨이 서브넷의 이름은 **"GatewaySubnet"** 이어야 합니다.
- **VNet 주소 공간**은 Azure에서 HANA 큰 인스턴스 단위로 트래픽을 허용 또는 차단하기 위해 HANA 큰 인스턴스 쪽의 필터로 사용됩니다. Azure VNet의 BGP 라우팅 정보와 HANA 큰 인스턴스 측에서 필터링을 위해 구성된 IP 주소 범위가 일치하지 않으면 연결 문제가 발생할 수 있습니다.
- 게이트웨이 서브넷에 대한 자세한 내용은 'VNet을 HANA 큰 인스턴스 ExpressRoute에 연결' 섹션에서 자세히 설명합니다.



## <a name="different-ip-address-ranges-to-be-defined"></a>다양한 IP 주소 범위 정의 

HANA 큰 인스턴스를 배포하는 데 필요한 IP 주소 범위에 대해서는 이전 섹션에서 이미 소개했습니다. 하지만 중요한 IP 주소 범위가 몇 가지 더 있습니다. 이 내용에 대해 자세히 알아보겠습니다. 초기 배포 요청을 보내기 전에 Microsoft에 모두 제출할 필요가 없는 다음 IP 주소를 정의해야 합니다.

- **VNet 주소 공간:** 이미 이전에 소개한 것처럼 SAP HANA 큰 인스턴스 환경에 연결되는 Azure Virtual Network(VNet)의 주소 공간 매개 변수에 할당한(또는 할당 계획) IP 주소 범위입니다. 이 주소 공간 매개 변수는 이전 그림에서와 같이 Azure VM 서브넷 범위와 Azure 게이트웨이 서브넷 범위로 구성된 여러 줄 값입니다. 이 범위는 온-프레미스, 서버 IP 풀 또는 ER-P2P 주소 범위와 겹치면 안 됩니다. 이런 IP 주소 범위는 어떻게 가져오나요? 회사 네트워크 팀 또는 서비스 공급자는 네트워크 내에서 사용되지 않는 하나 이상의 IP 주소 범위를 제공해야 합니다. 예: Azure VM 서브넷(이전 참조)이 10.0.1.0/24이고 Azure 게이트웨이 서브넷(다음 참조)이 10.0.2.0/28이면, Azure VNet 주소 공간은 10.0.1.0/24 및 10.0.2.0/28로 두 줄이 되도록 하는 것이 좋습니다. 주소 공간 값을 집계할 수 있지만 나중에 네트워크의 다른 곳에서 더 큰 주소 공간 내의 사용되지 않은 IP 주소를 우발적으로 재사용하는 것을 방지하기 위해 서브넷 범위와 일치시키는 것이 좋습니다. **VNET 주소 공간은 초기 배포를 요청할 때 Microsoft에 제출해야 하는 IP 주소 범위입니다.**

- **Azure VM 서브넷 IP 주소 범위:** 이미 앞에서 설명한 것처럼 SAP HANA 큰 인스턴스 환경에 연결되는 Azure VNET의 Azure VNet 서브넷 매개 변수에 할당한(또는 할당 계획) IP 주소 범위입니다. 이 IP 주소 범위는 IP 주소를 Azure VM에 할당하는 데 사용됩니다. 이 범위를 벗어난 IP 주소 범위가 SAP HANA 큰 인스턴스 환경에 연결되도록 허용됩니다. 필요한 경우 여러 Azure VM 서브넷을 사용할 수 있습니다. 각 Azure VM 서브넷에 대해 /24 CIDR 블록을 사용하는 것이 좋습니다. 이 주소 범위는 Azure VNet 주소 공간에 사용된 값에 포함되어야 합니다. 이 IP 주소 범위는 어떻게 가져오나요? 회사 네트워크 팀 또는 서비스 공급자는 네트워크 내에서 현재 사용되지 않는 IP 주소 범위를 제공해야 합니다.

- **VNet 게이트웨이 서브넷 IP 주소 범위:** 사용 계획인 기능에 따라 권장되는 크기는 다음과 같습니다.
   - Ultra-performance ExpressRoute 게이트웨이: /26 주소 블록 - SKU의 Type II 클래스에 필요함
   - High-performance ExpressRoute 게이트웨이(이하)를 사용하고 VPN 및 ExpressRoute의 동시 존재: /27 주소 블록
   - 기타 모든 상황: /28 주소 블록. 이 주소 범위는 “VNet 주소 공간” 값에 사용된 값에 포함되어야 합니다. 이 주소 범위는 Microsoft에 제출해야 하는 Azure VNet 주소 공간 값에 사용된 값에 포함되어야 합니다. 이 IP 주소 범위는 어떻게 가져오나요? 회사 네트워크 팀 또는 서비스 공급자는 네트워크 내에서 현재 사용되지 않는 IP 주소 범위를 제공해야 합니다. 

- **ER-P2P 연결을 위한 주소 범위:** 이 범위는 SAP HANA 큰 인스턴스 ExpressRoute(ER) P2P 연결을 위한 IP 범위입니다. 이 IP 주소 범위는 /29 CIDR IP 주소 범위여야 합니다. 이 범위는 온-프레미스 또는 기타 Azure IP 주소 범위와 겹치면 안 됩니다. 이 IP 주소 범위는 Azure VNet ExpressRoute 게이트웨이에서 SAP HANA 큰 인스턴스 서버로 ER 연결을 설정하는 데 사용됩니다. 이 IP 주소 범위는 어떻게 가져오나요? 회사 네트워크 팀 또는 서비스 공급자는 네트워크 내에서 현재 사용되지 않는 IP 주소 범위를 제공해야 합니다. **이 범위는 초기 배포를 요청할 때 Microsoft에 제출해야 하는 IP 주소 범위입니다.**
  
- **서버 IP 풀 주소 범위:** 이 IP 주소 범위는 개별 IP 주소를 HANA 큰 인스턴스 서버에 할당하는 데 사용됩니다. 권장되는 서브넷 크기는 /24 CIDR 블록이지만 필요한 경우 최소 64개의 IP 주소를 제공하는 것보다 적은 규모일 수 있습니다. 이 범위에서 처음 30개 IP 주소는 Microsoft에서 사용하도록 예약됩니다. 범위 크기를 선택할 때 이 사실을 고려해야 합니다. 이 범위는 온-프레미스 또는 기타 Azure IP 주소와 겹치면 안 됩니다. 이 IP 주소 범위는 어떻게 가져오나요? 회사 네트워크 팀 또는 서비스 공급자는 네트워크 내에서 현재 사용되지 않는 IP 주소 범위를 제공해야 합니다. Azure(큰 인스턴스)에서 SAP HANA에 필요한 특정 IP 주소를 할당하는 데 사용할 /24(권장) 고유 CIDR 블록 **이 범위는 초기 배포를 요청할 때 Microsoft에 제출해야 하는 IP 주소 범위입니다.**
 
위의 IP 주소 범위를 정의하고 계획해야 하지만 모든 항목을 Microsoft에 전송해야 하는 것은 아닙니다. 위의 내용을 요약하면 Microsoft에 이름을 지정해야 하는 IP 주소 범위는 다음과 같습니다.

- Azure VNet 주소 공간
- ER-P2P 연결에 대한 주소 범위
- 서버 IP 풀 주소 범위

HANA 큰 인스턴스에 연결해야 하는 추가 VNet을 추가하려면 Microsoft에 추가하는 새 Azure VNet 주소 공간을 제출해야 합니다. 

다음은 구성하고 궁극적으로 Microsoft에 제공해야 하는 다양한 범위 및 몇 가지 범위 예입니다. 보시다시피, Azure VNet 주소 공간의 값은 첫 번째 예에서는 집계되지 않지만 첫 번째 Azure VM 서브넷 IP 주소 범위와 VNet 게이트웨이 서브넷 IP 주소 범위의 범위에서 정의됩니다. Azure VNet 내에서 여러 VM 서브넷을 사용하는 것은 Azure VNet 주소 공간의 일부로 추가 VM 서브넷의 추가 IP 주소 범위를 구성하고 제출함으로써 적절하게 작동합니다.

![Azure(큰 인스턴스)에서 SAP HANA 최소 배포에 필요한 IP 주소 범위](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

또한 Microsoft에 제출하는 데이터를 집계하는 가능성도 있습니다. 이 경우 Azure VNet의 주소 공간만 하나의 공간을 포함합니다. 이전 예제에 사용된 IP 주소 범위 사용. 이 집계된 VNet 주소 공간은 다음과 같습니다.

![Azure(큰 인스턴스)에서 SAP HANA 최소 배포에 필요한 IP 주소 범위의 두 번째 가능성](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

위에서 볼 수 있듯이 Azure VNet의 주소 공간을 정의한 두 개의 작은 범위 대신 4096개의 IP 주소를 포함하는 하나의 더 큰 범위를 포함합니다. 주소 공간의 그러한 큰 정의는 다소 큰 범위를 사용하지 않고 남겨 둡니다. VNet 주소 공간 값이 BGP 경로 전파에 사용되므로 온-프레미스 또는 네트워크의 다른 곳에서 사용하지 않는 범위를 사용하면 라우팅 문제가 발생할 수 있습니다. 따라서 주소 공간을 사용된 실제 서브넷 주소 공간과 밀접하게 연계하는 것이 좋습니다. VNet에서 가동 중단 시간이 발생하지 않고도 필요할 때 새 주소 공간 값을 나중에 언제든지 추가할 수 있습니다.
 
> [!IMPORTANT] 
> ER-P2P, 서버 IP 풀, Azure VNet 주소 공간의 각 IP 주소 범위는 서로 또는 네트워크의 다른 곳에서 사용되는 다른 범위와 겹치지 **않아야** 하며 각각은 분리되어 있어야 하고 앞의 두 그림은 다른 범위의 서브넷이 아님을 보여 줍니다. 범위 간 겹침이 발생하면 Azure VNet은 ExpressRoute 회로에 연결되지 않을 수 있습니다.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>주소 범위를 정의한 후 다음 단계
IP 주소 범위를 정의한 후에는 다음 작업이 수행되어야 합니다.

1. Azure VNet 주소 공간, ER-P2P 연결 및 서버 IP 풀 주소 범위에 대한 IP 주소 범위와 문서 시작 부분에 나열된 다른 데이터를 함께 제출합니다. 이 시점에서 VNet과 VM 서브넷 만들기를 시작할 수도 있습니다. 
2. ExpressRoute 회로는 Microsoft가 Azure 구독과 HANA 큰 인스턴스 스탬프 사이에 만듭니다.
3. 테넌트 네트워크는 Microsoft의 큰 인스턴스 스탬프에서 생성됩니다.
4. Microsoft는 HANA 큰 인스턴스와 통신할 Azure VNet 주소 공간의 IP 주소를 허용하도록 Azure(큰 인스턴스)의 SAP HANA에 네트워킹을 구성합니다.
5. 구매한 특정 Azure(큰 인스턴스)의 SAP HANA SKU에 따라 Microsoft에서 테넌트 네트워크의 계산 단위를 지정하고 저장소를 할당 및 탑재하며 운영 체제(SUSE 또는 Red Hat Linux)를 설치합니다. 이러한 단위에 대한 IP 주소는 Microsoft에 제출한 서버 IP 풀 주소 범위를 벗어나는 주소를 지정합니다.

배포 프로세스의 끝부분에는 Microsoft에서 다음 데이터를 사용자에게 제공합니다.
- HANA 큰 인스턴스에 Azure VNet을 연결하는 ExpressRoute 회로에 Azure VNet을 연결하는 데 필요한 정보:
     - 권한 부여 키
     - ExpressRoute PeerID
- ExpressRoute 회로 및 Azure VNet을 설정한 후 HANA 큰 인스턴스에 액세스하기 위한 데이터.

[SAP HANA 큰 인스턴스에 대한 종단 간 설치](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/) 문서에서 HANA 큰 인스턴스를 연결하는 순서를 확인할 수 있습니다. 대다수의 다음 단계는 해당 문서의 배포 예에 표시됩니다. 

**다음 단계**

- [VNet을 HANA 대규모 인스턴스 ExpressRoute에 연결](hana-connect-vnet-express-route.md)을 참조하세요.