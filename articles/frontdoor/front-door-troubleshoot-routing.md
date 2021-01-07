---
title: Azure Front 도어 구성 문제 해결
description: 이 자습서에서는 Azure Front 도어 인스턴스에 직면 했을 수 있는 몇 가지 일반적인 문제를 자체적으로 해결 하는 방법에 대해 알아봅니다.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 15cdcefe628a392704e650b560243e2f6a134ec2
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629991"
---
# <a name="troubleshooting-common-routing-problems"></a>일반적인 라우팅 문제 해결

이 문서에서는 Azure Front 도어 구성에 직면 하는 일반적인 라우팅 문제를 해결 하는 방법을 설명 합니다.

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>몇 초 후에 Azure Front 도어에서 응답 503

### <a name="symptom"></a>증상

* Azure 전면 도어를 거치지 않고 백 엔드로 전송 되는 일반 요청이 성공 합니다. Azure 전면 도어를 통해 503 오류 응답이 발생 합니다.
* Azure 전면 도어의 오류는 일반적으로 약 30 초 후에 표시 됩니다.

### <a name="cause"></a>원인

이 문제의 원인은 다음 두 가지 중 하나일 수 있습니다.
 
* 백 엔드가 Azure Front 도어에서 요청을 받기 위해 구성 된 시간 제한 (기본값은 30 초) 보다 오래 걸리고 있습니다.
* Azure 전면 도어에서 요청에 대 한 응답을 보내는 데 걸리는 시간이 제한 시간 값 보다 오래 걸리고 있습니다. 

### <a name="troubleshooting-steps"></a>문제 해결 단계

* Azure 전면 도어를 거치지 않고 백 엔드에 직접 요청을 보냅니다. 백 엔드가 일반적으로 응답 하는 데 걸리는 시간을 확인 합니다.
* Azure Front 도어를 통해 요청을 보내고 503 응답을 받고 있는지 확인 합니다. 그렇지 않은 경우에는 문제가 시간 제한 문제가 아닐 수 있습니다. 지원에 문의
* Azure 전면 도어를 통해 503 오류 응답 코드를 생성 하는 경우 `sendReceiveTimeout` Azure Front 도어에 대해 필드를 구성 합니다. 기본 시간 제한을 최대 4 분 (240 초)으로 확장할 수 있습니다. 설정이 아래에 `backendPoolSettings` 있고가 호출 됩니다 `sendRecvTimeoutSeconds` . 

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>사용자 지정 도메인에 전송 된 요청은 400 상태 코드를 반환 합니다.

### <a name="symptom"></a>증상

* Azure Front 도어 인스턴스를 만들었지만 도메인 또는 프런트 엔드 호스트에 대 한 요청이 HTTP 400 상태 코드를 반환 합니다.
* 구성한 프런트 엔드 호스트에 대 한 사용자 지정 도메인에 대 한 DNS 매핑을 만들었습니다. 그러나 사용자 지정 도메인 호스트 이름에 요청을 보내면 HTTP 400 상태 코드가 반환 됩니다. 구성 된 백 엔드로 라우팅하는 것은 아닙니다.

### <a name="cause"></a>원인

프런트 엔드 호스트로 추가 된 사용자 지정 도메인에 대 한 라우팅 규칙을 구성 하지 않은 경우이 문제가 발생 합니다. 해당 프런트 엔드 호스트에 대해 라우팅 규칙을 명시적으로 추가 해야 합니다. Azure Front 도어 하위 도메인 (*. azurefd.net)의 프런트 엔드 호스트에 대해 라우팅 규칙이 이미 구성 된 경우에도 마찬가지입니다.

### <a name="troubleshooting-steps"></a>문제 해결 단계

선택한 백 엔드 풀로 트래픽을 보낼 사용자 지정 도메인에 대 한 라우팅 규칙을 추가 합니다.

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>Azure Front 도어가 HTTP를 HTTPS로 리디렉션하지 않음

### <a name="symptom"></a>증상

Azure 전면 도어에는 HTTP를 HTTPS로 리디렉션하는 라우팅 규칙이 있지만 도메인에 액세스 하는 것은 HTTP를 프로토콜로 계속 유지 합니다.

### <a name="cause"></a>원인

이 동작은 Azure Front 문에 대해 라우팅 규칙을 올바르게 구성 하지 않은 경우에 발생할 수 있습니다. 기본적으로 현재 구성은 구체적이 아니며 충돌 하는 규칙이 있을 수 있습니다.

### <a name="troubleshooting-steps"></a>문제 해결 단계

## <a name="request-to-a-frontend-host-name-returns-a-404-status-code"></a>프런트 엔드 호스트 이름에 대 한 요청은 404 상태 코드를 반환 합니다.

### <a name="symptom"></a>증상

프런트 엔드 호스트, 백 엔드를 하나 이상 포함 하는 백 엔드 풀 및 프런트 엔드 호스트를 백 엔드 풀에 연결 하는 라우팅 규칙을 구성 하 여 Azure Front 도어 인스턴스를 만들었습니다. 구성 된 프런트 엔드 호스트에 요청을 할 때 콘텐츠를 사용할 수 없습니다. 따라서 요청은 HTTP 404 상태 코드를 반환 합니다.

### <a name="cause"></a>원인

이 증상의 잠재적 원인에는 몇 가지가 있습니다.

* 백 엔드는 공용 백 엔드가 아니며 Azure Front 문에 표시 되지 않습니다.
* 백 엔드가 잘못 구성 되어 Azure Front 도어가 잘못 된 요청을 보냅니다. 즉, 백 엔드는 HTTP만 수락 하 고 HTTPS는 허용 하지 않습니다. 따라서 Azure Front 도어가 HTTPS 요청을 전달 하려고 합니다.
* 백 엔드가 백 엔드에 대한 요청으로 전달된 호스트 헤더를 거부하고 있습니다.
* 백 엔드에 대 한 구성이 아직 완전히 배포 되지 않았습니다.

### <a name="troubleshooting-steps"></a>문제 해결 단계

* 배포 시간을 확인 합니다.
   * 구성이 배포 될 때까지 10 분 이상 기다린 후

* 백 엔드 설정을 확인 합니다.
    * 요청을 라우팅하는 백 엔드 풀로 이동 합니다. 이는 라우팅 규칙을 구성 하는 방법에 따라 달라 집니다. 백 엔드 호스트 유형 및 백 엔드 호스트 이름이 올바른지 확인 합니다. 백 엔드가 사용자 지정 호스트인 경우 철자가 정확한 지 확인 합니다. 

    * HTTP 및 HTTPS 포트를 확인합니다. 대부분의 경우 80 및 443 (각각)은 올바르지만 변경할 필요가 없습니다. 그러나 이러한 방식으로 백 엔드가 구성 되지 않아 다른 포트에서 수신 대기 하는 경우가 있습니다.

    * 프런트 엔드 호스트가 라우팅하는 백 엔드에 대해 구성 된 백 엔드 호스트 헤더를 확인 합니다. 대부분의 경우이 헤더는 백 엔드 호스트 이름과 동일 해야 합니다. 그러나 백 엔드에서 기대한 값과 다를 경우 다양한 HTTP 4xx 상태 코드가 반환될 수 있습니다. 백 엔드의 IP 주소를 입력 하는 경우 백 엔드 호스트 이름에 백 엔드 호스트 헤더를 설정 해야 할 수 있습니다.

* 라우팅 규칙 설정을 확인 합니다.
    * 문제의 프런트 엔드 호스트 이름에서 백 엔드 풀로 라우팅하는 라우팅 규칙으로 이동 합니다. 요청이 전달 될 때 허용 된 프로토콜이 올바르게 구성 되어 있는지 확인 합니다. 허용 되는 **프로토콜** 필드는 Azure Front 도어가 수락 해야 하는 요청을 결정 합니다. 전달 프로토콜은 Azure Front 문이 요청을 백 엔드에 전달 하는 데 사용 해야 하는 프로토콜을 결정 합니다.
      
      예를 들어 백 엔드가 HTTP 요청만 허용 하는 경우 다음 구성이 유효 합니다.
            
      * 허용되는 프로토콜은 HTTP 및 HTTPS입니다. 전달 프로토콜은 HTTP입니다. HTTPS가 허용 되는 프로토콜 이기 때문에 일치 요청이 작동 하지 않습니다. 요청이 HTTPS로 제공 되는 경우 Azure Front 도어가 HTTPS를 사용 하 여 전달 하려고 시도 합니다.

      * 허용 되는 프로토콜은 HTTP입니다. 전달 프로토콜은 일치 요청 또는 HTTP입니다.
    - **Url 재작성** 은 기본적으로 사용 되지 않습니다. 이 필드는 사용 가능 하도록 설정 하려는 백 엔드 호스트 리소스의 범위를 좁히는 경우에만 사용 됩니다. 이 필드를 사용 하지 않도록 설정 하면 Azure Front 도어가 수신 하는 것과 동일한 요청 경로를 전달 합니다. 이 필드를 잘못 구성할 수 있습니다. 따라서 Azure Front 도어가 사용 가능 하지 않은 백 엔드에서 리소스를 요청 하는 경우 HTTP 404 상태 코드를 반환 합니다.

## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>프런트 엔드 호스트 이름에 대 한 요청은 411 상태 코드를 반환 합니다.

### <a name="symptom"></a>증상

Azure Front 도어 인스턴스를 만들고 프런트 엔드 호스트, 백 엔드가 하나 이상 포함 된 백 엔드 풀 및 프런트 엔드 호스트를 백 엔드 풀에 연결 하는 라우팅 규칙을 구성 했습니다. HTTP 411 상태 코드가 반환 되기 때문에 구성 된 프런트 엔드 호스트로 요청이 이동할 때 콘텐츠를 사용할 수 없는 것 같습니다.

이러한 요청에 대 한 응답에는 설명 문을 포함 하는 HTML 오류 페이지가 응답 본문에 포함 될 수도 있습니다. 예: `HTTP Error 411. The request must be chunked or have a content length`.

### <a name="cause"></a>원인

이 증상의 원인에는 여러 가지가 있을 수 있습니다. 전반적인 이유는 HTTP 요청이 완전히 RFC 규격이 아닌 것입니다. 

비준수의 예는를 사용 하는 등의 방법으로 `POST` 또는 헤더 없이 전송 되는 요청입니다 `Content-Length` `Transfer-Encoding` `curl -X POST https://example-front-door.domain.com` . 이 요청은 [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2)에 설정 된 요구 사항을 충족 하지 않습니다. Azure 전면 도어는 HTTP 411 응답을 사용 하 여 차단 합니다.

이 동작은 Azure Front 도어의 WAF (웹 응용 프로그램 방화벽) 기능과는 별개입니다. 현재이 동작을 사용 하지 않도록 설정할 수 있는 방법은 없습니다. WAF 기능이 사용 되지 않는 경우에도 모든 HTTP 요청은 요구 사항을 충족 해야 합니다.

### <a name="troubleshooting-steps"></a>문제 해결 단계

- 요청이 필요한 Rfc에 설정 된 요구 사항을 준수 하는지 확인 합니다.

- 요청에 대 한 응답으로 반환 되는 HTML 메시지 본문을 기록해 둡니다. 메시지 본문은 요청이 비규격 인 경우를 정확 하 게 설명 *하* 는 경우가 많습니다.
