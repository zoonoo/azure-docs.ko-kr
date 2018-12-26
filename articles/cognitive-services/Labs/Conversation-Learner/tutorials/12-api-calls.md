---
title: Conversation Learner 모델에서 API 호출을 사용하는 방법 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 모델에서 API 호출을 사용하는 방법을 알아봅니다.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 815d1e9f6d1e4b9937647d55b67665e1b27f501e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51240769"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-model"></a>Conversation Learner 모델에 API 호출을 추가하는 방법

이 자습서는 모델에 API 호출을 추가하는 방법을 보여 줍니다. API 호출은 봇에서 정의 및 작성하고 Conversation Learner가 호출할 수 있는 함수입니다.

## <a name="video"></a>비디오

[![자습서 12 미리 보기](https://aka.ms/cl-tutorial-12-preview)](https://aka.ms/blis-tutorial-12)

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 “tutorialAPICalls.ts” 봇이 실행 중이어야 합니다.

    npm run tutorial-api-calls

## <a name="details"></a>세부 정보

- API 호출은 엔터티를 읽고 조작할 수 있습니다.
- API 호출은 메모리 관리자 개체에 액세스할 수 있습니다.
- API 호출은 인수를 사용할 수도 있습니다. 인수를 사용하면 동일한 API 호출을 다른 용도로 다시 사용할 수 있습니다.

### <a name="open-the-demo"></a>데모 열기

Web UI의 모델 목록에서 Tutorial-12-APICalls를 클릭합니다. 

### <a name="entities"></a>엔터티

모델에서 number라는 엔터티 하나를 정의했습니다.

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>API 호출
API 호출의 코드는 다음 파일에서 정의됩니다. C:\<installedpath\>\src\demos\tutorialAPICalls.ts

![](../media/tutorial12_apicalls.PNG)

- 첫 번째 API 콜백은 RandomGreeting입니다. 인사말 변수에 정의된 임의 인사말을 반환합니다.
- Multiply API 콜백: 사용자가 제공하는 두 개의 숫자를 곱합니다. 그런 다음, 두 숫자를 곱한 결과를 반환합니다. API 콜백이 입력을 사용할 수 있음을 보여 줍니다. 메모리 관리자가 첫 번째 인수입니다. 
- ClearEntities API 콜백: 사용자가 다음 숫자를 입력할 수 있도록 숫자 엔터티를 지웁니다. API 호출이 엔터티를 조작하는 방법을 보여 줍니다.

### <a name="actions"></a>작업

네 가지 작업을 만들었습니다. 

![](../media/tutorial12_actions.PNG)

- 대화 작업인 “What number do you want to multiply by 12?” 외에도 일반적인 API 호출 패턴을 보여 주는 세 가지 API 호출이 있습니다.

- RandomGreeting: 비대기 작업입니다. 이 작업을 설정하기 위해 작업 만들기 대화 상자에서 작업 유형으로 API_LOCAL을 선택하고 RandomGreeting을 선택했습니다. 

![](../media/tutorial12_setupapicall.PNG)

API 옆의 새로 고침 단추는 봇을 중지하고 API를 변경하려는 경우에 사용됩니다. 새로 고침을 클릭하면 최신 변경 내용이 반영됩니다.

다음은 Multiply 작업을 만든 방법입니다. API_Local과 API를 선택한 후에 첫 번째 입력 값(num1string)으로 엔터티($number)를 입력하고, 두 번째 입력 값(num2string)으로 값(12)을 입력했습니다. 이렇게 하면 봇과 API 호출 간의 간접 수준이 제공되므로 동일한 콜백을 시스템의 몇 가지 작업에 매핑할 수 있으며 작업 할당 방법이 서로 다릅니다.

![](../media/tutorial12_actionmultiply.PNG)

### <a name="train-dialog"></a>학습 대화

학습 대화를 살펴보겠습니다.

1. Train Dialogs(학습 대화), New Train Dialog(새 학습 대화)를 차례로 클릭합니다.
1. ‘hi’를 입력합니다.
2. Score Action(작업에 점수 지정)을 클릭합니다.
3. RandomGreeting을 클릭하여 선택합니다. 그러면 임의 인사말 API 호출이 실행됩니다.
3. ‘What number to do you want to multiply by 12?’를 클릭하여 선택합니다.
4. ‘8’을 입력합니다. 그런 다음, Score Actions(작업에 점수 지정)를 클릭합니다.
4. ‘Multiply $number 12’를 선택합니다. 곱하기 결과를 확인합니다.
5. ‘Clear Entities’(엔터티 지우기)를 선택합니다.
    - `number` 엔터티의 값이 지워졌습니다.
3. ‘What number to do you want to multiply by 12?’를 클릭하여 선택합니다.
4. 테스트 완료를 클릭합니다.

![](../media/tutorial12_dialog.PNG)

지금까지 API 콜백과 일반적인 패턴을 등록하는 방법 및 인수를 정의하고 인수를 통해 값과 엔터티를 연결하는 방법을 살펴보았습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [카드 1 부](./13-cards-1.md)
