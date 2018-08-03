---
title: 데모 Conversation Learner 모델, 가상 현실 앱 시작 관리자 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: 데모 Conversation Learner 모델을 만드는 방법을 알아봅니다.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 896ec007c03e30e5c20a5344430be040271bc00b
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171153"
---
# <a name="demo-virtual-reality-app-launcher"></a>데모: 가상 현실 앱 시작 관리자

이 데모에서는 “start Skype and put in on the wall” 등의 명령을 지원하는 가상 현실 모델 시작 관리자를 보여 줍니다. 앱을 시작하려면 사용자가 앱 이름과 위치를 말해야 합니다. 모델 시작은 API 호출을 통해 처리됩니다. 사용자로부터 앱 이름이 인식되면 entityDetectionCallback은 요청된 앱이 설치된 앱 목록에 있는 하나 이상의 앱과 일치하는지 확인합니다. 요청된 앱이 설치되어 있지 않고 앱 이름이 모호한(둘 이상의 설치된 앱과 일치함) 경우를 처리합니다.

## <a name="video"></a>비디오

[![데모 VR 앱 미리 보기](http://aka.ms/cl-demo-vrapp-preview)](http://aka.ms/blis-demo-vrapp)

## <a name="requirements"></a>요구 사항

이 자습서를 수행하려면 VRAppLauncher 봇이 실행 중이어야 합니다.

    npm run demo-vrapp
    
### <a name="open-the-demo"></a>데모 열기

Web UI의 모델 목록에서 VRAppLauncher를 클릭합니다. 

## <a name="entities"></a>엔터티

다음과 같은 네 가지 엔터티를 만들었습니다.

- AppName: 예제의 Skype
- PlacementLocation: 예제의 wall
- UnknownAppName: 시스템이 사용자가 말하는 엔터티 이름을 인식하지 못할 때(예: 설치되어 있지 않음) 설정하는 프로그래밍 엔터티
- DisAmbigAppNames: 사용자가 말한 항목과 일치하는 두 개 이상의 설치된 앱 이름 배열 

![](../media/tutorial_vrapplauncher_entities.PNG)

### <a name="actions"></a>작업

앱을 실행할 함수 호출을 시작하는 LaunchApp이라는 API를 포함하는 작업 집합을 만들었습니다.

![](../media/tutorial_vrapplauncher_actions.PNG)

### <a name="training-dialogs"></a>학습 대화
여러 학습 대화를 정의했습니다.

![](../media/tutorial_vrapplauncher_dialogs.PNG)

예를 들어 학습 세션을 시도해 보겠습니다.

1. Train Dialogs(학습 대화), New Train Dialog(새 학습 대화)를 차례로 클릭합니다.
1. ‘hi’를 입력합니다.
2. Score Action(작업에 점수 지정)을 클릭합니다.
3. ‘which app do you want to start?’를 클릭하여 선택합니다.
4. ‘outlook’을 입력합니다.
    - LUIS에서 엔터티로 인식합니다.
5. 작업에 점수 지정을 클릭합니다.
3. ‘where do you want it placed?’를 클릭하여 선택합니다.
4. ‘on the wall’을 입력합니다.
    - LUIS에서 PlacementLocation으로 인식합니다.
2. Score Actions(작업에 점수 지정)를 입력합니다.
6. ‘LaunchApp’을 선택합니다.
7. 시스템: ‘starting outlook on the wall’.
    - 이로 인해 API 호출이 트리거되었습니다. 이 호출의 코드는 C:\<\installedpath>\src\demos\demoVRAppLauncher.ts에 있습니다. 그러나 이 데모를 위해 Outlook을 시작하는 코드가 실제로는 없습니다.
    - AppName 및 PlacementLocation 엔터티를 지웁니다. 그런 다음, 위의 문자열을 응답으로 반환합니다.
4. 학습 완료를 클릭합니다.

![](../media/tutorial_vrapplauncher_callbackcode.PNG)

알 수 없고 모호한 엔터티를 처리하기 위해 다른 학습 세션을 시작하겠습니다.

1. 새 학습 대화를 클릭합니다.
1. ‘start OneNote’를 입력합니다. 
    - 모델은 OneNote를 앱 이름으로 인식합니다. 코드에 정의된 `EntityDetectionCallback` 함수는 코드에 정의된 앱 목록과 일치하는지 비교한 후 일치할 경우 사용자가 입력한 이름을 앱 이름으로 확인합니다. 그런 다음, 일치하는 모든 앱 집합이 반환됩니다. 
    - 일치 항목 목록이 0이면 앱이 설치되지 않은 것입니다. unknownAppName에 배치됩니다.
    - 앱을 두 개 이상 찾은 경우 `DisambigAppNames`에 복사하고 AppName 엔터티를 지웁니다.
2. 작업에 점수 지정을 클릭합니다.
3. ‘Sorry, I don't know the app $UknownAppName’을 클릭하여 선택합니다.
4. ‘start amazon’을 입력합니다. 다른 경로를 사용해 보겠습니다.
5. 작업에 점수 지정을 클릭합니다.
    - 이제 Amazon Video 및 Amazon Music은 `DisambigAppNames` 메모리에 있으며 OneNote가 지워졌습니다.
3. ‘There are few apps that sound like that...’을 클릭하여 선택합니다.
    - 이 시점까지 정의한 학습 대화가 몇 개밖에 되지 않으므로 점수는 별로 높지 않습니다. 학습 대화를 추가로 정의하면 좀 더 결정적인 모델이 구현됩니다.
2. Score Actions(작업에 점수 지정)를 입력합니다.
4. 학습 완료를 클릭합니다.

지금까지 엔터티 확인을 수행하는 방법을 살펴보았습니다. 데모에서는 API 콜백을 설명하고 정보 수집, 현재 상태 및 모호성 확인, 그에 따른 수정 작업 수행을 위한 템플릿을 보여 주었습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Conversation Learner 봇 배포](../deploy-to-bf.md)
