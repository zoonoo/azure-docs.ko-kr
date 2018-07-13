---
title: Conversation Learner 응용 프로그램에서 엔터티 검색 콜백을 사용하는 방법 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 응용 프로그램에서 엔터티 검색 콜백을 사용하는 방법을 알아봅니다.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e41ea5930ff0c8395d0c93aa42e224ebfc894ba8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376255"
---
# <a name="how-to-use-entity-detection-callback"></a>엔터티 검색 콜백을 사용하는 방법

이 자습서에서는 엔터티 검색 콜백 보여주며, 엔터티를 확인하기 위한 일반적인 패턴을 보여줍니다.

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 "tutorialEntityDetectionCallback" 봇이 실행 중이어야 합니다.

    npm run tutorial-entity-detection

## <a name="details"></a>세부 정보
엔터티 검색 콜백을 사용하면 엔터티와 관련된 비즈니스 규칙을 처리하는 사용자 지정 코드를 사용할 수 있습니다. 이 데모에서는 콜백 및 프로그래밍 엔터티를 사용하여 사용자가 입력한 도시 이름을 정식 이름으로 확인합니다(예: "빅 애플"을 "뉴욕"으로 확인).

### <a name="open-the-demo"></a>데모 열기

앱 목록에서 Tutorial-10-EntityDetenctionCallback을 클릭합니다. 

### <a name="entities"></a>엔터티

응용 프로그램에 세 개의 엔터티를 정의했습니다.

![](../media/tutorial10_entities.PNG)

1. City는 사용자가 텍스트 입력으로 제공하는 사용자 지정 엔터티입니다.
2. CityUnknown는 프로그래밍 엔터티입니다. 이 항목은 시스템에 의해 채워집니다. 시스템이 어느 도시인지 알지 못하는 경우 사용자 입력을 복사합니다.
3. CityResolved는 시스템에서 알 수 있는 도시입니다. 도시의 정식 이름을 사용합니다(예: "빅 애플"을 "뉴욕"으로 확인).

### <a name="actions"></a>작업

세 가지 작업을 만들었습니다. 

![](../media/tutorial10_actions.PNG)

1. 첫 번째 작업은 '어느 도시를 선택하시겠습니까?'입니다.
2. 두 번째는 '$CityUknown, 이 도시를 모릅니다. '어느 도시를 선택하시겠습니까?'입니다.
3. 세 번째는 '$City를 선택하셨습니다. $CityResolved로 확인합니다.'입니다.

### <a name="callback-code"></a>콜백 코드

코드를 살펴보겠습니다. C:\<installedpath>\src\demos\tutorialSessionCallbacks.ts 파일에서 EntityDetectionCallback 메서드를 찾을 수 있습니다.

![](../media/tutorial10_callbackcode.PNG)

이 함수는 엔터티가 확인된 후에 호출됩니다.
 
- 가장 먼저 수행할 작업은 $CityUknown을 지우는 것입니다. $CityUknown이 시작 부분에서 항상 지워므로 단일 순서에서만 지속됩니다.
- 그런 다음, 인식된 도시 목록을 가져옵니다. 첫 번째 작업을 수행하고 확인하도록 합니다.
- resolveCity를 확인하는 함수는 위의 정의 코드에서 정의됩니다. 정식 도시 이름 목록이 포함됩니다. 목록에서 도시 이름을 찾고, 반환합니다. 또는 'cityMap'에 표시되고 매핑된 이름을 반환합니다. 도시를 찾을 수 없으면 null을 반환합니다.
- 마지막으로 도시가 이름으로 확인되면 $CityKnown 엔터티에 저장합니다. 또는 사용자가 설명한 내용을 지우고 $CityUknown 엔터티를 채웁니다.

### <a name="train-dialogs"></a>학습 대화 상자

1. 학습 대화 상자, 새 학습 대화 상자를 차례로 클릭합니다.
2. '안녕하세요'를 입력합니다.
3. 작업에 점수 지정을 클릭하고 '어느 도시를 선택하시겠습니까?'를 선택합니다.
2. '뉴욕'을 입력합니다.
    - City 엔터티로 인식되었습니다.
5. 작업에 점수 지정을 클릭합니다.
    - City 및 CityResolved가 채워졌습니다.
6. '$City를 선택하셨습니다. $CityResolved로 확인합니다.'를 선택합니다.
7. 학습 완료를 클릭합니다.

또 다른 예제 대화 상자를 추가합니다.

1. 새 학습 대화를 클릭합니다.
2. '안녕하세요'를 입력합니다.
3. 작업에 점수 지정을 클릭하고 '어느 도시를 선택하시겠습니까?'를 선택합니다.
2. '빅 애플'을 입력합니다.
    - City 엔터티로 인식되었습니다.
5. 작업에 점수 지정을 클릭합니다.
    - CityResolved가 실행되는 코드의 결과를 보여줍니다.
6. '$City를 선택하셨습니다. $CityResolved로 확인합니다.'를 선택합니다.
7. 학습 완료를 클릭합니다.

또 다른 예제 대화 상자를 추가합니다.

1. 새 학습 대화를 클릭합니다.
2. '안녕하세요'를 입력합니다.
3. 작업에 점수 지정을 클릭하고 '어느 도시를 선택하시겠습니까?'를 선택합니다.
2. '어떤 도시'를 입력합니다.
    - 시스템이 알 수 없는 도시의 예제입니다. 
5. 작업에 점수 지정을 클릭합니다.
6. '$CityUknown, 이 도시를 모릅니다. '어느 도시를 선택하시겠습니까?'를 선택합니다.
7. '뉴욕'을 입력합니다.
8. 작업에 점수 지정을 클릭합니다.
    - CityUknown을 지우고 CityResolved를 채웁니다.
6. '$City를 선택하셨습니다. $CityResolved로 확인합니다.'를 선택합니다.
7. 학습 완료를 클릭합니다.

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [세션 콜백](./11-session-callbacks.md)
