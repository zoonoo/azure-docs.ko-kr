---
title: Azure VMware Solution 배포 계획
description: 이 문서에서는 Azure VMware Solution 배포 워크플로를 간략하게 설명합니다.  결국에는 VM(가상 머신)을 만들고 마이그레이션할 수 있는 환경이 준비됩니다.
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: e30692f26af786097f3cdb81690be617bfea0c79
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517364"
---
# <a name="planning-the-azure-vmware-solution-deployment"></a>Azure VMware Solution 배포 계획

이 문서에서는 배포 중에 사용되는 데이터를 식별하고 수집하기 위한 계획 프로세스를 제공합니다. 배포를 계획할 때 배포 중에 쉽게 참조할 수 있도록 수집한 정보를 문서화해야 합니다.

이 빠른 시작 프로세스는 VM(가상 머신)을 만들고 마이그레이션을 수행할 수 있는 프로덕션 준비 환경을 만듭니다. 

>[!IMPORTANT]
>Azure VMware Solution 리소스를 만들기 전에 [Azure VMware Solution 리소스를 사용하도록 설정하는 방법](enable-azure-vmware-solution.md) 문서에 따라 노드가 할당되도록 지원 티켓을 제출합니다. 지원 팀에서 요청을 받으면 요청을 확인하고 노드를 할당하는 데 최대 5 영업일이 걸립니다. 기존 Azure VMware Solution 프라이빗 클라우드가 있고 더 많은 노드를 할당하려는 경우에도 동일한 프로세스를 진행합니다. 


## <a name="subscription"></a>Subscription

Azure VMware Solution을 배포하는 데 사용할 구독을 확인합니다.  새 구독을 만들거나 기존 구독을 다시 사용할 수 있습니다.

>[!NOTE]
>구독은 Microsoft 기업계약과 연결되어야 합니다.

## <a name="resource-group"></a>Resource group

Azure VMware Solution에 사용하려는 리소스 그룹을 확인합니다.  일반적으로 리소스 그룹은 특별히 Azure VMware Solution용으로 만들어지지만, 기존 리소스 그룹을 사용할 수 있습니다.

## <a name="region"></a>지역

Azure VMware Solution을 배포하려는 지역을 확인합니다.  자세한 내용은 [지역별 사용 가능한 Azure 제품 가이드](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=azure-vmware)를 참조하세요.

## <a name="resource-name"></a>리소스 이름

배포 중에 사용할 리소스 이름을 정의합니다.  리소스 이름은 Azure VMware Solution 프라이빗 클라우드의 제목으로 지정되는 친숙하고 설명이 포함된 이름입니다.

## <a name="size-nodes"></a>크기 노드

Azure VMware Solution을 배포할 때 사용하려는 크기 노드를 확인합니다.  전체 목록은 [Azure VMware Solution 프라이빗 클라우드 및 클러스터](concepts-private-clouds-clusters.md#hosts) 설명서를 참조하세요.

## <a name="number-of-hosts"></a>호스트 수

Azure VMware Solution 프라이빗 클라우드에 배포하려는 호스트 수를 정의합니다.  최소 노드 수는 3개이고, 최대 노드 수는 클러스터당 16개입니다.  자세한 내용은 [Azure VMware Solution 프라이빗 클라우드 및 클러스터](concepts-private-clouds-clusters.md#clusters) 설명서를 참조하세요.

초기 배포 수를 초과해야 하는 경우 나중에 언제든지 클러스터를 확장할 수 있습니다.

## <a name="vcenter-admin-password"></a>vCenter 관리자 암호
vCenter 관리자 암호를 정의합니다.  배포 중에 vCenter 관리자 암호를 만듭니다. 암호는 vCenter 빌드 중에 cloudadmin@vsphere.local 관리자 계정에 대해 지정됩니다. 이는 vCenter에 로그인하는 데 사용됩니다.

## <a name="nsx-t-admin-password"></a>NSX-T 관리자 암호
NSX-T 관리자 암호를 정의합니다.  배포 중에 NSX-T 관리자 암호를 만듭니다. 암호는 NSX 빌드 중에 NSX 계정의 관리 사용자에게 할당됩니다. 이는 NSX-T Manager에 로그인하는 데 사용됩니다.

## <a name="ip-address-segment"></a>IP 주소 세그먼트

배포를 계획하는 첫 번째 단계는 IP 조각화를 계획하는 것입니다.  Azure VMware Solution은 사용자가 제공하는 /22 네트워크를 수집합니다. 그런 다음, 더 작은 세그먼트로 분할하고, 해당 IP 세그먼트를 vCenter, VMware HCX, NSX-T 및 vMotion에 사용합니다.

Azure VMware Solution은 내부 ExpressRoute 회로를 통해 Microsoft Azure Virtual Network에 연결합니다. 대부분의 경우 ExpressRoute Global Reach를 통해 데이터 센터에 연결합니다. 

Azure VMware Solution, 기존 Azure 환경 및 온-프레미스 환경은 모두 경로를 교환합니다(일반적으로). 이 경우 이 단계에서 정의하는 /22 CIDR 네트워크 주소 블록은 온-프레미스 또는 Azure가 이미 있는 블록과 겹치지 않아야 합니다.

**예:** 10.0.0.0/22

자세한 내용은 [네트워크 계획 검사 목록](tutorial-network-checklist.md#routing-and-subnet-considerations)을 참조하세요.

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="확인 - IP 주소 세그먼트" border="false":::  

## <a name="ip-address-segment-for-virtual-machine-workloads"></a>가상 머신 워크로드에 대한 IP 주소 세그먼트

IP 세그먼트를 확인하여 첫 번째 네트워크(NSX 세그먼트)를 프라이빗 클라우드에 만듭니다.  즉, VM을 Azure VMware Solution에 배포할 수 있도록 네트워크 세그먼트를 Azure VMware Solution에 만들려고 합니다.   

L2 네트워크만 확장하려고 계획하는 경우에도 환경의 유효성을 검사할 네트워크 세그먼트를 만듭니다.

만든 모든 IP 세그먼트는 Azure 및 온-프레미스 공간에서 고유해야 합니다.  

**예:** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="확인 - 가상 머신 워크로드에 대한 IP 주소 세그먼트" border="false":::     

## <a name="optional-extend-networks"></a>(선택 사항) 네트워크 확장

네트워크 세그먼트는 온-프레미스에서 Azure VMware Solution으로 확장할 수 있습니다. 이렇게 하는 경우 지금 해당 네트워크를 확인합니다.  

다음 사항에 유의하세요.

- 온-프레미스에서 네트워크를 확장하려는 경우 해당 네트워크에서 온-프레미스 VMware 환경의 [vDS(vSphere 분산 스위치)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html)에 연결해야 합니다.  
- [vSphere 표준 스위치](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html)에서 라이브로 확장하려는 네트워크인 경우 확장할 수 없습니다.

## <a name="expressroute-global-reach-peering-network"></a>ExpressRoute Global Reach 피어링 네트워크

ExpressRoute Global Reach 피어링에 필요한 `/29` CIDR 네트워크 주소 블록을 식별합니다. 만든 모든 IP 세그먼트는 Azure VMware Solution 및 온-프레미스 공간에서 고유해야 합니다. 이 세그먼트의 IP는 ExpressRoute Global Reach 연결의 각 끝에서 사용되어 Azure VMware Solution ExpressRoute 회로를 온-프레미스 ExpressRoute 회로와 연결합니다. 

**예:** 10.1.0.0/29

:::image type="content" source="media/pre-deployment/expressroute-global-reach-ip-diagram.png" alt-text="확인 - ExpressRoute Global Reach 피어링 네트워크" border="false":::

## <a name="azure-virtual-network-to-attach-azure-vmware-solution"></a>Azure VMware Solution을 연결하기 위한 Azure Virtual Network

Azure VMware Solution 프라이빗 클라우드에 액세스하려면 Azure VMware Solution과 함께 제공되는 ExpressRoute 회로를 Azure Virtual Network에 연결해야 합니다.  배포 중에 새 가상 네트워크를 정의하거나 기존 가상 네트워크를 선택할 수 있습니다.

Azure VMware Solution의 ExpressRoute 회로는 이 단계에서 정의하는 Azure Virtual Network의 ExpressRoute 게이트웨이에 연결합니다.  

>[!IMPORTANT]
>가상 네트워크당 4개의 ExpressRoute 회로 제한을 초과하지 않는 한 기존 ExpressRoute 게이트웨이를 사용하여 Azure VMware Solution에 연결할 수 있습니다.  그러나 ExpressRoute를 통해 온-프레미스에서 Azure VMware Solution에 액세스하려면 ExpressRoute 게이트웨이가 연결된 회로 간에 전이적 라우팅을 제공하지 않으므로 ExpressRoute Global Reach가 있어야 합니다.  

ExpressRoute 회로를 Azure VMware Solution에서 기존 ExpressRoute 게이트웨이로 연결하려는 경우 배포 후에 이 작업을 수행할 수 있습니다.  

이제 요약하여, Azure VMware Solution을 기존 ExpressRoute 게이트웨이에 연결하려고 하나요?  

* **예** = 배포 중에 사용되지 않는 가상 네트워크를 확인합니다.
* **아니요** = 배포 중에 기존 가상 네트워크를 확인하거나 새 가상 네트워크를 만듭니다.

어느 방법이든 이 단계에서 수행하려는 작업을 문서화합니다.

>[!NOTE]
>이 가상 네트워크는 온-프레미스 환경 및 Azure VMware Solution에서 표시되므로 이 가상 네트워크와 서브넷에서 사용하는 IP 세그먼트가 겹치지 않도록 해야 합니다.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="확인 - Azure VMware Solution을 연결하기 위한 Azure Virtual Network" border="false":::

## <a name="vmware-hcx-network-segments"></a>VMware HCX 네트워크 세그먼트

VMware HCX는 Azure VMware Solution과 함께 번들되는 기술입니다. VMware HCX에 대한 주요 사용 사례는 워크로드 마이그레이션 및 재해 복구입니다. 둘 중 하나를 수행하려는 경우 지금 네트워킹을 계획하는 것이 가장 좋습니다.   그렇지 않은 경우 이 계획을 건너뛰고 다음 단계로 계속 진행할 수 있습니다.

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="next-steps"></a>다음 단계
이제 필요한 정보를 수집하고 문서화했으므로 다음 섹션으로 계속 진행하여 Azure VMware Solution 프라이빗 클라우드를 만듭니다.

> [!div class="nextstepaction"]
> [Azure VMware Solution 배포](deploy-azure-vmware-solution.md)
