---
title: Azure Functions를 Azure SignalR Service와 통합하기 위한 자습서 | Microsoft Docs
description: 이 자습서에서는 Azure SignalR Service에서 Azure Functions를 사용하는 방법을 알아봅니다.
services: signalr
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/24/2018
ms.author: wesmc
ms.openlocfilehash: b1bb6b3fe545d5a42fa985ab85bd7a914128e56b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-integrate-azure-functions-with-azure-signalr-service"></a>자습서: Azure Functions를 Azure SignalR Service와 통합

이 자습서는 이전 자습서에서 만든 대화방 응용 프로그램에서 진행됩니다. [SignalR Service로 대화방 만들기](signalr-quickstart-dotnet-core.md) 및 [Azure SignalR Service 인증](./signalr-authenticate-oauth.md)을 완료하지 않은 경우 먼저 해당 연습을 완료합니다. 

실시간 응용 프로그램을 사용한 일반적인 시나리오에서는 콘텐츠 업데이트가 서버에서 시작되고 웹 클라이언트에 게시됩니다. [Azure Functions](../azure-functions/functions-overview.md)는 이러한 콘텐츠 업데이트를 생성하기에 적합한 도구입니다. Azure Functions를 사용할 때의 주요 이점은 전체 응용 프로그램의 아키텍처나 실행 인프라를 신경쓰지 않고 요청 시에 코드를 실행할 수 있다는 것입니다. 또한 코드가 실제로 실행되는 시간에 대해서만 비용을 지불합니다.  

일반적으로 이 시나리오에서 SignalR은 콘텐츠 업데이트를 푸시하기 위해 클라이언트와 서버 간에 연결을 유지하려고 하기 때문에 SignalR을 사용하려고 하면 문제가 발생합니다. 코드는 요청 시에만 실행되므로 연결을 유지 관리할 수 없습니다. 그러나 Azure SignalR Service는 런타임에 사용자를 위해 연결을 관리하므로 이 시나리오를 지원할 수 있습니다.

이 자습서에서는 Azure Functions를 사용하여 분 간격이 시작될 때 타이머 함수를 통해 메시지를 생성할 것입니다. 이 함수는 이전 자습서에서 만든 대화방의 모든 클라이언트에 메시지를 게시합니다. 타이머 함수에 대한 자세한 내용은 [타이머 함수](../azure-functions/functions-create-scheduled-function.md)를 참조하세요.

이 빠른 시작의 단계를 완료하려면 아무 코드 편집기나 사용할 수 있습니다. 그러나 [Visual Studio Code](https://code.visualstudio.com/)는 Windows, macOS 및 Linux 플랫폼에서 사용할 수 있는 뛰어난 옵션입니다.

이 자습서에 대한 코드는 [AzureSignalR-samples GitHub 리포지토리](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Timer)에서 다운로드할 수 있습니다.

![서버 메시지를 포함하는 채팅 앱](./media/signalr-integrate-functions/signalr-functions-complete.png)

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure CLI를 사용하여 Azure Functions에서 새 타이머 함수를 만듭니다.
> * 로컬 Git 리포지토리 배포를 위해 타이머 함수를 구성합니다.
> * 타이머를 SignalR Service에 연결하여 1분마다 업데이트 푸시

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음 필수 구성 요소가 있어야 합니다.

* [Git](https://git-scm.com/)
* [.NET Core SDK](https://www.microsoft.com/net/download/windows) 
* [Azure Cloud Shell이 구성됨](https://docs.microsoft.com/azure/cloud-shell/quickstart)
* [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) github 리포지토리를 다운로드하거나 복제합니다.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-function-app"></a>함수 앱 만들기

함수에 대한 실행 환경을 정의하는 함수 앱을 만들어야 합니다. 또한 함수 앱을 통해 여러 함수를 논리 단위로 그룹화하여 더욱 쉽게 관리 및 배포하고 리소스를 공유할 수 있습니다. 자세한 내용은 [Azure CLI를 사용하여 첫 번째 함수 만들기](../azure-functions/functions-create-first-azure-function-azure-cli.md)를 참조하세요.

이 섹션에서는 Azure Cloud Shell을 사용하여 로컬 Git 리포지토리에서 배포용으로 구성된 새 Azure 함수 앱을 만듭니다. 

이전 자습서에서 사용한 것과 동일한 리소스 그룹에 함수 앱 리소스를 만듭니다. 이렇게 하면 모든 자습서 리소스를 보다 쉽게 관리할 수 있습니다.

아래 스크립트를 텍스트 편집기에 복사하고, 변수 매개 변수 값을 리소스에 대한 값으로 바꿉니다. 업데이트된 스크립트를 복사한 후 Azure Cloud Shell에 붙여넣고 **Enter** 키를 눌러 저장소 계정과 함수 앱을 만듭니다.

```azurecli-interactive
#====================================================================
#=== Update these variables to match your values from previous    === 
#=== tutorials.                                                   ===
#====================================================================
ResourceGroupName=SignalRTestResources
location=eastus

#====================================================================
#=== Update these variables for the new function app and storage  ===
#=== account.                                                     ===
#====================================================================
functionappName=mysignalfunctionapp
storageAccountName=mystorageaccount

# Create a storage account to hold function app code and settings
az storage account create --resource-group $ResourceGroupName \
--name $storageAccountName \
--location $location --sku Standard_LRS

# Create the function app
az functionapp create --resource-group $ResourceGroupName \
--name $functionappName \
--consumption-plan-location $location \
--storage-account $storageAccountName

```

| 매개 변수 | 설명 |
| -------------------- | --------------- |
| ResourceGroupName | 이 리소스 그룹 이름은 이전 자습서에서 제안된 것입니다. 모든 자습서 리소스를 그룹화된 상태로 유지하는 것이 좋습니다. 이전 자습서에서 사용한 것과 동일한 리소스 그룹을 사용합니다. | 
| location | 이전 자습서에서 리소스 그룹을 만드는 데 사용한 것과 동일한 위치로 이 변수를 업데이트합니다. | 
| functionappName | 새 함수 앱에 대한 고유 이름으로 이 변수를 업데이트합니다. 예를 들어, signalrfunctionapp22665120을 사용합니다. | 
| storageAccountName | 함수 앱 코드와 설정을 유지할 새 저장소 계정의 이름을 입력합니다. | 



## <a name="configure-the-function-app"></a>함수 앱 구성

이 섹션에서는 Azure SignalR Service 리소스에 대한 연결 문자열을 포함하는 앱 설정을 사용하여 함수 앱을 구성합니다. 함수 코드는 이 설정을 사용하여 대화방에 연결하고 메시지를 게시합니다. 또한 로컬 Git 리포지토리에서 배포용 함수 앱을 구성합니다.

아래의 스크립트를 복사하고 매개 변수 값을 대체합니다. 업데이트된 스크립트를 Azure Cloud Shell에 붙여 넣고 **Enter** 키를 누릅니다.

```azurecli-interactive
#====================================================================
#=== Update these variables to match your resources.              === 
#====================================================================
ResourceGroupName=SignalRTestResources
functionappName=mysignalfunctionapp

#========================================================================
#=== Replace this value with the connection string for your           ===
#=== SignalR Service resource.                                        ===
#========================================================================
connstring="Endpoint=<service_endpoint>;AccessKey=<access_key>;"

# Add the SignalR Service connection string app setting
az functionapp config appsettings set --resource-group $ResourceGroupName \
    --name $functionappName \
    --setting "Azure:SignalR:ConnectionString=$connstring"

# configure for deployment from a local Git repository
az functionapp deployment source config-local-git --name $functionappName \
    --resource-group $ResourceGroupName

```

| 매개 변수 | 설명 |
| -------------------- | --------------- |
| ResourceGroupName | 이 리소스 그룹 이름은 이전 자습서에서 제안된 것입니다. 모든 자습서 리소스를 그룹화된 상태로 유지하는 것이 좋습니다. 이전 자습서에서 사용한 것과 동일한 리소스 그룹을 사용합니다. | 
| functionappName | 새 함수 앱에 대한 고유 이름으로 이 변수를 업데이트합니다. 예를 들어, signalrfunctionapp22665120을 사용합니다. | 
| connstring | SignalR Service 리소스에 대한 연결 문자열을 입력합니다. **설정** 아래에서 **키**를 클릭하여 Azure Portal의 SignalR Service 리소스 페이지에서 이 연결 문자열을 검색할 수 있습니다. | 



마지막 명령에서 반환된 Git 배포 URL을 적어둡니다. 함수 코드를 배포할 때 이 URL을 사용합니다.


## <a name="the-timer-function"></a>타이머 함수

타이머 함수 예제는 다운로드의 */samples/Timer* 디렉터리에 있거나 [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Timer) github 리포지토리의 복제본입니다. 타이머 함수 코드는 *TimerFunction.cs*에 있습니다. 이 코드에서는 기본 구성 키(*Azure:SignalR:ConnectionString*)로 저장된 연결 문자열을 사용하여 허브에 대한 프록시를 만듭니다. 함수 코드가 서버 쪽에서 실행되므로 일반 클라이언트로 인증하도록 요구할 이유가 없습니다. 이 코드는 연결 문자열을 사용하도록 신뢰됩니다. 이 허브 프록시를 사용하여, 함수 코드는 허브에 대해 정의한 모든 메서드를 호출할 수 있습니다. 이 코드는 `BroadcastMessage` 메서드를 호출하여 트리거가 발생할 때의 시간을 포함하는 메시지를 게시합니다.

함수 코드에 대한 트리거는 *TimerFunction/function.json*의 바인딩에 정의된 *timerTrigger*입니다. 여기에는 분 간격이 시작할 때마다 타이머 트리거가 실행되도록 설정하기 위한 [CRON 식](https://wikipedia.org/wiki/Cron#CRON_expression)이 포함됩니다.

```json
{
  "bindings": [
    {
      "type": "timerTrigger",
      "schedule": "0 * * * * *",
      "useMonitor": true,
      "runOnStartup": false,
      "name": "myTimer"
    }
  ],
  "disabled": false,
  "scriptFile": "../Timer.dll",
  "entryPoint": "Timer.TimerFunction.Run"
}
```


## <a name="building-the-timer-function"></a>타이머 함수 빌드

다음 단계의 [.NET Core CLI(명령줄 인터페이스)](https://docs.microsoft.com/dotnet/core/tools/)를 사용하여 함수를 빌드하고 배포를 준비합니다.

1. 다운로드의 */samples/Timer* 하위 디렉터리로 이동하거나 [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) github 리포지토리의 복제본으로 이동합니다.

2. 다음 명령을 사용하여 NuGet 패키지를 복원합니다.

        dotnet restore

3. 다음 명령을 사용하여 *Timer* 함수 앱을 빌드합니다.

        msbuild /p:Configuration=Release


## <a name="create-and-deploy-the-local-git-repo"></a>로컬 Git 리포지토리 만들기 및 배포

1. Git 셸에서 빌드 하위 디렉터리 */samples/Timer/bin/Release/net461*로 이동합니다.

        cd ./AzureSignalR-samples/samples/Timer/bin/Release/net461

2. 다음 명령을 사용하여 해당 디렉터리를 새 Git 리포지토리로 초기화합니다.

        git init

3. 빌드 디렉터리의 모든 파일에 대해 새 커밋을 추가합니다.

        git add -A
        git commit -v -a -m "Initial Timer function commit"        

4. 함수 앱 구성 동안 적어 놓은 Git 배포 URL의 원격 끝점을 추가합니다.

        git remote add Azure <enter your Git deployment URL>

5. 함수 앱 배포

        git push Azure master

   코드가 배포되면 타이머는 분 간격으로 바로 실행되면서 코드를 실행합니다.

## <a name="test-the-chat-app"></a>채팅 앱 테스트

채팅 응용 프로그램으로 이동합니다. 그러면 방금 만든 타이머 함수가 분 간격으로 시간을 보고합니다.

![서버 메시지를 포함하는 채팅 앱](./media/signalr-integrate-functions/signalr-functions-complete.png)



## <a name="clean-up-resources"></a>리소스 정리

응용 프로그램을 계속 테스트하려는 경우 만든 리소스를 유지할 수 있습니다.

그렇지 않고, 샘플 응용 프로그램 사용이 끝나면 Azure 리소스를 삭제하여 요금이 청구되는 것을 방지할 수 있습니다. 

> [!IMPORTANT]
> 리소스 그룹 삭제는 취소할 수 없으며 해당 리소스 그룹 및 해당 그룹 안에 있는 모든 리소스는 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다. 유지하려는 리소스가 포함된 기존 리소스 그룹 내에 이 샘플을 호스트하기 위한 리소스를 만든 경우 리소스 그룹을 삭제하는 대신, 해당 블레이드에서 각 리소스를 개별적으로 삭제할 수 있습니다.
> 
> 

[Azure 포털](https://portal.azure.com) 에 로그인하고 **리소스 그룹**을 클릭합니다.

**이름을 기준으로 필터링...** 텍스트 상자에 리소스 그룹의 이름을 입력합니다. 이 문서의 지침에서는 *SignalRTestResources*라는 리소스 그룹을 사용합니다. 결과 목록의 리소스 그룹에서 **...** 를 클릭한 후 **리소스 그룹 삭제**를 클릭합니다.

   
![삭제](./media/signalr-integrate-functions/signalr-delete-resource-group.png)


리소스 그룹을 삭제할지 확인하는 메시지가 표시됩니다. 리소스 그룹의 이름을 입력하여 확인한 후 **삭제**를 클릭합니다.
   
잠시 후 리소스 그룹 및 해당 그룹에 포함된 모든 리소스가 삭제됩니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure 함수와 통합하여 Azure 함수 트리거를 기준으로 클라이언트에 업데이트를 푸시하는 방법을 배웠습니다. Azure SignalR Server에 대해 자세히 알아보려면 SignalR Service용 Azure CLI 샘플을 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure SignalR CLI 샘플](./signalr-cli-samples.md)



