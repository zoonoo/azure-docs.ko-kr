---
title: Conversation Learner 응용 프로그램에서 무효화할 수 있는 엔터티를 사용하는 방법 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 응용 프로그램에서 무효화할 수 있는 엔터티를 사용하는 방법을 알아봅니다.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 3d65376c9c43ee1407468f3e8bf3e058048bd556
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376318"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-application"></a>Conversation Learner 응용 프로그램에서 무효화할 수 있는 엔터티를 사용하는 방법

이 자습서에서는 엔터티의 "무효화할 수 있는" 속성을 설명합니다.

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 일반 자습서 봇이 실행 중이어야 합니다.

    npm run tutorial-general

## <a name="details"></a>세부 정보
사용자가 엔터티 값을 "지울" 수 있는 경우 작업을 "무효화할 수 있음", "아니요, $entity를 사용하지 않습니다." 또는 "아니요, $entity가 아닙니다."로 표시합니다. 예를 들어 "아니요, 보스턴을 떠나지 않습니다."

구체적으로 엔터티의 "무효화할 수" 속성을 다음으로 설정합니다.

- 레이블 지정 엔터티가 언급되면 엔터티의 정상(양수) 인스턴스 및 엔터티의 "음수" 인스턴스 모두의 레이블을 지정할 수 있습니다.
- LUIS는: 양수 인스턴스 및 음수 인스턴스라는 두 엔터티 모델을 학습합니다.
- 엔터티의 음수 인스턴스 효과는 엔터티 변수(있는 경우)에서 해당 값을 지우는 것입니다.

## <a name="steps"></a>단계

### <a name="create-the-application"></a>응용 프로그램 만들기

1. Web UI에서 새 앱을 클릭합니다.
2. 이름에 NegatableEntity를 입력합니다. 그런 다음, 만들기를 클릭합니다.

### <a name="create-an-entity"></a>엔터티 만들기

1. 엔터티, 새 엔터티를 차례로 클릭합니다.
2. 엔터티 이름에 이름을 입력합니다.
3. 무효화할 수 있음을 선택합니다.
    - 이 사용자가 엔터티에 값을 제공할 수 있거나 엔터티의 값이 *아님*을 나타냅니다. 후자의 경우 이렇게 하면 엔터티의 일치하는 값을 삭제합니다.
3. 만들기를 클릭합니다.

![](../media/tutorial5_entities.PNG)

### <a name="create-two-actions"></a>두 가지 작업 만들기

1. 작업, 새 작업을 차례로 클릭합니다.
2. 응답에 '이름을 알지 못합니다.'를 입력합니다.
3. 실격 엔터티에 이름을 입력합니다.
3. 만들기 클릭

그런 다음, 두 번째 작업을 만듭니다.

1. 작업, 새 작업을 차례로 클릭하여 두 번째 작업을 만듭니다.
3. 응답에 '이름을 압니다. $name입니다.'를 입력합니다.
4. 만들기 클릭

이제 두 가지 작업이 있습니다.

![](../media/tutorial5_actions.PNG)

### <a name="train-the-bot"></a>봇 학습

1. 학습 대화 상자, 새 학습 대화 상자를 차례로 클릭합니다.
2. '안녕하세요'를 입력합니다.
3. 작업에 점수 지정을 클릭하고 '이름을 알지 못합니다.'를 선택합니다.
    - 유효한 작업이기 때문에 100% 점수를 얻습니다.
2. '내 이름은 david입니다.'를 입력합니다.
3. 'david'를 선택하고 레이블을 '+이름'으로 지정합니다.
    - '+이름' 및 '-이름'이라는 '이름'의 두 가지 인스턴스가 있습니다.  +는 해당 값을 제공한다는 의미입니다. -는 값이 아닌 시스템을 알려준다는 의미입니다.
5. 작업에 점수 지정을 클릭합니다.
    - 이제 이름 값이 봇의 메모리에 있습니다.
    - '이름을 알고 있습니다. $name입니다.'만을 사용할 수 있습니다. 
6. '이름을 알고 있습니다. $name입니다.'를 선택합니다.

무효화할 수 있는 엔터티를 지우겠습니다.

7. '내 이름은 david가 아닙니다.'를 입력합니다.
    - 이전 패턴을 기반으로 'not'을 이름으로 선택합니다. 잘못되었습니다.
2. 'not', 빨간색 x를 차례로 클릭합니다. 
3. 'david'를 클릭합니다.
    - 이제 이름 엔터티의 값이 아니라고 통신하는 음수 엔터티입니다.
2. '-이름'을 선택합니다.
3. 작업에 점수 지정을 클릭합니다.
    - 값이 메모리에서 지워졌습니다.
2. 유일한 작업인 '이름을 알지 못합니다.'를 선택합니다.

다음으로 이름의 새 값을 입력할 수 있는 방법을 보여줍니다.

3. 이름으로 'john'을 입력합니다. 그런 다음, 'john'을 선택하고 이름을 클릭합니다.
4. 작업에 점수 지정을 클릭합니다.
5. '이름을 알고 있습니다. $name입니다.'를 선택합니다.

이제 입력한 이름을 바꿔보세요.

6. '내 이름은 susan입니다.'를 입력합니다.
7. '이름을 알고 있습니다. $name입니다.'를 선택합니다.
7. 작업에 점수 지정을 클릭합니다.
8. **susan**은 엔터티 값에서 **john**으로 덮어썼습니다.
9. '내 이름은 susan이 아닙니다.'를 입력합니다.
    - 시스템에서 음수 인스턴스로 레이블을 지정합니다.
2. 작업에 점수 지정을 클릭합니다.
3. 유일한 작업인 '이름을 알지 못합니다.'를 선택합니다.
7. 학습 완료를 클릭합니다.

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [다중 값 엔터티](./6-multi-value-entities.md)다중 값 엔터티
