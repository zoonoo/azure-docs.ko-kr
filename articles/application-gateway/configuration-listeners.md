---
title: Azure 애플리케이션 게이트웨이 수신기 구성
description: 이 문서에서는 Azure 애플리케이션 게이트웨이 수신기를 구성 하는 방법을 설명 합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: ef2ff8924cd8a92c5d2d2e5dd9da6bb74fad1a14
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89653222"
---
# <a name="application-gateway-listener-configuration"></a>Application Gateway 수신기 구성

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

수신기는 포트, 프로토콜, 호스트 및 IP 주소를 사용 하 여 들어오는 연결 요청을 확인 하는 논리적 엔터티입니다. 수신기를 구성할 때 게이트웨이의 들어오는 요청에서 해당 값과 일치 하는 값을 입력 해야 합니다.

Azure Portal를 사용 하 여 응용 프로그램 게이트웨이를 만드는 경우 수신기의 프로토콜 및 포트를 선택 하 여 기본 수신기도 만듭니다. 수신기에서 HTTP2 지원을 사용할지 여부를 선택할 수 있습니다. 응용 프로그램 게이트웨이를 만든 후에는 해당 기본 수신기 (*appGatewayHttpListener*)의 설정을 편집 하거나 새 수신기를 만들 수 있습니다.

## <a name="listener-type"></a>수신기 형식

새 수신기를 만들 때 [ *기본* *사이트와 다중 사이트*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners)중에서 선택할 수 있습니다.

- 모든 도메인에 대 한 모든 요청을 수락 하 여 백 엔드 풀에 전달 하려는 경우 기본을 선택 합니다. [기본 수신기를 사용 하 여 응용 프로그램 게이트웨이를 만드는 방법을](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)알아봅니다.

- *호스트* 헤더 또는 호스트 이름에 따라 여러 백 엔드 풀에 요청을 전달 하려는 경우 다중 사이트 수신기를 선택 합니다. 여기에서 들어오는 요청과 일치 하는 호스트 이름도 지정 해야 합니다. Application Gateway HTTP 1.1 호스트 헤더를 사용 하 여 동일한 공용 IP 주소 및 포트에서 둘 이상의 웹 사이트를 호스트 하기 때문입니다. 자세히 알아보려면 [Application Gateway를 사용 하 여 여러 사이트 호스팅](multiple-site-overview.md)을 참조 하세요.

### <a name="order-of-processing-listeners"></a>처리 수신기 순서

V1 SKU의 경우 요청은 규칙의 순서와 수신기의 형식에 따라 일치 합니다. 기본 수신기를 사용 하는 규칙이 먼저 순서 대로 제공 되 면 먼저 처리 되 고 해당 포트 및 IP 조합에 대 한 모든 요청을 허용 합니다. 이를 방지 하려면 먼저 다중 사이트 수신기를 사용 하 여 규칙을 구성 하 고 기본 수신기를 사용 하 여 규칙을 목록에서 마지막으로 푸시합니다.

V2 SKU의 경우 다중 사이트 수신기가 기본 수신기 보다 먼저 처리 됩니다.

## <a name="front-end-ip-address"></a>프런트 엔드 IP 주소

이 수신기와 연결 하려는 프런트 엔드 IP 주소를 선택 합니다. 수신기가이 IP에서 들어오는 요청을 수신 대기 합니다.

## <a name="front-end-port"></a>프런트 엔드 포트

프런트 엔드 포트를 선택 합니다. 기존 포트를 선택 하거나 새로 만듭니다. [허용 되는 포트 범위](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports)에서 값을 선택 합니다. 잘 알려진 포트 (예: 80 및 443) 뿐만 아니라 적절 한 모든 사용자 지정 포트를 사용할 수 있습니다. 포트는 공용 수신기 또는 개인 연결 수신기에 사용할 수 있습니다.

## <a name="protocol"></a>프로토콜

HTTP 또는 HTTPS를 선택 합니다.

- HTTP를 선택 하면 클라이언트와 응용 프로그램 게이트웨이 간의 트래픽이 암호화 되지 않습니다.

- [Tls 종료](features.md#secure-sockets-layer-ssltls-termination) 또는 [종단 간 tls 암호화](https://docs.microsoft.com/azure/application-gateway/ssl-overview)를 원하는 경우 HTTPS를 선택 합니다. 클라이언트와 응용 프로그램 게이트웨이 간의 트래픽이 암호화 됩니다. 그리고 TLS 연결은 application gateway에서 종료 됩니다. 종단 간 TLS 암호화를 원하는 경우 HTTPS를 선택 하 고 **백 엔드 HTTP** 설정을 구성 해야 합니다. 이렇게 하면 응용 프로그램이 응용 프로그램 게이트웨이에서 백 엔드에서 이동할 때 트래픽이 다시 암호화 됩니다.


TLS 종단 및 종단 간 TLS 암호화를 구성 하려면 응용 프로그램 게이트웨이에서 대칭 키를 파생할 수 있도록 수신기에 인증서를 추가 해야 합니다. 이는 TLS 프로토콜 사양에 따라 결정 됩니다. 대칭 키는 게이트웨이로 전송 되는 트래픽을 암호화 하 고 암호 해독 하는 데 사용 됩니다. 게이트웨이 인증서는 PFX (개인 정보 교환) 형식 이어야 합니다. 이 형식을 사용 하면 게이트웨이에서 트래픽을 암호화 하 고 암호 해독 하는 데 사용 하는 개인 키를 내보낼 수 있습니다.

## <a name="supported-certificates"></a>지원 되는 인증서

[Application Gateway를 사용 하는 tls 종료 및 종단 간 Tls 개요](ssl-overview.md#certificates-supported-for-tls-termination) 를 참조 하세요.

## <a name="additional-protocol-support"></a>추가 프로토콜 지원

### <a name="http2-support"></a>HTTP2 지원

HTTP/2 프로토콜 지원은 응용 프로그램 게이트웨이 수신기에만 연결 되는 클라이언트에서 사용할 수 있습니다. 백 엔드 서버 풀에 대 한 통신은 HTTP/1.1을 통해 전달 됩니다. 기본적으로 HTTP/2 지원은 사용할 수 없습니다. 다음 Azure PowerShell 코드 조각에서는이를 사용 하도록 설정 하는 방법을 보여 줍니다.

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

### <a name="websocket-support"></a>WebSocket 지원

WebSocket 지원은 기본적으로 사용 하도록 설정 되어 있습니다. 사용 하거나 사용 하지 않도록 설정할 수 있는 사용자 구성 설정이 없습니다. Websocket은 HTTP 및 HTTPS 수신기 모두와 함께 사용할 수 있습니다.

## <a name="custom-error-pages"></a>사용자 지정 오류 페이지

전역 수준 또는 수신기 수준에서 사용자 지정 오류를 정의할 수 있습니다. 그러나 Azure Portal에서 전역 수준 사용자 지정 오류 페이지를 만드는 것은 현재 지원 되지 않습니다. 수신기 수준에서 403 웹 응용 프로그램 방화벽 오류 또는 502 유지 관리 페이지에 대 한 사용자 지정 오류 페이지를 구성할 수 있습니다. 또한 지정 된 오류 상태 코드에 대해 공개적으로 액세스할 수 있는 blob URL을 지정 해야 합니다. 자세한 내용은 [Application Gateway 사용자 지정 오류 페이지 만들기](https://docs.microsoft.com/azure/application-gateway/custom-error)를 참조하세요.

![Application Gateway 오류 코드](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

전역 사용자 지정 오류 페이지를 구성 하려면 [Azure PowerShell 구성](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration)을 참조 하세요.

## <a name="tls-policy"></a>TLS 정책

TLS/SSL 인증서 관리를 중앙 집중화 하 고 백 엔드 서버 팜에 대 한 암호화 해독 오버 헤드를 줄일 수 있습니다. 중앙 집중식 TLS 처리를 사용 하 여 보안 요구 사항에 적합 한 중앙 TLS 정책을 지정할 수도 있습니다. *기본*, *미리 정의*됨 또는 *사용자 지정* TLS 정책을 선택할 수 있습니다.

Tls 프로토콜 버전을 제어 하도록 TLS 정책을 구성 합니다. Tls 1.0, TLS 1.1 및 TLS 1.2의 TLS 핸드셰이크에 최소 프로토콜 버전을 사용 하도록 응용 프로그램 게이트웨이를 구성할 수 있습니다. 기본적으로 SSL 2.0 및 3.0은 사용 하지 않도록 설정 되며 구성할 수 없습니다. 자세한 내용은 [APPLICATION GATEWAY TLS 정책 개요](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview)를 참조 하세요.

수신기를 만든 후에는 요청 라우팅 규칙과 연결 합니다. 이 규칙은 수신기에서 수신 된 요청을 백 엔드로 라우팅하는 방법을 결정 합니다.

## <a name="next-steps"></a>다음 단계

- [요청 라우팅 규칙에 대해 알아봅니다](configuration-request-routing-rules.md).