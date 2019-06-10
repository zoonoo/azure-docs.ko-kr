---
title: Conversation Learner 모델에서 API 호출을 사용하는 방법 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 모델에서 API 호출을 사용하는 방법을 알아봅니다.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 1f7c7c72703d7c3134dd2acdcc466fc0182fa38a
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389950"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-model"></a>Conversation Learner 모델에 API 호출을 추가하는 방법

이 자습서는 모델에 API 호출을 추가하는 방법을 보여 줍니다. API 호출은 봇에서 정의 및 작성하고 Conversation Learner가 호출할 수 있는 함수입니다.

## <a name="video"></a>비디오

[![API 호출 자습서 미리 보기](https://aka.ms/cl_Tutorial_v3_APICalls_Preview)](https://aka.ms/cl_Tutorial_v3_APICalls)

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 “tutorialAPICalls.ts” 봇이 실행 중이어야 합니다.

    npm run tutorial-api-calls

## <a name="details"></a>세부 정보

- API 호출은 엔터티를 읽고 조작할 수 있습니다.
- API 호출은 메모리 관리자 개체에 액세스할 수 있습니다.
- API 호출은 인수를 사용할 수 있습니다.

### <a name="open-the-demo"></a>데모 열기

웹 UI에서 "자습서 가져오기"를 클릭하고 "Tutorial-14-APICalls"라는 모델을 선택합니다.

### <a name="entities"></a>엔터티

모델에서 `number`라는 엔터티 하나를 정의했습니다.

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>API 호출
API 호출 코드는 `C:\<installedpath>\src\demos\tutorialAPICalls.ts` 정의 파일에서 정의됩니다.

![](../media/tutorial12_apicalls.PNG)

- `RandomGreeting` 콜백은 `greeting` 배열에서 정의된 임의 인사말을 반환합니다.
- `Multiply` 콜백은 UI에서 렌더링할 수 있는 결과를 호출하고 반환하는 작업에서 전달된 두 숫자를 곱하기합니다.
    - 메모리 관리자가 첫 번째 인수인지 확인합니다. 
    - `num1string` 및 `num2string` 경우에 API 콜백이 여러 입력을 사용할 수 있는지 확인합니다.
- `ClearEntities` 콜백은 사용자가 다른 숫자를 입력할 수 있도록 숫자 엔터티를 지웁니다. 
    - API 호출이 엔터티를 조작하는 방법을 보여줍니다.

### <a name="actions"></a>작업
네 가지 작업을 만들었습니다. 이 중 세 가지는 "비대기" API 작업이며, 네 번째 작업은 사용자에게 다른 자습서에서 살펴본 것과 비슷한 질문을 하는 "텍스트" 작업입니다. 각 작업을 만드는 방법을 확인하려면 다음을 수행합니다.
1. 왼쪽 패널에서 "작업"을 클릭한 다음, 그리드에 나열된 네 가지 작업 중 하나를 클릭합니다.
2. 표시되는 양식에서 각 필드의 값을 확인합니다.
3. API 필드 옆에 있는 `Refresh` 단추를 확인합니다.
    - 봇을 중지하고 UI 페이지가 실행되는 동안 API를 변경하는 경우 `Refresh` 단추를 클릭하여 최신 변경 내용을 선택할 수 있습니다.

![](../media/tutorial12_actions.PNG)

#### <a name="clearentities-multiply-and-randomgreeting"></a>ClearEntities, Multiply 및 RandomGreeting
이러한 작업 세 가지 모두는 API 형식입니다. 이러한 작업 각각은 API 콜백 함수에 따라 몇 가지 작업을 수행하고 사용자에게 표시되는 값을 반환할 수 있습니다.

#### <a name="what-number-do-you-want-to-multiply-by-12"></a>“12로 곱하려는 번호는?”
이 작업은 "텍스트" 작업으로 사용자에 대해 질문하기만 하면 됩니다. 이 작업은 실제로 API 콜백 중 하나와 상호 작용하지 않는 반면, 사용자에게 엔터티 메모리로 이동한 다음, API 콜백 중 하나를 사용하는 "곱하기" 작업에서 사용되는 숫자로 응답할 것을 요구합니다.


### <a name="train-dialog"></a>학습 대화

"학습 대화"를 살펴보겠습니다.

1. 왼쪽 패널에서 `Train Dialogs`를 클릭한 다음, `New Train Dialog` 단추를 클릭합니다.
2. "안녕하세요"를 입력합니다.
3. `Score Actions` 단추를 클릭합니다.
4. `RandomGreeting`을(를) 선택합니다. 
    - 그러면 임의 인사말 API 호출이 실행됩니다.
    - 또한 사용자 응답을 기다리지 않습니다.
5. `What number to do you want to multiply by 12?`을(를) 선택합니다.
6. 숫자, 모든 숫자 및 숫자만 입력합니다.
    - 번호가 자동으로 `number` 엔터티로 레이블이 표시되었는지 확인합니다.
7. `Score Actions` 단추를 클릭합니다.
8. `Multiply` 작업을 선택합니다.
    - 12로 곱하기 결과를 확인합니다.
    - 메모리에 `number`에 대해 입력한 값이 여전히 포함되어 있는지 확인
9. `ClearEntities` 작업을 선택합니다.
    - `number`의 엔터티 값이 메모리에서 지워졌는지 확인합니다.
10. `Save` 단추를 클릭합니다.

지금까지 API 콜백과 일반적인 패턴을 등록하는 방법 및 인수를 정의하고 인수를 통해 값과 엔터티를 연결하는 방법을 살펴보았습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [카드 1 부](./15-cards.md)
