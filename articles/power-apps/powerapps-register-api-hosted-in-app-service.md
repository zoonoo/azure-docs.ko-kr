<properties
	pageTitle="PowerApps 엔터프라이즈의 앱 서비스 환경에서 호스트되는 API 개발 또는 만들기 | Microsoft Azure"
	description="Azure 포털의 앱 서비스 환경에서 호스트되는 사용자 지정 API를 등록하는 방법 알아보기"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="guayan"/>

# 앱 서비스 환경에서 호스트되는 API 등록
PowerApps는 클라우드 또는 온-프레미스 어디서나 호스트되는 기존 API를 지원하므로 이는 매우 강력합니다. 일부 시나리오에서 몇 가지 새 API를 개발하거나 만들 수 있습니다. 예를 들어 다음을 수행할 수 있습니다.

- 조직이 사용할 일부 새 기능을 구현합니다.
- 사용자가 해당 앱을 빌드할 때 더 나은 환경을 제공할 수 있도록 기존 기능 또는 데이터의 위에서 빌드합니다.

앱 서비스 환경에서 API를 호스트하면 [앱 서비스 환경](../app-service-app-service-environment-intro.md)에 있는 기존의 모든 기능을 활용할 뿐만 아니라 더 나은 통합 환경을 사용할 수 있습니다.

앱에서 이러한 API를 사용하려면 Azure 포털에서 API를 "등록"해야 합니다. 사용할 수 있는 옵션은 다음과 같습니다.

- [Microsoft 관리되는 API 또는 IT 관리되는 API](powerapps-register-from-available-apis.md)를 등록합니다.
- 앱 서비스 환경 내에서 호스트되는 API를 등록합니다.
- [Swagger 2.0 API 정의](powerapps-register-existing-api-from-api-definition.md)를 사용하여 등록합니다.

이 문서는 **앱 서비스 환경에서 호스트되는 API 등록** 방법을 보여 줍니다.

#### 시작하기 위한 필수 조건

- [PowerApps 엔터프라이즈](powerapps-get-started-azure-portal.md)에 등록합니다.
- [앱 서비스 환경](powerapps-get-started-azure-portal.md)을 만듭니다.


## 앱 서비스 환경에서 API 개발 및 배포

앱 서비스 환경에서 API를 개발하는 것은 간단합니다. 원하는 프로그래밍 언어를 선택하여 웹 API를 빌드한 다음 [Swagger 2.0](http://swagger.io)을 사용하여 API 정의를 설명합니다. 일부 사례:

- [Azure 앱 서비스에서 .NET 빌드 및 배포](../app-service-api-dotnet-get-started.md)
- [Azure 앱 서비스에서 Java API 앱 빌드 및 배포](../app-service-api-java-api-app.md)
- [Azure 앱 서비스에서 Node.js API 앱 빌드 및 배포](../app-service-api-nodejs-api-app.md)

Visual Studio에서 배포 및 소스 제어 시스템을 사용하여 지속적으로 배포하는 옵션뿐만 아니라 앱 서비스 환경으로 웹 API를 배포하는 옵션도 있습니다. [Azure 앱 서비스에서 웹앱 배포](../web-sites-deploy.md)는 좋은 리소스입니다.

## 앱 서비스 환경에서 사용자 지정 API 등록

API가 앱 서비스 환경에 배포된 후 다음 단계를 사용하여 등록합니다.

1. [Azure 포털](https://portal.azure.com/)에서 **PowerApps**를 선택한 다음 **API 관리**를 선택합니다. ![][11]
2. API 관리에서 **추가**를 선택합니다. ![][12]  
3. **API 추가**에서 API 속성을 입력합니다.  

	- **이름**에서 API에 대한 이름을 입력합니다. 입력하는 이름은 API의 런타임 URL에 포함됩니다. 조직에서 의미 있고 고유한 이름을 만듭니다.	
	- **원본**에서 **Import from APIs hosted in App Service Environment**(앱 서비스 환경에서 호스트되는 API에서 가져오기)를 선택합니다. ![][13]
4. **API hosted in App Service Environment**(앱 서비스 환경에서 호스트되는 API)에서 가져오려는 API를 선택합니다. 이 목록은 앱 서비스 환경에서 **apiDefinition.url** 속성이 구성된 모든 웹앱, API 앱 및 모바일 앱을 보여 줍니다. API를 가져오려면 이 속성을 사용하여 노출된 Swagger 2.0 API 정의를 사용합니다. API를 등록할 때 이 URL을 공개적으로 액세스할 수 있는지 확인합니다. ![][14]
5. **추가**를 선택하여 이 단계를 완료합니다.

> [AZURE.TIP]API를 등록하는 경우 API를 앱 서비스 환경에 등록하는 것입니다. 앱 서비스 환경에 있는 경우 동일한 앱 서비스 환경 내의 다른 앱에서 사용할 수 있습니다.

## 요약 및 다음 단계
이 항목에서는 앱 서비스 환경에서 호스트되는 API를 등록하는 방법을 살펴보았습니다. 다음은 PowerApps에 대한 자세한 내용을 확인할 수 있는 몇 가지 관련 항목 및 리소스입니다.

- [API 구성](../powerapps-configure-apis.md)
- [새 API 추가](../powerapps-register-from-available-apis.md)

<!--Reference-->
[11]: ./media/powerapps-register-api-hosted-in-app-service/registered-apis-part.png
[12]: ./media/powerapps-register-api-hosted-in-app-service/add-api-button.png
[13]: ./media/powerapps-register-api-hosted-in-app-service/add-api-blade.png
[14]: ./media/powerapps-register-api-hosted-in-app-service/add-api-select-from-ase.png

<!---HONumber=AcomDC_1203_2015-->