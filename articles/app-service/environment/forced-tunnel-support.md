---
title: "Azure App Service Environment가 강제 터널링되도록 구성"
description: "아웃바운드 트래픽이 강제 터널링될 때 ASE가 작동하도록 설정"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: ccompy
ms.openlocfilehash: cd89bd23074dec1de6fa0e8784d42a5c539938b1
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2017
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>강제 터널링으로 App Service Environment 구성

ASE(App Service Environment)는 고객의 Azure VNet(가상 네트워크)에 Azure App Service를 배포한 것입니다. 많은 고객이 VPN 또는 ExpressRoute 연결을 통해 자사의 VNet이 온-프레미스 네트워크의 확장이 되도록 구성합니다. 회사 정책이나 기타 보안 제약 조건으로 인해 모든 아웃 바운드 트래픽은 인터넷으로 나가기 전에 온-프레미스로 보내지도록 경로를 구성합니다. VNet의 아웃바운드 트래픽이 VPN이나 ExpressRoute 연결을 통해 온-프레미스로 흐르도록 VNet의 라우팅을 변경하는 것을 강제 터널링이라고 합니다.  

강제 터널링으로 인해 ASE에 문제가 발생할 수 있습니다. ASE에는 다수의 외부 종속성이 있으며, [ASE 네트워크 아키텍처][network] 문서에 열거되어 있습니다. 기본적으로 ASE에서는 모든 아웃바운드 통신이 ASE로 프로비전된 VIP를 통과해야 합니다.

경로는 강제 터널링이 무엇인지, 어떻게 처리하는지에 대한 중요한 측면입니다. Azure Virtual Network에서 라우팅은 LPM(Longest Prefix Match)을 기반으로 수행됩니다.  LPM 일치가 동일한 경로가 두 개 이상 있으면 다음 순서대로 해당 원점에 따라 경로가 선택됩니다.

1. 사용자 정의 경로
1. BGP 경로(ExpressRoute를 사용하는 경우)
1. 시스템 경로

VNet의 라우팅에 대한 자세한 내용은 [사용자 정의된 경로 및 IP 전달][routes]을 참조하세요. 

ASE를 강제 터널 VNet에서 작동하려면 두 가지 옵션이 있습니다.

1. ASE가 인터넷에 직접 액세스하도록 설정
1. ASE의 송신 끝점 변경

## <a name="enable-your-ase-to-have-direct-internet-access"></a>ASE가 인터넷에 직접 액세스하도록 설정

VNet이 ExpressRoute로 구성된 상태로 ASE가 작동하도록 하려면:

* 0.0.0.0/0을 보급하도록 ExpressRoute를 구성합니다. 기본적으로 ExpressRoute는 온-프레미스의 모든 아웃바운드 트래픽을 강제로 터널링합니다.
* UDR를 만듭니다. 그리고 주소 접두사가 0.0.0.0/0이며 다음 홉 형식이 인터넷인 ASE가 포함된 서브넷에 UDR을 적용합니다.

이러한 두 가지 사항을 변경하면 ASE 서브넷에서 발생하는 인터넷용 트래픽이 ExpressRoute로 강제 전송되지 않으며 ASE가 정상적으로 작동합니다.

> [!IMPORTANT]
> UDR에 정의된 경로는 ExpressRoute 구성을 통해 보급된 경로보다 우선하도록 충분히 구체적이어야 합니다. 이전 예제에서는 광범위한 0.0.0.0/0 주소 범위를 사용합니다. 따라서 더 구체적인 주소 범위를 사용하는 경로 보급 알림으로 인해 주소 범위가 잘못 재정의될 가능성이 있습니다.
>
> 공용 피어링 경로에서 개인 피어링 경로로 경로의 교차 보급을 수행하는 ExpressRoute 구성에서는 ASE가 지원되지 않습니다. 공용 피어링이 구성된 ExpressRoute 구성은 Microsoft에서 경로 보급 알림을 받습니다. 보급 알림에는 대규모 Microsoft Azure IP 주소 범위 집합이 포함되어 있습니다. 개인 피어링 경로에서 주소 범위를 교차 보급하는 경우 ASE 서브넷의 모든 아웃바운드 네트워크 패킷이 고객의 온-프레미스 네트워크 인프라로 강제 터널링됩니다. 이 네트워크 흐름은 현재 ASE에서 기본적으로 지원되지 않습니다. 이 문제를 해결하려면 공용 피어링 경로에서 개인 피어링 경로로의 교차 보급 경로를 중지합니다.  다른 솔루션은 ASE가 강제 터널 구성에서 작동하도록 설정하는 것입니다.

## <a name="change-the-egress-endpoint-for-your-ase"></a>ASE의 송신 끝점 변경 ##

이 섹션에서는 ASE가 사용하는 송신 끝점을 변경하여 ASE가 강제 터널 구성에서 작동하도록 설정하는 방법을 설명합니다. ASE의 아웃바운드 트래픽이 온-프레미스 네트워크로 강제 터널링된 경우에는 ASE VIP 주소 이외의 IP 주소에서 해당 트래픽을 원본에 보낼 수 있도록 해야 합니다.

ASE는 외부 종속성을 가질 뿐만 아니라 ASE를 관리하기 위해 인바운드 트래픽을 수신 대기해야 합니다. ASE는 이러한 트래픽에 응답할 수 있어야 하며 다른 주소에서 응답을 보내면 TCP가 중단되므로 다른 주소에서는 응답을 다시 보낼 수 없습니다.  따라서 ASE의 송신 끝점을 변경하는 데 필요한 3단계가 있습니다.

1. 인바운드 관리 트래픽이 동일한 IP 주소에서 되돌아 갈 수 있도록 경로 테이블을 설정
1. ASE 방화벽으로 나가는 데 사용할 IP 주소를 추가
1. ASE의 아웃바운드 트래픽 경로가 터널링되도록 설정

![강제 터널 네트워크 흐름][1]

ASE가 이미 가동 중이거나 작동 중이면 다른 송신 주소로 ASE를 구성하거나 ASE 배포 중에 설정할 수 있습니다.  

### <a name="changing-the-egress-address-after-the-ase-is-operational"></a>ASE 작동 후 송신 주소 변경 ###
1. ASE의 송신 IP로 사용할 IP 주소를 가져옵니다. 강제 터널링을 수행하는 경우에는 NAT 또는 게이트웨이 IP가 됩니다.  ASE 아웃바운드 트래픽을 NVA를 통해 라우팅하려면 NVA의 공용 IP가 송신 주소가 됩니다.
2. ASE 구성 정보에서 송신 주소를 설정합니다. resource.azure.com에서 Subscription/<subscription id>/resourceGroups/<ase resource group>/providers/Microsoft.Web/hostingEnvironments/<ase name>로 이동하면 ASE를 설명하는 json을 볼 수 있습니다.  상단에 읽기/쓰기가 표시되는지 확인합니다.  편집을 클릭하고 맨 아래로 스크롤하여 userWhitelistedIpRanges를  

       "userWhitelistedIpRanges": null 
      
  에서 다음과 같이 변경합니다. 송신 주소 범위로 설정할 주소를 사용합니다. 

      "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"] 

  맨 위에서 PUT을 클릭합니다. 그러면 ASE에서 크기 조정 작업이 트리거되고 방화벽이 조정됩니다.
   
3. 경로 테이블을 만들거나 편집하고 ASE 위치에 매핑되는 관리 주소에 대한 액세스를 허용하는 규칙을 채웁니다.  관리 주소는 [App Service Environment 관리 주소][management]에 있습니다. 

4. ASE 서브넷에 적용되는 경로를 경로 테이블 또는 BGP 경로로 조정합니다.  

ASE가 포털에서 응답하지 않으면 변경 사항에 문제가 있는 것입니다.  송신 주소 목록이 불완전하거나 트래픽이 끊어졌거나 차단되었을 수 있습니다.  

### <a name="create-a-new-ase-with-a-different-egress-address"></a>다른 송신 주소로 새 ASE 만들기  ###

VNet이 모든 트래픽에 터널링을 강제하도록 이미 구성되어 있는 경우, ASE가 성공적으로 생성되도록 몇 가지 추가 단계를 수행해야 합니다. 즉, ASE 생성 중 다른 송신 끝점을 사용할 수 있도록 설정해야 합니다.  이렇게 하려면 허용되는 송신 주소를 지정하는 템플릿으로 ASE를 생성해야 합니다.

1. ASE의 송신 주소로 사용될 IP 주소를 가져옵니다.
1. ASE에서 사용할 서브넷을 미리 만듭니다. 경로를 설정하려면 필요하고 템플릿에도 필요하기 때문입니다.  
1. ASE 위치에 매핑되는 관리 IP로 경로 테이블을 생성하고 ASE에 할당합니다.
1. [템플릿으로 ASE 만들기][template]의 지침에 따라 적절한 템플릿을 끌어옵니다.
1. azuredeploy.json 파일 "resources" 섹션을 편집합니다. **userWhitelistedIpRanges** 줄에 다음 값을 포함시킵니다.

       "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]

올바르게 구성되면 ASE가 아무 문제없이 시작됩니다.  


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/forced-tunnel-flow.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
