---
title: Conversation Learner 응용 프로그램에서 엔터티를 사용하는 방법 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 응용 프로그램에서 엔터티를 사용하는 방법을 알아봅니다.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 85df31c2e2ff3ca81698921a1f17f415daefb6c5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376231"
---
# <a name="introduction-to-entities"></a>엔터티 소개

이 자습서에서는 엔터티를 소개하고, 작업에서 “Disqualifying(실격) 엔터티” 및 “필수 엔터티” 필드를 사용하는 방법을 보여 줍니다.

## <a name="requirements"></a>요구 사항

이 자습서를 수행하려면 일반 자습서 봇이 실행 중이어야 합니다.

    npm run tutorial-general

## <a name="details"></a>세부 정보

이 자습서에서는 엔터티의 두 가지 일반적인 사용에 대해 설명합니다.  첫째, 엔터티는 “what's the weather in Seattle?”에서 도시 식별 등 사용자 메시지에서 하위 문자열을 추출할 수 있습니다.  둘째, 엔터티는 작업을 사용할 수 있는 경우를 제한할 수 있습니다.  특히 작업은 엔터티를 “필수” 또는 “disqualifying”(실격)으로 나열할 수 있습니다.
- 작업을 사용할 수 있으려면 작업의 필수 엔터티가 봇의 메모리에 있어야 합니다.
- 작업을 사용할 수 있으려면 Disqualifying(실격) 엔터티가 봇의 메모리에 *없어야* 합니다.

기타 자습서에서는 미리 빌드된 엔터티, 다중값 및 부정 가능 엔터티, 프로그래밍 엔터티, 코드에서 엔터티 조작 등 엔터티의 다양한 측면을 다룹니다.

## <a name="steps"></a>단계

### <a name="create-the-application"></a>응용 프로그램 만들기

1. Web UI에서 새 앱을 클릭합니다.
2. 이름에 IntroToEntities를 입력합니다. 만들기를 클릭합니다.

### <a name="create-entity"></a>엔터티 만들기

1. 엔터티, 새 엔터티를 차례로 클릭합니다.
2. 엔터티 이름에 city를 입력합니다.
3. 만들기 클릭

엔터티 형식은 ‘custom’(사용자 지정)으로, 엔터티를 학습할 수 있음을 의미합니다.  동작을 조정할 수 없는 미리 빌드된 엔터티도 있습니다. 이러한 엔터티는 다른 자습서에서 설명합니다.

### <a name="create-two-actions"></a>두 가지 작업 만들기

1. 작업, 새 작업을 차례로 클릭합니다.
2. 응답에 ‘I don't know what city you want’를 입력합니다.
3. Disqualifying(실격) 엔터티에 $city를 입력합니다. 저장을 클릭합니다.
    - 이 엔터티가 봇의 메모리에 정의되어 있으면 이 작업을 사용할 수 *없음*을 의미합니다.
2. 작업, 새 작업을 차례로 클릭하여 두 번째 작업을 만듭니다.
3. 응답에 ‘The weather in the $city is probably sunny’를 입력합니다.
4. 필수 엔터티에서 city 엔터티가 참조되어 자동으로 추가된 것을 확인합니다.
5. 저장을 클릭합니다.

이제 두 가지 작업이 있습니다.

![](../media/tutorial3_actions.PNG)

### <a name="train-the-bot"></a>봇 학습

1. Train Dialogs(학습 대화), New Train Dialog(새 학습 대화)를 차례로 클릭합니다.
2. ‘hello’를 입력합니다.
3. Score Actions(작업에 점수 지정)를 클릭하고 ‘I don't know what city you want?’를 선택합니다.
    - city 엔터티가 봇의 메모리에 정의되어 있지 않으므로 city 엔터티가 필수인 응답은 선택할 수 없습니다.
2. ‘I don't know what city you want’를 선택합니다.
4. ‘seattle’을 입력합니다. seattle을 강조 표시하고 city를 클릭합니다.
5. Score Actions(작업에 점수 지정)를 클릭합니다.
    - 이제 city 값이 봇의 메모리에 있습니다.
    - ‘Weather in $city is probably sunny’를 응답으로 사용할 수 있습니다. 
6. ‘Weather in $city is probably sunny’를 선택합니다.

사용자가 ‘repeat that’을 입력한다고 가정합니다. 
1. 해당 문구를 입력하고 Enter 키를 누릅니다. city 엔터티와 해당 값이 메모리에 있고 사용할 수 있습니다.
2. ‘Weather in $city is probably sunny’를 선택합니다.

![](../media/tutorial3_entities.PNG)

이제 엔터티를 만들고 사용자 메시지에서 해당 인스턴스에 레이블을 지정했습니다.  또한 봇의 메모리에서 엔터티 있음/없음을 사용하여 작업의 disqualifying(실격) 및 필수 엔터티 필드를 통해 작업을 사용할 수 있는 경우를 제어했습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [예상 엔터티](./4-expected-entity.md)
