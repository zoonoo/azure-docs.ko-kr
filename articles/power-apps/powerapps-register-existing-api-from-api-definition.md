<properties
	pageTitle="PowerApps 엔터프라이즈의 API에서 Swagger 2.0 API 정의 만들기 | Microsoft Azure"
	description="기존 API에서 만들어진 Swagger 2.0 API 정의에서 API를 등록하는 방법 알아보기"
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

# Swagger 2.0 API 정의에서 API 등록  
대부분의 조직에는 사용자가 자신의 앱에서 사용할 수 있는 몇 가지 기존 API가 이미 있습니다. 앱에서 이러한 API를 사용하려면 Azure 포털에서 API를 "등록"해야 합니다. 사용할 수 있는 옵션은 다음과 같습니다.

- [Microsoft 관리되는 API 또는 IT 관리되는 API](powerapps-register-from-available-apis.md)를 등록합니다.
- [앱 서비스 환경](powerapps-register-api-hosted-in-app-service.md) 내에서 호스트되는 API를 등록합니다.
- Swagger 2.0 API 정의를 사용하여 등록합니다.

이 문서는 기존 API에서 만든 **Swagger 2.0 API 정의 등록** 방법을 보여 줍니다.

#### 시작하기 위한 필수 조건

- [PowerApps 엔터프라이즈](powerapps-get-started-azure-portal.md)에 등록합니다.
- [앱 서비스 환경](powerapps-get-started-azure-portal.md)을 만듭니다.

## Swagger 2.0 API 정의를 사용하여 기존 API 등록

이러한 기존 API를 등록하는 것은 매우 쉽습니다. 단계는 다음과 같습니다.

1. 기존 API에 대해 [Swagger 2.0](http://swagger.io) API 정의를 만듭니다. PowerApps는 Swagger 2.0을 API 정의 형식으로 사용합니다. [Swagger 2.0 웹 사이트](http://swagger.io)에서 참조된 도구를 사용하여 Swagger 2.0 API 정의를 쉽게 작성할 수 있습니다. 주의할 사항:  

	- ``host`` 속성은 기존 API의 실제 끝점을 가리켜야 합니다. 구성표 또는 모든 하위 경로를 사용하지 마세요. 예를 들어 ``api.contoso.com``을 입력합니다. <br/><br/>
	- ``basePath`` 속성은 기존 API 끝점의 하위 경로(있는 경우)를 나열합니다. 슬래시 ``/``로 시작합니다. 예를 들어 ``/purchaseorderapi``를 입력합니다.

2. 앱 서비스 환경에서 기존 API에 안전하게 액세스할 수 있는지 확인합니다. <br/><br/> a) 인터넷에서 편하게 API에 액세스할 수 있는 경우 앱 서비스 환경과 기존 API 간에 HTTP 기본 액세스 인증을 설정할 수 있습니다. 방법을 보려면 [여기](powerapps-configure-apis.md)로 이동합니다. <br/><br/> b) 조직의 네트워크 내에서 API를 유지하려는 경우 앱 서비스 환경에서 가상 네트워크를 설정하면 조직의 네트워크에 안전하게 액세스할 수 있습니다. 방법을 보려면 [여기](../app-service-app-service-environment-intro.md)로 이동합니다.

3. [Azure 포털](https://portal.azure.com/)에서 **PowerApps**를 선택한 다음 **API 관리**를 선택합니다. ![][11]
4. API 관리에서 **추가**를 선택합니다. ![][12]
5. **API 추가**에서 API 속성을 입력합니다.  

	- **이름**에서 API에 대한 이름을 입력합니다. 입력하는 이름은 API의 런타임 URL에 포함됩니다. 조직에서 의미 있고 고유한 이름을 만듭니다.
	- **원본**에서 **Swagger 2.0에서 가져오기**를 선택합니다.

6. **API 정의(Swagger 2.0)**에서 Swagger 2.0 API 정의 파일을 업로드합니다. ![][13]
7. **추가**를 선택하여 이 단계를 완료합니다.

> [AZURE.TIP]API를 등록하는 경우 API를 앱 서비스 환경에 등록하는 것입니다. 앱 서비스 환경에 있는 경우 동일한 앱 서비스 환경 내의 다른 앱에서 사용할 수 있습니다.

## 요약 및 다음 단계

이 항목에서는 Swagger 2.0 API 정의에서 API를 등록하는 방법을 살펴보았습니다. 다음은 PowerApps에 대한 자세한 내용을 확인할 수 있는 몇 가지 관련 항목 및 리소스입니다.

- [API 구성](powerapps-configure-apis.md)
- [새 API 추가](powerapps-register-from-available-apis.md)

<!--References-->
[11]: ./media/powerapps-register-existing-api-from-api-definition/registered-apis-part.png
[12]: ./media/powerapps-register-existing-api-from-api-definition/add-api-button.png
[13]: ./media/powerapps-register-existing-api-from-api-definition/add-api-blade.png

<!---HONumber=AcomDC_1203_2015-->