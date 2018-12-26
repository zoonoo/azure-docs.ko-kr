---
title: Conversation Learner 모델에서 카드를 사용하는 방법, 2부 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 모델에서 카드를 사용하는 방법을 알아봅니다.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 99c474978d4da1d80669505330b2dc6220d7ca5f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51226637"
---
# <a name="how-to-use-cards-part-1-of-2"></a>카드 사용 방법(2부 중 1부)
이 자습서는 채우기 가능 양식 카드를 봇에 추가하는 방법을 보여 줍니다. 양식 필드가 어떻게 엔터티로 이동하는지를 보여 줍니다.

Conversation Learner는 봇이 시작된 디렉터리에 있는 “cards” 디렉터리에 카드 정의 파일이 있을 것으로 예상합니다.

## <a name="video"></a>비디오

[![자습서 14 미리 보기](https://aka.ms/cl-tutorial-14-preview)](https://aka.ms/blis-tutorial-14)

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 일반 자습서 봇이 실행 중이어야 합니다.

    npm run tutorial-general

## <a name="details"></a>세부 정보

카드는 사용자가 대화에서 옵션을 선택할 수 있도록 하는 UI 요소입니다. 

### <a name="open-the-demo"></a>데모 열기

Web UI의 모델 목록에서 Tutorial-14-Cards-2를 클릭합니다. 

### <a name="the-card"></a>카드

카드 정의는 다음 위치에 있습니다. C:\<installedpath\>\src\cards\shippingAddress.json

이 카드는 배송 주소에서 구/군/시, 주소, 시/도의 3개 필드를 수집합니다.

![](../media/tutorial14_card.PNG)

### <a name="actions"></a>작업

세 가지 작업을 만들었습니다. 아래와 같이 첫 번째 작업은 카드입니다.

![](../media/tutorial14_actions.PNG)

카드 작업 형식이 어떻게 생성되었는지 살펴보겠습니다.

- Input.text 형식인 Address-Street와 해당 ID를 확인합니다.
- 마찬가지로, Address-City와 ID가 Address-State인 드롭다운이 있습니다.

ID는 필드가 채워지고 제출될 때 봇에서 해당 값을 수신할 엔터티 이름이기 때문에 중요합니다.

## <a name="entities"></a>엔터티
위에서 확인한 것처럼 카드와 일치하는 세 가지 엔터티를 정의했습니다.

![](../media/tutorial14_entities.PNG)

## <a name="actions"></a>작업

두 가지 작업을 정의했습니다.

![](../media/tutorial14_actions.PNG)

- 첫 번째는 작업 형식이 CARD이고 드롭다운에서 shippingAddress 템플릿이 선택된 배송 주소 카드입니다.
- 두 번째는 배송 주소를 읽는 간단한 작업입니다.

![](../media/tutorial14_sa_card.PNG)

### <a name="train-dialog"></a>학습 대화

학습 대화를 살펴보겠습니다.

1. Train Dialogs(학습 대화), New Train Dialog(새 학습 대화)를 차례로 클릭합니다.
1. ‘hi’를 입력합니다.
2. Score Action(작업에 점수 지정)을 클릭합니다.
3. ‘배송 주소’를 클릭하여 선택합니다.
4. 카드를 입력하고 제출합니다.
    - 이제 해당 값이 엔터티 메모리로 이동되었습니다. 양식에서 입력을 이미 분할했기 때문에 구문 분석할 필요가 없습니다.
5. Score Actions(작업에 점수 지정)를 클릭합니다.
3. ‘$Address(으)로 배송...’을 클릭하여 선택합니다.
4. 테스트 완료를 클릭합니다.

![](../media/tutorial14_train_dialog.PNG)

이제 채우기 가능 필드와 드롭다운이 있는 카드에서 값을 가져오고 봇 엔터티에서 캡처 및 수집하는 방법을 확인했습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [분기 및 실행 취소](./15-branching-and-undo.md)
