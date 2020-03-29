---
title: 인덱서 오류 및 경고
titleSuffix: Azure Cognitive Search
description: 이 문서에서는 Azure Cognitive Search에서 AI 보강 중에 발생할 수 있는 일반적인 오류 및 경고에 대한 정보와 솔루션을 제공합니다.
manager: nitinme
author: amotley
ms.author: abmotley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 72bf08dce36d857c1fe91bbe9806336dfa185f7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671984"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>Azure 인지 검색에서 일반적인 인덱서 오류 및 경고 문제 해결

이 문서에서는 Azure Cognitive Search에서 인덱싱 및 AI 보강 중에 발생할 수 있는 일반적인 오류 및 경고에 대한 정보와 솔루션을 제공합니다.

오류 수가 ['maxFailedItems'를](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures)초과하면 인덱싱이 중지됩니다. 

인덱서가 이러한 오류를 무시하고 "실패한 문서"를 건너뛰도록 하려면 `maxFailedItems` `maxFailedItemsPerBatch` [여기에](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers)설명된 대로 업데이트하는 것이 좋습니다.

> [!NOTE]
> 문서 키와 함께 실패한 각 문서(사용 가능한 경우)는 인덱서 실행 상태에 오류로 표시됩니다. [인덱스 API를](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) 사용하여 인덱서가 오류를 허용하도록 설정한 경우 나중에 문서를 수동으로 업로드할 수 있습니다.

이 문서의 오류 정보는 오류를 해결하는 데 도움이 되므로 인덱싱을 계속할 수 있습니다.

경고는 인덱싱을 중지하지 않지만 예기치 않은 결과를 초래할 수 있는 조건을 나타냅니다. 조치를 취했는지 여부는 데이터와 시나리오에 따라 달라집니다.

API 버전부터 `2019-05-06`항목 수준 인덱서 오류 및 경고는 원인 및 다음 단계에 대한 명확성을 높이기 위해 구조화되어 있습니다. 여기에는 다음 속성이 포함됩니다.

| 속성 | Description | 예제 |
| --- | --- | --- |
| key | 오류 또는 경고의 영향을 받는 문서의 문서 ID입니다. | https:\//coromsearch.blob.core.windows.net/jfk-1k/docid-32112954.pdf |
| name | 오류 또는 경고가 발생한 위치를 설명하는 작업 이름입니다. 이것은 다음과 같은 구조에 의해 생성됩니다: [범주]. [하위 범주]. [리소스유형]을 입력합니다. [리소스 이름] | 문서추출.azureblob.myBlob컨테이너 이름 강화.WebApiSkill.mySkillName 프로젝션.검색인덱스.출력필드매핑.myOutputFieldName 프로젝션.SearchIndex.MergeOrUpload.myIndexName 프로젝션.지식스토어.테이블.마이테이블네임 |
| message | 오류 또는 경고에 대한 상위 수준 설명입니다. | Web Api 요청이 실패했기 때문에 기술을 실행할 수 없습니다. |
| 자세히 | 사용자 지정 기술을 실행하는 데 실패한 경우 WebApi 응답과 같이 문제를 진단하는 데 도움이 될 수 있는 추가 세부 정보입니다. | `link-cryptonyms-list - Error processing the request record : System.ArgumentNullException: Value cannot be null. Parameter name: source at System.Linq.Enumerable.All[TSource](IEnumerable`1 소스,`2 predicate) at Microsoft.CognitiveSearch.WebApiSkills.JfkWebApiSkills.` 펑 ... 스택 추적의 나머지 ... |
| 문서링크 | 문제를 디버깅하고 해결하기 위한 자세한 정보가 있는 관련 문서에 대한 링크입니다. 이 링크는 종종 이 페이지의 아래 섹션 중 하나를 가리킵니다. | https://go.microsoft.com/fwlink/?linkid=2106475 |

<a name="could-not-read-document"/>

## <a name="error-could-not-read-document"></a>오류: 문서를 읽을 수 없습니다.

인덱서가 데이터 원본에서 문서를 읽을 수 없습니다. 이 경우 다음과 같은 경우 발생할 수 있습니다.

| 이유 | 세부 정보/예 | 해결 방법 |
| --- | --- | --- |
| 여러 문서에서 일관되지 않은 필드 유형 | "값 유형이 열 형식과 일치하지 않습니다. 작성자 열에 저장할 `'{47.6,-122.1}'` 수 없습니다.  예상 유형은 JArray입니다."  "데이터 형식 nvarchar를 부동으로 변환하는 오류입니다."  "nvarchar 값 '12개월'을 데이터 형식 int로 변환할 때 변환에 실패했습니다."  "식을(를) 데이터 형식 int(으)로 변환하는 중 산술 오버플로 오류가 발생했습니다." | 각 필드의 형식이 여러 문서에서 동일한지 확인합니다. 예를 들어 첫 번째 `'startTime'` 문서 필드가 DateTime이고 두 번째 문서에서 문자열인 경우 이 오류가 발생합니다. |
| 데이터 원본의 기본 서비스에서 오류가 발생하면 | (코스모스 DB 출신)`{"Errors":["Request rate is large"]}` | 저장소 인스턴스가 정상인지 확인합니다. 크기 조정/분할을 조정해야 할 수 있습니다. |
| 일시적인 문제 | 서버에서 결과를 받을 때 전송 수준 오류가 발생했습니다. (공급자: TCP 공급자, 오류: 0 - 원격 호스트에 의해 기존 연결이 강제로 닫혔습니다. | 예기치 않은 연결 문제가 있는 경우가 있습니다. 나중에 인덱서를 통해 문서를 다시 실행해 보십시오. |

<a name="could-not-extract-document-content"/>

## <a name="error-could-not-extract-content-or-metadata-from-your-document"></a>오류: 문서에서 콘텐츠 또는 메타데이터를 추출할 수 없습니다.
Blob 데이터 원본을 가진 인덱서가 문서에서 콘텐츠 또는 메타데이터를 추출할 수 없습니다(예: PDF 파일). 이 경우 다음과 같은 경우 발생할 수 있습니다.

| 이유 | 세부 정보/예 | 해결 방법 |
| --- | --- | --- |
| Blob이 크기 제한을 초과했습니다. | 문서바이트는 `'150441598'` 현재 서비스 계층의 `'134217728'` 문서 추출을 위한 최대 크기 바이트를 초과합니다. | [Blob 인덱싱 오류](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| Blob에 지원되지 않는 콘텐츠 유형이 있습니다. | 문서에 지원되지 않는 콘텐츠 유형이 있습니다.`'image/png'` | [Blob 인덱싱 오류](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| Blob이 암호화됩니다. | 문서를 처리할 수 없습니다- 암호화되거나 암호로 보호될 수 있습니다. | Blob [설정으로](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed)Blob을 건너뛸 수 있습니다. |
| 일시적인 문제 | "오류 처리 Blob: 요청이 중단되었습니다. "처리 중에 문서 시간이 시간 미각입니다." | 예기치 않은 연결 문제가 있는 경우가 있습니다. 나중에 인덱서를 통해 문서를 다시 실행해 보십시오. |

<a name="could-not-parse-document"/>

## <a name="error-could-not-parse-document"></a>오류: 문서를 구문 분석할 수 없습니다.
Indexer는 데이터 원본에서 문서를 읽었지만 문서 내용을 지정된 필드 매핑 스키마로 변환하는 데 문제가 있었습니다. 이 경우 다음과 같은 경우 발생할 수 있습니다.

| 이유 | 세부 정보/예 | 해결 방법 |
| --- | --- | --- |
| 문서 키가 없습니다. | 문서 키가 누락되거나 비어 있을 수 없습니다. | 모든 문서에 유효한 문서 키가 있는지 확인 |
| 문서 키가 잘못되었습니다. | 문서 키는 1024자보다 길 수 없습니다. | 유효성 검사 요구 사항에 맞게 문서 키를 수정합니다. |
| 필드에 필드 매핑을 적용할 수 없습니다. | 필드에 `'functionName'` `'fieldName'`매핑 함수를 적용할 수 없습니다. 배열은 null일 수 없습니다. 매개 변수 이름: 바이트 | 인덱서에 정의된 [필드 매핑을](search-indexer-field-mappings.md) 다시 확인하고 실패한 문서의 지정된 필드의 데이터와 비교합니다. 필드 매핑 또는 문서 데이터를 수정해야 할 수 있습니다. |
| 필드 값을 읽을 수 없습니다. | 인덱스에서 `'fieldName'` `'fieldIndex'`열 값을 읽을 수 없습니다. 서버에서 결과를 받을 때 전송 수준 오류가 발생했습니다. (공급자: TCP 공급자, 오류: 0 - 현재 현결이 원격 호스트에 의해 강제로 끊겼습니다.) | 이러한 오류는 일반적으로 데이터 원본의 기본 서비스와 예기치 않은 연결 문제로 인해 발생할 수 있습니다. 나중에 인덱서를 통해 문서를 다시 실행해 보십시오. |

<a name="could-not-execute-skill"/>

## <a name="error-could-not-execute-skill"></a>오류: 기술을 실행할 수 없습니다.
인덱서가 스킬 세트에서 스킬을 실행할 수 없습니다.

| 이유 | 세부 정보/예 | 해결 방법 |
| --- | --- | --- |
| 일시적인 연결 문제 | 일시적인 오류가 발생했습니다. 나중에 다시 시도하세요. | 예기치 않은 연결 문제가 있는 경우가 있습니다. 나중에 인덱서를 통해 문서를 다시 실행해 보십시오. |
| 잠재적인 제품 버그 | 예기치 않은 오류가 발생했습니다. | 이는 알 수 없는 오류 클래스를 나타내며 제품 버그가 있음을 의미할 수 있습니다. 도움을 받으려면 [지원 티켓을](https://ms.portal.azure.com/#create/Microsoft.Support) 제출하십시오. |
| 실행 중에 기술이 오류가 발생했습니다. | (병합 기술에서) 하나 이상의 오프셋 값이 잘못되어 구문 분석할 수 없습니다. 텍스트 끝에 항목이 삽입되었습니다. | 오류 메시지의 정보를 사용하여 문제를 해결합니다. 이러한 종류의 오류는 해결하려면 작업이 필요합니다. |

<a name="could-not-execute-skill-because-the-web-api-request-failed"/>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>오류: 웹 API 요청이 실패하여 기술을 실행할 수 없습니다.
웹 API 호출에 실패했기 때문에 기술 실행이 실패했습니다. 일반적으로 이 장애 클래스는 사용자 지정 기술을 사용할 때 발생하며, 이 경우 문제를 해결하기 위해 사용자 지정 코드를 디버깅해야 합니다. 대신 오류가 기본 제공 기술에서 발생한 경우 오류 메시지를 참조하여 문제를 해결하는 데 도움이 됩니다.

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"/>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>오류: 웹 API 기술 응답이 잘못되어 기술을 실행할 수 없습니다.
웹 API를 호출하면 잘못된 응답을 반환했기 때문에 기술 실행이 실패했습니다. 일반적으로 이 장애 클래스는 사용자 지정 기술을 사용할 때 발생하며, 이 경우 문제를 해결하기 위해 사용자 지정 코드를 디버깅해야 합니다. 대신 오류가 내장 된 기술에서 인 경우 지원을받을 [수있는 지원 티켓을](https://ms.portal.azure.com/#create/Microsoft.Support) 제출하십시오.

<a name="skill-did-not-execute-within-the-time-limit"/>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>오류: 제한 시간 내에 기술이 실행되지 않았습니다.
이 오류 메시지가 발생할 수 있는 두 가지 경우가 있으며 각 오류 메시지는 다르게 처리되어야 합니다. 이 오류를 반환한 기술에 따라 아래 지침을 따르십시오.

### <a name="built-in-cognitive-service-skills"></a>내장된 코그너티브 서비스 기술
언어 감지, 엔터티 인식 또는 OCR과 같은 많은 기본 제공 인지 기술은 코그너티브 서비스 API 엔드포인트에 의해 뒷받침됩니다. 경우에 따라 이러한 끝점에 일시적인 문제가 있으며 요청시간이 시간 지정됩니다. 일시적인 문제의 경우 기다렸다가 다시 시도하는 것 외에는 아무런 해결책이 없습니다. 완화로 인덱서가 일정에 따라 [실행되도록](search-howto-schedule-indexers.md)설정하는 것이 좋습니다. 예약된 인덱싱은 중단된 위치를 선택합니다. 일시적인 문제가 해결된다고 가정하면 인덱싱 및 인지 기술 처리는 다음 예약된 실행에서 계속할 수 있어야 합니다.

내장 된 인지 기술에 대한 동일한 문서에서이 오류가 계속 표시되는 경우 예상하지 않는 한 [지원을받을 수있는 지원 티켓을](https://ms.portal.azure.com/#create/Microsoft.Support) 제출하십시오.

### <a name="custom-skills"></a>사용자 지정 기술
만든 사용자 지정 기술로 시간 지정 오류가 발생하면 몇 가지 를 시도해 볼 수 있습니다. 먼저 사용자 지정 기술을 검토하고 무한 루프에 갇혀 있지 않고 결과를 일관되게 반환하고 있는지 확인합니다. 이 경우 를 확인한 후 기술의 실행 시간을 결정합니다. 사용자 지정 기술 정의에 `timeout` 값을 명시적으로 설정하지 않은 `timeout` 경우 기본값은 30초입니다. 30초가 기술이 실행되기에 충분하지 않은 경우 사용자 `timeout` 지정 기술 정의에 더 높은 값을 지정할 수 있습니다. 다음은 시간 지정이 90초로 설정된 사용자 지정 기술 정의의 예입니다.

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "uri": "<your custom skill uri>",
        "batchSize": 1,
        "timeout": "PT90S",
        "context": "/document",
        "inputs": [
          {
            "name": "input",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "output",
            "targetName": "output"
          }
        ]
      }
```

`timeout` 매개 변수에 대해 설정할 수 있는 최대 값은 230초입니다.  사용자 지정 기술이 230초 이내에 일관되게 실행되지 않는 `batchSize` 경우 단일 실행 내에서 처리할 문서가 더 적도록 사용자 지정 기술을 줄이는 것이 좋습니다.  이미 `batchSize` 1로 설정한 경우, 230초 이내에 실행할 수 있도록 스킬을 다시 작성하거나 여러 사용자 지정 스킬로 분할하여 단일 사용자 지정 스킬의 실행 시간이 최대 230초가 되도록 해야 합니다. 자세한 내용은 [사용자 지정 기술 설명서를](cognitive-search-custom-skill-web-api.md) 검토하십시오.

<a name="could-not-mergeorupload--delete-document-to-the-search-index"/>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>오류: 수`MergeOrUpload`없습니다 ' | '`Delete`검색 인덱스에 문서

문서를 읽고 처리했지만 인덱서가 검색 인덱스에 추가할 수 없습니다. 이 경우 다음과 같은 경우 발생할 수 있습니다.

| 이유 | 세부 정보/예 | 해결 방법 |
| --- | --- | --- |
| 필드에 너무 큰 용어가 포함되어 있습니다. | 문서의 용어가 [32KB 제한보다](search-limits-quotas-capacity.md#api-request-limits) 큽합니다. | 필드가 필터링 가능, 면 테이블 또는 정렬 가능으로 구성되지 않도록 하여 이러한 제한을 피할 수 있습니다.
| 문서가 너무 커서 인덱싱할 수 없습니다. | 문서가 최대 API [요청 크기보다](search-limits-quotas-capacity.md#api-request-limits) 큽합니다. | [대용량 데이터 세트인덱싱 하는 방법](search-howto-large-index.md)
| 문서에 컬렉션에 개체가 너무 많습니다. | 문서의 컬렉션은 모든 [복잡한 컬렉션에서 최대 요소를 초과하여](search-limits-quotas-capacity.md#index-limits) `'1000052'` "키가 `'4303'` 있는 문서에 는 컬렉션에 개체가 있습니다(JSON 배열). 대부분의 `'3000'` 개체는 전체 문서에서 컬렉션에 있을 수 있습니다. 컬렉션에서 개체를 제거하고 문서를 다시 인덱싱해 보십시오." | 문서의 복잡한 컬렉션 크기를 제한 이하로 줄이고 저장소 사용률이 높지 않도록 하는 것이 좋습니다.
| 서비스가 쿼리 또는 인덱싱과 같은 다른 로드 아래에 있기 때문에 대상 인덱스(재시도 후 지속됨)에 연결하는 데 문제가 있습니다. | 업데이트 인덱스에 대한 연결을 설정하지 못했습니다. 검색 서비스가 과부하 상태입니다. | [검색 서비스 확장](search-capacity-planning.md)
| 서비스 업데이트를 위해 검색 서비스가 패치되거나 토폴로지 재구성 중입니다. | 업데이트 인덱스에 대한 연결을 설정하지 못했습니다. 검색 서비스가 현재 다운/검색 서비스가 전환을 진행 중입니다. | [SLA 설명서당](https://azure.microsoft.com/support/legal/sla/search/v1_0/) 99.9% 가용성에 대해 3개 이상의 복제본으로 서비스 구성
| 기본 계산/네트워킹 리소스의 오류(드물게) | 업데이트 인덱스에 대한 연결을 설정하지 못했습니다. 알 수 없는 오류가 발생했습니다. | 실패한 상태에서 선택하도록 [일정에 따라 실행되도록](search-howto-schedule-indexers.md) 인덱서를 구성합니다.
| 네트워크 문제로 인해 시간 시간 지정 기간 내에 대상 인덱스에 대한 인덱싱 요청이 승인되지 않았습니다. | 적시에 검색 인덱스에 대한 연결을 설정할 수 없습니다. | 실패한 상태에서 선택하도록 [일정에 따라 실행되도록](search-howto-schedule-indexers.md) 인덱서를 구성합니다. 또한 이 오류 조건이 지속되면 인덱서 [일괄 처리 크기를](https://docs.microsoft.com/rest/api/searchservice/create-indexer#parameters) 낮추십시오.

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"/>

## <a name="error-could-not-index-document-because-some-of-the-documents-data-was-not-valid"></a>오류: 문서의 일부 데이터가 잘못되었기 때문에 문서를 인덱싱할 수 없습니다.

인덱서에서 문서를 읽고 처리했지만 인덱스 필드 구성과 인덱서에서 추출및 처리한 데이터의 불일치로 인해 검색 인덱스에 추가할 수 없습니다. 이 경우 다음과 같은 경우 발생할 수 있습니다.

| 이유 | 세부 정보/예
| --- | ---
| 인덱서에서 추출한 필드의 데이터 형식은 해당 대상 인덱스 필드의 데이터 모델과 호환되지 않습니다. | 키 '888'이 있는 문서의 데이터 필드 _'데이터'에는_'Edm.String' 형식의 '잘못된 값'이 있습니다. 예상 되는 유형은 '컬렉션 (Edm.String)'. |
| 문자열 값에서 JSON 엔터티를 추출하지 못했습니다. | JSON 개체로 _'데이터'의_'Edm.String' 형식의 값을 구문 분석할 수 없습니다. 오류:'값을 구문 분석한 후 예기치 않은 문자가 발생했습니다. 경로 '_경로_' 선 1, 위치 3162.' |
| 문자열 값에서 JSON 엔터티 컬렉션을 추출하지 못했습니다.  | JSON 배열로 _'데이터'의_'Edm.String' 형식의 값을 구문 분석할 수 없습니다. 오류:'값을 구문 분석한 후 예기치 않은 문자가 발생했습니다. 경로 '[0]', 1줄, 위치 27.' |
| 원본 문서에서 알 수 없는 형식이 발견되었습니다. | 알 수 없는 유형 '_알 수 없음_' 인덱싱할 수 없습니다. |
| 원본 문서에서 지리 지점에 대한 호환되지 않는 표기표가 사용되었습니다. | WKT POINT 문자열 리터럴은 지원되지 않습니다. 대신 GeoJson 포인트 리터럴을 사용하십시오. |

이러한 모든 경우에 [인덱서에 대한](https://docs.microsoft.com/rest/api/searchservice/data-type-map-for-indexers-in-azure-search) [지원되는 데이터 형식](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) 및 데이터 형식 맵을 참조하여 인덱스 스키마를 올바르게 빌드하고 적절한 [인덱서 필드 매핑을](search-indexer-field-mappings.md)설정했는지 확인합니다. 오류 메시지에는 불일치의 원인을 추적하는 데 도움이 되는 세부 정보가 포함됩니다.

## <a name="error-integrated-change-tracking-policy-cannot-be-used-because-table-has-a-composite-primary-key"></a>오류: 테이블에 복합 기본 키가 있기 때문에 통합 변경 사항 추적 정책을 사용할 수 없습니다.

이는 SQL 테이블에 적용되며 일반적으로 키가 복합 키로 정의되거나 테이블이 고유한 클러스터된 인덱스를 정의한 경우(Azure Search 인덱스가 아닌 SQL 인덱스)에 적용됩니다. 주된 이유는 키 특성이 [고유한 클러스터된 인덱스의](https://docs.microsoft.com/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?view=sql-server-ver15)경우 복합 기본 키로 수정되기 때문입니다. 이 경우 SQL 테이블에 고유한 클러스터된 인덱스가 없는지 또는 키 필드를 중복 값이 없는 필드에 매핑해야 합니다.

<a name="could-not-process-document-within-indexer-max-run-time"/>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>오류: 인덱서 최대 실행 시간 내에서 문서를 처리할 수 없습니다.

이 오류는 인덱서가 허용된 실행 시간 내에 데이터 원본에서 단일 문서 처리를 완료할 수 없을 때 발생합니다. 기술 집합을 사용할 때 [최대 실행 시간이](search-limits-quotas-capacity.md#indexer-limits) 짧아지습니다. 이 오류가 발생하면 maxFailedItems가 0이 아닌 값으로 설정된 경우 인덱싱은 인덱싱이 진행될 수 있도록 향후 실행시 문서를 무시합니다. 문서를 건너뛸 여유가 없거나 이 오류가 지속적으로 표시되는 경우 단일 인덱서 실행 내에서 부분적인 진행을 수행할 수 있도록 문서를 더 작은 문서로 나누는 것이 좋습니다.

<a name="could-not-project-document"/>

## <a name="error-could-not-project-document"></a>오류: 문서를 투사할 수 없습니다.

이 오류는 인덱서가 [데이터를 지식 저장소에 프로젝터로](knowledge-store-projection-overview.md) 프로젝터하려고 시도할 때 발생하며 이를 수행하려고 시도할 때 오류가 발생합니다.  이 오류는 일관되고 수정 가능하거나 프로젝션 출력 싱크에 일시적인 오류가 발생할 수 있으며 이를 해결하기 위해 기다렸다가 다시 시도해야 할 수 있습니다.  다음은 알려진 오류 상태 및 가능한 해결 방법 집합입니다.

| 이유 | 세부 정보/예 | 해결 방법 |
| --- | --- | --- |
| 컨테이너에서 프로젝션 `'blobUri'` Blob을 업데이트할 수 없습니다.`'containerName'` |지정된 컨테이너가 없습니다. | 인덱서에서는 지정된 컨테이너가 이전에 만들어졌는지 확인하고 필요한 경우 만들지만 인덱서 실행당 한 번만 이 검사를 수행합니다. 이 오류는 이 단계 이후에 컨테이너를 삭제한 것을 의미합니다.  이 오류를 해결하려면 저장소 계정 정보를 그대로 두고 인덱서가 완료될 때까지 기다린 다음 인덱서를 다시 실행합니다. |
| 컨테이너에서 프로젝션 `'blobUri'` Blob을 업데이트할 수 없습니다.`'containerName'` |전송 연결에 데이터를 쓸 수 없음: 원격 호스트에서 기존 연결이 강제로 닫혔습니다. | Azure Storage의 일시적인 오류가 발생할 것으로 예상되므로 인덱서를 다시 실행하여 해결해야 합니다. 이 오류가 지속적으로 발생하면 추가 조사를 받을 수 있도록 [지원 티켓을](https://ms.portal.azure.com/#create/Microsoft.Support) 제출하십시오.  |
| 테이블에서 행을 `'projectionRow'` 업데이트할 수 없습니다.`'tableName'` | 서버가 사용 중입니다. | Azure Storage의 일시적인 오류가 발생할 것으로 예상되므로 인덱서를 다시 실행하여 해결해야 합니다. 이 오류가 지속적으로 발생하면 추가 조사를 받을 수 있도록 [지원 티켓을](https://ms.portal.azure.com/#create/Microsoft.Support) 제출하십시오.  |

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"/>

## <a name="warning-skill-input-was-invalid"></a>경고: 기술 입력이 잘못되었습니다.
기술에 대한 입력이 없거나 형식이 잘못되었거나 유효하지 않습니다. 경고 메시지는 영향을 나타냅니다.
1) 스킬을 실행할 수 없습니다.
2) 기술이 실행되었지만 예기치 않은 결과가 발생할 수 있음

인지 능력에는 입력과 선택적 입력이 필요합니다. 예를 들어 [Key 구 추출 기술에는](cognitive-search-skill-keyphrases.md) 두 개의 필수 입력이 `text` `languageCode`있으며 선택적 입력이 없습니다. 사용자 지정 기술 입력은 모두 선택적 입력으로 간주됩니다.

필요한 입력이 없거나 입력이 올바른 형식이 아닌 경우 기술이 건너뛰고 경고를 생성합니다. 건너뛴 기술은 출력을 생성하지 않으므로 다른 기술이 건너뛴 기술의 출력을 사용하는 경우 추가 경고를 생성할 수 있습니다.

선택적 입력이 없는 경우 기술은 계속 실행되지만 누락된 입력으로 인해 예기치 않은 출력이 생성될 수 있습니다.

두 경우 모두 데이터 모양으로 인해 이 경고가 발생할 수 있습니다. 예를 `firstName`들어 필드가 `middleName`있는 사람에 대한 정보가 포함된 문서가 `lastName`있는 경우 및 에 대한 `middleName`항목이 없는 일부 문서가 있을 수 있습니다. 파이프라인의 기술에 `middleName` 대한 입력으로 전달하는 경우 이 기술 입력이 일부 누락될 수 있습니다. 이 경고의 결과로 작업이 필요한지 여부를 확인하려면 데이터와 시나리오를 평가해야 합니다.

입력이 누락된 경우 기본값을 제공하려는 경우 [조건부 기술을](cognitive-search-skill-conditional.md) 사용하여 기본값을 생성한 다음 [조건부 기술의](cognitive-search-skill-conditional.md) 출력을 기술 입력으로 사용할 수 있습니다.


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'en'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

| 이유 | 세부 정보/예 | 해결 방법 |
| --- | --- | --- |
| 기술 입력이 잘못된 유형입니다. | "필요한 기술 입력이 예상 `String`된 유형이 아니었습니다. 이름: `text`, `/document/merged_content`출처: "  "필요한 기술 입력이 예상되는 형식이 아니었습니다. 이름: `text`, `/document/merged_content`출처: "  "비배열을 `/document/normalized_images/0/imageCelebrities/0/detail/celebrities`반복할 수 없습니다."  "비배열에서 `0` `/document/normalized_images/0/imageCelebrities/0/detail/celebrities`선택할 수 없습니다." | 특정 기술은 특정 유형의 입력을 기대합니다(예: [Sentiment Skill은](cognitive-search-skill-sentiment.md) 문자열이 될 것으로 예상). `text` 입력이 비 문자열 값을 지정하면 기술이 실행되지 않고 출력을 생성하지 않습니다. 데이터 집합에 입력 값이 형식에 균일한지 확인하거나 [사용자 지정 웹 API 기술을](cognitive-search-custom-skill-web-api.md) 사용하여 입력을 전처리합니다. 배열을 통해 기술을 반복하는 경우 기술 컨텍스트와 입력이 `*` 올바른 위치에 있는지 확인합니다. 일반적으로 컨텍스트와 입력 소스는 `*` 배열에 대해 끝나야 합니다. |
| 기술 입력이 누락되었습니다. | "필요한 기술 입력이 없습니다. 이름: `text`, `/document/merged_content`소스: " `/document/normalized_images/0/imageTags`"누락 된 값."  "길이의 `0` `0` 배열에서 `/document/pages` 선택할 수 없습니다." | 모든 문서가 이 경고를 받으면 입력 경로에 오타가 있을 가능성이 높으며 속성 이름 대/소문자를 다시 확인하고 경로에서 추가 또는 누락된 `*` 문서를 데이터 원본의 문서가 필요한 입력을 제공하는지 확인해야 합니다. |
| 기술 언어 코드 입력이 잘못되었습니다. | 기술 `languageCode` 입력에는 다음 `X,Y,Z`언어 코드가 있으며 그 중 하나 이상이 유효하지 않습니다. | 자세한 내용은 [아래](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid) 를 참조하십시오. |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"/>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>경고: 기술 입력 'languageCode'에는 다음 언어 코드 'X, Y, Z'가 있으며 그 중 하나 이상이 유효하지 않습니다.
다운스트림 기술의 선택적 `languageCode` 입력으로 전달된 값 중 하나 이상이 지원되지 않습니다. 이는 [언어 탐지기술의](cognitive-search-skill-language-detection.md) 출력을 후속 기술에 전달하는 경우 발생할 수 있으며 출력은 해당 다운스트림 기술에서 지원되는 언어보다 많은 언어로 구성됩니다.

데이터 집합이 모두 한 언어로 되어 있는 경우 [언어 감지](cognitive-search-skill-language-detection.md) Skill `languageCode` 및 기술 `defaultLanguageCode` 입력을 제거하고 해당 기술에 대한 기술 매개 변수를 대신 사용해야 합니다.

데이터 집합에 여러 언어가 포함되어 있으므로 [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) 및 `languageCode` 입력이 필요한 경우 [조건부 Skill을](cognitive-search-skill-conditional.md) 추가하여 텍스트를 다운스트림 기술로 전달하기 전에 지원되지 않는 언어로 텍스트를 필터링하는 것이 좋습니다.  다음은 EntityRecognitionSkill의 경우 이러한 모양의 예입니다.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'de' || $(/document/language) == 'en' || $(/document/language) == 'es' || $(/document/language) == 'fr' || $(/document/language) == 'it'" },
        { "name": "whenTrue", "source": "/document/content" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "supportedByEntityRecognitionSkill" } ]
}
```

다음은 이 오류 메시지를 생성할 수 있는 각 기술에 대해 현재 지원되는 언어에 대한 몇 가지 참조입니다.
* [텍스트 분석 지원](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) 언어(키어프레이즈추출기술, [엔티티인식스킬,](cognitive-search-skill-entity-recognition.md) [감성스킬](cognitive-search-skill-sentiment.md)및 [PIIDetectionSkill)](cognitive-search-skill-pii-detection.md) [KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
* [번역기 지원](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) [언어(텍스트 번역기술용)](cognitive-search-skill-text-translation.md)
* [텍스트 분할 기술](cognitive-search-skill-textsplit.md) 지원되는 언어:`da, de, en, es, fi, fr, it, ko, pt`

<a name="skill-input-was-truncated"/>

## <a name="warning-skill-input-was-truncated"></a>경고: 기술 입력이 잘렸습니다.
인지 능력은 한 번에 분석 할 수있는 텍스트의 길이에 제한이 있습니다. 이러한 기술의 텍스트 입력이 해당 제한을 초과하면 제한을 충족하도록 텍스트를 잘린 다음 잘린 텍스트에 대한 보강을 수행합니다. 즉, 기술이 실행되지만 모든 데이터에 대해 실행되지는 않습니다.

아래의 LanguageDetectionSkill 예제에서 `'text'` 입력 필드는 문자 제한을 초과하는 경우 이 경고를 트리거할 수 있습니다. [기술 설명서에서](cognitive-search-predefined-skills.md)기술 입력 한도를 찾을 수 있습니다.

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [...]
  }
```

모든 텍스트를 분석하려면 [분할 기술을](cognitive-search-skill-textsplit.md)사용하는 것이 좋습니다.

<a name="web-api-skill-response-contains-warnings"/>

## <a name="warning-web-api-skill-response-contains-warnings"></a>경고: 웹 API 기술 응답에 경고가 포함되어 있습니다.
인덱서에서는 기술 집합에서 기술을 실행할 수 있었지만 웹 API 요청의 응답에 실행 중에 경고가 있음을 표시했습니다. 경고를 검토하여 데이터가 어떻게 영향을 받는지, 그리고 조치가 필요한지 여부를 파악합니다.

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"/>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>경고: 현재 인덱서 구성이 증분 진행률을 지원하지 않습니다.

이 경고는 Cosmos DB 데이터 원본에 대해서만 발생합니다.

인덱싱하는 동안 증분 진행률은 인덱서 실행이 일시적 오류 또는 실행 시간 제한에 의해 중단되었는지를 확인합니다. 인덱서는 처음부터 전체 컬렉션을 다시 인덱스하는 대신 다음으로 실행할 위치를 선택할 수 있습니다. 대규모 컬렉션을 인덱싱할 때 특히 유용합니다.

완료되지 않은 인덱싱 작업을 다시 시작할 수 있는 기능은 `_ts` 열에서 정렬된 문서를 갖는 데 중점을 두는 것입니다. 인덱서에서는 타임스탬프를 사용하여 다음에 선택할 문서를 결정합니다. 열이 `_ts` 없거나 인덱서가 사용자 지정 쿼리의 순서를 지정할지 확인할 수 없는 경우 인덱서가 처음에 시작되고 이 경고가 표시됩니다.

`assumeOrderByHighWatermarkColumn` 이 동작을 재정의하여 증분 진행을 가능하게 하고 구성 속성을 사용하여 이 경고를 억제할 수 있습니다.

자세한 내용은 [증분 진행률 및 사용자 지정 쿼리를](search-howto-index-cosmosdb.md#IncrementalProgress)참조하십시오.

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"/>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>경고: 투영 중에 일부 데이터가 손실되었습니다. 테이블 'Y'의 행 'X'에는 너무 긴 문자열 속성 'Z'가 있습니다.

[테이블 저장소 서비스에는](https://azure.microsoft.com/services/storage/tables) 큰 [엔터티 속성의](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model#property-types) 수에 제한이 있습니다. 문자열은 32,000자 이하를 가질 수 있습니다. 문자열 속성이 32,000자보다 긴 행이 투영되는 경우 처음 32,000자만 유지됩니다. 이 문제를 해결하려면 문자열 속성이 32,000자보다 긴 행을 투영하지 마십시오.

<a name="truncated-extracted-text-to-x-characters"/>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>경고: X 문자로 잘린 추출된 텍스트
인덱서작성자는 한 문서에서 추출할 수 있는 텍스트의 양을 제한합니다. 이 제한은 프리 티어의 경우 32,000자, 기본 용 64,000자, 표준 S2의 경우 400만 대, 표준 S3의 경우 1,600만 문자입니다. 잘린 텍스트는 인덱싱되지 않습니다. 이 경고를 방지하려면 많은 양의 텍스트가 있는 문서를 더 작은 여러 문서로 분리해 보십시오. 

자세한 내용은 [인덱서 제한을](search-limits-quotas-capacity.md#indexer-limits)참조하십시오.

<a name="could-not-map-output-field-x-to-search-index"/>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>경고: 출력 필드 'X'를 검색 인덱스에 매핑할 수 없습니다.
존재하지 않는/null 데이터를 참조하는 출력 필드 매핑은 각 문서에 대한 경고를 생성하고 빈 인덱스 필드를 생성합니다. 이 문제를 해결하려면 출력 필드 매핑 소스 경로를 다시 확인하여 가능한 오타에 대해 확인하거나 [조건부 기술을](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist)사용하여 기본값을 설정합니다.

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"/>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>경고: 데이터 변경 검색 정책은 키 열 'X'를 사용하도록 구성됩니다.
[데이터 변경 검색 정책에는](https://docs.microsoft.com/rest/api/searchservice/create-data-source#data-change-detection-policies) 변경 을 검색하는 데 사용하는 열에 대한 특정 요구 사항이 있습니다. 이러한 요구 사항 중 하나는 원본 항목이 변경될 때마다 이 열이 업데이트된다는 것입니다. 또 다른 요구 사항은 이 열의 새 값이 이전 값보다 크다는 것입니다. 모든 업데이트에서 변경되지 않으므로 키 열이 이 요구 사항을 충족하지 못합니다. 이 문제를 해결하려면 변경 검색 정책에 대한 다른 열을 선택합니다.

<a name="document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"/>

## <a name="warning-document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>경고: 문서 텍스트가 UTF-16 인코딩된 것처럼 보이지만 바이트 순서 표시가 없습니다.

[인덱서 구문 분석 모드는](https://docs.microsoft.com/rest/api/searchservice/create-indexer#blob-configuration-parameters) 텍스트를 구문 분석하기 전에 텍스트가 인코딩되는 방법을 알아야 합니다. 텍스트를 인코딩하는 가장 일반적인 두 가지 방법은 UTF-16과 UTF-8입니다. UTF-8은 각 문자가 1바이트에서 4바이트 사이의 가변 길이 인코딩입니다. UTF-16은 각 문자가 2바이트 길이인 고정 길이 인코딩입니다. UTF-16에는 "빅 엔디안"과 "리틀 엔디안"이라는 두 가지 변형이 있습니다. 텍스트 인코딩은 텍스트 앞에 일련의 바이트인 "바이트 순서 표시"로 결정됩니다.

| Encoding | 바이트 순서 표시 |
| --- | --- |
| UTF-16 빅 엔디안 | 0xFE 0xFF |
| UTF-16 리틀 엔디안 | 0xFF 0xFE |
| UTF-8 | 0xEF 0xBB 0xBF |

바이트 순서 표시가 없는 경우 텍스트는 UTF-8로 인코딩된 것으로 가정합니다.

이 경고를 해결하려면 이 Blob에 대한 텍스트 인코딩을 결정하고 적절한 바이트 순서 표시를 추가합니다.

<a name="cosmos-db-collection-has-a-lazy-indexing-policy"/>

## <a name="warning-cosmos-db-collection-x-has-a-lazy-indexing-policy-some-data-may-be-lost"></a>경고: Cosmos DB 컬렉션 'X'에는 지연 인덱싱 정책이 있습니다. 일부 데이터가 손실될 수 있습니다.

[지연](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode) 인덱싱 정책이 있는 컬렉션은 일관되게 쿼리할 수 없으므로 인덱서에서 데이터가 누락됩니다. 이 경고를 해결하려면 인덱싱 정책을 일관성으로 변경합니다.
