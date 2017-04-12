---
title: "웹 응용 프로그램 방화벽이 있는 Azure Application Gateway 만들기 또는 업데이트 | Microsoft Docs"
description: "포털을 사용하여 웹 응용 프로그램 방화벽이 있는 Application Gateway를 만드는 방법에 대해 알아보기"
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: b561a210-ed99-4ab4-be06-b49215e3255a
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/03/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 9f16384a3944c3943dbfc094aaba37a24969e949
ms.lasthandoff: 03/30/2017


---

# <a name="create-an-application-gateway-with-web-application-firewall-by-using-the-portal"></a>포털을 사용하여 웹 응용 프로그램 방화벽이 있는 Application Gateway 만들기

> [!div class="op_single_selector"]
> * [Azure 포털](application-gateway-web-application-firewall-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-web-application-firewall-powershell.md)

Azure Application Gateway의 웹 응용 프로그램 방화벽(WAF)은 SQL 삽입 공격, 사이트 간 스크립팅 공격, 세션 하이재킹 등의 일반적인 웹 기반 공격으로부터 웹 응용 프로그램을 보호합니다. 웹 응용 프로그램은 다양한 OWASP 상위 10 일반적인 웹 취약점으로부터 보호합니다.

Azure 응용 프로그램 게이트웨이는 계층 7 부하 분산 장치입니다. 클라우드 또는 온-프레미스이든 상관없이 서로 다른 서버 간에 장애 조치(Failover), 성능 라우팅 HTTP 요청을 제공합니다.
응용 프로그램은 HTTP 부하 분산, 쿠키 기반 세션 선호도, SSL(Secure Sockets Layer) 오프로드, 사용자 지정 상태 프로브, 다중 사이트 지원 및 기타를 포함하여 많은 ADC(Application Delivery Controller)를 제공합니다.
지원되는 기능의 전체 목록을 찾으려면 [Application Gateway 개요](application-gateway-introduction.md)

## <a name="scenarios"></a>시나리오

이 문서에는 두 가지 시나리오가 있습니다.

첫 번째 시나리오에서는 [기존 Application Gateway에 웹 응용 프로그램 방화벽을 추가하는 방법](#add-web-application-firewall-to-an-existing-application-gateway)에 대해 알아봅니다.

두 번째 시나리오에서는 [웹 응용 프로그램 방화벽을 사용하여 Application Gateway를 만드는 방법](#create-an-application-gateway-with-web-application-firewall)

![예제 시나리오][scenario]

> [!NOTE]
> 초기 배포 중이 아닌 경우 응용 프로그램 게이트웨이를 구성하면 사용자 지정 상태 프로브, 백 엔드 풀 주소, 추가 규칙 등 응용 프로그램 게이트웨이에 대한 추가 구성이 구성됩니다.

## <a name="before-you-begin"></a>시작하기 전에

Azure 응용 프로그램 게이트웨이에는 자체 서브넷이 필요합니다. 가상 네트워크를 만들 때 여러 서브넷을 둘 수 있는 충분한 주소 공간이 있는지 확인합니다. Application Gateway를 서브넷에 배포한 경우 추가 Application Gateway를 서브넷에 추가할 수 있습니다.

##<a name="add-web-application-firewall-to-an-existing-application-gateway"></a> 기존 Application Gateway에 웹 응용 프로그램 방화벽 추가

이 시나리오에서는 방지 모드에서 웹 응용 프로그램 방화벽을 지원하도록 기존 Application Gateway를 업데이트합니다.

### <a name="step-1"></a>1단계

Azure Portal로 이동하여 기존 Application Gateway를 선택합니다.

![Application Gateway 만들기][1]

### <a name="step-2"></a>2단계

**웹 응용 프로그램 방화벽**을 클릭하고 응용 프로그램 게이트웨이 설정을 업데이트합니다. 완료되면 **저장**

웹 응용 프로그램 방화벽을 지원하도록 기존 Application Gateway를 업데이트하는 설정은 다음과 같습니다.

* **WAF 계층으로 업그레이드** -WAF를 구성하려면 이 설정이 필요합니다.
* **방화벽 상태** - 이 설정은 웹 응용 프로그램 방화벽을 비활성화 또는 활성화합니다.
* **방화벽 모드** - 웹 응용 프로그램 방화벽이 악의적인 트래픽을 처리하는 방식에 대한 설정입니다. **검색** 모드는 이벤트를 기록하기만 하는 반면 **방지** 모드는 이벤트를 기록하고 악의적인 트래픽을 중지합니다.
* **규칙 집합** - 이 설정은 백 엔드 풀 멤버를 보호하는 데 사용되는 [코어 규칙 집합](application-gateway-web-application-firewall-overview.md#core-rule-sets)을 결정합니다.
* **사용하지 않는 규칙 구성** -가능한 가양성을 방지하기 위해 이 설정은 특정 [규칙 및 규칙 그룹](application-gateway-crs-rulegroups-rules.md)을 사용하지 않도록 설정할 수 있도록 합니다.

>[!NOTE]
> 기존 응용 프로그램 게이트웨이를 WAF SKU로 업그레이드할 때 SKU 크기는 **중형**으로 변경됩니다. 구성이 완료된 후 이 크기를 다시 구성할 수 있습니다.

![기본 설정이 표시된 블레이드][2]

> [!NOTE]
> 웹 응용 프로그램 방화벽 로그를 보려면 진단을 활성화하고 ApplicationGatewayFirewallLog를 선택해야 합니다. 테스트 목적으로 인스턴스 수 1을 선택할 수 있습니다. 그러나 두 개 미만의 인스턴스 수는 SLA에서 다루지 않으므로 권장되지 않는다는 점을 알아야 합니다. 웹 응용 프로그램 방화벽을 사용하는 경우 소형 게이트웨이를 사용할 수 없습니다.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>웹 응용 프로그램 방화벽을 사용하여 Application Gateway를 만드는 방법

이 시나리오에서는 다음을 수행합니다.

* 두 인스턴스를 사용하여 중형 웹 응용 프로그램 방화벽 Application Gateway를 만듭니다.
* 예약된 CIDR 블록이 10.0.0.0/16이고 이름이 AdatumAppGatewayVNET인 가상 네트워크를 만듭니다.
* CIDR 블록으로 10.0.0.0/28을 사용하는 Appgatewaysubnet이라고 하는 서브넷을 만듭니다.
* SSL 오프로드용 인증서를 구성합니다.

### <a name="step-1"></a>1단계

Azure 포털로 이동하여 **새로 만들기** > **네트워킹** > **응용 프로그램 게이트웨이**를 클릭합니다.

![Application Gateway 만들기][1-1]

### <a name="step-2"></a>2단계

응용 프로그램 게이트웨이에 대한 기본 정보를 입력합니다. 계층으로 **WAF** 를 선택해야 합니다. 완료되면 **확인**

기본 설정에 필요한 정보는 다음과 같습니다.

* **이름** - 응용 프로그램 게이트웨이의 이름입니다.
* **계층** - Application Gateway의 계층이며, 사용 가능한 옵션은 (**표준** 및 **WAF**)입니다. 웹 응용 프로그램 방화벽은 WAF 계층에만 사용할 수 있습니다.
* **SKU 크기** - 이 설정은 Application Gateway의 크기입니다. 사용 가능한 옵션은 (**중형** 및 **대형**)입니다.
* **인스턴스 수** - 인스턴스 수입니다. 이 값은 **2**에서 **10** 사이의 숫자여야 합니다.
* **리소스 그룹** - Application Gateway를 유지할 리소스 그룹입니다. 기존 리소스 그룹 또는 새 리소스 그룹일 수 있습니다.
* **위치** - Application Gateway의 지역입니다. 리소스 그룹에서의 위치와 같은 위치입니다. *위치는 가상 네트워크와 공용 IP가 게이트웨이와 동일한 위치에 있어야 하므로 중요합니다*.

![기본 설정이 표시된 블레이드][2-2]

> [!NOTE]
> 테스트 목적으로 인스턴스 수 1을 선택할 수 있습니다. 그러나 두 개 미만의 인스턴스 수는 SLA에서 다루지 않으므로 권장되지 않는다는 점을 알아야 합니다. 웹 응용 프로그램 방화벽 시나리오에는 소형 게이트웨이가 지원되지 않습니다.

### <a name="step-3"></a>3단계

기본 설정을 정의한 후에는 사용할 가상 네트워크를 정의합니다. 가상 네트워크는 Application Gateway에서 부하를 분산하는 응용 프로그램을 수용합니다.

**가상 네트워크 선택** 을 클릭하여 가상 네트워크를 구성합니다.

![응용 프로그램 게이트웨이에 대한 설정이 표시된 블레이드][3]

### <a name="step-4"></a>4단계

**가상 네트워크 선택** 블레이드에서 **새로 만들기**

이 시나리오에는 설명되어 있지 않지만 여기서 기존 Virtual Network를 선택할 수도 있습니다.  기존 가상 네트워크를 사용하는 경우 해당 가상 네트워크에 사용할 빈 서브넷 또는 Application Gateway 리소스의 서브넷이 필요합니다.

![가상 네트워크 선택 블레이드][4]

### <a name="step-5"></a>5단계

위 **시나리오** 설명에 따라 [가상 네트워크 만들기](#scenario) 블레이드에서 네트워크 정보를 입력합니다.

![정보가 입력된 가상 네트워크 만들기 블레이드][5]

### <a name="step-6"></a>6단계

가상 네트워크를 만든 후에는 응용 프로그램 게이트웨이에 대한 프런트 엔드 IP를 정의합니다. 이 시점에서는 프런트 엔드에 대해 공용 또는 개인 IP 주소를 선택합니다. 응용 프로그램이 인터넷 연결용인지 내부용인지에 따라 선택이 달라집니다. 이 시나리오에서는 공용 IP 주소를 사용하는 것으로 가정합니다. 개인 IP 주소를 선택하려면 **개인** 단추를 클릭하면 됩니다. 자동으로 할당된 IP 주소가 선택되거나, **특정 개인 IP 주소 선택** 확인란을 클릭하여 수동으로 입력할 수 있습니다.

**공용 IP 주소 선택**을 클릭합니다. 기존 공용 IP 주소를 사용할 수 있는 경우 이 시점에서 선택될 수 있지만 이 시나리오에서는 새 공용 IP 주소를 만듭니다. **Create new**

![공용 IP 주소 선택 블레이드][6]

### <a name="step-7"></a>7단계

공용 IP 주소의 이름을 지정하고 **확인**

![공용 IP 주소 만들기 블레이드][7]

### <a name="step-8"></a>8단계

다음으로 수신기 구성을 설정합니다.  **http**가 사용되는 경우 구성할 항목이 더 이상 없으므로 **확인**을 클릭하면 됩니다. **https** 를 사용하려면 추가 구성이 필요합니다.

**https**를 사용하려면 인증서가 필요합니다. 인증서의 개인 키가 필요하므로 인증서의 .pfx 내보내기 및 파일의 암호를 제공해야 합니다.

**HTTPS**를 클릭한 다음 **PFX 인증서 업로드** 텍스트 상자 옆의 **폴더** 아이콘을 클릭합니다.
파일 시스템에서 .pfx 인증서 파일로 이동합니다. 선택한 후 인증서의 이름을 지정하고 .pfx 파일에 대한 암호를 입력합니다.

완료되면 **확인** 을 클릭하여 응용 프로그램 게이트웨이에 대한 설정을 검토합니다.

![설정 블레이드의 수신기 구성 섹션][8]

### <a name="step-9"></a>9단계

**WAF** 설정을 구성합니다.

* **방화벽 상태** - 이 설정은 WAF를 켜고 끕니다.
* **방화벽 모드** - 이 설정은 WAF가 악의적인 트래픽에 대해 수행하는 작업을 결정합니다. **검색** 을 선택하면 트래픽이 기록되기만 합니다.  **방지**를 선택하면 트래픽이 기록되고 403 권한 없음 응답에 따라 중지됩니다.

![웹 응용 프로그램 방화벽 설정][9]

### <a name="step-10"></a>10단계

요약 페이지를 검토하고 **확인**을 클릭합니다.  이제 응용 프로그램 게이트웨이가 만들어지고 대기 상태가 됩니다.

### <a name="step-11"></a>11단계

응용 프로그램 게이트웨이를 만든 후에는 포털에서 해당 응용 프로그램 게이트웨이로 이동하여 응용 프로그램 게이트웨이 구성을 계속합니다.

![응용 프로그램 게이트웨이 리소스 보기][10]

이러한 단계에서는 수신기, 백 엔드 풀, 백 엔드 http 설정 및 규칙에 대한 기본 설정으로 기본 Application Gateway를 만듭니다. 프로비전에 성공하면 배포에 맞게 이러한 설정을 수정할 수 있습니다.

> [!NOTE]
> 기본 웹 응용 프로그램 방화벽 구성을 사용하여 만든 응용 프로그램 게이트웨이는 보호를 위해 CRS 3.0으로 구성됩니다.

## <a name="next-steps"></a>다음 단계

[Application Gateway 진단](application-gateway-diagnostics.md)을 방문하여 진단 로깅을 구성하는 방법 및 웹 응용 프로그램 방화벽을 통해 검색 또는 방지되는 이벤트를 기록하는 방법에 대해 알아보기

[사용자 지정 상태 프로브 만들기](application-gateway-create-probe-portal.md)

[SSL 오프로드 구성](application-gateway-ssl-portal.md)

<!--Image references-->
[1]: ./media/application-gateway-web-application-firewall-portal/figure1.png
[2]: ./media/application-gateway-web-application-firewall-portal/figure2.png
[1-1]: ./media/application-gateway-web-application-firewall-portal/figure1-1.png
[2-2]: ./media/application-gateway-web-application-firewall-portal/figure2-2.png
[3]: ./media/application-gateway-web-application-firewall-portal/figure3.png
[4]: ./media/application-gateway-web-application-firewall-portal/figure4.png
[5]: ./media/application-gateway-web-application-firewall-portal/figure5.png
[6]: ./media/application-gateway-web-application-firewall-portal/figure6.png
[7]: ./media/application-gateway-web-application-firewall-portal/figure7.png
[8]: ./media/application-gateway-web-application-firewall-portal/figure8.png
[9]: ./media/application-gateway-web-application-firewall-portal/figure9.png
[10]: ./media/application-gateway-web-application-firewall-portal/figure10.png
[scenario]: ./media/application-gateway-web-application-firewall-portal/scenario.png

