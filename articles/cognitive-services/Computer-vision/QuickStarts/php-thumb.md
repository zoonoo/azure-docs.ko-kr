---
title: Computer Vision API PHP 빠른 시작 만들기 썸네일 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 이 빠른 시작에서는 Cognitive Services에서 PHP와 함께 Computer Vision을 사용하여 이미지에서 썸네일을 생성합니다.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 7170e469d042c4406a555fddaa25bff53236f365
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43750680"
---
# <a name="quickstart-generate-a-thumbnail---rest-php"></a>빠른 시작: 썸네일 생성 - REST, PHP

이 빠른 시작에서는 Computer Vision을 사용하여 이미지에서 썸네일을 생성합니다.

## <a name="prerequisites"></a>필수 조건

Computer Vision을 사용하려면 구독 키가 필요합니다. [구독 키 얻기](../Vision-API-How-to-Topics/HowToSubscribe.md)를 참조하세요.

## <a name="get-thumbnail-request"></a>썸네일 가져오기 요청

[썸네일 가져오기 메서드](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb)를 사용하여 이미지의 썸네일을 생성할 수 있습니다. 입력 이미지의 가로 세로 비율과 다를 수 있는 높이와 너비를 지정합니다. Computer Vision은 스마트 자르기를 사용하여 관심 지역을 지능적으로 식별하고, 해당 지역을 기반으로 자르기 좌표를 생성합니다.

샘플을 실행하려면 다음 단계를 수행합니다.

1. 다음 코드를 편집기에 복사합니다.
1. `<Subscription Key>`를 유효한 구독 키로 바꿉니다.
1. 필요한 경우 `uriBase`를 변경하여 구독 키를 가져온 위치를 사용합니다.
1. 필요에 따라 `imageUrl`을 분석할 이미지로 설정합니다.
1. 확장명이 `.php`인 파일을 저장합니다.
1. PHP를 지원하는 브라우저 창에서 파일을 엽니다.

이 샘플에서는 PHP5 [HTTP_Request2](http://pear.php.net/package/HTTP_Request2) 패키지를 사용합니다.

```php
<html>
<head>
    <title>Get Thumbnail Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

$imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . 'generateThumbnail');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'width' => '100',  // Width of the thumbnail.
    'height' => '100', // Height of the thumbnail.
    'smartCropping' => 'true',
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

## <a name="get-thumbnail-response"></a>썸네일 가져오기 응답

성공적인 응답에는 썸네일 이미지 이진 파일이 포함됩니다. 요청에 실패하면 무엇이 잘못되었는지 확인할 수 있도록 응답에 오류 코드 및 메시지가 포함됩니다.

## <a name="next-steps"></a>다음 단계

Computer Vision API를 탐색하여 이미지를 분석하고, 유명인 및 랜드마크를 검색하고, 썸네일을 만들고, 인쇄되고 필기된 텍스트를 추출합니다. Computer Vision API를 사용하여 신속하게 실험하려면 [API 테스트 콘솔 열기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)를 사용하세요.

> [!div class="nextstepaction"]
> [Computer Vision API 탐색](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
