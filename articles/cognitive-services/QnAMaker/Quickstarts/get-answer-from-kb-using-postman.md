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
ms.date: 12/11/2018
ms.author: diberry
ms.openlocfilehash: 476e982bdddd41c1daf06c3a673d964ce2a85f98
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53270895"
---
# <a name="quickstart-get-an-answer-from-knowledge-base-using-postman"></a>빠른 시작: Postman을 사용하여 기술 자료에서 답변 가져오기

이 Postman 기반 빠른 시작에서는 기술 자료에서 답변을 가져오는 과정을 안내합니다.

## <a name="prerequisites"></a>필수 조건

* 최신 [**Postman**](https://www.getpostman.com/).
* [QnA Maker 서비스](../How-To/set-up-qnamaker-service-azure.md) 및 [질문과 대답을 포함하는 기술 자료](../Tutorials/create-publish-query-in-portal.md)가 있어야 합니다. 

## <a name="publish-to-get-endpoint"></a>게시하여 엔드포인트 가져오기

기술 자료 데이터베이스에서 질문에 대한 대답을 생성할 준비가 되었으면 기술 자료를 [게시](../How-to/publish-knowledge-base.md)합니다.

## <a name="use-production-endpoint-with-postman"></a>Postman과 함께 프로덕션 엔드포인트 사용

기술 자료가 게시되면 **게시** 페이지에 대답을 생성하기 위한 HTTP 요청 설정이 표시됩니다. 기본 보기에는 [Postman](https://www.getpostman.com)에서 대답을 생성하는 데 필요한 설정이 표시됩니다.

[![게시 결과](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png)](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png#lightbox)

Postman을 사용하여 대답을 생성하려면 다음 단계를 완료합니다.

1. Postman을 엽니다. 
1. 기본 요청을 만들려는 문서 블록을 선택합니다.
1. **요청 이름**을 `Generate QnA Maker answer`로 설정하고, **컬렉션**을 `Generate QnA Maker answers`로 설정합니다.
1. 작업 영역에서 HTTP 메서드로 **POST**를 선택합니다.
1. URL에 대해 HOST 값과 POST 값을 연결하여 전체 URL을 만듭니다. 

    [![Postman에서 메서드를 POST 및 전체 URL로 설정합니다.](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png#lightbox)

1. URL 아래의 **헤더** 탭을 선택하고 **일괄 편집**을 선택합니다. 
1. 텍스트 영역에 헤더를 복사합니다.

    [![Postman에서 헤더를 설정합니다.](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png#lightbox)

1. **본문** 탭을 선택합니다.
1. **원시** 형식을 선택하고 질문을 나타내는 JSON을 입력합니다.

    [![Postman에서 본문 JSON 값을 설정합니다.](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png#lightbox)

1. **보내기** 단추를 선택합니다.
1. 응답에는 클라이언트 애플리케이션에 중요할 수 있는 기타 정보와 함께 대답이 포함됩니다. 

    [![Postman에서 본문 JSON 값을 설정합니다.](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png)](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png#lightbox)

## <a name="use-staging-endpoint-with-curl"></a>cURL과 함께 스테이징 엔드포인트 사용

스테이징 엔드포인트에서 대답을 가져오려면 querystring 부울 매개 변수 `isTest`와 `true` 값을 사용합니다.

`isTest=true`

## <a name="next-steps"></a>다음 단계

게시 페이지에서는 cURL과 함께 [대답을 생성](get-answer-from-kb-using-curl.md)하기 위한 정보를 제공합니다. 

> [!div class="nextstepaction"]
> [대답을 생성하는 동안 메타데이터 사용](../How-to/metadata-generateanswer-usage.md)