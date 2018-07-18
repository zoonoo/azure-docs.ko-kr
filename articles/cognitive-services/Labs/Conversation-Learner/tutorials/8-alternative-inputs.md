---
title: Conversation Learner에서 대체 입력을 사용하는 방법 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner에서 대체 입력을 사용하는 방법을 알아봅니다.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 2214436b193932e5b3b80c190f7754a0436b7ed8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376390"
---
# <a name="how-to-use-alternative-inputs"></a>대체 입력을 사용하는 방법

이 자습서는 학습 인터페이스에서 “대체 입력” 필드를 사용자 입력에 사용하는 방법을 보여 줍니다.

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 일반 자습서 봇이 실행 중이어야 합니다.

    npm run tutorial-general

## <a name="details"></a>세부 정보
“대체 입력”은 사용자가 학습 대화의 특정 시점에서 말했을 수 있는 대체 사용자 발언입니다. 대체 입력을 사용하면 각 변형을 새 학습 대화에 나열하지 않고도 사용자가 말할 수 있는 항목의 변형을 보다 간결하게 지정할 수 있습니다.

## <a name="steps"></a>단계

### <a name="create-the-application"></a>응용 프로그램 만들기

1. Web UI에서 새 앱을 클릭합니다.
2. 이름에 AlternativeInputs를 입력합니다. 만들기를 클릭합니다.

### <a name="create-an-entity"></a>엔터티 만들기

1. 엔터티, 새 엔터티를 차례로 클릭합니다.
2. 엔터티 이름에 city를 입력합니다.
3. 만들기를 클릭합니다.

### <a name="create-three-actions"></a>세 가지 작업 만들기

1. 작업, 새 작업을 차례로 클릭합니다.
2. 응답에 'Which city do you want?'를 입력합니다.
3. Disqualifying(실격) 엔터티에 $city를 입력합니다.
3. 만들기 클릭

그런 다음, 두 번째 작업을 만듭니다.

1. 작업, 새 작업을 차례로 클릭합니다.
3. 응답에 ‘The weather in the $city is probably sunny’를 입력합니다.
4. 필수 엔터티, $city를 입력합니다.
4. 만들기를 클릭합니다.

세 번째 작업을 만듭니다.

1. 작업, 새 작업을 차례로 클릭합니다.
3. 응답에 ‘Try asking for the weather’를 입력합니다.
    - ‘what can the system do?’와 같은 사용자 질문에 대한 응답에 포함됩니다.
4. Disqualifying(실격) 엔터티에 $city를 입력합니다.
4. 만들기 클릭

이제 세 가지 작업이 있습니다.

### <a name="train-the-bot"></a>봇 학습

1. Train Dialogs(학습 대화), New Train Dialog(새 학습 대화)를 차례로 클릭합니다.
2. ‘what's the weather’를 입력합니다.
3. Score Actions(작업에 점수 지정)를 클릭하고 ‘Which city do you want?’를 선택합니다.
2. ‘denver’를 입력합니다.
3. ‘denver’를 두 번 클릭하고 도시를 선택합니다.
    - 그러면 도시 엔터티로 표시됩니다.
5. Score Actions(작업에 점수 지정)를 클릭합니다.
    - denver는 현재 도시 엔터티에 있습니다. 
6. 'The weather in $city is probably sunny'를 선택합니다.
7. 학습 완료를 클릭합니다.

또 다른 예제 대화 상자를 추가합니다.

1. 새 작업, 새 학습 대화 상자를 차례로 클릭합니다.
2. ‘what can you do?’를 입력합니다.
3. Score Actions(작업에 점수 지정)를 클릭하고 ‘Try asking for the weather’를 선택합니다.
2. ‘What's the weather in seattle’을 입력합니다.
3. ‘seattle’을 두 번 클릭하고 도시를 선택합니다.
    - 그러면 도시 엔터티로 표시됩니다.
5. Score Actions(작업에 점수 지정)를 클릭합니다.
    - seattle은 현재 도시 엔터티에 있습니다. 
6. 'The weather in $city is probably sunny'를 선택합니다.
7. 학습 완료를 클릭합니다.

의미 체계가 위와 유사한 내용을 사용자가 말하면 어떻게 되는지 살펴보겠습니다.

1. 새 작업, 새 학습 대화 상자를 차례로 클릭합니다.
2. ‘help’를 입력합니다.
3. Score Actions(작업에 점수 지정)를 클릭합니다.
    - 두 가지 잠재적 응답의 점수가 매우 가깝습니다. 이는 모델에서 두 작업 간의 경계가 혼동됨을 알려 줍니다.
6. Abandon Teaching and Confirm(학습 중단 및 확인)을 클릭합니다.

![](../media/tutorial8_closescores.png)

이 경우 대화에 대체 입력을 추가하면 도움이 됩니다. 학습을 수행하면서 추가할 수 있습니다. 나중에 돌아가서 추가할 수도 있습니다.

2. 학습 대화에서 ‘What can you do?’를 클릭합니다.
2. 대화에서 ‘what can you do?’를 클릭하여 선택합니다.
    1. 오른쪽 창에서 엔터티 검색 아래의 대체 입력 추가에 몇 가지 대안을 입력합니다.
    1. ‘what are my choices?’를 입력합니다.
    2. ‘Tell me my choices’를 입력합니다.
    3. ‘help’를 입력합니다.
    1. 변경 내용 제출을 클릭합니다.


![](../media/tutorial8_helpalternates.png)

2. 이제 ‘what's the weather in seattle’을 클릭합니다.
    1. 대체 입력 추가에 ‘forecast for seattle’을 입력합니다.
    2. ‘seattle’을 두 번 클릭하고 도시를 선택합니다. 대체 입력에 대한 엔터티가 있고 동일한 엔터티 집합을 포함해야 합니다. 엔터티의 콘텐츠는 달라도 됩니다.
    3. 대체 입력 추가에 ‘will it rain today in denver’를 입력합니다.
    4. ‘denver’를 클릭하고 도시를 선택합니다.
    5. 변경 내용 제출 및 완료를 클릭합니다.


첫 번째 대화에 대체 입력을 추가하겠습니다.

1. 학습 대화를 클릭합니다.
2. ‘what's the weather’로 시작하는 대화를 클릭합니다.
2. 왼쪽 창에서 ‘what's the weather’를 클릭하여 선택합니다.
    1. 대체 입력 추가에 ‘weather forecast’를 입력합니다.
    2. ‘will it rain?’을 입력합니다.
    3. 변경 내용 제출을 클릭합니다.
4. 왼쪽 창에서 ‘denver’를 클릭하여 선택합니다.
    1. 대체 입력 추가에 ‘'for denver’를 입력합니다.
    2. ‘forecast for austin’을 입력합니다.
        - 전체 문구가 강조 표시됩니다. 문구를 클릭한 다음, 빨간색 x를 클릭합니다. austin을 선택하고 도시를 클릭합니다.
        - 변경 내용 제출을 클릭합니다.
    1. 완료를 클릭하면 모델이 다시 학습됩니다.

![](../media/tutorial8_altcities.png)

다음과 같이 변형을 시도해 보겠습니다.

1. 새 학습 대화를 클릭합니다.
2. ‘what are you capabilities’를 입력합니다.
3. Score Actions(작업에 점수 지정)를 클릭합니다.
    - 이제 점수가 다음 작업을 더 명확하게 식별하며, 이는 모델의 확실성을 나타냅니다.
2. ‘Try asking for weather’를 선택합니다.
6. 학습 완료를 클릭합니다.

지금까지 대체 입력을 사용하여 사용자가 말했을 수 있는 다른 항목을 나타내는 방법을 살펴보았습니다. 대체 입력을 통해 단일 대화로 축소하고 사용자가 말할 수 있는 항목을 열거하면 여러 측면에서 동일한 대화를 많이 만들지 않도록 방지할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [로그 대화](./9-log-dialogs.md)
