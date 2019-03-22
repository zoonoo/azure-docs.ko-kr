---
title: Azure Application Gateway 구성 개요
description: 이 문서에서는 Azure Application Gateway의 여러 구성 요소를 구성 하는 방법 설명
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: absha
ms.openlocfilehash: 61b3a9e066a3ee20effa97f1c6c7a0bd1ae90ac0
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58285841"
---
# <a name="application-gateway-configuration-overview"></a>Application Gateway 구성 개요

다양 한 시나리오를 수행 하는 데 한 가지 방법으로 구성할 수 있는 몇 가지 컴포넌트의 응용 프로그램 게이트웨이 구성 됩니다. 이 문서에서는 각 구성 요소 구성 하는 하는 방법을 안내 합니다.

![application-gateway-components](./media/configuration-overview/configuration-overview1.png)

위의 예제 이미지에서는 3 명의 수신기를 사용 하 여 응용 프로그램의 구성을 보여 줍니다. 먼저 두 가지에 대 한 다중 사이트 수신기 `http://acme.com/*` 고 `http://fabrikam.com/*`, 각각. 모두 포트 80에서 수신 합니다. 세 번째 수신기가 종단 간 SSL 종료를 사용 하 여 기본 수신기입니다. 

## <a name="prerequisites"></a>필수 조건

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Azure 가상 네트워크와 전용된 서브넷

Application gateway는 전용된 가상 네트워크에 배포 합니다. 가상 네트워크 내에서 전용된 서브넷은 application gateway에 대 한 필수입니다. 이 서브넷에 지정 된 응용 프로그램 게이트웨이 배포의 여러 인스턴스가 있습니다. 다른 응용 프로그램 게이트웨이 서브넷에 배포할 수도 있지만 응용 프로그램 게이트웨이 서브넷에서 다른 리소스를 배포할 수 없습니다.  

> [!NOTE]   
> 동일한 서브넷에서 Standard_v2와 표준 Application Gateway의 혼합은 지원되지 않습니다.

#### <a name="size-of-the-subnet"></a>서브넷 크기

Application Gateway는 인스턴스당 하나의 개인 IP 주소를 사용하고 개인 프런트엔드 IP 구성이 구성된 경우 또 다른 개인 IP 주소를 사용합니다. 또한 Azure에서는 내부 사용을 위해 각 서브넷에서 처음 4개 및 마지막 IP 주소를 예약합니다. 예를 들어, application gateway는 세 개의 인스턴스와 없는 개인 프런트 엔드 IP로 설정 된, 경우 최소한 8 개의 IP 주소 서브넷-내부 사용에 대 한 5 개의 IP 주소 및 application gateway의 세 가지 인스턴스에 대 한 3 개의 IP 주소에에서 필요한 됩니다. 따라서이 경우에 / 29 서브넷 크기 이상이 필요 합니다. 세 개의 인스턴스가 있고 9 개의 IP 주소 필요 됩니다 개인 프런트 엔드 IP 구성에 대 한 IP 주소 3 개의 IP 주소가 application gateway의 세 가지 인스턴스에 대 한 개인 프런트 엔드 IP와 5 개의 IP에 대 한 하나의 IP 주소에 대 한 주소 내부 사용량입니다. 따라서이 경우에 / 28 서브넷 크기 이상이 필요 합니다.

모범 사례로, 적어도/28을 사용 하 여 서브넷 크기입니다. 이렇게 하면 11 주소만 사용할 수 있습니다. 응용 프로그램 부하 10 개가 넘는 인스턴스를 요구 하는 경우/27 또는/26 고려해 야 서브넷 크기입니다.

#### <a name="network-security-groups-supported-on-the-application-gateway-subnet"></a>Application Gateway 서브넷에서 지원 되는 네트워크 보안 그룹

네트워크 보안 그룹 (Nsg)는 다음 제한 Application Gateway 서브넷에서 지원 됩니다. 

- Application Gateway v1 SKU의 경우 포트 65503-65534에서, v2 SKU의 경우에는 포트 65200-65535에서 들어오는 트래픽에 대한 예외를 적용해야 합니다. 이 포트 범위는 Azure 인프라 통신에 필요합니다. Azure 인증서에 의해 보호(잠김)됩니다. 적절한 인증서가 없는 경우 해당 게이트웨이 고객을 포함하여 외부 엔터티는 해당 엔드포인트에서 변경을 시작할 수 없습니다.

- 아웃바운드 인터넷 연결은 차단할 수 없습니다. NSG의 기본 아웃 바운드 규칙이 인터넷 연결을 이미 허용합니다. 기본 아웃 바운드 규칙을 제거 하지는 및 아웃 바운드 인터넷 연결을 거부 하는 다른 아웃 바운드 규칙을 만들지 않는 하는 것이 좋습니다.

- AzureLoadBalancer 태그의 트래픽을 허용해야 합니다.

##### <a name="whitelist-application-gateway-access-to-a-few-source-ips"></a>몇 가지 원본 Ip에 대 한 허용 목록에 추가 Application Gateway 액세스

이 시나리오는 Application Gateway 서브넷에서 NSG를 사용하여 수행할 수 있습니다. 우선 순위에 따라 나열된 다음 제한 사항을 서브넷에 적용해야 합니다.

1. 원본 IP/IP 범위에서 들어오는 트래픽을 허용합니다.
2. [백 엔드 상태 통신](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)을 위해 모든 원본에서 포트 65503-65534로 들어오는 요청을 허용합니다. 이 포트 범위는 Azure 인프라 통신에 필요합니다. Azure 인증서에 의해 보호(잠김)됩니다. 적절한 인증서가 없는 경우 해당 게이트웨이 고객을 포함하여 외부 엔터티는 해당 엔드포인트에서 변경을 시작할 수 없습니다.
3. [NSG](https://docs.microsoft.com/azure/virtual-network/security-overview)에 대한 들어오는 Azure Load Balancer 프로브(AzureLoadBalancer 태그) 및 인바운드 가상 네트워크 트래픽(VirtualNetwork 태그)을 허용합니다.
4. 모두 거부 규칙을 사용하여 다른 모든 들어오는 트래픽을 차단합니다.
5. 모든 대상에 대해 인터넷으로의 아웃바운드 트래픽을 허용합니다.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Application Gateway 서브넷에서 지원 되는 사용자 정의 경로

V1 SKU의 경우 사용자 정의 경로 (Udr)으로 종단 간 요청/응답 통신을 변경 하지 않는 응용 프로그램 게이트웨이 서브넷에서 지원 됩니다. 예를 들어 패킷 검사를 위한 방화벽 장비를 가리키도록 애플리케이션 게이트웨이 서브넷에서 UDR을 설정할 수 있지만 패킷이 원하는 대상 사후 검사에 도달할 수 있는지 확인해야 합니다. 이렇게 하지 않으면 잘못된 상태 프로브 또는 트래픽 라우팅 동작을 초래할 수 있습니다. 여기에는 가상 네트워크에서 ExpressRoute 또는 VPN 게이트웨이를 통해 전파된 학습한 경로 또는 기본 0.0.0.0/0 경로가 포함됩니다.

V2의 경우 SKU, application gateway 서브넷에서 Udr을 지원 되지 않습니다. 자세한 내용은 [자동 크기 조정 및 영역 중복 Application Gateway(공개 미리 보기)](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#known-issues-and-limitations)를 참조하세요.

> [!NOTE]
> 상태를 하면 응용 프로그램 게이트웨이 서브넷에서 Udr을 사용 하는 [백 엔드 상태 보기](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) 으로 표시 되어야 **알 수 없는** 차세대 응용 프로그램 게이트웨이 로그의 오류도 발생 하 고 메트릭입니다. 백 엔드 상태, 로그 및 메트릭을 볼 수 있으려면 응용 프로그램 게이트웨이 서브넷에서 Udr를 사용 하지 않는 것이 좋습니다.

## <a name="frontend-ip"></a>프런트 엔드 IP

공용 IP 주소 또는 개인 IP 주소를 포함 하도록 응용 프로그램 게이트웨이 구성할 수 있습니다. 공용 IP는 인터넷 연결 VIP 통해 인터넷을 통해 클라이언트에서 액세스할 수 하는 데 필요한 백 엔드를 호스트 하는 경우 필요 합니다. 공용 IP는 내부 부하 분산 장치 (ILB) 끝점 이라고 인터넷에 노출 되지 않은 내부 끝점에 대 한 필요 하지 않습니다. ILB를 사용하여 게이트웨이를 구성하는 것은 인터넷에 노출되지 않은 비즈니스 애플리케이션의 내부 라인에 대해 유용합니다. 또한 인터넷에 노출되지 않은 보안 경계에 앉아 있는 다중 계층 애플리케이션 내에 포함된 서비스 및 계층에 유용하지만 여전히 라운드 로빈 부하 분산, 세션 인력 또는 SSL(Secure Sockets Layer) 종료가 필요합니다.

하나의 공용 IP 주소 또는 하나의 개인 IP 주소가 지원 됩니다. Application Gateway를 만드는 동안 프런트 엔드 IP를 선택 합니다. 

- 공용 IP의 경우 Application Gateway와 동일한 위치에 기존 공용 IP를 사용 하거나 새 공용 IP를 만들 수도 있습니다. 새 공용 IP 주소를 만드는 경우 IP 주소 유형은 선택 (정적 또는 동적) 나중에 변경할 수 없습니다. 자세한 내용은 참조 하세요. [정적 포트 대 동적 공용 IP](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-vs-dynamic-public-ip) 

- 개인 IP를 발생 시 응용 프로그램 게이트웨이가 생성 되는 서브넷에서 개인 IP 주소를 지정 수 있습니다. 명시적으로 지정 하지 않으면 임의의 IP 주소 서브넷에서 자동으로 선택 됩니다. 자세한 내용은 참조 하세요. [내부 부하 분산 장치 (ILB) 끝점을 사용 하 여 응용 프로그램 게이트웨이 만듭니다.](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)

프런트 엔드 IP에 연결 된 *수신기* 프런트 엔드 IP에서 들어오는 요청을 확인 합니다.

## <a name="listeners"></a>수신기

수신기는 포트, 프로토콜, 호스트 및 IP 주소를 사용 하 여 들어오는 연결 요청을 확인 하는 논리 엔터티입니다. 따라서 포트의 해당 값을 입력 해야 하는 수신기를 구성할 때 프로토콜, 호스트 및 IP 주소는 게이트웨이의 들어오는 요청에 해당 값과 동일 합니다. Azure portal을 사용 하 여 응용 프로그램 게이트웨이 만든 수신기에 대 한 프로토콜 및 포트를 선택 하 여 기본 수신기를 만듭니다. 또한 수신기에서 HTTP2 지원할 것인지 여부를 선택할 수 있습니다. 응용 프로그램 게이트웨이 만든 후이 기본 수신기의 설정을 편집할 수 있습니다 (*appGatewayHttpListener*/*appGatewayHttpsListener*) 하거나 새 수신기를 만듭니다.

### <a name="listener-type"></a>수신기 유형

선택할 수 있습니다 [기본 또는 다중 사이트 수신기](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners) 새 수신기를 만드는 동안. 

- 응용 프로그램 게이트웨이 뒤에 단일 사이트를 호스트 하는 경우에 기본 수신기를 선택 합니다. 에 대해 알아봅니다 [기본 수신기를 사용 하 여 응용 프로그램 게이트웨이 만드는 방법](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)합니다.

- 를 구성 하는 둘 이상의 웹 응용 프로그램 또는 동일한 부모 도메인의 여러 하위 도메인에서 동일한 application gateway 인스턴스는 다중 사이트 수신기를 선택 합니다. 다중 사이트 수신기에 대 한 호스트 이름을 입력 해야 또한 합니다. Application Gateway는 HTTP 1.1 호스트 헤더를 동일한 공용 IP 주소 및 포트에서 둘 이상의 웹 사이트를 호스트 하는 때문입니다.

#### <a name="order-of-processing-listeners"></a>수신기 처리 순서

V1 Sku의 경우 수신기는 표시 된 순서 대로 처리 됩니다. 이러한 이유로 기본 수신기에서 들어오는 요청과 일치하는 경우 이 요청을 먼저 처리합니다. 따라서 올바른 백 엔드에 트래픽이 라우팅되는 되도록 기본 수신기 보다 먼저 다중 사이트 수신기를 구성 되어야 합니다.

V2 Sku의 경우 다중 사이트 수신기는 기본 수신기 보다 먼저 처리 됩니다.

### <a name="frontend-ip"></a>프런트 엔드 IP

이 수신기에 연결 하려고 하는 프런트 엔드 IP를 선택 합니다. 수신기는이 IP에서 들어오는 요청에 수신 됩니다.

### <a name="frontend-port"></a>프런트 엔드 포트

프런트 엔드 포트를 선택 합니다. 기존 포트에서 선택 하거나 새로 만들 수 있습니다. 값을 선택할 수는 [허용 되는 포트의 범위](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports)합니다. 따라서, 80 및 443 같은 잘 알려진 포트를 사용할 뿐만 아니라 있지만 사용자 지정 포트 사용에 대 한 적절 한 수 있습니다. 1 개의 포트 연결 수신기를 공용 또는 개인 연결 수신기에 대 한 중 사용할 수 있습니다.

### <a name="protocol"></a>프로토콜

HTTP 및 HTTPS 프로토콜 중 하나를 선택 해야 합니다. 

- HTTP를 선택 하면 클라이언트 및 응용 프로그램 게이트웨이 간에 트래픽은 암호화 되지 않은 합니다.

- 원하는 경우 HTTPS를 선택 [Secure Sockets Layer (SSL) 종료](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssl-terminationl) 하거나 [종단 간 SSL 암호화](https://docs.microsoft.com/azure/application-gateway/ssl-overview)합니다. HTTPS를 선택 하면 클라이언트 및 응용 프로그램 게이트웨이 간의 트래픽은 암호화 되 고 application gateway에서 SSL 연결을 종료 됩니다.  종단 간 SSL 암호화를 사용 하도록 하려는 경우에 HTTPS 프로토콜을 구성 하는 동안 선택 해야 또한 *백 엔드 HTTP 설정*합니다. 이렇게 하면 백 엔드 응용 프로그램 게이트웨이에서 데이터가 이동할 때 트래픽을 다시 암호화 됩니다.

  Secure Sockets Layer (SSL) 종료 및 종단 간 SSL 암호화를 구성 하려면 인증서를 응용 프로그램 게이트웨이에서 SSL 프로토콜 사양에 따라 대칭 키를 파생 하기 위해 수신기에 추가 해야 합니다. 대칭 키 암호화 및 게이트웨이에 전송 된 트래픽을 암호 해독에 사용 됩니다. 게이트웨이 인증서는 개인 정보 교환(PFX) 형식이어야 합니다. 이 파일 형식을 사용하면 애플리케이션 게이트웨이에서 트래픽의 암호화 및 암호 해독을 수행하는 데 필요한 개인 키를 내보낼 수 있습니다. 

#### <a name="supported-certificates"></a>지원 되는 인증서

참조 [SSL 종료를 위한 지원 인증서](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination)합니다.

### <a name="additional-protocol-support"></a>추가 프로토콜 지원

#### <a name="http2-support"></a>HTTP2 지원

Application Gateway 수신기에 연결하는 클라이언트의 경우에만 HTTP/2 프로토콜이 지원됩니다. 백 엔드 서버 풀에 대한 통신은 HTTP/1.1을 통해 이루어집니다. 기본적으로 HTTP/2 지원은 사용할 수 없습니다. 다음 Azure PowerShell 코드 조각 예제는 이를 사용하도록 설정하는 방법을 보여 줍니다.

```azurepowershell
$gw = Get-AzureRmApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>Websocket 지원

Websocket 지원 기본적으로 사용 됩니다. WebSocket 지원을 선택적으로 사용하거나 사용하지 않도록 설정하는 사용자 구성 가능 설정은 없습니다. Websocket은 HTTP 및 HTTPS 수신기와 함께 사용할 수 있습니다. 

### <a name="custom-error-page"></a>사용자 지정 오류 페이지

수신기 수준 뿐만 아니라 전역 수준에서 사용자 지정 오류 페이지를 정의할 수 있습니다, 그리고 있지만 Azure portal에서 전역 수준 사용자 지정 오류 페이지를 만들기는 현재 지원 되지 않습니다. 수신기 수준에서 403 WAF 오류에 대 한 사용자 지정 오류 페이지 또는 502 유지 관리 페이지를 구성할 수 있습니다. 지정 된 오류 상태 코드에 공개적으로 액세스 가능한 blob URL을 지정 해야 합니다. 자세한 내용은 [만들기 사용자 지정 오류 페이지](https://docs.microsoft.com/azure/application-gateway/custom-error)합니다.

![Application Gateway 오류 코드](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

전역 사용자 지정 오류 페이지를 구성 하려면 사용 [구성에 대 한 Azure PowerShell](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration) 

### <a name="ssl-policy"></a>SSL 정책

SSL 인증서 관리를 중앙 집중화 하 고 백 엔드 서버 팜에서 암호화 및 암호 해독 오버 헤드를 줄일 수 있습니다. 또한 이러한 중앙 집중식 SSL 처리를 통해 조직의 보안 요구 사항에 적합한 중앙 SSL 정책을 지정할 수 있습니다.  미리 정의 된 기본 및 사용자 지정 SSL 정책을 선택할 수 있습니다. 

SSL 프로토콜 버전을 제어 하는 SSL 정책을 구성할 수 있습니다. TLS1.0, TLS1.1 및 TLS1.2를 거부 하도록 application gateway를 구성할 수 있습니다. SSL 2.0 및 3.0은 기본적으로 비활성화되며 구성할 수 없습니다. 자세한 내용은 [Application Gateway SSL 정책 개요](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview)합니다.

수신기를 만든 후 연결 수신기에서 수신한 요청 백 엔드에 라우팅해야 하는 하는 방법을 결정 하는 요청 라우팅 규칙을 사용 하 여 합니다.

## <a name="request-routing-rule"></a>라우팅 규칙 요청

기본 규칙을 만들면 Azure portal을 사용 하 여 application gateway를 만드는 동안 (*rule1*), 기본 수신기를 바인딩하는 (*appGatewayHttpListener*) (기본백엔드풀을사용하여*appGatewayBackendPool*) 및 기본 백 엔드 HTTP 설정 (*appGatewayBackendHttpSettings*). 응용 프로그램 게이트웨이 만든 후에이 기본 규칙의 설정을 편집 하거나 새 규칙을 만들 수 있습니다.

### <a name="rule-type"></a>규칙 유형

선택할 수 있습니다 [기본 또는 경로 기반 규칙](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rule) 새 규칙을 만드는 동안. 

- 연결 된 수신기에 대 한 모든 요청에 전달 하려는 경우 (예: blog.contoso.com/*)를 단일 백 엔드 풀에 기본 수신기를 선택 합니다. 
- 특정 백 엔드 풀에 있는 특정 URL 경로 사용 하 여 수신기 경로 기반 라우팅 하려는 경우 요청을 선택 합니다. 경로 패턴은 해당 쿼리 매개 변수 필요가 URL의 경로에 적용 됩니다.


#### <a name="order-of-processing-rules"></a>규칙 처리 순서

V1 Sku의 경우 들어오는 요청 패턴의 일치 하는 경로 기반 규칙의 URL 경로 맵에 경로 나열 되는 순서 대로 처리 됩니다. 따라서 요청 URL 경로 맵에서 둘 이상의 경로에 있는 패턴과 일치 하는 경우 다음에 나열 된 경로 먼저 일치 시킬 및 해당 경로와 관련 된 백 엔드에 요청 전달 됩니다.

V2 Sku의 경우 정확히 일치 하는 경로에 URL 경로 맵 나열 되는 순서를 통해 더 높은 우선 순위를 보유 합니다. 에 대 한 이유, 요청을 백 엔드에 요청을 전달할지를 둘 이상의 경로에 패턴과 일치 하는 경우 요청과 정확히 일치 하는 경로 사용 하 여 연결 합니다. 들어오는 요청에 포함 된 경로 정확히 일치 하지 않는 URL 경로 맵 임의 경로 들어오는 요청 패턴의 일치 하는 경로 기반 규칙의 URL 경로 맵에 경로 나열 되는 순서 대로 처리 됩니다.

### <a name="associated-listener"></a>연결 된 수신기

규칙에 수신기를 연결 해야 있도록를 *요청 라우팅 규칙* 연관 합니다 *수신기* 결정 하도록 평가 됩니다는 *백 엔드 풀* 는 요청이 라우팅될 되었습니다.

### <a name="associated-backend-pool"></a>연결 된 백 엔드 풀

수신기가 수신한 요청에 사용할 백 엔드 대상을 포함 하는 백 엔드 풀을 연결 합니다. 기본 규칙을 발생 시이 백 엔드 풀에 연결 된 수신기에서 모든 요청을 전달할지 하므로 백 엔드 풀이 하나만 허용 됩니다. 경로 기반 규칙을 발생 시 각 URL 경로 해당 하는 여러 백 엔드 풀을 추가 합니다. 여기에 입력 한 URL 경로 일치 하는 요청을 해당 백 엔드 풀으로 전달 됩니다. 또한이 규칙에 입력 된 모든 URL 경로 일치 하지 않습니다는 요청을 전달할 것 이므로 기본 백 엔드 풀을 추가 합니다.

### <a name="associated-backend-http-setting"></a>연결 된 백 엔드 HTTP 설정

각 규칙에 대 한 백 엔드 HTTP 설정을 추가 합니다. 요청을 백 엔드 대상 포트 번호, 프로토콜 및이 설정에 지정 된 다른 설정을 사용 하 여 Application Gateway에서 라우팅됩니다. 기본 규칙을 발생 시이 HTTP 설정을 사용 하 여 해당 백 엔드 대상에 연결 된 수신기에서 모든 요청을 전달할지 하므로 백 엔드 HTTP 설정은 하나만 허용 됩니다. 경로 기반 규칙을 발생 시 각 URL 경로 해당 하는 여러 백 엔드 HTTP 설정을 추가 합니다. 여기에 입력 한 URL 경로 일치 하는 요청은 각 URL 경로 해당 하는 HTTP 설정을 사용 하 여 해당 백 엔드 대상에 전달 됩니다. 또한 기본 HTTP 설정을 사용 하 여 기본 백 엔드 풀에는이 규칙에 입력 된 모든 URL 경로 일치 하지 않음을 요청을 전달할지 하므로 기본 HTTP 설정을 추가 합니다.

### <a name="redirection-setting"></a>리디렉션 설정

리디렉션 기본 규칙으로 구성 된 경우 연결 된 수신기에서 모든 요청 함으로써 전역 리디렉션을 사용 하도록 설정 하는 리디렉션 대상이 리디렉션됩니다. 경로 기반 규칙을 특정 사이트 영역에만 요청에 대 한 리디렉션을 구성 된 경우 예를 들어는 장바구니 영역 가리키는/카트 / *, 있으므로 경로 기반 리디렉션을 사용 하도록 설정 하는 리디렉션 대상이 리디렉션됩니다. 

리디렉션 기능에 대 한 자세한 내용은 [리디렉션 개요](https://docs.microsoft.com/azure/application-gateway/redirect-overview)합니다.

- #### <a name="redirection-type"></a>리디렉션 유형

  필요한 의도치 않은 리디렉션의 유형을 선택 합니다. Other(303) Permanent(301), Temporary(307), Found(302) 또는 참조 하십시오입니다.

- #### <a name="redirection-target"></a>리디렉션 대상

  리디렉션 대상으로 다른 수신기 또는 외부 사이트를 선택할 수 있습니다. 

  - ##### <a name="listener"></a>수신기

    리디렉션 대상으로 선택 하면 수신기는 게이트웨이에서 다른 수신기로 하나의 수신기에서 리디렉션에 도움이 됩니다. HTTP 및 HTTPS 간의 리디렉션으로, 즉, 들어오는 HTTPS 요청을 확인 하는 대상 수신기에 들어오는 HTTP 요청에 대 한 검사 원본 수신기에서 트래픽의 리디렉션 사용 하도록 설정 하려는 경우이 설정은 필수입니다. 리디렉션 대상에 전달 요청에 포함할 원래 요청에 쿼리 문자열 및 경로 선택할 수 있습니다.![application-gateway-components](./media/configuration-overview/configure-redirection.png)

    자세한 내용은 HTTP에서 HTTPS로 리디렉션에 대 한 참조 [포털을 사용 하 여 HTTP에 HTTP 리디렉션](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal), [PowerShell를 사용 하 여 HTTP에 HTTP 리디렉션을](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell), [CLI를 사용 하 여 HTTP에 HTTP 리디렉션을](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

  - ##### <a name="external-site"></a>외부 사이트

    외부 사이트로 리디렉션할 수 있으므로 규칙과 연결 된 수신기에서 트래픽을 리디렉션할 하려는 경우 외부 사이트를 선택 합니다. 리디렉션 대상에 전달 요청에 포함할 원래 요청에 쿼리 문자열을 선택할 수 있습니다. 외부 사이트로 원래 요청에 대 한 경로 전달할 수 없습니다.

    외부 사이트로 리디렉션에 대 한 자세한 내용은 참조 하세요. [PowerShell을 사용 하는 외부 사이트에 트래픽을 리디렉션할](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell) 및 [https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-http-header-setting"></a>HTTP 헤더 설정 다시 작성

이 기능을 사용 하면 추가, 제거 또는 요청 하는 동안 HTTP 요청 및 응답 헤더를 업데이트할 수 있습니다 하 고 응답 패킷의 클라이언트와 백 엔드 풀 간에 이동 합니다.    이 기능은 PowerShell 통해서만 구성할 수 있습니다. 포털 및 CLI 지원이 제공 되지 않습니다. 자세한 내용은 [재작성 HTTP 헤더](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) 개요 및 [구성 하는 HTTP 헤더 재작성](https://docs.microsoft.com/azure/application-gateway/add-http-header-rewrite-rule-powershell#specify-your-http-header-rewrite-rule-configuration).

## <a name="http-settings"></a>HTTP 설정

Application gateway는이 구성 요소에 지정 된 구성을 사용 하 여 백 엔드 서버에 트래픽을 라우팅합니다. HTTP 설정이 만든 후 하나를 사용 하 여 연결 해야 하거나 더 요청 라우팅 규칙입니다.

### <a name="cookie-based-affinity"></a>쿠키 기반 선호도

이 기능은 동일한 서버의 사용자 세션을 유지 하려는 경우에 유용 합니다. Application Gateway는 게이트웨이 관리형 쿠키를 사용하여 사용자 세션에서 동일한 서버에 후속 트래픽을 처리하도록 지시할 수 있습니다. 이는 세션 상태가 사용자 세션의 서버에 로컬로 저장된 경우에 특히 중요합니다. 응용 프로그램 쿠키 기반 선호도 처리할 수 없는 경우 다음 됩니다이 기능을 사용할 수 있습니다. 쿠키 기반 세션 선호도 사용 하려면 클라이언트에서 쿠키를 지원 해야 한다는 확인 해야 합니다. 

### <a name="connection-draining"></a>연결 드레이닝

연결 드레이닝은 예정된 서비스 업데이트 중에 백 엔드 풀 멤버를 정상적으로 제거하는 데 도움이 됩니다. 이 설정은 규칙을 만드는 동안 백 엔드 풀의 모든 멤버에 적용할 수 있습니다. 사용 하도록 설정 하면 응용 프로그램 게이트웨이 백 엔드 풀의 모든 등록 해제 인스턴스 구성 된 제한 시간 내에 완료에 대 한 기존 요청을 허용 하는 동안 새 요청을 수신 하지 수행 되도록 합니다. 이것은 API 호출에 의해 백엔드 풀에서 명시적으로 제거된 백 엔드 인스턴스와 상태 프로브에 의해 확인된 대로 비정상 상태로 보고된 백엔드 인스턴스에 모두 적용됩니다.

### <a name="protocol"></a>프로토콜

Application gateway는 라우팅 요청을 백 엔드 서버에 대 한 HTTP 및 HTTPS 프로토콜을 지원 합니다. HTTP 프로토콜을 선택한 경우에 트래픽 흐름 백 엔드 서버에 암호화 되지 않은 합니다. 이 경우 백 엔드 서버에 암호화 되지 않은 통신은 허용 가능한 옵션이 아닙니다 HTTPS 프로토콜을 선택 해야 합니다. 이 설정은 수신기에서 HTTPS 프로토콜을 선택 하는 일을 함께 사용 하면 [종단 간 SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview)합니다. 중요 한 데이터를 암호화 하는 백 엔드로 안전 하 게 전송할 수 있습니다. 종단 간 SSL이 활성화된 백 엔드 풀의 각 백 엔드 서버는 보안 통신을 허용하도록 인증서를 사용하여 구성해야 합니다.

### <a name="port"></a>포트

Application Gateway에서 들어오는 트래픽만 수신 하는 백 엔드 서버 포트입니다. 1에서 ~ 65535 범위의 포트를 구성할 수 있습니다.

### <a name="request-timeout"></a>요청 시간 초과

Application gateway 백 엔드 풀에서 "연결 시간 초과 되었습니다." 오류를 반환 하기 전에 응답을 수신 대기할 시간 (초) 수입니다.

### <a name="override-backend-path"></a>백 엔드 경로 재정의

이 설정은 백 엔드에 요청 전달 될 때 사용 하는 선택적 사용자 지정 전달 경로 구성할 수 있습니다. 사용자 지정 경로 지정 된 일치 하는 들어오는 경로의 일부를 복사 하는 합니다 **백 엔드 경로 재정의** 필드 전달 된 경로를 합니다. 기능 작동 방식을 이해 하려면 아래 표를 참조 합니다.

- 경우 HTTP 설정은 기본 요청 라우팅 규칙에 연결 됩니다.

  | 원래 요청  | 백 엔드 경로 재정의 | 백 엔드에 전달 되는 요청 |
  | ----------------- | --------------------- | ---------------------------- |
  | /home/            | /override/            | 재정의/home / /              |
  | /home/secondhome/ | /override/            | /override/home/secondhome/   |

- 경우 HTTP 설정은 요청 경로 기반 라우팅 규칙에 연결 됩니다.

  | 원래 요청           | 경로 규칙       | 백 엔드 경로 재정의 | 백 엔드에 전달 되는 요청 |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /pathrule*      | /override/            | 재정의/home / /              |
  | /pathrule/home/secondhome/ | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /home/                     | /pathrule*      | /override/            | 재정의/home / /              |
  | /home/secondhome/          | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | /override/            | /override/                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /override/            | /override/secondhome/        |

### <a name="use-for-app-service"></a>App Service에 사용

App service 백 엔드에 대 한 두 개의 필수 설정을 선택 하는 UI 바로 이것이-하면 백 엔드 주소에서 호스트 이름을 선택 하 고 새 사용자 지정 프로브를 만듭니다. 이전이 완료 되는 이유는 이유는에 대 한 섹션에서 설명한 **백 엔드 주소에서 호스트 이름을 선택** 설정 합니다. 새 프로브를 백 엔드 멤버의 주소에서 프로브 헤더 선택도 됩니다 만들어집니다.

### <a name="use-custom-probe"></a>사용자 지정 프로브 사용

이 설정은 연결 되는 [사용자 지정 프로브](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe) 이 HTTP 설정을 사용 하 여 합니다. 하나의 사용자 지정 프로브는 HTTP 설정을 사용 하 여 연결할 수 있습니다. 하는 경우 다음 사용자 지정 프로브를 명시적으로 연결 하지 있습니다 [기본 프로브](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings) 백 엔드의 상태를 모니터링 하 게 됩니다. 프로그램 백엔드의 상태 모니터링을 보다 세부적으로 제어할 사용자 지정 프로브를 만드는 것이 좋습니다.

> [!NOTE]   
> 사용자 지정 프로브 백 엔드 풀의 상태를 모니터링 하 여 해당 하는 HTTP 설정을 명시적으로 수신기와 연결 된 경우가 아니면 시작 되지 됩니다.

### <a name="pick-host-name-from-backend-address"></a>백 엔드 주소에서 호스트 이름 선택

이 기능을 동적으로 설정 합니다 *호스트* 호스트 이름 IP 주소 또는 정규화 된 도메인 이름 (FQDN)을 사용 하 여 백 엔드 풀의 요청에 헤더입니다. 이 방법은 백 엔드의 도메인 이름은 응용 프로그램 게이트웨이의 DNS 이름과 다를 특정 호스트 헤더 또는 SNI 확장으로 다중 테 넌 트 서비스의 경우와 같이 올바른 끝점으로 해결에 의존 하는 백 엔드 시나리오에 유용 백 엔드입니다. App service는 단일 IP 주소를 사용 하 여 공유 공간을 사용 하 여 다중 테 넌 트 서비스 이기 때문에 App service는 사용자 지정 도메인 설정에 구성 된 호스트 이름에만 액세스할 수 있습니다. 기본적으로 사용자 지정 도메인 이름은 *example.azurewebsites.net*합니다. 따라서 Application gateway의 FQDN 또는 App service에서 명시적으로 등록 된 호스트를 사용 하 여 응용 프로그램 게이트웨이 사용 하 여 App service에 액세스 하려는 경우 재정의 해야 하는 App service의 호스트 이름, 원래 요청에 호스트 이름으로 사용 하도록 설정 **백 엔드 주소에서 호스트 이름을 선택** 설정 합니다.

사용자 지정 도메인을 소유 하 고 기존 사용자 지정 DNS 이름을 App service에 매핑한 경우이 설정을 사용 하도록 설정 필요가 없습니다.

> [!NOTE]   
> ASE는 전용된 배포 이므로이 설정에 대 한 서비스 환경 (ASE (App) 필요 하지 않습니다. 

### <a name="host-name-override"></a>호스트 이름 재정

이 기능을 대체 합니다 *호스트* 여기에서 지정한 호스트 이름에 application gateway에서 들어오는 요청의 헤더입니다. 예를 들어, 경우 www\.contoso.com으로 지정 합니다 **호스트 이름** 원래 요청을 설정 https://appgw.eastus.cloudapp.net/path1 로 변경 됩니다 https://www.contoso.com/path1 백 엔드 서버로 요청은 전달 하는 경우. 

## <a name="backend-pool"></a>백 엔드 풀

백 엔드 풀 네 가지 유형의 백 엔드 구성원을 가리킬 수: 특정 가상 컴퓨터, 가상 머신 확장 집합, IP 주소/FQDN 또는 app service입니다. 각 백 엔드 풀은 동일한 형식의 여러 멤버를 가리킬 수 있습니다. 동일한 백 엔드 풀에서 다른 형식의 멤버를 가리키는 지원 되지 않습니다. 

백 엔드 풀을 만든 후 하나 이상의 요청 라우팅 규칙을 사용 하 여 연결 해야 합니다. Application gateway에서 각 백 엔드 풀에 대 한 상태 프로브를 구성 해야 합니다. 요청 라우팅 규칙 조건 충족 되 면 application gateway는 해당 백 엔드 풀의 정상 서버 (상태 프로브에 의해 결정)로 트래픽을 전달 합니다.

## <a name="health-probes"></a>상태 프로브

기본적으로 해당 백 엔드에 있는 모든 리소스의 상태를 모니터링 하는 응용 프로그램 게이트웨이, 경우에 상태 모니터링을 통해 더 세부적으로 제어할 수 있도록 각 백 엔드 HTTP 설정에 대 한 사용자 지정 프로브를 만든 것이 좋습니다. 사용자 지정 상태 프로브를 구성 하는 방법에 알아보려면 참조 [사용자 지정 상태 프로브 설정](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings)합니다.

> [!NOTE]   
> 사용자 지정 상태 프로브를 만든 후에 백 엔드 HTTP 설정에 연결 해야 합니다. 사용자 지정 프로브 백 엔드 풀의 상태를 모니터링 하 여 해당 하는 HTTP 설정을 명시적으로 수신기와 연결 된 경우가 아니면 시작 되지 됩니다.

## <a name="next-steps"></a>다음 단계

에 응용 프로그램 게이트웨이 구성 요소에 대해 학습 한 후 다음을 수행할 수 있습니다.

- [Azure portal에서 응용 프로그램 게이트웨이 만들기](quick-create-portal.md)
- [PowerShell을 사용 하 여 응용 프로그램 게이트웨이 만들기](quick-create-powershell.md)
- [Azure CLI를 사용 하 여 응용 프로그램 게이트웨이 만들기](quick-create-cli.md)
