---
title: "Azure Portal에서 첫 번째 함수 만들기 | Microsoft Docs"
description: "Azure를 시작합니다. Azure Portal에서 첫 번째 Azure Function을 만듭니다."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/15/2017
ms.author: glenga
ms.custom: welcome-email
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: 6bb15dfd0e2c40b99d06ef2d695e3ba7ca78e91b
ms.lasthandoff: 03/22/2017


---
# <a name="create-your-first-function-in-the-azure-portal"></a>Azure Portal에서 첫 번째 Azure Function을 만듭니다.

이 토픽에서는 HTTP 요청에서 호출되는 간단한 "hello world" Azure Function을 만드는 방법을 보여 줍니다. Azure Portal에서 함수를 만들기 전에 Azure App Service에서 함수 앱을 만들어서 함수의 실행을 호스트해야 합니다.

이 빠른 시작을 완료하려면 Azure 계정이 있어야 합니다. [무료 계정](https://azure.microsoft.com/free/)을 사용할 수 있습니다. 또한 Azure에 등록하지 않고 [Azure Functions를 사용](https://azure.microsoft.com/try/app-service/functions/)할 수도 있습니다.

## <a name="create-a-function-app"></a>함수 앱 만들기

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

자세한 내용은 [Azure Portal에서 함수 앱 만들기](functions-create-function-app-portal.md)를 참조하세요.

## <a name="create-a-function"></a>함수 만들기
이 단계에서는 Azure Functions 빠른 시작을 사용하여 새로운 함수 앱에서 함수를 만듭니다.

1. **빠른 시작** 탭에서 **WebHook + API**를 클릭하고 함수에 대한 언어를 선택한 다음 **함수 만들기**를 클릭합니다. 새 미리 정의된 함수는 선택한 언어로 생성됩니다.  
   
    ![](./media/functions-create-first-azure-function-azure-portal/function-app-quickstart-node-webhook.png)

4. (선택 사항) 빠른 시작의 이 시점에서 포털의 Azure Functions 기능을 둘러보도록 선택할 수 있습니다. 둘러보기를 완료했거나 건너뛴 경우 HTTP 요청을 전송하여 새 함수를 테스트할 수 있습니다.


## <a name="test-the-function"></a>함수 테스트
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="next-steps"></a>다음 단계
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


