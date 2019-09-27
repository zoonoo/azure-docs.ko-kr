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
ms.subservice: cognitive-search
ms.openlocfilehash: 4e31f818e96ae9f13e3ce8892e575318831848f6
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71329384"
---
# <a name="common-errors-and-warnings-of-the-ai-enrichment-pipeline-in-azure-search"></a>Azure Search AI 보강 파이프라인의 일반적인 오류 및 경고

이 문서에서는 Azure Search AI 보강 중 발생할 수 있는 일반적인 오류 및 경고에 대 한 정보 및 솔루션을 제공 합니다.

## <a name="errors"></a>오류
오류 수가 [' maxfaileditems '](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures)를 초과 하면 인덱싱이 중지 됩니다. 다음 섹션에서는 오류를 해결 하는 데 도움이 될 수 있으므로 인덱싱을 계속할 수 있습니다.

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

##  <a name="warnings"></a>경고
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