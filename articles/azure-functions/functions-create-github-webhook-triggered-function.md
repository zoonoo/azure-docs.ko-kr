---
title: "Azure에서 GitHub webhook를 통해 트리거되는 함수 만들기 | Microsoft 문서"
description: "Azure Functions를 사용하여 GitHub webhook를 통해 호출되는 서버 없는 함수를 만듭니다."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 36ef34b8-3729-4940-86d2-cb8e176fcc06
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/18/2017
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: d4354546f3342d65353a86a4cec7d02547ab92e7
ms.lasthandoff: 04/22/2017


---
# <a name="create-a-function-triggered-by-a-github-webhook"></a>GitHub webhook를 통해 트리거되는 함수 만들기

GitHub webhook를 통해 트리거되는 함수를 만드는 방법을 알아봅니다. 

![Azure Portal에서 함수 앱 만들기](./media/functions-create-github-webhook-triggered-function/function-app-in-portal-editor.png)

이 토픽을 진행하려면 [Azure Portal에서 첫 번째 함수 만들기](functions-create-first-azure-function.md) 토픽에서 만든 리소스가 필요합니다.

GitHub 계정도 필요합니다. 아직 없는 경우 [무료 GitHub 계정을 등록](https://github.com/join)할 수 있습니다. 

이 토픽의 모든 단계를 완료하는 데 5분 이상 걸리지 않습니다.

## <a name="find-your-function-app"></a>함수 앱 찾기    

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다. 

2. 포털 맨 위에 있는 검색 표시줄에 함수 앱의 이름을 입력하고 목록에서 선택합니다.

## <a name="create-function"></a>GitHub webhook를 통해 트리거되는 함수 만들기

1. 함수 앱에서 **참수** 옆에 있는 **+** 단추를 클릭하고, 원하는 언어의 **GitHubWebHook** 템플릿을 클릭하고, **만들기**를 클릭합니다.
   
    ![Azure Portal에서 GitHub 웹후크를 통해 트리거되는 함수를 만듭니다.](./media/functions-create-github-webhook-triggered-function/functions-create-github-webhook-trigger.png) 

2. **</> 함수 URL 가져오기**를 클릭한 다음 값을 복사하여 저장합니다. **</> GitHub 비밀 가져오기**에 대해 같은 작업을 수행합니다. 이러한 값은 GitHub에서 webhook를 구성할 때 사용됩니다. 

    ![함수 코드 검토](./media/functions-create-github-webhook-triggered-function/functions-copy-function-url-github-secret.png) 
         
다음으로 GitHub 리포지토리에 webhook를 만듭니다. 

## <a name="configure-the-webhook"></a>웹후크 구성
1. GitHub에서 소유한 리포지토리로 이동합니다. 분기된 모든 리포지토리를 사용할 수도 있습니다.
 
2. **설정**, **Webhooks**, **webhook 추가**를 차례로 클릭합니다.
   
    ![GitHub 웹후크를 추가합니다.](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-2.png)

3. 함수의 URL 및 암호를 **페이로드 URL** 및 **암호**에 붙여 넣고 **콘텐츠 형식**에 **application/json**을 선택합니다.

4. **개별 이벤트 선택**을 클릭하고 **문제 주석**을 선택하고 **웹후크 추가**를 클릭합니다.
   
    ![웹후크 URL 및 암호를 설정합니다.](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-3.png)

이제 새로운 문제 주석이 추가되면 함수를 트리거하도록 webhook가 구성되었습니다. 

## <a name="test-the-function"></a>함수 테스트
1. GitHub 리포지토리에서 **문제** 탭을 새 브라우저 창에서 엽니다.

2. 새 창에서 **새 문제**를 클릭하고 제목을 입력한 다음 **새 문제 제출**을 클릭합니다. 

2. 문제에 주석을 입력하고 **주석**을 클릭합니다. 

3. 다른 GitHub 창에서 새 webhook 옆에 있는 **편집**을 클릭하고, **최근 배달**이 나올 때까지 아래로 스크롤하고, 함수가 webhook 요청을 처리했는지 확인합니다. 
 
    ![웹후크 URL 및 암호를 설정합니다.](./media/functions-create-github-webhook-triggered-function/functions-github-webhook-triggered.png)

   함수의 응답에 `New GitHub comment: <Your issue comment text>`가 포함되어야 합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


