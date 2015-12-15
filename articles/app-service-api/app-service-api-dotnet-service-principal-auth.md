<properties
	pageTitle="Azure 앱 서비스의 API 앱에 대한 서비스 주체 인증 | Microsoft Azure"
	description="서비스 간 시나리오에 대해 Azure 앱 서비스에서 API 앱을 보호하는 방법에 알아봅니다."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="11/28/2015"
	ms.author="tdykstra"/>

# Azure 앱 서비스의 API 앱에 대한 서비스 주체 인증

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## 개요

이 자습서에서는 Azure 앱 서비스의 인증 및 권한 부여 기능을 사용하여 API 앱을 보호하는 방법 및 서비스 계정 대신 보호된 API 앱을 사용하는 방법을 보여 줍니다. 자습서에 나와 있는 인증 공급자는 Azure Active Directory이며 클라이언트와 API는 API 앱에서 실행 중인 ASP.NET Web API입니다.

## Azure 앱 서비스의 인증 및 권한 부여

이 자습서에 사용된 인증 기능에 대한 개요는 시리즈의 이전 자습서인 [Azure 앱 서비스의 API 앱에 대한 인증 및 권한 부여](app-service-api-dotnet-get-started.md)을 참조하세요.

## 이 자습서를 진행하는 방법

이 자습서는 [API 앱 및 ASP.NET 시작 시리즈의 첫 번째 자습서](app-service-api-dotnet-get-started.md)에서 다운로드하여 만든 API 앱에 대한 응용 프로그램 예제를 기반으로 작성되었습니다.

## CompanyUsers.API 샘플 프로젝트

[ContactsList 응용 프로그램 예제](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list)에서 CompanyUsers.API 프로젝트는 하드 코드된 연락처 목록을 반환하는 Get 메서드 하나가 포함된 간단한 Web API 프로젝트입니다. 서비스 간 시나리오를 보여 주기 위해 ContactsList.API의 Get 메서드는 CompanyContacts.API의 Get 메서드를 호출하고 연락처를 다시 추가하여 자체 데이터 저장소로 가져온 후 조합된 목록을 반환합니다.

다음은 CompanyUsers.API의 Get 메서드입니다.

		public async Task<IEnumerable<Contact>> Get()
		{
		    var contacts = new Contact[]{
		                new Contact { Id = 1, EmailAddress = "nancy@contoso.com", Name = "Nancy Davolio"},
		                new Contact { Id = 2, EmailAddress = "alexander@contoso.com", Name = "Alexander Carson"}
		            };
		
		    return contacts;
		}


ContactsList.API의 Get 메서드는 다음과 같습니다. 이 코드는 CompanyContacts.API를 호출하고 반환되는 항목에 결과를 추가하는 방법을 보여 줍니다. 일부 코드는 명확한 설명을 위해 생략되어 있습니다.

		private async Task<IEnumerable<Contact>> GetContacts()
		{
		    var contacts = await _storage.Get(FILENAME);
		
		    var contactsList = contacts.ToList<Contact>();
		    using (var client = CompanyContactsAPIClientWithAuth())
		    {
		        var results = await client.Contacts.GetAsync();
		        foreach (Contact c in results)
		        {
		            contactsList.Add(c);
		        }
		    }
		
		    return contactsList;
		}

위의 코드에서 `CompanyContactsAPIClientWithAuth()`가 반환한 클라이언트 개체는 생성된 클라이언트 코드를 기반으로 하지만 HTTP 요청에 권한 부여 토큰을 추가합니다.

		private static CompanyContactsAPI CompanyContactsAPIClientWithAuth()
		{
		    var client = new CompanyContactsAPI(new Uri(ConfigurationManager.AppSettings["CompanyContactsAPIUrl"]));
		    client.HttpClient.DefaultRequestHeaders.Authorization =
		        new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);
		    return client;
		}

## Azure에서 API 앱을 만들고 이 앱에 CompanyContacts.API 프로젝트 배포

1. **솔루션 탐색기**에서 CompanyContacts.API 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다.

3.  **웹 게시** 마법사의 **프로필** 단계에서 **Microsoft Azure 앱 서비스**를 클릭합니다.

	![](./media/app-service-api-dotnet-service-principal-auth/selectappservice.png)

4. Azure 계정에 로그인(아직 로그인하지 않은 경우)하거나 자격 증명을 새로 고칩니다(만료된 경우).

4. **앱 서비스** 대화 상자에서 사용할 Azure **구독**을 선택하고 **새로 만들기**를 클릭합니다.

	![](./media/app-service-api-dotnet-service-principal-auth/clicknew.png)

3. **앱 서비스 만들기** 대화 상자의 **호스팅** 탭에서 **형식 변경**을 클릭한 다음 **API 앱**을 클릭합니다.

4. *azurewebsites.net* 도메인에서 고유한 **API 앱 이름**을 입력합니다.

6. **리소스 그룹** 드롭다운에서 이러한 자습서에 사용한 리소스 그룹을 선택합니다.

4. **앱 서비스 계획** 드롭다운에서 이러한 자습서에 사용한 계획을 선택합니다.

7. **만들기**를 클릭합니다.

	![](./media/app-service-api-dotnet-service-principal-auth/createappservice.png)

	Visual Studio에서 API 앱을 만들고 새 API 앱에 필요한 모든 설정이 있는 게시 프로필을 만듭니다.

8. **웹 게시** 마법사의 **연결** 탭에서 **게시**를 클릭합니다.

	![](./media/app-service-api-dotnet-service-principal-auth/conntab.png)

	Visual Studio에서 프로젝트를 새 API 앱에 배포하고 해당 API 앱의 URL로 브라우저를 엽니다. "만들기 성공" 페이지가 브라우저에 표시됩니다.

9. 브라우저를 닫습니다.

## ContactsList.API 프로젝트에서 생성된 클라이언트 코드 업데이트

ContactsList.API 프로젝트에는 생성된 클라이언트 코드가 이미 있지만 이를 삭제하고 사용자 고유의 API 앱에서 다시 생성합니다.

1. Visual Studio **솔루션 탐색기**의 ContactsList.API 프로젝트에서 *CompanyContactsAPI* 폴더를 삭제합니다.

2. ContactsList.API 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가 > REST API 클라이언트**를 클릭합니다.

3. **REST API 클라이언트 추가** 대화 상자에서 **Microsoft Azure API 앱에서 다운로드**를 클릭한 다음 **찾아보기**를 클릭합니다.

8. **앱 서비스** 대화 상자에서 이 자습서에 사용하는 리소스 그룹을 확장한 다음 방금 만든 API 앱을 선택합니다.

	목록에 API 앱이 보이지 않으면 API 앱을 만들 때 웹앱에서 API 앱으로 형식을 변경하는 단계를 실수로 생략했을 수 있습니다. 이 경우 앞서 수행한 단계를 반복하여 새 API 앱을 만들면 됩니다. 포털로 이동하여 웹앱을 먼저 삭제하지 않는 경우 API 앱에 다른 이름을 선택해야 합니다.

10. **확인**을 클릭합니다.

9. **REST API 클라이언트 추가** 대화 상자에서 **확인**을 클릭합니다.

	Visual Studio에서 API 앱의 이름을 딴 폴더를 만들고 클라이언트 클래스를 생성합니다.

## ContactsList.API에서 코드를 업데이트하고 프로젝트 배포

CompanyContacts.API를 호출하는 ContactsList.API의 코드는 이전 자습서에서 주석 처리되었습니다. 이 섹션에서는 이 코드의 주석을 제거하고 앱을 배포합니다.

1. ContactsList.API 프로젝트에서 *Controllers/ContactsController.cs*를 엽니다.

2. 생성된 클라이언트 클래스를 사용하여 권한 부여 토큰을 추가하는 코드에서 `ContactsController` 클래스의 위쪽에 있는 클래스 이름 `CompanyContactsAPI`를 API 앱에서 생성된 클래스 이름으로 바꿉니다.

	예를 들어 API 앱의 이름이 CompanyContactsAPI3인 경우 코드는 다음과 같습니다.

		 private static CompanyContactsAPI3 CompanyContactsAPIClientWithAuth()
		 {
		     var client = new CompanyContactsAPI3(new Uri(ConfigurationManager.AppSettings["CompanyContactsAPIUrl"]));
		     client.HttpClient.DefaultRequestHeaders.Authorization =
		         new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);
		     return client;
		 }
 
4. `Get` 메서드에서 CompanyContacts.API를 호출하는 코드 블럭의 주석 처리를 제거합니다.

		using (var client = CompanyContactsAPIClientWithAuth())
		{
		    var results = await client.Contacts.GetAsync();
		    foreach (Contact c in results)
		    {
		        contactsList.Add(c);
		    }
		}

2. ContactsList.API 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다.

	이전에 사용한 게시 프로필로 **웹 게시** 마법사가 열립니다.

3. **웹 게시** 마법사에서 **게시**를 클릭합니다.

	Visual Studio에서 프로젝트를 배포하고 API 앱 기본 URL로 브라우저 창을 엽니다. 이 브라우저 창을 닫습니다.

## Azure에서 새 API 앱에 대한 인증 및 권한 부여 설정

1. [Azure 포털](https://portal.azure.com/)에서 CompanyContacts.API 프로젝트에 대해 이 자습서에서 만든 API 앱의 API 앱 블레이드로 이동하여 **설정**을 클릭합니다.

2. **기능** 섹션을 찾아서 **인증/권한 부여**를 클릭합니다.

3. **인증/권한 부여** 블레이드에서 **설정**을 클릭합니다.

4. **요청이 인증되지 않은 경우에 수행할 동작** 드롭다운 목록에서 **Azure Active Directory를 사용하여 로그인**을 선택합니다.

5. **인증 공급자** 아래에서 **Azure Active Directory**를 클릭합니다.

6. **Azure Active Directory 설정** 블레이드에서 **Express**를 클릭합니다.

	AAD 테넌트에 AAD 응용 프로그램이 자동으로 만들어집니다. 나중에 클라이언트 ID를 가져오기 위해 클래식 Azure 포털로 이동할 경우 선택할 것이므로 새 AAD 응용 프로그램의 이름을 적어 둡니다.

7. **확인**을 클릭합니다.

10. **인증/권한 부여** 블레이드에서 **저장**을 클릭합니다.
 
11. [Azure 클래식 포털](https://manage.windowsazure.com/)에서 **Azure Active Directory**로 이동합니다.

12. **디렉터리** 탭에서 AAD 테넌트를 클릭합니다.

14. **응용 프로그램 > 회사 소유 응용 프로그램**을 클릭한 다음 확인 표시를 클릭합니다.

15. 응용 프로그램 목록에서 API 앱에 대한 인증을 활성화할 때 만들어진 응용 프로그램의 이름을 클릭합니다.

16. **Configure**를 클릭합니다.

17. 자습서의 이후 단계에서 값을 복사하고 붙여 넣으며 페이지에서 값을 업데이트할 수 있도록 이 페이지를 열어 둡니다.

## ContactsList.API 프로젝트 코드를 실행하는 API 앱의 설정 업데이트

3. 다른 브라우저 창에서 [클래식 Azure 포털](https://manage.windowsazure.com/)로 이동한 후 ContactsList.API API 앱에 대해 만든 AAD 응용 프로그램의 **구성** 탭으로 이동합니다.

5. **키** 아래의 **기간 선택** 드롭다운 목록에서 **1년**을 선택합니다.

6. **Save**를 클릭합니다.

	![](./media/app-service-api-dotnet-service-principal-auth/genkey.png)

7. 키 값을 복사합니다.

	![](./media/app-service-api-dotnet-service-principal-auth/genkeycopy.png)

1. 다른 브라우저 창에서 [Azure 포털](https://portal.azure.com/)로 이동한 후 ContactsList.API 프로젝트를 배포한 API 앱에 대한 API 앱 블레이드로 이동합니다. (이것은 호출하는 API 앱으로, 호출되는 API 앱인 ContactsList.API가 아니며, CompanyContacts.API가 아닙니다.)

2. **설정 > 응용 프로그램 설정**을 클릭합니다.

3. **앱 설정** 섹션에서 "ida:ClientSecret"라는 키를 추가하고 값 필드에 방금 만든 키를 붙여 넣습니다.

3. "Ida: ClientId"라는 키를 추가하고 값 필드에 동일한 AAD **구성** 페이지의 클라이언트 ID를 붙여 넣습니다.

4. "Ida: Authority"라는 키를 추가하고 값 필드에 "https://login.windows.net/{테넌트}";를 입력합니다. 예를 들어 "https://login.windows.net/contoso.onmicrosoft.com"입니다.

3. 클래식 Azure 포털에서 CompanyContacts.API API 앱에 대해 만든 AAD 응용 프로그램의 **구성** 탭으로 이동합니다.

4. 클라이언트 ID를 복사합니다.

3. Azure 포털 **응용 프로그램 설정** 블레이드의 **앱 설정** 섹션에서 ida:Resource라는 키를 추가하고 값 필드에 방금 만든 클라이언트 ID를 붙여 넣습니다.

4. "CompanyContactsAPIUrl"이라는 키를 추가하고 값 필드에 "https://{api 앱 이름}.azurewebsites.net"을 입력합니다. 예를 들어 "https://companycontactsapi.azurewebsites.net"입니다.

6. 저장을 클릭합니다.

	![](./media/app-service-api-dotnet-service-principal-auth/appsettings.png)

## Azure에서 테스트

1. ContactsList.Angular.AAD 프로젝트를 배포한 웹앱의 URL로 이동합니다.

2. **연락처** 탭을 클릭한 다음 로그인합니다.

	CompanyContacts.API API 앱에서 검색한 연락처가 추가된 연락처 페이지가 나타납니다.

	![](./media/app-service-api-dotnet-service-principal-auth/contactspagewithdavolio.png)

이전 자습서와 마찬가지로 localhost SSL URL로 Visual Studio 프로젝트를 설정할 수 있으며 응용 프로그램을 로컬로 실행할 수 있습니다. 이 경우 Azure(클라이언트 ID, 클라이언트 암호 등)에서 실행을 위해 Azure에 저장한 설정을 Web.config 파일에 저장할 수 있습니다. 그러나 클라이언트 암호와 같은 중요한 정보가 포함된 Web.config 파일을 소스 제어에 체크 인하지 않도록 주의하세요. 자세한 내용은 [ASP.NET 및 Azure 앱 서비스에 암호 및 기타 중요한 데이터 배포를 위한 모범 사례](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure)를 참조하세요.

## 브라우저 액세스로부터 API 앱 보호

이 자습서에서는 Azure 포털에서 Express 옵션을 사용하여 서비스 주체 인증을 사용하여 액세스할 API 앱에 대한 AAD 인증을 설정합니다. 기본적으로 앱 서비스는 사용자가 브라우저에서 API 앱의 URL로 이동한 후 로그인하도록 하는 방식으로 새 AAD 응용 프로그램을 구성합니다. 즉, 서비스 주체뿐만 아니라 최종 사용자도 API에 액세스할 수 있습니다. 서비스 주체만 API에 액세스할 수 있도록 하려면 AAD 응용 프로그램에서 **회신 URL**을 변경하여 API 앱의 기본 URL을 다르게 하여 브라우저 액세스를 차단할 수 있습니다.

### 브라우저 액세스가 작동하는지 확인

1. 새 브라우저 창에서 CompanyContacts.API 프로젝트에 대해 만든 API 앱의 HTTPS URL로 이동합니다.

	브라우저에 로그인 화면이 표시됩니다.
	
2. AAD 테넌트의 사용자에 대한 자격 증명으로 로그인합니다.

3. 브라우저에는 API 앱의 "만들기 성공" 화면이 표시됩니다.

	Swagger UI를 활성화한 경우 `/swagger` URL로 이동하여 API를 호출할 수 있습니다. `/api/contacts/get`을 URL에 추가하여 브라우저에서 API를 호출할 수 있습니다.

### 브라우저 액세스 사용 안 함

1. CompanyContacts.API 프로젝트에 대해 만든 AAD 응용 프로그램에 대한 클래식 포털 **구성** 탭에서 **회신 URL** 필드의 값을 변경하여 API 앱의 URL이 아닌 유효한 URL이 되도록 할 수 있습니다.
 
2. **Save**를 클릭합니다.

### 브라우저 액세스가 더 이상 작동하지 않는지 확인

1. 새 브라우저 창에서 API 앱의 URL로 다시 이동합니다.

2. 이렇게 하라는 메시지가 표시되면 로그인합니다.

3. 로그인에 성공하지만 오류 페이지가 표시됩니다.

	서비스 주체 토큰을 사용하여 계속 API 앱에 액세스할 수 있지만 AAD 테넌트의 사용자는 로그인할 수 없으며 브라우저에서 API에 액세스할 수 없습니다.

## 다음 단계

이 자습서는 API 앱 시작 시리즈의 마지막 자습서입니다. 이 섹션에서는 API 앱 작동 방법을 자세히 알아보기 위한 추가 제안을 제공합니다.

* 앱 서비스 앱을 배포하는 다른 방법

	Visual Studio를 사용하거나 [소스 제어 시스템](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control)에서 [배포를 자동화](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery)하여 웹앱에 웹 프로젝트를 배포하는 다른 방법에 대한 개요는 [Azure 웹앱을 배포하는 방법](web-sites-deploy.md)을 참조하세요.

	Visual Studio에서 배포를 자동화하는 데 사용할 수 있는 Windows PowerShell 스크립트를 생성할 수도 있습니다. 자세한 내용은 [자동화(Azure에서 실제 클라우드 앱 빌드)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything)(영문)를 참조하십시오.

* 앱 서비스 앱의 문제를 해결하는 방법

	Visual Studio는 Azure 로그가 생성될 때 실시간으로 보기 쉽도록 만드는 기능을 제공합니다. 원격으로 Azure에서 디버그 모드로 실행할 수도 있습니다. 자세한 내용은 [Visual Studio에서 Azure 웹앱 문제 해결](web-sites-dotnet-troubleshoot-visual-studio.md)을 참조하세요.

* 사용자 지정 도메인 이름 및 SSL을 추가하는 방법

	SSL 및 사용자만의 도메인(예: contoso.azurewebsites.net가 아닌 www.contoso.com 사용)을 사용하는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.

	* [Azure 앱 서비스에서 사용자 지정 도메인 이름 구성](web-sites-custom-domain-name.md)
	* [Azure 웹 사이트에 HTTPS 사용](web-sites-configure-ssl-certificate.md)

<!---HONumber=AcomDC_1210_2015-->