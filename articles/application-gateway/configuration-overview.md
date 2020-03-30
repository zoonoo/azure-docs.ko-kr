---
title: Azure 응용 프로그램 게이트웨이 구성 개요
description: 이 문서에서는 Azure 응용 프로그램 게이트웨이의 구성 요소를 구성하는 방법을 설명합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: absha
ms.openlocfilehash: f31c24c96732ec3311ea904fc9c63344e2d14109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371249"
---
# <a name="application-gateway-configuration-overview"></a>애플리케이션 게이트웨이 구성 개요

Azure 응용 프로그램 게이트웨이는 다양한 시나리오에 대해 다양한 방법으로 구성할 수 있는 여러 구성 요소로 구성됩니다. 이 문서에서는 각 구성 요소를 구성하는 방법을 보여 주며 이 문서에서는

![응용 프로그램 게이트웨이 구성 요소 순서도 차트](./media/configuration-overview/configuration-overview1.png)

이 이미지는 세 개의 수신기가 있는 응용 프로그램을 보여 줍니다. 처음 두 개는 각각 및 `http://acme.com/*` `http://fabrikam.com/*`에 대한 다중 사이트 리스너입니다. 둘 다 포트 80에서 듣습니다. 세 번째는 이전에 SSL(보안 소켓 계층) 종료라고 하는 TLS(종단 간 전송 계층 보안) 종료가 있는 기본 수신기입니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>사전 요구 사항

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Azure 가상 네트워크 및 전용 서브넷

응용 프로그램 게이트웨이는 가상 네트워크의 전용 배포입니다. 가상 네트워크 내에서 응용 프로그램 게이트웨이에 전용 서브넷이 필요합니다. 서브넷에 지정된 응용 프로그램 게이트웨이 배포의 여러 인스턴스를 가질 수 있습니다. 서브넷에 다른 응용 프로그램 게이트웨이를 배포할 수도 있습니다. 그러나 응용 프로그램 게이트웨이 서브넷에 다른 리소스를 배포할 수 없습니다.

> [!NOTE]
> 동일한 서브넷에서 Standard_v2 표준 Azure 응용 프로그램 게이트웨이를 혼합할 수 없습니다.

#### <a name="size-of-the-subnet"></a>서브넷 크기

응용 프로그램 게이트웨이는 인스턴스당 하나의 개인 IP 주소와 개인 프런트 엔드 IP가 구성된 경우 다른 개인 IP 주소를 사용합니다.

또한 Azure는 내부 사용을 위해 각 서브넷에 5개의 IP 주소(처음 4개 및 마지막 IP 주소)를 예약합니다. 예를 들어 개인 프런트 엔드 IP가 없는 15개의 응용 프로그램 게이트웨이 인스턴스를 고려해 보십시오. 이 서브넷에는 최소 20개의 IP 주소가 필요합니다: 내부 용 5개, 응용 프로그램 게이트웨이 인스턴스의 경우 15개입니다. 따라서 /27 서브넷 크기 이상이 필요합니다.

27개의 응용 프로그램 게이트웨이 인스턴스와 개인 프런트 엔드 IP에 대한 IP 주소가 있는 서브넷을 고려합니다. 이 경우 33개의 IP 주소가 필요합니다: 응용 프로그램 게이트웨이 인스턴스의 경우 27개, 개인 프런트 엔드에 대해 하나, 내부용으로 는 5개의 IP 주소가 필요합니다. 따라서 /26 서브넷 크기 이상이 필요합니다.

최소 /28의 서브넷 크기를 사용하는 것이 좋습니다. 이 크기는 11개의 사용 가능한 IP 주소를 제공합니다. 응용 프로그램 로드에 10개 이상의 응용 프로그램 게이트웨이 인스턴스가 필요한 경우 /27 또는 /26 서브넷 크기를 고려하십시오.

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>응용 프로그램 게이트웨이 서브넷의 네트워크 보안 그룹

NSG(네트워크 보안 그룹)는 응용 프로그램 게이트웨이에서 지원됩니다. 그러나 몇 가지 제한 사항이 있습니다.

- 응용 프로그램 게이트웨이 v1 SKU의 경우 TCP 포트 65503-65534에서 들어오는 인터넷 트래픽을 허용해야 하며, 대상 서브넷이 있는 v2 SKU의 경우 TCP 포트 65200-65535를 **임의로** 허용하고 **게이트웨이 관리자** 서비스 태그로 소스로 원본을 제공해야 합니다. 이 포트 범위는 Azure 인프라 통신에 필요합니다. 이러한 포트는 Azure 인증서에 의해 보호(잠기)됩니다. 이러한 게이트웨이의 고객을 포함한 외부 엔터티는 이러한 끝점에서 통신할 수 없습니다.

- 아웃바운드 인터넷 연결은 차단할 수 없습니다. NSG의 기본 아웃바운드 규칙은 인터넷 연결을 허용합니다. 다음을 수행하는 것이 좋습니다.

  - 기본 아웃바운드 규칙을 제거하지 마십시오.
  - 아웃바운드 연결을 거부하는 다른 아웃바운드 규칙을 만들지 마십시오.

- **AzureLoadBalancer** 태그의 트래픽은 허용되어야 합니다.

#### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>몇 개의 소스 IP에 대한 애플리케이션 게이트웨이 액세스 허용

이 시나리오에서는 응용 프로그램 게이트웨이 서브넷에서 NSG를 사용합니다. 서브넷에 다음과 같은 제한을 우선 순위 순서로 두십시오.

1. 전체 응용 프로그램 게이트웨이 서브넷 주소 범위 및 대상 포트를 인바운드 액세스 포트(예: HTTP 액세스에 대한 포트 80)로 대상과 함께 원본 IP 또는 IP 범위에서 들어오는 트래픽을 허용합니다.
2. 응용 프로그램 게이트웨이 v1 SKU에 대해 **Any** 및 대상 포트로 **게이트웨이Manager** 서비스 태그 및 대상으로 소스에서 들어오는 요청을 허용하고 [백 엔드 상태 통신을](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)위해 v2 SKU의 포트 65200-65535를 허용합니다. 이 포트 범위는 Azure 인프라 통신에 필요합니다. 이러한 포트는 Azure 인증서에 의해 보호(잠기)됩니다. 적절한 인증서가 없으면 외부 엔터티는 해당 끝점에 대한 변경을 시작할 수 없습니다.
3. [네트워크 보안 그룹에](https://docs.microsoft.com/azure/virtual-network/security-overview)들어오는 Azure Load Balancer 프로브(AzureLoadBalancer 태그) 및 인바운드 가상 네트워크 트래픽(가상*네트워크* 태그)을 허용합니다.*AzureLoadBalancer*
4. 거부-모든 규칙을 사용하여 다른 모든 들어오는 트래픽을 차단합니다.
5. 모든 대상에 대해 인터넷으로의 아웃바운드 트래픽을 허용합니다.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Application Gateway 서브넷에서 지원되는 사용자 정의 경로

> [!IMPORTANT]
> 응용 프로그램 게이트웨이 서브넷에서 DR을 사용하면 [백 엔드 상태 보기의](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) 상태 상태가 **알 수 없음으로**표시될 수 있습니다. 또한 응용 프로그램 게이트웨이 로그 및 메트릭 생성이 실패할 수도 있습니다. 백 엔드 상태, 로그 및 메트릭을 볼 수 있도록 응용 프로그램 게이트웨이 서브넷에서 DR을 사용하지 않는 것이 좋습니다.

- **v1**

   v1 SKU의 경우 종단 간 요청/응답 통신을 변경하지 않는 한 응용 프로그램 게이트웨이 서브넷에서 사용자 정의 경로(DR)가 지원됩니다. 예를 들어 응용 프로그램 게이트웨이 서브넷에서 패킷 검사를 위해 방화벽 어플라이언스를 가리키도록 UDR을 설정할 수 있습니다. 그러나 패킷이 검사 후 의도한 대상에 도달할 수 있는지 확인해야 합니다. 이렇게 하지 않으면 잘못된 상태 프로브 또는 트래픽 라우팅 동작이 발생할 수 있습니다. 여기에는 학습된 경로 또는 Azure ExpressRoute 또는 가상 네트워크의 VPN 게이트웨이에 의해 전파되는 기본 0.0.0.0/0 경로가 포함됩니다.

- **v2**

   v2 SKU의 경우 지원및 지원되지 않는 시나리오가 있습니다.

   **v2 지원 시나리오**
   > [!WARNING]
   > 라우트 테이블을 잘못 구성하면 응용 프로그램 게이트웨이 v2에서 비대칭 라우팅이 발생할 수 있습니다. 모든 관리/제어 평면 트래픽이 가상 어플라이언스를 통하지 않고 인터넷으로 직접 전송되도록 합니다. 로깅 및 메트릭도 영향을 받을 수 있습니다.


  **시나리오 1**: UDR이 애플리케이션 게이트웨이 서브넷으로 의 경계 게이트웨이 프로토콜(BGP) 경로 전파를 비활성화합니다.

   경우에 따라 기본 게이트웨이 경로(0.0.0.0/0)가 응용 프로그램 게이트웨이 가상 네트워크와 연결된 ExpressRoute 또는 VPN 게이트웨이를 통해 보급됩니다. 이렇게 하면 인터넷에 대한 직접 경로가 필요한 관리 평면 트래픽이 중단됩니다. 이러한 시나리오에서는 UDR을 사용하여 BGP 경로 전파를 비활성화할 수 있습니다. 

   BGP 경로 전파를 사용하지 않으려면 다음 단계를 따르세요.

   1. Azure에서 라우트 테이블 리소스를 만듭니다.
   2. 가상 **네트워크 게이트웨이 경로 전파** 매개 변수를 사용하지 않도록 설정합니다. 
   3. 경로 테이블을 적절한 서브넷에 연결합니다. 

   이 시나리오에서 UDR을 사용하도록 설정하면 기존 설정이 중단되지 않습니다.

  **시나리오 2**: UDR이 0.0.0.0/0을 인터넷으로 직접

   UDR을 만들어 0.0.0.0/0 트래픽을 인터넷으로 직접 보낼 수 있습니다. 

  **시나리오 3**: Azure Kubernetes 서비스 쿠베넷에 대한 UDR

  AKS(Azure Kubernetes Service) 및 응용 프로그램 게이트웨이 침투 컨트롤러(AGIC)를 사용하여 kubenet을 사용하는 경우 포드로 전송된 트래픽이 올바른 노드로 라우팅될 수 있도록 경로 테이블을 설정해야 합니다. Azure CNI를 사용하는 경우에는 이 작업을 수행할 필요가 없습니다. 

   kubenet이 작동하도록 경로 테이블을 설정하려면 다음 단계를 사용합니다.

  1. Azure에서 라우트 테이블 리소스를 만듭니다. 
  2. 생성된 후 **경로** 페이지로 이동합니다. 
  3. 새 경로 추가:
     - 주소 접두사는 AKS에서 도달하려는 포드의 IP 범위여야 합니다. 
     - 다음 홉 유형은 **가상 어플라이언스여야**합니다. 
     - 다음 홉 주소는 주소 접두사 필드에 정의된 IP 범위 내에서 포드를 호스팅하는 노드의 IP 주소여야 합니다. 
    
  **v2 지원되지 않는 시나리오**

  **시나리오 1**: 가상 어플라이언스용 UDR

  가상 어플라이언스, 허브/스포크 가상 네트워크 또는 온-프레미스(강제 터널링)를 통해 0.0.0.0/0을 리디렉션해야 하는 모든 시나리오는 v2 공개 미리 보기에 는 지원되지 않습니다. 

## <a name="front-end-ip"></a>프런트 엔드 IP

공용 IP 주소, 개인 IP 주소 또는 둘 다있도록 응용 프로그램 게이트웨이를 구성할 수 있습니다. 클라이언트가 인터넷을 통해 액세스해야 하는 백 엔드를 호스팅할 때 공용 IP가 필요합니다. 

인터넷에 노출되지 않는 내부 끝점에는 공용 IP가 필요하지 않습니다. 이를 *ILB(내부 로드 밸런서)* 엔드포인트 또는 프라이빗 프런트 엔드 IP라고 합니다. 응용 프로그램 게이트웨이 ILB는 인터넷에 노출되지 않는 내부 업무용 응용 프로그램에 유용합니다. 또한 인터넷에 노출되지 않지만 라운드 로빈 로드 배포, 세션 끈기 또는 TLS 종료가 필요한 보안 경계 내의 다중 계층 응용 프로그램의 서비스 및 계층에도 유용합니다.

공용 IP 주소가 1개또는 개인 IP 주소가 하나만 지원됩니다. 응용 프로그램 게이트웨이를 만들 때 프런트 엔드 IP를 선택합니다.

- 공용 IP의 경우 새 공용 IP 주소를 만들거나 응용 프로그램 게이트웨이와 동일한 위치에 있는 기존 공용 IP를 사용할 수 있습니다. 자세한 내용은 [정적 및 동적 공용 IP 주소를 참조하십시오.](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address)

- 개인 IP의 경우 응용 프로그램 게이트웨이가 생성되는 서브넷에서 개인 IP 주소를 지정할 수 있습니다. 하나를 지정하지 않으면 임의의 IP 주소가 서브넷에서 자동으로 선택됩니다. 선택한 IP 주소 유형(정적 또는 동적)은 나중에 변경할 수 없습니다. 자세한 내용은 [내부 부하 분산조정기가 있는 응용 프로그램 게이트웨이 만들기를](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)참조하십시오.

프런트 엔드 IP 주소는 *프런트*엔드 IP에서 들어오는 요청을 확인하는 수신기에 연결됩니다.

## <a name="listeners"></a>수신기

수신기는 포트, 프로토콜, 호스트 및 IP 주소를 사용하여 들어오는 연결 요청을 확인하는 논리적 엔터티입니다. 수신기를 구성할 때 게이트웨이에서 들어오는 요청의 해당 값과 일치하는 값을 입력해야 합니다.

Azure 포털을 사용하여 응용 프로그램 게이트웨이를 만들 때 수신기에 대한 프로토콜 및 포트를 선택하여 기본 수신기도 만듭니다. 수신기에서 HTTP2 지원을 사용하도록 설정할지 여부를 선택할 수 있습니다. 응용 프로그램 게이트웨이를 만든 후 해당 기본*수신기(appGatewayHttpListener)의*설정을 편집하거나 새 리스너를 만들 수 있습니다.

### <a name="listener-type"></a>리스너 유형

새 수신기를 만들 때 [ *기본* 및 *다중 사이트*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners)중에서 선택합니다.

- 모든 요청(모든 도메인)이 수락되고 백 엔드 풀로 전달되려면 기본을 선택합니다. [기본 수신기를 사용하여 응용 프로그램 게이트웨이를 만드는 방법에](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)대해 알아봅니다.

- *호스트* 헤더 또는 호스트 이름에 따라 다른 백 엔드 풀에 요청을 전달하려는 경우 들어오는 요청과 일치하는 호스트 이름도 지정해야 하는 다중 사이트 수신기를 선택합니다. 이는 응용 프로그램 게이트웨이가 HTTP 1.1 호스트 헤더를 사용하여 동일한 공용 IP 주소 및 포트에서 두 개 이상의 웹 사이트를 호스트하기 때문입니다.

#### <a name="order-of-processing-listeners"></a>처리 리스너 순서

v1 SKU의 경우 요청은 규칙의 순서와 수신기 유형에 따라 일치합니다. 기본 리스너가 있는 규칙이 먼저 순서대로 제공되면 먼저 처리되며 해당 포트와 IP 조합에 대한 모든 요청을 수락합니다. 이를 방지하려면 먼저 다중 사이트 리스너로 규칙을 구성하고 기본 리스너가 목록의 마지막 까지 규칙을 푸시합니다.

v2 SKU의 경우 다중 사이트 리스너가 기본 리스너보다 전에 처리됩니다.

### <a name="front-end-ip"></a>프런트 엔드 IP

이 수신기와 연결할 프런트 엔드 IP 주소를 선택합니다. 수신기는 이 IP에서 들어오는 요청을 듣습니다.

### <a name="front-end-port"></a>프런트 엔드 포트

프런트 엔드 포트를 선택합니다. 기존 포트를 선택하거나 새 포트를 만듭니다. [허용되는 포트 범위에서](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports)값을 선택합니다. 80 및 443과 같은 잘 알려진 포트뿐만 아니라 허용되는 사용자 지정 포트를 사용할 수 있습니다. 포트는 공용 수신기 또는 개인 용 수신기에 사용할 수 있습니다.

### <a name="protocol"></a>프로토콜

HTTP 또는 HTTPS를 선택합니다.

- HTTP를 선택하면 클라이언트와 응용 프로그램 게이트웨이 간의 트래픽이 암호화되지 않습니다.

- [TLS 종료](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssltls-termination) 또는 종단 간 [TLS 암호화를](https://docs.microsoft.com/azure/application-gateway/ssl-overview)원하는 경우 HTTPS를 선택합니다. 클라이언트와 응용 프로그램 게이트웨이 간의 트래픽이 암호화됩니다. 그리고 TLS 연결은 응용 프로그램 게이트웨이에서 종료됩니다. 종단 간 TLS 암호화를 원하는 경우 HTTPS를 선택하고 **백 엔드 HTTP** 설정을 구성해야 합니다. 이렇게 하면 응용 프로그램 게이트웨이에서 백 엔드로 이동할 때 트래픽이 다시 암호화됩니다.

- [TLS 종료](features.md#secure-sockets-layer-ssltls-termination) 또는 종단 간 [TLS 암호화를](https://docs.microsoft.com/azure/application-gateway/ssl-overview)원하는 경우 HTTPS를 선택합니다. 클라이언트와 응용 프로그램 게이트웨이 간의 트래픽이 암호화됩니다. 그리고 TLS 연결은 응용 프로그램 게이트웨이에서 종료됩니다. 종단 간 TLS 암호화를 원하는 경우 HTTPS를 선택하고 **백 엔드 HTTP** 설정을 구성해야 합니다. 이렇게 하면 응용 프로그램 게이트웨이에서 백 엔드로 이동할 때 트래픽이 다시 암호화됩니다.


TLS 종료 및 종단 간 TLS 암호화를 구성하려면 응용 프로그램 게이트웨이가 대칭 키를 파생할 수 있도록 수신기에 인증서를 추가해야 합니다. 이는 TLS 프로토콜 사양에 따라 결정됩니다. 대칭 키는 게이트웨이로 전송되는 트래픽을 암호화하고 해독하는 데 사용됩니다. 게이트웨이 인증서는 개인 정보 교환(PFX) 형식이어야 합니다. 이 형식을 사용하면 게이트웨이에서 트래픽을 암호화하고 해독하는 데 사용하는 개인 키를 내보낼 수 있습니다.

#### <a name="supported-certificates"></a>지원되는 인증서

[TLS 종료에 지원되는 인증서를](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination)참조하십시오.

### <a name="additional-protocol-support"></a>추가 프로토콜 지원

#### <a name="http2-support"></a>HTTP2 지원

HTTP/2 프로토콜 지원은 응용 프로그램 게이트웨이 수신기에만 연결하는 클라이언트에서 사용할 수 있습니다. 백 엔드 서버 풀에 대한 통신은 HTTP/1.1이상입니다. 기본적으로 HTTP/2 지원은 사용할 수 없습니다. 다음 Azure PowerShell 코드 조각에서는 이 기능을 활성화하는 방법을 보여 주며 다음과 같습니다.

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>WebSocket 지원

WebSocket 지원은 기본적으로 활성화되어 있습니다. 사용자가 구성할 수 있는 설정이 없습니다. HTTP 및 HTTPS 수신기모두에서 WebSocket을 사용할 수 있습니다.

### <a name="custom-error-pages"></a>사용자 지정 오류 페이지

전역 수준 또는 수신기 수준에서 사용자 지정 오류를 정의할 수 있습니다. 그러나 Azure Portal에서 전역 수준의 사용자 지정 오류 페이지를 만드는 것은 현재 지원되지 않습니다. 403 웹 응용 프로그램 방화벽 오류에 대한 사용자 지정 오류 페이지 또는 수신기 수준에서 502 유지 관리 페이지를 구성할 수 있습니다. 또한 지정된 오류 상태 코드에 대해 공개적으로 액세스할 수 있는 Blob URL을 지정해야 합니다. 자세한 내용은 [Application Gateway 사용자 지정 오류 페이지 만들기](https://docs.microsoft.com/azure/application-gateway/custom-error)를 참조하세요.

![Application Gateway 오류 코드](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

전역 사용자 지정 오류 페이지를 구성하려면 [Azure PowerShell 구성을](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration)참조하십시오.

### <a name="tls-policy"></a>TLS 정책

TLS/SSL 인증서 관리를 중앙 집중화하고 백 엔드 서버 팜에 대한 암호화 암호 해독 오버헤드를 줄일 수 있습니다. 중앙 집중식 TLS 처리를 사용하면 보안 요구 사항에 적합한 중앙 TLS 정책을 지정할 수도 있습니다. *기본,* 미리 *정의된*또는 *사용자 지정* TLS 정책을 선택할 수 있습니다.

TLS 프로토콜 버전을 제어하도록 TLS 정책을 구성합니다. TLS1.0, TLS1.1 및 TLS1.2의 TLS 핸드셰이크에 대한 최소 프로토콜 버전을 사용하도록 응용 프로그램 게이트웨이를 구성할 수 있습니다. 기본적으로 SSL 2.0 및 3.0은 비활성화되어 있으며 구성할 수 없습니다. 자세한 내용은 [응용 프로그램 게이트웨이 TLS 정책 개요를](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview)참조하십시오.

수신기를 만든 후 요청 라우팅 규칙과 연결합니다. 이 규칙은 수신기에서 수신된 요청이 백 엔드로 라우팅되는 방법을 결정합니다.

## <a name="request-routing-rules"></a>라우팅 규칙 요청

Azure 포털을 사용하여 응용 프로그램 게이트웨이를 만들 때 기본*규칙(rule1)을*만듭니다. 이 규칙은 기본 백 엔드 풀 *(appGatewayBackendPool)과*기본 백 엔드 HTTP 설정 *(appGatewayBackendHttpSettings)과*기본 수신기 *(appGatewayHttpListener)를*바인딩합니다. 게이트웨이를 만든 후 기본 규칙의 설정을 편집하거나 새 규칙을 만들 수 있습니다.

### <a name="rule-type"></a>규칙 유형

규칙을 만들 때 [ *기본* 및 *경로 기반*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules)중에서 선택합니다.

- 연결된 수신기의 모든 요청(예: *블로그<i></i>.contoso.com/)을\** 단일 백 엔드 풀로 전달하려면 기본을 선택합니다.
- 특정 URL 경로에서 특정 백 엔드 풀로 요청을 라우팅하려면 경로 기반을 선택합니다. 경로 패턴은 쿼리 매개 변수가 아닌 URL 경로에만 적용됩니다.

#### <a name="order-of-processing-rules"></a>처리 규칙 의 순서

v1 SKU의 경우 들어오는 요청의 패턴 일치는 경로 기반 규칙의 URL 경로 맵에 경로가 나열되는 순서대로 처리됩니다. 요청이 경로 맵에서 두 개 이상의 경로의 패턴과 일치하는 경우 먼저 나열된 경로가 일치합니다. 요청은 해당 경로와 연결된 백 엔드로 전달됩니다.

v2 SKU의 경우 URL 경로 맵의 경로 순서보다 정확한 일치가 우선 순위가 높습니다. 요청이 두 개 이상의 경로에서 패턴과 일치하는 경우 요청은 요청과 정확히 일치하는 경로와 연결된 백 엔드로 전달됩니다. 들어오는 요청의 경로가 맵의 경로와 정확히 일치하지 않으면 요청의 패턴 일치가 경로 기반 규칙의 경로 맵 순서 목록에서 처리됩니다.

### <a name="associated-listener"></a>연결된 수신기

수신기를 규칙에 연결하여 수신기와 연결된 *요청 라우팅 규칙이* 평가되어 요청을 라우팅할 백 엔드 풀을 결정합니다.

### <a name="associated-back-end-pool"></a>연결된 백 엔드 풀

규칙에 연결 수신기가 받는 요청을 제공하는 백 엔드 대상을 포함하는 백 엔드 풀입니다.

 - 기본 규칙의 경우 백 엔드 풀은 하나만 허용됩니다. 연결된 수신기의 모든 요청은 해당 백 엔드 풀로 전달됩니다.

 - 경로 기반 규칙의 경우 각 URL 경로에 해당하는 여러 백 엔드 풀을 추가합니다. 입력된 URL 경로와 일치하는 요청은 해당 백 엔드 풀로 전달됩니다. 또한 기본 백 엔드 풀을 추가합니다. 규칙의 URL 경로와 일치하지 않는 요청은 해당 풀로 전달됩니다.

### <a name="associated-back-end-http-setting"></a>연결된 백 엔드 HTTP 설정

각 규칙에 대한 백 엔드 HTTP 설정을 추가합니다. 요청은 이 설정에 지정된 포트 번호, 프로토콜 및 기타 정보를 사용하여 응용 프로그램 게이트웨이에서 백 엔드 대상으로 라우팅됩니다.

기본 규칙의 경우 백 엔드 HTTP 설정은 하나만 허용됩니다. 이 HTTP 설정을 사용하여 연결된 수신기의 모든 요청이 해당 백 엔드 대상으로 전달됩니다.

경로 기반 규칙의 경우 각 URL 경로에 해당하는 여러 백 엔드 HTTP 설정을 추가합니다. 이 설정의 URL 경로와 일치하는 요청은 각 URL 경로에 해당하는 HTTP 설정을 사용하여 해당 백 엔드 대상으로 전달됩니다. 또한 기본 HTTP 설정을 추가합니다. 이 규칙의 URL 경로와 일치하지 않는 요청은 기본 HTTP 설정을 사용하여 기본 백 엔드 풀로 전달됩니다.

### <a name="redirection-setting"></a>리디렉션 설정

기본 규칙에 대해 리디렉션이 구성된 경우 연결된 수신기의 모든 요청이 대상으로 리디렉션됩니다. 이것은 *전역* 리디렉션입니다. 경로 기반 규칙에 대해 리디렉션이 구성된 경우 특정 사이트 영역의 요청만 리디렉션됩니다. 예를 들어 */cart/로\** 표시된 장바구니 영역이 있습니다. 경로 *기반* 리디렉션입니다.

리디렉션에 대한 자세한 내용은 [응용 프로그램 게이트웨이 리디렉션 개요를](redirect-overview.md)참조하십시오.

#### <a name="redirection-type"></a>리디렉션 유형

필요한 리디렉션 유형을 선택합니다: *영구(301),* *임시(307),* *Found(302)* 또는 *기타(303) 참조.*

#### <a name="redirection-target"></a>리디렉션 대상

다른 수신기 또는 외부 사이트를 리디렉션 대상으로 선택합니다.

##### <a name="listener"></a>수신기

수신기를 리디렉션 대상으로 선택하여 게이트웨이의 한 수신기에서 다른 수신기로 트래픽을 리디렉션합니다. 이 설정은 HTTP-HTTPS 리디렉션을 사용하도록 설정하려는 경우에 필요합니다. 들어오는 HTTP 요청을 확인하는 원본 수신기에서 들어오는 HTTPS 요청을 확인하는 대상 수신기로 트래픽을 리디렉션합니다. 리디렉션 대상으로 전달된 요청에 원래 요청의 쿼리 문자열 및 경로를 포함하도록 선택할 수도 있습니다.

![응용 프로그램 게이트웨이 구성 요소 대화 상자](./media/configuration-overview/configure-redirection.png)

HTTP-HTTPS 리디렉션에 대한 자세한 내용은 다음을 참조하십시오.
- [Azure 포털을 사용하여 HTTP-HTTPS 리디렉션](redirect-http-to-https-portal.md)
- [PowerShell을 사용하여 HTTP-HTTPS 리디렉션](redirect-http-to-https-powershell.md)
- [Azure CLI를 사용하여 HTTP-HTTPS 리디렉션](redirect-http-to-https-cli.md)

##### <a name="external-site"></a>외부 사이트

이 규칙과 연결된 수신기의 트래픽을 외부 사이트로 리디렉션하려면 외부 사이트를 선택합니다. 리디렉션 대상으로 전달된 요청에 원래 요청의 쿼리 문자열을 포함하도록 선택할 수 있습니다. 원래 요청에 있던 외부 사이트로 경로를 전달할 수 없습니다.

리디렉션에 대한 자세한 내용은 다음을 참조하십시오.
- [PowerShell을 사용하여 트래픽을 외부 사이트로 리디렉션](redirect-external-site-powershell.md)
- [CLI를 사용하여 트래픽을 외부 사이트로 리디렉션](redirect-external-site-cli.md)

#### <a name="rewrite-the-http-header-setting"></a>HTTP 헤더 설정 다시 작성

이 설정은 요청 및 응답 패킷이 클라이언트와 백 엔드 풀 간에 이동하는 동안 HTTP 요청 및 응답 헤더를 추가, 제거 또는 업데이트합니다. 자세한 내용은 다음을 참조하세요.

 - [HTTP 헤더 개요 다시 작성](rewrite-http-headers.md)
 - [HTTP 헤더 재작성 구성](rewrite-http-headers-portal.md)

## <a name="http-settings"></a>HTTP 설정

응용 프로그램 게이트웨이는 여기서 지정한 구성을 사용하여 트래픽을 백 엔드 서버로 라우팅합니다. HTTP 설정을 만든 후 하나 이상의 요청 라우팅 규칙과 연결해야 합니다.

### <a name="cookie-based-affinity"></a>쿠키 기반 선호도

Azure 응용 프로그램 게이트웨이는 사용자 세션을 유지 관리하기 위해 게이트웨이 관리 쿠키를 사용합니다. 사용자가 응용 프로그램 게이트웨이에 첫 번째 요청을 보낼 때 세션 세부 정보를 포함하는 해시 값으로 응답에서 선호도 쿠키를 설정하여 선호도 쿠키를 전달하는 후속 요청이 동일한 백 엔드 서버로 라우팅되도록 합니다. 끈적거림을 유지합니다. 

이 기능은 사용자 세션을 동일한 서버에 유지하려는 경우와 세션 상태가 사용자 세션의 서버에 로컬로 저장될 때 유용합니다. 응용 프로그램에서 쿠키 기반 선호도를 처리할 수 없는 경우 이 기능을 사용할 수 없습니다. 이를 사용하려면 클라이언트가 쿠키를 지원하는지 확인합니다.

[크롬 브라우저](https://www.chromium.org/Home) [v80 업데이트는](https://chromiumdash.appspot.com/schedule) [SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) 특성이없는 HTTP 쿠키가 SameSite = Lax로 처리되어야하는 명령을 가져왔습니다. CORS(원본 간 리소스 공유) 요청의 경우 쿠키를 타사 컨텍스트에서 보내야 하는 경우 *SameSite=None을 사용해야 합니다. 보안* 속성및 HTTPS를 통해 전송되어야 합니다. 그렇지 않으면 HTTP 전용 시나리오에서 브라우저는 타사 컨텍스트에서 쿠키를 보내지 않습니다. Chrome에서 이 업데이트의 목표는 보안을 강화하고 CSRF(교차 사이트 요청 위조) 공격을 방지하는 것입니다. 

이러한 변경을 지원하기 위해 2020년 2월 17일부터 응용 프로그램 게이트웨이(모든 SKU 유형)는 기존 *ApplicationGatewayAffinityity* COOKIE 외에 *ApplicationGatewayAffinityCORS라는* 또 다른 쿠키를 삽입합니다. *응용 프로그램 게이트웨이AffinityCORS* 쿠키에는 두 가지 속성이 추가되었습니다("SameSite=없음;* "secure")* origin 간 요청에 대해서도 고정 세션이 유지되도록 합니다.

기본 선호도 쿠키 이름은 *ApplicationGatewayAffinity이며* 변경할 수 있습니다. 사용자 지정 선호도 쿠키 이름을 사용하는 경우 CORS와 함께 추가 쿠키가 접미사로 추가됩니다. 예를 들어 *사용자 지정 쿠키네임코르*.

> [!NOTE]
> *SameSite=None* 특성이 설정된 경우 쿠키에 *보안* 플래그도 포함되어 야 하며 HTTPS를 통해 보내야 합니다.  CORS에 대해 세션 선호도가 필요한 경우 워크로드를 HTTPS로 마이그레이션해야 합니다. 응용 프로그램 게이트웨이에 대한 TLS 오프로드 및 종단 간 TLS [Configure an application gateway with TLS termination using the Azure portal](create-ssl-portal.md)설명서를 참조하십시오 [.](ssl-overview.md) [Configure end-to-end TLS by using Application Gateway with the portal](end-to-end-ssl-portal.md)

### <a name="connection-draining"></a>연결 드레이닝

연결 드레인을 사용하면 계획된 서비스 업데이트 중에 백 엔드 풀 멤버를 정상적으로 제거할 수 있습니다. 규칙을 만드는 동안 백 엔드 풀의 모든 멤버에 이 설정을 적용할 수 있습니다. 백 엔드 풀의 모든 등록 취소 인스턴스가 기존 연결을 계속 유지하고 구성 가능한 시간 시간에 대한 진행 중 요청을 제공하고 새 요청이나 연결을 받지 않도록 합니다. 이에 대한 유일한 예외는 게이트웨이 관리 세션 선호도 로 인해 인스턴스를 등록 취소하기 위해 바인딩된 요청이며 등록 취소 인스턴스로 계속 전달됩니다. 연결 드레인은 백 엔드 풀에서 명시적으로 제거된 백 엔드 인스턴스에 적용됩니다.

### <a name="protocol"></a>프로토콜

응용 프로그램 게이트웨이는 백 엔드 서버에 대한 요청을 라우팅하기 위해 HTTP와 HTTPS를 모두 지원합니다. HTTP를 선택하면 백 엔드 서버로의 트래픽이 암호화되지 않습니다. 암호화되지 않은 통신이 허용되지 않는 경우 HTTPS를 선택합니다.

수신기의 HTTPS와 결합된 이 설정은 [종단 간 TLS를](ssl-overview.md)지원합니다. 이렇게 하면 백 엔드에 암호화된 중요한 데이터를 안전하게 전송할 수 있습니다. 종단 간 TLS가 활성화된 백 엔드 풀의 각 백 엔드 서버는 보안 통신을 허용하는 인증서로 구성해야 합니다.

### <a name="port"></a>포트

이 설정은 백 엔드 서버가 응용 프로그램 게이트웨이에서 트래픽을 수신하는 포트를 지정합니다. 1에서 65535까지의 포트를 구성할 수 있습니다.

### <a name="request-timeout"></a>요청 시간 초과

이 설정은 응용 프로그램 게이트웨이가 백 엔드 서버에서 응답을 받기 위해 대기하는 시간(초)입니다.

### <a name="override-back-end-path"></a>백 엔드 패스 재정의

이 설정을 사용하면 요청이 백 엔드로 전달될 때 사용할 선택적 사용자 지정 전달 경로를 구성할 수 있습니다. **재정의 백 엔드** 경로 필드의 사용자 지정 경로와 일치하는 들어오는 경로의 모든 부분이 전달된 경로로 복사됩니다. 다음 표에서는 이 기능의 작동 방식을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 있습니다.

- HTTP 설정이 기본 요청 라우팅 규칙에 연결된 경우:

  | 원래 요청  | 백 엔드 패스 재정의 | 백 엔드로 전달된 요청 |
  | ----------------- | --------------------- | ---------------------------- |
  | /홈/            | /재정의/            | /재정의/홈/              |
  | /홈/세컨드홈/ | /재정의/            | /재정의/홈/세컨드홈/   |

- HTTP 설정이 경로 기반 요청 라우팅 규칙에 연결된 경우:

  | 원래 요청           | 경로 규칙       | 백 엔드 패스 재정의 | 백 엔드로 전달된 요청 |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /패스룰/홈/            | /pathrule*      | /재정의/            | /재정의/홈/              |
  | /pathrule/홈/세컨드홈/ | /pathrule*      | /재정의/            | /재정의/홈/세컨드홈/   |
  | /홈/                     | /pathrule*      | /재정의/            | /재정의/홈/              |
  | /홈/세컨드홈/          | /pathrule*      | /재정의/            | /재정의/홈/세컨드홈/   |
  | /패스룰/홈/            | /패스룰/홈* | /재정의/            | /재정의/                   |
  | /pathrule/홈/세컨드홈/ | /패스룰/홈* | /재정의/            | /재정의/세컨드홈/        |
  | /pathrule/                 | /pathrule/      | /재정의/            | /재정의/                   |

### <a name="use-for-app-service"></a>앱 서비스에 사용

Azure App Service 백 엔드에 필요한 두 설정을 선택하는 UI 전용 바로 가기입니다. 백 **엔드 주소에서 호스트 이름을 선택할**수 있으며 아직 호스트가 없는 경우 새 사용자 지정 프로브를 만듭니다. 자세한 내용은 이 문서의 백 엔드 주소 설정 [섹션에서 호스트 이름 선택](#pick) 섹션을 참조하십시오. 새 프로브가 만들어지고 프로브 헤더가 백 엔드 멤버의 주소에서 선택됩니다.

### <a name="use-custom-probe"></a>사용자 지정 프로브 사용

이 설정은 [사용자 지정 프로브를](application-gateway-probe-overview.md#custom-health-probe) HTTP 설정과 연결합니다. 하나의 사용자 지정 프로브만 HTTP 설정과 연결할 수 있습니다. 사용자 지정 프로브를 명시적으로 연결하지 않으면 [기본 프로브가](application-gateway-probe-overview.md#default-health-probe-settings) 백 엔드의 상태를 모니터링하는 데 사용됩니다. 백 엔드의 상태 모니터링을 보다 잘 제어할 수 있도록 사용자 지정 프로브를 만드는 것이 좋습니다.

> [!NOTE]
> 사용자 지정 프로브는 해당 HTTP 설정이 수신기와 명시적으로 연결되지 않는 한 백 엔드 풀의 상태를 모니터링하지 않습니다.

### <a name="pick-host-name-from-back-end-address"></a><a id="pick"/></a>백 엔드 주소에서 호스트 이름 선택

이 기능은 요청의 *호스트* 헤더를 백 엔드 풀의 호스트 이름으로 동적으로 설정합니다. IP 주소 또는 FQDN을 사용합니다.

이 기능은 백 엔드의 도메인 이름이 응용 프로그램 게이트웨이의 DNS 이름과 다르고 백 엔드가 특정 호스트 헤더를 사용하여 올바른 끝점으로 확인할 때 도움이 됩니다.

예를 들어 다중 테넌트 서비스를 백 엔드로 합니다. 앱 서비스는 단일 IP 주소로 공유 공간을 사용하는 다중 테넌트 서비스입니다. 따라서 앱 서비스는 사용자 지정 도메인 설정에서 구성된 호스트 이름을 통해서만 액세스할 수 있습니다.

기본적으로 사용자 지정 도메인 이름은 *example.azurewebsites.net.* 앱 서비스에 명시적으로 등록되지 않은 호스트 이름을 사용하거나 응용 프로그램 게이트웨이의 FQDN을 통해 응용 프로그램 게이트웨이를 사용하여 앱 서비스에 액세스하려면 원래 요청의 호스트 이름을 앱 서비스의 호스트 이름으로 재정의합니다. 이렇게 하려면 백 **엔드 주소 설정에서 호스트 이름 선택을** 사용하도록 설정합니다.

기존 사용자 지정 DNS 이름이 앱 서비스에 매핑된 사용자 지정 도메인의 경우 이 설정을 사용하도록 설정할 필요가 없습니다.

> [!NOTE]
> 전용 배포인 앱 서비스 환경에는 이 설정이 필요하지 않습니다.

### <a name="host-name-override"></a>호스트 이름 재정의

이 기능은 응용 프로그램 게이트웨이에서 들어오는 요청의 *호스트* 헤더를 지정한 호스트 이름으로 바꿉니다.

예를 들어 *호스트* **이름** 설정에서 www.contoso.com 지정하면 요청이 백 엔드 서버로 전달될 때 원래 요청 *이`https://appgw.eastus.cloudapp.azure.com/path1` *로`https://www.contoso.com/path1` 변경됩니다.

## <a name="back-end-pool"></a>백 엔드 풀

백 엔드 풀을 특정 가상 컴퓨터, 가상 시스템 규모 집합, IP 주소/FQDN 또는 앱 서비스의 네 가지 유형의 백 엔드 멤버로 지정할 수 있습니다. 

백 엔드 풀을 만든 후 하나 이상의 요청 라우팅 규칙과 연결해야 합니다. 또한 응용 프로그램 게이트웨이의 각 백 엔드 풀에 대한 상태 프로브를 구성해야 합니다. 요청 라우팅 규칙 조건이 충족되면 응용 프로그램 게이트웨이는 해당 백 엔드 풀에서 상태 프로브에 의해 결정된 대로 정상 서버로 트래픽을 전달합니다.

## <a name="health-probes"></a>상태 프로브

응용 프로그램 게이트웨이는 기본적으로 백 엔드에 있는 모든 리소스의 상태를 모니터링합니다. 그러나 상태 모니터링을 보다 잘 제어하려면 각 백 엔드 HTTP 설정에 대한 사용자 지정 프로브를 만드는 것이 좋습니다. 사용자 지정 프로브를 구성하는 방법에 대해 알아보려면 [사용자 지정 상태 프로브 설정을](application-gateway-probe-overview.md#custom-health-probe-settings)참조하십시오.

> [!NOTE]
> 사용자 지정 상태 프로브를 만든 후 백 엔드 HTTP 설정에 연결해야 합니다. 사용자 지정 프로브는 해당 HTTP 설정이 규칙을 사용하는 수신기와 명시적으로 연결되지 않는 한 백 엔드 풀의 상태를 모니터링하지 않습니다.

## <a name="next-steps"></a>다음 단계

이제 응용 프로그램 게이트웨이 구성 요소에 대해 알고 있으므로 다음을 수행할 수 있습니다.

- [Azure 포털에서 응용 프로그램 게이트웨이 만들기](quick-create-portal.md)
- [PowerShell을 사용하여 응용 프로그램 게이트웨이 만들기](quick-create-powershell.md)
- [Azure CLI를 사용하여 Application Gateway 만들기](quick-create-cli.md)
