<properties
	pageTitle="Azure 포털에서 PowerApps API 속성 변경 또는 업데이트 | Microsoft Azure"
	description="PowerApps API의 사용자 지정 아이콘 추가, XML 정책 업데이트, 또는 Swagger 정의 업데이트"
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

# 기존 API 및 속성 업데이트

앱 서비스 환경에 등록하는 API는 본질적으로 백 엔드 서비스에 대한 프록시입니다. API를 만들고 난 후에 속성을 변경할 수 있습니다. 예를 들어 다음을 수행할 수 있습니다.

- API에 대한 사용자 지정 아이콘을 추가합니다.
- API에 사용되는 백 엔드를 보호하는 방법을 변경합니다. 
- API의 표시 이름을 사용자에게 보다 친숙한 이름으로 업데이트합니다.


#### 시작하기 위한 필수 조건

- [PowerApps 엔터프라이즈](powerapps-get-started-azure-portal.md)에 등록합니다.
- [앱 서비스 환경](powerapps-get-started-azure-portal.md)을 만듭니다.
- 환경에 [API](powerapps-register-from-available-apis)를 등록합니다.

## 사용자 지정 아이콘을 추가하거나 사용자에게 친숙한 표시 이름을 추가합니다.

1. [Azure 포털](https://portal.azure.com)에서 API에 대한 블레이드를 엽니다.
2. **모든 설정**을 선택합니다
3. **설정**에서 **일반**을 선택합니다. ![][11]

일반적으로 다음 설정을 변경할 수 있습니다.

설정 | 설명
--- | ---
표시 이름 | 이 이름은 PowerApps에서 *사용 가능한 연결*을 나열할 때 사용됩니다. 기본적으로 API의 리소스 이름이 사용되며, 이것이 PowerApps 사용자에게 최고의 이름이 아닐 수 있습니다. 사용자에게 친숙한 표시 이름을 입력할 수 있습니다. 예를 들어 **새로운 고객 주문**이나 **판매 내역 보기**로 이름을 지정할 수 있습니다.  
아이콘 URL | API에 대한 사용자 지정 아이콘을 추가할 수 있습니다. 아이콘은 PowerApps에서 *사용 가능한 연결* 및 *내 연결*을 나열할 때 사용됩니다. 기본적으로 다음과 같은 아이콘이 사용됩니다. <br/><br/>![][12] <br/><br/>사용자 지정 아이콘을 사용하는 경우에는:<br/><ul><li>아이콘에 대한 URL을 공개적으로 액세스할 수 있어야 합니다.</li><li>.png 또는 .svg 파일을 사용할 수 있습니다. png 파일을 사용하는 경우, 40 x 40 픽셀을 사용하는 것이 좋습니다.</li></ul>
URL 구성표 | API에서 지원할 구성표를 선택합니다. **HTTP**, **HTTPS**, 또는 **HTTP 및 HTTPS**를 선택할 수 있습니다. 기본적으로 HTTP 및 HTTPS를 사용하도록 설정됩니다. <br/><br/>앱 서비스 환경은 백 엔드 구성을 기반으로 구성표를 자동으로 구성합니다. 따라서 추가적으로 구성할 내용이 있으면 백 엔드 서비스를 변경하거나 개발합니다. 
백 엔드 서비스 인증 | 앱 서비스 환경에 백 엔드 서비스를 등록한 후에는 클라이언트가 사용자의 API만을 사용하여 호출하도록 백 엔드를 보호하는 것이 좋습니다. 백 엔드가 배포되는 위치에 따라서 다음과 같은 옵션을 사용할 수 있습니다.:<br/><br/><ul><li><strong>Only accessible via this API</strong>(이 API를 통해서만 액세스): 이 옵션은 백 엔드가 앱 서비스 환경에 표시되는 경우에만 사용이 가능합니다. 이 옵션을 선택하면 백 엔드의 모든 호스트 이름을 표시합니다. API 프록시 역시 동일한 앱 서비스 환경에서 실행 중이므로, 백 엔드에 액세스할 수 있습니다.</li><li><strong>HTTP basic authentication</strong>(HTTP 기본 인증): 이 옵션은 백 엔드가 표시되는 위치와 상관없이 사용할 수 있습니다. 이 옵션을 선택하면 사용자 이름 및 암호를 입력합니다. 프록시에서 백 엔드를 호출하면, HTTP 기본 인증을 사용하여 HTTP 인증 헤더에 사용자 이름과 암호를 전달합니다. 최종적으로, 백 엔드 서비스는 입력된 사용자 이름과 암호를 확인(인증)해야 합니다.<br/><br/>ASP.NET Web API 2에서 HTTP 기본 인증을 구현하는 내용에 대해 자세히 알아보려면 [ASP.NET Web API 2의 인증 필터](http://www.asp.net/web-api/overview/security/authentication-filters)(영문)을 참조하세요.</li></ul>


## API의 Swagger 업데이트

1. [Azure 포털](https://portal.azure.com)에서 API에 대한 블레이드를 엽니다.
2. **모든 설정**을 선택합니다
3. **설정**에서 **API 정의**를 선택합니다. ![][13]

**Swagger 2.0**은 지원되는 API 정의 형식입니다. 현재 API 정의는 포함되어 있는 JSON 편집기에 있습니다. 새 JSON 파일을 업로드하거나 인라인으로 편집할 수 있습니다. 변경 내용을 **저장**한 후에 API 정의에 대한 오류를 비롯하여, 오류가 있으면 이 블레이드에 표시됩니다.

- Swagger 2.0에 대해 자세히 알아보려면 [공식 Swagger 웹 사이트](http://swagger.io)(영문)를 참조하세요.
- API를 개발하는 경우 Swagger 2.0을 구하는 방법에 대해 알아보려면 아래 항목을 참조하세요.  
	- [Azure 앱 서비스에서 ASP.NET API 앱 만들기](../app-service-dotnet-create-api-app.md)
	- [Azure 앱 서비스에서 Java API 앱 빌드 및 배포](../app-service-api-java-api-app.md)
	- [Azure 앱 서비스에서 Node.js API 앱 빌드 및 배포](../app-service-api-nodejs-api-app.md)
	- [Swashbuckle 생성 API 정의 사용자 지정](../app-service-api-dotnet-swashbuckle-customize.md)
- PowerApps에 대한 Swagger 2.0 사용의 모범 사례에 대해 알아보려면 [PowerApps용 API 개발](powerapps-develop-api.md)을 참조하세요.

## API의 XML 정책 업데이트

1. [Azure 포털](https://portal.azure.com)에서 API에 대한 블레이드를 엽니다.
2. **모든 설정**을 선택합니다
3. **설정**에서 **정책**을 선택합니다. ![][14]

이 정책은 [Azure API 관리](https://azure.microsoft.com/services/api-management/)에서 지원되는 정책과 동일합니다. 현재 정책은 포함된 XML 편집기에 있습니다. 새 XML 파일을 업로드하거나 인라인으로 편집할 수 있습니다. 변경 내용을 **저장**한 후에 API 정책에 대한 문제를 비롯한 오류가 있으면 이 블레이드에 표시됩니다.

[Azure API 관리의 정책](../api-management/api-management-howto-policies.md)은 정책 구성 및 이해에 대해 자세히 알아볼 수 있는 좋은 자료입니다.


## 요약 및 다음 단계
API를 만든 후에는, 아래 문서의 단계를 사용하여 설정을 변경하고 일부 설정을 사용자 지정할 수 있습니다.

다음은 PowerApps에 대한 자세한 내용을 확인할 수 있는 몇 가지 관련 항목 및 리소스입니다.

- [AAD 보호 백 엔드에 연결하도록 API 구성](powerapps-configure-apis-aad.md)
- [PowerApps용 API 개발](powerapps-develop-api.md)

[11]: ./media/powerapps-configure-apis/api-settings-general.png
[12]: ./media/powerapps-configure-apis/api-default-icon.png
[13]: ./media/powerapps-configure-apis/api-settings-api-definition.png
[14]: ./media/powerapps-configure-apis/api-settings-policy.png

<!---HONumber=AcomDC_0316_2016-->