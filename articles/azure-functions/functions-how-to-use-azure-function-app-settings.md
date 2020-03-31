---
title: Azure에서 함수 앱 설정 구성
description: Azure 함수 앱 설정을 구성하는 방법에 알아봅니다.
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 08/14/2019
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 662a04dbcc39f3fa95b0098eb8fe556b18b3495b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276947"
---
# <a name="manage-your-function-app"></a>함수 앱 관리 

Azure Functions에서 함수 앱은 개별 함수에 대한 실행 컨텍스트를 제공합니다. 함수 앱 동작은 지정된 함수 앱에서 호스트하는 모든 함수에 적용됩니다. 함수 앱의 모든 함수는 동일한 [언어여야](supported-languages.md)합니다. 

함수 앱의 개별 함수는 함께 배포되고 함께 확장됩니다. 동일한 함수 앱의 모든 함수는 함수 앱이 확장됨에 따라 인스턴스당 리소스를 공유합니다. 

연결 문자열, 환경 변수 및 기타 응용 프로그램 설정은 각 함수 앱에 대해 별도로 정의됩니다. 함수 앱 간에 공유해야 하는 모든 데이터는 외부에 지속된 저장소에 저장해야 합니다.

이 문서에서는 함수 앱을 구성하고 관리하는 방법을 설명합니다. 

> [!TIP]  
> [Azure CLI]를 사용하여 많은 구성 옵션을 관리할 수도 있습니다. 

## <a name="get-started-in-the-azure-portal"></a>Azure Portal에서 시작

시작하려면 [Azure Portal]로 이동한 후 Azure 계정으로 로그인합니다. 포털 맨 위에 있는 검색 표시줄에 함수 앱의 이름을 입력하고 목록에서 선택합니다. 함수 앱을 선택하면 다음 페이지가 표시됩니다.

![Azure Portal의 함수 앱 개요](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

개요 페이지에서 기능 앱을 관리하는 데 필요한 모든 것, 특히 **[응용 프로그램 설정](#settings)** 및 **[플랫폼 기능으로](#platform-features)** 이동할 수 있습니다.

## <a name="application-settings"></a><a name="settings"></a>응용 프로그램 설정

**응용 프로그램 설정** 탭은 함수 앱에서 사용하는 설정을 유지 관리합니다. 이러한 설정은 암호화되어 저장되며 포털의 값을 보려면 **값 표시를** 선택해야 합니다. Azure CLI를 사용하여 응용 프로그램 설정에 액세스할 수도 있습니다.

### <a name="portal"></a>포털

포털에 설정을 추가하려면 **새 응용 프로그램 설정을** 선택하고 새 키-값 쌍을 추가합니다.

![Azure 포털의 함수 앱 설정입니다.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

### <a name="azure-cli"></a>Azure CLI

명령은 [`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list) 다음 예제와 같이 기존 응용 프로그램 설정을 반환합니다.

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

명령은 [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) 응용 프로그램 설정을 추가하거나 업데이트합니다. 다음 예제는 키라는 이름의 `CUSTOM_FUNCTION_APP_SETTING` 설정과 다음 `12345`값을 가진 설정을 만듭니다.


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

### <a name="use-application-settings"></a>응용 프로그램 설정 사용

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

로컬로 함수 앱을 개발할 때는 local.settings.json 프로젝트 파일에서 이러한 값의 로컬 복사본을 유지 관리해야 합니다. 자세한 내용은 [로컬 설정 파일을](functions-run-local.md#local-settings-file)참조하십시오.

## <a name="platform-features"></a>플랫폼 기능

![함수 앱 플랫폼 기능 탭.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

함수 앱은 Azure App Service 플랫폼에서 실행되고 유지 관리됩니다. 따라서 함수 앱은 Azure의 핵심 웹 호스팅 플랫폼 기능 대부분에 액세스할 수 있습니다. **플랫폼 기능** 탭에서는 함수 앱에서 사용할 수 있는 App Service 플랫폼의 많은 기능에 액세스할 수 있습니다. 

> [!NOTE]
> 함수 앱이 소비 호스팅 계획에서 실행될 때 모든 App Service 기능을 사용할 수 있는 것은 아닙니다.

이 문서의 나머지 부분에서는 기능에 유용한 Azure 포털의 다음 앱 서비스 기능에 대해 중점적입니다.

+ [App Service 편집기](#editor)
+ [콘솔](#console)
+ [고급 도구(Kudu)](#kudu)
+ [배포 옵션](#deployment)
+ [CORS](#cors)
+ [인증](#auth)

App Service 설정을 사용하는 방법에 대한 자세한 내용은 [Azure App Service 설정 구성](../app-service/configure-common.md)을 참조하세요.

### <a name="app-service-editor"></a><a name="editor"></a>앱 서비스 편집기

![App Service 편집기](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

App Service 편집기는 JSON 구성 파일과 코드 파일을 둘 다 수정하는 데 사용할 수 있는 포털 내 고급 편집기입니다. 이 옵션을 선택하면 기본 편집기와 함께 별도의 브라우저 탭이 실행됩니다. 이를 통해 Git 리포지토리와 통합하고 코드를 실행 및 디버깅하며 함수 앱 설정을 수정할 수 있습니다. 이 편집기는 기본 제공 함수 편집기와 비교하여 함수에 대한 향상된 개발 환경을 제공합니다.  

로컬 컴퓨터에서 함수를 개발하는 것이 좋습니다. 로컬로 개발하고 Azure에 게시하면 프로젝트 파일은 포털에서 읽기 전용으로 표시됩니다. 자세한 내용은 [코드 및 테스트 Azure 함수를 로컬로](functions-develop-local.md)참조하세요.

### <a name="console"></a><a name="console"></a>콘솔

![함수 앱 콘솔](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

포털 내 콘솔은 명령줄에서 함수 앱과 상호 작용하려는 경우에 이상적인 개발자 도구입니다. 일반적인 명령에는 배치 파일 및 스크립트 실행과 함께 디렉터리 및 파일의 생성 및 탐색이 포함됩니다. 

로컬로 개발할 [때Azure 함수 핵심 도구](functions-run-local.md) 및 Azure [CLI를]사용하는 것이 좋습니다.

### <a name="advanced-tools-kudu"></a><a name="kudu"></a>고급 도구(Kudu)

![Kudu 구성](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

App Service용 고급 도구(Kudu라고도 함)를 사용하면 함수 앱의 고급 관리 기능에 액세스할 수 있습니다. Kudu에서 시스템 정보, 앱 설정, 환경 변수, 사이트 확장, HTTP 헤더 및 서버 변수를 관리할 수 있습니다. `https://<myfunctionapp>.scm.azurewebsites.net/`과 같은 함수 앱에 대한 SCM 엔드포인트로 이동하여 **Kudu**를 시작할 수도 있습니다. 


### <a name="deployment-center"></a><a name="deployment"></a>배포 센터

소스 제어 솔루션을 사용하여 함수 코드를 개발하고 유지 관리하는 경우 배포 센터를 사용하면 소스 제어에서 빌드하고 배포할 수 있습니다. 업데이트를 수행할 때 프로젝트가 빌드되고 Azure에 배포됩니다. 자세한 내용은 [Azure Functions의 배포 기술을](functions-deployment-technologies.md)참조하십시오.

### <a name="cross-origin-resource-sharing"></a><a name="cors"></a>크로스-원본 자원 공유

클라이언트에서 악성 코드 실행을 방지하기 위해 최신 브라우저는 웹 응용 프로그램의 요청을 별도의 도메인에서 실행 중인 리소스로 차단합니다. [CORS(원본 간 리소스 공유)를](https://developer.mozilla.org/docs/Web/HTTP/CORS) 사용하면 헤더가 `Access-Control-Allow-Origin` 함수 앱에서 끝점을 호출할 수 있는 원본을 선언할 수 있습니다.

#### <a name="portal"></a>포털

함수 앱에 대해 **허용된 원본** 목록을 `Access-Control-Allow-Origin` 구성하면 함수 앱의 HTTP 끝점의 모든 응답에 헤더가 자동으로 추가됩니다. 

![함수 앱의 CORS 목록 구성](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

와일드카드 ()를`*`사용하면 다른 모든 도메인은 무시됩니다. 

[`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add) 명령을 사용하여 허용된 원본 목록에 도메인을 추가합니다. 다음 예제는 contoso.com 도메인을 추가합니다.

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

[`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show) 명령을 사용하여 허용되는 현재 원기를 나열합니다.

### <a name="authentication"></a><a name="auth"></a>인증

![함수 앱에 대한 인증 구성](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

함수가 HTTP 트리거를 사용하는 경우 먼저 호출이 인증되도록 요구할 수 있습니다. 앱 서비스는 Azure Active Directory 인증 및 Facebook, Microsoft 및 Twitter와 같은 소셜 공급자와의 로그인을 지원합니다. 특정 인증 공급자를 구성하는 방법에 대한 자세한 내용은 [Azure App Service 인증 개요](../app-service/overview-authentication-authorization.md)를 참조하세요. 


## <a name="next-steps"></a>다음 단계

+ [Azure App Service 설정 구성](../app-service/configure-common.md)
+ [Azure Functions에 대한 연속 배포](functions-continuous-deployment.md)

[Azure CLI]: /cli/azure/
[Azure 포털]: https://portal.azure.com
