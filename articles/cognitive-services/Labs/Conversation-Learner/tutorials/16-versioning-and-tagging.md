---
title: Conversation Learner 응용 프로그램에서 버전 관리 및 태그 지정을 사용하는 방법 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 응용 프로그램에서 버전 관리 및 태그 지정을 사용하는 방법을 알아봅니다.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: ea013db078ff33f8597b0e15a8fc951e8ae320e8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376286"
---
# <a name="how-to-use-versioning-and-tagging"></a>버전 관리 및 태그 지정을 사용하는 방법

이 자습서에서는 Conversation Learner 앱 버전에 태그를 지정하고 “라이브” 버전을 설정하는 방법을 설명합니다.  

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 로그 대화 Web UI가 아니라 봇 에뮬레이터를 사용하여 로그 대화를 만들어야 합니다.  

이 자습서를 수행하려면 일반 자습서 봇이 실행 중이어야 합니다.

    npm run tutorial-general

## <a name="details"></a>세부 정보

편집할 때는 항상 “마스터”라는 태그를 편집합니다. 마스터에서 태그가 지정된 버전을 만들 수 있지만(기본적으로 마스터 스냅숏 생성), 태그가 지정된 버전을 편집할 수는 없습니다.

## <a name="steps"></a>단계

### <a name="install-the-bot-framework-emulator"></a>Bot Framework 에뮬레이터 설치

- [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator)로 이동합니다.
- 에뮬레이터를 다운로드 및 설치합니다.

### <a name="create-an-app"></a>앱 만들기

1. 새 앱을 클릭합니다.
2. 이름 필드에 Tutorial-16-Versioning을 입력합니다.
3. 만들기 클릭 
4. 설정 클릭
5. 앱 ID를 복사합니다.

### <a name="configure-the-emulator"></a>에뮬레이터 구성

- Conversation Learner 루트 폴더에서 .env 파일을 엽니다.
- 앱 ID를 CONVERSATION_LEARNER_APP_ID의 값으로 붙여넣습니다.
- 명령 프롬프트에서 종료하고 다음을 다시 실행하여 Conversation Learner 서비스를 다시 시작합니다.
 
    npm run tutorial-general 

### <a name="actions"></a>작업

다음과 같이 작업을 만들겠습니다.

1. Web UI로 전환합니다.
1. 작업, 새 작업을 차례로 클릭합니다.
2. 응답에 “hi there (version 1)”를 입력합니다.
3. 저장을 클릭합니다.


![](../media/tutorial16_action1.PNG)

새 태그를 만듭니다.

3. “설정”을 클릭하고 새 “태그”를 만듭니다.
    - 이름을 “버전 1”로 지정합니다.
4. “버전 1”을 “라이브”로 설정합니다.  
    - 라이브 태그를 “버전 1”로 설정하면 이 봇을 사용하는 채널이 “버전 1” 태그를 사용하게 됩니다.
    - 태그가 지정된 응용 프로그램 버전은 편집(작업 및 엔터티 변경, 학습 대화 추가)의 영향을 받지 않습니다.  
    - 응용 프로그램 편집(작업 및 엔터티 변경, 학습 대화 추가)은 항상 “마스터” 태그에서 수행됩니다.  즉, “마스터”는 변경 가능한 유일한 태그이고 다른 태그는 고정된 스냅숏입니다.
    - Conversation Learner UI의 로그 대화는 항상 마스터(라이브 태그 아님)를 사용합니다.

![](../media/tutorial16_v1_create.PNG)

버전은 설정에서 다음과 같이 생성되었습니다.

![](../media/tutorial16_settings.PNG)

두 번째 작업을 추가하겠습니다.

1. 작업, 새 작업을 차례로 클릭합니다.
2. 응답에 “bye bye (version 2)”를 입력합니다.

첫 번째 작업을 편집합니다.

1. 작업을 클릭합니다.
2. 작업 아래에서 “hi there (version 1)”를 클릭합니다.
3. 응답을 “hi there (version 2)”로 변경합니다.

![](../media/tutorial16_hi_there_v2.PNG)

### <a name="switch-to-the-bot-emulator"></a>봇 에뮬레이터로 전환

1. 봇 UI에서 “goodbye”를 입력합니다.
2. 봇이 “hi there (version 1)”로 응답합니다.
    - 이는 버전 1이 “라이브”임을 보여 줍니다. 

![](../media/tutorial16_bf_response.PNG)

### <a name="switch-to-the-web-ui"></a>Web UI로 전환

1. 로그 대화를 클릭합니다. 대화가 표시되지 않는 경우 앱을 새로 고칩니다.
2. “hi there (version 2)”를 클릭합니다.

현재 사용 가능한 모든 작업에서 선택하여 수정할 수 있습니다. 이러한 편집은 마스터에 대해 수행됩니다.

지금까지 버전 관리의 작동 방식 및 Bot Framework 에뮬레이터를 사용하여 봇을 조작하는 방법을 살펴보았습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [데모 - 암호 재설정](./demo-password-reset.md)
