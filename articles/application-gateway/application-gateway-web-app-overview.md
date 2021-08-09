---
title: 다중 테넌트 백 엔드
titleSuffix: Azure Application Gateway
description: 이 페이지에서는 다중 테넌트 백 엔드에 대한 Application Gateway 지원의 개요를 제공합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: fe6ea6f348d796962141bd39ff858d891a29a2f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397691"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>App Service와 같은 다중 테넌트 백 엔드에 대한 Application Gateway 지원

웹 서버의 다중 테넌트 아키텍처 설계에서는 여러 웹 사이트가 동일한 웹 서버 인스턴스에서 실행됩니다. 호스트 이름은 호스팅되는 여러 애플리케이션을 구별하는 데 사용됩니다. 기본적으로 애플리케이션 게이트웨이는 클라이언트에서 들어오는 HTTP 호스트 헤더를 변경하지 않으며 변경되지 않은 헤더를 백 엔드로 보냅니다. 이는 NIC, 가상 머신 확장 집합, 공용 IP, 내부 IP 주소 및 FQDN과 같은 백 엔드 풀 구성원에 대해 잘 작동합니다. 이러한 구성원은 올바른 엔드포인트를 확인하기 위해 특정 호스트 헤더 또는 SNI 확장에 의존하지 않기 때문입니다. 그러나 Azure App Service Web Apps 및 Azure API Management처럼 기본적으로 다중 테넌트이고 특정 호스트 헤더 또는 SNI 확장을 사용하여 올바른 엔드포인트를 확인하는 여러 서비스가 있습니다. 일반적으로 애플리케이션 게이트웨이와 연결된 DNS 이름인 애플리케이션의 DNS 이름은 백 엔드 서비스의 도메인 이름과 다릅니다. 따라서 애플리케이션 게이트웨이에서 수신한 원래 요청의 호스트 헤더는 백 엔드 서비스의 호스트 이름과 동일하지 않습니다. 이 때문에 애플리케이션 게이트웨이에서 백 엔드로의 요청에 있는 호스트 헤더가 백 엔드 서비스의 호스트 이름으로 변경되지 않는 한 다중 테넌트 백 엔드는 올바른 엔드포인트에 대한 요청을 확인할 수 없습니다. 

Application Gateway는 사용자가 백 엔드의 호스트 이름에 따라 요청의 HTTP 호스트 헤더를 재정의할 수 있는 기능을 제공합니다. 이 기능을 사용하면 Azure 앱 서비스 웹앱 및 API 관리 같은 다중 테넌트 백 엔드를 지원할 수 있습니다. 이 기능은 v1/v2 표준 및 WAF SKU에 모두 사용할 수 있습니다. 

![호스트 재정의](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> Azure ASE(App Service Environment)는 멀티 테넌트 리소스인 Azure App Service와 달리 전용 리소스이므로 이는 Azure ASE에 적용되지 않습니다.

## <a name="override-host-header-in-the-request"></a>요청에서 호스트 헤더 재정의

호스트 재정의를 지정하는 기능은 [HTTP 설정](./configuration-overview.md#http-settings)에서 정의되며 규칙을 만드는 동안 모든 백 엔드 풀에 적용할 수 있습니다. 다중 테넌트 백 엔드에 대한 호스트 헤더 및 SNI 확장을 재정의하는 다음 두 가지 방법이 지원됩니다.

- HTTP 설정에 명시적으로 입력된 고정 값으로 호스트 이름을 설정하는 기능. 이 기능을 사용하면 특정 HTTP 설정이 적용되는 백 엔드 풀의 모든 트래픽에서 호스트 헤더가 이 값으로 재정의됩니다. 종단 간 TLS를 사용하면 이 재정의된 호스트 이름이 SNI 확장에 사용됩니다. 이 기능은 백 엔드 풀 팜이 들어오는 고객 호스트 헤더와 다른 호스트 헤더를 예상하는 시나리오를 지원할 수 있습니다.

- 백 엔드 풀 멤버의 IP 또는 FQDN에서 호스트 이름을 파생하는 기능. HTTP 설정은 개별 백 엔드 풀 구성원에서 호스트 이름을 파생시키는 옵션으로 구성된 경우 백 엔드 풀 구성원의 FQDN에서 호스트 이름을 동적으로 선택하는 옵션도 제공합니다. 종단 간 TLS를 사용하면 이 호스트 이름이 FQDN에서 파생되며 SNI 확장에 사용됩니다. 이 기능을 사용하면 백 엔드 풀이 Azure 웹앱 같은 다중 테넌트 PaaS 서비스를 두 개 이상 가질 수 있고 각 멤버에 대한 요청의 호스트 헤더에 FQDN에서 파생된 호스트 이름이 포함되는 시나리오를 지원할 수 있습니다. 이 시나리오를 구현하기 위해 HTTP 설정에서 [백 엔드 주소에서 호스트 이름 선택](./configuration-http-settings.md#pick-host-name-from-back-end-address)이라는 스위치를 사용합니다. 이 스위치는 원래 요청의 호스트 헤더를 백 엔드 풀에서 언급된 헤더로 동적으로 재정의합니다.  예를 들어 백 엔드 풀 FQDN에 "contoso11.azurewebsites.net" 및 "contoso22.azurewebsites.net"이 포함된 경우 요청이 적절한 백 엔드 서버에 전송되면 원래 요청의 호스트 헤더인 contoso.com은 contoso11.azurewebsites.net 또는 contoso22.azurewebsites.net으로 재정의됩니다. 

  ![웹앱 시나리오](./media/application-gateway-web-app-overview/scenario.png)

이 기능을 사용하여 고객이 HTTP 설정 및 사용자 지정 프로브의 옵션을 적절한 구성으로 지정합니다. 그러면 규칙을 사용하여 이 설정이 수신기와 백 엔드 풀에 연결됩니다.

## <a name="special-considerations"></a>특별 고려 사항

### <a name="tls-termination-and-end-to-end-tls-with-multi-tenant-services"></a>다중 테넌트 서비스를 지원하는 TLS 종료 및 엔드투엔드 TLS

TLS 종료 및 엔드투엔드 TLS 암호화는 모두 다중 테넌트 서비스에서 지원됩니다. 애플리케이션 게이트웨이에서 TLS를 종료하는 경우 TLS 인증서를 애플리케이션 게이트웨이 수신기에 계속 추가해야 합니다. 그러나 엔드투엔드 TLS의 경우 Azure App Service Web Apps와 같은 신뢰할 수 있는 Azure 서비스는 애플리케이션 게이트웨이에서 백 엔드를 허용할 필요가 없습니다. 따라서 인증 인증서를 추가할 필요가 없습니다. 

![엔드투엔드 TLS](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

위 이미지에서 App Service가 백 엔드로 선택되었을 때 인증 인증서를 추가할 필요가 없다는 사실에 유의합니다.

### <a name="health-probe"></a>상태 프로브

**HTTP 설정** 에서 호스트 헤더를 재정의하면 요청과 해당 라우팅에만 영향을 줍니다. 상태 프로브 동작에는 영향을 미치지 않습니다. 종단 간 기능이 작동하려면 올바른 구성을 반영하도록 프로브와 HTTP 설정을 모두 수정해야 합니다. 사용자 지정 프로브는 프로브 구성에서 호스트 헤더를 지정하는 기능을 제공하는 것 외에 현재 구성된 HTTP 설정에서 호스트 헤더를 파생시키는 기능도 지원합니다. 이 구성은 프로브 구성의 `PickHostNameFromBackendHttpSettings` 매개 변수를 사용하여 지정할 수 있습니다.

### <a name="redirection-to-app-services-url-scenario"></a>App Service의 URL로 리디렉션 시나리오

App Service의 응답에 있는 호스트 이름이 최종 사용자 브라우저를 Application Gateway와 연결된 도메인 대신 *.azurewebsites.net 호스트 이름으로 연결하는 시나리오가 있을 수 있습니다. 이 문제는 다음과 같은 경우에 발생할 수 있습니다.

- App Service에 리디렉션이 구성되어 있습니다. 리디렉션은 요청에 후행 슬래시를 추가하는 것처럼 간단할 수 있습니다.
- 리디렉션을 유발하는 Azure AD 인증이 있습니다.

이러한 경우를 해결하려면 [App Services URL로의 리디렉션 문제 해결](./troubleshoot-app-service-redirection-app-service-url.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[Application Gateway로 App Service Web App 구성](./configure-web-app-portal.md)을 방문하여 Azure App Service Web App과 같은 다중 테넌트 앱을 백 엔드 풀 멤버로 사용하여 Application Gateway를 설정하는 방법을 알아봅니다.
