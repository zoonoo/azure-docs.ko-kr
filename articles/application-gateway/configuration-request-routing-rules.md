---
title: Azure 애플리케이션 게이트웨이 요청 라우팅 규칙 구성
description: 이 문서에서는 Azure 애플리케이션 게이트웨이 요청 라우팅 규칙을 구성 하는 방법을 설명 합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 20a665eefbb73f062f1f036e17b16da891a43eef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89653208"
---
# <a name="application-gateway-request-routing-rules"></a>Application Gateway 요청 라우팅 규칙

Azure Portal를 사용 하 여 응용 프로그램 게이트웨이를 만들 때 기본 규칙 (*rule1*)을 만듭니다. 이 규칙은 기본 수신기 (*appGatewayHttpListener*)를 기본 백 엔드 풀 (*appGatewayBackendPool*)과 기본 백 엔드 HTTP 설정 (*appGatewayBackendHttpSettings*)에 바인딩합니다. 게이트웨이를 만든 후에는 기본 규칙의 설정을 편집 하거나 새 규칙을 만들 수 있습니다.

## <a name="rule-type"></a>규칙 유형

규칙을 만들 때 [ *기본* 및 *경로 기반*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules)중에서 선택 합니다.

- 연결 된 수신기의 모든 요청 (예: * <i></i> contoso.com/ \* )* 을 단일 백 엔드 풀로 전달 하려면 기본을 선택 합니다.
- 특정 URL 경로에서 특정 백 엔드 풀로 요청을 라우팅하 려는 경우 경로 기반을 선택 합니다. 경로 패턴은 해당 쿼리 매개 변수가 아닌 URL 경로에만 적용 됩니다.

### <a name="order-of-processing-rules"></a>처리 규칙 순서

V1 및 v2 SKU의 경우 들어오는 요청의 패턴 일치는 경로가 경로 기반 규칙의 URL 경로 맵에 나열 된 순서 대로 처리 됩니다. 요청이 경로 맵에 있는 두 개 이상의 경로에 있는 패턴과 일치 하면 먼저 나열 된 경로가 일치 됩니다. 그리고 요청은 해당 경로와 연결 된 백 엔드에 전달 됩니다.

## <a name="associated-listener"></a>연결 된 수신기

수신기와 연결 된 *요청 라우팅 규칙* 을 평가 하 여 요청을 라우팅할 백 엔드 풀을 결정 하도록 수신기를 규칙에 연결 합니다.

## <a name="associated-back-end-pool"></a>연결 된 백 엔드 풀

수신기가 수신 하는 요청을 처리 하는 백 엔드 대상이 포함 된 백 엔드 풀을 규칙에 연결 합니다.

 - 기본 규칙의 경우 백 엔드 풀은 하나만 허용 됩니다. 연결 된 수신기에 대 한 모든 요청은 해당 백 엔드 풀로 전달 됩니다.

 - 경로 기반 규칙의 경우 각 URL 경로에 해당 하는 여러 백 엔드 풀을 추가 합니다. 입력 한 URL 경로와 일치 하는 요청은 해당 백 엔드 풀로 전달 됩니다. 또한 기본 백 엔드 풀을 추가 합니다. 규칙의 모든 URL 경로와 일치 하지 않는 요청은 해당 풀로 전달 됩니다.

## <a name="associated-back-end-http-setting"></a>연결 된 백 엔드 HTTP 설정

각 규칙에 대 한 백 엔드 HTTP 설정을 추가 합니다. 요청은이 설정에 지정 된 포트 번호, 프로토콜 및 기타 정보를 사용 하 여 응용 프로그램 게이트웨이에서 백 엔드 대상으로 라우팅됩니다.

기본 규칙의 경우 하나의 백 엔드 HTTP 설정만 허용 됩니다. 연결 된 수신기에 대 한 모든 요청은이 HTTP 설정을 사용 하 여 해당 백 엔드 대상으로 전달 됩니다.

경로 기반 규칙의 경우 각 URL 경로에 해당 하는 여러 백 엔드 HTTP 설정을 추가 합니다. 이 설정에서 URL 경로와 일치 하는 요청은 각 URL 경로에 해당 하는 HTTP 설정을 사용 하 여 해당 백 엔드 대상으로 전달 됩니다. 또한 기본 HTTP 설정을 추가 합니다. 이 규칙의 모든 URL 경로와 일치 하지 않는 요청은 기본 HTTP 설정을 사용 하 여 기본 백 엔드 풀로 전달 됩니다.

## <a name="redirection-setting"></a>리디렉션 설정

기본 규칙에 대해 리디렉션이 구성 된 경우 연결 된 수신기에 대 한 모든 요청이 대상으로 리디렉션됩니다. *전역* 리디렉션입니다. 경로 기반 규칙에 대해 리디렉션이 구성 된 경우 특정 사이트 영역의 요청만 리디렉션됩니다. 예를 들면 */cart/ \* *로 표시 되는 쇼핑 카트 영역이 있습니다. *경로 기반* 리디렉션입니다.

리디렉션에 대 한 자세한 내용은 [Application Gateway 리디렉션 개요](redirect-overview.md)를 참조 하세요.

### <a name="redirection-type"></a>리디렉션 유형

필요한 리디렉션 유형 ( *301)*, *임시 (307)*, *찾음 (302)* 또는 *기타 참조 (303)* 를 선택 합니다.

### <a name="redirection-target"></a>리디렉션 대상

다른 수신기 또는 외부 사이트를 리디렉션 대상으로 선택 합니다.

#### <a name="listener"></a>수신기

한 수신기에서 다른 수신기로 트래픽을 리디렉션하는 리디렉션 대상으로 수신기를 선택 합니다. 이 설정은 HTTP에서 HTTPS로의 리디렉션을 사용 하도록 설정 하려는 경우에 필요 합니다. 들어오는 HTTPS 요청을 확인 하는 대상 수신기에 들어오는 HTTP 요청을 확인 하는 소스 수신기에서 트래픽을 리디렉션합니다. 리디렉션 대상에 전달 되는 요청의 원래 요청에서 쿼리 문자열과 경로를 포함 하도록 선택할 수도 있습니다.

![Application Gateway 구성 요소 대화 상자](./media/configuration-overview/configure-redirection.png)

HTTP에서 HTTPS로의 리디렉션에 대 한 자세한 내용은 다음을 참조 하세요.
- [Azure Portal를 사용 하 여 HTTP에서 HTTPS로 리디렉션](redirect-http-to-https-portal.md)
- [PowerShell을 사용 하 여 HTTP에서 HTTPS로 리디렉션](redirect-http-to-https-powershell.md)
- [Azure CLI를 사용 하 여 HTTP에서 HTTPS로 리디렉션](redirect-http-to-https-cli.md)

#### <a name="external-site"></a>외부 사이트

이 규칙과 연결 된 수신기의 트래픽을 외부 사이트에 리디렉션하려면 외부 사이트를 선택 합니다. 리디렉션 대상에 전달 되는 요청의 원래 요청에서 쿼리 문자열을 포함 하도록 선택할 수 있습니다. 원래 요청에 있던 외부 사이트에 경로를 전달할 수 없습니다.

리디렉션에 대 한 자세한 내용은 다음을 참조 하세요.
- [PowerShell을 사용 하 여 외부 사이트로 트래픽 리디렉션](redirect-external-site-powershell.md)
- [CLI를 사용 하 여 외부 사이트로 트래픽 리디렉션](redirect-external-site-cli.md)

## <a name="rewrite-http-headers-and-url"></a>HTTP 헤더 및 URL 다시 쓰기

다시 쓰기 규칙을 사용 하 여 요청 및 응답 패킷이 응용 프로그램 게이트웨이를 통해 클라이언트와 백 엔드 풀 간에 이동할 때 URL 경로 및 쿼리 문자열 매개 변수 뿐만 아니라 HTTP 요청 및 응답 헤더를 추가, 제거 또는 업데이트할 수 있습니다.

헤더 및 URL 매개 변수는 정적 값 또는 다른 헤더 및 서버 변수로 설정할 수 있습니다. 클라이언트 IP 주소 추출, 백 엔드에 대 한 중요 한 정보 제거, 보안 추가 등의 중요 한 사용 사례에 도움이 됩니다.
자세한 내용은 다음을 참조하세요.

 - [HTTP 헤더 및 URL 다시 작성 개요](rewrite-http-headers-url.md)
 - [HTTP 헤더 재작성 구성](rewrite-http-headers-portal.md)
 - [URL 재작성 구성](rewrite-url-portal.md)

## <a name="next-steps"></a>다음 단계

- [HTTP 설정에 대해 알아보기](configuration-http-settings.md)