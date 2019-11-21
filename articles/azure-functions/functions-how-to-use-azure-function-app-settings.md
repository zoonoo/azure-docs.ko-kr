---
title: Configure function app settings in Azure
description: Azure 함수 앱 설정을 구성하는 방법에 알아봅니다.
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 08/14/2019
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 662a04dbcc39f3fa95b0098eb8fe556b18b3495b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230567"
---
# <a name="manage-your-function-app"></a>Manage your function app 

Azure Functions에서 함수 앱은 개별 함수에 대한 실행 컨텍스트를 제공합니다. 함수 앱 동작은 지정된 함수 앱에서 호스트하는 모든 함수에 적용됩니다. All functions in a function app must be of the same [language](supported-languages.md). 

Individual functions in a function app are deployed together and are scaled together. All functions in the same function app share resources, per instance, as the function app scales. 

Connection strings, environment variables, and other application settings are defined separately for each function app. Any data that must be shared between function apps should be stored externally in a persisted store.

This article describes how to configure and manage your function apps. 

> [!TIP]  
> Many configuration options can also be managed by using the [Azure CLI]. 

## <a name="get-started-in-the-azure-portal"></a>Azure Portal에서 시작

시작하려면 [Azure 포털]로 이동한 후 Azure 계정으로 로그인합니다. 포털 맨 위에 있는 검색 표시줄에 함수 앱의 이름을 입력하고 목록에서 선택합니다. 함수 앱을 선택하면 다음 페이지가 표시됩니다.

![Azure Portal의 함수 앱 개요](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

You can navigate to everything you need to manage your function app from the overview page, in particular the **[Application settings](#settings)** and **[Platform features](#platform-features)** .

## <a name="settings"></a>애플리케이션 설정

The **Application Settings** tab maintains settings that are used by your function app. These settings are stored encrypted, and you must select **Show values** to see the values in the portal. You can also access application settings by using the Azure CLI.

### <a name="portal"></a>포털

To add a setting in the portal, select **New application setting** and add the new key-value pair.

![Function app settings in the Azure portal.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

### <a name="azure-cli"></a>Azure CLI

The [`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list) command returns the existing application settings, as in the following example:

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

The [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) command adds or updates an application setting. The following example creates a setting with a key named `CUSTOM_FUNCTION_APP_SETTING` and a value of `12345`:


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

### <a name="use-application-settings"></a>Use application settings

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

When you develop a function app locally, you must maintain local copies of these values in the local.settings.json project file. To learn more, see [Local settings file](functions-run-local.md#local-settings-file).

## <a name="platform-features"></a>Platform features

![함수 앱 플랫폼 기능 탭.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

함수 앱은 Azure App Service 플랫폼에서 실행되고 유지 관리됩니다. 따라서 함수 앱은 Azure의 핵심 웹 호스팅 플랫폼 기능 대부분에 액세스할 수 있습니다. **플랫폼 기능** 탭에서는 함수 앱에서 사용할 수 있는 App Service 플랫폼의 많은 기능에 액세스할 수 있습니다. 

> [!NOTE]
> 함수 앱이 소비 호스팅 계획에서 실행될 때 모든 App Service 기능을 사용할 수 있는 것은 아닙니다.

The rest of this article focuses on the following App Service features in the Azure portal that are useful for Functions:

+ [App Service 편집기](#editor)
+ [Console](#console)
+ [고급 도구(Kudu)](#kudu)
+ [배포 옵션](#deployment)
+ [CORS](#cors)
+ [인증](#auth)

App Service 설정을 사용하는 방법에 대한 자세한 내용은 [Azure App Service 설정 구성](../app-service/configure-common.md)을 참조하세요.

### <a name="editor"></a>App Service 편집기

![App Service 편집기](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

App Service 편집기는 JSON 구성 파일과 코드 파일을 둘 다 수정하는 데 사용할 수 있는 포털 내 고급 편집기입니다. 이 옵션을 선택하면 기본 편집기와 함께 별도의 브라우저 탭이 실행됩니다. 이를 통해 Git 리포지토리와 통합하고 코드를 실행 및 디버깅하며 함수 앱 설정을 수정할 수 있습니다. This editor provides an enhanced development environment for your functions compared with the built-in function editor.  

We recommend that you consider developing your functions on your local computer. When you develop locally and publish to Azure, your project files are read-only in the portal. To learn more, see [Code and test Azure Functions locally](functions-develop-local.md).

### <a name="console"></a>콘솔

![함수 앱 콘솔](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

포털 내 콘솔은 명령줄에서 함수 앱과 상호 작용하려는 경우에 이상적인 개발자 도구입니다. 일반적인 명령에는 배치 파일 및 스크립트 실행과 함께 디렉터리 및 파일의 생성 및 탐색이 포함됩니다. 

When developing locally, we recommend using the [Azure Functions Core Tools](functions-run-local.md) and the [Azure CLI].

### <a name="kudu"></a>고급 도구(Kudu)

![Kudu 구성](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

App Service용 고급 도구(Kudu라고도 함)를 사용하면 함수 앱의 고급 관리 기능에 액세스할 수 있습니다. Kudu에서 시스템 정보, 앱 설정, 환경 변수, 사이트 확장, HTTP 헤더 및 서버 변수를 관리할 수 있습니다. `https://<myfunctionapp>.scm.azurewebsites.net/`과 같은 함수 앱에 대한 SCM 엔드포인트로 이동하여 **Kudu**를 시작할 수도 있습니다. 


### <a name="deployment"></a>Deployment Center

When you use a source control solution to develop and maintain your functions code, Deployment Center lets you build and deploy from source control. Your project is built and deployed to Azure when you make updates. For more information, see [Deployment technologies in Azure Functions](functions-deployment-technologies.md).

### <a name="cors"></a>Cross-origin resource sharing

To prevent malicious code execution on the client, modern browsers block requests from web applications to resources running in a separate domain. [Cross-origin resource sharing (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS) lets an `Access-Control-Allow-Origin` header declare which origins are allowed to call endpoints on your function app.

#### <a name="portal"></a>포털

When you configure the **Allowed origins** list for your function app, the `Access-Control-Allow-Origin` header is automatically added to all responses from HTTP endpoints in your function app. 

![Configure function app's CORS list](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

When the wildcard (`*`) is used, all other domains are ignored. 

Use the [`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add) command to add a domain to the allowed origins list. The following example adds the contoso.com domain:

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

Use the [`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show) command to list the current allowed origins.

### <a name="auth"></a>인증

![함수 앱에 대한 인증 구성](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

함수가 HTTP 트리거를 사용하는 경우 먼저 호출이 인증되도록 요구할 수 있습니다. App Service supports Azure Active Directory authentication and sign-in with social providers, such as Facebook, Microsoft, and Twitter. 특정 인증 공급자를 구성하는 방법에 대한 자세한 내용은 [Azure App Service 인증 개요](../app-service/overview-authentication-authorization.md)를 참조하세요. 


## <a name="next-steps"></a>다음 단계

+ [Azure App Service 설정 구성](../app-service/configure-common.md)
+ [Azure Functions에 대한 연속 배포](functions-continuous-deployment.md)

[Azure CLI]: /cli/azure/
[Azure 포털]: https://portal.azure.com
