---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 121f6ffa5c1a7c903e59be8a5bc3e1e1db0834fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80673318"
---
## <a name="add-an-output-binding-definition-to-the-function"></a>함수에 출력 바인딩 정의 추가

함수에는 하나의 트리거만 있을 수 있지만 여러 개의 입력 및 출력 바인딩이 있을 수 있으므로 사용자 지정 통합 코드를 작성하지 않고도 다른 Azure 서비스 및 리소스에 연결할 수 있습니다. 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
이러한 바인딩은 함수 폴더의 *function.json* 파일에서 선언합니다. 이전 빠른 시작에서 *HttpExample* 폴더의 *function.json* 파일에 있는 `bindings` 컬렉션에는 두 개의 바인딩이 포함되어 있습니다.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-python"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-powershell"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json" range="2-18":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript, programming-language-powershell, programming-language-typescript"  
각 바인딩에는 적어도 하나의 형식, 방향 및 이름이 있습니다. 위의 예제에서 첫 번째 바인딩은 `in` 방향의 `httpTrigger` 형식입니다. `in` 방향의 경우 `name`은 트리거에서 호출할 때 함수로 보내는 입력 매개 변수의 이름을 지정합니다.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
컬렉션의 두 번째 바인딩에는 `res`라는 이름이 지정됩니다. 이 `http` 바인딩은 HTTP 응답을 작성하는 데 사용되는 출력 바인딩(`out`)입니다. 

이 함수에서 Azure Storage 큐에 쓰려면 아래 코드와 같이 이름이 `msg`인 `queue` 형식의 `out` 바인딩을 추가합니다.

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
컬렉션의 두 번째 바인딩은 `out` 방향의 `http` 형식입니다. 이 경우 `$return`의 특수 `name`은 이 바인딩에서 입력 매개 변수를 제공하지 않고 함수의 반환 값을 사용함을 나타냅니다.

이 함수에서 Azure Storage 큐에 쓰려면 아래 코드와 같이 이름이 `msg`인 `queue` 형식의 `out` 바인딩을 추가합니다.

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
컬렉션의 두 번째 바인딩에는 `res`라는 이름이 지정됩니다. 이 `http` 바인딩은 HTTP 응답을 작성하는 데 사용되는 출력 바인딩(`out`)입니다. 

이 함수에서 Azure Storage 큐에 쓰려면 아래 코드와 같이 이름이 `msg`인 `queue` 형식의 `out` 바인딩을 추가합니다.

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
이 경우 `msg`가 출력 인수로 함수에 제공됩니다. `queue` 형식의 경우 `queueName`에는 큐 이름을 지정하고, `connection`에는 *local.settings.json*에 있는 Azure Storage 연결의 *name*을 제공해야 합니다. 
::: zone-end  
