---
title: Azure CLI를 사용하여 첫 번째 함수 만들기
description: Azure CLI 및 Azure Functions Core Tools를 사용하여 서버를 사용하지 않는 실행을 위해 첫 번째 Azure Function을 만드는 방법을 알아봅니다.
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 11/13/2018
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 222e4a98974a1af40ff860cfc4fdb246d9c97bca
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769390"
---
# <a name="quickstart-create-your-first-function-from-the-command-line-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 명령줄에서 첫 번째 함수 만들기

이 빠른 시작 토픽에서는 명령줄 또는 터미널에서 첫 번째 함수를 만드는 방법을 설명합니다. Azure CLI를 사용하여 함수를 호스트하고 [서버를 사용하지 않는](https://azure.microsoft.com/solutions/serverless/) 인프라인 함수 앱을 만듭니다. 함수 코드 프로젝트는 Azure에 함수 앱 프로젝트를 배포하는 데도 사용되는 [Azure Functions Core Tools](functions-run-local.md)를 사용하여 템플릿에서 생성됩니다.

Mac, Windows 또는 Linux 컴퓨터를 사용하여 아래 단계를 따르면 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

이 샘플을 실행하기 전에 다음 사항이 준비되어야 합니다.

+ [Azure Functions Core Tools](./functions-run-local.md#v2) 버전 2.6.666 이상을 설치합니다.

+ [Azure CLI](/cli/azure/install-azure-cli)를 설치합니다. 이 문서에는 Azure CLI 버전 2.0 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. [Azure Cloud Shell](https://shell.azure.com/bash)을 사용할 수도 있습니다.

+ 활성화된 Azure 구독.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-create-function-app-cli](../../includes/functions-create-function-app-cli.md)]

## <a name="enable-extension-bundles"></a>확장 번들 사용

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>함수 앱 만들기

함수 실행을 호스트하는 함수 앱이 있어야 합니다. 함수 앱은 서버를 사용하지 않는 함수 코드 실행을 위한 환경을 제공합니다. 이를 통해 함수를 논리 단위로 그룹화하여 더욱 쉽게 리소스를 관리, 배포, 크기 조정 및 공유할 수 있습니다. [az functionapp create](/cli/azure/functionapp#az-functionapp-create) 명령을 사용하여 함수 앱을 만듭니다.

다음 명령에서 `<APP_NAME>` 자리 표시자 및 `<STORAGE_NAME>`의 스토리지 계정 이름을 고유한 함수 앱 이름으로 바꿉니다. `<APP_NAME>`은 함수 앱의 기본 DNS 도메인으로 사용되므로 이름이 Azure의 모든 앱에서 고유해야 합니다. 또한 `dotnet`(C#) 또는 `node`(JavaScript)에서 함수 앱에 대한 `<language>` 런타임을 설정해야 합니다.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westeurope \
--name <APP_NAME> --storage-account  <STORAGE_NAME> --runtime <language>
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

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]

