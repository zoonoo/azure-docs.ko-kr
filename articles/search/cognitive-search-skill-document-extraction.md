---
title: 문서 추출 인식 기술 (미리 보기)
titleSuffix: Azure Cognitive Search
description: 보강 파이프라인 내에서 파일의 콘텐츠를 추출 합니다. 이 기술은 현재 공개 미리 보기로 제공 됩니다.
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: chalton
ms.openlocfilehash: 0f67caad03c4ebd1cf8f3721f377d8362219016a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76837734"
---
# <a name="document-extraction-cognitive-skill"></a>문서 추출 인식 기술

> [!IMPORTANT] 
> 이 기술은 현재 공개 미리 보기로 제공 됩니다. 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. 현재 포털 또는 .NET SDK가 지원 되지 않습니다.

**문서 추출** 기술은 보강 파이프라인 내에서 파일의 콘텐츠를 추출 합니다. 이렇게 하면 다른 기술에서 생성 될 수 있는 파일을 사용 하 여 기술 실행 하기 전에 일반적으로 발생 하는 문서 추출 단계를 활용할 수 있습니다.

> [!NOTE]
> 처리 빈도를 늘리거나 문서를 추가하거나 AI 알고리즘을 추가하여 범위를 확장할 때 [청구 가능한 Cognitive Services 리소스를 연결](cognitive-search-attach-cognitive-services.md)해야 합니다. Cognitive Services에서 Api를 호출 하는 경우와 인덱싱 중 문서 크랙 단계의 일부로 이미지 추출에 대 한 요금이 부과 됩니다. 문서에서 텍스트 추출할 때는 요금이 발생하지 않습니다.
>
> 기본 제공 기술을 실행하는 요금은 기존 [Cognitive Services 종량제 가격](https://azure.microsoft.com/pricing/details/cognitive-services/)으로 청구됩니다. 이미지 추출 가격은 [가격 책정 페이지](https://go.microsoft.com/fwlink/?linkid=2042400)에 설명 되어 있습니다.
## <a name="odatatype"></a>@odata.type  
Microsoft. DocumentExtractionSkill

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분합니다.

| 입력            | 허용되는 값 | Description |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | 순수한 텍스트 또는 json이 아닌 파일에서 문서 추출을 `default`로 설정 합니다. 일반 텍스트 파일의 성능을 향상 시키려면 `text`로 설정 합니다. Json 파일에서 구조화 된 콘텐츠를 추출 하려면 `json`로 설정 합니다. `parsingMode` 명시적으로 정의 되지 않은 경우 `default`로 설정 됩니다. |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | 를 `contentAndMetadata` 설정 하 여 각 파일에서 모든 메타 데이터 및 텍스트 콘텐츠를 추출 합니다. [콘텐츠 유형별 메타](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata) 데이터만 추출 하려면 `allMetadata`로 설정 합니다 (예: .png 파일에만 고유한 메타 데이터). `dataToExtract` 명시적으로 정의 되지 않은 경우 `contentAndMetadata`로 설정 됩니다. |
| `configuration` | 아래를 참조하세요. | 문서 추출을 수행 하는 방법을 조정 하는 선택적 매개 변수의 사전입니다. 지원 되는 구성 속성에 대 한 설명은 아래 표를 참조 하세요. |

| 구성 매개 변수   | 허용되는 값 | Description |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | 데이터 집합에 포함 된 이미지 또는 이미지 파일을 무시 하려면 `none`로 설정 합니다. 이것이 기본값입니다. <br/>[인지 기술을 사용 하는 이미지 분석](cognitive-search-concept-image-scenarios.md)의 경우 기술에서 문서 크랙의 일부로 정규화 된 이미지 배열을 만들도록 `generateNormalizedImages`로 설정 합니다. 이 작업을 수행 하려면 `parsingMode` `default`으로 설정 되 고 `dataToExtract`이 `contentAndMetadata`로 설정 되어야 합니다. 정규화된 이미지는 시각적 검색 결과에 이미지를 포함할 때 일관된 렌더링을 위해 크기 및 회전이 지정되어 균일한 이미지를 출력하는 추가 처리를 의미합니다(예: [JFK 데모](https://github.com/Microsoft/AzureSearch_JFK_Files)에서 볼 수 있는 그래프 컨트롤의 동일 크기 사진). 이 옵션을 사용 하는 경우 각 이미지에 대해이 정보가 생성 됩니다.  <br/>을 `generateNormalizedImagePerPage`로 설정 하면 PDF 파일이 포함 된 이미지를 추출 하는 대신 다른 방식으로 처리 됩니다. 각 페이지는 이미지로 렌더링 되 고 그에 따라 정규화 됩니다.  비 PDF 파일 형식은 `generateNormalizedImages` 설정 된 것과 동일 하 게 처리 됩니다.
| `normalizedImageMaxWidth` | 50-10000 사이의 정수 | 정규화된 이미지의 최대 너비(픽셀 단위)가 생성되었습니다. 기본값은 2000입니다. | 
| `normalizedImageMaxHeight` | 50-10000 사이의 정수 | 정규화된 이미지의 최대 높이(픽셀 단위)가 생성되었습니다. 기본값은 2000입니다. |

> [!NOTE]
> 정규화된 이미지의 최대 너비와 높이에 대한 2000 픽셀이라는 기본값은 [OCR 기술](cognitive-search-skill-ocr.md) 및 [이미지 분석 기술](cognitive-search-skill-image-analysis.md)에서 지원하는 최대 크기에 기반합니다. [OCR 기술은](cognitive-search-skill-ocr.md) 영어가 아닌 언어의 경우 최대 너비와 높이를 4200, 영어의 경우 1만을 지원 합니다.  최대 한도를 늘리면 기술 정의 및 문서 언어에 따라 큰 이미지에서 처리가 실패할 수 있습니다. 
## <a name="skill-inputs"></a>기술 입력

| 입력 이름     | Description |
|--------------------|-------------|
| file_data | 콘텐츠를 추출 해야 하는 파일입니다. |

"File_data" 입력은 다음과 같이 정의 된 개체 여야 합니다.

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

이 파일 참조 개체는 다음 세 가지 방법 중 하나로 생성 될 수 있습니다.

 - 인덱서 정의의 `allowSkillsetToReadFileData` 매개 변수를 "true"로 설정 합니다.  이렇게 하면 blob 데이터 원본에서 다운로드 한 원래 파일 데이터를 나타내는 개체인 `/document/file_data` 경로가 만들어집니다. 이 매개 변수는 Blob storage의 데이터에만 적용 됩니다.

 - 인덱서 정의의 `imageAction` 매개 변수를 `none`이외의 값으로 설정 합니다.  이렇게 하면 개별적으로 전달 된 경우 (예: `/document/normalized_images/*`)이 기술에 대 한 입력에 필요한 규칙을 따르는 이미지 배열이 생성 됩니다.

 - 사용자 지정 기술에서 정확히 위와 같이 정의 된 json 개체를 반환 합니다.  `$type` 매개 변수는 정확 하 게 `file`로 설정 해야 하 고 `data` 매개 변수는 파일 콘텐츠의 기본 64 인코딩 바이트 배열 데이터 여야 합니다.

## <a name="skill-outputs"></a>기술 출력

| 출력 이름    | Description |
|--------------|-------------|
| content | 문서의 텍스트 내용입니다. |
| normalized_images | `imageAction` `none`다른 값으로 설정 된 경우에는 새 *normalized_images* 필드가 이미지 배열을 포함 합니다. 각 이미지의 출력 형식에 대 한 자세한 내용은 [이미지 추출에 대 한 설명서를](cognitive-search-concept-image-scenarios.md) 참조 하세요. |

##  <a name="sample-definition"></a>샘플 정의

```json
 {
    "@odata.type": "#Microsoft.Skills.Util.DocumentExtractionSkill",
    "parsingMode": "default",
    "dataToExtract": "contentAndMetadata",
    "configuration": {
        "imageAction": "generateNormalizedImages",
        "normalizedImageMaxWidth": 2000,
        "normalizedImageMaxHeight": 2000
    },
    "context": "/document",
    "inputs": [
      {
        "name": "file_data",
        "source": "/document/file_data"
      }
    ],
    "outputs": [
      {
        "name": "content",
        "targetName": "content"
      },
      {
        "name": "normalized_images",
        "targetName": "normalized_images"
      }
    ]
  }
```

##  <a name="sample-input"></a>샘플 입력

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "file_data": {
          "$type": "file",
          "data": "aGVsbG8="
        }
      }
    }
  ]
}
```


##  <a name="sample-output"></a>샘플 출력

```json
{
  "values": [
    {
      "recordId": "1",
      "data": {
        "content": "hello",
        "normalized_images": []
      }
    }
  ]
}
```

## <a name="see-also"></a>참고 항목

+ [기본 제공 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [인식 검색 시나리오에서 이미지의 정보를 처리 하 고 추출 하는 방법](cognitive-search-concept-image-scenarios.md)