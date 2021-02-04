---
title: Azure Functions에서 함수를 사용하지 않도록 설정하는 방법
description: Azure Functions에서 함수를 사용하지 않도록 설정하고 사용하도록 설정하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 02/03/2021
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: cbb84308507ea15f1c44c00122a9a59472f12a88
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551046"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Azure Functions에서 함수를 사용하지 않도록 설정하는 방법

이 문서에서는 Azure Functions에서 함수를 사용하지 않도록 설정하는 방법을 설명합니다. 함수를 *사용하지 안도록 설정* 하는 것은 함수에 대해 정의된 자동 트리거를 런타임에서 무시하도록 하는 것입니다. 이렇게 하면 전체 함수 앱을 중지하지 않고도 특정 함수가 실행되지 않도록 할 수 있습니다.

함수를 사용 하지 않도록 설정 하는 권장 방법은로 설정 된 형식의 앱 설정을 사용 하는 것입니다 `AzureWebJobs.<FUNCTION_NAME>.Disabled` `true` . [Azure CLI](/cli/azure/) 및 [Azure Portal](https://portal.azure.com)의 함수 **개요** 탭을 사용 하는 등 여러 가지 방법으로이 응용 프로그램 설정을 만들고 수정할 수 있습니다. 

> [!NOTE]  
> 이 문서에서 설명하는 방법을 사용하여 HTTP 트리거 함수를 사용하지 않도록 설정하면 로컬 컴퓨터에서 실행 중일 때도 엔드포인트에 계속 액세스할 수 있습니다.  

## <a name="use-the-azure-cli"></a>Azure CLI 사용

Azure CLI에서 [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) 명령을 사용하여 앱 설정을 만들고 수정합니다. 다음 명령을 사용하여 `true`로 설정된 `AzureWebJobs.QueueTrigger.Disabled`라는 앱 설정을 만들어 `QueueTrigger`라는 함수를 사용하지 않도록 설정합니다. 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

이 함수를 다시 사용하도록 설정하려면 `false` 값을 사용하여 동일한 명령을 다시 실행합니다.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

## <a name="use-the-portal"></a>포털 사용

함수의 **개요** 페이지에서 **사용** 및 **사용 안 함** 단추를 사용할 수도 있습니다. 이러한 단추는 앱 설정 값을 변경 하는 방식으로 작동 `AzureWebJobs.<FUNCTION_NAME>.Disabled` 합니다. 이 함수 관련 설정은 처음 사용 하지 않도록 설정할 때 생성 됩니다. 

![함수 상태 스위치](media/disable-function/function-state-switch.png)

로컬 프로젝트에서 함수 앱에 게시 하는 경우에도 포털을 사용 하 여 함수 앱에서 함수를 사용 하지 않도록 설정할 수 있습니다. 

> [!NOTE]  
> 포털 통합 테스트 기능은 `Disabled` 설정을 무시합니다. 즉, 사용하지 않도록 설정한 함수는 포털의 **테스트** 창에서 시작할 경우 계속 실행됩니다. 

## <a name="localsettingsjson"></a>local.settings.json

함수는 로컬로 실행할 때와 동일한 방식으로 비활성화할 수 있습니다. 이라는 함수를 사용 하지 않도록 설정 하려면 다음과 `HttpExample` 같이 local.settings.js파일의 Values 컬렉션에 항목을 추가 합니다.

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "UseDevelopmentStorage=true", 
    "AzureWebJobs.HttpExample.Disabled": "true"
  }
}
``` 

## <a name="other-methods"></a>다른 방법

애플리케이션 설정 방법은 모든 언어 및 모든 런타임 버전에 권장되지만 함수를 사용하지 않도록 설정하는 몇 가지 다른 방법이 있습니다. 언어 및 런타임 버전에 따라 달라지는 이러한 방법은 이전 버전과의 호환성을 위해 유지 관리됩니다. 

### <a name="c-class-libraries"></a>C# 클래스 라이브러리

클래스 라이브러리 함수에서 `Disable` 특성을 사용하여 함수가 트리거되지 않도록 할 수도 있습니다. 이 특성을 사용 하면 함수를 사용 하지 않도록 설정 하는 데 사용 되는 설정의 이름을 사용자 지정할 수 있습니다. 다음 예제와 같이 부울 앱 설정을 참조 하는 생성자 매개 변수를 정의할 수 있는 특성의 버전을 사용 합니다.

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

이 메서드를 사용하면 다시 컴파일하거나 다시 배포하지 않고 앱 설정을 변경하여 함수를 사용하지 않도록 설정할 수 있습니다. 앱 설정을 바꾸면 사용 안 함 상태 변경을 즉시 인식할 수 있도록 함수 앱이 다시 시작하게 됩니다.

또한 설정 이름에 대 한 문자열을 허용 하지 않는 매개 변수에 대 한 생성자가 있습니다. 이 버전의 특성은 권장 되지 않습니다. 이 버전을 사용 하는 경우 프로젝트를 다시 컴파일하고 다시 배포 하 여 함수의 비활성화 상태를 변경 해야 합니다.

### <a name="functions-1x---scripting-languages"></a>Functions 1.x - 스크립팅 언어

버전 1.x에서는 *function.json* 파일의 `disabled` 속성을 사용하여 런타임에서 함수를 트리거하지 않도록 지시할 수도 있습니다. 이 방법은 C# 스크립트 및 JavaScript와 같은 스크립트 언어에 대해서만 작동합니다. `disabled` 속성은 `true` 또는 앱 설정 이름으로 설정할 수 있습니다.

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
또는 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

두 번째 예제에서는 IS_DISABLED라고 명명되고 `true` 또는 1로 설정된 앱 설정이 있는 경우 함수가 사용하지 않도록 설정됩니다.

>[!IMPORTANT]  
>포털에서는 응용 프로그램 설정을 사용 하 여 v1. x 함수를 사용 하지 않도록 설정 합니다. 애플리케이션 설정이 function.json 파일과 충돌하면 오류가 발생할 수 있습니다. 오류를 방지하려면 function.json 파일에서 `disabled` 속성을 제거해야 합니다. 


## <a name="next-steps"></a>다음 단계

이 문서에서는 자동 트리거를 사용하지 않도록 설정하는 방법을 설명합니다. 트리거에 대한 자세한 내용은 [트리거 및 바인딩](functions-triggers-bindings.md)을 참조하세요.
