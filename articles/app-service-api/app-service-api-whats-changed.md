<properties
	pageTitle="앱 서비스 API 앱 - 변경된 내용 | Microsoft Azure"
	description="Azure 앱 서비스의 API 앱에 대한 새로운 기능을 알아봅니다."
	services="app-service\api"
	documentationCenter=".net"
	authors="mohitsriv"
	manager="wpickett"
	editor="tdykstra"/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/13/2016"
	ms.author="mohisri"/>

# 앱 서비스 API 앱 - 변경된 내용

2015년 11월 Connect() 행사에서 Azure 앱 서비스에 대한 다양한 향상된 기능이 [발표](https://azure.microsoft.com/blog/azure-app-service-updates-november-2015/)되었습니다. 이러한 향상된 기능에는 모바일 및 웹앱에 보다 적합하게 조정하고, 개념 수를 줄이고, 배포 및 런타임 성능을 개선하기 위한 API 앱의 기본 변경 내용이 포함됩니다. 2015년 11월 30일부터 Azure 관리 포털 또는 최신 도구를 사용하여 만드는 새 API 앱에는 이러한 변경 내용이 반영됩니다. 이 문서에서는 이러한 변경 내용과 기존 앱을 다시 배포하여 기능을 활용하는 방법을 설명합니다.

## 기능 변경
API 앱의 주요 기능(인증, CORS 및 API 메타데이터)이 앱 서비스로 바로 이동되었습니다. 이 변경 내용에 따라 웹, 모바일 및 API 앱에서 기능을 사용할 수 있습니다. 실제로 세 기능 모두 리소스 관리자에서 동일한 **microsoft.web/sites** 리소스 종류를 공유합니다. API 앱 게이트웨이는 더 이상 필요 없거나 API 앱과 함께 제공되지 않습니다. 따라서 단일 API 관리 게이트웨이만 있으므로 Azure API 관리를 보다 쉽게 사용할 수 있습니다.

![API 앱 개요](./media/app-service-api-whats-changed/api-apps-overview.png)

API 앱 업데이트의 핵심 디자인 원칙은 선택한 언어의 API를 있는 그대로 가져올 수 있도록 하는 것입니다. API가 웹 앱 또는 모바일 앱으로 이미 배포된 경우* 앱을 다시 배포하지 않아도 새 기능을 활용할 수 있습니다. 현재 API 앱 미리 보기를 사용하는 경우 자세한 내용은 아래의 마이그레이션 지침을 참조하세요.

### 인증
기존 턴키 API 앱, 모바일 서비스/앱 및 웹앱 인증 기능이 통합되었으며 관리 포털의 단일 Azure 앱 서비스 인증 블레이드에서 사용할 수 있습니다. 앱 서비스의 인증 서비스에 대한 소개는 [앱 서비스 인증/권한 부여 확장](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/)을 참조하세요.

API 시나리오의 경우 관련된 여러 가지 새로운 기능이 있습니다.

- AAD 토큰과 세션 토큰을 교환하는 클라이언트 코드 없이 **Azure Active Directory 사용을 직접 지원**: 클라이언트에서 전달자 토큰 사양에 따라 인증 헤더에 AAD 토큰을 포함하기만 하면 됩니다. 이는 클라이언트 또는 서버 쪽에 앱 서비스별 SDK가 필요 없음을 의미하기도 합니다. 
- **서비스 간 또는 "내부" 액세스**: 디먼 프로세스 또는 인터페이스 없이 API에 액세스해야 하는 다른 클라이언트가 있는 경우 AAD 서비스 주체를 사용하여 토큰을 요청하고 응용 프로그램 인증을 위해 이를 앱 서비스에 전달할 수 있습니다.
- **지연된 권한 부여**: 대부분의 응용 프로그램은 해당 응용 프로그램의 여러 부분에 대해 액세스 제한 사항이 서로 다릅니다. 일부 API는 공개적으로 사용할 수 있도록 하고 다른 API는 로그인하도록 할 수 있습니다. 원래 인증/권한 부여 기능은 전체 사이트에서 로그인을 요구하므로 전부 아니면 전무였습니다. 이 옵션은 여전히 존재하지만, 앱 서비스에서 사용자를 인증한 후 액세스 결정을 렌더링하도록 응용 프로그램 코드를 허용할 수 있습니다.
 
새로운 인증 기능에 대한 자세한 내용은 [인증 및 Azure App Service에서 API 앱 인증](app-service-api-authentication.md)을 참조하세요. 새 이전 API 앱 모델에서 새 모델로 기존 API 앱을 마이그레이션하는 방법에 대한 자세한 내용은 이 문서의 뒷부분에 나오는 [기존 API 앱 마이그레이션](#migrating-existing-api-apps)을 참조하세요.
 
### CORS
쉼표로 구분된 **MS\_CrossDomainOrigins** 앱 설정 대신 이제 Azure 관리 포털에 CORS를 구성하는 블레이드가 있습니다. 또는 Azure PowerShell, CLI, [리소스 탐색기](https://resources.azure.com/) 등의 리소스 관리자 도구를 사용하여 구성할 수 있습니다. **&lt;site name&gt;/web** 리소스에 대해 **Microsoft.Web/sites/config** 리소스 종류에서 **cors** 속성을 설정합니다. 예:

    {
        "cors": {
            "allowedOrigins": [
                "https://localhost:44300"
            ]
        }
    } 

### API 메타데이터
이제 웹, 모바일 및 API 앱에서 API 정의 블레이드를 사용할 수 있습니다. 관리 포털에서 상대 url 또는 API의 Swagger 2.0 표현을 호스트하는 끝점을 가리키는 절대 url을 지정할 수 있습니다. 또는 리소스 관리자 도구를 사용하여 구성할 수 있습니다. **&lt;site name&gt;/web** 리소스에 대해 **Microsoft.Web/sites/config** 리소스 종류에서 **apiDefinition** 속성을 설정합니다. 예:

    {
        "apiDefinition":
        {
            "url": "https://myStorageAccount.blob.core.windows.net/swagger/apiDefinition.json"
        }
    }

이번에는 많은 다운스트림 클라이언트(예: Visual Studio REST API 클라이언트 생성 및 PowerApps "API 추가" 흐름)에서 사용할 수 있도록 메타데이터 끝점을 인증 없이 공개적으로 사용할 수 있도록 해야 합니다. 이는 앱 서비스 인증을 사용하는 경우 앱 자체 내에서 API 정의를 노출하려면 Swagger 메타데이터의 경로가 공개되도록 이전에 설명한 지연된 인증 옵션을 사용해야 함을 의미합니다.

## 관리 포털
포털에서 **새로 만들기 > 웹 + 모바일 > API 앱**을 선택하면 이 문서에 설명된 새 기능을 반영하는 API 앱이 만들어집니다. **찾아보기 > API 앱**은 이러한 새 API 앱만 표시합니다. API 앱으로 이동하면 블레이드에서 웹 및 모바일 앱과 동일한 레이아웃 및 기능을 공유합니다. 빠른 시작 콘텐츠와 설정 순서만 다릅니다.

이전 미리 보기 기능이 있는 기존 API 앱(또는 논리 앱에서 만든 Marketplace API 앱)은 논리 앱 디자이너에 계속 표시되며, 리소스 그룹의 모든 리소스를 검색할 때도 표시됩니다.

## Visual Studio

대부분의 웹앱 도구는 동일한 기본 **Microsoft.Web/sites** 리소스 종류를 공유하므로 새 API 앱과 함께 작동합니다. 그러나 Azure Visual Studio 도구는 API에 특정한 여러 기능을 노출하므로 버전 2.8.1 이상으로 업그레이드해야 합니다. [Azure 다운로드 페이지](https://azure.microsoft.com/downloads/)에서 SDK를 다운로드합니다.

앱 서비스 형식의 합리화에 따라 게시도 **게시 > Microsoft Azure 앱 서비스** 아래에 통합되었습니다.

![API 앱 게시](./media/app-service-api-whats-changed/api-apps-publish.png)

SDK 2.8.1에 대한 자세한 내용은 알림 [블로그 게시물](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-1-for-net/)을 참조하세요.

또는 관리 포털에서 게시 프로필을 수동으로 가져와 게시를 설정할 수 있습니다. 그러나 클라우드 탐색기, 코드 생성 및 API 앱 선택/만들기에는 SDK 2.8.1 이상이 필요합니다.

## 기존 API 앱 마이그레이션
사용자 지정 API를 이전 미리 보기 버전의 API 앱에 배포한 경우 2015년 12월 31일까지 API 앱의 새 모델로 마이그레이션하는 것이 좋습니다. 이전 모델과 새 모델 모두 앱 서비스에서 호스트되는 Web API를 기반으로 하기 때문에 기존 코드를 대부분 다시 사용할 수 있습니다.

### 호스팅 및 다시 배포
다시 배포 단계는 기존 Web API를 앱 서비스에 배포하는 것과 동일합니다. 단계:

1. 빈 API 앱을 만듭니다. 포털(새로 만들기 > API 앱), Visual Studio(게시) 또는 리소스 관리자 도구에서 만들 수 있습니다. 리소스 관리자 도구 또는 템플릿을 사용하는 경우 관리 포털의 빠른 시작 및 설정이 API 시나리오를 지향하도록 **Microsoft.Web/sites** 리소스 종류에서 **kind** 값을 **api**로 설정합니다.
2. 앱 서비스에서 지원하는 배포 메커니즘 중 하나를 사용하여 빈 API 앱에 프로젝트를 연결하고 배포합니다. 자세한 내용은 [Azure 앱 서비스 배포 설명서](../app-service-web/web-sites-deploy.md)를 참조하세요. 
  
### 인증
앱 서비스 인증 서비스는 이전 API 앱 모델에서 사용할 수 있던 것과 동일한 기능을 지원합니다. 세션 토큰을 사용할 때 SDK가 필요한 경우 다음 클라이언트 및 서버 SDK를 사용합니다.

- 클라이언트: [Azure Mobile Client SDK](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- 서버: [Microsoft Azure Mobile App .NET Authentication Extension](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/) 

대신 앱 서비스 알파 SDK를 사용한 경우 이제 다음 SDK는 더 이상 사용되지 않습니다.

- 클라이언트: [Microsoft Azure AppService SDK](http://www.nuget.org/packages/Microsoft.Azure.AppService)
- 서버: [Microsoft.Azure.AppService.ApiApps.Service](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service)

그러나 특히 Azure Active Directory는 AAD 토큰을 직접 사용하는 경우 앱 서비스별 SDK가 필요 없습니다.

### 내부 액세스
이전 API 앱 모델에는 기본 제공 내부 액세스 수준이 포함되어 있습니다. 이 모델에서는 요청을 서명하는 데 SDK를 사용해야 했습니다. 앞서 설명했듯이, 새 API 앱 모델에서는 앱 서비스별 SDK 없이 AAD 서비스 주체를 서비스 간 인증에 대신 사용할 수 있습니다. 자세한 내용은 [Azure 앱 서비스의 API 앱에 대한 서비스 주체 인증](app-service-api-dotnet-service-principal-auth.md)을 참조하세요.

### 검색
이전 API 앱 모델에는 동일한 게이트웨이 뒤의 동일한 리소스 그룹에서 런타임에 다른 API 앱을 검색하는 API가 있었습니다. 이는 마이크로 서비스 패턴을 구현하는 아키텍처에 특히 유용합니다. 이 기능은 직접 지원되지 않지만 사용 가능한 여러 옵션이 있습니다.

1. Azure 리소스 관리자 API를 검색에 사용합니다.
2. Azure API 관리를 앱 서비스에서 호스트되는 API 앞에 둡니다. Azure API 관리는 외부에 노출되며 내부 토폴로지가 변경된 경우에도 안정적인 외부 연결 url을 제공할 수 있습니다.
3. 사용자 고유의 검색 API 앱을 빌드하고, 시작 시 다른 API 앱을 검색 앱에 등록합니다.
4. 배포 시 다른 API 앱의 끝점과 함께 모든 API 앱(및 클라이언트)의 앱 설정을 채웁니다. API 앱이 url을 제어하므로 이는 템플릿 배포에 표시됩니다.

## 다음 단계

자세한 내용은 [API 앱 설명서 섹션](https://azure.microsoft.com/documentation/services/app-service/api/)의 문서를 참조하세요. API 앱에 대한 새 모델을 반영하도록 업데이트되었습니다. 또한 포럼에서 마이그레이션에 대한 추가 지침 또는 세부 정보를 확인할 수 있습니다.

- [MSDN 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureAPIApps)
- [스택 오버플로](http://stackoverflow.com/questions/tagged/azure-api-apps)

<!---HONumber=AcomDC_0316_2016-->