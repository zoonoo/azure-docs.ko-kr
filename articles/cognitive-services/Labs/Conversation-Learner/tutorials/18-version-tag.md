---
title: 대화 학습자 모델-Azure Cognitive Services를 사용 하 여 버전 태그를 사용 하는 방법 | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 모델에서 버전 관리 및 태그 지정을 사용하는 방법을 알아봅니다.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 4067c7fb43cc200b8f49dbc14151c69a188e4e8e
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475715"
---
# <a name="how-to-use-version-tagging"></a>Version Tagging을 사용하는 방법

이 자습서에서는 Conversation Learner 모델 버전에 태그를 지정하고 “라이브” 버전을 설정하는 방법을 설명합니다.  

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 로그 대화 Web UI가 아니라 Bot Framework Emulator를 사용하여 로그 대화를 만들어야 합니다.  

이 자습서를 수행하려면 일반 자습서 봇이 실행 중이어야 합니다.

    npm run tutorial-general

## <a name="details"></a>세부 정보

모델의 태그가 지정된 버전은 정적이므로 편집하거나 변경할 수 없습니다. 모델을 편집할 때 항상 마스터 버전을 편집 중입니다. 새 태그를 추가할 때 Conversation Learner는 해당 시점에 모델의 스냅샷을 캡처합니다. 

봇이 "라이브" 버전으로 선택한 버전의 모델을 사용하지만 "태그 편집"을 "마스터"로 설정하는 경우 봇이 보유한 모든 대화를 볼 수 있습니다. 모델의 "태그 편집" 속성이 "Master" 이외의 값으로 설정되는 경우 모델의 스냅샷을 볼 수 있지만 모델을 어떤 방식으로도 변경할 수 없습니다.

## <a name="steps"></a>단계

### <a name="install-the-bot-framework-emulator"></a>Bot Framework Emulator 설치

1. [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator)로 이동합니다.
2. 에뮬레이터를 다운로드 및 설치합니다.

### <a name="create-a-model"></a>모델 만들기

1. 모델 목록 홈 페이지에서 `New Model` 단추를 클릭합니다.
2. `Name` 필드에서 "Tutorial-18-Versioning"을 입력하고 Enter 키를 누릅니다.
4. 왼쪽 패널에서 "설정"을 클릭합니다.
5. CONVERSATION_LEARNER_MODEL_ID 필드의 내용을 클립보드에 복사합니다.

### <a name="configure-the-emulator"></a>에뮬레이터 구성

1. Conversation Learner 루트 폴더에서 ".env" 파일을 엽니다.
2. 다음과 같이 ".env" 파일에 줄을 추가합니다.
    - `CONVERSATION_LEARNER_MODEL_ID=[paste-model-id-from-clipboard-here]`
3. 명령 프롬프트에서 종료하고 다음을 다시 실행하여 Conversation Learner 서비스를 다시 시작합니다.
    - `npm run tutorial-general`
4. Bot Framework Emulator에서 새 봇 구성 만들고 엔드포인트 URL을 `http://localhost:3978/api/messages`로 설정

### <a name="version-1"></a>버전 1

버전 1에 대한 단일 작업을 만듭니다.

1. Web UI의 왼쪽 패널에서 “작업”을 클릭한 다음, `New Action` 단추를 클릭합니다.
2. “봇의 응답” 필드에 “안녕하세요(버전 1)”를 입력합니다.
3. `Save` 단추를 클릭합니다.

이제 모델의 "버전 1"로 태그를 지정하겠습니다.

1. 왼쪽 패널에서 "설정"을 클릭한 다음, "버전 태그"![](../media/tutorial18_version_tags.PNG) 아이콘을 클릭하여 클릭해야 하는 `New Tag` 단추를 표시합니다.
    - “버전 1”로 이름 지정
1. "라이브 태그" 드롭다운 메뉴에서 "버전 1"을 선택합니다.  
    - 이제 이 봇을 사용하는 채널이 모델의 "버전 1"을 사용합니다.
    - 엔터티, 작업 및 이 버전 1 모델의 학습 대화 상자를 더 이상 변경할 수 없습니다.
    - "태그 편집"으로 "버전 1"을 선택하는 경우 모델을 볼 수만 있고 편집할 수는 없습니다.
    - "태그 편집" 설정을 "마스터"로 유지하면 편집할 수 있는 유일한 버전의 모델이 됩니다.

이제 "버전 태그" 그리드에 "버전 1"이 표시됩니다.

### <a name="version-2"></a>버전 2

이제 버전 1과 구분하려면 모델을 편집합니다.

1. 왼쪽 창에서 "작업"을 클릭합니다.
2. 작업 그리드에서 “안녕하세요(버전 1)”를 클릭합니다.
3. “봇의 응답” 필드를 “안녕하세요(버전 2)”로 변경합니다.
4. `Save` 단추를 클릭합니다.
5. `New Action` 단추를 클릭합니다.
6. “봇의 응답” 필드에 “bye bye(버전 2)”를 입력합니다.

### <a name="confirm-bot-framework-emulator-is-using-version-1"></a>Bot Framework Emulator가 버전 1을 사용하는지 확인

1. Bot Framework Emulator의 메시지에 "안녕하세요"를 입력합니다.
2. 봇이 “안녕하세요(version 1)”로 응답합니다.
    - 그러면 버전 1이 "라이브"인지 확인합니다.

### <a name="view-the-conversation-logs-in-conversation-learner-web-ui"></a>Conversation Learner Web UI에서 대화 로그 보기

1. 왼쪽 창에서 "로그 대화 상자" 클릭
    - 대화가 표시되지 않는 경우 새로 고침 단추를 클릭합니다.
2. 그리드에서 "버전 1" 태그를 확인합니다.
3. 그리드에서 “안녕하세요(버전 1)” 클릭

> [!NOTE]
> 현재 사용할 수 있는 모든 Conversation Learner 기능에서 선택하여 수정할 수 있지만, 이러한 편집 작업은 버전 1이 아닌 마스터에 수행되어야 합니다.

지금까지 버전 관리의 작동 방식 및 Bot Framework Emulator를 사용하여 봇과 상호 작용할 수 있는 방법을 살펴보았습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [열거형 엔터티 및 엔터티 동작 설정](./tutorial-enum-set-entity.md)
