---
title: "Azure Functions 런타임 버전을 대상으로 지정하는 방법"
description: "Azure Functions는 여러 버전의 런타임을 지원합니다. Azure에서 호스팅하는 함수 앱의 런타임 버전을 지정하는 방법을 알아봅니다."
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2017
ms.author: glenga
ms.openlocfilehash: 063232e40b30d03b0ee8b087a602fed0fee3be0a
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2017
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Azure Functions 런타임 버전을 대상으로 지정하는 방법

함수 앱은 특정 Azure Functions 런타임 버전에서 실행됩니다. 1.x와 2.x의 두 가지 주요 버전이 있습니다. 이 문서에는 사용할 주요 버전을 선택하는 방법 및 선택한 버전에서 실행할 함수 앱을 Azure에서 구성하는 방법을 설명합니다. 특정 버전에 대한 로컬 개발 환경을 구성하는 방법에 대한 자세한 내용은 [로컬로 Azure Functions 코딩 및 테스트](functions-run-local.md)를 참조하세요.

## <a name="differences-between-runtime-1x-and-2x"></a>런타임 1.x 및 2.x 간의 차이점

> [!IMPORTANT] 
> 런타임 1.x는 프로덕션용으로 승인된 유일한 버전입니다.

| 런타임 | 가동 상태 |
|---------|---------|
|1.x|일반 공급(GA)|
|2.x|미리 보기|

다음 섹션에서는 언어, 바인딩 및 플랫폼 간 개발 지원의 차이점을 설명합니다.

### <a name="languages"></a>언어

다음 표는 각 런타임 버전에서 지원되는 프로그래밍 언어를 나타냅니다.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

자세한 내용은 [지원되는 언어](supported-languages.md)를 참조하세요.

### <a name="bindings"></a>바인딩 

런타임 1.x에서 지원하는 실험적 바인딩은 지원 2.x에서 사용할 수 없습니다. 바인딩 지원 및 2.x의 다른 기능 차이에 대한 자세한 내용은 [런타임 2.0의 알려진 문제점](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues)을 참조하세요.

런타임 2.x를 사용하면 사용자 지정 [바인딩 확장](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)을 만들 수 있습니다. 이 확장성 모델을 사용하는 기본 제공 바인딩은 2.x에서만 사용할 수 있으며 그 중 첫 번째는 [Microsoft Graph 바인딩](functions-bindings-microsoft-graph.md)입니다.

### <a name="cross-platform-development"></a>교차 플랫폼 개발

런타임 1.x는 포털 또는 Windows에서만 함수 개발을 지원하며 2.x를 사용하면 Linux 또는 macOS에서 Azure Functions를 개발 및 실행할 수 있습니다.

## <a name="automatic-and-manual-version-updates"></a>자동 및 수동 버전 업데이트

Functions를 통해 함수 앱에서 `FUNCTIONS_EXTENSION_VERSION` 응용 프로그램 설정을 사용하여 특정 버전의 런타임을 대상으로 지정할 수 있습니다. 함수 앱을 새 버전으로 변경하도록 명시적으로 선택할 때까지 지정된 주 버전에서 유지합니다.

주 버전(1.x의 경우 "~1" 또는 2.x의 경우 "beta")만 지정하면 새로운 런타임 부 버전을 사용할 수 있을 때 함수 앱이 해당 런타임으로 자동 업데이트됩니다. 새로운 부 버전에는 주요 변경 내용이 도입되지 않습니다. 부 버전(예: "1.0.11360")을 지정하면 사용자가 명시적으로 변경할 때까지 함수 앱이 해당 버전을 유지합니다. 

새 버전을 공개적으로 사용할 수 있으면 포털에서 메시지를 표시하여 해당 버전으로 업그레이드할 수 있는 기회를 제공합니다. 새 버전으로 변경한 후에 `FUNCTIONS_EXTENSION_VERSION` 응용 프로그램 설정을 사용하여 이전 버전으로 다시 변경할 수 있습니다.

런타임 버전을 변경하면 함수 앱을 다시 시작하게 됩니다.

자동 업데이트를 사용하기 위해 `FUNCTIONS_EXTENSION_VERSION` 앱 설정에서 설정할 수 있는 값은 현재 1.x 런타임의 경우 "~ 1"이고 2.x의 경우 "beta"입니다.

## <a name="view-the-current-runtime-version"></a>현재 런타임 버전 보기

현재 함수 앱에서 사용하는 런타임 버전을 보려면 다음 절차를 따르세요. 

1. [Azure Portal](https://portal.azure.com)에서 함수 앱으로 이동하고 **구성된 기능** 아래에서 **함수 앱 설정**을 선택합니다. 

    ![함수 앱 설정을 선택합니다.](./media/functions-versions/add-update-app-setting.png)

2. **함수 앱 설정** 탭에서 **런타임 버전**을 찾습니다. 특정 런타임 버전 및 요청된 주 버전을 적어둡니다. 다음 예제에는 FUNCTIONS\_EXTENSION\_VERSION 앱 설정이 `~1`로 설정되어 있습니다.
 
   ![함수 앱 설정을 선택합니다.](./media/functions-versions/function-app-view-version.png)

## <a name="target-the-version-20-runtime"></a>버전 2.0 런타임을 대상으로 지정

>[!IMPORTANT]   
> Azure Functions 런타임 2.0은 미리 보기 상태이며 현재 Azure Functions의 일부 기능은 지원되지 않습니다. 자세한 내용은 이 문서 앞 부분의 [런타임 1.x와 2.x 간의 차이점](#differences-between-runtime-1x-and-2x)을 참조하세요.

Azure Portal에서 함수 앱을 런타임 버전 2.0 미리 보기로 변경할 수 있습니다. **함수 앱 설정** 탭의 **런타임 버전** 아래에서 **베타**를 찾습니다.  

![함수 앱 설정을 선택합니다.](./media/functions-versions/function-app-view-version.png)

이 설정은 `FUNCTIONS_EXTENSION_VERSION` 응용 프로그램 설정을 `beta`로 설정하는 것과 동일합니다. **~1** 단추를 선택하여 현재 공개적으로 지원되는 주 버전으로 다시 변경합니다. 또한 Azure CLI를 사용하여 이 응용 프로그램 설정을 업데이트할 수 있습니다. 

## <a name="target-a-version-using-the-portal"></a>포털을 사용하여 대상 버전 지정

현재 주 버전이 아닌 버전을 대상 또는 버전 2.0으로 지정해야 하는 경우 `FUNCTIONS_EXTENSION_VERSION` 응용 프로그램 설정을 설정해야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 함수 앱으로 이동하고 **구성된 기능** 아래에서 **응용 프로그램 설정**을 선택합니다.

    ![함수 앱 설정을 선택합니다.](./media/functions-versions/add-update-app-setting1a.png)

2. **응용 프로그램 설정** 탭에서 `FUNCTIONS_EXTENSION_VERSION` 설정을 찾고, 1.x 런타임의 유효한 버전 또는 버전 2.0의 `beta`로 값을 변경합니다. 

    ![함수 앱 설정 지정](./media/functions-versions/add-update-app-setting2.png)

3. **저장**을 클릭하여 응용 프로그램 설정 업데이트를 저장합니다. 

## <a name="target-a-version-using-azure-cli"></a>Azure CLI를 사용하여 대상 버전 지정

 Azure CLI에서 `FUNCTIONS_EXTENSION_VERSION`을 설정할 수도 있습니다. Azure CLI를 사용하여 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) 명령으로 함수 앱에서 응용 프로그램 설정을 업데이트합니다.

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```
이 코드에서 `<function_app>`을 함수 앱 이름으로 바꿉니다. 또한 `<my_resource_group>`을 함수 앱의 리소스 그룹 이름으로 바꿉니다. `<version>`을 1.x 런타임의 유효한 버전 또는 버전 2.0의 `beta`로 바꿉니다. 

앞의 코드 샘플에서 **사용해 보세요.**를 선택하여 [Azure Cloud Shell](../cloud-shell/overview.md)에서 이 명령을 실행할 수 있습니다. 또한 [Azure CLI locally(로컬로 Azure CLI 설치)](/cli/azure/install-azure-cli)를 사용하면 [az login](/cli/azure#az_login)을 실행하여 로그인한 후 이 명령을 실행할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [로컬 개발 환경에서 2.0 런타임을 대상으로 지정](functions-run-local.md)

> [!div class="nextstepaction"]
> [런타임 버전에 대한 릴리스 정보 참조](https://github.com/Azure/azure-webjobs-sdk-script/releases)