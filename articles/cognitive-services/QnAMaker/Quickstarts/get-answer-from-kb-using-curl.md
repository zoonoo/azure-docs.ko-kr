---
title: '빠른 시작: cURL을 사용하여 기술 자료에서 답변 가져오기 - QnA Maker'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 cURL을 사용하여 기술 자료에서 답변을 가져오는 과정을 안내합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 12/04/2018
ms.author: diberry
ms.openlocfilehash: b269785d41931ce68827e2b48cc543d35ee47597
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55870285"
---
# <a name="quickstart-get-answer-from-knowledge-base-using-curl"></a>빠른 시작: cURL을 사용하여 기술 자료에서 답변 가져오기

이 cURL 기반 빠른 시작에서는 기술 자료에서 답변을 가져오는 과정을 안내합니다.

## <a name="prerequisites"></a>필수 조건

* 최신 [**cURL**](https://curl.haxx.se/)
* [QnA Maker 서비스](../How-To/set-up-qnamaker-service-azure.md) 및 [질문과 대답이 있는 기술 자료](../Tutorials/create-publish-query-in-portal.md)가 있어야 합니다.

## <a name="publish-to-get-endpoint"></a>엔드포인트를 가져오기 위한 게시

기술 자료에서 질문에 대한 대답을 생성할 준비가 되면 기술 자료를 [게시](../How-to/publish-knowledge-base.md)합니다.

## <a name="use-production-endpoint-with-curl"></a>cURL을 통해 프로덕션 엔드포인트 사용

기술 자료가 게시되면 **게시** 페이지에 대답을 생성하기 위한 HTTP 요청 설정이 표시됩니다. **CURL** 탭에는 [CURL](https://www.getpostman.com) 명령줄 도구에서 대답을 생성하는 데 필요한 설정이 표시됩니다.

[![게시 결과](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png)](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png#lightbox)

CURL을 사용하여 대답을 생성하려면 다음 단계를 수행합니다.

1. CURL 탭의 텍스트를 복사합니다. 
1. 명령줄 또는 터미널을 열고 텍스트를 붙여넣습니다.
1. 기술 자료와 관련된 질문을 편집합니다. 질문 주위에 포함된 JSON을 제거하지 않도록 주의하세요.
1. 명령을 입력합니다. 
1. 응답에는 대답에 대한 관련 정보가 포함됩니다. 

    ```bash
    > curl -X POST https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/1111f8c-d01b-4698-a2de-85b0dbf3358c/generateAnswer -H "Authorization: EndpointKey 111841fb-c208-4a72-9412-03b6f3e55ca1" -H "Content-type: application/json" -d "{'question':'How do I programmatically update my Knowledge Base?'}"
    {
      "answers": [
        {
          "questions": [
            "How do I programmatically update my Knowledge Base?"
          ],
          "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600",
          "score": 100.0,
          "id": 18,
          "source": "Custom Editorial",
          "metadata": [
            {
              "name": "category",
              "value": "api"
            }
          ]
        }
      ]
    }
    ```

## <a name="use-staging-endpoint-with-curl"></a>cURL을 통해 준비 엔드포인트 사용

준비 엔드포인트에서 대답을 가져오려면 값이 `true`인 `isTest` 쿼리 문자열 부울 매개 변수를 사용합니다.

`isTest=true`

## <a name="next-steps"></a>다음 단계

게시 페이지에는 Postman을 사용하여 [대답을 생성](get-answer-from-kb-using-postman.md)하기 위한 정보도 제공됩니다. 

> [!div class="nextstepaction"]
> [대답을 생성하는 동안 메타데이터 사용](../How-to/metadata-generateanswer-usage.md)
