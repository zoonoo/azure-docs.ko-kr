---
title: Bing Visual Search API에 대한 JavaScript 빠른 시작 | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Bing Visual Search API에 이미지를 업로드하고 이미지에 대한 중요 정보를 다시 얻는 방법을 보여 줍니다.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 60b1dc9b8ea9eda258e9776b8967df38c97d964e
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071706"
---
# <a name="your-first-bing-visual-search-query-in-javascript"></a>JavaScript의 첫 번째 Bing Visual Search 쿼리

Bing Visual Search API는 사용자가 제공하는 이미지에 대한 정보를 반환합니다. 이미지의 URL, 인사이트 토큰을 사용하거나 이미지를 업로드하여 이미지를 제공할 수 있습니다. 이러한 옵션에 대한 내용은 [Bing Visual Search API란?](../overview.md)을 참조하세요. 이 아티클에서는 이미지 업로드 방법을 보여줍니다. 이미지 업로드는 잘 알려진 이정표 사진을 찍은 후 해당 정보를 다시 얻는 모바일 시나리오에서 유용할 수 있습니다. 예를 들어, 중요 정보에는 이정표에 대한 잡학 지식이 포함될 수 있습니다. 

로컬 이미지를 업로드하는 경우 다음은 POST 본문에 포함해야 하는 양식 데이터를 보여줍니다. 양식 데이터에는 Content-Disposition 헤더가 포함되어야 합니다. 해당 `name` 매개 변수를 "image"로 설정해야 하고 `filename` 매개 변수를 임의의 문자열 매개 변수로 설정할 수 있습니다. 양식의 콘텐츠는 이미지의 이진입니다. 업로드할 수는 최대 이미지 크기는 1MB입니다. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

이 문서에는 Bing Visual Search API 요청을 보내고 JSON 검색 결과를 표시하는 간단한 콘솔 응용 프로그램이 포함되어 있습니다. JavaScript에서 이 응용 프로그램이 작성되는 반면 API는 RESTful 웹 서비스로서 HTTP를 요청하고 JSON을 구문 분석할 수 있는 모든 프로그래밍 언어와 호환됩니다. 

## <a name="prerequisites"></a>필수 조건

이 코드를 실행하려면 [Node.js 6](https://nodejs.org/en/download/)이 필요합니다.

이 빠른 시작에서는 [평가판](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) 구독 키 또는 유료 구독 키를 사용할 수 있습니다.

## <a name="running-the-application"></a>응용 프로그램 실행

다음에서는 Node.js의 FormData를 사용하여 메시지를 보내는 방법을 보여 줍니다.

이 응용 프로그램을 실행하려면 다음 단계를 따릅니다.

1. 프로젝트에 대한 폴더를 만듭니다(또는 즐겨찾는 IDE 또는 편집기 사용).
2. 명령 프롬프트 또는 터미널에서, 방금 만든 폴더로 이동합니다.
3. 요청 모듈을 설치합니다.  
  ```  
  npm install request  
  ```  
3. 다음과 같이 form-data 모듈을 설치합니다.  
  ```  
  npm install form-data  
  ```  
4. GetVisualInsights.js라는 파일을 만들고 다음 코드를 추가합니다.
5. `subscriptionKey` 값을 구독 키로 바꿉니다.
6. `imagePath` 값을 업로드할 이미지의 경로로 바꿉니다.
7. 프로그램을 실행합니다.  
  ```
  node GetVisualInsights.js
  ```

```javascript
var request = require('request');
var FormData = require('form-data');
var fs = require('fs');

var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
var subscriptionKey = '<yoursubscriptionkeygoeshere>';
var imagePath = "<pathtoyourimagegoeshere>";

var form = new FormData();
form.append("image", fs.createReadStream(imagePath));

form.getLength(function(err, length){
  if (err) {
    return requestCallback(err);
  }

  var r = request.post(baseUri, requestCallback);
  r._form = form; 
  r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
});

function requestCallback(err, res, body) {
    console.log(JSON.stringify(JSON.parse(body), null, '  '))
}
```


## <a name="next-steps"></a>다음 단계

[인사이트 토큰을 사용하여 이미지에 대한 중요 정보 얻기](../use-insights-token.md)  
[Bing Visual Search 이미지 업로드 자습서](../tutorial-visual-search-image-upload.md)
[Bing Visual Search 단일 페이지 앱 자습서](../tutorial-bing-visual-search-single-page-app.md)  
[Bing Visual Search 개요](../overview.md)  
[사용해보기](https://aka.ms/bingvisualsearchtryforfree)  
[평가판 액세스 키 받기](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Bing Visual Search API 참조](https://aka.ms/bingvisualsearchreferencedoc)
