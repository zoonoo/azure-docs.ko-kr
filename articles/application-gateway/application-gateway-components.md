---
title: Azure 응용 프로그램 게이트웨이 구성 요소
description: 이 문서에서는 Application Gateway에서 여러 구성 요소 정보
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 44c8b331ebb258c39a003c91e0711e6dfb87cb12
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076299"
---
# <a name="application-gateway-components"></a>응용 프로그램 게이트웨이 구성 요소

 Application gateway는 단일 클라이언트에 대 한 연결 지점으로 사용 됩니다. Azure Vm, 가상 머신 확장 집합, App Services 또는 온-프레미스/외부 서버와 같은 여러 백 엔드 풀에 걸쳐 트래픽을 응용 프로그램을 배포합니다. 이 위해이 문서에서 설명 하는 여러 구성 요소 사용 합니다.

![application-gateway-components](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-address"></a>프런트 엔드 IP 주소

프런트 엔드 IP 주소에는 응용 프로그램 게이트웨이에 연결 된 IP 주소가입니다. 공용 IP 주소 또는 개인 IP 주소를 포함 하도록 응용 프로그램 게이트웨이 구성할 수 있습니다. 하나의 공용 IP 주소 또는 하나의 개인 IP 주소가 지원 됩니다. 가상 네트워크 및 공용 IP 주소는 application gateway와 동일한 위치에 있어야 합니다.

프런트 엔드 IP 주소에 연결 되는 *수신기* 만들어진 후 합니다. 

### <a name="static-vs-dynamic-public-ip-address"></a>정적 포트 대 동적 공용 IP 주소

V1 SKU 고정 내부 IP 주소를 지원 하지만 정적 공용 IP 주소를 지원 하지 않습니다. Application Gateway를 중지한 후에 시작하면 VIP가 변경될 수 있습니다. Application Gateway와 연결된 DNS 이름은 게이트웨이 수명 중에 변경되지 않습니다. 그러므로 CNAME 별칭을 사용하고 해당 별칭이 Application Gateway의 DNS 주소를 가리키도록 설정하는 것이 좋습니다.

Application Gateway v2 SKU 고정 내부 IP 주소 뿐만 아니라 정적 공용 IP 주소를 지원합니다. 

## <a name="listeners"></a>수신기

Application gateway를 사용 하 여 시작 하기 전에 하나 이상의 수신기를 추가 해야 합니다. 수신기는 들어오는 연결 요청을 확인 하 고 프로토콜, 포트, 호스트 및 IP 주소 연결 프로토콜, 포트, 호스트 및 수신기 구성과 사용 하 여 연결 된 IP 주소를 사용 하 여 일치 하는 요청 된 경우 요청을 수락 하는 논리 엔터티입니다. 응용 프로그램 게이트웨이에 연결 된 리스너가 여러 개 있을 수 있으며 동일한 프로토콜에 사용할 수 있습니다. 수신기는 클라이언트에서 들어오는 요청을 감지 후 Application Gateway는 들어오는 요청을 수신 하는 수신기에 대 한 정의 하는 요청 라우팅 규칙을 사용 하 여 백 엔드 풀에서 멤버에 이러한 요청을 라우팅합니다.

수신기는 다음 포트와 프로토콜을 지원합니다.

### <a name="ports"></a>포트

클라이언트 요청에 대 한 수신기가 수신 하는 포트입니다. 1 사이에 속하는 65502 V1 SKU 및 1을 65199 V2 SKU에 대 한 포트를 구성할 수 있습니다.

### <a name="protocols"></a>프로토콜

Application gateway는 다음 네 가지 프로토콜을 지원 합니다. HTTP, HTTPS, http/2 및 WebSocket

수신기 구성에서 HTTP 및 HTTPS 프로토콜 간의 선택 명시적으로 지정 합니다. 합니다 [http/2 및 Websocket 프로토콜에 대 한 지원](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic) 기본적으로 제공 됩니다. [Websocket 지원](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) 기본적으로 사용 됩니다. WebSocket 지원을 선택적으로 사용하거나 사용하지 않도록 설정하는 사용자 구성 가능 설정은 없습니다. Websocket은 HTTP 및 HTTPS 수신기와 함께 사용할 수 있습니다. Application Gateway 수신기에 연결하는 클라이언트의 경우에만 HTTP/2 프로토콜이 지원됩니다. 백 엔드 서버 풀에 대한 통신은 HTTP/1.1을 통해 이루어집니다. 기본적으로 HTTP/2 지원은 사용할 수 없습니다. 사용 하도록 선택할 수 있습니다.

SSL 종료를 위한 HTTPS 수신기를 사용할 수 있습니다. HTTPS 수신기를 오프 로드 하는 암호화 및 암호 해독 작업 응용 프로그램 게이트웨이에 웹 서버 오버 헤드에서 부담이 되지 않습니다. 응용 프로그램 고객이 비즈니스 논리에 집중 하 게 됩니다.

### <a name="custom-error-pages"></a>사용자 지정 오류 페이지

응용 프로그램 게이트웨이 사용 하면 기본 오류 페이지를 표시 하는 대신 사용자 지정 오류 페이지를 만들 수 있습니다. 사용자 지정 오류 페이지를 사용하여 자체 브랜딩과 레이아웃을 사용할 수 있습니다. 요청이 백엔드에 도달할 수 없을 때 애플리케이션 게이트웨이가 사용자 지정 오류 페이지를 표시할 수 있습니다. 자세한 내용은 [Application Gateway에 대 한 사용자 지정 오류 페이지](https://docs.microsoft.com/azure/application-gateway/custom-error)합니다.

### <a name="types-of-listeners"></a>수신기의 형식

수신기는 다음과 같은 두 종류가 있습니다.

- **기본**: Application gateway의 IP 주소에 대 한 단일 DNS 매핑을 권한이 있는 단일 도메인 사이트로이 유형의 수신기 수신 대기 합니다. 이 수신기 구성은 응용 프로그램 게이트웨이 뒤에 단일 사이트를 호스팅하는 경우에 필요 합니다.
- **다중 사이트**: 이 수신기 구성은 동일한 응용 프로그램 게이트웨이 인스턴스에서 둘 이상의 웹 응용 프로그램을 구성한 경우에 필요 합니다. 이 옵션을 사용 하면 응용 프로그램 게이트웨이를 최대 100 개의 웹 사이트를 추가 하 여 배포에 대 한 보다 효율적인 토폴로지를 구성할 수 있습니다. 각 웹 사이트는 고유한 백 엔드 풀로 이동할 수 있습니다. 예를 들면 다음과 같습니다.  세 가지 하위 도메인에 대 한-abc.contoso.com, xyz.contoso.com 및 application gateway의 IP 주소를 가리키는 pqr.contoso.com 합니다. 형식의 세 가지 수신기를 만들 *다중 사이트* 해당 포트에 대 한 각 수신기를 구성 하 고 프로토콜 설정 합니다. 자세한 내용은 [다중 사이트 호스팅](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview)합니다.

수신기를 만든 후 수신기에서 수신 하는 요청 백 엔드에 라우팅되는 방법을 결정 하는 요청 라우팅 규칙을 사용 하 여 연결 합니다.

수신기는 표시된 순서대로 처리됩니다. 이러한 이유로 기본 수신기는 들어오는 요청과 일치 하는 경우 먼저 처리 합니다. 트래픽이 올바른 백 엔드로 라우팅되려면 다중 사이트 수신기를 기본 수신기보다 먼저 구성해야 합니다.

## <a name="request-routing-rule"></a>라우팅 규칙 요청

이 application gateway의 가장 중요 한 구성 요소 및이 규칙을 사용 하 여 연결 된 수신기에 트래픽이 라우팅되는 방법을 결정 합니다. 규칙은 수신기, 백 엔드 서버 풀 및 백 엔드 HTTP 설정에 바인딩합니다. 요청을 수신기에서 수락 되 면 요청 라우팅 규칙은 백 엔드에 전달 하거나 다른 곳에서 리디렉션 요청 인지 여부를 결정 합니다. 요청을 백 엔드에 전달할 확인할 요청 라우팅 규칙을 정의 하는 백 엔드 서버 풀 것을 전달 해야 합니다. 또한 요청 라우팅 규칙 요청 헤더를 다시 작성 하려는 경우 또한 결정 합니다. 하나의 수신기 하나만 규칙에 연결할 수 있습니다.

두 가지 유형의 요청 라우팅 규칙이 있을 수 있습니다.

- **기본:** 연결 된 수신기에서 모든 요청 (예: blog.contoso.com/*) 관련된 HTTP 설정을 사용 하 여 연결 된 백 엔드 풀에 전달 됩니다.
- **경로 기반:** 이 규칙 유형을 사용 하면 요청에 URL을 기준으로 특정 백 엔드 풀에 연결 된 수신기에서 요청을 라우팅할 수 있습니다. 요청에서 URL의 경로 경로 기반 규칙에 경로 패턴과 일치 하는 경우 해당 규칙을 사용 하 여 요청이 라우팅됩니다. 경로 패턴은 해당 쿼리 매개 변수 필요가 URL의 경로에 적용 됩니다. 요청에 라우팅되는 경우 경로 기반 규칙, 수신기에서 요청 URL의 경로 일치 하지 않습니다 합니다 *기본* 백 엔드 풀 및 *기본* HTTP 설정입니다. 자세한 내용은 [URL 기반 라우팅](https://docs.microsoft.com/azure/application-gateway/url-route-overview)합니다.

### <a name="redirection-support"></a>리디렉션 지원

또한 요청 라우팅 규칙을 사용 하면 응용 프로그램 게이트웨이에서 트래픽을 리디렉션할 수 있습니다. 이는 일반 리디렉션 메커니즘이므로 규칙을 사용하여 정의하는 모든 포트 간에서 리디렉션할 수 있습니다. 다른 수신기 (도움이 되는 자동 HTTP-HTTPS 리디렉션 사용 하도록 설정) 또는 외부 사이트 리디렉션 대상 선택, 일시적 또는 영구적으로 적용 되도록 리디렉션을 선택 하거나 리디렉션된 URL에 URI 경로 및 쿼리 문자열을 추가 선택할 수 있습니다. 자세한 내용은 [응용 프로그램 게이트웨이에서 트래픽을 리디렉션할](https://docs.microsoft.com/azure/application-gateway/redirect-overview)합니다.

#### <a name="rewrite-http-headers"></a>HTTP 헤더 다시 쓰기

요청 라우팅 규칙 추가, 제거 또는 HTTP (S) 업데이트 요청을 사용 하 여 및 응답 헤더를 요청 하는 동안 및 응답 패킷을 application gateway 통해 클라이언트와 백 엔드 풀 간에 이동 합니다. 헤더만 설정할 수 없습니다 정적 값 뿐만 아니라 다른 헤더 및 중요 한 서버 변수입니다. 이 클라이언트의 백 엔드에 대 한 중요 한 정보를 제거 등 추가 보안 조치 추가 IP 주소를 추출 하는 등의 몇 가지 중요 한 사용 사례를 수행 하는 데 도움이 됩니다. 자세한 내용은 [application gateway에서 다시 작성 하는 HTTP 헤더](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)합니다.

## <a name="http-settings"></a>HTTP 설정

응용 프로그램 게이트웨이에 경로 트래픽 (HTTP 설정을 연결 되어 있는 요청 라우팅 규칙에 지정 됨) 서버 프로토콜, 포트 번호를 사용 하 여 백 엔드 및이 구성 요소에 지정 된 다른 설정 합니다. 포트 및 HTTP 설정에 사용 되는 프로토콜에 따라서 종단 간 SSL을 수행 하거나 암호화 되지 않은 응용 프로그램 게이트웨이와 백 엔드 서버 간의 트래픽을 암호화 여부를 결정 합니다. 또한이 구성 요소는 하는 데 사용: 사용자 세션을 사용 하 여 동일한 서버에 유지할지 여부를 확인 합니다 [쿠키 기반 세션 선호도](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity), 정상 백 엔드 풀 멤버를 사용 하 여 제거를 수행할 [연결 드레이닝](https://docs.microsoft.com/azure/application-gateway/overview#connection-draining), 백 엔드 상태를 모니터링, 요청 시간 제한 간격을 설정 하 고, 호스트 이름 및 요청에 대 한 경로 재정의할 사용자 지정 프로브를 연결 및 App service 백 엔드에 대 한 백 엔드 설정을 지정 하려면 한 번의 클릭 용이성을 제공 합니다. 

## <a name="backend-pool"></a>백 엔드 풀

백 엔드 풀 요청을 처리 하는 백 엔드 서버로 요청을 라우팅하기 위해 사용 됩니다. Nic가 백 엔드 풀 구성 될 수 있습니다, 가상 머신 확장 집합, 공용 IP 주소, 내부 IP 주소, FQDN 및 다중 테 넌 트 백 엔드를 Azure App Service를 선택 합니다. 응용 프로그램 게이트웨이 백 엔드 풀 멤버는 가용성 집합에 연결 되지 않습니다. Application Gateway와 통신할 수 가상 네트워크 외부 인스턴스와 이기에 클러스터, 데이터 센터 또는 Azure 외부에서 백 엔드 풀의 멤버 수 있으므로 IP 연결이으로. 백 엔드 풀 멤버로 내부 IP를 사용할 계획인 경우 [VNET 피어링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 또는 [VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)가 필요합니다. VNet 피어 링은 지원 되며에 대 한 유용한 다른 가상 네트워크에서 트래픽의 부하를 분산 합니다. Application gateway 통신할 수도 온-프레미스 서버에 ExpressRoute 또는 VPN 터널과 연결 되어 있는 경우으로 트래픽이 허용 됩니다.

다양 한 유형의 요청에 대 한 다른 백 엔드 풀을 만들 수 있습니다. 예를 들어 일반 요청에 대 한 하나의 백 엔드 풀 및 응용 프로그램에 대 한 마이크로 서비스에 대 한 요청에 대 한 다른 백 엔드 풀을 만듭니다.

## <a name="health-probes"></a>상태 프로브

기본적으로 application gateway는 백 엔드 풀의 모든 리소스의 상태를 모니터링 및 자동으로 풀에서 비정상으로 간주 한 모든 리소스를 제거 합니다. 계속 해 서 비정상 인스턴스를 모니터링 하 고 사용할 수 있게 하 고 상태 프로브에 응답 정상 백 엔드 풀에 다시 추가 합니다. 기본 상태 프로브 모니터링 사용 외에도 애플리케이션 프로그의 요구 사항에 맞게 상태 프로브를 사용자 지정할 수도 있습니다. 사용자 지정 프로브를 통해 상태 모니터링을 보다 세부적으로 제어할 수 있습니다. 사용자 지정 프로브를 사용 하는 경우 프로브 간격, URL 경로 테스트 하려면 및 백 엔드 풀 인스턴스를 비정상으로 표시 되기 전까지 허용할 실패 응답 횟수를 구성할 수 있습니다. 각 백 엔드 풀의 상태를 모니터링 하는 사용자 지정 프로브를 구성 하는 것이 좋습니다. 자세한 내용은 [Application gateway의 상태를 모니터링](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview)합니다.

## <a name="next-steps"></a>다음 단계

에 응용 프로그램 게이트웨이 구성 요소에 대해 학습 한 후 다음을 수행할 수 있습니다.
* [Azure portal에서 응용 프로그램 게이트웨이 만들기](quick-create-portal.md)
* [Azure PowerShell을 사용 하 여 응용 프로그램 게이트웨이 만들기](quick-create-powershell.md)
* [Azure CLI를 사용 하 여 응용 프로그램 게이트웨이 만들기](quick-create-cli.md)
