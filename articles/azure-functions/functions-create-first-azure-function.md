---
title: "첫 번째 Azure Function 만들기 | Microsoft Docs"
description: "2분 이내에 서버가 없는 응용 프로그램인 첫 번째 Azure Function을 작성합니다."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 4a1669e7-233e-4ea2-9b83-b8624f2dbe59
ms.service: functions
ms.devlang: multiple
ms.topic: hero-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/14/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: a797910e286cd2aacf5a8aa6c509e2b0f5f60276
ms.lasthandoff: 03/17/2017


---
# <a name="create-your-first-azure-function"></a>첫 번째 Azure Function 만들기

이 항목에서는 포털에서 Azure Functions 빠른 시작을 사용하여 HTTP 요청에서 호출하는 간단한 "hello world" 함수를 만드는 방법을 보여 줍니다. Azure Functions에 대해 자세히 알아보려면 [Azure Functions 개요](functions-overview.md)를 참조하세요.

시작하기 전에 Azure 계정이 있어야 합니다. [무료 계정](https://azure.microsoft.com/free/)을 사용할 수 있습니다. 또한 Azure에 등록하지 않고 [Azure Functions를 시도](https://azure.microsoft.com/try/app-service/functions/)할 수도 있습니다.

## <a name="create-a-function-from-the-portal-quickstart"></a>포털 빠른 시작에서 함수 만들기

1. [Azure Functions 포털](https://functions.azure.com/signin)로 이동하고 Azure 계정으로 로그인합니다. 
 
2. 새 함수 앱에 대해 고유한 **이름**을 입력하거나 자동 생성된 이름을 수락하거나 선호하는 **지역**을 선택한 후 **만들기 + 시작**을 클릭합니다. 유효한 이름에는 문자, 숫자 및 하이픈만 포함할 수 있습니다. 밑줄(**_**)은 허용되는 문자가 아닙니다.

3. **빠른 시작** 탭에서 **웹후크 + API**를 클릭하고 함수에 대한 언어를 선택한 다음 **함수 만들기**를 클릭합니다. 새 미리 정의된 함수는 선택한 언어로 생성됩니다. 
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

4. (선택 사항) 빠른 시작의 이 시점에서 포털의 Azure Functions 기능을 둘러보도록 선택할 수 있습니다. 둘러보기를 완료했거나 건너뛴 경우 HTTP 요청을 전송하여 새 함수를 테스트할 수 있습니다.

## <a name="test-the-function"></a>함수 테스트
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="watch-the-video"></a>비디오 보기
다음 비디오는 이 자습서의 기본 단계를 수행하는 방법을 보여 줍니다. 

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-your-first-Azure-Function-simple/player]
> 


## <a name="next-steps"></a>다음 단계
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


