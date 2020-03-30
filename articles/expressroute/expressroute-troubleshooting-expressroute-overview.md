---
title: 'Azure ExpressRoute: 연결 확인 - 문제 해결 가이드'
description: ExpressRoute 회로에 대한 종단 간 연결의 유효성 검사 및 문제 해결에 대한 지침을 제공합니다.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 10/31/2019
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: 58ae39e8dfdf918ae14ca9bb8dac28405828999e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330960"
---
# <a name="verifying-expressroute-connectivity"></a>ExpressRoute 연결 확인
이 문서는 ExpressRoute 연결 문제를 확인하고 해결하는 데 도움을 줍니다. ExpressRoute는 일반적으로 연결 공급자에 의해 촉진 되는 개인 연결을 통해 Microsoft 클라우드에 온-프레미스 네트워크를 확장 합니다. ExpressRoute 연결에는 일반적으로 다음과 같이 세 개의 서로 다른 네트워크 영역이 포함됩니다.

-   고객 네트워크
-   공급자 네트워크
-   Microsoft 데이터 센터

> [!NOTE]
> ExpressRoute 직접 연결 모델(10/100Gbps 대역폭으로 제공)에서 고객은 MSEE(Microsoft 엔터프라이즈 Edge) 라우터 포트에 직접 연결할 수 있습니다. 따라서 직접 연결 모델에는 고객 및 Microsoft 네트워크 영역만 있습니다.
>


이 문서의 목적은 사용자가 연결 문제가 있는지 및 위치를 식별할 수 있도록 하는 것입니다. 따라서 문제를 해결하기 위해 해당 팀의 지원을 요청할 수 있습니다. 문제를 해결하기 위해 지원이 필요하면 [Microsoft 지원][Support]에서 지원 티켓을 열어주세요.

> [!IMPORTANT]
> 이 문서는 간단한 문제를 진단하고 수정하는 데 사용됩니다. 이 문서는 Microsoft 기술 지원 서비스를 대체하지 않습니다. 제공된 지침을 사용하여 문제를 해결할 수 없으면 [Microsoft 지원][Support]에서 지원 티켓을 열어주세요.
>
>

## <a name="overview"></a>개요
다음 다이어그램에서는 ExpressRoute를 통한 고객 네트워크와 Microsoft 네트워크 간의 논리적 연결을 보여 줍니다.
[![1]][1]

위의 다이어그램에서 숫자는 주요 네트워크 지점을 나타냅니다. 이러한 네트워크 지점은 이 문서에서 관련 번호로 참조됩니다. ExpressRoute 연결 모델-클라우드 Exchange 공동 위치, 지점 간 이더넷 연결 또는 임의(IPVPN)에 따라 네트워크 포인트 3및 4는 스위치(계층 2 장치) 또는 라우터(계층 3 장치)일 수 있습니다. 직접 연결 모델에는 네트워크 포인트 3과 4가 없습니다. 대신 CE(2)는 어두운 섬유를 통해 MSEE에 직접 연결됩니다. 위의 다이어그램에서 보여 주는 주요 네트워크 지점은 다음과 같습니다.

1.  고객 컴퓨팅 디바이스(예: 서버 또는 PC)
2.  CE: 고객 에지 라우터 
3.  PE(CE 연결): 고객 에지 라우터에 연결되는 공급자 에지 라우터/스위치입니다. 이 문서에서는 PE-CE라고 합니다.
4.  PE(MSEE 연결): MSEE에 연결되는 공급자 에지 라우터/스위치입니다. 이 문서에서는 PE-MSEE라고 합니다.
5.  MSEE: MSEE(Microsoft Enterprise Edge) ExpressRoute 라우터
6.  VNet(Virtual Network) 게이트웨이
7.  Azure VNet의 컴퓨팅 디바이스

클라우드 Exchange 공동 위치, 지점 간 이더넷 또는 직접 연결 모델이 사용되는 경우 CE(2)는 MSEE(5)를 사용하여 BGP 피어링을 설정합니다. 

IPVPN(임의)연결 모델을 사용하는 경우 PE-MSEE(4)는 MSEE(5)를 사용하여 BGP 피어링을 설정합니다. PE-MSEE는 IPVPN 서비스 공급자 네트워크를 통해 Microsoft에서 받은 경로를 고객 네트워크로 다시 전파합니다.

> [!NOTE]
>고가용성을 위해 Microsoft는 MSEE(5)와 PE-MSEE(4) 쌍 간에 완전히 중복된 병렬 연결을 설정합니다. 고객 네트워크와 PE-CEs 쌍 간에도 완전히 중복된 병렬 네트워크 경로가 권장됩니다. 고가용성에 대한 자세한 내용은 [ExpressRoute를 통해 고가용성을 위한 설계][HA] 문서를 참조하십시오.
>
>

다음은 ExpressRoute 회로 문제 해결의 논리적 단계입니다.

* [회로 프로비저닝 및 상태 확인](#verify-circuit-provisioning-and-state)
  
* [피어링 구성 유효성 검사](#validate-peering-configuration)
  
* [ARP 유효성 검사](#validate-arp)
  
* [MSEE에서 BGP 및 경로 유효성 검사](#validate-bgp-and-routes-on-the-msee)
  
* [트래픽 흐름 확인](#confirm-the-traffic-flow)


## <a name="verify-circuit-provisioning-and-state"></a>회로 프로비저닝 및 상태 확인
ExpressRoute 회로를 프로비전하는 것은 CE/PE-MSEE(2)/(4)와 MSEE(5) 사이의 중복 계층 2 연결을 설정합니다. ExpressRoute 회로 만들기, 수정, 프로비전 및 확인 방법에 대한 자세한 내용은 [ExpressRoute 회로 만들기 및 수정][CreateCircuit] 문서를 참조하세요.

>[!TIP]
>서비스 키는 ExpressRoute 회로를 고유하게 식별합니다. ExpressRoute 문제를 해결하기 위해 Microsoft 또는 ExpressRoute 파트너의 도움이 필요한 경우 회로를 쉽게 식별할 수 있는 서비스 키를 제공하십시오.
>
>

### <a name="verification-via-the-azure-portal"></a>Azure Portal을 통한 확인
Azure 포털에서 ExpressRoute 회로 블레이드를 엽니다. 블레이드의 ![3개][3] 섹션에서 ExpressRoute 필수 사항은 다음 스크린샷과 같이 나열됩니다.

![4][4]    

ExpressRoute Essentials에서 *회로 상태*는 Microsoft 쪽 회로 상태를 나타냅니다. *공급자 상태*는 서비스 공급자 쪽에서 회로가 *프로비전됨/프로비전되지 않음*인지를 나타냅니다. 

ExpressRoute 회로가 작동하려면 *회로 상태*가 *사용*이고, *공급자 상태*가 *프로비전됨*이어야 합니다.

> [!NOTE]
> ExpressRoute 회로를 구성한 후 *회로 상태가* 활성화되지 않은 상태로 설정된 경우 [Microsoft 지원에][Support]문의하십시오. 반면에 *공급자 상태가* 프로비저닝되지 않은 상태로 발생한 경우 서비스 공급자에게 문의하십시오.
>
>

### <a name="verification-via-powershell"></a>PowerShell을 통한 확인
리소스 그룹의 모든 ExpressRoute 회로를 나열하려면 다음 명령을 사용합니다.

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>리소스 그룹의 이름을 찾고 있는 경우 *Get-AzResourceGroup* 명령을 사용하여 구독의 모든 리소스 그룹을 나열하여 리소스 그룹을 얻을 수 있습니다.
>


[리소스 그룹]에서 특정 ExpressRoute 회로를 선택하려면 다음 명령을 사용합니다.

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

샘플 응답은 다음과 같습니다.

    Name                             : Test-ER-Ckt
    ResourceGroupName                : Test-ER-RG
    Location                         : westus2
    Id                               : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                        "Name": "Standard_UnlimitedData",
                                        "Tier": "Standard",
                                        "Family": "UnlimitedData"
                                        }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             : 
    ServiceProviderProperties        : {
                                        "ServiceProviderName": "****",
                                        "PeeringLocation": "******",
                                        "BandwidthInMbps": 100
                                        }
    ServiceKey                       : **************************************
    Peerings                         : []
    Authorizations                   : []

ExpressRoute 회로가 작동하는지 확인하려면 다음 필드에 특히 주의하세요.

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

> [!NOTE]
> ExpressRoute 회로를 구성한 후 *회로 상태가* 활성화되지 않은 상태로 설정된 경우 [Microsoft 지원에][Support]문의하십시오. 반면에 *공급자 상태가* 프로비저닝되지 않은 상태로 발생한 경우 서비스 공급자에게 문의하십시오.
>
>

## <a name="validate-peering-configuration"></a>피어링 구성 유효성 검사
서비스 공급자가 ExpressRoute 회로 프로비저닝을 완료한 후 CE/MSEE-PE(2)/(4)와 MSEE(5) 사이의 ExpressRoute 회로를 통해 여러 eBGP 기반 라우팅 구성을 생성할 수 있습니다. 각 ExpressRoute 회로에는 Azure 개인 피어링(Azure의 개인 가상 네트워크에 대한 트래픽) 및/또는 Microsoft 피어링(PaaS 및 SaaS의 공용 끝점으로의 트래픽)이 있을 수 있습니다. 라우팅 구성을 만들고 수정하는 방법에 대한 자세한 내용은 [ExpressRoute 회로의 라우팅 만들기 및 수정][CreatePeering] 문서를 참조하세요.

### <a name="verification-via-the-azure-portal"></a>Azure Portal을 통한 확인

> [!NOTE]
> IPVPN 연결 모델에서 서비스 공급자는 피어링(계층 3 서비스)을 구성하는 책임을 처리합니다. 이러한 모델에서 서비스 공급자가 피어링을 구성하고 포털에서 피어링이 비어 있는 경우 포털의 새로 고침 단추를 사용하여 회로 구성을 새로 고쳐 보십시오. 이 작업은 회로에서 현재 라우팅 구성을 가져옵니다. 
>

Azure 포털에서 ExpressRoute 회로 피어링의 상태를 ExpressRoute 회로 블레이드에서 확인할 수 있습니다. 블레이드의 ![3개][3] 섹션에서 ExpressRoute 피어링은 다음 스크린샷과 같이 나열됩니다.

![5][5]

앞의 예제에서는 언급한 Azure 개인 피어링이 프로비전되지만 Azure 공용 및 Microsoft 피어링은 프로비전되지 않습니다. 성공적으로 프로비저닝된 피어링 컨텍스트에는 기본 및 보조 지점 간 서브넷도 나열됩니다. /30 서브넷은 MSEE 및 CE/PE-MSE의 인터페이스 IP 주소에 사용됩니다. 프로비전된 피어링의 경우 목록은 구성을 마지막으로 수정한 사용자도 나타냅니다. 

> [!NOTE]
> 피어링 사용이 실패하는 경우 할당된 기본 서브넷과 연결된 CE/PE-MSEE의 구성과 일치하는지 확인합니다. 또한 올바른 *VlanId,* *AzureASN*및 *PeerASN이* MSEE에서 사용되는지 확인하고 이러한 값이 연결된 CE/PE-MSEE에 사용된 값과 매핑되는지 확인합니다. MD5 해시를 선택한 경우 공유 키는 MSEE 및 PE-MSEE/CE 쌍에서 동일해야 합니다. 보안상의 이유로 이전에 구성된 공유 키가 표시되지 않습니다. MSEE 라우터에서 이러한 구성을 변경해야 하는 경우 [ExpressRoute 회로의 라우팅 만들기 및 수정을][CreatePeering]참조하십시오.  
>

> [!NOTE]
> 인터페이스에 할당된 /30 서브넷에서 Microsoft는 MSEE 인터페이스에 대한 서브넷의 두 번째 사용 가능한 IP 주소를 선택합니다. 따라서 서브넷의 첫 번째 사용 가능한 IP 주소가 피어있는 CE/PE-MSEE에 할당되었는지 확인합니다.
>


### <a name="verification-via-powershell"></a>PowerShell을 통한 확인
Azure 프라이빗 피어링 구성 세부 정보를 가져오려면 다음 명령을 사용합니다.

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

성공적으로 구성된 프라이빗 피어링에 대한 샘플 응답은 다음과 같습니다.

    Name                       : AzurePrivatePeering
    Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
    Etag                       : W/"################################"
    PeeringType                : AzurePrivatePeering
    AzureASN                   : 12076
    PeerASN                    : 123##
    PrimaryPeerAddressPrefix   : 172.16.0.0/30
    SecondaryPeerAddressPrefix : 172.16.0.4/30
    PrimaryAzurePort           : 
    SecondaryAzurePort         : 
    SharedKey                  : 
    VlanId                     : 200
    MicrosoftPeeringConfig     : null
    ProvisioningState          : Succeeded

 성공적으로 사용되는 피어링 컨텍스트에는 기본 및 보조 주소 접두사가 나열되어 있습니다. /30 서브넷은 MSEE 및 CE/PE-MSE의 인터페이스 IP 주소에 사용됩니다.

Azure 공용 피어링 구성 세부 정보를 가져오려면 다음 명령을 사용합니다.

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

Microsoft 피어링 구성 세부 정보를 가져오려면 다음 명령을 사용합니다.

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

피어링이 구성되어 있지 않으면 오류 메시지가 표시됩니다. 명시된 피어링(이 예제에서는 Azure 공용 피어링)이 회로 내에 구성되지 않은 경우 샘플 응답은 다음과 같습니다.

    Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


> [!NOTE]
> 피어링 사용이 실패하는 경우 할당된 기본 서브넷과 연결된 CE/PE-MSEE의 구성과 일치하는지 확인합니다. 또한 올바른 *VlanId,* *AzureASN*및 *PeerASN이* MSEE에서 사용되는지 확인하고 이러한 값이 연결된 CE/PE-MSEE에 사용된 값과 매핑되는지 확인합니다. MD5 해시를 선택한 경우 공유 키는 MSEE 및 PE-MSEE/CE 쌍에서 동일해야 합니다. 보안상의 이유로 이전에 구성된 공유 키가 표시되지 않습니다. MSEE 라우터에서 이러한 구성을 변경해야 하는 경우 [ExpressRoute 회로의 라우팅 만들기 및 수정을][CreatePeering]참조하십시오.  
>
>

> [!NOTE]
> 인터페이스에 할당된 /30 서브넷에서 Microsoft는 MSEE 인터페이스에 대한 서브넷의 두 번째 사용 가능한 IP 주소를 선택합니다. 따라서 서브넷의 첫 번째 사용 가능한 IP 주소가 피어있는 CE/PE-MSEE에 할당되었는지 확인합니다.
>

## <a name="validate-arp"></a>ARP 유효성 검사

ARP 테이블은 특정 피어링에 대한 IP 주소 및 MAC 주소의 매핑을 제공합니다. ExpressRoute 회로 피어링의 ARP 테이블은 각 인터페이스(기본 및 보조)에 대한 다음 정보를 제공합니다.
* 온-프레미스 라우터 인터페이스 IP 주소를 MAC 주소에 매핑
* ExpressRoute 라우터 인터페이스 IP 주소를 MAC 주소에 매핑
* 매핑 ARP 테이블의 나이는 계층 2 구성의 유효성을 검사하고 기본 계층 2 연결 문제를 해결하는 데 도움이 될 수 있습니다.


[ResourceRoute 피어링의][ARP] ARP 테이블을 보는 방법 및 정보를 사용하여 계층 2 연결 문제를 해결하는 방법에 대한 리소스 관리자 배포 모델 문서의 ARP 테이블 받기를 참조하십시오.


## <a name="validate-bgp-and-routes-on-the-msee"></a>MSEE에서 BGP 및 경로 유효성 검사

*개인* 라우팅 컨텍스트에 대한 *기본* 경로의 MSEE에서 라우팅 테이블을 얻으려면 다음 명령을 사용합니다.

    Get-AzExpressRouteCircuitRouteTable -DevicePath Primary -ExpressRouteCircuitName ******* -PeeringType AzurePrivatePeering -ResourceGroupName ****

예제 응답은 다음과 같습니다.

    Network : 10.1.0.0/16
    NextHop : 10.17.17.141
    LocPrf  : 
    Weight  : 0
    Path    : 65515

    Network : 10.1.0.0/16
    NextHop : 10.17.17.140*
    LocPrf  : 
    Weight  : 0
    Path    : 65515

    Network : 10.2.20.0/25
    NextHop : 172.16.0.1
    LocPrf  : 
    Weight  : 0
    Path    : 123##


> [!NOTE]
> MSEE와 CE/PE-MSEE 간의 eBGP 피어링 상태가 활성 또는 유휴 상태인 경우 할당된 기본 및 보조 피어 서브넷이 연결된 CE/PE-MSEE의 구성과 일치하는지 확인합니다. 또한 올바른 *VlanId,* *AzureAsn*및 *PeerAsn이* MSEE에 사용되는지 확인하고 이러한 값이 연결된 PE-MSEE/CE에 사용되는 값과 매핑되는지 확인합니다. MD5 해시를 선택한 경우 공유 키는 MSEE 및 CE/PE-MSEE 쌍에서 동일해야 합니다. MSEE 라우터에서 이러한 구성을 변경해야 하는 경우 [ExpressRoute 회로의 라우팅 만들기 및 수정을][CreatePeering]참조하십시오.
>


> [!NOTE]
> 피어링을 통해 특정 대상에 연결할 수 없는 경우 해당 피어링 컨텍스트에 대한 MSEE의 경로 테이블을 확인합니다. 라우팅 테이블에 일치하는 접두사(NATed IP일 수 있음)가 있는 경우 트래픽을 차단하는 경로에 방화벽/NSG/ACL이 있는지 확인합니다.
>


다음 예제에서는 존재하지 않는 피어링에 대한 명령의 응답을 보여 주십니까?

    Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
    StatusCode: 400

## <a name="confirm-the-traffic-flow"></a>트래픽 흐름 확인
피어링 컨텍스트의 기본 및 보조 경로 트래픽 통계(송/수신 바이트 수)를 가져오려면 다음 명령을 사용합니다.

    Get-AzExpressRouteCircuitStats -ResourceGroupName $RG -ExpressRouteCircuitName $CircuitName -PeeringType 'AzurePrivatePeering'

명령 출력의 예제는 다음과 같습니다.

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

존재하지 않는 피어링에 대한 명령 출력의 예제는 다음과 같습니다.

    Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
    StatusCode: 400

## <a name="next-steps"></a>다음 단계
자세한 정보 또는 도움말을 보려면 다음 링크를 확인하세요.

- [Microsoft 지원][Support]
- [익스프레스루트 회로 생성 및 수정][CreateCircuit]
- [ExpressRoute 회로의 라우팅 만들기 및 수정][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "논리 ExpressRoute 연결"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "모든 리소스 아이콘"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "개요 아이콘"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "ExpressRoute Essentials 샘플 스크린샷"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "ExpressRoute Essentials 샘플 스크린샷"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[DR-Pvt]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering





