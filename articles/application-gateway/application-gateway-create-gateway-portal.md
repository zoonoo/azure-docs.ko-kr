---
title: "포털을 사용하여 Application Gateway 만들기 | Microsoft Docs"
description: "포털을 사용하여 응용 프로그램 게이트웨이를 만드는 방법을 알아봅니다."
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 54dffe95-d802-4f86-9e2e-293f49bd1e06
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/12/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 9edaa7a101ae0e1a395491999854ee7009fb69cd
ms.lasthandoff: 03/30/2017


---
# <a name="create-an-application-gateway-by-using-the-portal"></a>포털을 사용하여 응용 프로그램 게이트웨이 만들기

> [!div class="op_single_selector"]
> * [쉬운 테이블](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
> * [Azure 클래식 PowerShell](application-gateway-create-gateway.md)
> * [Azure Resource Manager 템플릿](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI](application-gateway-create-gateway-cli.md)

Azure 응용 프로그램 게이트웨이는 계층 7 부하 분산 장치입니다. 클라우드 또는 온-프레미스이든 상관없이 서로 다른 서버 간에 장애 조치(Failover), 성능 라우팅 HTTP 요청을 제공합니다.
응용 프로그램 게이트웨이는 HTTP 부하 분산, 쿠키 기반 세션 선호도, SSL(Secure Sockets Layer) 오프로드, 사용자 지정 상태 프로브, 다중 사이트 지원 및 기타를 포함하여 많은 ADC(Application Delivery Controller)를 제공합니다.

지원되는 기능의 전체 목록을 찾으려면 [Application Gateway 개요](application-gateway-introduction.md)

## <a name="scenario"></a>시나리오

이 시나리오에서는 Azure 포털을 사용하여 응용 프로그램 게이트웨이를 만드는 방법을 알아봅니다.

이 시나리오에서는 다음을 수행합니다.

* 두 인스턴스를 사용하여 중간 응용 프로그램 게이트웨이를 만듭니다.
* 예약된 CIDR 블록이 10.0.0.0/16이고 이름이 AdatumAppGatewayVNET인 가상 네트워크를 만듭니다.
* CIDR 블록으로 10.0.0.0/28을 사용하는 Appgatewaysubnet이라고 하는 서브넷을 만듭니다.
* SSL 오프로드용 인증서를 구성합니다.

![예제 시나리오][scenario]

> [!IMPORTANT]
> 초기 배포 중이 아닌 경우 응용 프로그램 게이트웨이를 구성하면 사용자 지정 상태 프로브, 백 엔드 풀 주소, 추가 규칙 등 응용 프로그램 게이트웨이에 대한 추가 구성이 구성됩니다.

## <a name="before-you-begin"></a>시작하기 전에

Azure 응용 프로그램 게이트웨이에는 자체 서브넷이 필요합니다. 가상 네트워크를 만들 때 여러 서브넷을 둘 수 있는 충분한 주소 공간이 있는지 확인합니다. Application Gateway를 서브넷에 배포한 경우 추가 Application Gateway를 서브넷에 추가할 수 있습니다.

## <a name="create-the-application-gateway"></a>응용 프로그램 게이트웨이 만들기

### <a name="step-1"></a>1단계

Azure 포털로 이동하여 **새로 만들기** > **네트워킹** > **응용 프로그램 게이트웨이**를 클릭합니다.

![Application Gateway 만들기][1]

### <a name="step-2"></a>2단계

응용 프로그램 게이트웨이에 대한 기본 정보를 입력합니다. 완료되면 **확인**

기본 설정에 필요한 정보는 다음과 같습니다.

* **이름** - 응용 프로그램 게이트웨이의 이름입니다.
* **계층** - 이 설정은 Application Gateway의 계층입니다. **WAF** 및 **표준**의 두 개의 계층을 사용할 수 있습니다. WAF에는 웹 응용 프로그램 방화벽 기능이 있습니다.
* **SKU 크기** - 이 설정은 Application Gateway의 크기입니다. 사용 가능한 옵션은 (**소량**, **중간** 및 **대량**)입니다. WAF 계층을 선택할 때 소량은 사용할 수 없습니다.
* **인스턴스 수** - 인스턴스 수입니다. 이 값은 2에서 10 사이의 숫자여야 합니다.
* **리소스 그룹** - Application Gateway를 유지할 리소스 그룹입니다. 기존 리소스 그룹 또는 새 리소스 그룹일 수 있습니다.
* **위치** - Application Gateway의 지역입니다. 리소스 그룹에서의 위치와 같은 위치입니다. 위치는 가상 네트워크와 공용 IP가 게이트웨이와 동일한 위치에 있어야 하므로 중요합니다.

![기본 설정이 표시된 블레이드][2]

> [!NOTE]
> 테스트 목적으로 인스턴스 수 1을 선택할 수 있습니다. 그러나 두 개 미만의 인스턴스 수는 SLA에서 다루지 않으므로 권장되지 않는다는 점을 알아야 합니다. 작은 게이트웨이는 개발 테스트용이며 프로덕션용으로는 사용되지 않습니다.
> 
> 

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

### <a name="step-7"></a>7단계

**공용 IP 주소 선택**을 클릭합니다. 기존 공용 IP 주소를 사용할 수 있는 경우 이 시점에서 선택될 수 있지만 이 시나리오에서는 새 공용 IP 주소를 만듭니다. **Create new**

![공용 IP 주소 선택 블레이드][6]

### <a name="step-8"></a>8단계

공용 IP 주소의 이름을 지정하고 **확인**

![공용 IP 주소 만들기 블레이드][7]

### <a name="step-9"></a>9단계

응용 프로그램 게이트웨이를 만들 때 구성할 마지막 설정은 수신기 구성입니다.  **http**가 사용되는 경우 구성할 항목이 더 이상 없으므로 **확인**을 클릭하면 됩니다. **https** 를 사용하려면 추가 구성이 필요합니다.

**https**를 사용하려면 인증서가 필요합니다. 인증서의 개인 키가 필요하므로 인증서의 .pfx 내보내기 및 암호를 제공해야 합니다.

### <a name="step-10"></a>10단계

**HTTPS**를 클릭한 다음 **PFX 인증서 업로드** 텍스트 상자 옆의 **폴더** 아이콘을 클릭합니다.
파일 시스템에서 .pfx 인증서 파일로 이동합니다. 선택한 후 인증서의 이름을 지정하고 .pfx 파일에 대한 암호를 입력합니다.

완료되면 **확인** 을 클릭하여 응용 프로그램 게이트웨이에 대한 설정을 검토합니다.

![설정 블레이드의 수신기 구성 섹션][9]

### <a name="step-11"></a>11단계

요약 페이지를 검토하고 **확인**을 클릭합니다.  이제 응용 프로그램 게이트웨이가 만들어지고 대기 상태가 됩니다.

### <a name="step-12"></a>12단계

응용 프로그램 게이트웨이를 만든 후에는 포털에서 해당 응용 프로그램 게이트웨이로 이동하여 응용 프로그램 게이트웨이 구성을 계속합니다.

![응용 프로그램 게이트웨이 리소스 보기][10]

이러한 단계에서는 수신기, 백 엔드 풀, 백 엔드 http 설정 및 규칙에 대한 기본 설정으로 기본 Application Gateway를 만듭니다. 프로비전에 성공하면 배포에 맞게 이러한 설정을 수정할 수 있습니다.

## <a name="add-servers-to-backend-pools"></a>백 엔드 풀에 서버 추가

Application Gateway가 만들어지면 부하 분산을 위해 응용 프로그램을 호스트하는 시스템을 여전히 Application Gateway에 추가해야 합니다. 이러한 서버의 IP 주소 또는 FQDN 값은 백 엔드 주소 풀에 추가됩니다.

### <a name="ip-address-or-fqdn"></a>IP 주소 또는 FQDN

#### <a name="step-1"></a>1단계

만든 Application Gateway를 클릭하고 **백 엔드 풀**을 클릭한 후 현재 백 엔드 풀을 선택합니다.

![Application Gateway 백 엔드 풀][11]

#### <a name="step-2"></a>2단계

**대상 추가**를 클릭하여 FQDN 값의 IP 주소 추가

![Application Gateway 백 엔드 풀][11-1]

#### <a name="step-3"></a>3단계

모든 백 엔드 값을 입력한 후 **저장** 클릭

![Application Gateway 백 엔드 풀에 값을 추가합니다.][12]

이 작업은 값을 백 엔드 풀에 저장합니다. Application Gateway가 업데이트된 후 Application Gateway로 들어가는 트래픽은 이 단계에서 추가한 백 엔드 주소로 라우팅됩니다.

### <a name="virtual-machine-and-nic"></a>가상 컴퓨터 및 NIC

백 엔드 풀 구성원으로 가상 컴퓨터 NIC를 추가할 수도 있습니다. Application Gateway로써 같은 가상 네트워크에 있는 가상 컴퓨터만 드롭다운을 통해 사용할 수 있습니다.

#### <a name="step-1"></a>1단계

만든 Application Gateway를 클릭하고 **백 엔드 풀**을 클릭한 후 현재 백 엔드 풀을 선택합니다.

![Application Gateway 백 엔드 풀][11]

#### <a name="step-2"></a>2단계

**대상 추가**를 클릭하여 새 백 엔드 풀 구성원을 추가합니다. 드롭다운 상자에서 가상 컴퓨터와 NIC를 선택합니다.

![Application Gateway 백 엔드 풀에 nics 추가][13]

#### <a name="step-3"></a>3단계

완료되면 **저장**을 클릭하여 NIC를 백 엔드 구성원으로 저장합니다.

![nic Application Gateway 백 엔드 풀 저장][14]

## <a name="next-steps"></a>다음 단계

이 시나리오는 기본 응용 프로그램 게이트웨이를 만듭니다. 다음 단계에서는 설정을 수정하고, 게이트웨이의 규칙을 조정하여 Application Gateway를 구성합니다. 이러한 단계는 다음 문서에서 확인할 수 있습니다.

[사용자 지정 상태 프로브 만들기](application-gateway-create-probe-portal.md)

[SSL 오프로드 구성](application-gateway-ssl-portal.md)

응용 프로그램 게이트웨이의 기능인 [웹 응용 프로그램 방화벽](application-gateway-webapplicationfirewall-overview.md)을 사용하여 웹 사이트를 보호하는 방법을 알아봅니다.

<!--Image references-->
[1]: ./media/application-gateway-create-gateway-portal/figure1.png
[2]: ./media/application-gateway-create-gateway-portal/figure2.png
[3]: ./media/application-gateway-create-gateway-portal/figure3.png
[4]: ./media/application-gateway-create-gateway-portal/figure4.png
[5]: ./media/application-gateway-create-gateway-portal/figure5.png
[6]: ./media/application-gateway-create-gateway-portal/figure6.png
[7]: ./media/application-gateway-create-gateway-portal/figure7.png
[8]: ./media/application-gateway-create-gateway-portal/figure8.png
[9]: ./media/application-gateway-create-gateway-portal/figure9.png
[10]: ./media/application-gateway-create-gateway-portal/figure10.png
[11]: ./media/application-gateway-create-gateway-portal/figure11.png
[11-1]: ./media/application-gateway-create-gateway-portal/figure11-1.png
[12]: ./media/application-gateway-create-gateway-portal/figure12.png
[13]: ./media/application-gateway-create-gateway-portal/figure13.png
[14]: ./media/application-gateway-create-gateway-portal/figure14.png
[scenario]: ./media/application-gateway-create-gateway-portal/scenario.png

