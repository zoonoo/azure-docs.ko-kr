---
title: '예제: 이미지 분석 API 호출 - Computer Vision'
titlesuffix: Azure Cognitive Services
description: Azure Cognitive Services에서 REST를 사용하여 Computer Vision API를 호출하는 방법을 알아봅니다.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 03/21/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 0e2767660edf2a9dbcb8617b07a6b9f71fedb743
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60011246"
---
# <a name="example-how-to-call-the-computer-vision-api"></a>예제: Computer Vision API를 호출하는 방법

이 가이드에서는 REST를 사용하여 Computer Vision API를 호출하는 방법을 보여 줍니다. 샘플은 Computer Vision API 클라이언트 라이브러리를 사용하여 C#으로 작성되고 HTTP POST/GET 호출로 작성됩니다. 다음을 집중적으로 살펴보겠습니다.

- “태그”, “설명” 및 “범주”를 가져오는 방법
- “도메인 특정” 정보(유명인)를 가져오는 방법

## <a name="prerequisites"></a>필수 조건

- 로컬에 저장된 이미지의 이미지 URL 또는 경로.
- 지원되는 입력 방법: application/octet stream 또는 이미지 URL 형식의 원시 이미지 이진
- 지원되는 이미지 형식: JPEG, PNG, GIF, BMP
- 이미지 파일 크기: 4MB 미만
- 이미지 크기: 50 x 50픽셀 초과
  
아래 예제에서는 다음 기능을 보여 줍니다.

1. 이미지를 분석하고 반환된 태그 및 설명의 배열 가져오기.
2. 도메인별 모델(특히 “유명인” 모델)을 사용하여 이미지를 분석하고 반환된 해당 결과를 JSON으로 가져오기.

기능은 다음을 기준으로 분류됩니다.

- **옵션 1:** 범위 분석 - 제공된 모델만 분석
- **옵션 2:** 고급 분석 - [86개 범주 분류](../Category-Taxonomy.md)를 통해 추가 정보를 제공하도록 분석
  
## <a name="authorize-the-api-call"></a>API 호출 권한 부여

Computer Vision API를 호출할 때마다 구독 키가 필요합니다. 이 키는 쿼리 문자열 매개 변수를 통해 전달되거나 요청 헤더에서 지정되어야 합니다.

평가판 키를 가져오려면 [Cognitive Services 사용해보기](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)를 참조하세요. 또는 [Cognitive Services 계정 만들기](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)의 지침에 따라 Computer Vision을 구독하고 키를 가져옵니다.

1. 쿼리 문자열을 통한 구독 키 전달은 아래 Computer Vision API 예제를 확인합니다.

```https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>```

1. 구독 키 전달은 HTTP 요청 헤더에서 지정될 수도 있습니다.

```ocp-apim-subscription-key: <Your subscription key>```

1. 클라이언트 라이브러리를 사용하는 경우 구독 키는 VisionServiceClient의 생성자를 통해 전달됩니다.

```var visionClient = new VisionServiceClient("Your subscriptionKey");```

## <a name="upload-an-image-to-the-computer-vision-api-service-and-get-back-tags-descriptions-and-celebrities"></a>Computer Vision API 서비스에 이미지를 업로드하고 태그, 설명 및 유명인 다시 가져오기

Computer Vision API 호출을 수행하는 기본적인 방법은 직접 이미지를 업로드하는 것입니다. 이미지에서 데이터 판독과 함께 애플리케이션/옥텟 스트림 콘텐츠 형식으로 “POST” 요청을 보내 수행합니다. “태그” 및 “설명”의 경우 이 업로드 메서드는 모든 Computer Vision API 호출에 동일합니다. 유일한 차이점은 사용자가 지정하는 쿼리 매개 변수입니다. 

제공된 이미지의 “태그” 및 “설명”을 가져오는 방법은 다음과 같습니다.

**옵션 1:** “태그” 목록 및 “설명” 목록 가져오기

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```

```csharp
using Microsoft.ProjectOxford.Vision;
using Microsoft.ProjectOxford.Vision.Contract;
using System.IO;

AnalysisResult analysisResult;
var features = new VisualFeature[] { VisualFeature.Tags, VisualFeature.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.AnalyzeImageAsync(fs, features);
}
```

**옵션 2:** “태그” 목록만 또는 “설명” 목록만 가져오기:

###### <a name="tags-only"></a>태그만:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/tag&subscription-key=<Your subscription key>
var analysisResult = await visionClient.GetTagsAsync("http://contoso.com/example.jpg");
```

###### <a name="description-only"></a>설명만:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/describe&subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.DescribeAsync(fs);
}
```

### <a name="get-domain-specific-analysis-celebrities"></a>도메인 특정 분석 가져오기(유명인)

**옵션 1:** 범위 분석 - 제공된 모델만 분석
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

이 옵션의 경우 모든 다른 쿼리 매개 변수 {visualFeatures, details}가 유효하지 않습니다. 지원되는 모든 모델을 확인하려면 다음을 사용합니다.

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.0/models 
var models = await visionClient.ListModelsAsync();
```

**옵션 2:** 고급 분석 - [86개 범주 분류](../Category-Taxonomy.md)를 통해 추가 정보를 제공하도록 분석

하나 이상 도메인별 모델의 세부 정보 외에 일반 이미지 분석을 가져오려는 애플리케이션의 경우 모델 쿼리 매개 변수를 사용하여 v1 API를 확장합니다.

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?details=celebrities
```

이 메서드를 호출하는 경우 86개 범주 분류자를 먼저 호출합니다. 범주 중 하나라도 알려진/일치 모델의 범주와 일치할 경우 분류자 호출의 두 번째 전달이 발생합니다. 예를 들어 “details=all” 또는 “details”에 ‘celebrities’가 포함된 경우 86개 범주 분류자가 호출된 후 유명인 모델을 호출하고 결과에는 범주 사용자가 포함됩니다. 이렇게 하면 유명인에 관심 있는 사용자의 대기 시간이 옵션 1에 비해 증가합니다.

이 경우 모든 v1 쿼리 매개 변수는 동일하게 동작합니다.  visualFeatures=categories가 지정되지 않으면 암시적으로 사용하도록 설정됩니다.

## <a name="retrieve-and-understand-the-json-output-for-analysis"></a>분석을 위한 JSON 출력 검색 및 이해

예를 들면 다음과 같습니다.

```json
{  
  "tags":[  
    {  
      "name":"outdoor",
      "score":0.976
    },
    {  
      "name":"bird",
      "score":0.95
    }
  ],
  "description":{  
    "tags":[  
      "outdoor",
      "bird"
    ],
    "captions":[  
      {  
        "text":"partridge in a pear tree",
        "confidence":0.96
      }
    ]
  }
}
```

필드 | 유형 | 콘텐츠
------|------|------|
Tags  | `object` | 태그 배열의 최상위 개체
tags[].Name | `string`  | 태그 분류자의 키워드
tags[].Score    | `number`  | 신뢰도 점수, 0~1.
description  | `object` | 설명의 최상위 개체.
description.tags[] |    `string`    | 태그 목록.  캡션을 생성하는 기능에 신뢰도가 부족한 경우 태그는 호출자에게 제공되는 유일한 정보일 수 있습니다.
description.captions[].text | `string`  | 이미지를 설명하는 구.
description.captions[].confidence   | `number`  | 구의 신뢰도.

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>도메인 특정 모델의 JSON 출력 검색 및 이해

**옵션 1:** 범위 분석 - 제공된 모델만 분석

출력은 태그 배열이고, 예제는 다음과 같습니다.

```json
{  
  "result":[  
    {  
      "name":"golden retriever",
      "score":0.98
    },
    {  
      "name":"Labrador retriever",
      "score":0.78
    }
  ]
}
```

**옵션 2:** 고급 분석 - 86개 범주 분류를 통해 추가 정보를 제공하도록 분석

옵션 2(고급 분석)를 사용한 도메인별 모델의 경우 범주 반환 형식이 확장됩니다. 예제는 다음과 같습니다.

```json
{  
  "requestId":"87e44580-925a-49c8-b661-d1c54d1b83b5",
  "metadata":{  
    "width":640,
    "height":430,
    "format":"Jpeg"
  },
  "result":{  
    "celebrities":[  
      {  
        "name":"Richard Nixon",
        "faceRectangle":{  
          "left":107,
          "top":98,
          "width":165,
          "height":165
        },
        "confidence":0.9999827
      }
    ]
  }
}
```

범주 필드는 원래 분류의 [86개 범주](../Category-Taxonomy.md) 중 하나 이상이 포함된 목록입니다. 밑줄로 끝나는 범주는 해당 범주 및 자식과 일치합니다(예: 유명인 모델의 경우 people_ 및 people_group).

필드   | 형식  | 콘텐츠
------|------|------|
categories | `object`   | 최상위 개체
categories[].name    | `string` | 86개 범주 분류의 이름
categories[].score  | `number`  | 신뢰도 점수, 0~1
categories[].detail  | `object?`      | 선택적 세부 정보 개체

여러 범주가 일치하는 경우(예: model=celebrities일 경우 86개 범주 분류자가 people_ 및 people_young의 점수를 둘 다 반환하는 경우) 세부 정보는 가장 일반적인 수준 일치(이 예제에서는 people_)에 연결됩니다.

## <a name="errors-responses"></a>오류 응답

이는 옵션 1 및 옵션 2 시나리오에서 둘 다 반환될 수 있는, NotSupportedModel 오류(HTTP 400)의 추가 오류가 포함된 vision.analyze와 동일합니다. 옵션 2(고급 분석)의 경우 세부 정보에 지정된 모델이 인식되지 않으면 API는 모델 중 하나 이상이 유효하더라도 NotSupportedModel을 반환합니다.  사용자는 listModels를 호출하여 지원되는 모델을 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

REST API를 사용하려면 [Computer Vision API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)로 이동합니다.
