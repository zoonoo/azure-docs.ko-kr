---
title: 문서 추출 인지 기술(미리 보기)
titleSuffix: Azure Cognitive Search
description: 보강 파이프라인 내의 파일에서 콘텐츠를 추출합니다. 이 기술은 현재 공개 미리 보기 상태입니다.
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: chalton
ms.openlocfilehash: 0f67caad03c4ebd1cf8f3721f377d8362219016a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76837734"
---
# <a name="document-extraction-cognitive-skill"></a>문서 추출 인지 기술

> [!IMPORTANT] 
> 이 기술은 현재 공개 미리 보기 상태입니다. 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. 현재 포털 또는 .NET SDK 지원이 없습니다.

**문서 추출** 기술은 보강 파이프라인 내의 파일에서 콘텐츠를 추출합니다. 이렇게 하면 다른 기술에 의해 생성될 수 있는 파일을 사용하여 기술 집합을 실행하기 전에 일반적으로 발생하는 문서 추출 단계를 활용할 수 있습니다.

> [!NOTE]
> 처리 빈도를 늘리거나, 문서를 더 추가하거나, AI 알고리즘을 추가하여 범위를 확장하려면 [청구 가능한 인지 서비스 리소스를 연결해야](cognitive-search-attach-cognitive-services.md)합니다. 인지 서비스에서 API를 호출할 때 및 인덱싱에서 문서 크래킹 단계의 일부로 이미지 추출시 요금이 부과됩니다. 문서에서 텍스트 추출할 때는 요금이 발생하지 않습니다.
>
> 기본 제공 기술을 실행하는 요금은 기존 [Cognitive Services 종량제 가격](https://azure.microsoft.com/pricing/details/cognitive-services/)으로 청구됩니다. 이미지 추출 가격은 [가격 책정 페이지에](https://go.microsoft.com/fwlink/?linkid=2042400)설명되어 있습니다.
## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.DocumentExtractionSkill

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분합니다.

| 입력            | 허용되는 값 | 설명 |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | 순수 `default` 텍스트 나 json이 아닌 파일에서 문서 추출을 위해 설정합니다. 일반 `text` 텍스트 파일의 성능을 향상시최대로 설정합니다. json `json` 파일에서 구조화 된 콘텐츠를 추출하도록 설정합니다. 명시적으로 `parsingMode` 정의되지 않은 경우 `default`로 설정됩니다. |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | 각 `contentAndMetadata` 파일에서 모든 메타데이터 및 텍스트 콘텐츠를 추출하도록 설정합니다. 콘텐츠 `allMetadata` [유형별 메타데이터(예:](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata) .png 파일만 고유한 메타데이터)만 추출하도록 설정합니다. 명시적으로 `dataToExtract` 정의되지 않은 경우 `contentAndMetadata`로 설정됩니다. |
| `configuration` | 아래를 참조하십시오. | 문서 추출 수행 방법을 조정하는 선택적 매개 변수의 사전입니다. 지원되는 구성 속성에 대한 설명은 아래 표를 참조하십시오. |

| 구성 매개 변수   | 허용되는 값 | 설명 |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | 데이터 `none` 집합에 포함된 이미지 또는 이미지 파일을 무시하도록 설정합니다. 이것이 기본값입니다. <br/>[인지 능력을 이용한 이미지 분석의](cognitive-search-concept-image-scenarios.md)경우, 문서 크래킹의 일환으로 정규화된 이미지의 배열을 생성하도록 `generateNormalizedImages` 설정됩니다. 이 작업을 `parsingMode` 수행하려면 `default` `contentAndMetadata`로 `dataToExtract` 설정되고 로 설정됩니다. 정규화된 이미지는 시각적 검색 결과에 이미지를 포함할 때 일관된 렌더링을 위해 크기 및 회전이 지정되어 균일한 이미지를 출력하는 추가 처리를 의미합니다(예: [JFK 데모](https://github.com/Microsoft/AzureSearch_JFK_Files)에서 볼 수 있는 그래프 컨트롤의 동일 크기 사진). 이 정보는 이 옵션을 사용할 때 각 이미지에 대해 생성됩니다.  <br/>`generateNormalizedImagePerPage`을 설정하면 PDF 파일이 포함된 이미지를 추출하는 대신 각 페이지가 이미지로 렌더링되고 그에 따라 정규화됩니다.  비 PDF 파일 형식은 설정된 것과 `generateNormalizedImages` 동일하게 처리됩니다.
| `normalizedImageMaxWidth` | 50-10000 사이의 모든 정수 | 정규화된 이미지의 최대 너비(픽셀 단위)가 생성되었습니다. 기본값은 2000입니다. | 
| `normalizedImageMaxHeight` | 50-10000 사이의 모든 정수 | 정규화된 이미지의 최대 높이(픽셀 단위)가 생성되었습니다. 기본값은 2000입니다. |

> [!NOTE]
> 정규화된 이미지의 최대 너비와 높이에 대한 2000 픽셀이라는 기본값은 [OCR 기술](cognitive-search-skill-ocr.md) 및 [이미지 분석 기술](cognitive-search-skill-image-analysis.md)에서 지원하는 최대 크기에 기반합니다. [OCR 기술은](cognitive-search-skill-ocr.md) 영어 이외의 언어의 경우 최대 너비와 높이 4200, 영어의 경우 10000을 지원합니다.  최대 제한을 늘리면 기술 집합 정의 및 문서의 언어에 따라 더 큰 이미지에서 처리가 실패할 수 있습니다. 
## <a name="skill-inputs"></a>기술 입력

| 입력 이름     | 설명 |
|--------------------|-------------|
| file_data | 콘텐츠를 추출해야 하는 파일입니다. |

"file_data" 입력은 다음과 같이 정의된 개체여야 합니다.

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

이 파일 참조 개체는 다음 세 가지 방법 중 하나를 생성할 수 있습니다.

 - 인덱서 정의의 매개 변수를 `allowSkillsetToReadFileData` "true"로 설정합니다.  이렇게 하면 Blob 데이터 원본에서 다운로드한 원본 파일 데이터를 나타내는 개체인 경로가 `/document/file_data` 생성됩니다. 이 매개 변수는 Blob 저장소의 데이터에만 적용됩니다.

 - 인덱서 정의의 매개 변수를 `imageAction` 을 `none`제외한 값으로 설정합니다.  이렇게 하면 개별적으로(즉,)를 전달하는 경우 이 기술에 입력하는 데 필요한 `/document/normalized_images/*`규칙을 따르는 이미지 배열이 만들어집니다.

 - 사용자 지정 기술을 갖는 것은 위와 같이 정확하게 정의된 json 객체를 반환합니다.  매개 `$type` 변수는 정확하게 `file` 설정해야 `data` 하며 매개 변수는 파일 콘텐츠의 기본 64 인코딩된 바이트 배열 데이터여야 합니다.

## <a name="skill-outputs"></a>기술 출력

| 출력 이름    | 설명 |
|--------------|-------------|
| content | 문서의 텍스트 내용입니다. |
| normalized_images | 값이 `imageAction` 다른 `none`값으로 설정되면 새 *normalized_images* 필드에 이미지 배열이 포함됩니다. 각 이미지의 출력 형식에 대한 자세한 내용은 [이미지 추출 설명서를](cognitive-search-concept-image-scenarios.md) 참조하십시오. |

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

## <a name="see-also"></a>참조

+ [기본 제공 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [인식 검색 시나리오의 이미지에서 정보를 처리하고 추출하는 방법](cognitive-search-concept-image-scenarios.md)