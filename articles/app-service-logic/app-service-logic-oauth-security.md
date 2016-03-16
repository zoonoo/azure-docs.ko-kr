<properties
	pageTitle="SaaS 커넥터 및 API 앱의 OAUTH 보안 | Azure"
	description="Azure 앱 서비스에서 커넥터 및 API 앱의 OAUTH 보안 알아보기, 마이크로 서비스 아키텍처, saas"
	services="app-service\logic"
	documentationCenter=""
	authors="MandiOhlinger"
	manager="dwrede"
	editor="cgronlun"/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/18/2016"
	ms.author="mandia"/>


# SaaS 커넥터의 OAUTH 보안에 대해 알아보기

>[AZURE.NOTE] 이 문서 버전은 논리 앱 2014-12-01-미리 보기 스키마 버전에 적용됩니다.

Facebook, Twitter, DropBox 등 많은 SaaS(Software as a Service)는 사용자가 OAUTH 프로토콜을 사용하여 인증해야 합니다. 사용자가 논리 앱에서 SaaS 커넥터를 사용할 때 논리 앱 프로그램 디자이너에서 "인증"을 클릭하는 간소화된 사용자 환경이 제공됩니다. 사용자가 **인증**하면 로그인 후(아직 하지 않은 경우) 사용자를 대신하여 SaaS 서비스에 연결할 수 있도록 동의해 달라는 메시지가 나타납니다. 요청에 동의하고 인증한 후에 논리 앱에서 이러한 SaaS 서비스에 액세스할 수 있습니다.

## 사용자 고유의 SaaS 앱 만들기
이 간소화된 환경이 가능한 이유는 이전에 이러한 SaaS 서비스에서 응용 프로그램을 만들고 등록했기 때문입니다. 사용자가 고유의 응용 프로그램을 등록하여 사용하려는 경우가 있습니다. 예를 들어 [SaaS 커넥터 API 앱을 배포](../app-service-api/app-service-api-connnect-your-app-to-saas-connector.md)하는 경우처럼 사용자 지정 응용 프로그램에서 이러한 SaaS 커넥터를 사용하려는 경우에 이 방법이 필요합니다. 이 예에서는 DropBox 커넥터를 사용하지만 OAUTH를 사용하는 모든 커넥터의 프로세스가 동일합니다.

심지어 논리 앱에서도 기본적으로 제공되는 응용 프로그램 대신 사용자 고유의 응용 프로그램을 사용할 수 있습니다. "인증" 단추를 눌러도 연결되지 않으면 사용자 고유의 응용 프로그램을 만들 수 있습니다. 다음은 Twitter 커넥터에서 사용자 고유의 응용 프로그램을 만드는 단계입니다.

1. Azure Preview 포털에서 Twitter 커넥터를 엽니다. **찾아보기** > **API 앱**으로 이동합니다. Twitter 커넥터를 선택합니다. ![][1]

2. **설정** > **인증**을 선택합니다. ![][2]

3. **리디렉션 URI** 값을 복사합니다. ![][3]

4. [Twitter](http://apps.twitter.com)로 이동한 후 **새 앱 만들기**로 이동합니다. Twitter 커넥터에서 복사한 **리디렉션 URL** 값을 **콜백 URI** 속성에 붙여넣습니다. ![][4]
5. Twitter 앱이 생성되면 **키 및 액세스 토큰**을 선택합니다. 이러한 값을 복사합니다.
6. Twitter 커넥터 인증 설정에서, 이러한 값을 **클라이언트 ID** 및 **클라이언트 암호** 속성에 붙여넣습니다. ![][5]  
7. 커넥터 설정을 저장합니다.  

이제 논리 앱에서 사용자의 커넥터를 사용할 수 있습니다. 논리 앱에서 이 커넥터를 사용하면 커넥터가 기본 응용 프로그램 대신 사용자 고유의 응용 프로그램을 사용합니다.

> [AZURE.NOTE] 이전에 앱을 인증했더라도 다시 인증해야 할 수 있습니다.


<!--Image references-->
[1]: ./media/app-service-logic-oauth-security/TwitterConnector.png
[2]: ./media/app-service-logic-oauth-security/Authentication.png
[3]: ./media/app-service-logic-oauth-security/RedirectURI.png
[4]: ./media/app-service-logic-oauth-security/TwitterApp.png
[5]: ./media/app-service-logic-oauth-security/TwitterKeys.png

<!---HONumber=AcomDC_0224_2016-->