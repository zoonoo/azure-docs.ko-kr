---
title: Azure VMware Solution 배포 계획
description: 이 문서에서는 Azure VMware Solution 배포 워크플로를 간략하게 설명합니다.  결국에는 VM(가상 머신)을 만들고 마이그레이션할 수 있는 환경이 준비됩니다.
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: 60e0a4083c0253d322b2e10472d0df7496722c10
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107247"
---
# <a name="planning-the-azure-vmware-solution-deployment"></a>Azure VMware Solution 배포 계획

이 문서에서는 배포 중에 사용할 정보를 식별하고 수집하기 위한 계획 프로세스를 제공합니다. 배포를 계획할 때 배포 중에 쉽게 참조할 수 있도록 수집한 정보를 문서화해야 합니다.

이 빠른 시작에 설명된 단계는 VM(가상 머신)을 만들고 마이그레이션을 수행할 수 있는 프로덕션 준비 환경을 제공합니다. 

수집할 데이터를 추적하려면 [HCX 계획 검사 목록](https://www.virtualworkloads.com/2021/04/hcx-planning-checklist/)을 받습니다.

> [!IMPORTANT]
> Azure VMware Solution 리소스를 만들기 위해 준비할 때 초기에 호스트 할당량을 요청하는 것이 중요합니다. 지금 호스트 할당량을 요청할 수 있으므로 계획 프로세스가 완료되면 Azure VMware Solution 프라이빗 클라우드를 배포할 준비가 된 것입니다. 지원 팀에서 호스트 할당량 요청을 받으면 요청을 확인하고 호스트를 할당하는 데 최대 5일(영업일 기준)이 걸립니다. 기존 Azure VMware Solution 프라이빗 클라우드가 있고 더 많은 호스트를 할당하려는 경우에도 동일한 프로세스를 완료합니다. 자세한 내용은 구독 유형에 따라 다음 링크를 참조하세요.
> - [EA 고객](enable-azure-vmware-solution.md?tabs=azure-portal#request-host-quota-for-ea-customers)
> - [CSP 고객](enable-azure-vmware-solution.md?tabs=azure-portal#request-host-quota-for-csp-customers)

## <a name="subscription"></a>Subscription

Azure VMware Solution을 배포하는 데 사용할 구독을 확인합니다.  새 구독을 만들거나 기존 구독을 다시 사용할 수 있습니다.

>[!NOTE]
>구독은 Microsoft 기업계약 또는 클라우드 솔루션 공급자 Azure 플랜과 연결되어야 합니다. 자세한 내용은 [Azure VMware Solution 리소스를 사용하도록 설정하는 방법](enable-azure-vmware-solution.md)을 참조하세요.

## <a name="resource-group"></a>Resource group

Azure VMware Solution에 사용하려는 리소스 그룹을 확인합니다.  일반적으로 리소스 그룹은 특별히 Azure VMware Solution용으로 만들어지지만, 기존 리소스 그룹을 사용할 수 있습니다.

## <a name="region"></a>지역

Azure VMware Solution을 배포하려는 지역을 확인합니다.  자세한 내용은 [지역별 사용 가능한 Azure 제품 가이드](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=azure-vmware)를 참조하세요.

## <a name="resource-name"></a>리소스 이름

배포 중에 사용할 리소스 이름을 정의합니다.  리소스 이름은 Azure VMware Solution 프라이빗 클라우드의 제목으로 지정되는 친숙하고 설명이 포함된 이름입니다.

>[!IMPORTANT]
>이름은 40자를 초과하면 안 됩니다. 이름이 이 제한을 초과하면 프라이빗 클라우드에서 사용할 공용 IP 주소를 만들 수 없습니다. 

## <a name="size-hosts"></a>호스트 크기 조정

Azure VMware Solution을 배포할 때 사용할 호스트 크기를 확인합니다.  전체 목록은 [Azure VMware Solution 프라이빗 클라우드 및 클러스터](concepts-private-clouds-clusters.md#hosts) 설명서를 참조하세요.

## <a name="number-of-clusters-and-hosts"></a>클러스터 및 호스트 수

사용자가 수행하는 첫 번째 Azure VMware Solution 배포는 단일 클러스터를 포함하는 프라이빗 클라우드로 구성됩니다. 배포의 경우 첫 번째 클러스터에 배포할 호스트 수를 정의해야 합니다.

>[!NOTE]
>클러스터당 최소 호스트 수는 3개이고 최대 호스트 수는 16개입니다. 프라이빗 클라우드당 최대 클러스터 수는 4개입니다. 

자세한 내용은 [Azure VMware Solution 프라이빗 클라우드 및 클러스터](concepts-private-clouds-clusters.md#clusters) 설명서를 참조하세요.

>[!TIP]
>초기 배포 수를 초과해야 하는 경우 언제든지 나중에 클러스터를 확장하고 클러스터를 추가할 수 있습니다.

## <a name="ip-address-segment-for-private-cloud-management"></a>프라이빗 클라우드 관리를 위한 IP 주소 세그먼트

배포를 계획하는 첫 번째 단계는 IP 조각화를 계획하는 것입니다. Azure VMware Solution에는 /22 CIDR 네트워크가 필요합니다. 이 주소 공간은 더 작은 네트워크 세그먼트(서브넷)로 분할되어 vCenter, VMware HCX, NSX-T 및 vMotion 기능을 비롯한 Azure VMware Solution 관리 세그먼트에 사용됩니다. 아래 시각화는 이 세그먼트가 사용되는 위치를 강조 표시합니다.

이 /22 CIDR 네트워크 주소 블록은 온-프레미스 또는 Azure에 있는 기존 네트워크 세그먼트와 겹치지 않아야 합니다.

**예:** 10.0.0.0/22

프라이빗 클라우드 [네트워크 계획 검사 목록](tutorial-network-checklist.md#routing-and-subnet-considerations)에 따라 /22 CIDR 네트워크가 어떻게 세분화되었는지 자세히 분석합니다.

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="확인 - IP 주소 세그먼트" border="false":::  

## <a name="ip-address-segment-for-virtual-machine-workloads"></a>가상 머신 워크로드에 대한 IP 주소 세그먼트

모든 VMware 환경과 마찬가지로 가상 머신은 네트워크 세그먼트에 연결해야 합니다. Azure VMware Solution에는 두 가지 유형의 세그먼트, 즉 L2 확장 세그먼트(뒷부분에서 설명)와 NSX-T 네트워크 세그먼트가 있습니다. Azure VMware Solution의 프로덕션 배포가 확장됨에 따라 온-프레미스 및 로컬 NSX-T 네트워크 세그먼트의 L2 확장 세그먼트 조합이 있는 경우가 많습니다. 초기 배포를 계획하려면 Azure VMware Solution에서 단일 네트워크 세그먼트(IP 네트워크)를 식별합니다. 이 네트워크는 온-프레미스 또는 Azure의 미사용 네트워크 세그먼트와 겹치지 않아야 하며 앞에서 정의한 /22 네트워크 세그먼트 내에 있지 않아야 합니다.

이 네트워크 세그먼트는 주로 초기 배포 중 테스트 목적으로 사용됩니다.

>[!NOTE]
>배포 중에는 이 네트워크가 필요하지 않습니다. 배포 후 단계로 생성됩니다.
  
**예:** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="확인 - 가상 머신 워크로드에 대한 IP 주소 세그먼트" border="false":::     

## <a name="attach-azure-virtual-network-to-azure-vmware-solution"></a>Azure VMware Solution에 Azure Virtual Network 연결

Azure VMware Solution에 대한 연결을 제공하기 위해 Azure VMware Solution 프라이빗 클라우드에서 ExpressRoute 가상 네트워크 게이트웨이로 ExpressRoute가 빌드됩니다.

*기존* 또는 *새로운* ExpressRoute 가상 네트워크 게이트웨이를 사용할 수 있습니다.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="확인 - Azure VMware Solution을 연결하기 위한 Azure Virtual Network" border="false":::

### <a name="use-an-existing-expressroute-virtual-network-gateway"></a>기존 ExpressRoute 가상 네트워크 게이트웨이 사용

*기존* ExpressRoute 가상 네트워크 게이트웨이를 사용하려는 경우 Azure VMware Solution ExpressRoute 회로가 배포 후 단계로 설정됩니다. 이 경우 **Virtual Network** 필드를 비워 둡니다.

일반적으로 기존 ExpressRoute 가상 네트워크 게이트웨이를 사용하는 것이 좋습니다. 계획을 수립하기 위해 사용할 ExpressRoute 가상 네트워크 게이트웨이를 기록한 후, [다음 단계](#vmware-hcx-network-segments)를 계속 진행합니다.

### <a name="create-a-new-expressroute-virtual-network-gateway"></a>새로운 ExpressRoute 가상 네트워크 게이트웨이 만들기

*새로운* ExpressRoute 가상 네트워크 게이트웨이를 만드는 경우 기존 Azure Virtual Network를 사용하거나 새로 만들 수 있습니다.  

- 기존 Azure Virtual Network의 경우:
   1. 기존 ExpressRoute 가상 네트워크 게이트웨이가 없는 Azure Virtual Network를 식별합니다.
   2. 배포하기 전에 Azure Virtual Network에서 [GatewaySubnet](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet)을 만듭니다.

- 새 Azure Virtual Network 및 가상 네트워크 게이트웨이의 경우 **Virtual Network** 목록에서 **새로 만들기** 링크를 선택하여 배포 중에 이를 만듭니다.  배포 전에 주소 공간과 서브넷을 정의하는 것이 중요하므로 배포 단계를 완료할 때 해당 정보를 입력할 준비가 된 것입니다.

다음 이미지는 **Virtual Network** 필드가 강조 표시된 **프라이빗 클라우드 만들기** 배포 화면을 보여줍니다.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-deployment-screen-vnet-circle.png" alt-text="Virtual Network 필드가 강조 표시된 Azure VMware Solution 배포 화면의 스크린샷":::

> [!NOTE]
> 앞으로 사용하거나 만들 가상 네트워크는 온-프레미스 환경 및 Azure VMware Solution에서 표시되므로 이 가상 네트워크와 서브넷에서 사용하는 IP 세그먼트가 겹치지 않도록 해야 합니다.

## <a name="vmware-hcx-network-segments"></a>VMware HCX 네트워크 세그먼트

VMware HCX는 Azure VMware Solution과 함께 제공되는 기술입니다. VMware HCX에 대한 주요 사용 사례는 워크로드 마이그레이션 및 재해 복구입니다. 둘 중 하나를 수행하려는 경우 지금 네트워킹을 계획하는 것이 가장 좋습니다. 그렇지 않은 경우 이 계획을 건너뛰고 다음 단계로 계속 진행할 수 있습니다.

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="optional-extend-your-networks"></a>(선택 사항) 네트워크 확장

네트워크 세그먼트는 온-프레미스에서 Azure VMware Solution으로 확장할 수 있습니다. 네트워크 세그먼트를 확장한 경우 지금 해당 네트워크를 식별합니다.  

고려해야 할 몇 가지 요인은 다음과 같습니다.

- 온-프레미스에서 네트워크를 확장하려는 경우 해당 네트워크에서 온-프레미스 VMware 환경의 [vDS(vSphere 분산 스위치)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html)에 연결해야 합니다.  
- [vSphere 표준 스위치](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html)에 있는 네트워크는 확장할 수 없습니다.

>[!NOTE]
>이러한 네트워크는 배포 중이 아니라 구성의 마지막 단계로 확장됩니다.
>
## <a name="next-steps"></a>다음 단계
이제 필요한 정보를 수집하고 문서화했으므로 다음 섹션으로 계속 진행하여 Azure VMware Solution 프라이빗 클라우드를 만듭니다.

> [!div class="nextstepaction"]
> [Azure VMware Solution 배포](deploy-azure-vmware-solution.md)
> 
