---
title: "Azure Functions 런타임 버전을 대상으로 지정하는 방법 | Microsoft Docs"
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
ms.date: 10/04/2017
ms.author: glenga
ms.openlocfilehash: 26d4276a0a550d78a9c7657c464bd3320c956fb0
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2017
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Azure Functions 런타임 버전을 대상으로 지정하는 방법

Azure Functions 런타임은 Azure에서 서버를 사용하지 않는 코드 실행을 구현합니다. Azure에서 호스팅되는 것 이외의 다양한 환경에서 이 런타임을 찾습니다. [Azure Functions 핵심 도구](functions-run-local.md)는 개발 컴퓨터에서 이 런타임을 구현합니다. [Azure Functions 런타임](functions-runtime-overview.md)을 사용하면 온-프레미스 환경에서 Azure Functions를 사용할 수 있습니다. 

Functions는 여러 주 버전의 런타임을 지원합니다. 주 버전 업데이트에는 주요 변경 내용이 도입될 수 있습니다. 이 항목에서는 Azure에서 호스팅될 때 특정 런타임 버전의 함수 앱을 대상으로 지정하는 방법에 대해 설명합니다. 

Functions를 통해 함수 앱에서 `FUNCTIONS_EXTENSION_VERSION` 응용 프로그램 설정을 사용하여 특정 주 버전의 런타임을 대상으로 지정할 수 있습니다. 이 기능은 공용 및 미리 보기 버전에 모두 적용됩니다. 함수 앱을 새 버전으로 변경하도록 명시적으로 선택할 때까지 지정된 주 런타임 버전에서 유지합니다. 사용할 수 있는 경우 함수 앱을 런타임의 새로운 부 버전으로 업데이트됩니다. 부 버전 업데이트에는 주요 변경 내용이 도입되지 않습니다.  

새로운 주 버전을 공개적으로 사용할 수 있는 경우 포털에서 함수 앱을 볼 때 해당 버전을 업데이트할 기회가 있습니다. 새 버전으로 변경한 후에 `FUNCTIONS_EXTENSION_VERSION` 응용 프로그램 설정을 사용하여 이전 런타임 버전으로 다시 변경할 수 있습니다.

런타임 버전을 변경할 때마다 앱 함수를 다시 시작하게 됩니다. 모든 런타임 릴리스(주 및 부 버전 모두)의 릴리스 정보는 [GitHub 리포지토리](https://github.com/Azure/azure-webjobs-sdk-script/releases)에 게시됩니다.   
## <a name="view-the-current-runtime-version"></a>현재 런타임 버전 보기

현재 함수 앱에서 사용하는 특정 런타임 버전을 보려면 다음 절차를 따르세요. 

1. [Azure Portal](https://portal.azure.com)에서 함수 앱으로 이동하고 **구성된 기능** 아래에서 **함수 앱 설정**을 선택합니다. 

    ![함수 앱 설정을 선택합니다.](./media/functions-versions/add-update-app-setting.png)

2. **함수 앱 설정** 탭에서 **런타임 버전**을 찾습니다. 특정 런타임 버전 및 요청된 주 버전을 적어둡니다. 아래 예제에서 주 버전은 `~1.0`으로 설정됩니다.
 
   ![함수 앱 설정을 선택합니다.](./media/functions-versions/function-app-view-version.png)

## <a name="target-the-functions-version-20-runtime"></a>함수 버전 2.0 런타임 대상 지정

>[!IMPORTANT]   
> Azure Functions 런타임 2.0은 미리 보기 상태이며, 현재 Azure Functions의 일부 기능은 지원되지 않습니다. 자세한 내용은 [Azure Functions 런타임 2.0 알려진 문제](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues)를 참조하세요.  

<!-- Add a table comparing the 1.x and 2.x runtime features-->

[!INCLUDE [functions-set-runtime-version](../../includes/functions-set-runtime-version.md)]

Azure Portal에서 함수 앱을 런타임 버전 2.0 미리 보기로 변경할 수 있습니다. **함수 앱 설정** 탭의 **런타임 버전** 아래에서 **베타**를 찾습니다.  

   ![함수 앱 설정을 선택합니다.](./media/functions-versions/function-app-view-version.png)

이 설정은 `FUNCTIONS_EXTENSION_VERSION` 응용 프로그램 설정을 `beta`로 설정하는 것과 동일합니다. **~1** 단추를 선택하여 현재 공개적으로 지원되는 주 버전으로 다시 변경합니다. 또한 Azure CLI를 사용하여 이 응용 프로그램 설정을 업데이트할 수 있습니다. 

## <a name="target-a-specific-runtime-version-from-the-portal"></a>포털에서 특정 런타임 버전 대상 지정

현재 주 버전이 아닌 주 버전을 대상 또는 버전 2.0으로 지정해야 하는 경우 `FUNCTIONS_EXTENSION_VERSION` 응용 프로그램 설정을 설정해야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 함수 앱으로 이동하고 **구성된 기능** 아래에서 **응용 프로그램 설정**을 선택합니다.

    ![함수 앱 설정을 선택합니다.](./media/functions-versions/add-update-app-setting1a.png)

2. **응용 프로그램 설정** 탭에서 `FUNCTIONS_EXTENSION_VERSION` 설정을 찾고, 1.x 런타임의 유효한 주 버전 또는 버전 2.0의 `beta`로 값을 변경합니다. 

    ![함수 앱 설정 지정](./media/functions-versions/add-update-app-setting2.png)

3. **저장**을 클릭하여 응용 프로그램 설정 업데이트를 저장합니다. 

## <a name="target-a-specific-version-using-azure-cli"></a>Azure CLI를 사용하여 특정 버전 대상 지정

 Azure CLI에서 `FUNCTIONS_EXTENSION_VERSION`을 설정할 수도 있습니다. Azure CLI를 사용하여 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) 명령으로 함수 앱에서 응용 프로그램 설정을 업데이트합니다.

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```
이 코드에서 `<function_app>`을 함수 앱 이름으로 바꿉니다. 또한 `<my_resource_group>`을 함수 앱의 리소스 그룹 이름으로 바꿉니다. `<version>`을 1.x 런타임의 유효한 주 버전 또는 버전 2.0의 `beta`로 바꿉니다. 

앞의 코드 샘플에서 **사용해 보세요.**를 선택하여 [Azure Cloud Shell](../cloud-shell/overview.md)에서 이 명령을 실행할 수 있습니다. 또한 [Azure CLI locally(로컬로 Azure CLI 설치)](/cli/azure/install-azure-cli)를 사용하면 [az login](/cli/azure#az_login)을 실행하여 로그인한 후 이 명령을 실행할 수도 있습니다.
