---
title: Azure Front 도어 표준/프리미엄 (미리 보기) 로깅
description: 이 문서에서는 Azure Front 도어 Standard/Premium에서 로깅이 작동 하는 방식에 대해 설명 합니다.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 03/15/2021
ms.author: duau
ms.openlocfilehash: 531f4a9c9f535779e451ca316a8a5867f6cdaba5
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103573900"
---
# <a name="azure-front-door-standardpremium-preview-logging"></a>Azure Front 도어 표준/프리미엄 (미리 보기) 로깅

> [!Note]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

Azure Front 도어는 프런트 도어를 추적, 모니터링 및 디버그 하는 데 도움이 되는 다양 한 로깅을 제공 합니다. 

* 액세스 로그에는 AFD에서 수신 하 고 액세스 패턴을 분석 및 모니터링 하 고 문제를 디버그 하는 데 도움이 되는 모든 요청에 대 한 자세한 정보가 있습니다. 
* 활동 로그는 Azure 리소스에서 수행된 작업에 대한 가시성을 제공합니다.  
* 상태 프로브 로그는 원본에 대해 실패 한 모든 프로브에 대 한 로그를 제공 합니다. 
* WAF (웹 응용 프로그램 방화벽) 로그는 Azure Front 도어 끝점의 검색 또는 방지 모드를 통해 기록 되는 요청에 대 한 자세한 정보를 제공 합니다. WAF를 사용 하 여 구성 된 사용자 지정 도메인은 이러한 로그를 통해 볼 수도 있습니다.

> [!IMPORTANT]
> Azure 전면 도어 표준/프리미엄 (미리 보기)은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

액세스 로그, 상태 프로브 로그 및 WAF 로그는 기본적으로 사용 하도록 설정 되어 있지 않습니다. 다음 단계를 사용 하 여 로깅을 사용 하도록 설정 합니다. 활동 로그 항목은 기본적으로 수집되고 Azure Portal에서 볼 수 있습니다. 로그가 최대 몇 분까지 지연 될 수 있습니다. 

로그 저장에는 세 가지 옵션이 있습니다. 

* **저장소 계정:** 저장소 계정은 로그가 장기간 저장 되 고 필요한 경우 검토 되는 시나리오에 가장 적합 합니다. 
* **Event hubs:** Event hubs는 다른 SIEM (보안 정보 및 이벤트 관리) 도구나 외부 데이터 저장소와 통합 하는 데 유용한 옵션입니다. 예: Splunk/DataDog/Sumo. 
* **Azure Log Analytics:** Azure Monitor의 azure Log Analytics는 Azure Front 도어 성능에 대 한 일반적인 실시간 모니터링 및 분석에 가장 적합 합니다.

## <a name="configure-logs"></a>로그 구성

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Front 도어 Standard/Premium을 검색 하 고 Azure Front 도어 프로필을 선택 합니다.

1. 프로필에서 **모니터링** 으로 이동 하 고 **진단 설정** 을 선택 합니다. **진단 설정 추가** 를 선택합니다.

   :::image type="content" source="../media/how-to-logging/front-door-logging-1.png" alt-text="진단 설정 방문 페이지의 스크린샷":::

1. **진단 설정** 아래에서  **진단 설정 이름** 에 대 한 이름을 입력 합니다.

1. **FrontDoorAccessLog**, **FrontDoorHealthProbeLog** 및 **FrontDoorWebApplicationFirewallLog** 에서  **로그** 를 선택 합니다.

1.  **대상 세부 정보** 를 선택 합니다. 대상 옵션은 다음과 같습니다. 

    * **Log Analytics에 보내기**
        * *구독* 및 *Log Analytics 작업 영역* 을 선택합니다.
    * **스토리지 계정에 보관**
        * *구독* 및 *스토리지 계정* 을 선택합니다. **보존 기간 (일)** 을 설정 합니다.
    * **이벤트 허브로 스트림**
        *  *구독, 이벤트 허브 네임 스페이스, 이벤트 허브 이름 (선택 사항)* 및 *이벤트 허브 정책 이름을* 선택 합니다. 

     :::image type="content" source="../media/how-to-logging/front-door-logging-2.png" alt-text="진단 설정 페이지의 스크린샷":::

1. **Save** 를 클릭합니다.

## <a name="access-log"></a>액세스 로그

Azure 전면 도어는 현재 아래와 같이 다음 스키마가 있고 JSON 형식으로 기록 된 각 항목에 대 한 개별 API 요청을 제공 합니다. 

| 속성 | Description |
|----------|-------------| 
| TrackingReference | AFD에 의해 처리 되는 요청을 식별 하는 고유한 참조 문자열로, 클라이언트에 대해 X-y 헤더로 전송 됩니다. 특정 요청의 액세스 로그에서 세부 정보를 검색하는 데 필요합니다. |
| 시간 | AFD edge에서 요청 된 콘텐츠를 클라이언트에 전달 하는 날짜 및 시간 (UTC)입니다. |
| HttpMethod | 요청에서 사용 하는 HTTP 메서드로, DELETE, GET, HEAD, OPTIONS, PATCH, POST 또는 PUT입니다. |
| HttpVersion | 뷰어에서 요청에 지정 된 HTTP 버전입니다. |
| RequestUri | 받은 요청의 URI 이 필드는 전체 체계, 포트, 도메인, 경로 및 쿼리 문자열입니다. |
| HostName | 클라이언트의 요청에 있는 호스트 이름입니다. 사용자 지정 도메인을 사용 하도록 설정 하 고 와일드 카드 도메인 (*. contoso.com)을 사용 하는 경우 호스트 이름은 a.contoso.com입니다. Azure Front 도어 도메인 (contoso.azurefd.net)을 사용 하는 경우 호스트 이름은 contoso.azurefd.net입니다. |
| RequestBytes | 요청 헤더 및 요청 본문을 포함하여 HTTP 요청 메시지의 크기(바이트)입니다. 뷰어가 헤더를 포함 하 여 요청에 포함 한 데이터의 바이트 수입니다. |
| ResponseBytes | 백 엔드 서버에서 응답으로 보낸 바이트 수입니다. |
| UserAgent | 클라이언트에서 사용한 브라우저 유형입니다. |
| ClientIp | 원래 요청을 수행한 클라이언트의 IP 주소입니다. 요청에 X로 전달 된 헤더가 있는 경우 클라이언트 IP는 동일한에서 선택 됩니다. |
| SocketIp | AFD에 지에 대 한 직접 연결의 IP 주소입니다. 클라이언트에서 HTTP 프록시 또는 부하 분산 장치를 사용 하 여 요청을 보낸 경우 SocketIp의 값은 프록시 또는 부하 분산 장치의 IP 주소입니다. |
| 대기 시간 | Afd에 지 서버에서 클라이언트에 대 한 응답의 마지막 바이트를 전송 하는 시간에 대 한 클라이언트 요청을 수신 하는 시간 (밀리초)입니다. 이 필드는 네트워크 대기 시간 및 TCP 버퍼링을 고려 하지 않습니다. |
| Owin.requestprotocol | 클라이언트에서 요청에 지정 된 프로토콜: HTTP, HTTPS |
| SecurityProtocol | 요청에 사용되는 TLS/SSL 프로토콜 버전이거나, 암호화가 없는 경우 null입니다. 가능한 값은 다음과 같습니다. SSLv3, TLSv1, TLSv 1.1, TLSv 1.2 |
| SecurityCipher | 요청 프로토콜의 값이 HTTPS 인 경우이 필드는 클라이언트에서 협상 한 TLS/SSL 암호화와 암호화를 위해 AFD를 나타냅니다. |
| 엔드포인트 | AFD 끝점의 도메인 이름 (예: contoso.z01.azurefd.net) |
| HttpStatusCode | AFD에서 반환 된 HTTP 상태 코드입니다. |
| Pop | 사용자 요청에 응답한 에지 POP입니다.  |
| 캐시 상태 | 캐싱이 제공 될 때 CDN 서비스에서 요청을 처리 하는 방법의 상태 코드를 제공 합니다. 가능한 값은 다음과 같습니다. HTTP 요청은 AFD edge POP 캐시에서 제공 되었습니다. <br> **누락**: HTTP 요청이 원본에서 제공 되었습니다. <br/> **PARTIAL_HIT**: 요청의 일부 바이트는 AFD edge POP 캐시에서 제공 되는 반면 일부 바이트는 개체 청크 시나리오의 원본에서 제공 되었습니다. <br> **CACHE_NOCONFIG**: 무시 시나리오를 포함 하 여 캐싱을 설정 하지 않고 요청을 전달 합니다. <br/> **PRIVATE_NOSTORE**: 고객의 캐싱 설정에서 캐시가 구성 되지 않았습니다. <br> **REMOTE_HIT**: 부모 노드 캐시에서 요청을 처리 했습니다. <br/> **N/A**:* * 서명 된 URL 및 규칙 집합에 의해 거부 된 요청입니다. |
| MatchedRulesSetName | 처리 된 규칙의 이름입니다. |
| RouteName | 요청에 일치 하는 경로의 이름입니다. |
| ClientPort | 요청을 수행한 클라이언트의 IP 포트입니다. |
| Referrer | 요청을 시작한 사이트의 URL입니다. |
| TimetoFirstByte | AFD의 시간 (밀리초)은 Azure Front 도어에서 측정 된 대로 첫 번째 바이트가 클라이언트로 전송 되는 시간에 대 한 요청을 받습니다. 이 속성은 클라이언트 데이터를 측정 하지 않습니다. |
| ErrorInfo | 이 필드는 각 응답의 오류 토큰에 대 한 자세한 정보를 제공 합니다. <br> **Noerror**: 오류가 없음을 나타냅니다. <br> **Certificateerror**: 일반 SSL 인증서 오류입니다. <br> **CertificateNameCheckFailed**: SSL 인증서의 호스트 이름이 잘못 되었거나 일치 하지 않습니다. <br> **Clientdisconnected**: 클라이언트 네트워크 연결 때문에 요청이 실패 했습니다. <br> **Clientgeoblocked**: 클라이언트가 IP의 지리적 위치에 따라 차단 되었습니다. <br> **UnspecifiedClientError**: 일반 클라이언트 오류입니다. <br> **Invalidrequest**: 요청이 잘못 되었습니다. 잘못 된 헤더, 본문 및 URL 때문에 발생할 수 있습니다. <br> **Dnsfailure**: DNS 오류가 발생 했습니다. <br> **DNSTimeout**: 백 엔드를 확인 하는 DNS 쿼리 시간이 초과 되었습니다. <br> **DNSNameNotResolved**: 서버 이름이 나 주소를 확인할 수 없습니다. <br> **OriginConnectionAborted**: 원본과의 연결이 비정상적으로 끊어졌습니다. <br> **OriginConnectionError**: 제네릭 원본 연결 오류입니다. <br> **OriginConnectionRefused**: 원본과의 연결이 설정 되지 않았습니다. <br> **OriginError**: 제네릭 원본 오류입니다. <br> **OriginInvalidRequest**: 잘못 된 요청을 원본으로 보냈습니다. <br> **Responseheadertoobig**: 원본에서 너무 많은 응답 헤더를 반환 했습니다. <br> **OriginInvalidResponse**:* * 원본이 잘못 되었거나 인식할 수 없는 응답을 반환 했습니다. <br> **OriginTimeout**: 원본 요청에 대 한 제한 시간이 만료 되었습니다. <br> **Responseheadertoobig**: 원본에서 너무 많은 응답 헤더를 반환 했습니다. <br> **RestrictedIP**: 제한 된 IP로 인해 요청이 차단 되었습니다. <br> **SSLHandshakeError**: SSL 핸드 흔들기 오류로 인해 원본과의 연결을 설정할 수 없습니다. <br> **SSLInvalidRootCA**: rootca.cer가 잘못 되었습니다. <br> **Sslininvalid cipher**: HTTPS 연결이 설정 된 암호가 잘못 되었습니다. <br> **OriginConnectionAborted**: 원본과의 연결이 비정상적으로 끊어졌습니다. <br> **OriginConnectionRefused**: 원본과의 연결이 설정 되지 않았습니다. <br> **UnspecifiedError**: 테이블의 오류에 맞지 않는 오류가 발생 했습니다. |
| OriginURL | 요청이 전송 되는 원본의 전체 URL입니다. 구성표, 호스트 헤더, 포트, 경로 및 쿼리 문자열로 구성 됩니다. <br> **Url 재작성**: 규칙 집합에 url 재작성 규칙이 있는 경우 경로가 다시 작성 된 경로를 참조 합니다. <br> **EDGE POP의 캐시** 에 지 POP에서 캐시가 적중 하는 경우 원본은 N/A입니다. <br> **대량 요청** 요청 된 콘텐츠가 다시 원본으로 이동 하는 여러 청크 요청이 있는 경우이 필드는 원본에 대 한 첫 번째 요청에 해당 합니다. 자세한 내용은 개체 청크를 참조 하세요. |
| OriginIP | 요청을 처리 한 원본 IP입니다. <br> **가장자리 POP의 캐시 적중** 에 지 POP에서 캐시가 적중 하는 경우 원본은 N/A입니다. <br> **대량 요청** 요청 된 콘텐츠가 다시 원본으로 이동 하는 여러 청크 요청이 있는 경우이 필드는 원본에 대 한 첫 번째 요청에 해당 합니다. 자세한 내용은 개체 청크를 참조 하세요. |
| OriginName| 원본에 대 한 전체 DNS 이름 (원본 URL의 호스트 이름)입니다. <br> **가장자리 POP의 캐시 적중** 에 지 POP에서 캐시가 적중 하는 경우 원본은 N/A입니다. <br> **대량 요청** 요청 된 콘텐츠가 다시 원본으로 이동 하는 여러 청크 요청이 있는 경우이 필드는 원본에 대 한 첫 번째 요청에 해당 합니다. 자세한 내용은 개체 청크를 참조 하세요. |

## <a name="health-probe-log"></a>상태 프로브 로그

상태 프로브 로그는 원본을 진단 하는 데 도움이 되는 실패 한 모든 프로브에 대 한 로깅을 제공 합니다.로그는 원본을 서비스로 다시 가져오는 데 사용할 수 있는 정보를 제공 합니다. 이 로그는 다음과 같은 경우에 유용할 수 있습니다.

* Azure Front 도어 트래픽이 일부 원본에 전송 되었음을 발견 했습니다. 예를 들어 트래픽을 수신 하는 네 가지 원본 중 3 개만 있습니다. 원본에서 프로브를 수신 하 고 있는지 여부를 확인 하려고 합니다.  

* 원본 상태%가 예상 보다 낮고 실패 한 원본 및 실패 이유를 알고 싶습니다.

### <a name="health-probe-log-properties"></a>상태 프로브 로그 속성

각 상태 프로브 로그에는 다음 스키마가 있습니다.

| 속성 | Description |
| --- | --- |
| HealthProbeId  | 요청을 식별 하는 고유 ID입니다. |
| 시간 | 프로브 완료 시간 |
| HttpMethod | 상태 프로브 요청에 사용 된 HTTP 메서드입니다. 값에는 상태 프로브 구성에 따라 GET 및 HEAD가 포함 됩니다. |
| 결과 | 상태 프로브 상태 프로브 상태, 값 포함 성공 및 기타 오류 텍스트입니다. |
| HttpStatusCode  | 원본에서 반환 된 HTTP 상태 코드입니다. |
| ProbeURL (대상) | 요청이 전송 되는 원본의 전체 URL입니다. 구성표, 호스트 헤더, 경로 및 쿼리 문자열로 구성 됩니다. |
| OriginName  | 요청이 전송 되는 원본입니다. 원본이 FDQN로 구성 된 경우이 필드는 관심의 원본을 찾는 데 도움이 됩니다. |
| POP | 프로브 요청을 보낸에 지 pop입니다. |
| 원본 IP | 대상 원본 IP입니다. FDQN를 사용 하 여 원본을 구성 하는 경우이 필드는 관심의 원본을 찾는 데 유용 합니다. |
| TotolaLatency | AFDX edge의 시간은 원본에 요청을 전송 하 여 원본에 요청을 보내면 AFDX edge에 마지막 응답을 보냅니다. |
| ConnectionLatency| HTTP 프로브 요청을 원본으로 보내기 위해 TCP 연결을 설정 하는 데 소요 된 시간입니다. | 
| DNSResolution 대기 시간 | 원본이 IP 대신 FDQN로 구성 된 경우 DNS 확인에 소요 된 시간입니다. 원본이 IP로 구성 된 경우 해당 없음 |

### <a name="health-probe-log-sample-in-json"></a>JSON의 상태 프로브 로그 샘플

`{ "records": [ { "time": "2021-02-02T07:15:37.3640748Z",
      "resourceId": "/SUBSCRIPTIONS/27CAFCA8-B9A4-4264-B399-45D0C9CCA1AB/RESOURCEGROUPS/AFDXPRIVATEPREVIEW/PROVIDERS/MICROSOFT.CDN/PROFILES/AFDXPRIVATEPREVIEW-JESSIE",
      "category": "FrontDoorHealthProbeLog",
      "operationName": "Microsoft.Cdn/Profiles/FrontDoorHealthProbeLog/Write",
      "properties": { "healthProbeId": "9642AEA07BA64675A0A7AD214ACF746E",
        "POP": "MAA",
        "httpVerb": "HEAD",
        "result": "OriginError",
        "httpStatusCode": "400",
        "probeURL": "http://afdxprivatepreview.blob.core.windows.net:80/",
        "originName": "afdxprivatepreview.blob.core.windows.net",
        "originIP": "52.239.224.228:80",
        "totalLatencyMilliseconds": "141",
        "connectionLatencyMilliseconds": "68",
        "DNSLatencyMicroseconds": "1814" } } ]
} `

## <a name="activity-logs"></a>활동 로그

활동 로그는 Azure Front 도어 Standard/Premium에서 수행 된 작업에 대 한 정보를 제공 합니다. 로그에는 Azure Front 도어에서 쓰기 작업을 수행 하는 사람 및 시기에 대 한 세부 정보가 포함 됩니다. 

> [!NOTE]
> 활동 로그에는 GET 작업이 포함 되지 않습니다. 또한 Azure Portal 또는 원래 관리 API를 사용 하 여 수행 하는 작업을 포함 하지 않습니다. 

Azure Monitor에서 Azure 리소스의 앞 문이나 모든 로그에 활동 로그에 액세스 합니다.

활동 로그를 보려면

1. 프런트 도어 프로필을 선택 합니다.

1. **활동 로그를 선택 합니다.**

1. 필터링 범위를 선택한 다음 **적용** 을 선택 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Front 도어 표준/프리미엄 (미리 보기) 보고서](how-to-reports.md)에 대해 알아봅니다.
- [Azure 전면 도어 표준/프리미엄 (미리 보기) 실시간 모니터링 메트릭에](how-to-monitor-metrics.md)대해 알아봅니다.
