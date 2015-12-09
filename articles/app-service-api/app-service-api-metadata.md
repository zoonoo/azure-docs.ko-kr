<properties
	pageTitle="API 검색 및 코드 생성에 대한 앱 서비스 API 앱 메타데이터 | Microsoft Azure"
	description="Azure 앱 서비스에서 API 앱이 Swagger 메타데이터를 사용하여 API 검색 및 코드 생성을 용이하게 하는 방법을 알아봅니다."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="11/25/2015"
	ms.author="tdykstra"/>

# API 검색 및 코드 생성에 대한 앱 서비스 API 앱 메타데이터 

[Swagger 2.0](http://swagger.io/) API 메타데이터에 대한 지원은 앱 서비스 API 앱에 작성됩니다. Swagger를 사용할 필요는 없지만 사용하면 검색 및 소비를 쉽게 만드는 API 앱 기능의 장점을 활용할 수 있습니다.

## Swagger 끝점

API 앱의 속성에서 API 앱에 Swagger 2.0 JSON 메타데이터를 제공하는 끝점을 지정할 수 있습니다. 끝점은 API 앱의 기본 URL 또는 절대 URL에 상대적일 수 있습니다. 절대 URL은 API 앱 외부를 가리킬 수 있습니다.

URL 공개적으로 액세스할 수 있어야 합니다.(사용자 또는 서비스 인증으로 보호되지 않음)

[Azure 포털](https://portal.azure.com/)에서 끝점 URL은 **API 정의** 블레이드에서 확인하고 변경할 수 있습니다.

![](./media/app-service-api-metadata/apidefblade.png)

Visual Studio를 사용하여 API 앱을 만들 때 API 정의 끝점은 API 앱 및 `/swagger/docs/v1`의 기본 URL로 자동으로 설정됩니다. [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) NuGet 패키지가 ASP.NET Web API 프로젝트에 대해 동적으로 생성된 Swagger 메타데이터를 사용하기 위해 사용하는 기본 URL입니다.

## 코드 생성

Azure API 앱에 Swagger를 통합하는 이점 중 하나는 자동 코드 생성입니다. 생성된 클라이언트 클래스는 API 앱을 호출하는 코드를 더욱 쉽게 작성하도록 합니다.

Visual Studio를 사용하거나 명령줄에서 API 앱에 대한 클라이언트 코드를 생성할 수 있습니다. Visual Studio에서 ASP.NET Web API 프로젝트에 클라이언트 클래스를 생성하는 방법에 대한 정보는 [API 앱 및 ASP.NET 시작](app-service-api-dotnet-get-started.md#codegen)을 참조하세요. 지원되는 모든 언어에 대한 명령줄에서 수행하는 방법에 대한 내용은 GitHub.com에서 [Azure/autorest](https://github.com/azure/autorest) 리포지토리의 추가 정보 파일을 참조하세요.
 
## 다음 단계

API 앱을 만들고 배포하며 소비하는 과정을 안내하는 단계별 자습서는 [Azure 앱 서비스에서 API 앱 시작](app-service-api-dotnet-get-started.md)을 참조하세요.

<!---HONumber=AcomDC_1203_2015-->