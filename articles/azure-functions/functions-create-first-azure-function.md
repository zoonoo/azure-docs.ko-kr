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
ms.date: 02/09/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 7743bfb98552f7fa2334d8daa6a6f6935969f393
ms.openlocfilehash: d76630e0be4a021720d88e6d7e64cf2258f84266


---
# <a name="create-your-first-azure-function"></a>첫 번째 Azure Function 만들기
## <a name="overview"></a>개요
Azure 기능은 다른 Azure 서비스, SaaS 제품 및 온-프레미스 시스템에서 발생하는 이벤트에 의해 트리거되는 코드를 구현하는 기능으로 기존 Azure 응용 프로그램 플랫폼을 확장하는 이벤트 기반의 주문형 계산 환경입니다. Azure Functions를 통해 응용 프로그램은 요구에 따라 확장하고 사용하는 리소스에 대해서만 비용을 지불합니다. Azure Functions를 사용하여 다양한 프로그래밍 언어로 구현되는 예약되거나 트리거된 코드 단위를 만들 수 있습니다. Azure Functions에 대해 자세히 알아보려면 [Azure Functions 개요](functions-overview.md)를 참조하세요.

이 항목에서는 포털에서 Azure Functions 빠른 시작을 사용하여 HTTP 트리거에서 호출하는 간단한 "hello world" JavaScript 함수를 만드는 방법을 보여 줍니다. 포털에서 이러한 단계는 수행하는 방법을 보여 주는 짧은 비디오를 시청할 수도 있습니다.

## <a name="watch-the-video"></a>비디오 보기
다음 비디오는 이 자습서의 기본 단계를 수행하는 방법을 보여 줍니다. 

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-your-first-Azure-Function-simple/player]
> 
> 

## <a name="create-a-function-from-the-quickstart"></a>빠른 시작에서 함수 만들기
함수 앱은 Azure에서 함수 실행을 호스트합니다. 새 함수와 함수 앱을 만들려면 다음 단계를 따릅니다. 함수 앱은 기본 구성으로 생성됩니다. 명시적으로 함수 앱을 만드는 방법의 예는 [다른 Azure Functions 빠른 시작 자습서](functions-create-first-azure-function-azure-portal.md)를 참조하세요.

첫 번째 함수를 만들기 전에 활성 Azure 계정이 있어야 합니다. Azure 계정이 아직 없는 경우 [무료 계정을 사용](https://azure.microsoft.com/free/)할 수 있습니다.

1. [Azure Functions 포털](https://functions.azure.com/signin) 로 이동하여 Azure 계정으로 로그인합니다.
2. 새 함수 앱에 대해 고유한 **이름**을 입력하거나 자동 생성된 이름을 수락하거나 선호하는 **지역**을 선택한 후 **만들기 + 시작**을 클릭합니다. 문자, 숫자 및 하이픈만 포함할 수 있는 유효한 이름을 입력해야 합니다. 밑줄(**_**)은 허용되는 문자가 아닙니다.
3. **빠른 시작** 탭에서 **웹후크 + API** 및 **JavaScript**를 클릭한 다음 **함수 만들기**를 클릭합니다. 새로운 미리 정의된 JavaScript 함수가 만들어집니다. 
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)
4. (선택 사항) 빠른 시작의 이 시점에서 포털의 Azure Functions 기능을 둘러보도록 선택할 수 있습니다. 둘러보기를 완료했거나 건너뛴 경우 HTTP 트리거를 사용하여 새 함수를 테스트할 수 있습니다.

## <a name="test-the-function"></a>함수 테스트
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="next-steps"></a>다음 단계
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Feb17_HO2-->


