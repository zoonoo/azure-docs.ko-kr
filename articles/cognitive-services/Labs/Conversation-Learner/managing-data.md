---
title: Conversation Learner에서 사용자 데이터 관리 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner에서 사용자 데이터를 관리하는 방법을 알아봅니다.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 7ea0b246a16ff196a4160d9822b5db15cd39a4a6
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66385194"
---
# <a name="managing-user-data"></a>사용자 데이터 관리

이 페이지에서는 일반 사용자와 대화할 때 Conversation Learner 클라우드 서비스에서 기록하는 사항을 설명합니다.  특정 사용자와 관련된 모든 로그를 검색하거나 삭제할 수 있도록 Conversation Learner 로그를 사용자 ID와 연결하는 방법도 설명합니다.

## <a name="overview-of-end-user-data-logging"></a>최종 사용자 데이터 로깅 개요

기본적으로 Conversation Learner 클라우드 서비스는 최종 사용자와 봇 간의 상호 작용을 기록합니다.  이러한 로그는 봇이 잘못된 엔터티를 추출했거나 잘못된 작업을 선택한 경우를 식별하여 봇을 개선하는 데 중요합니다.  UI의 “로그 대화” 페이지로 이동하고 수정한 다음, 이 수정된 대화를 새 학습 대화로 저장하면 이러한 오류를 수정할 수 있습니다. 자세한 내용은 “로그 대화”에 대한 자습서를 참조하세요.

## <a name="how-to-disable-logging"></a>로깅을 사용하지 않도록 설정하는 방법

최종 사용자와의 대화를 Conversation Learner 모델에 대한 “설정” 페이지에 포함할지 여부를 제어할 수 있습니다.  “로그 대화” 확인란이 있습니다.  이 확인란 선택을 취소하면 최종 사용자와의 대화가 기록되지 않습니다.

## <a name="what-is-logged"></a>기록되는 내용 

로그 대화에서 Conversation Learner는 각 턴에 대한 사용자 입력, 엔터티 값, 선택한 작업 및 타임스탬프를 저장합니다.  이러한 로그는 일정 기간 동안 저장된 다음, 삭제됩니다(자세한 내용은 "기본값 및 경계"에 대한 도움말 페이지 참조).  

Conversation Learner는 각 기록된 대화에 대한 고유 ID를 만듭니다.  Conversation Learner는 기록된 대화와 함께 사용자 식별자를 저장하지 *않습니다*.  

## <a name="associating-logged-dialogs-with-a-user-id"></a>기록된 대화를 사용자 ID에 연결

기록된 대화를 사용자 ID에 연결하는 기능이 중요한 경우가 많습니다. 예를 들어 특정 사용자에서 기록된 대화를 검색하거나 삭제하는 데 필요합니다.  Conversation Learner는 사용자 식별자를 저장하지 않으므로 이 연결은 개발자의 코드에서 유지 관리해야 합니다.  

이 매핑을 만들려면 `EntityDetectionCallback`에서 기록된 대화의 ID를 가져온 다음, 봇의 저장소에 사용자 ID와 이 기록된 대화 간의 연결을 저장합니다.  

```
cl.EntityDetectionCallback(async (text: string, memoryManager: ClientMemoryManager): Promise<void> => {

    // Get user and session info
    var sessionData = memoryManager.SessionInfo();

    // sessionData.sessionId is the ID of this logged dialog.
    // In your bot-specific data store, store an association
    // between your user identifier and this session ID.
    console.log(sessionData.logDialogId)

    // sessionData.userId and sessionData.userName are the 
    // user ID and user name as defined by the channel the user
    // is on (eg, Skype, Teams, Facebook Messenger, etc.).
    // For some channels, this will be undefined.
    // This information is NOT stored with the logged dialog.
    console.log(sessionData.userId);
    console.log(sessionData.userName);

})
```

## <a name="headers-for-http-calls"></a>HTTP 호출의 헤더

아래의 각 HTTP 호출에 다음 헤더를 추가합니다.

```
Ocp-Apim-Subscription-Key=<LUIS_AUTHORING_KEY>
```

여기서 `<LUIS_AUTHORING_KEY>`는 Conversation Learner 애플리케이션에 액세스하는 데 사용되는 LUIS 작성 키입니다.

## <a name="how-to-obtain-raw-data-for-a-logged-dialog"></a>기록된 대화의 원시 데이터를 얻는 방법

로그 대화의 원시 데이터를 얻으려면 다음 HTTP 호출을 사용할 수 있습니다.

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

여기서 `<appId>`는 이 Conversation Learner 모델의 GUID이고, `<logDialgoId>`는 검색할 로그 대화의 ID입니다.  

> [!NOTE]
> 개발자는 로그 대화를 편집한 다음, 학습 대화로 저장할 수 있습니다.  이 작업이 완료되면 Conversation Learner는 “원본” 로그 대화의 ID를 학습 대화와 함께 저장합니다.  또한 학습 대화 상자는 UI에서 “분기”할 수 있습니다. 학습 대화에 연결된 원본 로그 대화 ID가 있는 경우 해당 학습 대화의 분기는 동일한 로그 대화 ID로 표시됩니다.

로그 대화에서 파생된 모든 학습 대화를 가져오려면 다음 단계를 수행합니다.

먼저 모든 학습 대화를 검색합니다.

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialogs
```

여기서 `<appId>`는 이 Conversation Learner 모델의 GUID입니다.  

모든 학습 대화가 반환됩니다.  이 목록에서 연결된 `sourceLogDialogId`를 검색하고 연결된 `trainDialogId`를 확인합니다. 

ID별로 단일 학습 대화를 보려면 다음을 실행합니다.

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

여기서 `<appId>`는 이 Conversation Learner 모델의 GUID이고, `<trainDialogId>`는 검색할 학습 대화의 ID입니다.  

## <a name="how-to-delete-a-logged-dialog"></a>기록된 대화를 삭제하는 방법

해당 ID를 제공하여 로그 대화를 삭제하려면 다음 HTTP 호출을 사용할 수 있습니다.

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

여기서 `<appId>`는 이 Conversation Learner 모델의 GUID이고, `<logDialogId>`는 삭제할 로그 대화의 ID입니다. 

해당 ID를 제공하여 학습 대화를 삭제하려면 다음 HTTP 호출을 사용할 수 있습니다.

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

여기서 `<appId>`는 이 Conversation Learner 모델의 GUID이고, `<trainDialogId>`는 삭제할 학습 대화의 ID입니다. 
