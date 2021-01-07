---
title: Azure 전면 도어 캐싱 | Microsoft Docs
description: 이 문서는 캐싱이 설정 된 라우팅 규칙을 사용한 전방 도어 동작을 이해 하는 데 도움이 됩니다.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: 1a8064c3ff89c0bc8b0ceb5249492b912c219ce8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91535834"
---
# <a name="caching-with-azure-front-door"></a>Azure Front 도어를 사용 하 여 캐싱
다음 문서는 캐싱이 설정 된 경로 규칙을 사용 하 여 앞 도어 동작을 지정 합니다. 프런트 도어는 동적 사이트 가속 및 부하 분산이 있는 최신 Content Delivery Network (CDN)로, 다른 CDN과 마찬가지로 캐싱 동작도 지원 합니다.

## <a name="delivery-of-large-files"></a>대용량 파일 전달
Azure 전면 도어는 파일 크기에 캡이 없는 큰 파일을 제공 합니다. Front Door는 개체 청크라는 기술을 사용합니다. 대용량 파일이 요청되면 Front Door는 백엔드에서 더 작은 파일 조각을 검색합니다. 전체 또는 바이트 범위 파일 요청을 받은 후에는 프런트 도어 환경에서 백 엔드에서 8mb 청크를 통해 파일을 요청 합니다.

</br>청크가 프런트 도어 환경에 도착 하면 캐시 되어 사용자에 게 즉시 제공 됩니다. 그런 다음 Front Door가 다음 청크를 동시에 프리페치합니다. 프리페치 과정 덕분에 콘텐츠가 사용자보다 한 청크 앞서 진행되므로 대기 시간이 줄어듭니다. 이 프로세스는 전체 파일이 다운로드 될 때 (요청 된 경우) 또는 클라이언트에서 연결을 닫을 때까지 계속 됩니다.

</br>바이트 범위 요청에 대한 자세한 내용은 [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html)을 읽어보세요.
전면 도어는 수신 되는 모든 청크를 캐시 하므로 전체 파일을 프런트 도어 캐시에 캐시할 필요가 없습니다. 파일 또는 바이트 범위에 대 한 결과 요청은 캐시에서 제공 됩니다. 청크가 모두 캐시 되지 않은 경우에는 백 엔드에서 청크를 요청 하는 데 미리 페치가 사용 됩니다. 이 최적화는 백 엔드의 기능을 사용 하 여 바이트 범위 요청을 지원 합니다. 백 엔드가 바이트 범위 요청을 지원 하지 않으면이 최적화가 적용 되지 않습니다.

## <a name="file-compression"></a>파일 압축
전면 도어는에 지에서 동적으로 콘텐츠를 압축 하 여 클라이언트에 대 한 응답 시간을 더 작게 하 고 더 빠르게 수행할 수 있습니다. 모든 파일이 압축에 적합합니다. 그러나 압축에 적합 한 파일은 MIME 형식 이어야 합니다. 현재 앞 도어에서는이 목록을 변경할 수 없습니다. 현재 목록:</br>
- "application/eot"
- "application/font"
- "application/font-sfnt"
- "application/javascript"
- "application/json"
- "application/opentype"
- "application/otf"
- "application/pkcs7-mime"
- "application/truetype"
- "application/ttf",
- "application/vnd.ms-fontobject"
- "application/xhtml+xml"
- "application/xml"
- "application/xml+rss"
- "application/x-font-opentype"
- "application/x-font-truetype"
- "application/x-font-ttf"
- "application/x-httpd-cgi"
- "application/x-mpegurl"
- "application/x-opentype"
- "application/x-otf"
- "application/x-perl"
- "application/x-ttf"
- "application/x-javascript"
- "font/eot"
- "font/ttf"
- "font/otf"
- "font/opentype"
- "image/svg+xml"
- "text/css"
- "text/csv"
- "text/html"
- "text/javascript"
- "text/js", "text/plain"
- "text/richtext"
- "text/tab-separated-values"
- "text/xml"
- "text/x-script"
- "text/x-component"
- "text/x-java-source"

또한 파일의 크기는 1KB 이상 8MB 이하여야 합니다.

이러한 프로필은 다음과 같은 압축 인코딩을 지원합니다.
- [Gzip (GNU zip)](https://en.wikipedia.org/wiki/Gzip)
- [Brotli](https://en.wikipedia.org/wiki/Brotli)

요청이 gzip 및 Brotli 압축을 지원하는 경우 Brotli 압축이 우선적으로 적용됩니다.</br>
자산에 대 한 요청에서 압축을 지정 하 고 요청이 캐시 누락을 발생 하는 경우 전면 도어는 POP 서버에서 직접 자산을 압축 합니다. 이후 압축된 파일은 캐시에서 제공됩니다. 결과 항목은 transfer-encoding: chunked를 사용하여 반환됩니다.

## <a name="query-string-behavior"></a>쿼리 문자열 동작
Front Door를 사용하면 쿼리 문자열이 포함된 웹 요청에 대해 파일이 캐시되는 방식을 제어할 수 있습니다. 쿼리 문자열이 있는 웹 요청에서 쿼리 문자열은 물음표(?) 다음에 나오는 요청 부분입니다. 쿼리 문자열은 필드 이름 및 해당 값이 등호(=)로 구분된 하나 이상의 키-값 쌍을 포함할 수 있습니다. 각 키-값 쌍은 앰퍼샌드(&)로 구분됩니다. 예들 들어 `http://www.contoso.com/content.mov?field1=value1&field2=value2`입니다. 요청에 대 한 쿼리 문자열에 키-값 쌍이 둘 이상 있는 경우 해당 순서는 중요 하지 않습니다.
- **쿼리 문자열 무시**:이 모드에서 전방 도어는 요청자의 쿼리 문자열을 첫 번째 요청에 대 한 백 엔드로 전달 하 고 자산을 캐시 합니다. 프런트 도어 환경에서 제공 되는 자산에 대 한 모든 결과 요청은 캐시 된 자산이 만료 될 때까지 쿼리 문자열을 무시 합니다.

- **각 고유한 URL 캐시**: 이 모드에서는 쿼리 문자열을 포함하여 고유한 URL이 있는 각 요청이 고유 캐시가 있는 고유 자산으로 처리됩니다. 예를 들어 요청에 대 한 백 엔드에서의 응답은 `www.example.ashx?q=test1` 프런트 도어 환경에서 캐시 되 고 동일한 쿼리 문자열을 사용 하 여 결과 캐시에 대해 반환 됩니다. `www.example.ashx?q=test2`에 대한 요청은 자체 TTL(Time To Live) 설정과 함께 별도의 자산으로 캐시됩니다.

## <a name="cache-purge"></a>캐시 제거

전면 도어는 자산의 TTL (time-to-live)이 만료 될 때까지 자산을 캐시 합니다. 클라이언트에서 TTL이 만료 된 자산을 요청할 때마다 Front 도어 환경에서는 요청을 처리 하기 위해 업데이트 된 자산의 새 복사본을 검색 한 후 새로 고친 캐시를 저장 합니다.

사용자가 항상 자산의 최신 복사본을 받도록 보장하는 가장 좋은 방법은 각 업데이트에 대해 자산 버전 관리를 수행하여 자산을 새 URL로 게시하는 것입니다. Front Door는 다음 클라이언트 요청에 대한 새 자산을 즉시 검색합니다. 경우에 따라 모든 가장자리 노드에서 캐시된 콘텐츠를 제거하고 이러한 콘텐츠가 모두 새로 업데이트된 자산을 검색하도록 강제하려 할 수 있습니다. 그 이유는 웹 응용 프로그램에 대 한 업데이트 때문 이거나 잘못 된 정보를 포함 하는 자산을 신속 하 게 업데이트 했기 때문일 수 있습니다.

에 지 노드에서 제거 하려는 자산을 선택 합니다. 모든 자산을 지우려면 **모두 제거**를 선택 합니다. 그렇지 않고 **경로**에 제거 하려는 각 자산의 경로를 입력 합니다.

제거할 경로 목록에서 지원 되는 형식은 다음과 같습니다.

- **단일 경로 제거**:/pictures/strasbourg.png 같은 파일 확장명을 사용 하 여 자산의 전체 경로 (프로토콜 및 도메인 제외)를 지정 하 여 개별 자산을 제거 합니다.
- **와일드 카드 제거**: 별표(\*)를 와일드 카드로 사용할 수 있습니다. 경로에서/를 사용 하 여 끝점 아래의 모든 폴더, 하위 폴더 및 파일을 제거 \* 하거나, 폴더에/(예:/pictures/)를 지정 하 여 특정 폴더에 있는 모든 하위 폴더 및 파일을 제거 \* \* 합니다.
- **루트 도메인 제거**: 경로에 "/"가 포함된 엔드포인트의 루트를 제거합니다.

> [!NOTE]
> **와일드 카드 도메인 제거**:이 섹션에서 설명 하는 대로 제거에 대 한 캐시 된 경로 지정은 Front 문과 연결 된 와일드 카드 도메인에는 적용 되지 않습니다. 현재는 와일드 카드 도메인을 직접 제거 하는 기능을 지원 하지 않습니다. 특정 하위 도메인 및 제거 경로를 지정 하 여 특정 하위 도메인에서 경로를 제거할 수 있습니다. 예를 들어 내 Front 문이 있는 경우를 `*.contoso.com` `foo.contoso.com` 입력 하 여 하위 도메인의 자산을 삭제할 수 있습니다 `foo.contoso.com/path/*` . 현재, 해당 하는 경우 콘텐츠 제거 경로에 호스트 이름을 지정 하는 것은 와일드 카드 도메인의 하위 도메인에 imited 됩니다.
>

Front Door의 캐시 제거에서는 대/소문자를 구분하지 않습니다. 또한 쿼리 문자열은 독립적 이므로 URL을 제거 하면 해당 변수의 모든 쿼리 문자열 변형이 제거 됩니다. 

## <a name="cache-expiration"></a>캐시 만료
다음 헤더 순서는 캐시에 항목이 저장 되는 기간을 결정 하는 데 사용 됩니다.</br>
1. Cache-Control: s-maxage=\<seconds>
2. Cache-control: max-age =\<seconds>
3. 기간이 \<http-date>

캐시 제어와 같이 응답이 캐시 되지 않음을 나타내는 응답 헤더를 Cache-Control 합니다. 전용, Cache-control: 캐시 없음 및 Cache-control: 저장소가 적용 되지 않습니다.  Cache-Control 없는 경우, 기본 동작은 프런트 도어가 x 시간에 대 한 리소스를 캐시 합니다. 여기서 X는 1 ~ 3 일 사이에 임의로 선택 됩니다.

## <a name="request-headers"></a>요청 헤더

캐싱을 사용 하는 경우 다음 요청 헤더는 백 엔드로 전달 되지 않습니다.
- Content-Length
- Transfer-Encoding

## <a name="cache-duration"></a>캐시 기간

캐시 기간은 Front 도어 디자이너와 규칙 엔진에서 모두 구성할 수 있습니다. 전면 도어 디자이너에 설정 된 캐시 기간이 최소 캐시 기간입니다. 이 재정의는 원본의 캐시 컨트롤 헤더에 재정의 값 보다 큰 TTL이 있는 경우에는 작동 하지 않습니다. 

규칙 엔진을 통해 설정 된 캐시 기간은 진정한 캐시 재정의 이므로 원본 응답 헤더에 관계 없이 재정의 값을 사용 합니다.

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.
