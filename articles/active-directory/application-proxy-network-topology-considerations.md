---
title: "Azure Active Directory 응용 프로그램 프록시를 사용할 때 네트워크 토폴로지 고려 사항 | Microsoft Docs"
description: "Azure AD 응용 프로그램 프록시를 사용할 때 네트워크 토폴로지 고려 사항을 다룹니다."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 4b21bf6bc1be59facd503000a4f83a56189d55d3
ms.openlocfilehash: aea1b35348bec0affe2288ff683e0320e2b0f714
ms.lasthandoff: 02/28/2017


---

# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Azure Active Directory 응용 프로그램 프록시를 사용할 때 네트워크 토폴로지 고려 사항
> [!NOTE]
> 응용 프로그램 프록시는 Premium 또는 Basic 버전의 Azure Active Directory로 업그레이드하는 경우에만 사용할 수 있는 기능입니다. 자세한 내용은 [Azure Active Directory 버전](active-directory-editions.md)을 참조하세요.
>

이 문서에서는 응용 프로그램을 원격으로 게시 및 액세스하기 위해 Azure AD(Azure Active Directory) 응용 프로그램 프록시를 사용할 때 네트워크 토폴로지 고려 사항을 설명합니다.

## <a name="traffic-flow"></a>트래픽 흐름

Azure AD 응용 프로그램 프록시를 통해 응용 프로그램을 게시할 때 사용자에게서 대상 백 엔드 응용 프로그램으로 이동하는 모든 트래픽은 다음 홉을 통과합니다.

* 홉 1: 사용자-Azure에서 Azure AD 응용 프로그램 프록시 서비스의 공용 끝점
* 홉 2: 응용 프로그램 프록시 서비스-커넥터
* 홉 3: 커넥터-대상 응용 프로그램

 ![대상 응용 프로그램에 사용자의 트래픽 흐름을 보여 주는 다이어그램](./media/application-proxy-network-topologies/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>테넌트 위치 및 응용 프로그램 프록시 서비스

Azure AD 테넌트에 등록할 때 테넌트의 지역은 지정한 국가에 따라 결정됩니다. 응용 프로그램 프록시를 사용하도록 설정하면 사용자 테넌트의 응용 프로그램 프록시 서비스 인스턴스가 Azure AD 테넌트와 동일한 지역이나 가장 가까운 지역에 표시됩니다.

예를 들어, Azure AD 테넌트의 영역이 EU인 경우 모든 응용 프로그램 프록시 커넥터는 EU에 있는 Azure 데이터 센터의 서비스 인스턴스를 사용합니다. 따라서 모든 사용자가 게시된 응용 프로그램에 액세스를 시도할 때 이러한 위치의 응용 프로그램 프록시 서비스 인스턴스를 통과합니다.

## <a name="considerations-for-reducing-latency"></a>대기 시간 단축을 위한 고려 사항

모든 프록시 솔루션은 네트워크 연결에 대기 시간을 발생시킵니다. 원격 액세스 솔루션으로 프록시 또는 VPN 솔루션 중 어느 것을 선택하든지 간에 항상 회사 네트워크 내부에 연결을 사용하는 일련의 서버가 포함됩니다.

조직은 일반적으로 자신의 경계 네트워크에 서버 끝점을 포함합니다. 하지만 Azure AD 응용 프로그램 프록시를 사용하는 경우 경계 네트워크가 필요하지 않습니다. 트래픽은 클라우드의 프록시 서비스를 통해 흐르지만 커넥터는 회사 네트워크에 상주하기 때문입니다.

### <a name="connector-placement"></a>커넥터 배치

응용 프로그램 프록시는 테넌트 위치에 따라 사용자의 인스턴스 위치를 선택합니다. 따라서 커넥터를 설치할 위치를 결정하여 네트워크 트래픽의 대기 시간 특정을 정의할 수 있습니다.

응용 프로그램 프록시 서비스를 설정할 때 다음과 같은 질문을 해야 합니다.

* 앱은 어디에 있나요?
* 앱에 액세스하는 사용자는 대부분 어디에 있나요?
* 응용 프로그램 프록시 인스턴스는 어디에 있나요(사용자 테넌트를 기반으로 함)?
* Azure ExpressRoute 또는 유사한 VPN처럼 Azure 데이터 센터 설정에 대한 기존의 전용 네트워크 연결이 있나요?

커넥터의 배치는 홉 2 및 3의 대기 시간을 결정합니다(이전 섹션에서 설명). 커넥터의 배치를 평가할 때 다음을 고려해야 합니다.

* 커넥터에는 데이터 센터에 대한 시야가 필요합니다. 그렇게 하면 백 엔드 응용 프로그램으로 SSO(Single Sign-On)를 원하는 경우 커넥터가 KCD(Kerberos 제한 위임)를 수행할 수 있습니다.
* 일반적으로 커넥터에서 응용 프로그램으로의 시간을 줄이기 위해 커넥터는 응용 프로그램 가까이 설치됩니다.

### <a name="general-approach-to-minimize-latency"></a>대기 시간을 최소화하는 일반적인 방법

각 네트워크 홉을 최적화하여 종단 간 트래픽을 시도하고 대기 시간을 최소화할 수 있습니다. 다음을 수행하여 각 홉을 최적화할 수 있습니다.

* 홉의 두 끝 사이의 거리 줄이기
* 트래버스하기 적합한 네트워크 선택. 예를 들어 공용 인터넷보다 개인 네트워크를 트래버스하는 것이 전용 링크가 있으므로 더 빠를 수 있습니다.

Azure 및 회사 네트워크 간에 전용 VPN 또는 ExpressRoute가 있는 경우 다음을 사용하는 것이 좋습니다.

## <a name="focus-your-optimizing-strategy"></a>최적화 전략에 집중

사용자는 인터넷을 통해 원격으로 앱에 액세스할 수 있으므로 항상 홉 2 및 3을 최적화하는 것에 집중해야 합니다. 통합할 수 있는 일반적인 패턴은 다음과 같습니다.

### <a name="pattern-1-optimize-hop-3"></a>패턴 1: 홉 3 최적화

홉 3을 최적화하기 위해 커넥터를 고객 네트워크의 대상 응용 프로그램 가까이에 배치합니다. 이 작업을 수행할 경우 커넥터가 도메인 컨트롤러에 대한 시야를 필요로 할 수 있다는 장점이 있습니다. 이 방법은 대부분의 시나리오에서 적합합니다. 사실, 대부분의 고객은 이 패턴을 따릅니다.

 ![대상 응용 프로그램에 가까이 배치된 커넥터를 사용하여 홉 3 최적화를 보여 주는 다이어그램](./media/application-proxy-network-topologies/application-proxy-hop3.png)


> [!NOTE]
원하는 대기 시간 특성을 얻기 위해 홉 2와 홉 3을 최적화해야 하는 몇 가지 시나리오가 있습니다. 예를 들어. 네트워크와 Azure 데이터 센터 간에 VPN 또는 ExpressRoute가 설정된 경우 이러한 홉을 최적화할 수 있습니다.
>

### <a name="pattern-2-take-advantage-of-expressroute-with-public-peering"></a>패턴 2: 공용 피어링이 있는 ExpressRoute 활용

공용 피어링이 있는 ExpressRoute를 설정한 경우 홉 2에 대해 더 빨라진 ExpressRoute 연결을 사용할 수 있습니다. 사용자의 네트워크에서 앱 가까이에 커넥터를 배치하여 홉 3을 이미 최적화했습니다.

![ExpressRoute 연결을 사용하여 홉 2 최적화를 보여 주는 다이어그램](./media/application-proxy-network-topologies/application-proxy-expressroute-public.png)

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>패턴 3: 개인 피어링이 있는 ExpressRoute 활용

Azure 및 회사 네트워크 간에 개인 피어링이 있는 전용 VPN 또는 ExpressRoute를 설정한 경우 다른 옵션이 있습니다. 이 구성에서는 일반적으로 Azure의 가상 네트워크가 회사 네트워크의 확장으로 간주됩니다. 따라서 Azure 데이터 센터에 커넥터를 설치할 수 있으며, 홉 3에 대한 커넥터-앱 연결에 낮은 대기 시간 요구 사항을 계속해서 충족할 수 있습니다.

트래픽은 전용 연결을 통해 전송되므로 대기 시간을 변경시키지 않습니다. 홉 2의 대기 시간 특성을 향상시킨다는 이점을 추가로 얻을 수 있습니다. 이제 응용 프로그램 프록시 서비스-커넥터 연결이 홉이 짧아졌기 때문입니다. 커넥터는 Azure AD 테넌트(즉, 응용 프로그램 프록시) 위치와 가까운 Azure 데이터 센터에 설치됩니다.

![Azure 데이터 센터 내에 설치된 커넥터를 보여 주는 다이어그램](./media/application-proxy-network-topologies/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>다른 방법

이 문서에서는 커넥터 배치를 집중적으로 설명하지만 대기 시간을 개선하기 위해 응용 프로그램의 배치를 변경할 수도 있습니다.

조직은 더욱 더 네트워크를 호스티드 환경으로 이동하고 있습니다. 이렇게 함으로써 회사 네트워크에 포함되는 호스티드 환경에 앱을 배치할 수 있으며 계속 도메인 내에 있게 됩니다. 이 경우 이전 섹션에서 설명한 패턴을 새 응용 프로그램 위치에 적용할 수 있습니다.

다른 위치 및 네트워크에 있는 대상 앱에 커넥터 그룹을 사용하는 것이 좋습니다. 이 옵션을 고려하는 경우 [Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds)를 참조하세요.

## <a name="common-scenarios"></a>일반적인 시나리오

이 섹션에서는 몇 가지 사용 사례를 살펴봅니다. Azure AD 테넌트(즉, 프록시 서비스 끝점)가 미국에 위치한다고 가정합니다. 이러한 사용 사례에서 설명한 고려 사항은 일반적으로 전 세계 다른 지역에도 적용됩니다.

### <a name="use-case-1"></a>사용 사례 1

앱은 동일한 지역의 사용자와 함께 미국에 있는 조직의 네트워크에 있습니다. Azure 데이터 센터 및 회사 네트워크 간에 ExpressRoute 또는 VPN이 존재하지 않습니다.

**권장 사항:** 이전 섹션에서 설명한 대로 패턴 1을 따릅니다. 대기 시간 개선을 위해 필요한 경우 ExpressRoute를 사용하는 것이 좋습니다.

이것은 단순한 패턴입니다. 커넥터를 앱 가까이에 배치하여 홉 3을 최적화합니다. 일반적으로 커넥터는 KCD 작업을 수행하기 위해 앱과 데이터 센터에 대한 시야를 사용하여 설치되므로 자연스러운 선택이기도 합니다.

![미국의 개요를 보여 주는 다이어그램 및 이 사용 사례에서 홉이 정렬되는 방식](./media/application-proxy-network-topologies/application-proxy-pattern1.png)

### <a name="use-case-2"></a>사용 사례 2

앱은 전 세계에 분산된 사용자와 함께 미국에 있는 조직의 네트워크에 있습니다. Azure 데이터 센터 및 회사 네트워크 간에 ExpressRoute 또는 VPN이 존재하지 않습니다.

**권장 사항:** 이전 섹션에서 설명한 대로 패턴 2를 따릅니다. 대기 시간 개선을 위해 필요한 경우 ExpressRoute를 사용하는 것이 좋습니다.

일반적인 패턴은 홉 3을 최적화하는 것이며, 이 경우 커넥터를 앱 근처에 배치합니다. 홉 3이 모두 동일한 지역에 있는 경우 일반적으로 비용이 많이 들지 않습니다. 하지만 모든 사용자가 미국에 있는 응용 프로그램 프록시 인스턴스에 액세스하므로 홉 1은 사용자 위치에 따라 비용이 더 높을 수 있습니다. 모든 프록시 솔루션은 전 세계에 분산된 사용자에 따라 유사한 특성을 포함한다는 것에 주목해야 합니다.

![전 세계의 개요를 보여 주는 다이어그램 및 이 사용 사례에서 홉이 정렬되는 방식](./media/application-proxy-network-topologies/application-proxy-pattern2.png)

### <a name="use-case-3"></a>사용 사례 3

앱은 미국에 있는 조직의 네트워크에 있습니다. 공용 피어링이 있는 ExpressRoute는 Azure 및 회사 네트워크 간에 존재합니다.

**권장 사항:** 커넥터를 앱에 최대한 가까이 배치합니다. 시스템에서는 홉 2에 ExpressRoute를 자동으로 사용합니다. 이전 섹션에서 설명한 대로 패턴 2를 따릅니다.

ExpressRoute 링크가 공용 피어링을 사용하는 경우 프록시와 커넥터 간의 트래픽은 해당 링크를 통해 흐르게 됩니다. 홉 2는 대기 시간을 최적화하게 됩니다.

![미국의 개요를 보여 주는 다이어그램 및 이 사용 사례에서 홉이 정렬되는 방식](./media/application-proxy-network-topologies/application-proxy-pattern3.png)

### <a name="use-case-4"></a>사용 사례 4

앱은 미국에 있는 조직의 네트워크에 있습니다. 개인 피어링이 있는 ExpressRoute는 Azure 및 회사 네트워크 간에 존재합니다.

**권장 사항:** 커넥터를 ExpressRoute 개인 피어링을 통해 회사 네트워크에 연결된 Azure 데이터 센터에 배치합니다. 이전 섹션에서 설명한 대로 패턴 3을 따릅니다.

커넥터를 Azure 데이터 센터에 배치할 수 있습니다. 커넥터가 개인 네트워크를 통해 응용 프로그램 및 데이터 센터에 대한 가시권을 계속 확보하므로 홉 3은 최적화된 상태로 유지됩니다. 또한 홉 2는 더욱 최적화됩니다.

![미국의 개요를 보여 주는 다이어그램 및 이 사용 사례에서 홉이 정렬되는 방식](./media/application-proxy-network-topologies/application-proxy-pattern4.png)

### <a name="use-case-5"></a>사용 사례 5

앱은 미국의 사용자 대부분과 함께 유럽에 있는 조직의 네트워크에 있습니다.

**권장 사항:** 커넥터를 앱 가까이 배치합니다. 미국 사용자는 동일한 지역에 있는 응용 프로그램 프록시 인스턴스에 액세스하므로 홉 1의 비용은 그다지 비싸지 않습니다. 홉 3이 최적화됩니다. 하지만 홉 2는 일반적으로 이 사용 사례에서 비용이 많이 듭니다.

![전 세계의 개요를 보여 주는 다이어그램 및 이 사용 사례에서 홉이 정렬되는 방식](./media/application-proxy-network-topologies/application-proxy-pattern5a.png)

패턴 2와 3에 대한 이전 섹션에 설명된 대로 ExpressRoute 사용을 고려합니다.

![전 세계의 개요를 보여 주는 다이어그램 및 이 사용 사례에서 홉이 정렬되는 방식](./media/application-proxy-network-topologies/application-proxy-pattern5b.png)

이 상황에서 다른 한 가지 변수를 사용하도록 고려할 수 있습니다. 조직에 있는 대부분 사용자가 미국에 있는 경우 네트워크가 미국으로도 확장될 가능성이 있습니다. 해당되는 경우 커넥터를 미국에 배치하고 유럽에 있는 응용 프로그램으로 전용 내부 회사 네트워크 라인을 사용할 수 있습니다. 이 방식으로 홉 2와 3이 최적화됩니다.

![전 세계의 개요를 보여 주는 다이어그램 및 이 사용 사례에서 홉이 정렬되는 방식](./media/application-proxy-network-topologies/application-proxy-pattern5c.png)

## <a name="next-steps"></a>다음 단계
[응용 프로그램 프록시 사용](active-directory-application-proxy-enable.md)<br>
[Single Sign-On 사용](active-directory-application-proxy-sso-using-kcd.md)<br>
[조건부 액세스 사용](active-directory-application-proxy-conditional-access.md)<br>
[응용 프로그램 프록시에서 발생한 문제 해결](active-directory-application-proxy-troubleshoot.md)

