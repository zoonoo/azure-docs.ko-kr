---
title: "API 검색 및 코드 생성에 대한 App Service API 앱 메타데이터 | Microsoft Docs"
description: "Azure 앱 서비스에서 API 앱이 Swagger 메타데이터를 사용하여 API 검색 및 코드 생성을 용이하게 하는 방법을 알아봅니다."
services: app-service\api
documentationcenter: .net
author: tdykstra
manager: wpickett
editor: 
ms.assetid: c7f8e33a-61cc-486f-89df-4a97dc3c71d4
ms.service: app-service-api
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 53f5c17d51622dbc27a27c750999a925c3f80bff


---
# <a name="app-service-api-apps-metadata-for-api-discovery-and-code-generation"></a>API 검색 및 코드 생성에 대한 앱 서비스 API 앱 메타데이터
[Swagger 2.0](http://swagger.io/) API 메타데이터에 대한 지원은 앱 서비스 API 앱에 작성됩니다. Swagger를 사용할 필요는 없지만 사용하면 검색 및 소비를 쉽게 만드는 API 앱 기능의 장점을 활용할 수 있습니다.   

## <a name="swagger-endpoint"></a>Swagger 끝점
API 앱의 속성에서 API 앱에 Swagger 2.0 JSON 메타데이터를 제공하는 끝점을 지정할 수 있습니다. 끝점은 API 앱의 기본 URL 또는 절대 URL에 상대적일 수 있습니다. 절대 URL은 API 앱 외부를 가리킬 수 있습니다. 

많은 다운스트림 클라이언트(예: Visual Studio 코드 생성 및 PowerApps "API 추가" 흐름)인 URL은 공개적으로 액세스할 수 있어야 합니다.(사용자 또는 서비스 인증으로 보호되지 않음) 즉, 앱 서비스 인증을 사용하고 자체 앱 내에서 API 정의를 노출하려는 경우 익명 트래픽을 허용하는 인증 옵션을 사용하여 API에 도달해야 합니다. 자세한 내용은 [App Service API 앱에 대한 인증 및 권한 부여](app-service-api-authentication.md)를 참조하세요.

### <a name="portal-blade"></a>포털 블레이드
[Azure 포털](https://portal.azure.com/) 에서 끝점 URL은 **API 정의** 블레이드에서 확인하고 변경할 수 있습니다.

![](./media/app-service-api-metadata/apidefblade.png)

### <a name="azure-resource-manager-property"></a>Azure 리소스 관리자 속성
또한 [Azure PowerShell](../powershell-install-configure.md)과 [Azure CLI](../xplat-cli-install.md) 등 명령줄 도구의 [리소스 탐색기](https://resources.azure.com/) 또는 [Azure Resource Manager 템플릿](../resource-group-authoring-templates.md)을 사용하여 API 앱에 대한 API 정의 URL을 구성할 수 있습니다. 

**리소스 탐색기**에서 **구독 > {your subscription} > resourceGroups > {your resource group} > 공급자 > Microsoft.Web > 사이트 > {your site} > 구성 > 웹**으로 이동하면 `apiDefinition` 속성이 표시됩니다.

        "apiDefinition": {
          "url": "https://contactslistapi.azurewebsites.net/swagger/docs/v1"
        }

`apiDefinition` 속성을 설정하는 Azure Resource Manager 템플릿의 예를 보려면 [To-Do List 응용 프로그램 예제에 있는 azuredeploy.json 파일](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json)을 엽니다. 위에 표시된 JSON 샘플과 같은 템플릿 섹션을 찾습니다.

### <a name="default-value"></a>기본값
Visual Studio를 사용하여 API 앱을 만들 때 API 정의 끝점은 API 앱 및 `/swagger/docs/v1`의 기본 URL로 자동으로 설정됩니다. [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) NuGet 패키지가 ASP.NET Web API 프로젝트에 대해 동적으로 생성된 Swagger 메타데이터를 사용하기 위해 사용하는 기본 URL입니다. 

## <a name="code-generation"></a>코드 생성
Azure API 앱에 Swagger를 통합하는 이점 중 하나는 자동 코드 생성입니다. 생성된 클라이언트 클래스는 API 앱을 호출하는 코드를 더욱 쉽게 작성하도록 합니다.

Visual Studio를 사용하거나 명령줄에서 API 앱에 대한 클라이언트 코드를 생성할 수 있습니다. Visual Studio에서 ASP.NET Web API 프로젝트에 클라이언트 클래스를 생성하는 방법에 대한 정보는 [API 앱 및 ASP.NET 시작](app-service-api-dotnet-get-started.md#codegen)을 참조하세요. 지원되는 모든 언어에 대한 명령줄에서 수행하는 방법에 대한 내용은 GitHub.com에서 [Azure/autorest](https://github.com/azure/autorest) 리포지토리의 추가 정보 파일을 참조하세요.

## <a name="next-steps"></a>다음 단계
API 앱을 만들고 배포하며 소비하는 과정을 안내하는 단계별 자습서는 [Azure 앱 서비스에서 API 앱 시작](app-service-api-dotnet-get-started.md)을 참조하세요.

API 앱과 함께 Azure API 관리를 사용하는 경우 Swagger 메타데이터를 사용하여 API를 API 관리로 가져올 수 있습니다. 자세한 내용은 [Azure API 관리에서 작업과 함께 API의 정의를 가져오는 방법](../api-management/api-management-howto-import-api.md)을 참조하세요. 




<!--HONumber=Nov16_HO3-->


