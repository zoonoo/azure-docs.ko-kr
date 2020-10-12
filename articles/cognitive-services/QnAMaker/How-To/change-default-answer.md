---
title: 기본 대답 가져오기-QnA Maker
description: 질문과 일치 하는 항목이 없는 경우 기본 대답이 반환 됩니다. 표준 기본 응답에서 기본 대답을 변경 하는 것이 좋습니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: how-to
ms.date: 07/13/2020
ms.openlocfilehash: 14954f89fcdcbbc1ef4b8654582a3274f4bb0923
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776819"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>QnA Maker 리소스에 대 한 기본 응답 변경

답변을 찾을 수 없을 때 기술 자료에 대 한 기본 답은 반환 됩니다. [Azure Bot service](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs#calling-qna-maker-from-your-bot)와 같은 클라이언트 응용 프로그램을 사용 하는 경우 점수 임계값을 충족 하는 답변이 없음을 나타내는 별도의 기본 응답이 있을 수도 있습니다.

## <a name="types-of-default-answer"></a>기본 응답 형식

기술 자료에는 두 가지 유형의 기본 대답이 있습니다. 예측 쿼리에서 각을 반환 하는 방법과 시기를 이해 하는 것이 중요 합니다.


|질문의 유형|답변에 대 한 설명|
|--|--|
|답변이 결정 되지 않은 경우 응답 (KB)|`No good match found in KB.` - [GENERATEANSWER API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) 가 질문에 대 한 일치 하는 대답을 찾지 못하면 `DefaultAnswer` App service의 설정이 반환 됩니다. 동일한 QnA Maker 리소스의 모든 기술 자료가 동일한 기본 대답 텍스트를 공유 합니다.<br>설정을 가져오거나 [업데이트](https://docs.microsoft.com/rest/api/appservice/webapps/updateapplicationsettings) [하기 위한 REST](https://docs.microsoft.com/rest/api/appservice/webapps/listapplicationsettings) api 또는 App service를 통해 Azure Portal에서 설정을 관리할 수 있습니다.|
|후속 프롬프트 명령 텍스트|대화 흐름에서 추가 작업 프롬프트를 사용 하는 경우 사용자가 추가 작업 프롬프트에서 선택 하기 때문에 QnA 쌍의 응답이 필요 하지 않을 수 있습니다. 이 경우 추가 작업 프롬프트에 대해 각 예측이 반환 되는 기본 대답 텍스트를 설정 하 여 특정 텍스트를 설정 합니다. 텍스트는 추가 작업 프롬프트를 선택 하는 데 지침 텍스트로 표시 됩니다. 이 기본 대답 텍스트의 예는 `Please select from the following choices` 입니다. 이 구성은이 문서의 다음 섹션에 설명 되어 있습니다. 는 REST API를 사용 하는 기술 자료 정의의 일부로 설정할 수도 있습니다 `defaultAnswerUsedForExtraction` . [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)|

### <a name="client-application-integration"></a>클라이언트 응용 프로그램 통합

**Azure bot service**와 같은 클라이언트 응용 프로그램의 경우 다음과 같은 일반적인 시나리오에서 선택할 수 있습니다.

* 기술 자료의 설정 사용
* 클라이언트 응용 프로그램에서 다른 텍스트를 사용 하 여 응답이 반환 되는 경우와 점수 임계값을 충족 하지 않는 경우를 구분 합니다. 이 텍스트는 코드에 저장 된 정적 텍스트 이거나 클라이언트 응용 프로그램의 설정 목록에 저장 될 수 있습니다.

## <a name="set-follow-up-prompts-default-answer-when-you-create-knowledge-base"></a>기술 자료를 만들 때 후속 프롬프트의 기본 대답 설정

새 기술 자료를 만들 때 기본 대답 텍스트는 설정 중 하나입니다. 만들기 프로세스 중에 설정 하지 않도록 선택 하는 경우 나중에 다음 절차를 수행 하 여 변경할 수 있습니다.

## <a name="change-follow-up-prompts-default-answer-in-qna-maker-portal"></a>QnA Maker 포털에서 추가 작업 프롬프트의 기본 대답 변경

QnA Maker 서비스에서 응답이 반환 되지 않으면 기술 자료 기본 응답이 반환 됩니다.

1. [QnA Maker 포털](https://www.qnamaker.ai/) 에 로그인 하 고 목록에서 기술 자료를 선택 합니다.
1. 탐색 모음에서 **설정** 을 선택 합니다.
1. **기술 자료 관리** 섹션에서 **기본 대답 텍스트** 의 값을 변경 합니다.

    :::image type="content" source="../media/qnamaker-concepts-confidencescore/change-default-answer.png" alt-text="기본 답변 텍스트 상자가 강조 표시 된 QnA Maker 포털, 설정 페이지의 스크린샷":::

1. **저장 및 학습** 을 선택 하 여 변경 내용을 저장 합니다.

## <a name="next-steps"></a>다음 단계

* [기술 자료 만들기](../How-to/manage-knowledge-bases.md)
