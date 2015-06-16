<properties 
	pageTitle=".NET 클라이언트의 Azure 앱 서비스에서 API 앱 사용" 
	description="앱 서비스 SDK를 사용하여 .NET 클라이언트에서 API 앱을 사용하는 방법을 알아봅니다." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
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

# .NET 클라이언트의 Azure 앱 서비스에서 API 앱 사용 

## 개요

이 자습서에서는 앱 서비스 SDK를 사용하여 **공용(익명)** 또는 **공용(인증됨)** 액세스 수준에 대해 구성된 [API 앱](app-service-api-apps-why-best-platform.md)을 호출하는 코드를 작성하는 방법을 보여 줍니다. 이 문서에서 다루는 예제 시나리오는 다음과 같습니다.

- 콘솔 응용 프로그램에서 **공용(익명)** API 앱 호출
- Windows 데스크톱 응용 프로그램에서 **공용(인증됨)** API 앱 호출 

이러한 자습서 섹션은 각각 독립적입니다. 따라서 첫 번째 섹션의 단계를 완료하지 않고도 두 번째 시나리오에 대한 지침을 따를 수 있습니다.

## 필수 조건

이 자습서에서는 프로젝트를 만들고 Visual Studio에서 프로젝트에 코드를 추가하는 방법 및 [Azure Preview포털에서 API 앱을 관리하는](../app-service-api-apps-manage-in-portal.md) 방법을 잘 알고 있다고 가정합니다.

이 문서의 프로젝트 및 코드 샘플은 다음 문서에서 생성, 배포 및 보호하는 API 앱 프로젝트를 기반으로 합니다.

- [API 앱 만들기](app-service-dotnet-create-api-app.md)
- [API 앱 배포](app-service-dotnet-deploy-api-app.md)
- [API 앱 보호](../app-service-dotnet-add-authentication.md)

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

이 자습서에는 Azure SDK for .NET 버전 2.6 이상이 필요합니다.

## 콘솔 응용 프로그램에서 인증되지 않은 호출

이 섹션에서는 콘솔 응용 프로그램 프로젝트를 만들고 이 프로젝트에 인증이 필요 없는 API 앱을 호출하는 코드를 추가합니다.

### API 앱 설정 및 프로젝트 만들기

1. [API 앱 배포](app-service-dotnet-deploy-api-app.md)에 따라 Azure 구독에서 API 앱에 ContactsList 샘플 프로젝트를 배포합니다(아직 배포하지 않은 경우).

	이 자습서에서는 Visual Studio 게시 대화 상자에서 액세스 수준을 포털의 **공용(익명)**과 동일한 **모든 사용자가 사용 가능**으로 설정하도록 안내합니다. 그러나 그 다음에 [API 앱 보호](../app-service-dotnet-add-authentication.md) 자습서를 수행하여 액세스 수준이 **공용(인증됨)**으로 설정된 경우에는 다음 단계의 지시에 따라 액세스 수준을 변경해야 합니다.

2. [Azure Preview 포털](https://portal.azure.com/)의 호출한 API 앱에 대한 **API 앱** 블레이드에서 **설정 > 응용 프로그램 설정**으로 이동하여 **액세스 수준**을 **공용(익명)**으로 설정합니다.

	![](./media/app-service-api-dotnet-consume/setpublicanon.png)
 
2. Visual Studio에서 콘솔 응용 프로그램 프로젝트를 만듭니다.
 
### 앱 서비스 SDK에서 생성된 클라이언트 코드 추가

3. **솔루션 탐색기**에서 프로젝트(솔루션이 아님)를 마우스 오른쪽 단추로 클릭하고 **추가 > Azure API 앱 클라이언트**를 선택합니다. 

	![](./media/app-service-api-dotnet-consume/03-add-azure-api-client-v3.png)
	
3. **Azure API 앱 클라이언트 추가** 대화 상자에서 **Azure API 앱에서 다운로드**를 클릭합니다.

5. 드롭다운 목록에서 호출할 API 앱을 선택합니다.

7. **확인**을 클릭합니다.

	![생성 화면](./media/app-service-api-dotnet-consume/04-select-the-api-v3.png)

	마법사가 API 메타데이터 파일을 다운로드하고 API 앱을 호출하기 위한 형식화된 인터페이스를 생성합니다.

	![생성 발생](./media/app-service-api-dotnet-consume/05-metadata-downloading-v3.png)

	코드 생성이 완료되고 나면 **솔루션 탐색기**에 API 앱의 이름으로 만들어진 새 폴더가 표시됩니다. 이 폴더에는 클라이언트 클래스 및 데이터 모델을 구현하는 코드가 들어 있습니다.

	![생성 완료](./media/app-service-api-dotnet-consume/06-code-gen-output-v3.png)

### API 앱을 호출하는 코드 추가

API 앱을 호출하려면 다음 예제와 같이 클라이언트 개체를 만들고 이 개체에서 메서드를 호출하기만 하면 됩니다.

        var client = new ContactList();
        var contacts = client.Contacts.Get();

1. *Program.cs*를 열고 `Main` 메서드 안에 다음 코드를 추가합니다.

		var client = new ContactsList();
		
		// Send GET request.
		var contacts = client.Contacts.Get();
		foreach (var c in contacts)
		{
		    Console.WriteLine("{0}: {1} {2}",
		        c.Id, c.Name, c.EmailAddress);
		}
		
		// Send POST request.
		client.Contacts.Post(new Models.Contact
		{
		    EmailAddress = "lkahn@contoso.com",
		    Name = "Loretta Kahn",
		    Id = 4
		});
		Console.WriteLine("Finished");
		Console.ReadLine();

3. Ctrl+F5를 눌러 응용 프로그램을 실행합니다.

	![Generation Complete](./media/app-service-api-dotnet-consume/consoleappoutput.png)

## Windows 데스크톱 응용 프로그램에서 인증된 호출

이 섹션에서는 Windows 데스크톱 응용 프로그램 프로젝트를 만들고 이 프로젝트에 인증이 필요한 API 앱을 호출하는 코드를 추가합니다. 이 코드는 Oauth 2 *서버 인증 흐름*을 구현합니다. 즉, 클라이언트 응용 프로그램이 아니라 API 앱 게이트웨이가 인증 공급자로부터 토큰을 받습니다.

Azure API 앱은 클라이언트 인증 흐름도 지원합니다. 클라이언트 흐름 인증 시나리오는 나중에 이 자습서에 추가될 예정입니다.

### API 앱 설정 및 프로젝트 만들기

1. [API 앱 보호](../app-service-dotnet-add-authentication.md) 자습서에 따라 **공용(인증)** 액세스 수준으로 API 앱을 설정합니다.

1. Visual Studio에서 Windows Forms 데스크톱 프로젝트를 만듭니다.

2. 폼 디자이너에서 다음 컨트롤을 추가합니다.

	* 단추 컨트롤
	* 텍스트 상자 컨트롤
	* 웹 브라우저 컨트롤

3. 텍스트 상자 컨트롤을 여러 줄로 설정합니다.

	폼 모양은 다음 예제와 유사합니다.

	![](./media/app-service-api-dotnet-consume/form.png)

### 앱 서비스 SDK에서 생성된 클라이언트 코드 추가

3. **솔루션 탐색기**에서 프로젝트(솔루션이 아님)를 마우스 오른쪽 단추로 클릭하고 **추가 > Azure API 앱 클라이언트**를 선택합니다. 

3. **Azure API 앱 클라이언트 추가** 대화 상자에서 **Azure API 앱에서 다운로드**를 클릭합니다.

5. 드롭다운 목록에서 호출할 API 앱을 선택하고 **확인**을 클릭합니다.

### API 앱을 호출하는 코드 추가

5. Azure Preview 포털에서 API 앱 게이트웨이의 URL을 복사합니다. 이 값은 다음 단계에서 사용됩니다.

	![](./media/app-service-api-dotnet-consume/gatewayurl.png)

4. *Form1.cs* 소스 코드에서 GATEWAY_URL 값을 이전 단계에서 복사한 값으로 바꿔 `Form1()` 생성자 앞에 다음 코드를 추가합니다. 후행 슬래시(/)를 포함해야 합니다.

		private const string GATEWAY_URL = "https://resourcegroupnameb4f3d966dfa43b6607f30.azurewebsites.net/";
		private const string URL_TOKEN = "#token=";

4. 폼 디자이너에서 단추를 두 번 클릭하여 클릭 처리기를 추가한 후 처리기 메서드에서 다음과 같이 게이트웨이의 로그인 URL로 이동하는 코드를 추가합니다.

		webBrowser1.Navigate(string.Format(@"{0}login/[authprovider]", GATEWAY_URL));

	"[authprovider]"를 게이트웨이에서 구성한 ID 서비스 공급자(예: "aad", "twitter", "google", "microsoftaccount" 또는 "facebook")에 대한 코드로 바꿉니다. 예:

		webBrowser1.Navigate(string.Format(@"{0}login/aad", GATEWAY_URL));

7. 웹 브라우저 컨트롤에 대한 `DocumentCompleted` 이벤트 처리기를 추가하고 처리기 메서드에 다음 코드를 추가합니다.

		if (e.Url.AbsoluteUri.IndexOf(URL_TOKEN) > -1)
		{
		    var encodedJson = e.Url.AbsoluteUri.Substring(e.Url.AbsoluteUri.IndexOf(URL_TOKEN) + URL_TOKEN.Length);
		    var decodedJson = Uri.UnescapeDataString(encodedJson);
		    var result = JsonConvert.DeserializeObject<dynamic>(decodedJson);
		    string userId = result.user.userId;
		    string userToken = result.authenticationToken;
		
		    var appServiceClient = new AppServiceClient(GATEWAY_URL);
		    appServiceClient.SetCurrentUser(userId, userToken);
		
		    var contactsListClient = appServiceClient.CreateContactsList();
		    var contacts = contactsListClient.Contacts.Get();
		    foreach (Contact contact in contacts)
		    {
		        textBox1.Text += contact.Name + " " + contact.EmailAddress + System.Environment.NewLine;
		    }
		    //appServiceClient.Logout();
		    //webBrowser1.Navigate(string.Format(@"{0}login/aad", GW_URL));
		}

	추가한 코드는 사용자가 웹 브라우저 컨트롤을 사용하여 로그인한 후 실행됩니다. 로그인에 성공하면 응답 URL에 사용자 ID와 암호가 포함됩니다. 코드는 URL에서 이러한 값을 추출하여 앱 서비스 클라이언트 개체에 제공한 다음 해당 개체를 사용하여 API 앱 클라이언트 개체를 만듭니다. 그런 다음 이 API 앱 클라이언트 개체에서 메서드를 호출하여 API를 호출할 수 있습니다.

8. Ctrl+F5를 눌러 응용 프로그램을 실행합니다.

9. 단추를 클릭한 후 브라우저 컨트롤이 로그인 페이지를 표시하면 API 앱을 호출할 수 있는 권한이 있는 사용자의 자격 증명을 입력합니다.

	Azure에서 사용자를 인증하면 응용 프로그램이 API 앱을 호출하고 응답을 표시합니다.

	![](./media/app-service-api-dotnet-consume/formaftercall.png)

## 다음 단계

이 문서에서는 .NET 클라이언트에서 **공용(인증됨)** 및 **공용(익명)** 액세스 수준으로 설정된 API 앱을 사용하는 방법을 보여 주었습니다.

.NET 클라이언트에서 API 앱을 호출하는 코드에 대한 추가 예제를 보려면 [Azure Cards](https://github.com/Azure-Samples/API-Apps-DotNet-AzureCards-Sample) 샘플 응용 프로그램을 다운로드하세요.

<!--HONumber=52-->
 