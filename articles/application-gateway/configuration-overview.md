---
title: Azure Application Gateway 구성 개요
description: 이 문서에서는 Azure Application Gateway의 구성 요소를 구성 하는 방법 설명
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/1/2019
ms.author: absha
ms.openlocfilehash: 55c7670821ee6c6f5b924bf18b5f7ad01d4b6d51
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431307"
---
# <a name="application-gateway-configuration-overview"></a>Application Gateway 구성 개요

Azure Application Gateway 다양 한 시나리오에 대 한 다양 한 방법으로 구성할 수 있는 몇 가지 구성 요소로 구성 됩니다. 이 문서에서는 각 구성 요소를 구성 하는 방법을 보여 줍니다.

![응용 프로그램 게이트웨이 구성 요소 흐름 차트](./media/configuration-overview/configuration-overview1.png)

이 이미지에 있는 세 가지 수신기 응용을 프로그램을 보여 줍니다. 첫 번째 두 가지에 대 한 다중 사이트 수신기 `http://acme.com/*` 고 `http://fabrikam.com/*`, 각각. 모두 포트 80에서 수신 합니다. 세 번째 기본 수신기 엔드-투-엔드 Secure Sockets Layer (SSL) 종료 된 경우


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 조건

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Azure 가상 네트워크와 전용된 서브넷

Application gateway는 전용된 가상 네트워크에 배포 합니다. 가상 네트워크 내에서 전용된 서브넷은 application gateway에 대 한 필수입니다. 서브넷에 지정 된 응용 프로그램 게이트웨이 배포의 여러 인스턴스가 있습니다. 또한 다른 응용 프로그램 게이트웨이 서브넷에 배포할 수 있습니다. 하지만 응용 프로그램 게이트웨이 서브넷에서 다른 리소스를 배포할 수 없습니다.

> [!NOTE]
> 동일한 서브넷에 Standard_v2 및 표준 Azure Application Gateway를 혼합할 수 없습니다.

#### <a name="size-of-the-subnet"></a>서브넷 크기

Application Gateway는 개인 프런트 엔드 IP 구성 된 경우 인스턴스당 1 개인 IP 주소와 다른 개인 IP 주소를 사용 합니다.

Azure 내부 사용을 위해 각 서브넷에서 5 개의 IP 주소를 예약 합니다: 첫 번째 4 및 마지막 IP 주소입니다. 예를 들어 없는 개인 프런트 엔드 IP 사용 하 여 응용 프로그램 게이트웨이 인스턴스가 15 것이 좋습니다. 이 서브넷에 대 한 20 개 이상의 IP 주소가 필요합니다. 내부 사용 및 응용 프로그램 게이트웨이 인스턴스의 15 5입니다. 따라서/27 해야 크기 또는 더 큰 서브넷입니다.

개인 프런트 엔드 IP에 대 한 27 응용 프로그램 게이트웨이 인스턴스 및 IP 주소에 있는 것이 좋습니다. 이 경우 33 IP 주소가 필요합니다. 응용 프로그램 게이트웨이 인스턴스의 경우 개인 프런트 엔드에 대 한 1과 5 내부용 27입니다. 따라서 한/26 해야 크기 또는 더 큰 서브넷입니다.

적어도/28 서브넷 크기를 사용 하는 것이 좋습니다. 이 크기는 11 사용 가능한 IP 주소를 제공합니다. 응용 프로그램 부하에 10 개가 넘는 ip가 필요한 경우/27 또는/26 고려 서브넷 크기입니다.

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>Application Gateway 서브넷에서 네트워크 보안 그룹

네트워크 보안 그룹 (Nsg) Application Gateway에서 지원 됩니다. 하지만 몇 가지 제한 사항이 있습니다.

- V2 SKU에 대 한 포트 65200 65535 Application Gateway v1 SKU에 대 한 포트 65503-65534에 들어오는 트래픽에 대 한 예외를 포함 해야 합니다. 이 포트 범위는 Azure 인프라 통신에 필요합니다. 이러한 포트 (잠김) Azure 인증서에 의해 보호 됩니다. 외부 엔터티를 해당 게이트웨이 고객을 포함 하는 위치에 적절 한 인증서 없이 해당 끝점의 변경 내용을 시작할 수 없습니다.

- 아웃바운드 인터넷 연결은 차단할 수 없습니다. NSG의 기본 아웃 바운드 규칙이 인터넷 연결을 허용합니다. 다음을 수행하는 것이 좋습니다.

  - 기본 아웃 바운드 규칙을 제거 하지 마세요.
  - 아웃 바운드 인터넷 연결을 거부 하는 다른 아웃 바운드 규칙을 만들지 마세요.

- 트래픽 합니다 **AzureLoadBalancer** 태그를 허용 해야 합니다.

##### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>몇 가지 원본 Ip에 대 한 Application Gateway 액세스를 허용 합니다.

이 시나리오에 대 한 Application Gateway 서브넷에서 Nsg를 사용 합니다. 다음과 같은 제한 사항이 우선 순위에 따라이 서브넷에 적용 합니다.

1. 원본 IP/IP 범위에서에서 들어오는 트래픽을 허용 합니다.
2. 에 대 한 포트 65503-65534에 모든 원본에서 들어오는 요청을 허용 [백 엔드 상태 통신](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)합니다. 이 포트 범위는 Azure 인프라 통신에 필요합니다. 이러한 포트 (잠김) Azure 인증서에 의해 보호 됩니다. 위치에 적절 한 인증서가 없는 외부 엔터티는 해당 끝점의 변경 내용을 시작할 수 없습니다.
3. 들어오는 Azure Load Balancer 프로브 허용 (*AzureLoadBalancer* 태그) 인바운드 가상 네트워크 트래픽 및 (*VirtualNetwork* 태그)에 [네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview)합니다.
4. (모두 거부) 규칙을 사용 하 여 다른 모든 들어오는 트래픽을 차단 합니다.
5. 모든 대상에 대해 인터넷으로의 아웃바운드 트래픽을 허용합니다.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Application Gateway 서브넷에서 지원 되는 사용자 정의 경로

V1 SKU에 대 한 사용자 정의 경로 (Udr) 엔드-투-엔드 요청/응답 통신 변경 하지는 않습니다으로 Application Gateway 서브넷에서 지원 됩니다. 예를 들어, 패킷 검사에 대 한 방화벽 어플라이언스를 가리키도록 Application Gateway 서브넷에서 UDR을 설정할 수 있습니다. 하지만 패킷을 검사 후 의도 한 대상에 연결할 수 있는지 확인 해야 합니다. 이렇게 하지 않으면 잘못 된 상태 프로브 또는 트래픽 라우팅 동작 될 수 있습니다. 학습된 경로 또는 가상 네트워크에서 Azure ExpressRoute 또는 VPN 게이트웨이에서 전파 되는 기본 0.0.0.0/0 경로 포함 됩니다.

V2 SKU에 대 한 Udr은 Application Gateway 서브넷에서 지원 되지 않습니다. 자세한 내용은 [Azure Application Gateway v2 SKU](application-gateway-autoscaling-zone-redundant.md#differences-with-v1-sku)합니다.

> [!NOTE]
> 상태를 사용 하면 Application Gateway 서브넷에서 Udr을 사용 하 여 [백 엔드 상태 보기](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) "알 수 없습니다."로 표시 Application Gateway 로그 및 메트릭을 실패 생성이 됩니다. 백 엔드 상태, 로그 및 메트릭을 볼 수 있도록 Application Gateway 서브넷에서 Udr을 사용 하지 않는 것이 좋습니다.

## <a name="front-end-ip"></a>프런트 엔드 IP

공용 IP 주소, IP 주소를 개인 또는 둘 다 응용 프로그램 게이트웨이 구성할 수 있습니다. 공용 IP는 클라이언트는 인터넷을 통해는 인터넷 연결 VIP (가상 IP)을 통해 액세스 해야 하는 백 엔드를 호스트 하는 경우 필요 합니다. 

공용 IP는 인터넷에 노출 되지 않은 내부 끝점에 대 한 필요 하지 않습니다. 라고 하는 *내부 load balancer* (ILB) 끝점입니다. Application gateway ILB는 인터넷에 노출 되지 않은 내부 기간 업무 응용 프로그램에 유용 합니다. 서비스에도 유용 하 고는 인터넷에 노출 되지 않습니다 하지만 라운드 로빈을 필요로 하는 보안 경계 내에서 다중 계층 응용 프로그램 계층 부하 분산, 세션 인력 또는 SSL 종료 합니다.

1 개인 IP 주소 또는 공용 IP 주소를 하나만 지원 됩니다. Application gateway를 만들 때에 프런트 엔드 IP를 선택 합니다.

- 공용 IP에 대 한 새 공용 IP 주소를 만들 수도 있고 기존 공용 IP를 사용 하 여 application gateway와 동일한 위치에 키를 누릅니다. (정적 또는 동적) 새 공용 IP를 선택 하는 IP 주소 유형을 만드는 경우 나중에 변경할 수 없습니다. 자세한 내용은 [동적 공용 IP 주소와 정적](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-vs-dynamic-public-ip-address)합니다.

- 개인 IP에 대 한 application gateway가 만들어지는 서브넷의 개인 IP 주소를 지정할 수 있습니다. 하나를 지정 하지 않으면, 임의의 IP 주소 서브넷에서 자동으로 선택 됩니다. 자세한 내용은 [내부 부하 분산 장치를 사용 하 여 응용 프로그램 게이트웨이 만들기](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)합니다.

에 연결 된 프런트 엔드 IP 주소를는 *수신기*, 프런트 엔드 IP에서 들어오는 요청을 확인 합니다.

## <a name="listeners"></a>수신기

수신기는 들어오는 연결 요청에 대 한 포트, 프로토콜, 호스트 및 IP 주소를 사용 하 여 확인 하는 논리 엔터티입니다. 수신기를 구성할 때 게이트웨이에서 들어오는 요청에 해당 값과 일치 하는 이러한 값을 입력 해야 합니다.

Azure portal을 사용 하 여 응용 프로그램 게이트웨이 만든 수신기에 대 한 프로토콜 및 포트를 선택 하 여 기본 수신기를 만듭니다. 수신기에서 HTTP2 지원 사용 여부를 선택할 수 있습니다. Application gateway를 만든 후에 해당 기본 수신기의 설정을 편집할 수 있습니다 (*appGatewayHttpListener*/*appGatewayHttpsListener*) 하거나 새 수신기를 만듭니다.

### <a name="listener-type"></a>수신기 유형

선택할 새 수신기를 만들면 [ *기본* 하 고 *다중 사이트*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners)합니다.

- Application gateway 뒤에 단일 사이트를 호스트 하는 경우 기본 선택 합니다. 에 대해 알아봅니다 [기본 수신기를 사용 하 여 응용 프로그램 게이트웨이 만드는 방법](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)합니다.

- 동일한 응용 프로그램 게이트웨이 인스턴스에서 둘 이상의 웹 응용 프로그램 또는 동일한 부모 도메인의 여러 하위 도메인을 구성 하는, 하는 경우 다중 사이트 수신기를 선택 합니다. 다중 사이트 수신기를 호스트 이름을 입력 해야 합니다. Application Gateway는 HTTP 1.1 호스트 헤더를 동일한 공용 IP 주소 및 포트에서 둘 이상의 웹 사이트를 호스트 하는 때문입니다.

#### <a name="order-of-processing-listeners"></a>수신기 처리 순서

V1 SKU에 대 한 수신기는 나열 된 순서 대로 처리 됩니다. 기본 수신기는 들어오는 요청과 일치 하는 경우 수신기 먼저 해당 요청을 처리 합니다. 따라서 기본 수신기 트래픽이 올바른 백 엔드에 라우팅되는지 확인 하기 전에 다중 사이트 수신기를 구성 합니다.

V2 SKU에 대 한 다중 사이트 수신기는 기본 수신기 보다 먼저 처리 됩니다.

### <a name="front-end-ip"></a>프런트 엔드 IP

이 수신기에 연결 하려고 하는 프런트 엔드 IP 주소를 선택 합니다. 수신기는이 IP에서 들어오는 요청을 수신 합니다.

### <a name="front-end-port"></a>프런트 엔드 포트

프런트 엔드 포트를 선택 합니다. 기존 포트를 선택 하거나 새로 만듭니다. 값을 선택 합니다 [허용 되는 포트의 범위](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports)합니다. 80 및 443 같은 잘 알려진 포트 뿐 아니라 적합 한 모든 허용 되는 사용자 지정 포트를 사용할 수 있습니다. 공용 또는 개인 연결 수신기에 대 한 포트를 사용할 수 있습니다.

### <a name="protocol"></a>프로토콜

HTTP 또는 HTTPS를 선택 합니다.

- HTTP를 선택 하면 클라이언트 및 application gateway 간의 트래픽은 암호화 되지 않습니다.

- 원하는 경우 HTTPS를 선택 [SSL 종료](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssl-terminationl) 하거나 [종단 간 SSL 암호화](https://docs.microsoft.com/azure/application-gateway/ssl-overview)합니다. 클라이언트와 응용 프로그램 게이트웨이 간의 트래픽이 암호화 됩니다. 및 application gateway에서 SSL 연결을 종료 합니다. 종단 간 SSL 암호화를 사용 하도록 하려는 경우 HTTPS를 선택 하 고 구성 해야 합니다 **백 엔드 HTTP** 설정 합니다. 이렇게 하면 응용 프로그램 게이트웨이에서 백 엔드에 데이터가 이동할 때 트래픽을 다시 암호화 됩니다.

SSL 종료 및 종단 간 SSL 암호화를 구성 하려면 대칭 키를 파생 하려면 application gateway를 사용 하도록 설정 하려면 수신기에 인증서를 추가 해야 합니다. SSL 프로토콜 사양에 의해 결정 됩니다. 대칭 키에서 게이트웨이로 전송 되는 트래픽 암호화 및 해독에 사용 됩니다. 게이트웨이 인증서는 개인 정보 교환 (PFX) 형식 이어야 합니다. 이 형식에는 게이트웨이 사용 하 여 트래픽 암호화 및 해독 하는 개인 키를 내보낼 수 있습니다.

#### <a name="supported-certificates"></a>지원 되는 인증서

참조 [SSL 종료를 위한 지원 인증서](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination)합니다.

### <a name="additional-protocol-support"></a>추가 프로토콜 지원

#### <a name="http2-support"></a>HTTP2 지원

Http/2 프로토콜 지원은 응용 프로그램 게이트웨이 수신기에 연결 하는 클라이언트에 제공 됩니다. 백 엔드 서버 풀에 대 한 통신은 HTTP/1.1 끝났습니다. 기본적으로 HTTP/2 지원은 사용할 수 없습니다. 다음 Azure PowerShell 코드 조각은이 기능을 사용 하는 방법을 보여 줍니다.

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>WebSocket 지원

WebSocket 지원 기본적으로 사용 됩니다. 활성화 또는 비활성화 하는 사용자 구성 가능 설정은 없습니다. Websocket은 HTTP 및 HTTPS 수신기와 함께 사용할 수 있습니다.

### <a name="custom-error-pages"></a>사용자 지정 오류 페이지

전역 수준 또는 수신기 수준에서 사용자 지정 오류를 정의할 수 있습니다. 하지만 Azure portal에서 전역 수준 사용자 지정 오류 페이지를 만들기는 현재 지원 되지 않습니다. 수신기 수준에서 403 웹 응용 프로그램 방화벽 오류에 대 한 사용자 지정 오류 페이지 또는 502 유지 관리 페이지를 구성할 수 있습니다. 지정 된 오류 상태 코드에 대해 공개적으로 액세스 가능한 blob URL을 지정 해야 합니다. 자세한 내용은 [Application Gateway 사용자 지정 오류 페이지 만들기](https://docs.microsoft.com/azure/application-gateway/custom-error)를 참조하세요.

![Application Gateway 오류 코드](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

전역 사용자 지정 오류 페이지를 참조 하십시오 [Azure PowerShell 구성](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration)합니다.

### <a name="ssl-policy"></a>SSL 정책

SSL 인증서 관리를 중앙 집중화 하 고 암호화 해독 백 엔드 서버 팜에 대 한 오버 헤드를 줄일 수 있습니다. 또한 중앙 집중식된 SSL 처리는 보안 요구 사항에 적합 한 중앙 SSL 정책을 지정할 수 있습니다. 선택할 수 있습니다 *기본*하십시오 *미리 정의 된*, 또는 *사용자 지정* SSL 정책.

SSL 프로토콜 버전을 제어 하는 SSL 정책을 구성할 수 있습니다. TLS1.0, TLS1.1 및 TLS1.2를 거부 하도록 application gateway를 구성할 수 있습니다. 기본적으로 SSL 2.0 및 3.0 비활성화 되 고 구성할 수 없습니다. 자세한 내용은 [Application Gateway SSL 정책 개요](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview)합니다.

수신기를 만든 후 요청 라우팅 규칙을 사용 하 여 연결 합니다. 해당 규칙은 백 엔드 수신기에서 수신 되는 요청 라우팅되는 방식을 결정 합니다.

## <a name="request-routing-rules"></a>라우팅 규칙 요청

기본 규칙을 만들면 Azure portal을 사용 하 여 응용 프로그램 게이트웨이 만들 때 (*rule1*). 이 규칙에는 기본 수신기 바인딩합니다 (*appGatewayHttpListener*) 기본 백 엔드 풀을 사용 하 여 (*appGatewayBackendPool*) 및 기본 백 엔드 HTTP 설정 ( *appGatewayBackendHttpSettings*). 게이트웨이 만든 후에 기본 규칙의 설정을 편집 하거나 새 규칙을 만들 수 있습니다.

### <a name="rule-type"></a>규칙 유형

선택할 규칙을 만들면 [ *기본* 하 고 *경로 기반*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rule)합니다.

- 연결 된 수신기에 대 한 모든 요청에 전달 하려는 경우 기본 선택 (예를 들어 *블로그<i></i>.contoso.com/\*)* 단일 백 엔드 풀에 있습니다.
- 경로 기반 라우팅 특정 백 엔드 풀을 특정 URL 경로에서 요청 하려는 경우 선택 합니다. 경로 패턴은 해당 쿼리 매개 변수 필요가 URL의 경로에 적용 됩니다.

#### <a name="order-of-processing-rules"></a>규칙 처리 순서

V1 SKU에 대 한 패턴 일치 들어오는 요청의 경로 경로 기반 규칙의 URL 경로 맵에 나와 있는 순서 대로 처리 됩니다. 요청 경로 맵에서 둘 이상의 경로에 있는 패턴과 일치 하는 경우 나열 된 경로 먼저 일치 됩니다. 및 해당 경로 사용 하 여 연결 된 백 엔드에 요청 전달 됩니다.

V2 SKU에 대 한 정확한 일치 하는 경우 URL 경로 맵의 경로 순서 보다 높은 우선 순위 둘 이상의 경로 패턴과 일치 하는 요청을 정확 하 게 요청과 일치 하는 경로와 연결 된 백 엔드에 요청 전달 됩니다. 들어오는 요청에 포함 된 경로 구조의 모든 경로 정확히 일치 하지 않습니다 요청 패턴에 일치 하는 경로 기반 규칙 경로 맵 순서 목록에서 처리 됩니다.

### <a name="associated-listener"></a>연결 된 수신기

규칙에 수신기를 연결 되도록 합니다 *요청 라우팅 규칙* 연결 된 수신기에 요청을 라우팅하는 백 엔드 풀을 확인 하기 위해 평가 됩니다.

### <a name="associated-back-end-pool"></a>연결 된 백 엔드 풀

수신기에서 수신 하는 요청을 처리 하는 백 엔드 대상을 포함 하는 백 엔드 풀이 규칙에 연결 합니다.

 - 기본 규칙에 대 한 백 엔드 풀이 하나만 허용 됩니다. 연결 된 수신기에서 모든 요청은 해당 백 엔드 풀으로 전달 됩니다.

 - 경로 기반 규칙에 대 한 각 URL 경로에 해당 하는 여러 백 엔드 풀을 추가 합니다. 입력 된 URL 경로 일치 하는 요청을 해당 백 엔드 풀에 전달 됩니다. 또한 기본 백 엔드 풀을 추가 합니다. 규칙의 모든 URL 경로 일치 하지 않는 요청은 해당 풀으로 전달 됩니다.

### <a name="associated-back-end-http-setting"></a>연결 된 백 엔드 HTTP 설정

각 규칙에 대 한 백 엔드 HTTP 설정을 추가 합니다. 요청 포트 번호, 프로토콜 및이 설정에 지정 된 기타 정보를 사용 하 여 응용 프로그램 게이트웨이에서 백 엔드 대상에 라우팅됩니다.

기본 규칙에 대 한 백 엔드 HTTP 설정은 하나만 허용 됩니다. 연결 된 수신기에서 모든 요청은이 HTTP 설정을 사용 하 여 해당 백 엔드 대상에 전달 됩니다.

각 규칙에 대 한 백 엔드 HTTP 설정을 추가 합니다. 요청 포트 번호, 프로토콜 및이 설정에 지정 된 기타 정보를 사용 하 여 응용 프로그램 게이트웨이에서 백 엔드 대상에 라우팅됩니다.

기본 규칙에 대 한 백 엔드 HTTP 설정은 하나만 허용 됩니다. 연결 된 수신기에서 모든 요청은이 HTTP 설정을 사용 하 여 해당 백 엔드 대상에 전달 됩니다.

경로 기반 규칙에 대 한 각 URL 경로에 해당 하는 여러 백 엔드 HTTP 설정을 추가 합니다. 이 설정에서 URL 경로 일치 하는 요청은 각 URL 경로에 해당 하는 HTTP 설정을 사용 하 여 해당 백 엔드 대상에 전달 됩니다. 또한 기본 HTTP 설정을 추가 합니다. 이 규칙에서 모든 URL 경로 일치 하지 않는 요청은 기본 HTTP 설정을 사용 하 여 기본 백 엔드 풀에 전달 됩니다.

### <a name="redirection-setting"></a>리디렉션 설정

기본 규칙에 대 한 리디렉션 구성 된 경우 연결 된 수신기에서 모든 요청에 대상이 리디렉션됩니다. 이것이 *전역* 리디렉션. 리디렉션 경로 기반 규칙으로 구성 된 경우 특정 사이트 영역에 대 한 요청만 리디렉션됩니다. 으로 표시 되는 쇼핑 카트 영역과 예로 */cart/\** 합니다. 이것이 *경로 기반* 리디렉션.

리디렉션에 대 한 자세한 내용은 참조 하세요. [Application Gateway 리디렉션 개요](https://docs.microsoft.com/azure/application-gateway/redirect-overview)합니다.

#### <a name="redirection-type"></a>리디렉션 유형

필요한 의도치 않은 리디렉션의 유형을 선택 합니다. *Permanent(301)* , *Temporary(307)* 를 *Found(302)* , 또는 *other(303) 참조*합니다.

#### <a name="redirection-target"></a>리디렉션 대상

리디렉션 대상으로 다른 수신기 또는 외부 사이트를 선택 합니다.

##### <a name="listener"></a>수신기

게이트웨이에서 다른 하나의 수신기에서 트래픽을 리디렉션할 리디렉션 대상으로 수신기를 선택 합니다. HTTP-HTTPS 리디렉션을 사용 하도록 설정 하려는 경우이 설정은 필수입니다. 들어오는 HTTPS 요청을 확인 하는 대상 수신기는 들어오는 HTTP 요청에 대 한 확인 하는 원본 수신기에서 트래픽을 리디렉션합니다. 리디렉션 대상에 전달 되는 요청에 쿼리 문자열 및 원래 요청의 경로 포함할 수도 있습니다.

![응용 프로그램 게이트웨이 구성 요소 대화 상자](./media/configuration-overview/configure-redirection.png)

HTTP-HTTPS 리디렉션이 대 한 자세한 내용은 다음을 참조 하세요.
- [Azure portal을 사용 하 여 HTTP-HTTPS 리디렉션](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal)
- [PowerShell을 사용 하 여 HTTP-HTTPS 리디렉션](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell)
- [Azure CLI를 사용 하 여 HTTP-HTTPS 리디렉션](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

##### <a name="external-site"></a>외부 사이트

외부 사이트에이 규칙을 사용 하 여 연결 된 수신기에 대 한 트래픽을 리디렉션할 하려는 경우 외부 사이트를 선택 합니다. 리디렉션 대상에 전달 되는 요청에서 원래 요청의 쿼리 문자열을 포함 하도록 선택할 수 있습니다. 원래 요청에 있는 외부 사이트로 경로 전달할 수 없습니다.

리디렉션에 대 한 자세한 내용은 다음을 참조 하세요.
- [PowerShell을 사용 하 여 트래픽을 외부 사이트로 리디렉션](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell)
- [CLI를 사용 하 여 트래픽을 외부 사이트로 리디렉션](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-the-http-header-setting"></a>HTTP 헤더 설정 다시 작성

이 설정을 추가, 제거 하거나 HTTP 요청 및 응답 헤더를 요청 하는 동안 업데이트 및 응답 패킷의 클라이언트와 백 엔드 풀 간에 이동 합니다. PowerShell 통해이 기능에만 구성할 수 있습니다. Azure portal 및 CLI 지원은 아직 사용할 수 없습니다. 자세한 내용은 다음을 참조하세요.

 - [HTTP 헤더 개요를 다시 작성](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)
 - [HTTP 헤더 다시 쓰기를 구성 합니다.](https://docs.microsoft.com/azure/application-gateway/add-http-header-rewrite-rule-powershell#specify-your-http-header-rewrite-rule-configuration)

## <a name="http-settings"></a>HTTP 설정

Application gateway는 여기서 지정 하는 구성을 사용 하 여 백 엔드 서버로 트래픽을 라우팅합니다. HTTP 설정이 만든 후 하나 이상의 요청 라우팅 규칙을 사용 하 여 연결 해야 합니다.

### <a name="cookie-based-affinity"></a>쿠키 기반 선호도

이 기능은 동일한 서버의 사용자 세션을 유지 하려는 경우에 유용 합니다. 게이트웨이 관리형 쿠키를 응용 프로그램 게이트웨이 후속 트래픽을 사용자 세션에서 처리를 위해 동일한 서버에 있습니다. 세션 상태가 저장 되 면 로컬 서버에서 사용자 세션에 대 한 것입니다. 응용 프로그램 쿠키 기반 선호도 처리할 수 없는 경우이 기능을 사용할 수 없습니다. 를 사용 하려면 클라이언트가 쿠키를 지원 하는지 확인 합니다.

### <a name="connection-draining"></a>연결 드레이닝

연결 드레이닝 정상적으로 계획 된 서비스 업데이트 도중 백 엔드 풀 멤버를 제거할 수 있습니다. 규칙을 만드는 동안이 설정은 백 엔드 풀의 모든 멤버에 적용할 수 있습니다. 백 엔드 풀의 모든 등록 해제 인스턴스에 새 요청을 받지는 확인 합니다. 한편, 기존 요청은 구성 된 제한 시간 내에 완료 되도록 허용 됩니다. 연결 드레이닝 API 호출에 의해 명시적으로 백 엔드 풀에서 제거 되는 백 엔드 인스턴스에 적용 됩니다. 도로 보고 되는 백 엔드 인스턴스에 적용 됩니다 *비정상* 상태에 따라 검색 합니다.

### <a name="protocol"></a>프로토콜

Application Gateway는 라우팅 요청을 백 엔드 서버에 대 한 HTTP 및 HTTPS를 지원 합니다. HTTP를 선택할 경우에 백 엔드 서버로 트래픽을 암호화 되지 않습니다. 암호화 되지 않은 통신을 허용 하지 않으면 HTTPS를 선택 합니다.

이 설정은 수신기에서는 HTTPS를 사용 하 여 결합 [종단 간 SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview)합니다. 이 옵션을 사용 하면 안전 하 게 백 엔드에 암호화 된 중요 한 데이터를 전송할 수 있습니다. 보안 통신을 허용 하도록 인증서를 사용 하 여 종단 간 SSL이 활성화 된 백 엔드 풀의 각 백 엔드 서버를 구성 해야 합니다.

### <a name="port"></a>포트

이 설정은 application gateway에서 백 엔드 서버 트래픽을 수신 하는 위치는 포트를 지정 합니다. 1에서 ~ 65535 범위의 포트를 구성할 수 있습니다.

### <a name="request-timeout"></a>요청 시간 초과

이 설정은 application gateway가 "연결 시간이 초과 되었습니다" 오류 메시지를 반환 하기 전에 백 엔드 풀에서 응답을 수신 대기할 시간 (초)입니다.

### <a name="override-back-end-path"></a>백 엔드 경로 재정의 합니다.

이 요청은 백 엔드로 전달 되는 경우 사용에 대 한 선택적 사용자 지정 전달 경로 구성할 수 있습니다. 사용자 지정 경로 일치 하는 들어오는 경로 부분이 합니다 **백 엔드 경로 재정의** 필드는 전달 된 경로에 복사 됩니다. 다음 표에서이 기능의 작동 방식을 보여 줍니다.

- 경우 HTTP 설정은 기본 요청 라우팅 규칙에 연결 됩니다.

  | 원래 요청  | 백 엔드 경로 재정의 합니다. | 백 엔드에 전달 되는 요청 |
  | ----------------- | --------------------- | ---------------------------- |
  | /home/            | /override/            | 재정의/home / /              |
  | /home/secondhome/ | /override/            | /override/home/secondhome/   |

- 경우 HTTP 설정은 경로 기반 요청 라우팅 규칙에 연결 됩니다.

  | 원래 요청           | 경로 규칙       | 백 엔드 경로 재정의 합니다. | 백 엔드에 전달 되는 요청 |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /pathrule*      | /override/            | 재정의/home / /              |
  | /pathrule/home/secondhome/ | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /home/                     | /pathrule*      | /override/            | 재정의/home / /              |
  | /home/secondhome/          | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | /override/            | /override/                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /override/            | /override/secondhome/        |

### <a name="use-for-app-service"></a>App service에 대해 사용

Azure App Service 백 엔드에 대 한 두 개의 필수 설정을 선택 하 여 UI 바로 가기를 이것이입니다. 이 통해 **백 엔드 주소에서 호스트 이름을 선택**, 새 사용자 지정 프로브를 만듭니다. (자세한 내용은 참조는 [백 엔드 주소에서 호스트 이름을 선택](#pick) 이 문서의 섹션을 설정 합니다.) 새 프로브를 만들고 프로브 헤더는 백 엔드 멤버의 주소에서 선택 됩니다.

### <a name="use-custom-probe"></a>사용자 지정 프로브 사용

이 설정은 연결 된 [사용자 지정 프로브](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe) HTTP 설정으로 합니다. 하나의 사용자 지정 프로브는 HTTP 설정을 사용 하 여 연결할 수 있습니다. 사용자 지정 프로브를 명시적으로 연결 하지 않은 경우는 [기본 프로브](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings) 백엔드의 상태를 모니터링 하는 데 사용 됩니다. 많이 제어 방법을 사용 하 여 백 엔드의 상태 모니터링에 대 한 사용자 지정 프로브를 만드는 것이 좋습니다.

> [!NOTE]
> 해당 HTTP 설정을 명시적으로 수신기와 연결 된 경우가 아니면 사용자 지정 프로브는 백 엔드 풀의 상태를 모니터링 하지 않습니다.

### <a id="pick"/></a>백 엔드 주소에서 호스트 이름을 선택 합니다.

이 기능을 동적으로 설정 합니다 *호스트* 백 엔드 풀의 호스트 이름에 요청의 헤더입니다. IP 주소 또는 FQDN을 사용합니다.

이 기능은 백 엔드의 도메인 이름을 application gateway의 DNS 이름에서 다른 백 엔드 특정 호스트 헤더 또는 SNI 서버 이름 표시 () 확장 올바른 끝점으로 해석 하는 경우 도움이 됩니다.

사례는 예제는 백 엔드로 다중 테 넌 트 서비스입니다. App service는 단일 IP 주소를 사용 하 여 공유 공간을 사용 하는 다중 테 넌 트 서비스입니다. 따라서 app service는 사용자 지정 도메인 설정에 구성 된 호스트 이름을 통해 액세스할 수만 있습니다.

기본적으로 사용자 지정 도메인 이름은 *example.azurewebsites.<i> </i>net*합니다. Application gateway의 FQDN 또는 app service에서 명시적으로 등록 된 호스트 이름을 통해 응용 프로그램 게이트웨이 사용 하 여 app service에 액세스 하려면 app service의 호스트 이름 원래 요청에 호스트 이름을 재정의할 수 있습니다. 이 작업을 수행 하려면 사용 하도록 설정 합니다 **백 엔드 주소에서 호스트 이름을 선택** 설정 합니다.

App service에 기존 사용자 지정 DNS 이름이 매핑되는 사용자 지정 도메인에 대 한이 설정을 사용 하도록 설정 필요가 없습니다.

> [!NOTE]
> 이 설정이 필요 하지 않습니다 PowerApps에 대 한 App Service Environment에 대 한 전용된 배포는 합니다.

### <a name="host-name-override"></a>호스트 이름 재정

이 기능을 대체 합니다 *호스트* 지정 하는 호스트 이름 사용 하 여 application gateway에서 들어오는 요청의 헤더입니다.

예를 들어 경우 *www.contoso<i></i>.com* 에 지정 된 된 **호스트 이름** 원래 요청을 설정 *https:/<i></i>/appgw.eastus.cloudapp.net/path1* 로 변경 됩니다 *https:/<i></i>/www.contoso.com/path1* 백 엔드 서버로 요청은 전달 하는 경우.

## <a name="back-end-pool"></a>백 엔드 풀

네 가지 유형의 백 엔드 멤버 백 엔드 풀을 가리키도록 설정할 수 있습니다: 특정 가상 컴퓨터, 가상 머신 확장 집합, FQDN/IP 주소, 또는 app service입니다. 각 백 엔드 풀은 동일한 형식의 여러 멤버를 가리킬 수 있습니다. 동일한 백 엔드 풀에서 다른 형식의 멤버를 가리키는 지원 되지 않습니다.

백 엔드 풀을 만든 후 하나 이상의 요청 라우팅 규칙을 사용 하 여 연결 해야 합니다. 또한 application gateway에서 각 백 엔드 풀에 대 한 상태 프로브를 구성 해야 합니다. 요청 라우팅 규칙 조건이 충족 되 면 application gateway는 해당 백 엔드 풀의 정상 서버 (상태 프로브에 의해 결정)로 트래픽을 전달 합니다.

## <a name="health-probes"></a>상태 프로브

응용 프로그램 게이트웨이 기본적으로 해당 백 엔드의 모든 리소스의 상태를 모니터링합니다. 하지만 상태 모니터링 하는 큰 제어할 각 백 엔드 HTTP 설정에 대 한 사용자 지정 프로브를 만드는 것이 좋습니다. 사용자 지정 프로브를 구성 하는 방법에 알아보려면 참조 [사용자 지정 상태 프로브 설정](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings)합니다.

> [!NOTE]
> 사용자 지정 상태 프로브를 만든 후에 백 엔드 HTTP 설정에 연결 해야 합니다. 해당 하는 HTTP 설정을 명시적으로 수신기와 연결 된 경우가 아니면 사용자 지정 프로브를 백 엔드 풀의 상태를 모니터링 하지 않습니다.

## <a name="next-steps"></a>다음 단계

응용 프로그램 게이트웨이 구성 요소에 대 한 파악 했으므로 다음을 수행할 수 있습니다.

- [Azure portal에서 응용 프로그램 게이트웨이 만들기](quick-create-portal.md)
- [PowerShell을 사용 하 여 응용 프로그램 게이트웨이 만들기](quick-create-powershell.md)
- [Azure CLI를 사용 하 여 응용 프로그램 게이트웨이 만들기](quick-create-cli.md)
