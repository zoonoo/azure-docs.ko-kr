---
title: '빠른 시작: Postman을 사용하여 기술 자료에서 답변 가져오기 - QnA Maker'
titlesuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Postman을 사용하여 기술 자료에서 답변을 가져오는 과정을 안내합니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 01/03/2019
ms.author: diberry
ms.openlocfilehash: 75732a3ea78a89c06063102d37b77f315a3bcf62
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2019
ms.locfileid: "54001807"
---
# <a name="quickstart-get-an-answer-from-knowledge-base-using-postman"></a>빠른 시작: Postman을 사용하여 기술 자료에서 답변 가져오기

이 Postman 기반 빠른 시작에서는 기술 자료에서 답변을 가져오는 과정을 안내합니다.

## <a name="prerequisites"></a>필수 조건

* 최신 [**Postman**](https://www.getpostman.com/).
* [QnA Maker 서비스](../How-To/set-up-qnamaker-service-azure.md) 및 [질문과 대답을 포함하는 기술 자료](../Tutorials/create-publish-query-in-portal.md)가 있어야 합니다. 

## <a name="publish-to-get-endpoint"></a>엔드포인트를 가져오기 위한 게시

기술 자료 데이터베이스에서 질문에 대한 대답을 생성할 준비가 되었으면 기술 자료를 [게시](../How-to/publish-knowledge-base.md)합니다.

## <a name="use-production-endpoint-with-postman"></a>Postman과 함께 프로덕션 엔드포인트 사용

기술 자료가 게시되면 **게시** 페이지에 대답을 생성하기 위한 HTTP 요청 설정이 표시됩니다. 기본 보기에는 [Postman](https://www.getpostman.com)에서 대답을 생성하는 데 필요한 설정이 표시됩니다.

다음 이미지의 노란색 숫자는 다음 단계에서 사용할 이름/값 쌍을 나타냅니다.

[![게시 결과](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png)](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png#lightbox)

Postman을 사용하여 대답을 생성하려면 다음 단계를 완료합니다.

1. Postman을 엽니다. 구성 요소를 선택하도록 요청한 경우 **기본 요청** 구성 요소를 선택합니다. **요청 이름**을 `Generate QnA Maker answer`로 설정하고, **컬렉션**을 `Generate QnA Maker answers`로 설정합니다. 컬렉션에 저장하지 않으려는 경우 **취소** 단추를 선택합니다.
1. 작업 영역에서 HTTP 메서드로 **POST**를 선택합니다.

    [![Postman에서 POST 메서드 설정](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png)](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png#lightbox)

1. URL에 대해 HOST 값(이미지의 #2)과 POST 값(이미지의 #1)을 연결하여 전체 URL을 만듭니다. 전체 예제 URL은 다음과 같습니다. 

    `https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/e1115f8c-d01b-4698-a2ed-85b0dbf3348c/generateAnswer`

    [![Postman에서 전체 URL 설정](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png#lightbox)

1. URL 아래의 **헤더** 탭을 선택하고 **일괄 편집**을 선택합니다. 

1. 텍스트 영역(이미지의 #3 및 #4)에 헤더를 복사합니다.

    [![Postman에서 헤더를 설정합니다.](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png#lightbox)

1. **본문** 탭을 선택합니다.
1. **원시** 형식을 선택하고 질문을 나타내는 JSON(이미지의 #5)을 입력합니다.

    `{"question":"How do I programmatically update my Knowledge Base?"}`

    [![Postman에서 본문 JSON 값을 설정합니다.](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png#lightbox)

1. **보내기** 단추를 선택합니다.
1. 응답에는 클라이언트 애플리케이션에 중요할 수 있는 기타 정보와 함께 대답이 포함됩니다. 

    [![Postman에서 본문 JSON 값을 설정합니다.](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png)](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png#lightbox)

## <a name="use-staging-endpoint"></a>준비 엔드포인트 사용

준비 엔드포인트에서 대답을 가져오려면 값이 `true`인 `isTest` 쿼리 문자열 부울 매개 변수를 통해 URL을 추가합니다.

`?isTest=true`

## <a name="next-steps"></a>다음 단계

게시 페이지에서는 cURL과 함께 [대답을 생성](get-answer-from-kb-using-curl.md)하기 위한 정보를 제공합니다. 

> [!div class="nextstepaction"]
> [대답을 생성하는 동안 메타데이터 사용](../How-to/metadata-generateanswer-usage.md)