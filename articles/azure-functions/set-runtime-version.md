---
title: Azure Functions 런타임 버전을 대상으로 지정하는 방법
description: Azure Functions는 여러 버전의 런타임을 지원합니다. Azure에서 호스팅된 함수 앱의 런타임 버전을 지정하는 방법을 알아봅니다.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: glenga
ms.openlocfilehash: 6e8142e391dd02e78be42e1f16ae2626b74c41c3
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55734473"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Azure Functions 런타임 버전을 대상으로 지정하는 방법

함수 앱은 특정 Azure Functions 런타임 버전에서 실행됩니다. 두 가지 주 버전이 있습니다. [1.x 및 2.x](functions-versions.md). 기본적으로 작성되는 함수 앱은 런타임의 버전 2.x입니다. 이 문서에는 선택한 버전에서 실행할 함수 앱을 Azure에서 구성하는 방법을 설명합니다. 특정 버전에 대한 로컬 개발 환경을 구성하는 방법에 대한 자세한 내용은 [로컬로 Azure Functions 코딩 및 테스트](functions-run-local.md)를 참조하세요.

> [!NOTE]
> 함수가 하나 이상 포함된 함수 앱의 런타임 버전은 변경할 수 없습니다. 런타임 버전을 변경하려면 Azure Portal을 사용해야 합니다.

## <a name="automatic-and-manual-version-updates"></a>자동 및 수동 버전 업데이트

Functions를 통해 함수 앱에서 `FUNCTIONS_EXTENSION_VERSION` 애플리케이션 설정을 사용하여 특정 버전의 런타임을 대상으로 지정할 수 있습니다. 함수 앱을 새 버전으로 변경하도록 명시적으로 선택할 때까지 지정된 주 버전에서 유지합니다.

주 버전(2.x의 경우 "~2", 1.x의 경우 "~1")만 지정하면 새로운 런타임 부 버전을 사용할 수 있을 때 함수 앱이 해당 런타임으로 자동 업데이트됩니다. 새로운 부 버전에는 주요 변경 내용이 도입되지 않습니다. 부 버전(예: "2.0.12345")을 지정하면 버전을 명시적으로 변경할 때까지 함수 앱은 특정 버전으로 고정됩니다.

새 버전을 공개적으로 사용할 수 있으면 포털에서 메시지를 표시하여 해당 버전으로 업그레이드할 수 있는 기회를 제공합니다. 새 버전으로 변경한 후에 `FUNCTIONS_EXTENSION_VERSION` 애플리케이션 설정을 사용하여 이전 버전으로 다시 변경할 수 있습니다.

런타임 버전을 변경하면 함수 앱을 다시 시작하게 됩니다.

자동 업데이트를 사용하기 위해 `FUNCTIONS_EXTENSION_VERSION` 앱 설정에서 설정할 수 있는 값은 현재 1.x 런타임의 경우 "~1"이고 2.x의 경우 "~2"입니다.

## <a name="view-and-update-the-current-runtime-version"></a>현재 런타임 버전 확인 및 업데이트

함수 앱에 사용되는 런타임 버전을 변경할 수 있습니다. 주요 변경 내용이 발생할 가능성이 있으므로 함수 앱에서 함수를 만들기 전에 런타임 버전만 변경해야 합니다. 런타임 버전은 `FUNCTIONS_EXTENSION_VERSION` 설정에서 결정되지만, 설정을 직접 변경하지 않고 Azure Portal에서 이 변경을 수행해야 합니다. 그 이유는 포털에서 변경 내용이 확인되고 필요에 따라 관련된 다른 변경 내용이 적용되기 때문입니다.

### <a name="from-the-azure-portal"></a>Azure Portal에서

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

### <a name="view-and-update-the-runtime-version-using-azure-cli"></a>Azure CLI에서

Azure CLI에서 `FUNCTIONS_EXTENSION_VERSION`을 확인하고 설정할 수도 있습니다.

>[!NOTE]
>다른 설정이 런타임 버전의 영향을 받을 수 있으므로 포털에서 버전을 변경해야 합니다. 런타임 버전을 변경할 때 포털에서는 Node.js 버전과 런타임 스택 등의 기타 필요한 업데이트를 자동으로 수행합니다.  

Azure CLI를 사용하여 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) 명령을 실행해 현재 런타임 버전을 확인합니다.

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

이 코드에서 `<function_app>`을 함수 앱 이름으로 바꿉니다. 또한 `<my_resource_group>`을 함수 앱의 리소스 그룹 이름으로 바꿉니다. 

다음 출력의 `FUNCTIONS_EXTENSION_VERSION`은 보기 편하도록 잘린 상태입니다.

```output
[
  {
    "name": "FUNCTIONS_EXTENSION_VERSION",
    "slotSetting": false,
    "value": "~2"
  },
  {
    "name": "FUNCTIONS_WORKER_RUNTIME",
    "slotSetting": false,
    "value": "dotnet"
  },
  
  ...
  
  {
    "name": "WEBSITE_NODE_DEFAULT_VERSION",
    "slotSetting": false,
    "value": "8.11.1"
  }
]
```

함수 앱에서 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) 명령으로 함수 앱의 `FUNCTIONS_EXTENSION_VERSION` 설정을 업데이트할 수 있습니다.

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```

`<function_app>`은 함수 앱 이름으로 바꿉니다. 또한 `<my_resource_group>`을 함수 앱의 리소스 그룹 이름으로 바꿉니다. 그리고 `<version>`은 1.x 런타임의 유효한 버전 또는 2.x 버전의 경우 `~2`로 바꿉니다.

앞의 코드 샘플에서 **사용해 보세요.** 를 선택하여 [Azure Cloud Shell](../cloud-shell/overview.md)에서 이 명령을 실행할 수 있습니다. 또한 [Azure CLI locally(로컬로 Azure CLI 설치)](/cli/azure/install-azure-cli)를 사용하면 [az login](/cli/azure/reference-index#az-login)을 실행하여 로그인한 후 이 명령을 실행할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [로컬 개발 환경에서 2.0 런타임을 대상으로 지정](functions-run-local.md)

> [!div class="nextstepaction"]
> [런타임 버전에 대한 릴리스 정보 참조](https://github.com/Azure/azure-webjobs-sdk-script/releases)
