<properties
	pageTitle="자습서: ASP.Net 웹앱을 사용하여 PowerApps 및 논리 흐름에서 사용자 지정 API 만들기 | Microsoft Azure"
	description="PowerApps 및 논리 흐름에서 사용자 지정 API 만들기에 대한 ASP.Net 웹앱 자습서입니다."
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="sunaysv"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/12/2016"
   ms.author="mandia"/>

# 자습서: PowerApps 및 논리 흐름에 대한 사용자 지정 AAD로 보호된 Web API 만들기

이 자습서에서는 ASP.Net Web API를 만들어 Azure 웹앱에 호스트하고 간편한 인증을 통해 AAD 인증을 사용하도록 설정한 다음 Web API를 PowerApps 및 논리 흐름에 등록합니다.

>[AZURE.IMPORTANT] 이 항목은 [자습서: PowerApps 및 논리 흐름에 대한 사용자 지정 AAD로 보호된 Web API 만들기](https://powerapps.microsoft.com/tutorials/customapi-web-api-tutorial/)의 powerapps.microsoft.com으로 이동되었습니다. 최신 버전에 대한 PowerApps로 이동하세요. 이 Azure 링크가 보관됩니다.

## 시작에 필요한 항목

* Azure 구독
* PowerApps 계정
* Visual Studio 2013 이상

## 1단계: WebAPI를 만들고 Azure에 배포
1. Visual Studio를 열고 새 C# ASP.NET 웹 응용 프로그램을 만듭니다.![](./media/powerapps-web-api-tutorial/newwebapp.png "새 웹앱")

2. 다음 화면에서 Web API 템플릿을 선택하고 **인증 없음**을 선택합니다. ![](./media/powerapps-web-api-tutorial/noauth.png "권한 부여 없음")

	>[AZURE.IMPORTANT] "인증 없음"으로 인증을 설정해야 합니다.

3. 프로젝트를 만들 때 리소스에 대한 Web API를 빌드해야 합니다. 이 자습서에서는 Web API 빌드를 자세히 설명하지 않습니다.

4. 다음으로, Web API에 대한 Swagger 파일을 생성합니다. 이 작업은 __패키지 관리자 콘솔__을 열고 __Swashbuckle__을 설치하여 쉽게 수행할 수 있습니다. ![](./media/powerapps-web-api-tutorial/swashbuckle-console.png "Swashbuckle 콘솔")

5. 설치 및 사용하도록 설정되면 다음 Swagger 문서 및 UI 끝점을 각각 찾습니다. **<your-root-url>/swagger/docs/v1**

 	**<your-root-url>/swagger**

6. Web API에 익숙해지면 Azure에 게시합니다. Visual Studio에서 게시하려면 **빌드**로 이동하고 **게시**를 선택합니다.

7. ***https://\<azure-webapp-url>/swagger/docs/v1***로 이동하여 swagger json을 추출합니다.

	> [AZURE.IMPORTANT] 중복 operationid가 있는 swagger 문서는 유효하지 않습니다. 샘플 C# 템플릿을 사용하는 경우 operation-id "Values\_Get"이 두 번 반복됩니다. 인스턴스 하나를 "Value\_Get"으로 변경합니다.


이 자습서에서 사용되는 swagger를 [여기][6]에서 다운로드할 수 있습니다. 사용하기 전에 주석을 바꾸거나 제거해야 합니다. 주석은 `//`로 시작합니다.

## 2단계: AAD 인증 설정

이 자습서는 Azure의 AAD 응용 프로그램을 만드는 방법을 알고 있는 개발자를 대상으로 합니다. AAD 응용 프로그램을 만드는 방법에 대한 자세한 내용은 [Azure Resource Manager 자습서](powerapps-azure-resource-manager-tutorial.md)를 참조하세요. 이 자습서에서는 두 AAD 응용 프로그램이 필요합니다.

1. 첫 번째 AAD 응용 프로그램은 Web API를 보호하는 데 사용됩니다. **webAPI**로 이름을 지정합니다.
2. 두 번째 AAD 응용 프로그램은 사용자 지정 API 등록을 보호하고 첫 번째 앱으로 보호되는 Web API에 대한 위임된 액세스를 가져오는 데 사용됩니다. **webAPI-customAPI**로 이름을 지정합니다.
3. **webAPI**의 경우 다음 구성을 사용합니다.

  1. 로그온 URL: ***https://login.windows.net***
  2. App-ID Uri: ***https://\<your-root-url>***(일반적으로 Azure에 배포된 웹 사이트의 URL)
  3. 회신 Uri: ***https://\<your-root-url>/.auth/login/aad/callback***
  
	>[AZURE.IMPORTANT] 나중에 이 앱의 클라이언트 ID가 필요하므로 메모해 둡니다.

4. **webAPI-customAPI**의 경우 다음 구성을 사용합니다.
  
  1. 로그온 URL: **https://login.windows.net**
  2. App-ID Uri: ***고유 URL이 될 수 있음***
  3. 회신 URL: ***https://msmanaged-na.consent.azure-apim.net/redirect***
  4. webAPI에 대한 위임된 액세스를 갖도록 사용 권한을 추가합니다.
  5. 나중에 이 앱의 클라이언트 ID도 필요하므로 메모해 둡니다.
  6. 키를 생성하고 안전한 위치에 보관합니다. 이 키는 나중에 필요합니다.

>[AZURE.IMPORTANT] 두 앱은 동일한 디렉터리에 있어야 합니다.

## 3단계: 웹앱에서 간편한 인증 설정

1. [Azure 포털](https://portal.azure.com)에 로그인하고 이 항목 **1단계**에서 배포한 웹앱으로 이동합니다.
2. **설정**에서 **"인증/권한 부여"**를 선택합니다.
3. **앱 서비스 인증**을 켜고 **Azure Active Directory**를 선택합니다. 다음 블레이드에서 **Express**를 선택합니다.
4. **기존 AD 앱 선택**을 클릭하고 2단계에서 만든 첫 번째 AAD 응용 프로그램을 선택합니다. 이 경우 **webAPI**를 선택합니다.

웹앱에 대한 AAD 인증을 설정해야 합니다.

## 4단계:사용자 지정 API 설정 

1. `securityDefintions` 개체 및 웹앱에서 사용되는 AAD 인증을 입력하려면 swagger를 약간 수정해야 합니다. 다음 코드 줄을 추가합니다.

	```javascript
  "host": "<your-root-url>",
  "schemes": [
    "https"						//Change scheme to https 
  ],
  "securityDefinitions": {
    "AAD": {
      "type": "oauth2",
      "flow": "implicit",
      "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
      "scopes": []
    }
  },
	```

2. PowerApps [웹 포털][1]로 이동하여 사용자 지정 API를 추가합니다. [논리 흐름 및 PowerApps에서 사용자 지정 API 사용](powerapps-register-custom-api.md)에서 단계를 설명합니다.

3. Swagger를 업로드하면 webAPI에 AAD 인증을 사용하는지 마법사가 자동으로 검색합니다.

4. 사용자 지정 API에 대한 AAD 인증을 구성합니다.

  1. 클라이언트 ID: 이 항목 **2단계**의 4.5에서 메모한 ***webAPI-CustomAPI의 클라이언트 ID***
  2. 비밀: 이 항목 **2단계**의 4.6에서 보관한 ***webPI-CustomAPI의 키***
  3. 로그인 url: ***https://login.windows.net***
  4. ResourceUri: 이 항목 **2단계**의 3.4에서 메모한 ***webAPI의 클라이언트 ID***

5. **만들기**를 선택하고 사용자 지정 API의 연결을 만듭니다. 설치가 올바르게 되었으면 성공적으로 로그인할 수 있습니다.

PowerApps 및 논리 흐름을 만드는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [Connect to Office 365, Twitter, and Microsoft Translator(Office 365, Twitter 및 Microsoft Translator에 연결)][5]
- [Show data from Office 365(Office 365에서 데이터 표시)][4]
- [데이터에서 앱 만들기][3]
- [Get started with logic flows(논리 흐름 시작)][2]

질문이나 의견은 [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com)으로 메일을 보내세요.

<!--Reference links in article-->
[1]: https://web.powerapps.com
[2]: https://powerapps.microsoft.com/tutorials/get-started-logic-flow/
[3]: https://powerapps.microsoft.com/tutorials/get-started-create-from-data/
[4]: https://powerapps.microsoft.com/tutorials/show-office-data/
[5]: https://powerapps.microsoft.com/tutorials/powerapps-api-functions/
[6]: http://pwrappssamples.blob.core.windows.net/samples/webAPI.json

<!---HONumber=AcomDC_0713_2016-->