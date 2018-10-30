---
title: Azure CLI를 사용하여 첫 번째 함수 만들기
description: Azure CLI 및 Azure Functions Core Tools를 사용하여 서버를 사용하지 않는 실행을 위해 첫 번째 Azure Function을 만드는 방법을 알아봅니다.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 09/10/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: 07a079e00963f1f5aff96369649e2e4fb248aae0
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986001"
---
# <a name="create-your-first-function-from-the-command-line"></a>명령줄에서 첫 번째 함수 만들기

이 빠른 시작 토픽에서는 명령줄 또는 터미널에서 첫 번째 함수를 만드는 방법을 설명합니다. Azure CLI를 사용하여 함수를 호스트하고 [서버를 사용하지 않는](https://azure.microsoft.com/solutions/serverless/) 인프라인 함수 앱을 만듭니다. 함수 코드 프로젝트는 Azure에 함수 앱 프로젝트를 배포하는 데도 사용되는 [Azure Functions Core Tools](functions-run-local.md)를 사용하여 템플릿에서 생성됩니다.

Mac, Windows 또는 Linux 컴퓨터를 사용하여 아래 단계를 따르면 됩니다.

## <a name="prerequisites"></a>필수 조건

이 샘플을 실행하기 전에 다음이 있어야 합니다.

+ [Azure Core Tools 버전 2.x](functions-run-local.md#v2)를 설치합니다.

+ [Azure CLI]( /cli/azure/install-azure-cli)를 설치합니다. 이 문서에서 설명하는 단계를 수행하려면 Azure CLI 버전 2.0 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. [Azure Cloud Shell](https://shell.azure.com/bash)을 사용할 수도 있습니다.

+ 활성 Azure 구독.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>로컬 함수 앱 프로젝트 만들기

명령줄에서 다음 명령을 실행하여 현재 로컬 디렉터리의 `MyFunctionProj` 폴더에 함수 앱 프로젝트를 만듭니다. GitHub 리포지토리도 `MyFunctionProj`에서 생성됩니다.

```bash
func init MyFunctionProj
```

메시지가 표시되면 다음 언어 선택에서 작업자 런타임을 선택합니다.

+ `dotnet`: .NET 클래스 라이브러리 프로젝트(.csproj)를 만듭니다.
+ `node`: JavaScript 프로젝트를 만듭니다.

명령을 실행하는 경우 다음 출력과 같이 표시됩니다.

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Initialized empty Git repository in C:/functions/MyFunctionProj/.git/
```

다음 명령을 사용하여 새 `MyFunctionProj` 프로젝트 폴더로 이동합니다.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-update-function-code](../../includes/functions-update-function-code.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>함수 앱 만들기

함수 실행을 호스트하는 함수 앱이 있어야 합니다. 함수 앱은 서버를 사용하지 않는 함수 코드 실행을 위한 환경을 제공합니다. 이를 통해 함수를 논리 단위로 그룹화하여 더욱 쉽게 리소스를 관리, 배포 및 공유할 수 있습니다. [az functionapp create](/cli/azure/functionapp#az-functionapp-create) 명령을 사용하여 함수 앱을 만듭니다. 

다음 명령에서 `<app_name>` 자리 표시자 및 `<storage_name>`의 저장소 계정 이름을 고유한 함수 앱 이름으로 바꿉니다. `<app_name>`은 함수 앱의 기본 DNS 도메인으로 사용되므로 이름이 Azure의 모든 앱에서 고유해야 합니다. _deployment-source-url_ 매개 변수는 "Hello World" HTTP 트리거 함수를 포함하는 GitHub의 샘플 리포지토리입니다.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westeurope \
--name <app_name> --storage-account  <storage_name>  
```

_consumption-plan-location_ 매개 변수를 설정하는 것은 해당 함수 앱이 소비 호스팅 계획에서 호스팅된다는 뜻입니다. 이 서버리스 계획에서는 리소스가 필요에 따라 함수를 통해 동적으로 추가되며 함수가 실행된 것에 대해서만 지불합니다. 자세한 내용은 [올바른 호스팅 계획 선택](functions-scale.md)을 참조하세요.

함수 앱을 만들었으면 Azure CLI는 다음 예와 비슷한 정보를 표시합니다.

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

## <a name="configure-the-function-app"></a>함수 앱 구성

Core Tools 버전 2.x는 템플릿을 사용하여 Azure Functions 2.x 런타임에 대한 프로젝트를 만듭니다. 이로 인해 버전 2.x 런타임이 Azure에서 사용되는지 확인해야 합니다. `FUNCTIONS_WORKER_RUNTIME` 응용 프로그램 설정을 `~2`로 설정하면 함수 앱이 최신 2.x 버전에 고정됩니다. [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set) 명령을 사용하여 응용 프로그램 설정을 설정합니다.

다음 Azure CLI 명령에서 `<app_name>은 함수 앱의 이름입니다.

```azurecli-interactive
az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings FUNCTIONS_WORKER_RUNTIME=~2
```

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]
