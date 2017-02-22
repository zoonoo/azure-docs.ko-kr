---
title: "Azure AD 응용 프로그램 프록시를 사용할 때 네트워크 토폴로지 고려 사항 | Microsoft Docs"
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
ms.sourcegitcommit: 1fdc00865f31352026a64c2769d9d65047bb2fa6
ms.openlocfilehash: 26ec9167f1f883c3c71947a55142020fb48e206a


---

# <a name="network-topology-considerations-when-using-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시를 사용할 때 네트워크 토폴로지 고려 사항
> [!NOTE]
> 응용 프로그램 프록시는 Premium 또는 Basic 버전의 Azure Active Directory로 업그레이드하는 경우에만 사용할 수 있는 기능입니다. 자세한 내용은 [Azure Active Directory 버전](active-directory-editions.md)을 참조하세요.
> 

이 문서에서는 응용 프로그램을 원격으로 게시 및 액세스하기 위해 Azure AD 응용 프로그램 프록시를 사용할 때 네트워크 토폴로지 고려 사항을 설명합니다. 

## <a name="traffic-flow"></a>트래픽 흐름

Azure AD 앱 프록시를 통해 응용 프로그램을 게시할 때 사용자에게서 대상 백 엔드 응용 프로그램으로 이동하는 모든 트래픽은 다음 홉을 통과합니다.

* 홉 1: 사용자-Azure에서 Azure AD 앱 프록시 서비스의 공용 끝점
* 홉 2: 앱 프록시 서비스-커넥터
* 홉 3: 커넥터-대상 응용 프로그램

 ![AzureAD Iaas 다중 클라우드 공급업체](./media/application-proxy-network-topologies/application-proxy-three-hops.png)

## <a name="tenant-location-and-app-proxy-service"></a>테넌트 위치 및 앱 프록시 서비스

Azure AD 테넌트에 등록할 때 테넌트의 지역(US, EMEA, APAC 등)은 지정한 국가에 따라 결정됩니다. 앱 프록시를 사용하도록 설정하면 사용자 테넌트의 앱 프록시 서비스 인스턴스가 Azure AD 테넌트와 동일한 지역이나 가장 가까운 지역에 표시됩니다. 

예를 들어 Azure AD 테넌트의 지역이 EU(유럽 연합)이고 모든 Azure AD 앱 프록시 커넥터가 EU Azure 데이터 센터의 앱 프록시 서비스 인스턴스에 연결됩니다. 따라서 모든 사용자가 게시된 응용 프로그램에 액세스를 시도할 때 이러한 위치의 앱 프록시 서비스 인스턴스를 통과합니다.

## <a name="considerations-for-reducing-latency"></a>대기 시간 단축을 위한 고려 사항

모든 프록시 솔루션은 네트워크 연결에 대기 시간을 야기합니다. 원격 액세스 솔루션으로 프록시 또는 VPN 솔루션 중 어느 것을 선택하든지 간에 회사 네트워크 내부에 연결을 사용하는 데 항상 서버 집합이 포함됩니다. 

기업은 일반적으로 네트워크의 완충 영역(DMZ)에 서버 끝점을 포함합니다. 하지만 Azure AD 앱 프록시에서는 DMZ가 필요하지 않습니다.  앱 프록시 트래픽은 클라우드의 프록시 서비스를 통해 흐르지만 커넥터는 회사 네트워크에 상주하기 때문입니다.

### <a name="connector-placement"></a>커넥터 배치

앱 프록시 서비스는 테넌트 위치에 따라 사용자의 인스턴스 위치를 선택합니다. 따라서 커넥터를 설치할 위치를 결정하여 네트워크 트래픽의 종단 간 대기 시간 특정을 정의할 수 있습니다.

앱 프록시 서비스를 설정할 때는 다음과 같은 몇 가지 질문을 제기할 수 있습니다.

* 앱은 어디에 있나요?
* 대부분의 사용자는 어디에서 앱을 찾나요?
* 앱 프록시 인스턴스는 어디에 있나요(사용자 테넌트를 기반으로 함)?
* Express 경로 또는 유사한 VPN 설정처럼 Azure 데이터 센터에 대한 기존의 전용 네트워크 연결이 있나요?

커넥터의 위치에 따라 홉 #2 및 홉 #3의 대기 시간이 결정됩니다. 커넥터의 배치를 평가할 때 다음을 고려해야 합니다.

* 커넥터에서 Kerberos 제한 위임(KCD)을 수행하려면 백 엔드 응용 프로그램으로 SSO(Single Sign-On)를 원하는 경우 데이터 센터로의 가시권(line-of-sight)이 필요합니다.
* 일반적으로 커넥터에서 응용 프로그램으로의 시간을 줄이기 위해 커넥터는 응용 프로그램 가까이 설치됩니다.

### <a name="general-approach-to-minimize-latency"></a>대기 시간을 최소화하는 일반적인 방법

트래픽이 흐르도록 각 네트워크 홉을 최적화하여 종단 간 트래픽을 시도하고 대기 시간을 최소화할 수 있습니다.

다음을 수행하여 각 홉을 최적화할 수 있습니다.

* 홉의 두 끝 사이의 거리 줄이기
* 트래버스하기 적합한 네트워크 선택. 예를 들어 공용 인터넷보다 개인 네트워크를 트래버스하는 것이 전용 링크가 있으므로 더 빠를 수 있습니다.
 
Azure 및 회사 네트워크 간에 전용 VPN/Express 경로가 있는 경우 다음을 활용하는 것이 좋습니다.

## <a name="focus-your-optimizing-strategy"></a>최적화 전략에 집중

사용자는 인터넷을 통해 원격으로 앱에 액세스할 수 있으므로 항상 홉 2 및 3을 최적화하는 것에 집중해야 합니다. 통합할 수 있는 일반적인 패턴은 다음과 같습니다.

### <a name="pattern-1-optimize-hop-3"></a>패턴 #1: 홉 #3 최적화:

홉 3을 최적화하기 위해 커넥터를 고객 네트워크의 대상 응용 프로그램 가까이에 배치합니다. 이 작업을 수행할 때의 장점은 위에서 설명한 것처럼 커넥터에 도메인 컨트롤러에 대한 가시권이 필요할 수 있다는 것입니다. 이 방법은 보통 대부분의 고객 및 시나리오에 충분히 적용 가능합니다. 대부분의 고객은 이 패턴을 따릅니다.

 ![AzureAD Iaas 다중 클라우드 공급업체](./media/application-proxy-network-topologies/application-proxy-hop3.png)


> [!NOTE]
원하는 대기 시간 특성을 얻기 위해 홉 #2와 홉 #3을 최적화해야 하는 몇 가지 시나리오가 있습니다. 예를 들어 네트워크와 Azure 데이터 센터 간에 VPN 또는 ExpressRoute가 설정된 경우 이 시나리오에서는 홉 #3 외에도 홉 #2를 최적화할 수 있습니다.
>

### <a name="pattern-2-take-advantage-of-expressroute-with-public-peering"></a>패턴 #2: 공용 피어링이 있는 ExpressRoute 활용

공용 피어링이 있는 ExpressRoute를 설정한 경우 홉 #2에 대해 더 빨라진 ExpressRoute 연결을 활용하게 됩니다. 고객 네트워크에서 앱 가까이에 커넥터를 배치하여 홉 #3을 이미 최적화했습니다.

![AzureAD Iaas 다중 클라우드 공급업체](./media/application-proxy-network-topologies/application-proxy-expressroute-public.png)

### <a name="pattern-3-taking-advantage-expressroute-with-private-peering"></a>패턴 #3: 개인 피어링이 있는 ExpressRoute 활용

Azure 및 앱이 설치된 회사 네트워크 사이에 개인 피어링이 있는 전용 VPN 또는 ExpressRoute를 설정한 경우 다른 옵션이 있습니다. 이 구성에서는 일반적으로 Azure의 가상 네트워크가 회사 네트워크의 확장으로 간주됩니다. 따라서 Azure 데이터 센터에 커넥터를 설치할 수 있으며, 홉 #3에 대한 커넥터-앱 연결에 대해 낮은 대기 시간 요구 사항을 계속해서 만족시킬 수 있습니다. 

트래픽은 전용 연결을 통해 전송되므로 대기 시간에 나쁜 영향을 주지 않습니다. 하지만 홉 #2의 대기 시간 특성을 향상시킨다는 이점을 추가로 얻을 수 있습니다. 커넥터가 AAD 테넌트(즉, 앱 프록시) 위치 가까이 있는 Azure 데이터 센터에 설치되어 프록시 서비스-커넥터 연결(홉 #2)은 더 짧은 홉이기 때문입니다.

![AzureAD Iaas 다중 클라우드 공급업체](./media/application-proxy-network-topologies/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>다른 방법

이 문서에서 지금까지는 커넥터 배치에 중점을 두어 살펴보았습니다. 하지만 응용 프로그램 이동이 사용자의 선택인 경우(예를 들어, Azure 또는 다른 호스티드 환경) 더 나은 대기 시간 특성을 얻도록 응용 프로그램 배치를 변경할 수 있습니다. 

조직은 더욱 더 네트워크를 호스티드 환경으로 이동하고 있습니다. 이렇게 함으로써 회사 네트워크에 포함되는 호스티드 환경에 앱을 배치할 수 있으며 계속 도메인 내에 있게 됩니다. 이 경우 위의 패턴을 새 응용 프로그램 위치에 적용할 수 있습니다.

다른 위치 및 네트워크에 있는 대상 앱에 커넥터 그룹을 사용하는 것이 좋습니다. 이 옵션을 고려하는 경우 [Azure AD Domain Services](https://azure.microsoft.com/en-us/services/active-directory-ds)를 참조하세요. 

## <a name="common-scenarios"></a>일반적인 시나리오

이 섹션에서는 몇 가지 사용 사례를 살펴봅니다. 아래 모든 사용 사례에서는 Azure AD 테넌트(즉, 프록시 서비스 끝점)가 미국에 있다고 가정합니다. 전 세계 다른 지역에서도 일반적으로 같은 고려 사항이 적용됩니다.

### <a name="use-case-1"></a>사용 사례 1

앱은 같은 지역의 사용자와 함께 미국에 있는 고객 네트워크에 있습니다. Azure DC 및 회사 네트워크 간에 ExpressRoute 또는 VPN이 없습니다.

**권장 사항:** 위의 사용 사례 #1을 따르세요. 대기 시간 개선을 위해 필요한 경우 ExpressRoute를 사용하는 것이 좋습니다<사용 사례 #3 및 #4 참조>.

이것은 단순한 패턴입니다. 대부분의 일반적인 패턴은 홉 #3을 최적화하는 것이며, 이 경우 커넥터는 앱 근처에 배치됩니다. 일반적으로 커넥터는 KCD 작업을 수행하기 위해 앱과 DC에 대한 가시권에 설치되므로 자연스러운 선택이기도 합니다.
이 사용 사례는 아래와 같은 패턴 #1을 따릅니다.

![AzureAD Iaas 다중 클라우드 공급업체](./media/application-proxy-network-topologies/application-proxy-pattern1.png)

### <a name="use-case-2"></a>사용 사례 2

앱은 전 세계에 분산된 사용자와 함께 미국에 있는 고객 네트워크에 있습니다. Azure DC 및 회사 네트워크 간에 ExpressRoute 또는 VPN이 없습니다.

**권장 사항:** 위의 사용 사례 #2를 따르세요. 대기 시간 개선을 위해 필요한 경우 ExpressRoute를 사용하는 것이 좋습니다(사용 사례 #3 및 #4 참조).

역시, 일반적인 패턴은 홉 #3을 최적화하는 것이며, 이 경우 커넥터는 위에서 설명한 이유로 앱 근처에 배치됩니다. 일반적으로 홉 #3은 모두 동일한 지역에 있는 경우 비용이 많이 들지 않습니다. 하지만 모든 사용자가 미국에 있는 앱 프록시 인스턴스에 액세스하므로 홉 #1은 사용자 위치에 따라 비용이 더 높을 수 있습니다. 모든 프록시 솔루션은 전 세계에 분산된 사용자에 관해 유사한 특성을 포함한다는 것에 주목해야 합니다.

이 사용 사례는 아래와 같은 패턴 #2를 따릅니다.

![AzureAD Iaas 다중 클라우드 공급업체](./media/application-proxy-network-topologies/application-proxy-pattern2.png)

### <a name="use-case-3"></a>사용 사례 3

앱은 미국에 있는 고객 네트워크에 있습니다. 공용 피어링이 있는 ExpressRoute는 Azure 및 회사 네트워크 간에 존재합니다.

**권장 사항:** 커넥터를 앱에 최대한 가까이 배치합니다. 시스템에서는 홉 #2에 대해 ExpressRoute를 자동으로 사용합니다. 위에서 설명한 패턴 #2를 따릅니다.

ExpressRoute 링크가 공용 피어링을 사용하는 경우 프록시와 커넥터 사이의 트래픽은 해당 링크를 통해 흐르고 홉 #2는 최적화된 대기 시간을 갖게 됩니다.

이 사용 사례는 아래와 같은 패턴 #3을 따릅니다.

![AzureAD Iaas 다중 클라우드 공급업체](./media/application-proxy-network-topologies/application-proxy-pattern3.png)

### <a name="use-case-4"></a>사용 사례 4

앱은 미국에 있는 고객 네트워크에 있습니다. 개인 피어링이 있는 ExpressRoute는 Azure 및 회사 네트워크 간에 존재합니다.

**권장 사항:** 커넥터를 ExpressRoute 개인 피어링을 통해 회사 네트워크에 연결된 Azure DC에 배치합니다. 위에서 설명한 패턴 #3을 따릅니다.

커넥터를 Azure DC에 배치할 수 있습니다. 개인 네트워크를 통해 응용 프로그램 및 DC에 대한 가시권이 계속 확보되므로 홉 #3은 최적화된 상태로 유지됩니다. 하지만 이 설정은 홉 #2를 더욱 최적화합니다.

이 사용 사례는 아래와 같은 패턴 #4를 따릅니다.

![AzureAD Iaas 다중 클라우드 공급업체](./media/application-proxy-network-topologies/application-proxy-pattern4.png)

### <a name="use-case-5"></a>사용 사례 5

앱은 미국에 있는 대부분의 사용자와 함께 유럽에 있는 고객 네트워크에 있습니다.

**권장 사항:** 커넥터를 앱 가까이 배치합니다. 위에서 설명한 이유로 가장 적절한 선택입니다. 미국 사용자는 동일한 지역에 있는 앱 프록시 인스턴스에 액세스하므로 홉 #1의 비용은 그다지 비싸지 않습니다. 홉 #3이 최적화됩니다. 하지만 홉 #2는 일반적으로 이 사용 사례에서 비용이 많이 듭니다.

이 사용 사례는 아래와 같은 패턴 #5a를 따릅니다.

![AzureAD Iaas 다중 클라우드 공급업체](./media/application-proxy-network-topologies/application-proxy-pattern5a.png)

위의 패턴 #2 및 #3에서 호출한 대로 ExpressRoute를 활용하는 것이 좋습니다. 아래에서 패턴 #2가 적용된 것을 확인할 수 있습니다.

이 사용 사례는 아래와 같은 패턴 #5b를 따릅니다.

![AzureAD Iaas 다중 클라우드 공급업체](./media/application-proxy-network-topologies/application-proxy-pattern5b.png)

이 사용 사례에 대해 사용을 고려할 수 있는 다른 변형은 다음과 같습니다.

조직에 있는 대부분 사용자가 미국에 있는 경우 네트워크가 미국으로도 '확장'될 가능성이 있습니다 해당되는 경우 커넥터를 미국에 배치하고 유럽에 있는 응용 프로그램으로 전용 내부 회사 네트워크 라인을 활용할 수 있습니다. 이렇게 홉 #2와 홉 #3이 최적화됩니다.

이 사용 사례는 아래와 같은 패턴 #5c를 따릅니다.

![AzureAD Iaas 다중 클라우드 공급업체](./media/application-proxy-network-topologies/application-proxy-pattern5c.png)

## <a name="next-steps"></a>다음 단계
[응용 프로그램 프록시 사용](active-directory-application-proxy-enable.md)<br>
[Single Sign-On 사용](active-directory-application-proxy-sso-using-kcd.md)<br>
[조건부 액세스 사용](active-directory-application-proxy-conditional-access.md)<br>
[응용 프로그램 프록시에서 발생한 문제 해결](active-directory-application-proxy-troubleshoot.md)





<!--HONumber=Feb17_HO1-->


