---
title: Azure Functions를 사용하여 .NET 5 함수 개발 및 게시
description: .NET 5.0을 사용하여 C# 함수를 만들고 디버그한 다음 Azure Functions에서 서버리스 호스트에 로컬 프로젝트를 배포하는 방법을 알아봅니다.
ms.date: 05/03/2021
ms.topic: how-to
recommendations: false
zone_pivot_groups: development-environment-functions
ms.openlocfilehash: 6521c02686da55142d9a9d9f1faf569d584ef593
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111590007"
---
# <a name="develop-and-publish-net-5-functions-using-azure-functions"></a>Azure Functions를 사용하여 .NET 5 함수 개발 및 게시 

이 문서에서는 Azure Functions 런타임에서 Out of Process로 실행되는 .NET 5.0을 사용하여 C# 함수로 작업하는 방법을 보여 줍니다. Azure에 이러한 .NET 격리 프로세스 함수를 만들고, 로컬로 디버그하고, 게시하는 방법을 알아봅니다. Azure에서 이러한 함수는 .NET 5.0을 지원하는 격리 프로세스에서 실행됩니다. 자세한 내용은 [Azure의 .NET 5.0에서 함수를 실행하는 방법에 대한 가이드](dotnet-isolated-process-guide.md)를 참조하세요.

.NET 5.0을 지원하거나 Out of Process 함수를 실행하지 않아도 되는 경우 대신 [C# 클래스 라이브러리 함수를 만들](functions-create-your-first-function-visual-studio.md) 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

+ 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="development-environment-vscode,development-environment-cli"  
+ [.NET 5.0 SDK](https://dotnet.microsoft.com/download)

+ [Azure Functions Core Tools](functions-run-local.md#v2) 버전 3.0.3381 이상.

+ [Azure CLI](/cli/azure/install-azure-cli) 버전 2.20 이상.  
::: zone-end  
::: zone pivot="development-environment-vscode"  
+ [지원되는 플랫폼](https://code.visualstudio.com/docs/supporting/requirements#_platforms) 중 하나인 [Visual Studio Code](https://code.visualstudio.com/).  

+ Visual Studio Code용 [C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).  

+ Visual Studio Code 버전 1.3.0 이상용 [Azure Functions 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions).
::: zone-end  
::: zone pivot="development-environment-vs"  
+ [Visual Studio 2019](https://azure.microsoft.com/downloads/) 버전 16.10 이상. 설치에는 **Azure 개발** 또는 **ASP.NET 및 웹 개발** 워크로드가 포함되어야 합니다.  
::: zone-end  

## <a name="create-a-local-function-project"></a>로컬 함수 프로젝트 만들기

Azure Functions에서 함수 프로젝트는 각각 특정 트리거에 응답하는 하나 이상의 개별 함수에 대한 컨테이너입니다. 프로젝트의 모든 함수는 동일한 로컬 및 호스팅 구성을 공유합니다. 이 섹션에서는 단일 함수가 포함된 함수 프로젝트를 만듭니다.

::: zone pivot="development-environment-vscode"  
1. 작업 막대에서 Azure 아이콘을 선택한 다음, **Azure: Functions** 영역에서 **새 프로젝트 만들기...** 아이콘을 선택합니다.

    ![새 프로젝트 만들기 선택](./media/functions-create-first-function-vs-code/create-new-project.png)

1. 프로젝트 작업 영역에 대한 디렉터리 위치를 선택하고 **선택** 을 선택합니다.

    > [!NOTE]
    > 다음 단계는 작업 영역 외부에서 완료하도록 설계되었습니다. 이 경우 작업 영역에 포함된 프로젝트 폴더를 선택하지 마십시오.

1. 프롬프트에서 다음 정보를 제공합니다.

    + **함수 프로젝트에 대한 언어 선택**: `C#`을 선택합니다.

    + **.NET 런타임 선택**: `.NET 5 isolated`를 선택합니다.

    + **프로젝트의 첫 번째 함수에 대한 템플릿 선택**: `HTTP trigger`을 선택합니다.

    + **함수 이름 입력**: `HttpExample`.

    + **네임스페이스 입력**: `My.Functions`.

    + **권한 부여 수준**: 누구나 함수 엔드포인트를 호출할 수 있도록 하는 `Anonymous`를 선택합니다. 권한 부여 수준에 대해 알아보려면 [권한 부여 키](functions-bindings-http-webhook-trigger.md#authorization-keys)를 참조하세요.

    + **프로젝트를 여는 방법 선택**: `Add to workspace`을 선택합니다.

1. Visual Studio Code는 이 정보를 사용하여 HTTP 트리거를 통해 Azure Functions 프로젝트를 생성합니다. 탐색기에서 로컬 프로젝트 파일을 볼 수 있습니다. 생성된 파일에 대한 자세한 내용은 [생성된 프로젝트 파일](functions-develop-vs-code.md#generated-project-files)을 참조하세요.
::: zone-end  
::: zone pivot="development-environment-cli"  

1. 다음과 같이 `func init` 명령을 실행하여 *LocalFunctionProj* 폴더에 함수 프로젝트를 만듭니다.  

    ```console
    func init LocalFunctionProj --worker-runtime dotnetisolated
    ```
    
    `dotnetisolated`를 지정하면 .NET 5.0에서 실행하는 프로젝트가 생성됩니다.


1. 프로젝트 폴더로 이동합니다.

    ```console
    cd LocalFunctionProj
    ```

    이 폴더에는 [ocal.settings.json](functions-run-local.md#local-settings-file) 및 [host.json](functions-host-json.md) 구성 파일을 비롯하여 프로젝트의 다양한 파일이 있습니다. *local.settings.json* 은 Azure에서 다운로드한 비밀을 포함할 수 있으므로 이 파일은 기본적으로 *.gitignore* 파일의 원본 제어에서 제외됩니다.

1. 다음 명령을 사용하여 함수를 프로젝트에 추가합니다. 여기서 `--name` 인수는 함수(HttpExample)의 고유 이름이고, `--template` 인수는 함수의 트리거(HTTP)를 지정합니다.

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 

    `func new`는 HttpExample.cs 코드 파일을 만듭니다.
::: zone-end  

::: zone pivot="development-environment-vs"

1. Visual Studio 메뉴에서 **파일** > **새로 만들기** > **프로젝트** 를 차례로 선택합니다.

1. **새 프로젝트 만들기** 에서 검색 상자에 *함수* 를 입력하고, **Azure Functions** 템플릿을 선택한 다음, **다음** 을 선택합니다.

1. **새 프로젝트 구성** 에서 프로젝트에 대한 **프로젝트 이름** 을 입력한 다음, **만들기** 를 선택합니다. 함수 앱 이름은 C# 네임스페이스로 유효해야 하므로 밑줄, 하이픈 또는 기타 영숫자가 아닌 문자는 사용하지 마세요.

1. **새 Azure Functions 애플리케이션 만들기** 설정에 대해서는 다음 표의 값을 사용합니다.

    | 설정      | 값  | 설명                      |
    | ------------ |  ------- |----------------------------------------- |
    | **.NET 버전** | **.NET 5(격리)** | 이 값은 격리된 프로세스의 .NET 5.0에서 실행되는 함수 프로젝트를 만듭니다.   |
    | **함수 템플릿** | **HTTP 트리거** | 이 값은 HTTP 요청에 의해 트리거되는 함수를 만듭니다. |
    | **스토리지 계정(AzureWebJobsStorage)**  | **스토리지 에뮬레이터** | Azure의 함수 앱에는 스토리지 계정이 필요하기 때문에 Azure에 프로젝트를 게시할 때 할당되거나 생성됩니다. HTTP 트리거는 Azure Storage 계정 연결 문자열을 사용하지 않습니다. 다른 모든 트리거 형식에는 유효한 Azure Storage 계정 연결 문자열이 필요합니다.  |
    | **권한 부여 수준** | **익명** | 만들어진 함수를 모든 클라이언트에서 키를 제공하지 않고 트리거할 수 있습니다. 이 권한 부여 설정을 통해 새 함수를 쉽게 테스트할 수 있습니다. 키 및 권한 부여에 대한 자세한 내용은 [권한 부여 키](functions-bindings-http-webhook-trigger.md#authorization-keys) 및 [HTTP 및 웹후크 바인딩](functions-bindings-http-webhook.md)을 참조하세요. |
    
    
    ![Azure Functions 프로젝트 설정](./media/dotnet-isolated-process-developer-howtos/functions-project-settings.png)

    **권한 부여 수준** 을 **익명** 으로 설정했는지 확인합니다. 기본 수준인 **함수** 를 선택하면 함수 엔드포인트에 액세스하도록 요구하는 요청에 [함수 키](functions-bindings-http-webhook-trigger.md#authorization-keys)를 제공해야 합니다.

1. **만들기** 를 선택하여 함수 프로젝트 및 HTTP 트리거 함수를 만듭니다.

Visual Studio는 HTTP 트리거 함수 형식에 대한 상용구 코드가 포함된 프로젝트와 클래스를 만듭니다. 상용구 코드는 "Azure Functions 시작!"을 보냅니다. HTTP 응답. `HttpTrigger` 특성은 HTTP 요청에서 함수를 트리거하도록 지정합니다. 

## <a name="rename-the-function"></a>함수 이름 바꾸기

`FunctionName` 메서드 특성은 함수 이름을 설정하며, 생성되는 기본값은 `Function1`입니다. 프로젝트를 만들 때 도구를 사용하여 기본 함수 이름을 재정의할 수 없으므로 함수 클래스, 파일 및 메타데이터에 따라 더욱 적절한 이름을 생성해야 합니다.

1. **파일 탐색기** 에서 Function1.cs 파일을 마우스 오른쪽 단추로 클릭하고 이름을 `HttpExample.cs`로 바꿉니다.

1. 코드에서 Function1 클래스의 이름을 `HttpExample`로 바꿉니다.

1. `Run`이라는 `HttpTrigger` 메서드에서 `FunctionName` 메서드 특성의 이름을 `HttpExample`로 바꾸고 값을 `GetLogger` 메서드에 전달합니다.

함수 정의는 이제 다음 코드와 같이 표시됩니다.

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-isolated/HttpExample.cs" range="9-15":::
 
이제 함수의 이름을 바꾸었으므로 로컬 컴퓨터에서 이를 테스트할 수 있습니다.

## <a name="run-the-function-locally"></a>로컬에서 함수 실행

Visual Studio는 Azure Functions Core Tools와 통합되므로 Azure Functions 런타임 전체를 사용하여 함수를 로컬로 테스트할 수 있습니다.  

1. 함수를 실행하려면 Visual Studio에서 <kbd>F5</kbd>를 누릅니다. 도구에서 HTTP 요청을 처리할 수 있도록 방화벽 예외를 사용하도록 설정해야 할 수도 있습니다. 함수를 로컬로 실행하는 경우 권한 부여 수준이 적용되지 않습니다.

1. Azure Functions 런타임 출력에서 함수의 URL을 복사하고 요청을 실행합니다. 함수가 성공적으로 실행되고 로그가 런타임 출력에 기록되면 함수 시작 메시지가 표시됩니다. 

1. 디버깅을 중지하려면 Visual Studio에서 <kbd>Shift</kbd>+<kbd>F5</kbd>를 누릅니다.

함수가 로컬 컴퓨터에서 제대로 실행되는지 확인한 후에 해당 프로젝트를 Azure에 게시해야 합니다.

::: zone-end

::: zone pivot="development-environment-vscode"  

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end  
::: zone pivot="development-environment-cli" 

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>함수를 지원하는 Azure 리소스 만들기

함수 코드를 Azure에 배포하기 전에 다음 세 가지 리소스를 만들어야 합니다.

- [리소스 그룹](../azure-resource-manager/management/overview.md) - 관련 리소스에 대한 논리 컨테이너입니다.
- [스토리지 계정](../storage/common/storage-account-create.md) - 함수에 대한 상태 및 기타 정보를 유지 관리합니다.
- 함수 앱 - 함수 코드를 실행할 수 있는 환경을 제공합니다. 함수 앱은 로컬 함수 프로젝트에 매핑되며, 함수를 논리적 단위로 그룹화하여 리소스를 더 쉽게 관리, 배포 및 공유할 수 있습니다.

다음 명령을 사용하여 이러한 항목을 만듭니다.

1. 아직 로그인하지 않은 경우 Azure에 로그인합니다.

    ```azurecli
    az login
    ```

    [az login](/cli/azure/reference-index#az_login) 명령은 Azure 계정에 로그인합니다.

1. `westeurope` 지역에 `AzureFunctionsQuickstart-rg`라는 리소스 그룹을 만듭니다.

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    [az group create](/cli/azure/group#az_group_create) 명령은 리소스 그룹을 만듭니다. 일반적으로 `az account list-locations` 명령에서 반환된 사용 가능한 지역을 사용하여 가까운 지역에 리소스 그룹과 리소스를 만듭니다.

1. 범용 스토리지 계정을 리소스 그룹 및 지역에 만듭니다.

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    [az storage account create](/cli/azure/storage/account#az_storage_account_create) 명령은 스토리지 계정을 만듭니다. 

    이전 예제에서 `<STORAGE_NAME>`을 사용자에게 적절하고 Azure Storage에서 고유한 이름으로 바꿉니다. 이름은 3~24자의 숫자와 소문자만 포함해야 합니다. `Standard_LRS`는 범용 계정을 지정하며, [Functions로 지원](storage-considerations.md#storage-account-requirements)됩니다.

1. Azure에서 함수 앱을 만듭니다.
   
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet-isolated --runtime-version 5.0 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```

    [az functionapp create](/cli/azure/functionapp#az_functionapp_create) 명령은 Azure에서 함수 앱을 만듭니다. 
    
    이전 예제에서는 `<STORAGE_NAME>`을 이전 단계에서 사용한 계정의 이름으로 바꾸고, `<APP_NAME>`을 적절하고 전역적으로 고유한 이름으로 바꿉니다. `<APP_NAME>`은 함수 앱의 기본 DNS 도메인이기도 합니다. 
    
    이 명령은 [Azure Functions 소비 계획](consumption-plan.md)에 따라 .NET 5.0을 실행하는 함수 앱을 만듭니다. 이 계획은 이 문서에서 발생하는 사용량의 양에 대해 무료로 제공됩니다. 또한 이 명령은 동일한 리소스 그룹에서 연결된 Azure Application Insights 인스턴스를 프로비전합니다. 이 인스턴스를 사용하여 함수 앱을 모니터링하고 로그를 봅니다. 자세한 내용은 [Azure Functions 모니터링](functions-monitoring.md)을 참조하세요. 인스턴스를 활성화할 때까지 비용이 발생하지 않습니다.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

::: zone-end

:::zone pivot="development-environment-vs"

## <a name="publish-the-project-to-azure"></a>Azure에 프로젝트 게시

프로젝트를 게시하려면 먼저 Azure 구독에 함수 앱이 있어야 합니다. 프로젝트를 처음 게시할 때 Visual Studio 게시에서 함수 앱을 만듭니다.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="verify-your-function-in-azure"></a>Azure에서 함수 확인

1. 클라우드 탐색기에서 새 함수 앱을 선택해야 합니다. 그렇지 않은 경우 구독 > **App Services** 를 확장하고 새 함수 앱을 선택합니다.

1. 마우스 오른쪽 단추로 함수 앱을 클릭하고 **브라우저에서 열기** 를 선택합니다. 그러면 기본 웹 브라우저에서 함수 앱의 루트가 열리고 함수 앱이 실행 중임을 나타내는 페이지가 표시됩니다. 

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure.png" alt-text="함수 앱 실행 중":::

1. 브라우저의 주소 표시줄에서 경로 `/api/HttpExample`을 기준 URL에 추가하고 요청을 실행합니다.

1. 이 URL로 이동하면 로컬에서 실행할 때와 동일한 응답이 브라우저에 표시됩니다.

:::zone-end

::: zone pivot="development-environment-vscode"

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-the-project-to-azure"></a>Azure에 프로젝트 게시

이 섹션에서는 Azure 구독에서 함수 앱 및 관련 리소스를 만든 다음, 코드를 배포합니다.

> [!IMPORTANT]
> 기존 함수 앱에 게시하면 Azure에서 해당 앱의 콘텐츠를 덮어씁니다.


1. 작업 막대에서 Azure 아이콘을 선택한 다음, **Azure: Functions** 영역에서 **함수 앱에 배포...** 단추를 선택합니다.

    ![Azure에 프로젝트 게시](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. 프롬프트에서 다음 정보를 제공합니다.

    - **폴더 선택**: 작업 영역에서 폴더를 선택하거나 함수 앱을 포함하는 폴더를 찾습니다. 유효한 함수 앱이 이미 열려 있는 경우에는 이 메시지가 표시되지 않습니다.

    - **구독 선택**: 사용할 구독을 선택합니다. 구독이 한 개만 있으면 이 메시지가 표시되지 않습니다.

    - **Azure에서 함수 앱 선택**: `- Create new Function App`을 선택합니다. (이 문서에서 다루지 않는 `Advanced` 옵션은 선택하지 마세요.)
      
    - **함수 앱에 대해 전역적으로 고유 이름을 입력합니다**. URL 경로에 유효한 이름을 입력합니다. 입력한 이름이 Azure Functions에서 고유한지 확인하기 위해 유효성을 검사합니다.
    
    - **런타임 스택 선택**: `.NET 5 (non-LTS)`을 선택합니다. 
    
    - **새 리소스의 위치 선택**:  성능을 향상시키려면 근처에 있는 [지역](https://azure.microsoft.com/regions/)을 선택합니다. 
    
    알림 영역에서 Azure에 생성된 개별 리소스의 상태를 확인할 수 있습니다.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Azure 리소스 생성 알림":::
    
1.  완료되면 함수 앱 이름을 기반으로 하는 이름을 사용하여 구독에 다음 Azure 리소스가 생성됩니다.
    
    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

    함수 앱을 만들고 배포 패키지가 적용되면 알림이 표시됩니다. 

    [!INCLUDE [functions-vs-code-create-tip](../../includes/functions-vs-code-create-tip.md)]

4. 이 알림에서 **출력 보기** 를 선택하여 사용자가 만든 Azure 리소스를 포함한 만들기 및 배포 결과를 표시합니다. 알림이 누락된 경우 오른쪽 아래 모서리에 있는 종 모양 아이콘을 선택하여 다시 확인합니다.

    ![전체 알림 만들기](../../includes/media/functions-publish-project-vscode/function-create-notifications.png)

::: zone-end

::: zone pivot="development-environment-cli"  
[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]  
::: zone-end  

::: zone pivot="development-environment-vscode"  
[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]  
::: zone-end  

## <a name="clean-up-resources"></a>리소스 정리

이 문서를 완료하는 리소스를 만들었습니다. [계정 상태](https://azure.microsoft.com/account/) 및 [서비스 가격 책정](https://azure.microsoft.com/pricing/)에 따라 리소스에 대해 요금이 청구될 수 있습니다. 

::: zone pivot="development-environment-cli"  
추가 비용이 발생하지 않도록 다음 명령을 사용하여 리소스 그룹과 그룹에 포함된 모든 리소스를 삭제합니다.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```
::: zone-end  

::: zone pivot="development-environment-vscode"  
추가 비용이 발생하지 않도록 다음 단계를 수행하여 함수 앱 및 관련 리소스를 삭제합니다.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]  
::: zone-end  
::: zone pivot="development-environment-vs"   
[!INCLUDE [functions-vstools-cleanup](../../includes/functions-vstools-cleanup.md)]
::: zone-end

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [.NET 격리 함수에 대해 자세히 알아보기](dotnet-isolated-process-guide.md)

