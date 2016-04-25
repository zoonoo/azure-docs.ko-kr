<properties
	pageTitle="논리 흐름 및 PowerApps에서 사용자 지정 API를 사용하는 방법 | Microsoft Azure"
	description="사용자 지정 API란, OAuth 공급자 사용, Swagger를 사용하여 PowerApps 및 논리 흐름에서 사용자 지정 API 추가"
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
   ms.date="04/12/2016"
   ms.author="mandia"/>

# 사용자 지정 API란?

사용자 지정 API는 가져와서 PowerApps 및 논리 흐름으로 사용할 수 있는 RESTful API입니다. [OpenAPI][1] 표준을 따르는 잘 문서화된 사양을 사용할 수 있는 경우 이러한 API는 어디서나 호스트할 수 있습니다.

## 시작에 필요한 항목

- PowerApps를 사용하는 모든 사용자가 사용자 지정 API를 사용할 수 있습니다. PowerApps Enterprise는 필요하지 않습니다. 
- Swagger 파일(.json) 및 사용자 지정 API에 대한 아이콘이 필요합니다. 이 항목에서는 Swagger 파일을 만드는 몇 가지 옵션을 제공합니다. 아이콘은 원하는 어떤 이미지든 사용할 수 있습니다.


## 인증

다음 인증 메커니즘 중 하나를 사용할 수 있습니다.

- 기본 인증 
- OAuth 2.0: 다음 목록은 사용자 지정 API로 사용할 수 있는 지원되는 모든 OAuth 2.0 공급자입니다(더 많이 지원할 예정).	

	- Azure Active Directory
	- Box
	- Dropbox
	- Facebook
	- Google
	- Instagram
	- OneDrive
	- SalesForce
	- Slack
	- Yammer

> [AZURE.NOTE] API 키 인증에 대한 지원은 곧 제공될 예정입니다.


OpenAPI(Swagger) 문서에서 인증 형식을 지정하는 방법에 대한 자세한 내용은 [OpenAPI 사양][1]을 참조하세요.

API 끝점이 인증되지 않은 액세스를 허용하는 경우 OpenAPI(Swagger) 파일에서 ```securityDefintions``` 개체를 제거해야 합니다. 다음 예제에서는 다음 ```securityDefintions``` 개체를 모두 제거합니다.

```
  "securityDefinitions": {
    "AAD": {
      "type": "oauth2",
      "flow": "implicit",
      "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
      "scopes": []
    }
  },
```

> [AZURE.TIP] .json 파일에서 의견을 추가할 수 없습니다. 모든 텍스트는 데이터의 일부로 간주됩니다.

### 인증 예제
* AAD 인증을 사용하는 [Azure Resource Manager](powerapps-azure-resource-manager-tutorial.md)
* AAD 인증을 사용하는 [Azure 웹앱](powerapps-web-api-tutorial.md)

## 사용자 지정 API 등록

### 1단계: Swagger 파일 만들기

다음을 포함하여 **모든** API 끝점에서 Swagger 파일을 만들 수 있습니다.

- 게시되어 공개적으로 사용할 수 있는 모든 API. [Spotify][2], [Uber][3], [Slack][4], [Rackspace][5] 등이 있습니다. 
- 만들어서 웹앱을 배포할 수 있는 모든 클라우드 서비스(예: AWS(Amazon Web Services), Heroku, Azure 웹앱, Google Cloud)에 배포하는 API  
- 네트워크나 컴퓨터에 배포된 API(인터넷에서 공개적으로 사용할 수 있는 경우)

Swagger 파일을 만들 때 .json 파일이 만들어집니다. 이 .json 파일을 보관합니다.

#### Swagger 파일 만들기 도움말 보기

- Swagger 파일을 처음 만들거나 이전에 Swagger 파일을 만든 적이 없는 경우 [Swagger 시작][6]이 좋은 리소스입니다. 
 
- 사용자 고유의 API를 만들어 Azure에 배포하고 이 새 API와 다른 Swagger 파일을 만들려면 [Web API 자습서](powerapps-web-api-tutorial.md)를 사용하는 것이 좋습니다. 이 자습서는 작동되는 Swagger 파일을 제공합니다. 또한 GitHub의 [Hello World 예제][7]도 제공합니다.

- Swagger 파일의 유효성을 검사하려면 [Swagger 편집기][8]를 사용합니다. .json 데이터를 붙여넣으면 유효성 검사가 자동으로 발생합니다.

- PowerApps 및 논리 흐름을 사용하도록 Swagger 문서를 사용자 지정하려면 [Swagger 정의 사용자 지정](powerapps-how-to-swagger.md)을 참조하세요.

### 2단계: 사용자 지정 API에 연결 추가
이제 사용자 지정 API에 대한 Swagger 파일(.json 파일)이 생성되었으므로 PowerApps에 연결을 추가합니다. 사용자 지정 API에 대한 아이콘도 필요합니다.

1. PowerApps [웹 포털][9]로 이동하여 회사 계정으로 로그인합니다.  

	> [AZURE.NOTE] 현재 사용자 지정 API는 PowerApps 웹 포털에서만 사용할 수 있습니다. PowerApps 클라이언트에서는 사용할 수 없습니다.

2. **연결**을 선택한 다음 **연결 추가**를 선택합니다. ![](./media/powerapps-register-custom-api/createnewconnection.png "사용자 지정 API 만들기")

3. **사용자 지정 AP 추가**를 선택합니다. ![](./media/powerapps-register-custom-api/connecttocustomapi.png "사용자 지정 API 만들기") .json 및 아이콘 파일을 포함하여 API의 속성을 추가합니다. 그런 후 **다음**을 선택합니다.

	|속성|설명|
|---|---|
|이름|사용자 지정 API의 이름을 입력합니다. 샘플 Web API인 경우 이름을 **MySampleWebAPI**로 지정할 수 있습니다.|
|Swagger API 정의|Swagger에서 생성된 .json 파일을 찾습니다.|
|API 아이콘 업로드|아이콘 파일을 찾습니다.|
|설명|사용자 지정 API에 대한 설명을 입력합니다. 샘플 Web API인 경우 **샘플 Web API 자습서**를 입력할 수 있습니다.|

4. 모든 인증 속성을 입력합니다. ```securityDefintions``` 개체에서 .json 파일이 OAuth2 인증을 사용하는 경우 다음 값을 묻는 메시지가 표시됩니다.

	|속성|설명|
|---|---|
|클라이언트 ID|지원되는 OAuth ID 공급자(이 항목에 있음) 중 하나를 사용하는 경우 클라이언트 ID가 제공됩니다. 이 클라이언트 ID를 입력합니다.
|클라이언트 암호|선택한 ID 공급자의 클라이언트 암호를 입력합니다.|  

	이 항목의 **인증 예제**에서는 이러한 OAuth 속성의 예제를 제공합니다.

	.json 파일이 ```securityDefintions``` 개체를 사용하지 않는 경우 추가 값은 필요하지 않을 수 있습니다.

5. **만들기**를 선택합니다. 이제 사용자 지정 API가 **사용 가능한 연결**에 표시됩니다.

	![](./media/powerapps-register-custom-api/mycustomapi.png "사용할 수 있는 연결")


> [AZURE.TIP] Swagger 파일이 유효성 검사에 실패한 경우 추가 문자가 있을 수 있습니다. 예를 들어 웹 사이트를 포함하여 거의 모든 데이터는 따옴표로 묶어야 합니다. 따라서 `https://mywebapi.mywebsite.com`이 따옴표 밖에 있는 경우 파일은 유효성 검사에 실패합니다.


### 3단계: 논리 흐름 및 PowerApp에 사용자 지정 API 추가
이제 PowerApp 및 논리 흐름으로 사용자 지정 API를 사용할 준비가 되었습니다. 이 섹션에서는 사용자 지정 날씨 API를 사용합니다.

#### 논리 흐름에 사용자 지정 API 추가
이 단계에서는 사용자 지정 API를 추가하는 방법을 보여 주는 매우 간단한 논리 흐름을 만듭니다. 자세한 설명은 [논리 흐름 시작][10]을 참조하세요.

1. PowerApps [웹 포털][9]에서 **홈** 탭을 선택합니다.
2. **논리 흐름 만들기**에서 **시작**을 선택합니다. 
3. 이 창에는 몇 가지 일반적인 시나리오를 사용하는 여러 논리 흐름 템플릿이 이미 만들어져 있습니다. 이러한 템플릿 중 하나를 사용하여 사용자 지정 API를 추가할 수 있습니다. 또는 **Create from blank(새로 만들기)**를 선택하여 처음부터 논리 흐름을 만들 수 있습니다.  

	사용자 지정 API를 추가하는 가장 빠른 방법은 **Create from blank(새로 만들기)**를 선택하는 것입니다. 그러면 다음 논리 흐름이 열립니다. ![](./media/powerapps-register-custom-api/createfromblank.png "논리 흐름의 시작")

4. **되풀이**를 선택하고 실행 빈도를 1분으로 설정합니다. ![](./media/powerapps-register-custom-api/logicrecurrence.png "되풀이 선택")

5. + 기호(![](./media/powerapps-register-custom-api/flowplussign.png))를 선택하고 **작업 추가**를 선택합니다. 목록에 사용자 지정 API가 나열됩니다. ![](./media/powerapps-register-custom-api/logicflow.png "사용자 지정 API")

다음 단계는 API가 수행할 수 있는 작업에 의해 결정됩니다. 날씨 예제에서는 API가 현재 온도를 가져온 다음 Office 365를 사용하여 메일을 보냅니다.

![](./media/powerapps-register-custom-api/logicflowexample.png "날씨 예제")



#### PowerApp에 사용자 지정 API 추가
이 단계에서는 사용자 지정 API를 추가하는 방법을 보여 주는 매우 간단한 PowerApp을 만듭니다. 자세한 설명은 [데이터에서 앱 만들기][11]를 참조하세요.

> [AZURE.NOTE] 현재 사용자 지정 API는 PowerApps 웹 포털에서만 사용할 수 있습니다. PowerApps 클라이언트에서는 사용할 수 없습니다.

1. PowerApps [웹 포털][9]에서 **새 PowerApp**을 선택합니다. ![](./media/powerapps-register-custom-api/newpowerapp.png "새 PowerApp 선택")  
2. 브라우저에 새 탭이 열립니다. 이 새 탭에서 빈 PowerApp이 자동으로 만들어집니다. **데이터에 연결**을 선택합니다. ![](./media/powerapps-register-custom-api/blankpowerapp.png "데이터에 대한 연결 선택")  
3. **콘텐츠** 탭에서 **데이터 원본**을 선택합니다. ![](./media/powerapps-register-custom-api/datasources.png "데이터에 대한 연결 선택")  
4. 새 화면에서 **내 연결** 아래 사용자 지정 API를 선택합니다. ![](./media/powerapps-register-custom-api/screencustomapi.png "사용자 지정 API 선택")  
5. **데이터 원본 추가**를 선택합니다.

추가하면 기능 도구 모음, 텍스트 상자 등에서 사용자 지정 API를 사용할 수 있습니다. 예를 들어 기능 도구 모음에서 **MySampleWebAPI**를 입력하여 사용할 수 있는 기능을 볼 수 있습니다. [Office 365에서 데이터 표시][12]는 Office 365 API를 사용하는 예제입니다.


## 사용자 지정 API 공유
또한 사용자는 사용자 지정 API를 서로 공유할 수 있습니다. 사용자 지정 API를 추가하면 **연결** 탭을 선택하고 **사용자 지정 API**를 선택한 다음 공유 아이콘을 선택합니다.

![](./media/powerapps-register-custom-api/sharecustomapi.png "사용자 지정 API 공유")

> [AZURE.NOTE] 사용자의 조직에 있는 다른 사용자와만 사용자 지정 API를 공유할 수 있습니다.

## 할당량 및 제한

- PowerApps 계정에 최대 5개의 사용자 지정 API를 만들 수 있습니다. 사용자와 공유되는 사용자 지정 API는 이 할당량에 포함되지 않습니다.
- 사용자 지정 API에서 만든 각 연결에 대해 사용자는 분당 최대 500개를 요청할 수 있습니다.
- 사용자 지정 API를 삭제하면 API에 만들어진 모든 연결이 삭제됩니다. 

사용자 지정 API에 대한 질문이나 의견은 [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com)으로 메일을 보내세요.

<!--Reference links in article-->
[1]: https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#securityDefinitionsObject
[2]: https://developer.spotify.com/
[3]: https://developer.uber.com/
[4]: https://api.slack.com/
[5]: http://docs.rackspace.com/
[6]: http://swagger.io/getting-started/
[7]: https://github.com/OAI/OpenAPI-Specification/wiki/Hello-World-Sample
[8]: http://editor.swagger.io/#/
[9]: https://web.powerapps.com
[10]: https://powerapps.microsoft.com/tutorials/get-started-logic-flow/
[11]: https://powerapps.microsoft.com/tutorials/get-started-create-from-data/
[12]: https://powerapps.microsoft.com/tutorials/show-office-data/

<!---HONumber=AcomDC_0413_2016-->