<properties
   pageTitle="Azure에서 Elasticsearch로 데이터 집계 및 qQuery 성능 조정 | Microsoft Azure"
   description="Elasticsearch에 대한 쿼리 및 검색 성능을 최적화하는 경우 고려 사항의 요약입니다."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# Azure에서 Elasticsearch로 데이터 집계 및 qQuery 성능 조정

이 문서는 [시리즈의 일부](guidance-elasticsearch.md)입니다.

Elasticsearch를 사용하는 주된 이유는 데이터 검색을 지원하기 위해서입니다. 사용자가 찾고 있는 정보를 빠르게 찾을 수 있어야 합니다. 또한 시스템은 사용자가 데이터에 대해 질문하고, 상관 관계를 검색하며 비즈니스 의사 결정을 수행할 수 있는 결론을 얻도록 해야 합니다. 이 과정이 데이터가 정보와 다른 이유입니다.

이 문서는 시스템을 쿼리 및 검색 성능에 최적화하기 위한 가장 좋은 방법을 결정할 때 고려할 수 있는 옵션을 요약합니다.

모든 성능 권장 사항은 상황에 적용되는 시나리오, 인덱싱하는 데이터의 볼륨 및 응용 프로그램 및 사용자가 데이터를 쿼리하는 속도에 크게 의존합니다. 구성에서 모든 변경의 결과 또는 사용자 고유의 데이터 및 워크로드를 사용하는 인덱스 구조를 신중하게 테스트하여 특정 시나리오에 대한 이점을 평가해야 합니다. 이를 위해 이 문서는 다양한 구성을 사용하여 구현된 한 가지 특정 시나리오에 수행한 여러 벤치마크를 설명합니다. 고유한 시스템의 성능을 평가하기 위해 수행하는 방법을 적용할 수 있습니다.

## 인덱스 및 쿼리 성능 고려 사항

이 섹션에서는 빠른 쿼리 및 검색을 지원해야 하는 인덱스를 디자인하는 경우 생각해야 할 몇 가지 일반적인 요인을 설명합니다.

### 인덱스에 여러 형식 저장

Elasticsearch 인덱스는 여러 형식을 포함할 수 있습니다. 이 방법을 사용하지 않고 각 형식에 대한 별도 인덱스를 만드는 것이 나을 수 있습니다. 다음 사항을 고려합니다.

- 다양한 형식은 다른 분석기를 지정하며 형식 수준이 아닌 인덱스 수준에서 쿼리를 수행하는 경우 사용해야 하는 분석기 Elasticsearch가 때로 명확하지 않습니다. 자세한 내용은 [형식 알려진 문제 방지](https://www.elastic.co/guide/en/elasticsearch/guide/current/mapping.html#_avoiding_type_gotchas)를 참조하세요.

- 여러 형식을 보유하는 인덱스의 분할된 데이터베이스는 단일 형식을 포함하는 인덱스에 대한 분할된 데이터베이스보다 큽니다. 분할된 데이터베이스가 클수록 쿼리를 수행할 때 데이터를 필터링하는 Elasticsearch에서 많은 노력이 필요합니다

- 형식에 대한 데이터 볼륨 간에 심각한 불일치가 있으면 이 데이터를 검색하는 검색의 효율을 줄이는 여러 분할된 데이터베이스에 한 형식에 대한 정보가 드물게 배포될 수 있습니다.

![](./media/guidance-elasticsearch/query-performance1.png)

***그림 1. 형식 간에 인덱스 공유의 효과***

그림 1에서는 이 시나리오를 보여줍니다. 다이어그램의 위쪽에서 동일한 인덱스는 형식 A와 B 종류의 문서에서 공유됩니다.형식 B 보다 A인 문서가 많습니다.형식 A를 검색하면 네 개의 분할된 데이터베이스를 모두 쿼리하는 작업을 포함합니다. 각 형식에 대한 별도 인덱스를 만든 경우 다이어그램의 아래쪽은 효과를 보여줍니다. 이 경우에 형식 A에 대한 검색은 두 개의 분할된 데이터베이스에 액세스가 필요합니다.

작은 분할된 데이터베이스는 크게 분할된 데이터베이스보다 균등하게 분산될 수 있으며 이는 Elasticsearch가 노드 간에 부하를 고르게 분산할 수 있도록 합니다.

다른 형식에는 다른 보존 기간이 있을 수 있습니다. 활성 데이터로 분할된 데이터베이스를 공유하는 이전 데이터를 보관하기가 어려울 수 있습니다.

그러나 다음과 같은 경우 상황에 따라 형식 전체에서 인덱스를 공유하는 것이 효율적일 수 있습니다.

- 정기적으로 동일한 인덱스에 저장된 범위 형식을 검색합니다.

- 형식에는 각각 적은 수의 문서가 있으며 이 경우에 각 형식에 대한 분할된 데이터베이스의 별도 집합을 유지하는 작업은 상당한 오버 헤드될 수 있습니다.

### 인덱스 형식 최적화

Elasticsearch 인덱스는 이를 채우는 데 사용된 원래 JSON 문서의 복사본을 포함합니다. 이 정보는 각 인덱싱된 항목의 [*\_source*](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-source-field.html#mapping-source-field) 필드에 보유됩니다. 이 데이터를 검색할 수 없지만 기본적으로 *가져오기* 및 *검색* 요청에서 반환됩니다. 그러나 이 필드는 오버 헤드가 발생하며 저장소를 채우고 이는 분할된 데이터베이스를 더 크게 만들며 수행된 I/O의 볼륨을 증가시킵니다. 형식별로 *\_source* 필드를 사용하지 않도록 설정할 수 있습니다.

```http
PUT my_index
{
    "mappings": {
		"my_type": {
			"_source": {
				"enabled": false
			}
		}
	}
}
```

또한 이 필드를 사용하지 않도록 설정하면 다음 작업을 수행하는 기능을 제거합니다.

- *업데이트* API를 사용하여 인덱스의 데이터를 업데이트합니다.
- 강조 표시된 데이터를 반환하는 검색을 수행합니다.
- Elasticsearch 인덱스에서 직접 다른 인덱스로 다시 인덱싱합니다.
- 매핑 또는 분석 설정을 변경합니다.
- 원본 문서를 확인하여 쿼리를 디버깅합니다.
 
### 데이터 다시 인덱싱

인덱스에 사용할 수 있는 분할된 데이터베이스의 수는 궁극적으로 인덱스의 용량을 결정합니다. 필요한 분할된 데이터베이스를 초기(및 정보에 근거한)에 추측하는 데 시간이 걸리지만 항상 문서를 다시 인덱싱하도록 고려해야 합니다. 대부분의 경우에서 다시 인덱싱하는 작업은 데이터 증가할 때 의도된 태스크입니다. 검색 최적화를 위해 우선 많은 수의 분할된 데이터베이스를 인덱스에 할당하지 않으로 하지만 데이터의 볼륨이 확장되면 새롭게 분할된 데이터베이스를 할당합니다. 다른 경우에 데이터 볼륨 증가에 대한 예측이 정확하지 않다는 것을 간단히 입증하면 다시 인덱싱하는 작업은 더 임시로 수행되어야 합니다.

> [AZURE.NOTE] 다시 인덱싱하는 작업은 빠르게 노화하는 데이터에 필요하지 않을 수 있습니다. 이 경우에 응용 프로그램은 각 기간에 대한 새 인덱스를 만들 수 있습니다. 예제가 성능 로그를 포함하거나 매일 새로 고침 인덱스에 저장할 수 있는 데이터를 감사합니다.

다시 인덱싱하는 작업은 기존 인덱스의 데이터에서 새 인덱스를 만들고 이전 인덱스를 제거하는 작업을 효율적으로 포함합니다. 인덱스가 큰 경우 이 프로세스는 시간이 걸리고 이 기간 동안 데이터를 검색 가능하도록 해야 할 수 있습니다. 이러한 이유로 [각 인덱스에 대한 별칭](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-aliases.html)을 만들어야 하며 쿼리는 이러한 별칭을 통해 데이터를 검색해야 합니다. 인덱싱을 다시 수행하는 동안 이전 인덱스를 가리키는 별칭을 유지한 다음 다시 인덱싱을 완료하면 새 인덱스를 참조하도록 전환합니다. 또한 이 방법은 매일 새 인덱스를 만드는 시간 기반 데이터에 액세스하는 데 도움이 됩니다. 현재 데이터에 액세스하려면 데이터가 만들어질 때 새 인덱스를 롤오버하는 별칭을 사용합니다.

### 매핑 관리

Elasticsearch는 매핑을 사용하여 문서에서 각 필드에 발생하는 데이터를 해석하는 방법을 알아봅니다. 각 형식에는 효과적으로 해당 형식에 대한 스키마를 정의하는 고유한 매핑이 있습니다. Elasticsearch는 이 정보를 사용하여 어떤 형식의 문서에 각 필드에 대한 반전된 인덱스를 생성합니다. 문서에서 각 필드에는 데이터 형식(예: *문자열*, *날짜* 또는 *긴*) 및 값이 있습니다. 인덱스를 처음 만들 때 인덱스에 대한 매핑을 지정하거나 형식에 새 문서를 추가할 때 Elasticsearch로 유추할 수 있습니다. 그러나 다음 사항을 고려합니다.

- 동적으로 생성된 매핑은 문서를 인덱스에 추가할 경우 필드의 해석 방법에 따라 오류를 발생시킬 수 있습니다. 예를 들어 문서 1은 숫자를 보유하고 Elasticsearch를 발생시키는 필드 A를 포함하여 이 필드를 *길게* 지정하는 매핑을 추가합니다. 다음 문서가 숫자가 아닌 데이터를 포함하는 필드 A에 추가되면 실패합니다. 이 경우에 필드 A는 아마도 첫 번째 문서를 추가할 때 문자열로 해석했어야 합니다. 인덱스를 만들 때이 매핑 지정은 이러한 문제를 방지하는 데 도움을 줄 수 있습니다.

- 이처럼 아주 큰 매핑을 생성하지 않도록 문서를 디자인하면 검색을 수행하는 경우 상당한 오버헤드를 추가하고 많은 양의 메모리를 소비하며 쿼리에 데이터를 찾지 못하는 오류를 일으킬 수 있습니다. 동일한 형식을 공유하는 문서에서 필드에 대한 일관성 있는 명명 규칙을 적용합니다. 예를 들어 다른 문서에서 "first\_name", "FirstName", and "forename"과 같은 필드 이름을 사용하지 않고 각 문서에 동일한 필드 이름을 사용합니다. 또한 값을 키로 사용하려 하지 마세요(열 제품군 데이터베이스에서 일반적인 접근 방식이지만 Elasticsearch에 비효율성 및 실패를 발생시킬 수 있음). 자세한 내용은[매핑 급증](https://www.elastic.co/blog/found-crash-elasticsearch#mapping-explosion)을 참조하세요.

- *not\_analyzed*를 사용하여 적절한 위치에서 토큰화를 방지합니다. 예를 들어 문서가 "ABC-DEF" 값을 보유하는 *데이터*라는 문자열 필드를 포함하는 경우 값이 다음과 같이 일치하는 모든 문서를 검색해 볼 수 있습니다.

```http
GET /myindex/mydata/_search
{
	"query" : {
		"filtered" : {
			"filter" : {
				"term" : {
					"data" : "ABC-DEF"
				}
			}
		}
	}
}
```

그러나 이 검색은 인덱싱될 때 ABC-DEF 문자열을 토큰화하는 방식으로 인해 예상된 결과를 반환하지 못합니다. ABC와 DEF라는 두 개의 토큰을 하이픈으로 효과적으로 나눕니다. 이 기능은 전체 텍스트 검색을 지원하도록 디자인되었지만 문자열을 단일 원자 항목으로 해석하려는 경우 문서를 인덱스에 추가할 때 토큰화를 사용하지 않도록 해야 합니다. 이와 같은 매핑을 사용할 수 있습니다.

```http
PUT /myindex
{
	"mappings" : {
		"mydata" : {
			"properties" : {
				"data" : {
					"type" : "string",
					"index" : "not_analyzed"
				}
			}
		}
	}
}
```

자세한 내용은 [정확한 값 찾기](https://www.elastic.co/guide/en/elasticsearch/guide/current/_finding_exact_values.html#_term_filter_with_text)를 참조하세요.

### Doc 값 사용

많은 쿼리 및 집계는 검색 작업의 일환으로 정렬되어 있어야 합니다. 정렬 문서 목록에 하나 이상의 단어를 매핑할 수 있는 기능이 필요합니다. 이 프로세스를 지원하기 위해 Elasticsearch는 메모리에 정렬 키로 사용된 필드에 대한 값을 모두 로드할 수 있습니다. 이 정보는 *fielddata*라고 합니다. 메모리의 캐싱 필드 데이터가 I/O가 감소시키면 디스크에서 동일한 데이터를 반복적으로 읽는 것보다 빠를 수 있습니다. 그러나 필드에 높은 카디널리티가 있는 경우 필드 데이터를 메모리에 저장하는 작업이 힙 공간을 많이 사용할 수 있으며 다른 동시 작업을 수행하는 기능에 영향을 미치거나 Elasticsearch에 실패를 유발하는 저장소를 남용할 수 있습니다.

대체 방법으로 Elasticsearch도 *doc 값*을 지원합니다. doc 값은 디스크를 저장하며 데이터가 인덱스에 저장될 때 만들어진다는 점을 제외하고 메모리 내 필드 데이터의 항목과 비슷합니다(필드 데이터는 쿼리를 수행할 때 동적으로 생성됨). Doc 값은 힙 공간을 사용하지 않고 많은 고유한 값을 포함할 수 있는 필드에 데이터를 정렬 또는 집계하는 쿼리에 유용합니다. 또한 힙에 압력이 감소하면 디스크에서 데이터를 검색하고 메모리에서 읽는 작업 간의 성능 차이를 오프셋하는 데 도움이 될 수 있습니다. 가비지 수집은 발생할 가능성이 줄고 메모리를 활용하는 다른 동시 작업은 영향을 받을 가능성이 적습니다.

다음 예제에서와 같이 *doc\_values* 특성을 사용하여 인덱스에서 속성별로 doc 값을 사용하거나 사용하지 않도록 설정합니다.

```http
PUT /myindex
{
	"mappings" : {
		"mydata" : {
			"properties" : {
				"data" : {
					...
				"doc_values": true
				}
			}
		}
	}
}
```

> [AZURE.NOTE] doc 값은 기본적으로 Elasticsearch 버전 2.0.0 이상으로 활성화됩니다.

doc 값을 사용하는 경우 사용자 고유의 데이터 및 쿼리 시나리오에 특정될 가능성이 매우 높습니다. 따라서 유용성을 설정하는 성능 테스트를 수행할 준비를 합니다. 또한 doc 값이 분석된 문자열 필드로 작동하지 않는 점에 유의해야 합니다. 자세한 내용은 [Doc 값](https://www.elastic.co/guide/en/elasticsearch/guide/current/doc-values.html#doc-values)을 참조하세요.

### 클라이언트 노드 사용

모든 쿼리는 먼저 요청을 수신하는 노드에 의해 처리됩니다. 이 노드는 쿼리된 인덱스에 대한 분할된 데이터베이스를 포함하는 다른 모든 노드에 추가 요청을 보낸 다음 응답을 반환하는 결과를 누적합니다. 쿼리가 데이터를 집계하거나 복잡한 계산을 수행하는 작업을 포함하는 경우 초기 노드는 적절한 처리를 수행합니다. 시스템이 상대적으로 적은 수의 복잡한 쿼리를 지원해야 하는 경우 데이터 노드의 부하를 완화하기 위해 클라이언트 노드의 풀을 만드는 것이 좋습니다. 반대로, 시스템이 많은 수의 간단한 쿼리를 처리하는 경우 이러한 요청 데이터 노드에 직접 제출하고 부하 분산 장치를 사용하여 요청을 균등하게 분산합니다.

### 복제본을 사용하여 쿼리 경합 축소

쿼리의 성능을 향상시키는 일반적인 전략은 각 인덱스의 여러 복제본을 만드는 것입니다. 복제본에서 데이터를 가져오면 데이터 검색 작업을 처리할 수 있습니다. 그러나 이 전략은 데이터 수집 작업의 성능에 심각하게 영향을 줄 수 있으므로 혼합된 워크로드를 포함하는 시나리오에서 주의하여 사용해야 합니다. 또한 복제본이 노드에 분산되고 동일한 인덱스의 일부인 기본 분할된 데이터베이스를 사용하여 리소스에서 서로 충돌하지 않는 경우 이 전략은 장점만 발휘됩니다. 인덱스에 대한 복제본의 수를 동적으로 늘리거나 줄일 수 있습니다.

### 분할된 데이터베이스 요청 캐시 사용

Elasticsearch는 메모리에서 분할된 데이터베이스의 쿼리에 의해 요청된 로컬 데이터를 캐시할 수 있습니다. 이를 통해 보다 신속하게 실행할 동일한 데이터에 액세스하는 쿼리를 사용할 수 있습니다. 데이터는 디스크 저장소가 아닌 메모리에서 검색할 수 있습니다. 분할된 데이터베이스를 새로 고치고 데이터가 변경된 경우 캐시의 데이터가 무효화됩니다. 새로 고침 주기는 인덱스의 *refresh\_interval* 설정 값에 의해 제어됩니다. 기본적으로 인덱스에 대한 분할된 데이터베이스 요청 캐시는 사용할 수 없지만 다음과 같이 사용할 수 있습니다.

```http
PUT /myindex/_settings
{
	"index.requests.cache.enable": true
}
```

분할된 데이터베이스 요청 캐시는 기록 또는 로깅 데이터와 같이 상대적으로 정적인 정보에 가장 적합합니다.

## 집계 및 검색 성능 테스트 및 분석
이 섹션에서는 다양한 클러스터 및 인덱스 구성에 대해 수행된 테스트에 대한 일련의 결과를 설명합니다. 각 테스트는 대량 삽입 작업을 수행하여 진행하는 테스트로 채워진 빈 인덱스를 시작합니다(각 작업은 1000개의 문서를 추가함). 동시에 특정 데이터를 검색하고 집계를 생성하도록 디자인된 많은 쿼리가 5초 간격으로 반복되었습니다. 테스트의 목적은 쿼리 성능이 데이터의 볼륨에 어떤 영향을 받는지 설정하는 것이었습니다.

인덱스의 각 문서는 동일한 스키마를 가집니다. 이 테이블에서는 스키마의 필드를 요약합니다.

 이름 | 형식 | 참고 사항 |
  ----------------------------- | ------------ | -------------------------------------------------------- |
 조직 | String | 테스트는 200개의 고유한 조직을 생성합니다. |
 CustomField1 - CustomField5 |String |빈 문자열로 설정되는 다섯 개의 문자열 필드입니다.|
 DateTimeRecievedUtc |Timestamp |문서 추가된 날짜 및 시간입니다.|
 호스트 |문자열 |이 필드는 빈 문자열로 설정됩니다.|
 HttpMethod |String |이 필드는 "게시", "가져오기", "배치" 값 중 하나로 설정됩니다.|
 HttpReferrer |String |이 필드는 빈 문자열로 설정됩니다.|
 HttpRequest |문자열 |이 필드는 10자에서 200자 사이인 임의의 텍스트로 채워집니다.|
 HttpUserAgent |문자열 |이 필드는 빈 문자열로 설정됩니다.|
 HttpVersion |문자열 |이 필드는 빈 문자열로 설정됩니다.|
 OrganizationName |문자열 |이 필드는 조직 필드와 동일한 값으로 설정됩니다.|
 SourceIp |IP |이 필드는 데이터의 "출처"를 나타내는 IP 주소를 포함합니다. |
 SourceIpAreaCode |Long |이 필드는 0으로 설정됩니다.|
 SourceIpAsnNr |문자열 |이 필드는 "AS#####"으로 설정됩니다.|
 SourceIpBase10 |Long |이 필드는 500으로 설정됩니다.|
 SourceIpCountryCode |문자열 |이 필드는 2자인 국가 코드를 포함합니다. |
 SourceIpCity |String |이 필드는 국가에서 도시를 식별하는 문자열을 포함합니다. |
 SourceIpLatitude |Double |이 필드는 임의의 값을 포함합니다.|
 SourceIpLongitude |Double |이 필드는 임의의 값을 포함합니다.|
 SourceIpMetroCode |Long |이 필드는 0으로 설정됩니다.|
 SourceIpPostalCode |문자열 |이 필드는 빈 문자열로 설정됩니다.|
 SourceLatLong |지역 지점 |이 필드는 임의의 지역 지점으로 설정됩니다.|
 SourcePort |문자열 |이 필드는 임의의 수인 문자열 표현으로 채워집니다.|
 TargetIp |IP |0\.0.100.100에서 255.9.100.100 범위에서 임의의 IP 주소로 채워집니다.|
 SourcedFrom |문자열 |이 필드는 문자열 "MonitoringCollector"로 설정됩니다.|
 TargetPort |String |이 필드는 임의의 수인 문자열 표현으로 채워집니다.|
 등급 |문자열 |이 필드는 임의로 선택한 20개의 다른 문자열 값 중 하나로 채워집니다.|
 UseHumanReadableDateTimes |Boolean |이 필드는 false로 설정됩니다.|

다음 쿼리는 테스트의 반복에서 각각 배치로 수행되었습니다(기울임꼴로 표시된 이름이 이 문서의 나머지 부분에서 이러한 쿼리를 참조하는 데 사용됨).

- 최근 15분 동안 각 *등급* 값을 가진 문서가 입력된 수는(*등급별 수*)?

- 최근 15분 동안 5분 간격으로 추가된 문서 수는(*시간에 따른 수*)?

- 최근 15분 동안 각 국가에 추가된 *등급*별 문서 수는(*국가별 횟수*)?

- 최근 15분 동안 문서에 자주 추가된 15개의 조직은(*상위 15개 조직*)?

- 최근 15분 동안 문서에 추가된 다른 조직은(*고유한 조직 수*)?

- 최근 15분 동안 추가된 문서 수는(*총 횟수*)?

- 최근 15분 동안 문서에 추가된 *SourceIp* 값은(*고유한 IP 수*)?

다음 변수의 효과를 이해하기 위해 테스트를 수행합니다.

- **디스크 유형**. 테스트는 표준 저장소(HDD)를 사용하는 D4 VM의 6개 노드 클러스터에서 수행되고 프리미엄 저장소(SSD)를 사용하는 DS4 VM의 6개 노드 클러스터에서 반복되었습니다.

- **컴퓨터 크기 - 크기 조정**. DS3 VM을 구성하는 6개 노드 클러스터(*작은* 클러스터로 지정됨)에서 테스트를 수행하고 DS4 VM의 클러스터(*중간* 클러스터)에서 반복하며 DS14 컴퓨터의 클러스터(*큰* 클러스터)에서 다시 반복했습니다. 다음 테이블은 각 VM SKU의 주요 특징을 요약합니다.

프로비전 |VM SKU |코어 수 |데이터 디스크 수 |RAM(GB)|
--------- |--------------- |----------------- |---------------------- |--------|
작음 |표준 DS3 |4 |8 |14 |
중간 |표준 DS4 |8 |16 |28 |
큼 |표준 DS14 |16 |32 |112 |

- **Doc 값** 우선 *doc\_values*를 *true*로 설정한 인덱스를 사용하여 테스트를 수행했습니다. *doc\_values*를 *false*로 설정하여 선택된 테스트를 반복했습니다.

## 성능 결과 – 디스크 유형

다음 테이블에서는 D4 VM의 6개 노드 클러스터(HDD 사용) 및 DS4 VM의 6개 노드 클러스터(SSD 사용)에서 테스트를 실행하여 수행된 작업의 응답 시간을 요약합니다. 두 클러스터 모두에서 Elasticsearch의 구성은 동일했습니다. 각 노드에서 16개의 디스크 간에 데이터를 분산했고 각 노드에는 JVM을 실행하는 Elasticsearch에 할당된 14GB RAM이 있습니다. 남은 메모리(14GB)는 운영 체제를 위해 남겨두었습니다. 각 테스트는 24시간 동안 실행되었습니다. 데이터 볼륨의 증가 효과가 명확해지고 시스템을 안정화할 수 있도록 이 기간을 선택했습니다.

 프로비전 |작업/쿼리 |평균 응답 시간(밀리초)|
 -----------|---------------------------- |--------------------------|
 D4 |수집 |978 |
 |등급별 수 |103 |
 |시간에 따른 수 |134 |
 |국가별 횟수 |199 |
 |상위 15개 조직 |137 |
 |고유한 조직 수 |139 |
 |고유한 IP 수 |510 |
 |총 횟수 |89 |
 DS4 |수집 |511 |
 |등급별 수 |187 |
 |시간에 따른 수 |411 |
 |국가별 횟수 |402 |
 |상위 15개 조직 |307 |
 |고유한 조직 수 |320 |
 |고유한 IP 수 |841 |
 |총 횟수 |236 |

언뜻 보기에 때때로 응답 시간이 두 배(이상)로 DS4 클러스터가 D4 클러스터보다 쿼리를 잘 수행하지 못하는 것처럼 보입니다. 하지만 전부를 알려주지는 않습니다. 다음 테이블에서는 각 클러스터에서 수행된 수집 작업의 수를 보여줍니다(각 작업이 1000개의 문서를 로드함).

 프로비전 | 수집 작업
 ----------|---------------------
 D4 | 264769              
 DS4 | 503157              

DS4 클러스터는 테스트하는 동안 D4 클러스터보다 거의 두 배 많은 데이터를 로드할 수 있었습니다. 따라서 각 작업에 대한 응답 시간을 분석하는 경우 각 쿼리가 스캔해야 하는 문서 수 및 반환되는 문서 수를 고려해야 합니다.

다음은 인덱스의 문서 볼륨이 지속적으로 성장하는 경우 동적 수치입니다. 수집 작업에서 동시에 수행되는 I/O의 양을 무시하는 경우 비교 그림을 제공하기 위해 503137을 264769(각 클러스터에서 수행된 수집 작업의 수)로 나눌 수 없으며 결과를 D4 클러스터에서 수행한 각 쿼리에 대한 평균 응답 시간으로 곱할 수 없습니다.

대신에 기록되는 데이터의 실제 크기를 측정하고 테스트의 진행에 따라 디스크에서 읽어야 합니다. JMeter 테스트 계획은 각 노드에 대한 이 정보를 캡처합니다. 요약된 결과는 다음과 같았습니다.

 프로비전 |각 작업에 의해 작성된/읽은 평균 바이트|
  --------- |--------------------------------------------|
 D4 |13471557 |
 DS4 |24643470 |

이러한 그림은 DS4 클러스터가 D4 클러스터의 1.8 배 정도로 I/O 비율을 유지할 수 있음을 보여줍니다. 디스크의 특성 외에도 다른 모든 리소스가 동일하다는 점을 볼 때 HDD 대신 SSD를 사용으로 인한 차이가 분명합니다.

이 결론을 증명하기 위해 다음과 같은 그래프는 시간에 따라 각 클러스터에서 수행된 I/O를 보여줍니다.

![](./media/guidance-elasticsearch/query-performance2.png)

D4 클러스터에 대한 그래프는 테스트의 첫 번째 절반 중에 특히 중요한 변형을 표시합니다. I/O 속도를 줄이기 위한 제한 때문일 가능성이 있었습니다. 테스트의 초기 단계에서 쿼리는 데이터를 거의 분석하지 않기에 신속하게 실행할 수 있습니다. 따라서 D4 클러스터의 디스크는 IOPS 용량에 근접하게 작동될 수 있지만 각 I/O 작업은 많은 데이터를 반환할 수 없을 것입니다. DS4 클러스터는 높은 IOPS 속도를 지원할 수 있고 동일한 수준의 제한 문제가 발생하지 않습니다. I/O 속도는 일반적입니다.

이 이론을 설명하기 위해 그래프의 다음 쌍은 시간에 따라 디스크 I/O에 의해 차단된 CPU를 보여줍니다(그래프에 표시된 디스크 대기 시간은 CPU가 I/O를 기다리는 데 사용한 시간의 비율임).

![](./media/guidance-elasticsearch/query-performance3.png)

이 테스트 시나리오에서 CPU를 차단하기 위해 I/O 작업에 널리 사용되는 다음 두 가지 원인을 이해해야 합니다.

- I/O 하위 시스템은 디스크 간에 데이터를 읽거나 쓸 수 있습니다.

- I/O 하위 시스템은 호스트 환경에 의해 제한될 수 있습니다. 표준 저장소에서 지원하는 Azure 디스크의 최대 처리량은 500 IOPS입니다. 반면 프리미엄 저장소에서 지원하는 최대 처리량은 5000 IOPS입니다.

D4 클러스터의 경우 테스트의 상반기 동안 I/O를 기다리는 데 소요된 시간은 I/O 속도를 보여주는 그래프와 반전된 방식과 밀접한 상관 관계가 있습니다. 낮은 I/O의 기간은 CPU의 사용이 차단된 상당한 시간에 해당합니다.

I/O이 제한되고 있음을 나타냅니다. 상황이 변경한 클러스터에 더 많은 데이터가 추가되면 I/O 대기 시간에서 테스트 최대치의 두 번째 절반이 I/O 처리량의 최고에 해당합니다. 이 시점에서 실제 I/O를 수행하는 동안 CPU가 차단됩니다. 다시 DS4 클러스터를 사용하면 I/O 대기 시간이 훨씬 고르고 각각의 최대치는 최저치보다 I/O 성능의 동등한 최대치와 일치합니다. 이는 제한이 발생하지 않거나 거의 발생하지 않는다는 것을 의미합니다.

한 가지 고려해야 할 기타 요소가 있습니다. 테스트하는 동안 D4 클러스터는 10584 수집 오류 및 21 쿼리 오류를 생성했습니다. DS4 클러스터에서 테스트는 오류를 생성하지 않았습니다.

## 성능 결과 – 크기 조정

아래 테이블에서는 중간(DS4) 및 큰(DS14) 클러스터에서 실행한 테스트의 결과를 요약합니다. 각 VM은 데이터를 저장하는 데 SSD를 사용했습니다. 각 테스트는 24시간 동안 실행되었습니다.

| 프로비전 |작업/쿼리 |요청 수 |평균 응답 시간(밀리초)|
|  -------------- |---------------------------- |-------------------- |--------------------------|
| 중간(DS4) |수집 |503157 |511 |
| |등급별 수 |6958 |187 |
| |시간에 따른 수 |6958 |411 |
| |국가별 횟수 |6958 |402 |
| |상위 15개 조직 |6958 |307 |
| |고유한 조직 수 |6956 |320 |
| |고유한 IP 수 |6955 |841 |
| |총 횟수 |6958 |236 |
| 큼(DS14) |수집 |502714 |511 |
| |등급별 수 |7041 |201 |
| |시간에 따른 수 |7040 |298 |
| |국가별 횟수 |7039 |363 |
| |상위 15개 조직 |7038 |244 |
| |고유한 조직 수 |7037 |283 |
| |고유한 IP 수 |7037 |681 |
| |총 횟수 |7038 |200 |

<!-- 
DISCUSSION POINTS:

Similar volume of data ingested – same disk configuration for each cluster, and ingestion rate is 
constrained by I/O performance?

Average response time for queries decreases with SKU.

Show CPU graphs

Show memory utilization – more data cached, fewer GCs, etc.

-->

<!--
To isolate the effects of the ingestion operations and show how query performance varies as nodes scale up, a second set of tests was performed using the same nodes. The ingestion part of the test was omitted, and the index on each node was pre-populated with 100 million documents. An amended set of queries was performed; the time element limiting documents to those added in the last 15 minutes was removed as the data was now static. The tests ran for 90 minutes; there is less need to allow the system to stabilize due to the fixed amount of data. The following table summarizes the results obtained on each cluster:

> [AZURE.NOTE] The amended version of the test that omits the data ingestion process and that uses a set of indexes containing 100 million documents is referred to as the *query-only* test in the remainder of this document. You should not compare the performance of the queries in this test with that of the tests that perform ingestion and query operations because the queries have been modified and the volume of documents involved is different.

 |Cluster        |Operation/Query              |Number of Requests   Average Response Time (ms)
 | --------------| ----------------------------| -------------------- ----------------------------
 | Small (DS3)   | Count By Rating             |                      
 |               | Count Over Time             |                      
 |               | Hits By Country             |                      
 |               | Top 15 Organizations        |                      
 |               | Unique Count Organizations  |                      
 |               | Unique IP Count             |                      
 |               | Total Hits Counts           |                      
 | Medium (DS4)  | Count By Rating             |                      
 |               | Count Over Time             |                      
 |               | Hits By Country             |                      
 |               | Top 15 Organizations        |                      
 |               | Unique Count Organizations  |                      
 |               | Unique IP Count             |                      
 |               | Total Hits Counts           |                      
 | Large (DS14)  | Count By Rating             |                      
 |               | Count Over Time             |                      
 |               | Hits By Country             |                      
 |               | Top 15 Organizations        |                      
 |               | Unique Count Organizations  |                      
 |               | Unique IP Count             |                      
 |               | Total Hits Counts           |                      

### Performance Results – Scaling Out
-->


<!--
To show the system scales out with the number of nodes, tests were run using DS14 clusters comprising 1, 3, and 6 nodes. This time, only the query-only test was performed, using 100 million documents and running for 90 minutes:

> [AZURE.NOTE] For detailed information on how scaling out can affect the behavior of data ingestion operations, see the document [Maximizing Data Ingestion Performance with Elasticsearch on Azure](https://github.com/mspnp/azure-guidance/blob/master/Elasticsearch-Data-Ingestion-Performance.md).

|  Cluster   |Operation/Query              |Number of Requests   |Average Response Time (ms)
|  --------- |---------------------------- |-------------------- |----------------------------
|  1 Node    |Count By Rating              |                     |
|            |Count Over Time              |                     |
|            |Hits By Country              |                     |
|            |Top 15 Organizations         |                     |
|            |Unique Count Organizations   |                     |
|            |Unique IP Count              |                     |
|            |Total Hits Counts            |                     |
|  3 Nodes   |Count By Rating              |                     |
|            |Count Over Time              |                     |
|            |Hits By Country              |                     |
|            |Top 15 Organizations         |                     |
|            |Unique Count Organizations   |                     |
|            |Unique IP Count              |                     |
|            |Total Hits Counts            |                     |
|  6 Nodes   |Count By Rating              |                     |
|            |Count Over Time              |                     |
|            |Hits By Country              |                     |
|            |Top 15 Organizations         |                     |
|            |Unique Count Organizations   |                     |
|            |Unique IP Count              |                     |
|            |Total Hits Counts            |                     |
-->

<!--
### Performance Results – Number of Replicas

The ingestion and query tests were run against an index with a single replica. The tests were repeated on the 6-node DS4 and DS14 clusters using an index configured with two replicas. All tests ran for 24 hours. The table below shows the comparative results for one and two replicas:

|  Cluster|  Operation/Query            | response time 1 Replica (ms)| response time 2 Replicas (ms) | % Difference in Response Time
|  -------| ----------------------------| ----------------------------|----------- -------------------|--------------------- --------
|  DS4    |   Ingestion                 |   511                       |               655             |                          +28%
|         |   Count By Rating           |   187                       |               168             |                          -10%
|         |   Count Over Time           |   411                       |               309             |                          -25%
|         |   Hits By Country           |   402                       |               562             |                          +40%
|         |   Top 15 Organizations      |   307                       |               366             |                          +19%
|         |   Unique Count Organizations|   320                       |               378             |                          +18%
|         |   Unique IP Count           |   841                       |               987             |                          +17%
|         |   Total Hits Counts         |   236                       |               236             |                          +0%
|  DS14   |   Ingestion                 |   511                       |               618             |                          +21%
|         |   Count By Rating           |   201                       |               275             |                          +37%
|         |   Count Over Time           |   298                       |               466             |                          +56%
|         |   Hits By Country           |   363                       |               529             |                          +46%
|         |   Top 15 Organizations      |   244                       |               407             |                          +67%
|         |   Unique Count Organizations|   283                       |               403             |                          +42%
|         |   Unique IP Count           |   681                       |               823             |                          +21%
|         |   Total Hits Counts         |   200                       |               221             |                          +11%


NEED ## OF DOCUMENTS RETURNED TO JUSTIFY THIS DATA, OTHERWISE PERF FOR 2 REPLICAS LOOKS OFF!

PRESENT QUERY-ONLY TEST RESULTS TO SHOW BETTER RESULTS
-->

## 성능 결과 – Doc 값

수집 및 쿼리 테스트는 doc 값으로 수행되었으며 Elasticsearch가 디스크에 필드를 정렬하는 데 사용되는 데이터를 저장하도록 합니다. 테스트는 비활성화된 doc 값으로 반복되었으므로 Elasticsearch가 필드 데이터를 동적으로 생성하고 메모리에 캐시했습니다. 24시간 동안 모든 테스트를 실행했습니다.
 
 아래 테이블에서는 D4, DS4, 및 DS14 VM을 사용하여 빌드된 6개 노드 클러스터에 대해 실행된 테스트에 대한 응답 시간을 비교합니다.

| 프로비전 |작업/쿼리 |사용된 doc 값(ms) | 사용 안 한 doc 값(ms) | % 차이점 |
|  --------- |---------------------------| -----------------------|--------------------------  |--------------------|
| D4 |수집 | 978 | 835 | -15% |
| |등급별 수 | 103 | 132 | +28% |
| |시간에 따른 수 | 134 | 189 | +41% |
| |국가별 횟수 | 199 | 259 | +30% |
| |상위 15개 조직 | 137 | 184 | +34% |
| |고유한 조직 수 | 139 | 197 | +42% |
| |고유한 IP 수 | 510 | 604 | +18% |
| |총 횟수 | 89 | 134 | +51% |
| DS4 |수집 | 511 | 581 | +14% |
| |등급별 수 | 187 | 190 | +2% |
| |시간에 따른 수 | 411 | 409 | -0.5% |
| |국가별 횟수 | 402 | 414 | +3% |
| |상위 15개 조직 | 307 | 284 | -7% |
| |고유한 조직 수 | 320 | 313 | -2% |
| |고유한 IP 수 | 841 | 955 | +14% |
| |총 횟수 | 236 | 281 | +19% |
| DS14 |수집 | 511 | 571 | +12% |
| |등급별 수 | 201 | 232 | +15% |
| |시간에 따른 수 | 298 | 341 | +14% |
| |국가별 횟수 | 363 | 457 | +26% |
| |상위 15개 조직 | 244 | 338 | +39% |
| |고유한 조직 수 | 283 | 350 | +24% |
| |고유한 IP 수 | 681 | 909 | +33% |
| |총 횟수 | 200 | 245 | +23% |


<!--
The next table compares the number of ingestion operations performed by the tests:

  Cluster   Ingestion Operations – Doc Values Enabled   ## Ingestion Operations – Doc Values Disabled   % Difference in ## Ingestion Operations
  --------- ---------------------------------------------- ----------------------------------------------- -----------------------------------------
  D4        264769                                         408690                                          +54%
  DS4       503137                                         578237                                          +15%
  DS14      502714                                         586472                                          +17%

The improved ingestion rates occur with doc values disabled as less data is being written to disk as documents are inserted. The improved performance is especially noticeable with the D4 VM using HDDs to store data. In this case, the response time for ingestion operations also decreased by 15% (see the first table in this section). This could be due to the reduced pressure on the HDDs which were likely running close to their IOPS limits in the test with doc values enabled; see the [Disk Type](#performance-results-disk-type) test for more information. The following graph compares the I/O performance of the D4 VMs with doc values enabled (values held on disk) and doc values disabled (values held in memory):

![](./media/guidance-elasticsearch/query-performance4.png)

In contrast, the ingestion figures for the VMs using SSDs show a small increase in the number of documents but also an increase in the response time of the ingestion operations. With one or two small exceptions, the query response times were also worse. The SSDs are less likely to be running close to their IOPS limits with doc values enabled, so changes in performance are more likely due to increased processing activity and the overhead of managing the JVM heap. This is evident by comparing the CPU utilization with doc values enabled and disabled. The next graph highlights this data for the DS4 cluster, where most of the CPU utilization moves from the 30%-40% band with doc values enabled, to the 40%-50% band with doc values disabled (the DS14 cluster showed a similar trend):

![](./media/guidance-elasticsearch/query-performance5.png)

To isolate the effects that doc values on query performance from data ingestion, a pair of query-only tests were performed for each cluster as follows:

In the first test, the Elasticsearch index was created with doc values enabled, the index was pre-populated with 100 million documents, and then modified set of queries used by the replica tests were performed repeatedly for 90 minutes while performance statistics were gathered.

In the second test, the Elasticsearch index was created with doc values disabled, populated, and then subjected to the same query load for the same period as the first test.
-->

<!--
## Appendix: The Query and Aggregation Performance Test

This appendix describes the performance test performed against the Elasticsearch cluster. The tests were run by using JMeter running on a separate set of VMs. Details the configuration of the test environment are described in [Creating a Performance Testing Environment for Elasticsearch on Azure][]. To perform your own testing, you can create your own JMeter test plan manually following the guidance in this appendix, or you can use the automated test scripts available separately. See [Running the Automated Elasticsearch Performance Tests][] for further information.

The data query workload performed the set of queries described below while performing a large-scale upload of documents at the same time (the data was uploaded by using a JUnit test, following the same approach for the data ingestion tests described in the document Maximizing Data Ingestion Performance with Elasticsearch on Azure.) The purpose of this workload was to simulate a production environment where new data is constantly being added while searches are performed. The queries were structured to retrieve only the most recent data from documents added in the last 15 minutes.

Each document was stored in a single index named *sample*, and had the type *ctip*. You can use the following HTTP request to create the index. The *number\_of\_replicas* and *number\_of\_shards* settings varied from the values shown below in many of the tests. Additionally, for the tests that used fielddata rather than doc values, each property was annotated with the attribute *"doc\_values" : false*.

**Important**. The index was dropped and recreated prior to each test run.

```http
PUT /sample
{  
    "settings" : {
        "number_of_replicas": 1,
        "refresh_interval": "30s",
        "number_of_shards": "5",
        "index.translog.durability": "async"    
    },
    "ctip": {
        "mappings": {
            "event": {
                "_all": {
                    "enabled": false
                },
                "_timestamp": {
                    "enabled": true,
                    "store": true,
                    "format": "date_time"
                },
                "properties": {
                    "Organization": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField1": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField2": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField3": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField4": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField5": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "DateTimeReceivedUtc": {
                        "type": "date",
                        "format": "dateOptionalTime"
                    },
                    "Host": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpMethod": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpReferrer": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpRequest": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpUserAgent": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpVersion": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "OrganizationName": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIp": {
                        "type": "ip"
                    },
                    "SourceIpAreaCode": {
                        "type": "long"
                    },
                    "SourceIpAsnNr": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpBase10": {
                        "type": "long"
                    },
                    "SourceIpCity": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpCountryCode": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpLatitude": {
                        "type": "double"
                    },
                    "SourceIpLongitude": {
                        "type": "double"
                    },
                    "SourceIpMetroCode": {
                        "type": "long"
                    },
                    "SourceIpPostalCode": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpRegion": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceLatLong": {
                        "type": "geo_point",
                        "doc_values": true,
                        "lat_lon": true,
                        "geohash": true
                    },
                    "SourcePort": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourcedFrom": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "TargetIp": {
                        "type": "ip"
                    },
                    "TargetPort": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "Rating": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "UseHumanReadableDateTimes": {
                        "type": "boolean"
                    }
                }
            }
        }
    }
}

```

The following queries were performed by the test:

- How many documents with each *Rating* value have been entered in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "range": {
            "DateTimeReceivedUtc": {
              "gte": "now-15m",
              "lte": "now"
            }
          }
        }
      ],
      "must_not": [],
      "should": []
    }
  },
  "from": 0,
  "size": 0,
  "aggs": {
    "2": {
      "terms": {
        "field": "Rating",
        "size": 5,
        "order": {
          "_count": "desc"
        }
      }
    }
  }
}

```

- How many documents have been added in each 5 minute interval during the last 15 minutes?

```http
GET /sample/ctip/_search 
{
  "query": {
    "bool": {
      "must": [
        {
          "range": {
            "DateTimeReceivedUtc": {
              "gte": "now-15m",
              "lte": "now"
            }
          }
        }
      ],
      "must_not": [],
      "should": []
    }
  },
  "from": 0,
  "size": 0,
  "sort": [],
  "aggs": {
    "2": {
      "date_histogram": {
        "field": "DateTimeReceivedUtc",
        "interval": "5m",
        "time_zone": "America/Los_Angeles",
        "min_doc_count": 1,
        "extended_bounds": {
          "min": "now-15m",
          "max": "now"
        }
      }
    }
  }
}

```

- How many documents of each *Rating* value have been added for each country in the last 15 minutes?

```http
GET /sample/ctip/_search 
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "terms": {
        "field": "Rating",
        "size": 5,
        "order": {
          "_count": "desc"
        }
      },
      "aggs": {
        "3": {
          "terms": {
            "field": "SourceIpCountryCode",
            "size": 15,
            "order": {
              "_count": "desc"
            }
          }
        }
      }
    }
  }
}

```

- Which 15 organizations occur most frequently in documents added in the last 15 minutes?

``` http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "terms": {
        "field": "Organization",
        "size": 15,
        "order": {
          "_count": "desc"
        }
      }
    }
  }
}

```

- How many different organizations occur in documents added in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "cardinality": {
        "field": "Organization"
      }
    }
  }
}

```

- How many documents have been added in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "analyze_wildcard": true,
                  "query": "*"
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {}
}

```

- How many different *SourceIp* values occur in documents added in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "cardinality": {
        "field": "SourceIp"
      }
    }
  }
} 

```

[Running the Automated Elasticsearch Performance Tests]: guidance-elasticsearch-running-automated-performance-tests.md
[Creating a Performance Testing Environment for Elasticsearch on Azure]: guidance-elasticsearch-creating-performance-testing-environment.md
-->

<!---HONumber=AcomDC_0224_2016-->