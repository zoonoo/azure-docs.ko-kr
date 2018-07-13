---
title: 미리 빌드된 엔터티를 Conversation Learner 응용 프로그램에 추가하는 방법 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: 미리 빌드된 엔터티를 Conversation Learner 응용 프로그램에 추가하는 방법을 알아봅니다.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f014464419bfac39a9e57e679fcd28a737e9ebdb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377679"
---
# <a name="how-to-add-pre-built-entities"></a>미리 빌드된 엔터티를 추가하는 방법
이 자습서에서는 미리 빌드된 엔터티를 Conversation Learner 응용 프로그램에 추가하는 방법을 보여 줍니다.

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 일반 자습서 봇이 실행 중이어야 합니다.

    npm run tutorial-general

## <a name="details"></a>세부 정보

미리 빌드된 엔터티는 숫자, 날짜, 금액 등과 같은 일반적인 형식의 엔터티를 인식합니다.  사용자 지정 항목과 달리, "바로 사용할 수 있으며" 교육이 필요하지 않습니다.  사용자 지정 항목과 달리, 해당 동작을 변경할 수 없습니다.  기본적으로 미리 빌드된 엔터티는 다중 값으로 이루어집니다. 즉, 봇의 메모리는 엔터티의 식별된 모든 인스턴스를 누적해서 저장합니다.

## <a name="steps"></a>단계

### <a name="create-the-application"></a>응용 프로그램 만들기

1. Web UI에서 새 앱을 클릭합니다.
2. 이름에 BuiltInEntities를 입력합니다. 그런 후 만들기를 클릭합니다.

### <a name="create-an-entity"></a>엔터티 만들기

1. 엔터티, 새 엔터티를 차례로 클릭합니다.
2. EntityType 드롭다운 목록을 클릭하고 datetimev2를 선택합니다.
    - 프로그래밍 가능 및 무효화 가능 옵션은 미리 빌드된 엔터티에 적용되지 않으므로 사용되지 않도록 설정됩니다.
3. 만들기를 클릭합니다.

![](../media/tutorial7_entities.PNG)

### <a name="create-two-actions"></a>두 작업 만들기

1. 작업, 새 작업을 차례로 클릭합니다.
2. 응답에 'The date is $luis-datetimev2'를 입력합니다.
3. 만들기를 클릭합니다.

![](../media/tutorial7_actions.PNG)

그런 다음, 두 번째 작업을 만듭니다.

1. 작업, 새 작업을 차례로 클릭하여 두 번째 작업을 만듭니다.
3. 응답에 'What's the date?'를 입력합니다.
4. 실격 엔터티에 'luis-datetimev2'를 입력합니다.
4. 만들기 클릭

![](../media/tutorial7_actions2.PNG)

이제 두 가지 작업이 있습니다.

### <a name="train-the-bot"></a>봇 학습

1. 학습 대화 상자, 새 학습 대화 상자를 차례로 클릭합니다.
2. 'hello'를 입력합니다.
3. 작업에 점수 지정을 클릭하고 'What's the date?'를 선택합니다.
2. 'today'를 입력합니다. 
    - today는 미리 빌드된 엔터티로, 편집 가능하지 않으므로 태그가 지정되며, 두 번째 줄에 표시됩니다.
5. 작업에 점수 지정을 클릭합니다.
    - 이제 엔터티 메모리 섹션에 해당 날짜가 표시됩니다. 
    - 날짜 위로 마우스를 가져가면 LUIS에서 제공한 추가 데이터가 표시됩니다. 이 데이터는 유용하며 코드에서 추가로 조작할 수 있습니다. 
6. 'The date is $luis-datetimev2'를 선택합니다.
7. 학습 완료를 클릭합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [대체 입력](./8-alternative-inputs.md)
