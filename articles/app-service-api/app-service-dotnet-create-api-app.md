<properties
	pageTitle="Azure 앱 서비스에서 ASP.NET API 앱 만들기"
	description="Visual Studio 2013을 사용하여 Azure 앱 서비스에서 ASP.NET API 앱을 만드는 방법을 알아봅니다."
	services="app-service\api"
	documentationCenter=".net"
	authors="bradygaster"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="05/19/2015"
	ms.author="bradyg;tarcher"/>

# Azure 앱 서비스에서 ASP.NET API 앱 만들기

> [AZURE.SELECTOR]
- [Visual Studio 2013](app-service-dotnet-create-api-app.md)
- [Visual Studio 2015 RC](app-service-dotnet-create-api-app-vs2015.md)
- [Visual Studio Code](app-service-create-aspnet-api-app-using-vscode.md)

## 개요

이 자습서에서는 [Azure 앱 서비스](../app-service/app-service-value-prop-what-is.md)의 [API 앱](app-service-api-apps-why-best-platform.md)으로 클라우드에 배포할 프로젝트를 구성하는 Visual Studio 2013 템플릿을 사용하여 ASP.NET Web API 프로젝트를 만드는 방법을 보여 줍니다. 기존 Web API 프로젝트를 API 앱으로 배포하기 위해 구성하는 방법에 대한 정보는 [API app으로서의 Web API 프로젝트 구성](app-service-dotnet-create-api-app-visual-studio.md)을 참조하십시오.

시리즈의 이후 자습서에서는 이 자습서에서 만든 API 앱 프로젝트를 [배포](app-service-dotnet-deploy-api-app.md) 및 [디버그](../app-service-dotnet-remotely-debug-api-app.md)하는 방법을 보여 줍니다.

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

이 자습서에는 Azure SDK for .NET 버전 2.5.1 이상이 필요합니다.

## API 앱 프로젝트 만들기

프로젝트의 이름을 입력하라는 지침이 있으면 *ContactsList*를 입력합니다.

[AZURE.INCLUDE [app-service-api-create](../../includes/app-service-api-create.md)]

[AZURE.INCLUDE [app-service-api-review-metadata](../../includes/app-service-api-review-metadata.md)]

[AZURE.INCLUDE [app-service-api-define-api-app](../../includes/app-service-api-define-api-app.md)]

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## 다음 단계

이제 API 앱을 배포할 준비가 되었으며 [API 앱 배포](app-service-dotnet-deploy-api-app.md) 자습서에 따라 API 앱을 배포할 수 있습니다.
 

<!---HONumber=July15_HO4-->