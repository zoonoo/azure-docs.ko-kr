---
title: "Azure Cloud에서 부하 분산 서비스 사용하기 | Microsoft 문서"
description: "이 자습서에서는 Azure 부하 분산 포트폴리오인 Traffic Manager, Application Gateway 및 Load Balancer를 사용하는 시나리오를 만드는 방법을 보여 줍니다."
services: traffic-manager
documentationcenter: 
author: liumichelle
manager: vitinnan
editor: 
ms.assetid: f89be3be-a16f-4d47-bcae-db2ab72ade17
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: limichel
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 90918f6bb23f0f4e3c009506e1b12c028c45f02b

---

# <a name="using-load-balancing-services-in-the-azure-cloud"></a>Azure Cloud에서 부하 분산 서비스 사용하기

## <a name="introduction"></a>소개

Microsoft Azure는 네트워크 트래픽을 분산하고 부하를 분산하는 방법을 관리하는 여러 서비스를 제공합니다. 이러한 서비스를 개별적으로 사용하거나 또는 특정 요구 사항에 따라 해당 메서드를 결합하여 최적의 솔루션을 빌드할 수 있습니다.

이 자습서에서는 먼저 고객 사용 사례를 정의하고 Azure 부하 분산 포트폴리오인 Traffic Manager, Application Gateway 및 Load Balancer를 사용하여 보다 강력하고 성능이 뛰어나도록 만드는 방법을 알아봅니다. 그런 다음 지리적으로 중복이고, VM에 트래픽을 분산하고, 다양한 유형의 요청을 관리하는 기능을 제공하는 배포를 만들기 위한 단계별 지침을 제공합니다.

개념 수준에서 이러한 각 서비스는 부하 분산 계층 구조에서 저마다의 고유한 역할을 합니다.

1. **Traffic Manager**는 전역 DNS 부하 분산을 제공 합니다.  들어오는 DNS 요청을 보고 정상적인 끝점을 통해 고객이 선택한 라우팅 정책에 따라 응답합니다. 라우팅 메서드에 대한 옵션에는 대기 시간 측면에서 요청자를 가장 가까운 끝점에 보내는 성능 라우팅, 모든 트래픽을 하나의 끝점으로 보내고 나머지를 모두 백업 기능을 하도록 하는 우선 순위 라우팅 및 각 끝점에 할당된 가중치에 따라 트래픽을 분산하는 가중된 라운드 로빈 라우팅이 있습니다. 클라이언트는 해당 끝점에 직접 연결합니다. Azure Traffic Manager는 끝점이 비정상임을 감지할 때 클라이언트를 다른 정상적인 인스턴스로 리디렉션합니다. 서비스에 대한 자세한 내용은 [Azure Traffic Manager 설명서](traffic-manager-overview.md)를 참조하세요.
2. **Application Gateway**는 Application Delivery Controller (ADC)를 서비스로 제공하여 응용 프로그램에 대한 다양한 Layer 7 부하 분산 기능을 제공합니다. 따라서 고객은 CPU 사용량이 많은 SSL 종료를 Application Gateway에 오프로드하여 웹 팜 생산성을 최적화할 수 있습니다. Layer 7의 기타 라우팅 기능으로 들어오는 트래픽의 라운드 로빈 배포, 쿠키 기반 세션 선호도, URL 패스 기반 라우팅 및 단일 Application Gateway 뒤에 여러 웹 사이트를 호스트할 수 있는 기능 등을 포함합니다. Application Gateway는 인터넷 연결 게이트웨이, 내부 전용 게이트웨이 또는 둘의 조합으로 구성할 수 있습니다. Application Gateway는 전적으로 Azure에 의해 관리되고, 확장성 및 고가용성을 제공합니다. 향상된 관리 효율성을 위한 풍부한 진단 및 로깅 기능을 제공합니다.
3. **Load Balancer**는 Azure SDN 스택의 필수 요소입니다. Load Balancer는 모든 UDP 및 TCP 프로토콜에 대한 고성능, 지연율이 낮은 Layer 4 부하 분산 서비스를 제공 합니다.  인바운드 및 아웃 바운드 연결을 관리합니다.  공용 및 내부 부하 분산된 끝점을 구성 하고 서비스 가용성 관리 옵션을 검색하는 TCP 및 HTTP 상태를 사용하여 백 엔드 풀 대상에 인바운드 연결을 매핑하는 규칙을 정의할 수 있습니다.

## <a name="scenario"></a>시나리오

이 예제 시나리오에서는 두 가지 유형의 콘텐츠와 동적으로 렌더링 되는 웹 페이지를 제공하는 간단한 웹 사이트를 사용합니다. 이 웹 사이트는 지리 중복이어야 하며 사용자들에게 가장 가까운 (가장 낮은 대기 시간) 위치에서 서비스를 제공해야 합니다. 응용 프로그램 개발자는 /images/* 패턴과 일치하는 모든 URL이 웹 팜의 나머지 부분과는 다른 전용 VM 풀에서 서비스를 제공하도록 결정했습니다.

또한 동적 콘텐츠를 제공하는 기본 VM 풀은 고가용성 클러스터에 호스트되는 백 엔드 데이터베이스와 통신해야 합니다. 전체 배포는 Azure Resource Manager를 통해 프로비전됩니다.

Traffic Manager, Application Gateway 및 Load Balancer를 활용하여 이 웹 사이트에 이러한 디자인 목표를 달성할 수 있습니다.

1. **다중 지역 중복**: Traffic Manager를 사용하여 한 지역의 작동이 중단되더라도 응용 프로그램 소유자의 개입 없이 다음 최상의 지역으로 트래픽이 원활하게 라우팅됩니다.
2. **줄어든 대기 시간**: Azure Traffic Manager에서 자동으로 고객을 가장 가까운 지역으로 보내기 때문에 웹 페이지 콘텐츠를 요청할 때 대기 시간이 줄어든 것을 경험하게 됩니다.
3. **독립 확장성**: 웹 응용 프로그램 작업 부하를 콘텐츠 형식에 따라 구분되게 했으므로 응용 프로그램 소유자는 워크로드 요청을 서로 독립적으로 확장할 수 있습니다. Application Gateway는 트래픽을 지정된 규칙 및 응용 프로그램의 상태에 따라 적당한 풀로 라우팅되게 합니다.
4. **내부 부하 분산**: 고가용성 클러스터 앞에 Load Balancer가 놓이도록 배치하여 데이터베이스에 대한 활성 및 정상 끝점만이 응용 프로그램에 노출됩니다.  또한 데이터베이스 관리자는 프런트 엔드 응용 프로그램과는 독립적으로 클러스터 전반에 활성 및 수동 복제본을 배포하여 워크로드를 최적화할 수 있습니다.  Load Balancer는 고가용성 클러스터에 연결을 제공하고 정상적인 데이터베이스만 연결 요청을 받는지 확인합니다.

다음 다이어그램은 이 시나리오의 아키텍처를 보여 줍니다.

![시나리오 다이어그램 이미지](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> 이 예제는 Azure에서 제공하는 부하 분산 서비스의 다양한 구성 중 하나에 불과합니다. Azure Traffic Manager, Application Gateway 및 Load Balancer를 부하 분산 요구에 가장 잘 맞게 혼합 및 일치시킬 수 있습니다. 예를 들어, SSL 오프 로드 또는 Layer 7 처리가 필요 없는 경우 부하 분산 장치 Application Gateway 대신에 Load Balancer를 사용할 수 있습니다.

## <a name="setting-up-the-load-balancing-stack"></a>부하 분산 스택 설정

### <a name="step-1-create-a-traffic-manager-profile"></a>1단계: Traffic Manager 프로필 만들기

1. Azure Portal로 이동하여 **새로 만들기**를 클릭하고, "Traffic Manager 프로필"에 대한 마켓플레이스를 검색합니다.
2. "Traffic Manager 프로필 만들기" 블레이드에서 트래픽 관리자 프로필을 만들기 위한 기본 정보를 입력합니다.

   * **이름** - 트래픽 관리자 프로필에 DNS 접두사 이름을 제공합니다.
   * **라우팅 메서드** - 트래픽 라우팅 메서드 정책을 선택하고, 메서드에 대한 자세한 내용은 [Traffic Manager 트래픽 라우팅 방법 정보](traffic-manager-routing-methods.md)를 참조하세요.
   * **구독** - 프로필을 포함하는 구독
   * **리소스 그룹** - 트래픽 관리자 프로필을 포함하는 리소스 그룹으로 기존 또는 새 리소스 그룹일 수 있습니다.
   * **리소스 그룹 위치** - Traffic Manager 서비스는 전역이며 한 위치에 바인딩되지 않습니다. 하지만 Traffic Manager 프로필과 연결된 메타데이터가 상주하는 그룹에 대해 한 지역을 지정해야 합니다. 이 위치는 프로필의 런타임 가용성에 영향을 주지 않습니다.

3. 트래픽 관리자 프로필을 생성하려면 **만들기**를 클릭합니다.

    ![트래픽 관리자 블레이드 만들기](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>2단계: Application Gateway 만들기

1. Azure Portal로 이동하여 왼쪽 메뉴에서 **새로 만들기** > **네트워킹** > **Application Gateway**를 클릭합니다.
2. 그런 다음 응용 프로그램 게이트웨이에 대한 기본 정보를 입력합니다. 완료되면 확인을 클릭합니다. 기본 설정에 필요한 정보는 다음과 같습니다.

   * **이름** - 응용 프로그램 게이트웨이의 이름입니다.
   * **SKU 크기** - 이 설정은 응용 프로그램 게이트웨이의 크기입니다. 사용 가능한 옵션은 (소형, 중형 및 대형)입니다.
   * **인스턴스 수** - 인스턴스 수입니다. 이 값은 2에서 10 사이의 숫자여야 합니다.
   * **리소스 그룹** - Application Gateway를 유지할 리소스 그룹입니다. 기존 리소스 그룹 또는 새 리소스 그룹일 수 있습니다.
   * **위치** - Application Gateway의 지역입니다. 리소스 그룹에서의 위치와 같은 위치입니다. 위치는 가상 네트워크와 공용 IP가 게이트웨이와 동일한 위치에 있어야 하므로 중요합니다.

3. 그런 다음 응용 프로그램 게이트웨이에 대한 가상 네트워크, 서브넷, 프런트 엔드 IP 및 수신기 구성을 정의합니다. 이 시나리오에서 프런트 엔드 IP 주소는 나중에 Traffic Manager 프로필에 끝점으로 추가할 수 있도록 **공용**입니다.
4. 다음으로 구성할 응용 프로그램 게이트웨이 설정은 수신기 구성입니다. http가 사용되는 경우 구성할 항목이 더 이상 없으므로 **확인**을 클릭하면 됩니다. Https 추가 구성 사용이 필요한 경우 [Application Gateway 만들기](../application-gateway/application-gateway-create-gateway-portal.md) 9 단계부터 참조하세요.

#### <a name="configure-url-routing-for-application-gateways"></a>Application Gateway 위한 URL 라우팅 구성

패스 기반 규칙을 사용해 구성된 응용 프로그램 게이트웨이는 백 엔드 풀을 선택할 때 라운드 로빈 배포 외에도 요청 URL의 패스 패턴을 사용합니다. 이 시나리오에서는 "/images/\*"를 포함하는 모든 URL을 이미지 서버 풀로 보내도록 패스 기반 규칙을 추가합니다. 응용 프로그램 게이트웨이에 대해 URL 패스 기반 라우팅을 구성하는 자세한 내용은 [Application Gateway에 대한 패스 기반 규칙 만들기](../application-gateway/application-gateway-create-url-route-portal.md)를 참조하세요.

![웹 계층 다이어그램](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. 리소스 그룹에서 앞의 단계에서 만든 Application Gateway 인스턴스로 이동합니다.
2. 설정에서 **백 엔드 풀**을 선택하고 웹 계층 백 엔드 풀과 연결하려는 VM을 추가하려면 추가하기를 선택합니다.
3. "백 엔드 풀 추가" 블레이드에서 백 엔드 풀의 이름과 풀에 있는 컴퓨터의 모든 IP 주소를 입력합니다. 이 시나리오에서는 가상 컴퓨터의 두 백 엔드 서버 풀을 연결합니다.

    ![백 엔드 풀을 추가하는 응용 프로그램 게이트웨이](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. 그런 다음 응용 프로그램 게이트웨이의 설정에서 새 규칙을 추가하기 위해 **규칙**을 선택하고, **패스 기반** 단추를 클릭합니다.

    ![패스 규칙을 추가 하는 응용 프로그램 게이트웨이](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. "패스 기반 규칙 추가" 블레이드 내에서 규칙을 구성하려면 다음 정보를 제공합니다.

   * 기본 설정:
     + **이름** -포털에서 액세스할 수 있는 규칙의 친숙한 이름
     + **수신기** -규칙에 사용되는 수신기
     + **기본 백 엔드 풀** -기본 규칙에 사용할 백 엔드 풀
     + **기본 HTTP 설정** -기본 규칙에 사용할 HTTP 설정
   * 패스 기반 규칙:
     + **이름** -패스 기반 규칙의 친숙한 이름
     + **패스** -트래픽 전달에 사용되는 패스 규칙.
     + ** 백 엔드 풀** -이 규칙에 사용할 백 엔드 풀
     + **Http 설정** -이 규칙에 사용할 HTTP 설정

     > [!IMPORTANT]
     > 패스: 유효한 패스는 "/"로 시작해야 합니다. 와일드 카드 "\*"는 끝에만 허용됩니다. 사용 가능한 예는 /xyz, /xyz\* 또는 /xyz/\*입니다.

     ![pathrule 블레이드를 추가하는 응용 프로그램 게이트웨이](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>3단계: Application Gateway을 Traffic Manager 끝점에 추가

이 시나리오에서 Traffic Manager는 다른 지역에 있는 Application Gateway 인스턴스(위의 단계에서 구성됨)에 연결됩니다. Application Gateway를 구성했으므로 다음 단계는 그것을 Traffic Manager 프로필에 연결하는 것입니다.

1. 트래픽 관리자 프로필 인스턴스로 이동합니다.(이렇게 하려면 리소스 그룹 내에서 검색하거나 "모든 리소스"에서 트래픽 관리자 프로필의 이름을 검색합니다.)
2. 이 블레이드에서 **끝점**을 선택한 후 **추가**를 선택하여 새 끝점에 추가합니다.

    ![끝점을 추가하는 트래픽 관리자](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. "끝점 추가" 블레이드에서 새 끝점을 만들기 위한 정보를 입력합니다.

   * **유형** - 부하를 분산하기 위한 끝점 유형. 이 시나리오에서는 위에서 구성한 Application Gateway 인스턴스에 연결하기 때문에 Azure 끝점입니다.
   * **이름** -끝점의 이름
   * **대상 리소스 유형** - 공용 IP 주소를 선택하고 아래의 '대상 리소스' 설정에서 위에서 구성한 Application Gateway의 공용 IP를 선택합니다.

     ![끝점을 추가하는 트래픽 관리자](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. 이 시점부터 트래픽 관리자 프로필의 DNS를 사용하여 그것에 액세스하여 사용자의 설정을 테스트할 수 있습니다 (이 경우 TrafficManagerScenario.trafficmanager.net) 사용자 설정을 테스트 하려면 요청을 다시 보내고, 다른 지역에서 만든 VM/웹 서버를 위로 올리거나 아래로 내리고, Traffic Manager 프로필 설정을 변경할 수 있습니다.

### <a name="step-4-create-the-load-balancer"></a>4단계: Load Balancer 만들기

이 시나리오에서 Load Balancer는 고가용성 클러스터 내에서 웹 계층으로부터 데이터베이스로 연결을 배포합니다.

고가용성 데이터베이스 클러스터에서 SQL AlwaysOn을 사용하는 경우 단계별 지침으로 하나 이상의 [ AlwaysOn 가용성 그룹 수신기](../virtual-machines/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 구성을 참조하세요.

내부 부하 분산 장치 구성에 대한 자세한 내용은 [Azure Portal에서 내부 부하 분산 장치 만들기](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)를 참조하세요.

1. Azure Portal로 이동하여 왼쪽 메뉴에서 **새로 만들기** > **네트워킹** > **Load balancer**를 클릭합니다.
2. "부하 분산 장치 만들기" 블레이드에서 부하 분산 장치의 이름을 선택합니다.
3. **유형**을 내부로 설정하고 부하 분산 장치를 상주하게 할 적절한 가상 네트워크 및 서브넷을 선택합니다.
4. **IP 주소 할당**에서, 동적 또는 정적 중 하나를 선택합니다.
5. 그런 다음 **리소스 그룹**에서, 부하 분산 장치에 대한 리소스 그룹을 선택합니다.
6. **위치**에서, 부하 분산 장치에 대한 적절한 리소스 그룹을 선택합니다.
7. 마지막으로, **만들기**를 클릭하여 부하 분산 장치를 생성합니다.

#### <a name="connect-backend-database-tier-to-load-balancer"></a>백 엔드 데이터베이스 계층을 Load Balancer에 연결합니다.

1. 리소스 그룹에서 위의 단계에서 만든 Load Balancer를 찾습니다.
2. 설정에서 **백엔드 풀**과 **추가**를 차례로 클릭하여 새 백 엔드 풀을 추가합니다.
3. "백 엔드 풀 추가" 블레이드에서 백 엔드 풀의 이름을 입력합니다.
4. 여기에서 개별 컴퓨터를 백 엔드 풀에 추가하거나 가용성 집합을 백 엔드 풀에 추가할 수 있습니다.

   ![풀을 추가하는 부하 분산 장치](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

#### <a name="configure-a-probe"></a>프로브를 구성합니다.

1. 부하 분산 장치의 설정에서 새 프로브를 추가하려면 **프로브**와 **추가**를 차례로 선택합니다.
2. "프로브 추가" 블레이드에서 프로브에 대한 **이름**을 입력합니다.
3. 프로브에 대한 **프로토콜**을 선택합니다. 데이터베이스의 경우 HTTP 프로브 보다는 TCP 프로브가 좋습니다.   Load Balancer 프로브에 대한 자세한 내용은 [부하 분산 장치 프로브 이해](../load-balancer/load-balancer-custom-probe-overview.md)를 참조하세요.
4. 다음으로 프로브에 액세스할 때 사용될 **포트**를 입력합니다.
5. **간격**에서 얼마나 자주 응용 프로그램을 프로브할지 지정합니다.
6. **비정상 임계값**에서, 백 엔드 VM을 비정상 상태로 간주할 수 있으려면 발생해야 하는 연속 프로브 실패 수를 지정합니다.
7. **확인**을 클릭해 프로브를 만듭니다.

   ![부하 분산 장치 프로브](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

#### <a name="configure-load-balancing-rules"></a>부하 분산 규칙 구성

1. 부하 분산 장치의 설정에서 규칙을 만들려면 **부하 분산 규칙**과 **추가**를 차례로 선택합니다.
2. "부하 분산 규칙 추가" 블레이드에서 부하 분산 규칙 대한 **이름**을 입력합니다.
3. **부하 분산 장치의 프런트 엔드 IP 주소**, **프로토콜** 및 **포트**를 선택합니다.
4. **백 엔드 포트**에서, 백 엔드 풀에 사용할 포트를 지정합니다.
5. 규칙을 적용할 이전 단계에서 만든 **백 엔드 풀**과 **프로브**를 선택합니다.
6. **세션 지속성**에서 세션을 얼마나 지속할지 선택합니다.
7. **유휴 시간 제한**에서, 유휴 시간 제한까지의 시간(분)을 지정합니다.
8. **부동 IP**에 대해 Disabled이나 Enabled을 선택합니다.
9. **확인**을 클릭해 규칙을 만듭니다.

### <a name="step-5-connect-web-tier-vms-to-load-balancer"></a>5단계: Load Balancer에 웹 계층 VM 연결

이제 모든 데이터베이스 연결에 대해 웹 계층 VM에서 실행 중인 응용 프로그램의 IP 주소 및 Load Balancer 프런트엔드 포트를 구성합니다. 이 구성은 이러한 VM에서 실행되는 응용 프로그램에 특정합니다. 대상 IP 주소와 포트를 구성하려면 자세한 내용은 응용 프로그램 설명서를 참조하세요. 프런트 엔드의 IP 주소를 찾으려면 Azure Portal의 Load Balancer 설정 블레이드에서 프런트 엔드 IP 풀로 이동합니다.

![부하 분산 장치 프런트 엔드 ip 풀](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>다음 단계

* [Traffic Manager 개요](traffic-manager-overview.md)
* [Application Gateway 개요](../application-gateway/application-gateway-introduction.md)
* [Azure Load Balancer개요](../load-balancer/load-balancer-overview.md)



<!--HONumber=Nov16_HO3-->


