---
title: Azure Application Gateway 구성 개요
description: 이 문서에서는 Azure Application Gateway의 구성 요소를 구성하는 방법을 설명합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 0012ac99600c77dce5940387e1da54f29c3f6ab7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89652011"
---
# <a name="application-gateway-configuration-overview"></a>Application Gateway 구성 개요

Azure Application Gateway는 시나리오에 맞게 다양한 방법으로 구성할 수 있는 여러 구성 요소로 이루어져 있습니다. 이 문서에서는 각 구성 요소를 구성하는 방법을 보여 줍니다.

![Application Gateway 구성 요소 순서도](./media/configuration-overview/configuration-overview1.png)

이 이미지는 세 개의 수신기가 있는 애플리케이션을 보여 줍니다. 처음 두 개는 각각 `http://acme.com/*` 및 `http://fabrikam.com/*`의 다중 사이트 수신기입니다. 둘 다 포트 80에서 수신 대기합니다. 세 번째는 이전에 SSL(Secure Sockets Layer) 종료로 알려진 엔드투엔드 TLS(전송 계층 보안) 종료가 있는 기본 수신기입니다.

## <a name="infrastructure"></a>인프라

Application Gateway 인프라에는 가상 네트워크, 서브넷, 네트워크 보안 그룹, 사용자 정의 경로가 포함됩니다.

자세한 내용은 [Application Gateway 인프라 구성](configuration-infrastructure.md)을 참조하세요.



## <a name="front-end-ip-address"></a>프런트 엔드 IP 주소

공용 IP 주소, 개인 IP 주소 또는 둘 다를 사용하도록 애플리케이션 게이트웨이를 구성할 수 있습니다. 클라이언트가 인터넷 연결 VIP(가상 IP)로 인터넷을 통해 액세스해야 하는 백 엔드를 호스트하는 경우 공용 IP가 필요합니다.

자세한 내용은 [Application Gateway 프런트 엔드 IP 주소 구성](configuration-front-end-ip.md)을 참조하세요.

## <a name="listeners"></a>수신기

수신기는 포트, 프로토콜, 호스트, IP 주소를 사용하여 들어오는 연결 요청을 확인하는 논리적 엔터티입니다. 수신기를 구성하는 경우 게이트웨이에 들어오는 요청의 해당 값과 일치하는 설정 값을 입력해야 합니다.

자세한 내용은 [Application Gateway 수신기 구성](configuration-listeners.md)을 참조하세요.

## <a name="request-routing-rules"></a>라우팅 규칙 요청

Azure Portal를 사용하여 애플리케이션 게이트웨이를 만드는 경우 기본 규칙(*rule1*)을 만듭니다. 이 규칙은 기본 수신기(*appGatewayHttpListener*)를 기본 백 엔드 풀(*appGatewayBackendPool*) 및 기본 백 엔드 HTTP 설정(*appGatewayBackendHttpSettings*)에 바인딩합니다. 게이트웨이를 만든 후에는 기본 규칙의 설정을 편집하거나 새 규칙을 만들 수 있습니다.

자세한 내용은 [Application Gateway 요청 라우팅 규칙](configuration-request-routing-rules.md)을 참조하세요.

## <a name="http-settings"></a>HTTP 설정

애플리케이션 게이트웨이는 여기에서 지정한 구성을 사용하여 트래픽을 백 엔드 서버로 라우팅합니다. HTTP 설정을 만든 후에는 하나 이상의 요청 라우팅 규칙과 연결해야 합니다.

자세한 내용은 [Application Gateway HTTP 설정 구성](configuration-http-settings.md)을 참조하세요.

## <a name="back-end-pool"></a>백 엔드 풀

백 엔드 풀에서 특정 가상 머신, 가상 머신 확장 집합, IP 주소/FQDN 또는 App Service라는 네 가지 유형의 백 엔드 멤버를 가리킬 수 있습니다. 

백 엔드 풀을 만든 후에는 하나 이상의 요청 라우팅 규칙과 연결해야 합니다. 또한 애플리케이션 게이트웨이의 각 백 엔드 풀에 대해 상태 프로브를 구성해야 합니다. 요청 라우팅 규칙 조건이 충족되면 애플리케이션 게이트웨이는 해당 백 엔드 풀의 정상 서버(상태 프로브를 통해 확인됨)에 트래픽을 전달합니다.

## <a name="health-probes"></a>상태 프로브

애플리케이션 게이트웨이는 기본적으로 백 엔드에 있는 모든 리소스의 상태를 모니터링합니다. 하지만 상태 모니터링을 더 효율적으로 제어하려면 각 백 엔드 HTTP 설정에 대한 사용자 지정 프로브를 만드는 것이 좋습니다. 사용자 지정 프로브를 구성하는 방법을 알아보려면 [사용자 지정 상태 프로브 설정](application-gateway-probe-overview.md#custom-health-probe-settings)을 참조하세요.

> [!NOTE]
> 사용자 지정 상태 프로브를 만든 후에는 백 엔드 HTTP 설정에 연결해야 합니다. 규칙을 사용하여 해당 HTTP 설정이 수신기와 명시적으로 연결되지 않은 경우 사용자 지정 프로브는 백 엔드 풀의 상태를 모니터링하지 않습니다.

## <a name="next-steps"></a>다음 단계

Application Gateway 구성 요소에 대해 배웠으므로 이제 다음을 수행할 수 있습니다.

- [Azure Portal에서 애플리케이션 게이트웨이 만들기](quick-create-portal.md)
- [PowerShell을 사용하여 애플리케이션 게이트웨이 만들기](quick-create-powershell.md)
- [Azure CLI를 사용하여 애플리케이션 게이트웨이 만들기](quick-create-cli.md)
