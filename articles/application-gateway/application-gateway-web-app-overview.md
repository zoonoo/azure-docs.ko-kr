---
title: 다중 테넌트 백 엔드
titleSuffix: Azure Application Gateway
description: 이 페이지에서는 다중 테넌트 백 엔드에 대한 Application Gateway 지원의 개요를 제공합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: a171dc795e685655b5a3c73d088d3963c2aaa4ae
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312312"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>앱 서비스와 같은 다중 테넌트 백 엔드에 대한 응용 프로그램 게이트웨이 지원

웹 서버의 다중 테넌트 아키텍처 디자인에서 여러 웹 사이트가 동일한 웹 서버 인스턴스에서 실행되고 있습니다. 호스트 이름은 호스트되는 다른 응용 프로그램을 구분하는 데 사용됩니다. 기본적으로 애플리케이션 게이트웨이는 클라이언트에서 들어오는 HTTP 호스트 헤더를 변경하지 않으며 변경되지 않은 헤더를 백 엔드로 보냅니다. 이는 NIC, 가상 시스템 규모 집합, 공용 IP 주소, 내부 IP 주소 및 FQDN과 같은 백 엔드 풀 멤버에 적합하며, 이러한 문제는 올바른 끝점으로 해결하기 위해 특정 호스트 헤더 또는 SNI 확장에 의존하지 않습니다. 그러나 Azure App 서비스 웹 앱 및 Azure API 관리와 같은 많은 서비스가 있는데, 이는 본질적으로 다중 테넌트이며 특정 호스트 헤더 또는 SNI 확장을 사용하여 올바른 끝점으로 해결합니다. 일반적으로 응용 프로그램 게이트웨이와 연결된 DNS 이름인 응용 프로그램의 DNS 이름은 백 엔드 서비스의 도메인 이름과 다릅니다. 따라서 응용 프로그램 게이트웨이에서 받은 원래 요청의 호스트 헤더는 백 엔드 서비스의 호스트 이름과 다다. 따라서 응용 프로그램 게이트웨이에서 백 엔드에 대한 요청의 호스트 헤더가 백 엔드 서비스의 호스트 이름으로 변경되지 않으면 다중 테넌트 백엔드가 요청을 올바른 끝점으로 확인할 수 없습니다. 

Application Gateway는 사용자가 백 엔드의 호스트 이름에 따라 요청의 HTTP 호스트 헤더를 재정의할 수 있는 기능을 제공합니다. 이 기능을 사용하면 Azure 앱 서비스 웹앱 및 API 관리 같은 다중 테넌트 백 엔드를 지원할 수 있습니다. 이 기능은 v1/v2 표준 및 WAF SKU에 모두 사용할 수 있습니다. 

![호스트 재정의](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> ASE는 다중 테넌트 리소스인 Azure 앱 서비스와 달리 전용 리소스이므로 AZURE 앱 서비스 환경(ASE)에는 적용되지 않습니다.

## <a name="override-host-header-in-the-request"></a>요청에서 호스트 헤더 재정의

호스트 재정의를 지정하는 기능은 [HTTP 설정에](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) 정의되어 있으며 규칙을 만드는 동안 모든 백 엔드 풀에 적용할 수 있습니다. 다중 테넌트 백 엔드에 대한 호스트 헤더 및 SNI 확장을 재정의하는 다음 두 가지 방법이 지원됩니다.

- HTTP 설정에서 명시적으로 입력된 고정 값으로 호스트 이름을 설정하는 기능입니다. 이 기능을 사용하면 특정 HTTP 설정이 적용되는 백 엔드 풀에 대한 모든 트래픽에 대해 호스트 헤더가 이 값으로 재정의됩니다. 끝에서 끝까지 TLS를 사용하는 경우 이 재정의된 호스트 이름은 SNI 확장에 사용됩니다. 이 기능은 백 엔드 풀 팜이 들어오는 고객 호스트 헤더와 다른 호스트 헤더를 예상하는 시나리오를 지원할 수 있습니다.

- 백 엔드 풀 멤버의 IP 또는 FQDN에서 호스트 이름을 파생하는 기능. 또한 HTTP 설정은 개별 백 엔드 풀 멤버에서 호스트 이름을 파생하는 옵션으로 구성된 경우 백 엔드 풀 멤버의 FQDN에서 호스트 이름을 동적으로 선택하는 옵션을 제공합니다. 끝에서 끝까지 TLS를 사용하는 경우 이 호스트 이름은 FQDN에서 파생되며 SNI 확장에 사용됩니다. 이 기능을 사용하면 백 엔드 풀이 Azure 웹앱 같은 다중 테넌트 PaaS 서비스를 두 개 이상 가질 수 있고 각 멤버에 대한 요청의 호스트 헤더에 FQDN에서 파생된 호스트 이름이 포함되는 시나리오를 지원할 수 있습니다. 이 시나리오를 구현하기 위해 [백 엔드 주소에서 hostname이라는](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address) HTTP 설정의 스위치를 사용하여 원래 요청의 호스트 헤더를 백 엔드 풀에 언급 된 헤더로 동적으로 재정의합니다.  예를 들어 백 엔드 풀 FQDN에 "contoso11.azurewebsites.net" 및 "contoso22.azurewebsites.net"이 포함되어 있는 경우 contoso.com 원래 요청의 호스트 헤더는 contoso11.azurewebsites.net 또는 contoso22.azurewebsites.net 요청이 적절한 백 엔드 서버로 전송될 때 재정의됩니다. 

  ![웹앱 시나리오](./media/application-gateway-web-app-overview/scenario.png)

이 기능을 사용하여 고객이 HTTP 설정 및 사용자 지정 프로브의 옵션을 적절한 구성으로 지정합니다. 그러면 규칙을 사용하여 이 설정이 수신기와 백 엔드 풀에 연결됩니다.

## <a name="special-considerations"></a>특별 고려 사항

### <a name="tls-termination-and-end-to-end-tls-with-multi-tenant-services"></a>다중 테넌트 서비스를 통해 TLS 종료 및 종단 간 TLS

TLS 종료 및 종단 간 TLS 암호화는 다중 테넌트 서비스에서 지원됩니다. 응용 프로그램 게이트웨이에서 TLS 종료의 경우 TLS 인증서를 응용 프로그램 게이트웨이 수신기에 계속 추가해야 합니다. 그러나 종단 간 TLS의 경우 Azure App 서비스 웹 앱과 같은 신뢰할 수 있는 Azure 서비스는 응용 프로그램 게이트웨이의 백엔드를 허용 목록에 추가할 필요가 없습니다. 따라서 인증 인증서를 추가할 필요가 없습니다. 

![TLS 끝까지](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

위의 이미지에서 앱 서비스를 백 엔드로 선택할 때 인증 인증서를 추가할 필요가 없습니다.

### <a name="health-probe"></a>상태 프로브

**HTTP 설정에서** 호스트 헤더를 재정의하는 것은 요청 및 라우팅에만 영향을 줍니다. 상태 프로브 동작에 영향을 미치지 않습니다. 종단 간 기능이 작동하려면 올바른 구성을 반영하도록 프로브와 HTTP 설정을 모두 수정해야 합니다. 사용자 지정 프로브는 프로브 구성에서 호스트 헤더를 지정하는 기능을 제공할 뿐만 아니라 현재 구성된 HTTP 설정에서 호스트 헤더를 파생하는 기능도 지원합니다. 이 구성은 프로브 구성의 `PickHostNameFromBackendHttpSettings` 매개 변수를 사용하여 지정할 수 있습니다.

### <a name="redirection-to-app-services-url-scenario"></a>앱 서비스의 URL 시나리오로 리디렉션

앱 서비스의 응답에 있는 호스트 이름이 최종 사용자 브라우저를 응용 프로그램 게이트웨이와 연결된 도메인 대신 *.azurewebsites.net hostname으로 향할 수 있는 시나리오가 있을 수 있습니다. 이 문제는 다음과 같은 경우에 발생할 수 있습니다.

- 앱 서비스에서 리디렉션을 구성했습니다. 리디렉션은 요청에 후행 슬래시를 추가하는 것만큼 간단할 수 있습니다.
- 리디렉션을 일으키는 Azure AD 인증이 있습니다.

이러한 경우를 해결하려면 [앱 서비스의 URL 문제로 리디렉션된 문제 해결을](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url)참조하세요.

## <a name="next-steps"></a>다음 단계

응용 프로그램 게이트웨이를 사용하여 앱 서비스 웹 앱 [구성을](https://docs.microsoft.com/azure/application-gateway/configure-web-app-portal) 방문하여 Azure App 서비스 웹 앱과 같은 다중 테넌트 앱을 백 엔드 풀 멤버로 설정하는 방법에 대해 알아봅니다.
