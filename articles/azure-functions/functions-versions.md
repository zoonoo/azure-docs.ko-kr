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
ms.openlocfilehash: 110197943d25677112c9c2b84da70913e92d31ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Azure Functions 런타임 버전을 대상으로 지정하는 방법

Azure Functions 런타임은 Azure에서 서버를 사용하지 않는 코드 실행을 구현합니다. Azure에서 호스팅되는 것 이외의 다양한 환경에서 이 런타임을 찾습니다. [Azure Functions 핵심 도구](functions-run-local.md)는 개발 컴퓨터에서 이 런타임을 구현합니다. [Azure Functions 런타임](functions-runtime-overview.md)을 사용하면 온-프레미스 환경에서 Azure Functions를 사용할 수 있습니다. 

Azure Functions는 여러 버전의 런타임을 지원합니다. 이러한 버전은 기존 함수 앱에 대한 이전 버전과의 호환성을 유지하면서 새 기능과 향상된 기능을 지원할 수 있는 기능을 제공합니다. 이 항목에서는 Azure에서 호스팅될 때 특정 런타임 버전의 함수 앱을 대상으로 지정하는 방법에 대해 설명합니다. 

특정 런타임의 함수 앱을 로컬에서 테스트하는 방법을 알아보려면 [Azure Functions 핵심 도구](functions-run-local.md)를 참조하세요. 

>[!IMPORTANT]   
> Azure Functions 런타임 2.0은 미리 보기 상태이며, 현재 Azure Functions의 일부 기능은 지원되지 않습니다. 자세한 내용은 [Azure Functions 런타임 2.0 알려진 문제](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues)를 참조하세요.  

<!-- Add a table comparing the 1.x and 2.x runtime features-->

[!INCLUDE [functions-set-runtime-version](../../includes/functions-set-runtime-version.md)]

이 응용 프로그램 설정은 여러 가지 방법으로 함수 앱에 추가할 수 있습니다.

## <a name="set-the-runtime-in-the-portal"></a>포털에서 런타임 설정

1. [Azure Portal](https://portal.azure.com)에서 함수 앱으로 이동하고 **응용 프로그램 설정** 탭을 선택합니다.

2. `FUNCTIONS_EXTENSION_VERSION` 설정을 찾고 값을 `beta`로 변경합니다. 이 설정이 없으면 **+ 새 설정 추가**를 클릭하여 추가합니다.   

    ![](./media/functions-versions/add-update-app-setting.png)

3. **저장**을 클릭하여 응용 프로그램 설정 업데이트를 저장합니다. 

## <a name="set-the-runtime-using-azure-cli"></a>Azure CLI를 사용하여 런타임 설정

 Azure CLI를 사용하여 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) 명령으로 이 응용 프로그램 설정을 함수 앱에 추가합니다.

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=beta
```
이 코드에서 `<function_app>`을 함수 앱 이름으로 바꿉니다. 또한 `<my_resource_group>`을 함수 앱의 리소스 그룹 이름으로 바꿉니다. 앞의 코드 샘플에서 **사용해 보세요**를 선택하여 [Azure Cloud Shell]에서 이 명령을 실행할 수 있습니다. 또한 [Azure CLI locally(로컬로 Azure CLI 설치)](/cli/azure/install-azure-cli)를 사용하면 [az login](/cli/azure#az_login)을 실행하여 로그인한 후 이 명령을 실행할 수도 있습니다.