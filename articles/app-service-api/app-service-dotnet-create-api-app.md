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
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="bradyg;tarcher"/>

# Azure 앱 서비스에서 ASP.NET API 앱 만들기

## 개요

이 자습서에서는 ASP.NET Web API 프로젝트를 처음부터 만들고 [Azure 앱 서비스](../app-service/app-service-value-prop-what-is.md)에서 [API 앱](app-service-api-apps-why-best-platform.md)으로 클라우드에 배포하기 위해 구성하는 방법을 보여 줍니다. API 앱으로 변환하려는 기존 Web API 프로젝트가 있는 경우 [Web API 프로젝트를 API 앱으로 구성](./app-service-dotnet-create-api-app-visual-studio) 문서를 참조하세요. 시리즈의 이후 자습서에서는 이 자습서에서 만든 API 앱 프로젝트를 [배포](app-service-dotnet-deploy-api-app.md) 및 [디버그](../app-service-dotnet-remotely-debug-api-app.md)하는 방법을 보여 줍니다.

API 앱에 대한 자세한 내용은 [API 앱 정의](app-service-api-apps-why-best-platform.md)를 참조하세요.

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

이 자습서에는 Azure SDK for .NET 버전 2.5.1 이상이 필요합니다.

## API 앱 프로젝트 만들기 

이 섹션에서는 Azure API 앱 프로젝트 템플릿을 사용하여 API 앱을 처음부터 만드는 방법을 보여 줍니다. 기존 Web API 프로젝트를 API 앱으로 구성하는 방법을 알아보려면 [다음 섹션](#configure-a-web-api-project-as-an-api-app)으로 건너뛰세요.

1. Visual Studio 2013을 엽니다.

2. **파일 > 새 프로젝트**를 선택합니다.

3. **ASP.NET 웹 응용 프로그램** 템플릿을 선택합니다.

4. 프로젝트의 이름을 *ContactsList*로 지정합니다.

	![](./media/app-service-dotnet-create-api-app/01-filenew-v3.png)

5. **확인**을 클릭합니다.

6. **새 ASP.NET 프로젝트** 대화 상자에서 **Azure API 앱** 프로젝트 템플릿을 선택합니다.

	![](./media/app-service-dotnet-create-api-app/02-api-app-template-v3.png)

7. **확인**을 클릭하여 프로젝트를 생성합니다.

Visual Studio에서 API 앱으로 배포하기 위해 구성된 Web API 프로젝트를 만듭니다.

[AZURE.INCLUDE [app-service-api-review-metadata](../../includes/app-service-api-review-metadata.md)]

[AZURE.INCLUDE [app-service-api-define-api-app](../../includes/app-service-api-define-api-app.md)]

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## 다음 단계

이제 API 앱을 배포할 준비가 되었으며 [API 앱 배포](app-service-dotnet-deploy-api-app.md) 자습서에 따라 API 앱을 배포할 수 있습니다.

<!--HONumber=54--> 