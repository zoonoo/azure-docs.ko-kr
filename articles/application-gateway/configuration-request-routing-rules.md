---
title: Azure Application Gateway 요청 라우팅 규칙 구성
description: 이 문서에서는 Azure Application Gateway 요청 라우팅 규칙을 구성하는 방법을 설명합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 047ce9b33836e2c23a37b1383942323d7c382485
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397538"
---
# <a name="application-gateway-request-routing-rules"></a>Application Gateway 요청 라우팅 규칙

Azure Portal을 사용하여 애플리케이션 게이트웨이를 만드는 경우 기본 규칙(*rule1*)을 만듭니다. 이 규칙은 기본 수신기(*appGatewayHttpListener*)를 기본 백 엔드 풀(*appGatewayBackendPool*) 및 기본 백 엔드 HTTP 설정(*appGatewayBackendHttpSettings*)에 바인딩합니다. 게이트웨이를 만든 후에는 기본 규칙의 설정을 편집하거나 새 규칙을 만들 수 있습니다.

## <a name="rule-type"></a>규칙 유형

규칙을 만들 때 [‘기본’과 ‘경로 기반’](./application-gateway-components.md#request-routing-rules) 중에서 선택합니다. 

- 연결된 수신기(예: *blog<i></i>.contoso.com/\** )의 모든 요청을 단일 백 엔드 풀로 전달하려면 기본을 선택합니다.
- 특정 URL 경로에서 특정 백 엔드 풀로 요청을 라우팅하려면 경로 기반을 선택합니다. 경로 패턴은 해당 쿼리 매개 변수가 아닌 URL 경로에만 적용됩니다.

### <a name="order-of-processing-rules"></a>처리 규칙 순서

v1 및 v2 SKU의 경우 들어오는 요청의 패턴 일치는 경로 기반 규칙의 URL 경로 맵에 경로가 나열된 순서대로 처리됩니다. 경로 맵에 있는 두 개 이상 경로의 패턴과 요청이 일치하는 경우에는 처음 나열된 경로가 일치됩니다. 요청은 해당 경로와 연결된 백 엔드에 전달됩니다.

## <a name="associated-listener"></a>연결된 수신기

수신기와 연결된 ‘요청 라우팅 규칙’을 평가하여 요청을 라우팅할 백 엔드 풀을 결정하도록 수신기를 규칙에 연결합니다.

## <a name="associated-back-end-pool"></a>연결된 백 엔드 풀

수신기가 받는 요청을 제공하는 백 엔드 대상이 포함된 백 엔드 풀을 규칙에 연결합니다.

 - 기본 규칙의 경우 하나의 백 엔드 풀만 허용됩니다. 연결된 수신기의 모든 요청은 해당 백 엔드 풀에 전달됩니다.

 - 경로 기반 규칙의 경우 각 URL 경로에 해당하는 여러 백 엔드 풀을 추가합니다. 입력된 URL 경로와 일치하는 요청은 해당 백 엔드 풀에 전달됩니다. 기본 백 엔드 풀도 추가합니다. 규칙의 URL 경로와 일치하지 않는 요청은 해당 풀에 전달됩니다.

## <a name="associated-back-end-http-setting"></a>연결된 백 엔드 HTTP 설정

각 규칙에 대한 백 엔드 HTTP 설정을 추가합니다. 요청은 이 설정에 지정된 포트 번호, 프로토콜, 기타 정보를 사용하여 애플리케이션 게이트웨이에서 백 엔드 대상으로 라우팅됩니다.

기본 규칙의 경우 하나의 백 엔드 HTTP 설정만 허용됩니다. 연결된 수신기의 모든 요청은 이 HTTP 설정을 사용하여 해당 백 엔드 대상에 전달됩니다.

경로 기반 규칙의 경우 각 URL 경로에 해당하는 여러 백 엔드 HTTP 설정을 추가합니다. 이 설정의 URL 경로와 일치하는 요청은 각 URL 경로에 해당하는 HTTP 설정을 사용하여 해당 백 엔드 대상에 전달됩니다. 기본 HTTP 설정도 추가합니다. 이 규칙의 URL 경로와 일치하지 않는 요청은 기본 HTTP 설정을 사용하여 기본 백 엔드 풀에 전달됩니다.

## <a name="redirection-setting"></a>리디렉션 설정

기본 규칙에 대해 리디렉션을 구성하면 연결된 수신기의 모든 요청이 대상으로 리디렉션됩니다. 이 경우 ‘전역’ 리디렉션입니다. 경로 기반 규칙에 대해 리디렉션을 구성하면 특정 사이트 영역의 요청만 리디렉션됩니다. 예를 들어 */cart/\** 로 표시되는 쇼핑 카트 영역이 있습니다. 이 경우 ‘경로 기반’ 리디렉션입니다.

리디렉션에 대한 자세한 내용은 [Application Gateway 리디렉션 개요](redirect-overview.md)를 참조하세요.

### <a name="redirection-type"></a>리디렉션 유형

필요한 리디렉션 유형(‘영구(301)’, ‘임시 (307)’, ‘찾음(302)’ 또는 ‘기타 보기(303)’)을 선택합니다.   

### <a name="redirection-target"></a>리디렉션 대상

다른 수신기 또는 외부 사이트를 리디렉션 대상으로 선택합니다.

#### <a name="listener"></a>수신기

한 수신기에서 다른 수신기로 트래픽을 리디렉션하려면 수신기를 리디렉션 대상으로 선택합니다. 이 설정은 HTTP 및 HTTPS 간 리디렉션을 사용하도록 설정하려는 경우에 필요합니다. 들어오는 HTTP 요청을 확인하는 원본 수신기에서 들어오는 HTTPS 요청을 확인하는 대상 수신기로 트래픽을 리디렉션합니다. 리디렉션 대상에 전달되는 요청에 원래 요청의 쿼리 문자열과 경로를 포함하도록 선택할 수도 있습니다.

![Application Gateway 구성 요소 대화 상자](./media/configuration-overview/configure-redirection.png)

HTTP 및 HTTPS 간 리디렉션에 대한 자세한 내용은 다음을 참조하세요.
- [Azure Portal을 사용한 HTTP 및 HTTPS 간 리디렉션](redirect-http-to-https-portal.md)
- [PowerShell을 사용한 HTTP 및 HTTPS 간 리디렉션](redirect-http-to-https-powershell.md)
- [Azure CLI를 사용한 HTTP 및 HTTPS 간 리디렉션](redirect-http-to-https-cli.md)

#### <a name="external-site"></a>외부 사이트

이 규칙과 연결된 수신기의 트래픽을 외부 사이트로 리디렉션하려는 경우 외부 사이트를 선택합니다. 리디렉션 대상에 전달되는 요청에 원래 요청의 쿼리 문자열을 포함하도록 선택할 수 있습니다. 원래 요청에 있던 외부 사이트 경로는 전달할 수 없습니다.

리디렉션에 대한 자세한 내용은 다음을 참조하세요.
- [PowerShell을 사용하여 외부 사이트로 트래픽 리디렉션](redirect-external-site-powershell.md)
- [CLI를 사용하여 외부 사이트로 트래픽 리디렉션](redirect-external-site-cli.md)

## <a name="rewrite-http-headers-and-url"></a>HTTP 헤더 및 URL 다시 쓰기

다시 쓰기 규칙을 사용하면 요청 및 응답 패킷이 애플리케이션 게이트웨이를 통해 클라이언트와 백 엔드 풀 간에 이동할 때 URL 경로 및 쿼리 문자열 매개 변수뿐만 아니라 HTTP 요청 및 응답 헤더도 추가하거나, 제거하거나, 업데이트할 수 있습니다.

헤더 및 URL 매개 변수는 정적 값이나 다른 헤더와 서버 변수로 설정할 수 있습니다. 이 기능은 클라이언트 IP 주소 추출, 백 엔드에 대한 중요한 정보 제거, 보안 추가 등의 중요한 사용 사례에 도움이 됩니다.
자세한 내용은 다음을 참조하세요.

 - [HTTP 헤더 및 URL 다시 쓰기 개요](rewrite-http-headers-url.md)
 - [HTTP 헤더 다시 쓰기 구성](rewrite-http-headers-portal.md)
 - [URL 다시 쓰기 구성](rewrite-url-portal.md)

## <a name="next-steps"></a>다음 단계

- [HTTP 설정에 대한 자세한 정보](configuration-http-settings.md)