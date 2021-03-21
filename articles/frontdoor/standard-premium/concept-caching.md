---
title: 'Azure Front 도어: 캐싱'
description: 이 문서는 캐싱이 설정 된 라우팅 규칙을 사용 하 여 Azure Front 도어 Standard/Premium의 동작을 이해 하는 데 도움이 됩니다.
services: front-door
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 73b2e8e59774e12ddb9aa684382510d1f2c151b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101100152"
---
# <a name="caching-with-azure-front-door-standardpremium-preview"></a>Azure 전면 도어 표준/프리미엄 (미리 보기)을 사용 하 여 캐싱

> [!Note]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

이 문서에서는 캐싱이 설정 된 경우 Front 도어 표준/프리미엄 (미리 보기) 경로 및 규칙 집합이 동작 하는 방법을 알아봅니다. Azure 전면 도어는 동적 사이트 가속 및 부하 분산이 있는 현대적인 CDN (Content Delivery Network)입니다.

> [!IMPORTANT]
> Azure 전면 도어 표준/프리미엄 (미리 보기)은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="delivery-of-large-files"></a>대용량 파일 전달

전면 도어 표준/프리미엄 (미리 보기)은 파일 크기에 캡이 없는 큰 파일을 제공 합니다. Front Door는 개체 청크라는 기술을 사용합니다. 대형 파일이 요청 되 면 Front 도어가 원본에서 파일의 더 작은 부분을 검색 합니다. 전체 또는 바이트 범위 파일 요청을 받은 후에는 Front 도어 환경에서 원본에서 파일을 8mb 청크 단위로 요청 합니다.

청크가 프런트 도어 환경에 도착 하면 캐시 되어 사용자에 게 즉시 제공 됩니다. 그런 다음 Front Door가 다음 청크를 동시에 프리페치합니다. 프리페치 과정 덕분에 콘텐츠가 사용자보다 한 청크 앞서 진행되므로 대기 시간이 줄어듭니다. 이 프로세스는 전체 파일이 다운로드 될 때 (요청 된 경우) 또는 클라이언트에서 연결을 닫을 때까지 계속 됩니다.

바이트 범위 요청에 대한 자세한 내용은 [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html)을 읽어보세요.
전면 도어는 수신 되는 모든 청크를 캐시 하므로 전체 파일을 프런트 도어 캐시에 캐시할 필요가 없습니다. 파일 또는 바이트 범위에 대 한 결과 요청은 캐시에서 제공 됩니다. 청크가 모두 캐시 되지 않은 경우에는 백 엔드에서 청크를 요청 하는 데 미리 페치가 사용 됩니다. 이 최적화는 바이트 범위 요청을 지 원하는 원본 기능에 의존 합니다. 원본에서 바이트 범위 요청을 지원 하지 않으면이 최적화가 적용 되지 않습니다.

## <a name="file-compression"></a>파일 압축

Azure 전면 도어에서 파일을 압축 하 여 성능 향상을 참조 하세요.

## <a name="query-string-behavior"></a>쿼리 문자열 동작

Front Door를 사용하면 쿼리 문자열이 포함된 웹 요청에 대해 파일이 캐시되는 방식을 제어할 수 있습니다. 쿼리 문자열이 있는 웹 요청에서 쿼리 문자열은 물음표(?) 다음에 나오는 요청 부분입니다. 쿼리 문자열은 필드 이름 및 해당 값이 등호(=)로 구분된 하나 이상의 키-값 쌍을 포함할 수 있습니다. 각 키-값 쌍은 앰퍼샌드(&)로 구분됩니다. 예: `http://www.contoso.com/content.mov?field1=value1&field2=value2` 요청에 대 한 쿼리 문자열에 키-값 쌍이 둘 이상 있는 경우 해당 순서는 중요 하지 않습니다.

* **쿼리 문자열 무시**:이 모드에서 전방 도어는 요청자의 쿼리 문자열을 첫 번째 요청에 대 한 원본으로 전달 하 고 자산을 캐시 합니다. 프런트 도어 환경에서 제공 되는 자산에 대 한 모든 결과 요청은 캐시 된 자산이 만료 될 때까지 쿼리 문자열을 무시 합니다.

* **각 고유한 URL 캐시**: 이 모드에서는 쿼리 문자열을 포함하여 고유한 URL이 있는 각 요청이 고유 캐시가 있는 고유 자산으로 처리됩니다. 예를 들어 요청에 대 한 원본의 응답이 `www.example.ashx?q=test1` 프런트 도어 환경에서 캐시 되 고 동일한 쿼리 문자열을 사용 하 여 결과 캐시에 대해 반환 됩니다. `www.example.ashx?q=test2`에 대한 요청은 자체 TTL(Time To Live) 설정과 함께 별도의 자산으로 캐시됩니다.
* 또한 캐시 키가 생성 될 때 지정 된 매개 변수를 포함 하거나 제외 하기 위해 규칙 집합을 사용 하 여 **캐시 키 쿼리 문자열** 동작을 지정 하거나 제외할 수 있습니다. 예를 들어 기본 캐시 키는/foo/image/asset.html이 고 샘플 요청은 `https://contoso.com//foo/image/asset.html?language=EN&userid=100&sessionid=200` 입니다. 쿼리 문자열 ' userid '를 제외 하는 규칙 집합 규칙이 있습니다. 그런 다음 쿼리 문자열 캐시 키가 `/foo/image/asset.html?language=EN&sessionid=200` 입니다.

## <a name="cache-purge"></a>캐시 제거

캐시 제거를 참조 하십시오.

## <a name="cache-expiration"></a>캐시 만료
다음 헤더 순서는 캐시에 항목이 저장 되는 기간을 결정 하는 데 사용 됩니다.</br>
1. Cache-Control: s-maxage=\<seconds>
2. Cache-control: max-age =\<seconds>
3. 기간이 \<http-date>

캐시 제어와 같이 응답이 캐시 되지 않음을 나타내는 응답 헤더를 Cache-Control 합니다. 전용, Cache-control: 캐시 없음 및 Cache-control: 저장소가 적용 되지 않습니다.  Cache-Control 없는 경우, 기본 동작은 Front 도어가 X 시간 동안 리소스를 캐시 하는 것입니다. 여기서 X는 1 ~ 3 일 사이에서 임의로 선택 됩니다.

## <a name="request-headers"></a>요청 헤더

다음 요청 헤더는 캐싱을 사용할 때 원본으로 전달 되지 않습니다.
* Content-Length
* Transfer-Encoding

## <a name="cache-duration"></a>캐시 기간

규칙 집합에서 캐시 기간을 구성할 수 있습니다. 규칙 집합을 통해 설정 된 캐시 기간은 true 캐시 재정의입니다. 즉, 원본 응답 헤더에 관계 없이 재정의 값을 사용 하 게 됩니다.

## <a name="next-steps"></a>다음 단계

* [규칙 집합 일치 조건](concept-rule-set-match-conditions.md) 에 대 한 자세한 정보
* [규칙 집합 작업](concept-rule-set-actions.md) 에 대 한 자세한 정보