---
title: Azure Front 도어 표준/프리미엄 (미리 보기) 보고서
description: 이 문서에서는 Azure Front 도어에서 보고가 작동 하는 방식을 설명 합니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 9670d8204d04fc770bf3fe98a270a3f6ccbf234b
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100269"
---
# <a name="azure-front-door-standardpremium-preview-reports"></a>Azure Front 도어 표준/프리미엄 (미리 보기) 보고서

> [!IMPORTANT]
> Azure 전면 도어 표준/프리미엄 (미리 보기)은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure 전면 도어 표준/프리미엄 분석 보고서는 Azure Front 도어가 관련 웹 응용 프로그램 방화벽 메트릭과 함께 동작 하는 방법에 대 한 기본 제공 및 전체 보기를 제공 합니다. 액세스 로그를 활용 하 여 추가 문제 해결 및 디버깅을 수행할 수도 있습니다. Azure Front 도어 분석 보고서에는 트래픽 보고서와 보안 보고서가 포함 됩니다.

| 보고서 | 세부 정보 |
|---------|---------|
| 주요 메트릭 개요 | Azure 프런트 도어 가장자리에서 클라이언트로 전송 된 전체 데이터를 표시 합니다.<br/>-최고 대역폭<br/>-요청 <br/>-캐시 적중률<br/> -총 대기 시간<br/>-5XX 오류 율 |
| 도메인별 트래픽 | -프로필 아래의 모든 도메인에 대 한 개요를 제공 합니다.<br/>-AFD edge에서 클라이언트로 전송 된 데이터의 분석<br/>-총 요청<br/>-도메인 별 3XX/4XX/5XX 응답 코드 |
| 위치별 트래픽 | -상위 국가의 요청 및 사용에 대 한 지도 보기를 표시 합니다.<br/>-상위 국가의 추세 보기 |
| 사용량 | -Azure Front 도어 edge에서 클라이언트로 전송 되는 데이터를 표시 합니다.<br/>-원본에서 AFD edge로 데이터 전송<br/>-AFD edge에서 클라이언트로의 대역폭<br/>-원본에서 AFD edge로의 대역폭<br/>-요청<br/>-총 대기 시간<br/>-HTTP 상태 코드에의 한 요청 수 추세 |
| 캐싱 | -요청 수 별 캐시 적중 비율을 표시 합니다.<br/>-적중 및 누락 요청의 추세 보기 |
| 상위 URL | -요청 수를 표시 합니다. <br/>-전송 된 데이터 <br/>-캐시 적중률 <br/>-가장 많이 요청 된 50 자산에 대 한 응답 상태 코드 배포 |
| 상위 참조 페이지 | -요청 수를 표시 합니다. <br/>-전송 된 데이터 <br/>-캐시 적중률 <br/>-트래픽을 생성 하는 상위 50 개 참조 페이지의 응답 상태 코드 배포 |
| 상위 사용자 에이전트 | -요청 수를 표시 합니다. <br/>-전송 된 데이터 <br/>-캐시 적중률 <br/>-콘텐츠를 요청 하는 데 사용 된 상위 50 사용자 에이전트에 대 한 응답 상태 코드 배포 |

| 보안 보고서 | 세부 정보 |
|---------|---------|
| 주요 메트릭 개요 | -일치 하는 WAF 규칙을 표시 합니다.<br/>-일치 하는 OWASP 규칙<br/>-일치 하는 BOT 규칙<br/>-일치 하는 사용자 지정 규칙 |
| 차원 별 메트릭 | -작업과 일치 하는 WAF 규칙 추세 분석<br/>-규칙 집합 유형 및 이벤트 별 이벤트를 규칙 그룹별 도넛형 차트<br/>-규칙 ID, 국가, IP 주소, URL 및 사용자 에이전트로 상위 이벤트의 목록을 나눕니다.  |

> [!NOTE]
> 보안 보고서는 Azure Front 도어 Premium SKU 에서만 사용할 수 있습니다.

대부분의 보고서는 액세스 로그를 기반으로 하며 Azure Front 도어를 통해 고객에 게 무료로 제공 됩니다. 고객은 액세스 로그를 사용 하도록 설정 하거나 구성 하 여 이러한 보고서를 볼 필요가 없습니다. 보고서는 포털 및 API를 통해 액세스할 수 있습니다. CSV 다운로드도 지원 됩니다. 

보고서는 이전 90 일 로부터 선택한 날짜 범위를 지원 합니다. 데이터 요소가 5 분 마다, 1 시간 마다 또는 매일 선택 된 날짜 범위를 기준으로 합니다. 일반적으로 1 시간 내에 지연 시간을 사용 하 여 데이터를 볼 수 있으며 지연 시간이 최대 몇 시간까지 걸릴 수 있습니다. 

## <a name="access-reports-using-the-azure-portal"></a>Azure Portal를 사용 하 여 보고서 액세스

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 고 Azure Front 도어 표준/프리미엄 프로필을 선택 합니다.

1. 탐색 창의 *분석* 에서 **보고서 또는 보안** 을 선택 합니다.

   :::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="보고서 방문 페이지의 스크린샷":::

1. 각 차원에 대해 7 개의 탭이 있으며 원하는 차원을 선택 합니다.

   * 도메인별 트래픽
   * 사용량 
   * 위치별 트래픽
   * 캐시
   * 상위 url
   * 상위 참조 페이지
   * 상위 사용자 에이전트

1. 차원을 선택한 후에는 다른 필터를 선택할 수 있습니다.
  
    1. **데이터 표시** -도메인별 트래픽을 확인할 날짜 범위를 선택 합니다. 사용 가능한 범위는 다음과 같습니다.
        
        * 최근 24시간
        * 최근 7일
        * 지난 30일
        * 지난 90 일
        * 이번 달
        * 지난달
        * 사용자 지정 날짜

         기본적으로 데이터는 지난 7 일간 표시 됩니다. 꺾은선형 차트가 있는 탭의 경우 데이터 세분성은 기본 동작으로 선택한 날짜 범위로 이동 합니다. 
    
        * 5 분-24 시간 보다 작거나 같은 날짜 범위에 대해 5 분 마다 하나의 데이터 요소
        * 시간별 – 날짜 범위에 대 한 1 시간 마다 24 시간에서 30 일 사이의 데이터
        * 일별 – 날짜 범위가 30 일을 초과 하는 하루에 한 번의 데이터입니다.

        항상 집계를 사용 하 여 기본 집계 세분성을 변경할 수 있습니다. 참고: 14 일 보다 긴 데이터 범위에 대해 5 분이 작동 하지 않습니다. 

    1. **위치** -국가별로 단일 또는 여러 클라이언트 위치를 선택 합니다. 국가는 북아메리카, 아시아, 유럽, 아프리카, 오세아니아 및 남부 대륙의 6 개 지역으로 그룹화 됩니다. [지역/국가 매핑](https://en.wikipedia.org/wiki/Subregion)을 참조 하세요. 기본적으로 모든 국가가 선택 되어 있습니다.
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-locations.png" alt-text="Location dimension에 대 한 보고서의 스크린샷":::
   
    1. **프로토콜** -트래픽 데이터를 보려면 HTTP 또는 HTTPS 중 하나를 선택 합니다.
 
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-protocol.png" alt-text="프로토콜 차원에 대 한 보고서의 스크린샷":::
    
    1. **도메인** -단일 또는 다중 끝점 또는 사용자 지정 도메인을 선택 합니다. 기본적으로 모든 끝점과 사용자 지정 도메인이 선택 됩니다. 
    
        * 한 프로필에서 끝점 또는 사용자 지정 도메인을 삭제 한 다음 다른 프로필에 동일한 끝점 또는 도메인을 다시 만듭니다. 끝점은 두 번째 끝점으로 간주 됩니다.  
        * 사용자 지정 도메인을 기준으로 보고서를 보는 경우-사용자 지정 도메인 하나를 삭제 하 고 다른 끝점에 바인딩하면 됩니다. 이러한 항목은 하나의 사용자 지정 도메인으로 처리 됩니다. 끝점 별로 보기-개별 항목으로 처리 됩니다. 
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-domain.png" alt-text="도메인 차원에 대 한 보고서 스크린샷":::

1. 데이터를 CSV 파일로 내보내려면 선택한 탭에서 *Csv 다운로드* 링크를 선택 합니다.

    :::image type="content" source="../media/how-to-reports/front-door-reports-download-csv.png" alt-text="보고서용 csv 파일 다운로드의 스크린샷":::

### <a name="key-metrics-for-all-reports"></a>모든 보고서에 대 한 주요 메트릭

| 메트릭 | 설명 |
|---------|---------|
| 전송되는 데이터 | 선택한 시간 프레임, 클라이언트 위치, 도메인 및 프로토콜에 대해 AFD edge Pop에서 클라이언트로 전송 된 데이터를 표시 합니다. |
| 최대 대역폭 | 선택한 시간 프레임, 클라이언트 위치, 도메인 및 프로토콜에 대 한 Azure Front 도어가 edge Pop에서 클라이언트로의 최대 대역폭 사용량 (초)입니다. | 
| 총 요청 수 | AFD edge Pop에서 선택한 시간 프레임, 클라이언트 위치, 도메인 및 프로토콜에 대 한 클라이언트에 응답 한 요청 수입니다. |
| 캐시 적중률 | 선택한 시간 프레임, 클라이언트 위치, 도메인 및 프로토콜에 대 한에 지 캐시의 콘텐츠를 AFD에서 제공 하는 모든 캐시 가능 요청의 백분율입니다. |
| 5XX 오류 율 | 클라이언트에 대 한 HTTP 상태 코드를 선택한 시간 프레임, 클라이언트 위치, 도메인 및 프로토콜에 대 한 5XX 인 요청 비율입니다. |
| 총 대기 시간 | 선택한 시간 프레임, 클라이언트 위치, 도메인 및 프로토콜에 대 한 모든 요청의 평균 대기 시간입니다. 각 요청에 대 한 대기 시간은 azure Front 도어에서 클라이언트로 전송 되는 마지막 응답 바이트까지 클라이언트 요청이 수신 되는 총 시간으로 측정 됩니다. |

## <a name="traffic-by-domain"></a>도메인별 트래픽

도메인 별 트래픽은이 Azure 전면 도어 프로필 아래의 모든 도메인에 대 한 그리드 보기를 제공 합니다. 이 보고서에서 다음을 볼 수 있습니다. 
* 요청
* Azure Front 도어에서 클라이언트로 전송 되는 데이터
* 각 도메인의 상태 코드가 있는 요청 (3XX, 4Xx 및 5XX)

도메인에는 액세스 보고서 세션에 설명 된 대로 끝점 및 사용자 지정 도메인이 포함 됩니다.  

다른 탭으로 이동 하 여 자세한 정보를 확인 하거나 액세스 로그를 확인 하 여 예상 대로 메트릭을 찾을 수 있습니다. 

:::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="보고서용 방문 페이지의 스크린샷":::


## <a name="usage"></a>사용량

이 보고서는 다음과 같은 다양 한 차원의 트래픽 및 응답 상태 코드에 대 한 추세를 보여 줍니다.

* Edge에서 클라이언트로 전송 되는 데이터와 꺾은선형 차트의 원본에서 가장자리로 전송 된 데이터입니다. 

* Edge에서 클라이언트로 전송 되는 데이터는 꺾은선형 차트입니다. 

* 꺾은선형 차트의 edge에서 클라이언트로의 요청 수입니다.  

* 선형 차트에서 프로토콜, HTTP 및 HTTPS를 통한 클라이언트에서 클라이언트로의 요청 수입니다. 

* Edge에서 클라이언트로의 대역폭을 꺾은선형 차트에 있습니다. 

* 프런트 도어에서 클라이언트로 전송 된 마지막 응답 바이트까지 전달 된 클라이언트 요청의 총 시간을 측정 하는 총 대기 시간입니다.

* 선형 차트의 HTTP 상태 코드에 따라 edge에서 클라이언트로의 요청 수입니다. 모든 요청에서 HTTP 상태 코드를 생성 합니다. HTTP 상태 코드는 원시 로그의 HTTPStatusCode에 표시 됩니다. 상태 코드는 CDN에 지에서 요청을 처리 하는 방법을 설명 합니다. 예를 들어 2xx 상태 코드는 요청이 클라이언트에 성공적으로 처리 되었음을 나타냅니다. 4xx 상태 코드는 오류가 발생 했음을 나타냅니다. HTTP 상태 코드에 대한 자세한 내용은 HTTP 상태 코드 목록을 참조하세요. 

* HTTP 상태 코드에 따라에 지에서 클라이언트로의 요청 수입니다. 그리드의 모든 요청에 대 한 HTTP 상태 코드의 요청 비율입니다. 

:::image type="content" source="../media/how-to-reports/front-door-reports-usage.png" alt-text="사용 별 보고서 스크린샷" lightbox="../media/how-to-reports/front-door-reports-usage-expanded.png":::

## <a name="traffic-by-location"></a>위치별 트래픽

이 보고서는 자산에 가장 많이 액세스 하는 방문자의 인기 50 위치를 표시 합니다. 또한이 보고서는 국가의 메트릭을 분석 하 여 대부분의 트래픽이 생성 되는 국가의 전체 보기를 제공 합니다. 마지막으로 더 높은 캐시 적중률 또는 4XX/5XX 오류 코드가 있는 국가를 확인할 수 있습니다.

:::image type="content" source="../media/how-to-reports/front-door-reports-by-location.png" alt-text="위치의 보고서 스크린샷" lightbox="../media/how-to-reports/front-door-reports-by-location-expanded.png":::

보고서에는 다음이 포함 되어 있습니다.

* 데이터를 전송 하는 상위 50 국가의 세계 지도 보기 또는 선택한 요청.
* 데이터를 전송 하 고 선택한 요청에 따라 상위 5 개 국가의 두 꺾은선형 차트 추세 보기입니다. 
* 해당 데이터가 AFD에서 클라이언트로 전송 되 고, 모든 국가, 요청, 모든 국가에 대 한 요청%, 캐시 적중률, 4XX 응답 코드 및 5XX 응답 코드를 전송 하는 데이터를 포함 하는 상위 국가의 그리드입니다.

## <a name="caching"></a>캐싱

보고서 캐싱은 요청에 따라 캐시 적중/누락 및 캐시 적중률의 차트 보기를 제공 합니다. 이러한 핵심 메트릭은 캐시 적중의 가장 빠른 성능 결과 때문에 CDN에서 콘텐츠를 캐시 하는 방법을 설명 합니다. 캐시 누락을 최소화 하 여 데이터 전달 속도를 최적화할 수 있습니다. 이 보고서에는 다음이 포함 됩니다.

* 선형 차트의 캐시 적중 횟수 및 누락 개수 추세입니다.

* 꺾은선형 차트의 캐시 적중 비율입니다.

캐시 적중 수/누락은 클라이언트 요청에 대 한 요청 수 캐시 적중 수 및 캐시 누락 수를 설명 합니다.

* 적중 횟수: Azure CDN에 지 서버에서 직접 제공 되는 클라이언트 요청입니다. 는 원시 로그의 CacheStatus에 대 한 값이 적중, PARTIAL_HIT 또는 원격 적중 인 요청을 나타냅니다. 

* 누락: 원본에서 콘텐츠를 가져오는 Azure CDN edge 서버에서 제공 하는 클라이언트 요청입니다. 는 원시 로그의 CacheStatus 필드에 대 한 값이 누락 된 요청을 참조 합니다. 

**Cache hit ratio** edge에서 직접 처리 되는 캐시 된 요청의 백분율을 설명 합니다. Cache hit ratio의 수식은 `(PARTIAL_HIT +REMOTE_HIT+HIT/ (HIT + MISS + PARTIAL_HIT + REMOTE_HIT)*100%` 입니다. 

이 보고서는 캐싱 시나리오를 고려 하 여 다음과 같은 요구 사항을 충족 하는 요청을 계산으로 가져옵니다. 

* 요청 된 콘텐츠는 요청자 또는 원본 방패와 가장 가까운 POP에 캐시 되었습니다. 

* 개체 청크를 위한 부분적으로 캐시 된 콘텐츠입니다.

다음과 같은 경우 모두 제외 됩니다. 

* 규칙 집합으로 인해 거부 된 요청입니다.

* 사용 안 함 캐시로 설정 된 일치 규칙 집합을 포함 하는 요청입니다. 

* WAF에 의해 차단 된 요청입니다. 

* 원본 응답 헤더는 캐시 되지 않아야 함을 의미 합니다. 예를 들어 Cache-Control: private, Cache-Control: no-cache 또는 Pragma: no-cache 헤더는 자산이 캐시되지 않도록 합니다. 

:::image type="content" source="../media/how-to-reports/front-door-reports-caching.png" alt-text="캐싱에 대 한 보고서의 스크린샷":::

## <a name="top-urls"></a>상위 Url

상위 Url을 사용 하면 특정 끝점 또는 사용자 지정 도메인에 대해 발생 하는 트래픽 양을 볼 수 있습니다. 지난 90 일 동안 모든 기간 동안 가장 요청 된 50 자산에 대 한 데이터가 표시 됩니다. 인기 있는 Url이 다음 값과 함께 표시 됩니다. 사용자는 요청 수, 요청%, 전송 된 데이터 및 전송 된 데이터를 기준으로 Url을 정렬할 수 있습니다. 모든 메트릭은 시간별로 집계 되며 선택한 시간 프레임 마다 다를 수 있습니다. URL은 액세스 로그에서 RequestUri의 값을 참조 합니다. 

:::image type="content" source="../media/how-to-reports/front-door-reports-top-url.png" alt-text="상위 URL에 대 한 보고서 스크린샷":::

* URL은 형식으로 요청 된 자산의 전체 경로를 나타냅니다 `http(s)://contoso.com/index.html/images/example.jpg` . 
* 요청 수입니다. 
* Azure Front 도어가 처리 하는 총 요청 비율을 요청 합니다. 
* 전송 된 데이터입니다. 
* 전송 된 데이터%. 
* 캐시 적중률%
* 응답 코드가 4XX 인 요청
* 응답 코드가 5XX 인 요청

> [!NOTE]
> 상위 Url은 시간이 지남에 따라 변경 될 수 있으며 상위 50 Url의 정확한 목록을 가져올 수 있습니다. Azure Front 도어가 모든 URL 요청을 시간별로 계산 하 고 하루 중 누계를 유지 합니다. 500 Url의 아래쪽에 있는 Url은 해당 날짜에 대 한 목록에서 증가 하거나 삭제 될 수 있으므로 이러한 Url의 총 수는 근사치입니다.  
>
> 상위 50 Url은 목록에 포함 될 수 있지만 목록에서 사라집니다. 따라서 상위 Url의 숫자는 일반적으로 신뢰할 수 있습니다. URL이 목록에서 삭제 되 고 하루에 다시 증가 하는 경우 해당 기간에 표시 되는 URL의 요청 번호에 따라 목록에서 누락 된 기간 동안의 요청 수가 예상 됩니다.  
>
> 상위 사용자 에이전트에도 동일한 논리가 적용 됩니다. 

## <a name="top-referrers"></a>상위 참조 페이지

상위 참조 페이지를 통해 고객은 특정 끝점 또는 사용자 지정 도메인의 콘텐츠에 대 한 가장 많은 요청을 시작한 상위 50 참조 페이지를 볼 수 있습니다. 지난 90 일 동안의 모든 기간에 대 한 데이터를 볼 수 있습니다. 참조 페이지는 요청이 생성 된 URL을 나타냅니다. 참조 페이지는 검색 엔진 또는 다른 웹 사이트에서 가져올 수 있습니다. 사용자가 브라우저의 주소 줄에 URL을 직접 입력 하는 경우 (예: http (s)://contoso.com/index.html) 요청 된 참조 페이지는 "Empty"입니다. 상위 참조 페이지 보고서에는 다음 값이 포함 됩니다. 요청 수, 요청%, 전송 된 데이터 및 전송 된 데이터%를 기준으로 정렬할 수 있습니다. 모든 메트릭은 시간별로 집계 되며 선택한 시간 프레임 마다 다를 수 있습니다. 

* 참조 페이지, 원시 로그의 참조 페이지 값 
* 요청 수 
* 선택한 기간 동안 Azure CDN에서 제공 하는 총 요청 비율 (%)을 요청 합니다. 
* 전송되는 데이터 
* 전송 된 데이터% 
* 캐시 적중률%
* 응답 코드가 4XX 인 요청
* 응답 코드가 5XX 인 요청

:::image type="content" source="../media/how-to-reports/front-door-reports-top-referrer.png" alt-text="상위 참조 페이지의 보고서 스크린샷":::

## <a name="top-user-agent"></a>상위 사용자 에이전트

이 보고서를 사용 하면 콘텐츠를 요청 하는 데 사용 된 상위 50 사용자 에이전트의 그래픽 및 통계 보기를 사용할 수 있습니다. 예를 들면 다음과 같습니다.
* Mozilla/5.0 (Windows NT 10.0; 용 
* AppleWebKit/537.36 (Gecko와 같은 KHTML) 
* Chrome/86.0.4240.75 
* Safari/537.36.  

그리드는 요청 수, 요청%, 전송 된 데이터 및 전송 된 데이터, 캐시 적중률%, 응답 코드를 4XX로 포함 하는 요청 및 응답 코드가 5XX 인 요청을 표시 합니다. 사용자 에이전트는 액세스 로그에서 UserAgent의 값을 참조 합니다.

## <a name="security-report"></a>보안 보고서

이 보고서를 사용 하면 다양 한 차원 별로 WAF 패턴의 그래픽 및 통계 보기를 사용할 수 있습니다.

| 차원 | Description |
|---------|---------|
| 개요 메트릭-일치 WAF 규칙 | 사용자 지정 WAF 규칙, 관리 되는 WAF 규칙 및 봇 관리자와 일치 하는 요청입니다. |
| 개요 메트릭-차단 된 요청 | WAF 규칙에 일치 하는 모든 요청 중 WAF 규칙에 의해 차단 된 요청 비율입니다. |
| 개요 메트릭-일치 하는 관리 되는 규칙 | 블록, 로그, 허용 및 리디렉션 요청에 대 한 4 개의 꺾은선형 차트 추세입니다. |
| 개요 메트릭-일치 하는 사용자 지정 규칙 | 사용자 지정 WAF 규칙과 일치 하는 요청입니다. |
| 개요 메트릭-일치 하는 Bot 규칙 | Bot Manager와 일치 하는 요청입니다. |
| 작업 별 WAF 요청 추세 | 블록, 로그, 허용 및 리디렉션 요청에 대 한 4 개의 꺾은선형 차트 추세입니다. |
| 규칙 유형별 이벤트 | 규칙 유형별 WAF 요청 분포의 도넛형 차트 (예: 봇, 사용자 지정 규칙 및 관리 규칙) |
| 규칙 그룹별 이벤트 | 규칙 그룹별로 배포 된 WAF 요청에 대 한 도넛형 차트입니다. |
| 작업 별 요청 | 작업 별로 요청 된 테이블로, 내림차순으로 정렬 됩니다. |
| 상위 규칙 Id에의 한 요청 | 상위 50 규칙 Id가 내림차순으로 요청 하는 테이블입니다. |
| 상위 국가의 요청 |  상위 50 국가의 요청 테이블 (내림차순) |
| 상위 클라이언트 Ip의 요청 |  상위 50 Ip가 내림차순으로 요청 하는 테이블입니다. |
| 상위 요청 URL 별 요청 |  상위 50 Url에서 내림차순으로 요청 하는 테이블입니다. |
| 상위 호스트 이름으로 요청 | 상위 50 호스트 이름으로 내림차순으로 요청 하는 테이블입니다. |
| 상위 사용자 에이전트의 요청 | 상위 50 사용자 에이전트에서 내림차순으로 요청 하는 테이블입니다. |

## <a name="cvs-format"></a>CVS 형식

보고서의 여러 탭에 대 한 CSV 파일을 다운로드할 수 있습니다. 이 섹션에서는 각 CSV 파일의 값에 대해 설명 합니다.

### <a name="general-information-about-the-cvs-report"></a>CVS 보고서에 대 한 일반 정보

모든 CSV 보고서는 일반 정보를 포함 하며 모든 CSV 파일에서 정보를 사용할 수 있습니다. 다운로드 한 보고서를 기반으로 하는 변수를 사용 합니다. 


| 값 | 설명 |
|---------|---------|
| 보고서 | 보고서의 이름 | 
| 도메인 | 보고서의 끝점 또는 사용자 지정 도메인 목록입니다. |
| StartDateUTC | 보고서를 생성 한 날짜 범위의 시작 시간 (UTC (협정 세계시))입니다. |
| EndDateUTC | 보고서를 생성 한 날짜 범위의 끝 날짜를 UTC (협정 세계시)로 표시 합니다. |
| GeneratedTimeUTC | 보고서를 생성 한 날짜와 시간 (UTC (협정 세계시))입니다. |
| 위치 | 클라이언트 요청이 시작 된 국가 목록입니다. 기본값은 ALL입니다. 보안 보고서에는 적용 되지 않습니다. |
| 프로토콜 | 요청, HTTP 또는 HTTPs의 프로토콜입니다. 보고서 및 보안 보고서의 사용자 에이전트 별 상위 URL 및 트래픽에는 적용 되지 않습니다. |
| 집계 | 각 행의 데이터 집계 세분성 (5 분 마다, 매시간, 매일) 도메인, 상위 URL 및 보고서의 사용자 에이전트 및 보안 보고서에서의 트래픽에는 적용 되지 않습니다. |

### <a name="data-in-traffic-by-domain"></a>도메인의 트래픽 데이터

* 도메인 
* 총 요청 
* 캐시 적중률 
* 3XX 요청 
* 4XX 요청 
* 5XX 요청 
* ByteTransferredFromEdgeToClient 

### <a name="data-in-traffic-by-location"></a>위치별 트래픽 데이터

* 위치
* TotalRequests
* 요구
* BytesTransferredFromEdgeToClient

### <a name="data-in-usage"></a>사용 중인 데이터

이 CSV 파일에는 세 개의 보고서가 있습니다. HTTP 프로토콜에 대 한 하나는 HTTPS 프로토콜에 대 한 하나이 고 다른 하나는 HTTP 상태 코드입니다. 

HTTP 및 HTTPs에 대 한 보고서는 동일한 데이터 집합을 공유 합니다. 

* 시간 
* 프로토콜 
* 데이터가 (바이트) 
* TotalRequest 
* bpsFromEdgeToClient 
* 2XXRequest 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest 

HTTP 상태 코드에 대 한 보고서입니다. 

* 시간 
* 데이터가 (바이트) 
* TotalRequest 
* bpsFromEdgeToClient 
* 2XXRequest 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest

### <a name="data-in-caching"></a>캐시의 데이터 

* 시간
* CacheHitRatio 
* HitRequests 
* 잘못 되는 요청 

### <a name="data-in-top-url"></a>상위 URL의 데이터 

* URL 
* TotalRequests 
* 요구 
* 데이터가 (바이트) 
* 데이터가 

### <a name="data-in-user-agent"></a>사용자 에이전트의 데이터 

* UserAgent 
* TotalRequests 
* 요구 
* 데이터가 (바이트) 
* 데이터가 

### <a name="security-report"></a>보안 보고서 

아래에 동일한 필드가 있는 일곱 개의 테이블이 있습니다.  

* BlockedRequests 
* AllowedRequests 
* LoggedRequests 
* RedirectedRequests 
* OWASPRuleRequests 
* CustomRuleRequests 
* BotRequests 

7 개의 테이블은 시간, 규칙 ID, 국가, IP 주소, URL, 호스트 이름, 사용자 에이전트에 대 한 것입니다. 

## <a name="next-steps"></a>다음 단계

[Azure Front 도어 표준/프리미엄 실시간 모니터링 메트릭에](how-to-monitor-metrics.md)대해 알아봅니다.
