---
title: Azure에서 첫 번째 Linux 기반 함수 만들기
description: Azure에서 Azure Functions Core Tools 및 Azure CLI를 사용하여 Linux에 호스트되는 첫 번째 함수를 만드는 방법을 알아봅니다.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 11/28/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: javascript
manager: jeconnoc
ms.openlocfilehash: b6df653f89f05a9b253ecea102ed8310ff2a53b7
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53438286"
---
# <a name="create-your-first-function-hosted-on-linux-using-core-tools-and-the-azure-cli-preview"></a>Core Tools 및 Azure CLI(미리 보기)를 사용하여 Linux에 호스트되는 첫 번째 함수 만들기

Azure Functions를 사용하면 먼저 VM을 만들거나 웹 응용 프로그램을 게시하지 않고도 [서버리스](https://azure.com/serverless) Linux 환경에서 코드를 실행할 수 있습니다. Linux 호스팅은 현재 미리 보기이므로 [Functions 2.0 런타임](functions-versions.md)이 필요합니다. Linux에서 함수 앱을 실행하기 위한 미리 보기 고려 사항에 대해 자세히 알아보려면 [이 Linux의 함수 문서](https://aka.ms/funclinux)를 참조하세요.

이 빠른 시작 문서에서는 Azure CLI를 사용하여 Linux에서 실행되는 첫 번째 함수 앱을 만드는 방법을 안내합니다. [Azure Functions Core Tools](functions-run-local.md)를 사용하여 함수 코드를 로컬로 만든 후 Azure에 배포합니다.

다음 단계는 Mac, Windows 또는 Linux 컴퓨터에서 지원됩니다. 이 문서에서는 JavaScript 또는 C#으로 함수를 만드는 방법을 보여줍니다. Python 함수를 만드는 방법을 알아보려면 [Core 도구 및 Azure CLI를 사용하여 첫 번째 Python 함수 만들기(미리 보기)](functions-create-first-function-python.md)를 참조하세요.

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

메시지가 표시되면 화살표 키를 사용하여 다음 언어 선택에서 작업자 런타임을 선택합니다.

+ `dotnet`: .NET 클래스 라이브러리 프로젝트(.csproj)를 만듭니다.
+ `node`: JavaScript 프로젝트를 만듭니다.
+ `python`: Python 프로젝트를 만듭니다. Python 함수의 경우 [Python 빠른 시작](functions-create-first-function-python.md)을 참조하세요.

명령을 실행하는 경우 다음 출력과 같이 표시됩니다.

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Initialized empty Git repository in C:/functions/MyFunctionProj/.git/
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-update-function-code](../../includes/functions-update-function-code.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Linux App Service 계획 만들기

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Azure에서 Linux 함수 앱 만들기

Linux에서 함수 실행을 호스트하는 함수 앱이 있어야 합니다. 함수 앱은 함수 코드를 실행하기 위한 서버리스 환경을 제공합니다. 이를 통해 함수를 논리 단위로 그룹화하여 더욱 쉽게 리소스를 관리, 배포 및 공유할 수 있습니다. [az functionapp create](/cli/azure/functionapp#az-functionapp-create) 명령을 사용하여 Linux에서 실행되는 함수 앱을 만듭니다.

다음 명령에서 `<app_name>` 자리 표시자 및 `<storage_name>`의 저장소 계정 이름에 고유한 함수 앱 이름을 사용합니다. `<app_name>`은 함수 앱의 기본 DNS 도메인이기도 합니다. 이 이름은 Azure의 모든 앱에서 고유해야 합니다. 또한 `dotnet`(C#), `node`(JavaScript) 또는 `python`에서 함수 앱에 대한 `<language>` 런타임을 설정해야 합니다.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westus --os-type Linux \
--name <app_name> --storage-account  <storage_name> --runtime <language>
```

> [!NOTE]
> Linux가 아닌 App Service 앱이 포함된 `myResourceGroup`이라는 기존 리소스 그룹이 있는 경우 다른 리소스 그룹을 사용해야 합니다. Windows 앱과 Linux 앱을 동일한 리소스 그룹에 호스팅할 수 없습니다.  

함수 앱이 만들어지면 다음 메시지가 표시됩니다.

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

이제 Azure에서 새 함수 앱에 프로젝트를 게시할 수 있습니다.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>다음 단계

이 문서에서는 기본 Azure App Service 컨테이너에 함수 앱을 호스트하는 방법을 보여주었습니다. 사용자 지정 컨테이너에서 Linux에 함수를 호스팅할 수도 있습니다.

> [!div class="nextstepaction"]
> [사용자 지정 이미지를 사용하여 Linux에서 함수 만들기](functions-create-function-linux-custom-image.md)
