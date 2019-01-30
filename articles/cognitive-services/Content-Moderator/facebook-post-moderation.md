---
title: '자습서: Facebook 콘텐츠 조정 - Azure Content Moderator'
titlesuffix: Azure Cognitive Services
description: 이 자습서에서는 기계 학습 기반 Content Moderator를 사용하여 Facebook 게시물 및 의견을 조정하는 방법을 알아봅니다.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: tutorial
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 085ddaf757f2a11b4db4aadb22ad16009d29231e
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54260449"
---
# <a name="tutorial-facebook-content-moderation-with-content-moderator"></a>자습서: Content Moderator를 사용하여 Facebook 콘텐츠 조정

이 자습서에서는 기계 학습 기반 Content Moderator를 사용하여 Facebook 게시물 및 의견을 조정하는 방법을 알아봅니다.

이 자습서에서는 다음 단계를 안내합니다.

1. Content Moderator 팀을 만듭니다.
2. Content Moderator 및 Facebook에서 HTTP 이벤트에 대해 수신 대기하는 Azure Functions를 만듭니다.
3. Facebook 페이지 및 앱을 만들고, Content Moderator에 연결합니다.

완료한 후 Facebook은 방문자에 의해 게시된 콘텐츠를 Content Moderator에 보냅니다. 일치 임계값에 따라 Content Moderator 워크플로는 콘텐츠를 게시하거나 검토 도구 내에서 검토를 만듭니다. 

다음 그림에서는 솔루션의 빌딩 블록을 보여줍니다.

![Facebook 게시물 조정](images/tutorial-facebook-moderation.png)

## <a name="create-a-content-moderator-team"></a>Content Moderator 팀 만들기

[웹에서 Content Moderator 사용해 보기](quick-start.md) 빠른 시작을 참조하여 Content Moderator에 가입하고 팀을 만듭니다.

## <a name="configure-image-moderation-workflow-threshold"></a>이미지 중재 워크플로(임계값) 구성

[워크플로](review-tool-user-guide/workflows.md) 페이지를 참조하여 사용자 지정 이미지 워크플로(임계값)를 구성합니다. 워크플로 **이름**을 참고합니다.

## <a name="3-configure-text-moderation-workflow-threshold"></a>3. 텍스트 중재 워크플로(임계값) 구성

[워크플로](review-tool-user-guide/workflows.md) 페이지와 유사한 단계를 사용하여 사용자 지정 텍스트 임계값 및 워크플로를 구성합니다. 워크플로 **이름**을 참고합니다.

![텍스트 워크플로 구성](images/text-workflow-configure.PNG)

"워크플로 실행" 단추를 사용하여 워크플로를 테스트합니다.

![텍스트 워크플로 테스트](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Azure Functions 만들기

[Azure 관리 포털](https://portal.azure.com/)에 로그인하여 Azure Functions를 만듭니다. 다음 단계를 수행하세요.

1. [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) 페이지에 표시된 대로 Azure 함수 앱을 만듭니다.
2. 새로 만든 함수 앱을 엽니다.
3. 앱 내에서 **플랫폼 기능 -&gt; 애플리케이션 설정**으로 이동합니다.
4. 다음 [애플리케이션 설정](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings)을 정의합니다.

> [!NOTE]
> **cm: Region**은 지역의 이름이어야 합니다(공백 없이).
> 예를 들어 West Europe이 아닌 **westeurope**, West Central US가 아닌 **westcentralus** 등입니다.
>

| 앱 설정 | 설명   | 
| -------------------- |-------------|
| cm:TeamId   | Content Moderator 팀 ID  | 
| cm:SubscriptionKey | Content Moderator 구독 키 - [자격 증명](review-tool-user-guide/credentials.md) 참조 | 
| cm:Region | Content Moderator 지역 이름(공백 없이) 위의 참고를 참조하세요. |
| cm:ImageWorkflow | 이미지에서 실행할 워크플로의 이름 |
| cm:TextWorkflow | 텍스트에서 실행할 워크플로의 이름 |
| cm:CallbackEndpoint | 이 가이드의 뒷부분에서 만드는 CMListener 함수 앱에 대한 Url |
| fb:VerificationToken | Facebook 피드 이벤트에 구독하는 데도 사용되는 비밀 토큰 |
| fb:PageAccessToken | Facebook 그래프 API 액세스 토큰은 만료되지 않으며 사용자를 대신하여 함수 게시 숨기기/삭제를 허용합니다. |

5. **FBListener**라는 새 **HttpTrigger-CSharp** 함수를 만듭니다. 이 함수는 Facebook의 이벤트를 수신합니다. 다음 단계를 수행하여 이 함수를 만듭니다.

    1. 참조를 위해 [Azure Functions 생성](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) 페이지를 열어 둡니다.
    2. **+** 를 클릭하여 새 함수 만들기에 추가합니다.
    3. 기본 제공 템플릿 대신 **사용자 고유의/사용자 지정 함수에서 시작** 옵션을 선택합니다.
    4. **HttpTrigger-CSharp**라는 타일을 클릭합니다.
    5. 이름 **FBListener**를 입력합니다. **권한 수준** 필드를 **함수**로 설정해야 합니다.
    6. **만들기**를 클릭합니다.
    7. **run.csx**의 콘텐츠를 [**FbListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FbListener/run.csx)의 콘텐츠로 바꿉니다.

6. **CMListener**라는 새 **HttpTrigger-CSharp** 함수를 만듭니다. 이 함수는 Content Moderator에서 이벤트를 수신합니다. 다음 단계에 따라 이 함수를 만듭니다.

    1. 참조를 위해 [Azure Functions 생성](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) 페이지를 열어 둡니다.
    2. **+** 를 클릭하여 새 함수 만들기에 추가합니다.
    3. 기본 제공 템플릿 대신 **사용자 고유의/사용자 지정 함수에서 시작** 옵션을 선택합니다.
    4. **HttpTrigger-CSharp**라는 타일을 클릭합니다.
    5. 이름 **CMListener**를 입력합니다. **권한 수준** 필드를 **함수**로 설정해야 합니다.
    6. **만들기**를 클릭합니다.
    7. **run.csx**의 콘텐츠를 [**CMListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/CmListener/run.csx)의 콘텐츠로 바꿉니다.

## <a name="configure-the-facebook-page-and-app"></a>Facebook 페이지 및 앱 구성
1. Facebook 앱을 만듭니다.

    1. [Facebook 개발자 사이트](https://developers.facebook.com/)로 이동합니다.
    2. **내 앱**을 클릭합니다.
    3. 새 앱을 추가합니다.
    4. **웹후크 -> 시작**을 선택합니다.
    5. **페이지 -> 이 항목 구독**을 선택합니다.
    6. 콜백 URL로 **FBListener Url** 및 **함수 앱 설정** 아래에서 구성한 **토큰 확인**을 제공합니다.
    7. 구독한 후 피드 아래로 스크롤하고 **구독**을 선택합니다.

2. Facebook 페이지를 만듭니다.

    1. [Facebook](https://www.facebook.com/bookmarks/pages)으로 이동하고 **새 Facebook 페이지**를 만듭니다.
    2. Facebook 앱에서 이러한 단계를 따라 이 페이지에 액세스하도록 허용합니다.
        1. [Graph API 탐색기](https://developers.facebook.com/tools/explorer/)로 이동합니다.
        2. **애플리케이션**을 선택합니다.
        3. **페이지 액세스 토큰**을 선택하고, **Get** 요청을 보냅니다.
        4. 응답에서 **페이지 ID**를 클릭합니다.
        5. 이제 URL에 **/subscribed_apps**를 추가하고 **Get**(빈 응답) 요청을 보냅니다.
        6. **Post** 요청을 제출합니다. **success: true**로 응답을 받습니다.

3. 만료되지 않는 Graph API 액세스 토큰을 만듭니다.

    1. [Graph API 탐색기](https://developers.facebook.com/tools/explorer/)로 이동합니다.
    2. **애플리케이션** 옵션을 선택합니다.
    3. **사용자 액세스 토큰 가져오기** 옵션을 선택합니다.
    4. **권한 선택** 아래에서 **manage_pages** 및 **publish_pages** 옵션을 선택합니다.
    5. 다음 단계에서 **액세스 토큰**(수명이 짧은 토큰)을 사용합니다.

4. 다음 몇 단계에 Postman을 사용합니다.

    1. **Postman**을 엽니다(또는 [여기](https://www.getpostman.com/)에서 가져옴).
    2. 다음 두 파일을 가져옵니다.
        1. [Postman 컬렉션](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json)
        2. [Postman 환경](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json)       
    3. 이러한 환경 변수를 업데이트합니다.
    
    | 키 | 값   | 
    | -------------------- |-------------|
    | appId   | 여기에 Facebook 앱 식별자를 삽입합니다.  | 
    | appSecret | 여기에 Facebook 앱의 비밀을 삽입합니다. | 
    | short_lived_token | 이전 단계에서 생성한 수명이 짧은 사용자 액세스 토큰을 삽입합니다. |
    4. 이제 컬렉션에 나열된 3개의 API를 실행합니다. 
        1. **수명이 긴 액세스 토큰 생성**을 선택하고 **보내기**를 클릭합니다.
        2. **사용자 ID 가져오기**를 선택하고 **보내기**를 클릭합니다.
        3. **영구 페이지 액세스 토큰 가져오기**를 선택하고 **보내기**를 클릭합니다.
    5. 응답에서 **access_token** 값을 복사하고 앱 설정, **fb:PageAccessToken**에 할당합니다.

이것으로 끝입니다.

솔루션은 Facebook 페이지에 게시된 모든 이미지 및 텍스트를 Content Moderator에 보냅니다. 이전에 구성한 워크플로가 호출됩니다. 워크플로에서 정의한 조건을 통과하지 않는 콘텐츠는 검토 도구 내의 검토에 발생합니다. 나머지 콘텐츠는 게시됩니다.

## <a name="license"></a>라이선스

Microsoft Cognitive Services SDK 및 샘플은 모두 MIT 라이선스를 통해 사용이 허가됩니다. 자세한 내용은 [라이선스](https://microsoft.mit-license.org/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

1. Microsoft Build 2017에서 이 솔루션의 [데모(비디오)를 봅니다](https://channel9.msdn.com/Events/Build/2017/T6033).
1. [GitHub의 Facebook 샘플](https://github.com/MicrosoftContentModerator/samples-fbPageModeration)
1. https://docs.microsoft.com/azure/azure-functions/functions-create-github-webhook-triggered-function
2. http://ukimiawz.github.io/facebook/2015/08/12/webhook-facebook-subscriptions/
3. http://stackoverflow.com/questions/17197970/facebook-permanent-page-access-token
