---
title: '빠른 시작: Bing Visual Search REST API 및 Node.js를 사용하여 이미지 인사이트 가져오기'
titleSuffix: Azure Cognitive Services
description: 이미지를 Bing Visual Search API에 업로드하고 이미지에 대한 인사이트를 가져오는 방법을 알아봅니다.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 23c3e167f2fd1544a42c98946f4ae95d3451dafe
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55180622"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>빠른 시작: Bing Visual Search REST API 및 Node.js를 사용하여 이미지 인사이트 가져오기

이 빠른 시작을 사용하여 Bing Visual Search API를 처음 호출하고 검색 결과를 확인합니다. 이 간단한 JavaScript 애플리케이션은 API에 이미지를 업로드하고, 이미지에 대한 반환된 정보를 표시합니다. 이 애플리케이션은 JavaScript에서 작성되지만 API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다.

로컬 이미지를 업로드하는 경우 양식 데이터에는 Content-Disposition 헤더가 포함되어야 합니다. 해당 `name` 매개 변수를 "image"로 설정해야 하고 `filename` 매개 변수를 임의의 문자열 매개 변수로 설정할 수 있습니다. 양식의 콘텐츠는 이미지의 이진입니다. 업로드할 수는 최대 이미지 크기는 1MB입니다.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>필수 조건

* [Node.JS](https://nodejs.org/en/download/)
* JavaScript에 대한 요청 모듈
    * `npm install request`를 사용하여 이 모듈을 설치할 수 있습니다.
* form-data 모듈
    * `npm install form-data`를 사용하여 이 모듈을 설치할 수 있습니다.


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]


## <a name="initialize-the-application"></a>애플리케이션 초기화

1. 선호하는 IDE 또는 편집기에서 새 JavaScript 파일을 만들고 다음 요구 사항을 설정합니다.

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. API 엔드포인트, 구독 키에 대한 변수 및 이미지에 대한 경로를 만듭니다.

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. `requestCallback()`이라는 함수를 만들어 API에서 응답을 인쇄합니다.

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>검색 요청 생성 및 보내기

1. `FormData()`를 사용하여 form-data를 만들고, `fs.createReadStream()`을 사용하여 이미지 경로를 추가합니다.
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

2. 요청 라이브러리를 사용하여 응답을 인쇄하도록 `requestCallback()`을 호출하는 이미지를 업로드합니다. 요청 헤더에 구독 키를 추가해야 합니다. 

    ```javascript
    form.getLength(function(err, length){
      if (err) {
        return requestCallback(err);
      }
      var r = request.post(baseUri, requestCallback);
      r._form = form; 
      r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
    });
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Custom Search 웹앱 빌드](../tutorial-bing-visual-search-single-page-app.md)
