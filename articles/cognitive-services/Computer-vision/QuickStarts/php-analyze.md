---
title: Computer Vision API PHP 빠른 시작 이미지 분석 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 이 빠른 시작에서는 Cognitive Services에서 PHP와 함께 Computer Vision을 사용하여 이미지를 분석합니다.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 114674f47ee945717e866d97ffed747ae45decc8
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43750602"
---
# <a name="quickstart-analyze-a-remote-image---rest-php"></a>빠른 시작: 원격 이미지 분석 - REST, PHP

이 빠른 시작에서는 Computer Vision을 사용하여 이미지를 분석하여 시각적 특징을 추출합니다.

## <a name="prerequisites"></a>필수 조건

Computer Vision을 사용하려면 구독 키가 필요합니다. [구독 키 얻기](../Vision-API-How-to-Topics/HowToSubscribe.md)를 참조하세요.

## <a name="analyze-image-request"></a>이미지 분석 요청

[이미지 분석 메서드](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)를 사용하면 이미지 콘텐츠를 기반으로 시각적 특징을 추출할 수 있습니다. 이미지를 업로드하거나 이미지 URL을 지정하고 다음과 같이 반환할 특징을 선택할 수 있습니다.

* 이미지 콘텐츠와 관련된 자세한 태그 목록
* 완전한 문장으로 된 이미지 콘텐츠에 대한 설명
* 이미지에 포함된 모든 얼굴의 좌표, 성별 및 나이
* ImageType(클립 아트 또는 선 그리기)
* 기조색, 강조색, 이미지 흑백 여부
* 이 [분류](../Category-Taxonomy.md)에 정의된 범주
* 이미지에 성인 또는 성적인 내용이 암시된 콘텐츠가 포함되어 있나요?

샘플을 실행하려면 다음 단계를 수행합니다.

1. 다음 코드를 편집기에 복사합니다.
1. `<Subscription Key>`를 유효한 구독 키로 바꿉니다.
1. 필요한 경우 `uriBase`를 변경하여 구독 키를 가져온 위치를 사용합니다.
1. 필요에 따라 `imageUrl`을 분석할 이미지로 설정합니다.
1. 필요에 따라 응답 언어(`'language' => 'en'`)를 변경합니다.
1. `.php` 확장명으로 파일을 저장합니다.
1. PHP를 지원하는 브라우저 창에서 파일을 엽니다.

이 샘플에서는 PHP5 [HTTP_Request2](http://pear.php.net/package/HTTP_Request2) 패키지를 사용합니다.

```php
<html>
<head>
    <title>Analyze Image Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

$imageUrl = 'http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . '/analyze');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'visualFeatures' => 'Categories,Description',
    'details' => '',
    'language' => 'en'
);
$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body parameters
$body = json_encode(array('url' => $imageUrl));

// Request body
$request->setBody($body);

try
{
    $response = $request->send();
    echo "<pre>" .
        json_encode(json_decode($response->getBody()), JSON_PRETTY_PRINT) . "</pre>";
}
catch (HttpException $ex)
{
    echo "<pre>" . $ex . "</pre>";
}
?>
</body>
</html>
```

## <a name="analyze-image-response"></a>이미지 분석 응답

성공적인 응답이 JSON 형식으로 반환됩니다. 예:

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "requestId": "ebf5a1bc-3ba2-4c56-99b4-bbd20ba28705",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="next-steps"></a>다음 단계

이미지를 분석하고, 유명인 및 랜드마크를 검색하고, 썸네일을 만들고, 인쇄 및 필기 텍스트를 추출하는 데 사용되는 Computer Vision API를 탐색합니다. Computer Vision API를 사용하여 신속하게 실험하려면 [API 테스트 콘솔 열기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)를 사용하세요.

> [!div class="nextstepaction"]
> [Computer Vision API 탐색](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
