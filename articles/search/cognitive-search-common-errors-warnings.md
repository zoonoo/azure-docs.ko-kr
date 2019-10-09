---
title: 일반적인 오류 및 경고-Azure Search
description: 이 문서에서는 Azure Search AI 보강 중 발생할 수 있는 일반적인 오류 및 경고에 대 한 정보 및 솔루션을 제공 합니다.
services: search
manager: heidist
author: amotley
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: abmotley
ms.openlocfilehash: b5a161e570489e6382f2226ab5dc9a1c34dc67df
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028325"
---
# <a name="common-errors-and-warnings-of-the-ai-enrichment-pipeline-in-azure-search"></a>Azure Search AI 보강 파이프라인의 일반적인 오류 및 경고

이 문서에서는 Azure Search AI 보강 중 발생할 수 있는 일반적인 오류 및 경고에 대 한 정보 및 솔루션을 제공 합니다.

## <a name="errors"></a>오류
오류 수가 [' Maxfaileditems '](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures)를 초과 하면 인덱싱이 중지 됩니다. 

인덱서를 통해 이러한 오류를 무시 하 고 "실패 한 문서"를 건너뛰도록 하려면 [여기](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers)에 설명 된 대로 `maxFailedItems` 및 `maxFailedItemsPerBatch`을 업데이트 하는 것이 좋습니다.

> [!NOTE]
> 문서 키 (사용 가능한 경우)와 함께 실패 한 각 문서는 인덱서 실행 상태에서 오류로 표시 됩니다. 오류가 허용 되도록 인덱서를 설정한 경우 [인덱스 api](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) 를 활용 하 여 나중에 수동으로 문서를 업로드할 수 있습니다.

다음 섹션에서는 오류를 해결 하는 데 도움이 될 수 있으므로 인덱싱을 계속할 수 있습니다.

### <a name="could-not-read-document"></a>문서를 읽을 수 없습니다.
인덱서가 데이터 소스에서 문서를 읽을 수 없습니다. 이 문제는 다음과 같은 경우에 발생할 수 있습니다.

| Reason | 예제 | 작업 |
| --- | --- | --- |
| 여러 문서에서 일치 하지 않는 필드 형식 | 값의 형식이 열 형식과 일치 하지 않습니다. 작성자 열에 `'{47.6,-122.1}'`을 저장할 수 없습니다.  필요한 형식은 JArray입니다. | 각 필드의 형식이 서로 다른 문서에서 동일한 지 확인 합니다. 예를 들어 첫 번째 문서 `'startTime'` 필드가 DateTime이 고 두 번째 문서에서 문자열이 면이 오류가 발생 합니다. |
| 데이터 원본의 기본 서비스에서 발생 한 오류 | (Cosmos DB) `{"Errors":["Request rate is large"]}` | 저장소 인스턴스를 확인 하 여 정상 상태 인지 확인 합니다. 크기 조정/분할을 조정 해야 할 수 있습니다. |
| 일시적인 문제 | 서버에서 결과를 받을 때 전송 수준 오류가 발생했습니다. (공급자: TCP 공급자, 오류: 0-기존 연결이 원격 호스트에 의해 강제로 끊겼습니다. | 예기치 않은 연결 문제가 발생 하는 경우도 있습니다. 나중에 인덱서를 통해 문서를 다시 실행 해 보세요. |

### <a name="could-not-extract-document-content"></a>문서 콘텐츠를 추출할 수 없습니다.
Blob 데이터 원본이 포함 된 인덱서가 문서에서 콘텐츠를 추출할 수 없습니다 (예: PDF 파일). 이 문제는 다음과 같은 경우에 발생할 수 있습니다.

| Reason | 예제 | 작업 |
| --- | --- | --- |
| blob이 크기 제한을 초과 합니다. | 문서는 현재 서비스 계층의 문서 추출에 대 한 최대 크기 `'134217728'` 바이트를 초과 하는 `'150441598'` 바이트입니다. | [blob 인덱싱 오류](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| blob의 콘텐츠 형식이 지원 되지 않습니다. | 문서에 지원 되지 않는 콘텐츠 형식 @no__t 있습니다. | [blob 인덱싱 오류](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| blob이 암호화 되어 있습니다. | 문서를 처리할 수 없습니다. 암호화 또는 암호로 보호할 수 있습니다. | [blob 설정](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed) |
| 일시적인 문제 | Blob 처리 중 오류 발생: 요청이 중단 되었습니다. 요청이 취소 되었습니다. | 예기치 않은 연결 문제가 발생 하는 경우도 있습니다. 나중에 인덱서를 통해 문서를 다시 실행 해 보세요. |

### <a name="could-not-parse-document"></a>문서를 구문 분석할 수 없습니다.
인덱서는 데이터 소스에서 문서를 읽었지만 문서 콘텐츠를 지정 된 필드 매핑 스키마로 변환 하는 동안 문제가 발생 했습니다. 이 문제는 다음과 같은 경우에 발생할 수 있습니다.

| Reason | 예제 | 작업 |
| --- | --- | --- |
| 문서 키가 없습니다. | 문서 키를 누락 하거나 비워 둘 수 없습니다. | 모든 문서에 유효한 문서 키가 있는지 확인 |
| 문서 키가 잘못 되었습니다. | 문서 키는 1024 자를 초과할 수 없습니다. | 유효성 검사 요구 사항에 맞게 문서 키를 수정 합니다. |
| 필드에 필드 매핑을 적용할 수 없습니다. | 매핑 함수 `'functionName'`을 (를) @no__t 필드에 적용할 수 없습니다. 배열은 null 일 수 없습니다. 매개 변수 이름: 바이트 | 인덱서에 정의 된 [필드 매핑을](search-indexer-field-mappings.md) 두 번 확인 하 고 실패 한 문서의 지정 된 필드의 데이터와 비교 합니다. 필드 매핑 또는 문서 데이터를 수정 해야 할 수도 있습니다. |
| 필드 값을 읽을 수 없습니다. | 인덱스 `'fieldIndex'`에서 `'fieldName'` 열의 값을 읽을 수 없습니다. 서버에서 결과를 받을 때 전송 수준 오류가 발생했습니다. (공급자: TCP 공급자, 오류: 0-기존 연결이 원격 호스트에 의해 강제로 끊겼습니다.) | 이러한 오류는 일반적으로 데이터 원본의 기본 서비스와 관련 된 예기치 않은 연결 문제로 인해 발생 합니다. 나중에 인덱서를 통해 문서를 다시 실행 해 보세요. |

### <a name="could-not-index-document"></a>문서를 인덱싱할 수 없습니다.
문서를 읽고 처리 했지만 인덱서가 검색 인덱스에 추가할 수 없습니다. 이 문제는 다음과 같은 경우에 발생할 수 있습니다.

| Reason | 예제 | 작업 |
| --- | --- | --- |
| 필드에 너무 많은 용어가 있습니다. | 문서의 용어가 [32 KB 제한](search-limits-quotas-capacity.md#api-request-limits) 보다 큽니다. | 필드가 필터링 가능, 패싯 가능 또는 정렬 가능으로 구성 되지 않도록 하 여이 제한을 피할 수 있습니다.
| 문서가 너무 커서 인덱싱할 수 없습니다. | 문서가 [최대 api 요청 크기](search-limits-quotas-capacity.md#api-request-limits) 보다 큽니다. | [대량 데이터 집합을 인덱싱하는 방법](search-howto-large-index.md)

### <a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>기술 입력 ' languageCode '에 다음 언어 코드 ' X, Y, Z '가 하나 이상 잘못 되었습니다.
다운스트림 기술에 대 한 선택적 `languageCode` 입력으로 전달 된 값 중 하나 이상이 지원 되지 않습니다. 이 문제는 [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) 의 출력을 후속 기술로 전달 하 고 출력이 해당 다운스트림 기술에서 지원 되는 것 보다 많은 언어로 구성 된 경우에 발생할 수 있습니다.

데이터 집합이 모두 한 언어로 표시 되는 경우 [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) 및 @no__t 1 기술 입력을 제거 하 고 해당 기술에 대 한 언어가 지원 되는 경우 대신 해당 기술에 대 한 `defaultLanguageCode` 기술 매개 변수를 사용 해야 합니다.

데이터 집합에 여러 언어가 포함 되어 있으므로 [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) 및 `languageCode` 입력이 필요 하다 고 생각 되는 경우 [ConditionalSkill](cognitive-search-skill-conditional.md) 를 추가 하 여를 전달 하기 전에 지원 되지 않는 언어를 사용 하 여 텍스트를 필터링 하는 것이 좋습니다. 다운스트림 기술에 대 한 텍스트입니다.  EntityRecognitionSkill에 대 한 예는 다음과 같습니다.

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

이 오류 메시지를 생성할 수 있는 각 기술에 대해 현재 지원 되는 언어에 대 한 몇 가지 참조는 다음과 같습니다.
* [Text Analytics 지원 되는 언어](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) ( [KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md), [EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)및 [SentimentSkill](cognitive-search-skill-sentiment.md)의 경우)
* [Translator 지원 언어](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) ( [텍스트 TranslationSkill](cognitive-search-skill-text-translation.md))
* [텍스트 SplitSkill](cognitive-search-skill-textsplit.md) 지원 되는 언어: `da, de, en, es, fi, fr, it, ko, pt`

### <a name="skill-did-not-execute-within-the-time-limit"></a>기술이 제한 시간 내에 실행 되지 않았습니다.
이 오류 메시지가 나타날 수 있는 두 가지 경우에는 각각 다르게 처리 되어야 합니다. 이 오류를 반환한 기술에 따라 아래 지침을 따르세요.

#### <a name="built-in-cognitive-service-skills"></a>기본 제공 인식 서비스 기술
언어 검색, 엔터티 인식 또는 OCR과 같은 여러 가지 내장 인식 기술이 인식 서비스 API 끝점에 의해 지원 됩니다. 이러한 끝점에 일시적인 문제가 있어 요청 시간이 초과 되는 경우가 있습니다. 일시적인 문제의 경우 대기를 제외 하 고 다시 시도 하는 경우를 제외 하 고는 해결 방법이 없습니다. 이를 완화 하려면 [일정에 따라 실행](search-howto-schedule-indexers.md)되도록 인덱서를 설정 하는 것이 좋습니다. 예약 된 인덱싱이 중단 된 위치에서 선택 합니다. 일시적인 문제를 해결 하는 것으로 가정 하면 인덱싱 및 인식 기술 처리는 다음에 예약 된 실행을 계속할 수 있습니다.

#### <a name="custom-skills"></a>사용자 지정 기술
만든 사용자 지정 기술에 시간 초과 오류가 발생 하는 경우 몇 가지 작업을 수행해 볼 수 있습니다. 먼저 사용자 지정 기술을 검토 하 고 무한 루프에서 중단 되 고 결과를 일관 되 게 반환 하는지 확인 합니다. 이를 확인 한 후에는 기술 실행 시간을 결정 합니다. 사용자 지정 기술 정의에 `timeout` 값을 명시적으로 설정 하지 않은 경우 기본값 `timeout`이 30 초입니다. 30 초가 너무 길어서 기술 실행에 충분 하지 않은 경우 사용자 지정 기술 정의에 더 높은 `timeout` 값을 지정할 수 있습니다. 다음은 시간 제한이 90 초로 설정 된 사용자 지정 기술 정의의 예입니다.

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

@No__t-0 매개 변수에 대해 설정할 수 있는 최대값은 230 초입니다.  사용자 지정 기술이 230 초 내에 일관 되 게 실행할 수 없는 경우 단일 실행 내에서 처리할 문서 수가 줄어들기 때문에 사용자 지정 기술 `batchSize`을 줄이는 것이 좋습니다.  @No__t-0을 1로 이미 설정한 경우에는 230 초 이내에 실행할 수 있도록 기술을 다시 작성 해야 합니다. 그렇지 않으면 단일 사용자 지정 기술에 대 한 실행 시간이 최대 230 초가 되도록 여러 사용자 지정 기술로 분할할 수 있습니다. 자세한 내용은 [사용자 지정 기술 설명서](cognitive-search-custom-skill-web-api.md) 를 참조 하십시오.

### <a name="could-not-mergeorupload--delete-document-to-the-search-index"></a>' @No__t-0 '을 (를) 할 수 없습니다. | 검색 인덱스에 대 한 ' `Delete` ' 문서

문서를 읽고 처리 했지만 인덱서가 검색 인덱스에 추가할 수 없습니다. 이 문제는 다음과 같은 경우에 발생할 수 있습니다.

| Reason | 예제 | 작업 |
| --- | --- | --- |
| 문서의 용어가 [32 KB 제한](search-limits-quotas-capacity.md#api-request-limits) 보다 큽니다. | 필드에 너무 많은 용어가 있습니다. | 필드가 필터링 가능, 패싯 가능 또는 정렬 가능으로 구성 되지 않도록 하 여이 제한을 피할 수 있습니다.
| 문서가 [최대 api 요청 크기](search-limits-quotas-capacity.md#api-request-limits) 보다 큽니다. | 문서가 너무 커서 인덱싱할 수 없습니다. | [대량 데이터 집합을 인덱싱하는 방법](search-howto-large-index.md)
| 쿼리가 나 인덱싱 등의 다른 부하에서 서비스를 하 고 있으므로 대상 인덱스에 연결 하는 데 문제가 있습니다 (재시도 후 지속 됨). | 인덱스 업데이트에 대 한 연결을 설정 하지 못했습니다. 검색 서비스의 부하가 높습니다. | [Search 서비스 확장](search-capacity-planning.md)
| 서비스 업데이트를 위해 검색 서비스를 패치 하 고 있거나 토폴로지를 다시 구성 하는 중입니다. | 인덱스 업데이트에 대 한 연결을 설정 하지 못했습니다. 검색 서비스가 현재 다운 되었거나 검색 서비스에서 전환 중입니다. | [SLA 설명서](https://azure.microsoft.com/support/legal/sla/search/v1_0/) 당 99.9%의 가용성을 위해 3 개 이상의 복제본을 사용 하 여 서비스를 구성 합니다.
| 기본 계산/네트워킹 리소스에서 오류가 발생 했습니다 (드문 경우). | 인덱스 업데이트에 대 한 연결을 설정 하지 못했습니다. 알 수 없는 오류가 발생 했습니다. | 실패 상태에서 선택할 수 있도록 [일정에 따라 실행](search-howto-schedule-indexers.md) 되도록 인덱서를 구성 합니다.

##  <a name="warnings"></a>기록
경고는 인덱싱을 중지 하지 않지만 예기치 않은 결과가 발생할 수 있는 조건을 표시 합니다. 작업 수행 여부는 데이터와 시나리오에 따라 달라 집니다.

### <a name="skill-input-was-truncated"></a>기술 입력이 잘렸습니다.
인식 기술은 한 번에 분석할 수 있는 텍스트의 길이에 제한이 있습니다. 이러한 기술에 대 한 텍스트 입력이 해당 한도를 초과 하는 경우이 한도에 맞게 텍스트를 자르고 잘린 텍스트에 대해 보강를 수행 합니다. 즉, 기술이 실행 되지만 일부 데이터에 대해서는 실행 되지 않습니다.

아래 예제 LanguageDetectionSkill에서 `'text'` 입력 필드는 문자 제한을 초과 하는 경우이 경고를 트리거할 수 있습니다. 기술 [문서](cognitive-search-predefined-skills.md)에서 기술 입력 한도를 찾을 수 있습니다.

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

모든 텍스트를 분석 하려면 [분할 기술을](cognitive-search-skill-textsplit.md)사용 하는 것이 좋습니다.