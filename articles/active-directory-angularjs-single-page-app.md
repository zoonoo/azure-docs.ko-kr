<properties 
	pageTitle="Azure AD를 사용하여 AngularJS 단일 페이지 앱을 빌드하는 방법" 
	description="JavaScript용 ADAL(Active Directory 인증 라이브러리)을 사용하여, ASP.NET Web API 백 엔드로 구현되고 CORS를 사용하여 다른 ASP.NET Web API를 호출하는 AngularJS 기반 단일 페이지 앱의 보안을 설정하는 방법을 보여 줍니다." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="terrylan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.devlang="javascript" 
	ms.topic="hero-article" 
	ms.tgt_pltfrm="na" 
	ms.workload="identity" 
	ms.date="04/01/2015" 
	ms.author="justinha"/>


# Azure AD를 사용하여 AngularJS 단일 페이지 앱을 빌드하는 방법 

이 자습서에서는 JavaScript용 ADAL(Active Directory 인증 라이브러리)을 사용하여, ASP.NET Web API 백 엔드로 구현되고 CORS를 사용하여 다른 ASP.NET Web API를 호출하는 AngularJS 기반 단일 페이지 앱의 보안을 설정하는 방법을 보여 줍니다. 이 자습서에 사용된 코드 샘플을 보려면 GitHub의 [AzureADSamples/SinglePageApp-AngularJS-DotNet](https://github.com/AzureADSamples/SinglePageApp-AngularJS-DotNet)(영문)을 참조하세요.

JavaScript용 ADAL은 오픈 소스 라이브러리입니다.  배포 옵션, 소스 코드 및 기여 정보는 [ADAL JS 리포지토리](https://github.com/AzureAD/azure-activedirectory-library-for-js)(영문)를 참조하세요.

프로토콜이 이 시나리오 및 기타 시나리오에서 작동하는 방법에 대한 자세한 내용은 [Azure AD의 인증 시나리오](http://go.microsoft.com/fwlink/?LinkId=394414)를 참조하세요.

## 이 샘플을 실행하는 방법

간단하게 시작할 수 있습니다.  이 샘플을 실행하려면 다음이 필요합니다.

- Visual Studio 2013
- 인터넷 연결
- Azure 구독([무료 평가판](https://account.windowsazure.com/organization)이면 충분함)

모든 Azure 구독에는 연결된 Azure AD(Azure Active Directory) 테넌트가 있습니다. 이 샘플에서 사용하는 모든 Azure AD 기능은 무료로 사용할 수 있습니다.

## 이 리포지토리 복제 또는 다운로드

셸 또는 명령줄에서 다음 실행:
`git clone https://github.com/AzureADSamples/SinglePageApp-WebAPI-AngularJS-DotNet.git`

## Azure Active Directory 테넌트에 To Go API 서비스 등록

1. [Azure 관리 포털](https://manage.windowsazure.com)에 로그인합니다.
2. 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
3. 샘플 응용 프로그램을 등록할 디렉터리를 클릭합니다.
4. **응용 프로그램** 탭을 클릭합니다.
5. 서랍에서 **추가**를 클릭합니다.
6. **내 조직에서 개발 중인 응용 프로그램 추가**를 클릭합니다.
7. 응용 프로그램의 식별 이름을 입력하고(예: "To Go API") **웹 응용 프로그램** 및/또는 **Web API**를 선택한 후 **다음**을 클릭합니다.
8. 로그온 URL로는 샘플의 기준 URL(기본적으로 `https://localhost:44327/`임)을 입력합니다.
9. 앱 ID URI로는 `https://<your_directory_name>/ToGoAPI`를 입력하고 `<your_directory_name>`을 해당 Azure AD 디렉터리 이름으로 바꿉니다. 구성을 저장합니다.

모두 완료되었습니다!  다음 단계로 넘어가기 전에 API의 앱 ID URI를 찾아야 합니다.

1. 계속 Azure 포털에 있는 상태에서 응용 프로그램의 **구성** 탭을 클릭합니다.
2. 앱 ID URI 값을 찾아 클립보드에 복사합니다.

## Azure Active Directory 테넌트를 사용하도록 To Go API 구성

1. Visual Studio 2013에서 솔루션을 엽니다.
2. ToGoAPI 프로젝트에서 `web.config` 파일을 엽니다.
3. 앱 키 `ida:Tenant`를 찾아 값을 해당 Azure AD 테넌트 이름으로 바꿉니다.
4. 앱 키 `ida:Audience`를 찾아 값을 Azure 포털에서 복사한 앱 ID URI로 바꿉니다.
5. 또한 ToGoAPI 프로젝트에서 `Controllers/ToGoListController.cs` 파일을 엽니다.  `[EnableCors...]` 특성에 To Do SPA 클라이언트 위치를 입력합니다.  기본적으로 `https://localhost:44326`입니다.  후행 슬래시는 생략해야 합니다.
5. TodoSPA 프로젝트에서 `App/Scripts/App.js` 파일을 열고 `endpoints` 개체의 선언을 찾습니다.
6. To Go API 끝점 위치 매핑을 해당 리소스 식별자 또는 앱 ID URI에 입력합니다.   `endpoints` 개체의 속성 이름은 To Go API의 위치여야 합니다.  기본적으로  `https://localhost:44327/`. 입니다. 이 속성 값은 포털에서 복사한 앱 ID URI(예: `https://<your_tenant_name>/ToGoAPI`)여야 합니다.
8. 이 파일의 다른 구성 값은 조금 후에 살펴보겠습니다.
9. 또한 TodoSPA 프로젝트에서 `App/Scripts/toGoListSvc.js` 파일을 엽니다.  변수 `apiEndpoint`의 값을 To Go API의 위치로 바꿉니다.  기본적으로 `https://localhost:44327/`입니다.

## Azure Active Directory 테넌트에 To Do 단일 페이지 응용 프로그램 등록

1. 다시 [Azure 관리 포털](https://manage.windowsazure.com)에 로그인합니다.
2. 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
3. 샘플 응용 프로그램을 등록할 테넌트를 클릭합니다.
4. **응용 프로그램** 탭을 클릭합니다.
5. 서랍에서 **추가**를 클릭합니다.
6. **내 조직에서 개발 중인 응용 프로그램 추가**를 클릭합니다.
7. 응용 프로그램의 식별 이름을 입력하고(예: "To Do SPA") **웹 응용 프로그램 및/또는 Web API**를 선택한 후 **다음**을 클릭합니다.
8. 로그온 URL로는 샘플의 기준 URL(기본적으로 `https://localhost:44326/`임)을 입력합니다.
9. 앱 ID URI로는 `https://<your_directory_name>/ToDoSPA`를 입력하고 `<your_directory_name>`을 해당 Azure AD 디렉터리 이름으로 바꿉니다.
10. **다른 응용 프로그램에 대한 권한** 섹션에서 **응용 프로그램 추가**를 클릭합니다.  **표시** 드롭다운에서 **기타**를 선택하고 위쪽 확인 표시를 클릭합니다.  To Go API를 찾아 클릭하고 아래쪽 확인 표시를 클릭하여 응용 프로그램을 추가합니다.  **위임된 권한** 드롭다운에서 **To Go API 액세스**를 선택하고 구성을 저장합니다.

모두 완료되었습니다!  다음 단계로 넘어가기 전에 응용 프로그램의 클라이언트 ID를 찾아야 합니다.

1. 계속 Azure 포털에 있는 상태에서 응용 프로그램의 **구성** 탭을 클릭합니다.
2. 클라이언트 ID 값을 찾아 클립보드에 복사합니다.


## 응용 프로그램에 OAuth2 암시적 허용 사용

기본적으로 Azure AD에서 프로비전된 응용 프로그램은 OAuth2 암시적 허용을 사용하도록 설정되지 않습니다. 이 샘플을 실행하려면 명시적으로 옵트인(opt in)해야 합니다.

1. 이전 단계에서 브라우저는 계속 Azure 관리 포털을 표시하고 있어야 하며, 구체적으로 응용 프로그램 항목의 **구성** 탭을 표시하고 있어야 합니다.
2. 서랍의 **매니페스트 관리** 단추를 사용하여 응용 프로그램에 대한 매니페스트 파일을 다운로드하고 디스크에 저장합니다.
3. 텍스트 편집기에서 매니페스트 파일을 엽니다.  `oauth2AllowImplicitFlow` 속성을 검색합니다. 이 속성이  `false`로 설정되어 있음을 확인할 수 있습니다. 이를  `true`로 변경하고 파일을 저장합니다.
4. **매니페스트 관리** 단추를 사용하여 업데이트된 매니페스트 파일을 업로드합니다. 앱의 구성을 저장합니다.

## Azure Active Directory 테넌트를 사용하도록 To Do SPA 구성

1. Visual Studio 2013에서 솔루션을 엽니다.
2. TodoSPA 프로젝트에서 `web.config` 파일을 엽니다.
3. 앱 키 `ida:Tenant`를 찾아 값을 해당 Azure AD 디렉터리 이름으로 바꿉니다.
4. 앱 키 `ida:Audience`를 찾아 값을 Azure 포털에서 복사한 클라이언트 ID로 바꿉니다.
5. 또한 TodoSPA 프로젝트에서 `App/Scripts/App.js` 파일을 다시 한 번 열고 `adalAuthenticationServiceProvider.init(` 줄을 찾습니다.
6.  `tenant` 값을 Azure AD 디렉터리 이름으로 바꿉니다.
7.  `clientId` 값을 Azure 포털에서 복사한 클라이언트 ID로 바꿉니다.

## 샘플 실행

솔루션을 정리하고 솔루션을 다시 빌드한 후 실행합니다. 

오른쪽 위의 로그인 링크를 클릭하거나 **할 일 모음** 또는 **To Go List** 탭을 직접 클릭하여 로그인 환경을 트리거할 수 있습니다.  로그인하고 할 일 모음에 항목을 추가하고 사용자 계정을 제거한 후 다시 시작하여 샘플을 탐색합니다.  To Go List에 위치를 추가하고 CORS를 사용하여 To Go API에 대해 CRUD 작업을 수행합니다.

## Azure에 이 샘플을 배포하는 방법

Azure 웹 사이트에 To Do SPA 및 To Go API를 배포하려면 두 개의 웹 사이트를 만들고 각 웹 사이트에 프로젝트를 하나씩 게시한 후 IIS Express 대신 새 위치를 참조하도록 두 프로젝트를 모두 업데이트합니다.

### To Go API Azure 웹 사이트 만들기

1. [Azure 관리 포털](https://manage.windowsazure.com)에 로그인합니다.
2. 왼쪽 탐색 창에서 **웹 사이트**를 클릭합니다.
3. 왼쪽 아래에서 **새로 만들기**를 클릭하고 **계산** > **웹 사이트** > **사용자 지정 만들기**를 선택한 후 호스팅 계획 및 지역을 선택하고 웹 사이트에 이름을 지정합니다(예: togo-contoso.azurewebsites.net).  사용할 데이터베이스를 선택하거나 새로 만듭니다.  **웹 사이트 만들기**를 클릭합니다.
4. 웹 사이트를 만든 후 클릭하여 관리합니다.  이 단계 집합에서는 .publishsettings 파일을 다운로드하여 저장합니다.  소스 제어와 같은 다른 배포 메커니즘을 사용할 수도 있습니다. .publishsettings 파일 사용에 대한 자세한 내용은 [방법: 구독에 연결합니다](http://azure.microsoft.com/documentation/articles/install-configure-powershell/#Connect). 

### To Do SPA Azure 웹 사이트 만들기

1. [Azure 관리 포털](https://manage.windowsazure.com)로 이동합니다.
2. 왼쪽 탐색 창에서 **웹 사이트**를 클릭합니다.
3. 왼쪽 아래에서 **새로 만들기**를 클릭하고 **계산** > **웹 사이트** > **사용자 지정 만들기**를 선택한 후 호스팅 계획 및 지역을 선택하고 웹 사이트에 이름을 지정합니다(예: todo-contoso.azurewebsites.net).   사용할 데이터베이스를 선택합니다. To Go API와 같은 데이터베이스도 괜찮습니다.  **웹 사이트 만들기**를 클릭합니다.
4. 웹 사이트를 만든 후 클릭하여 관리합니다.  다시 한 번 이 사이트의 .publishsettings 파일을 다운로드하여 저장합니다.

### Azure 웹 사이트를 사용하도록 두 프로젝트를 모두 업데이트

1. Visual Studio에서 TodoSPA 프로젝트로 이동합니다.
2. 두 가지를 변경해야 합니다.   `App\Scripts\app.js`에서 `endpoints` 개체의 속성 이름을 To Go API의 새 위치(예: `https://togo-contoso.azurewebsites.net/`.  In `App\Scripts\toGoListSvc.js`)로 바꿉니다. `apiEndpoint` 변수를 동일한 값으로 바꿉니다.
3. ToGoAPI 프로젝트에서는 한 가지만 변경하면 됩니다.  `Controllers\ToGoListController.cs`에서 To Do SPA의 새 위치(예: `https://todo-contoso.azurewebsites.net`)를 반영하도록 `[EnableCors...]` 특성을 업데이트합니다.  이 경우에도 후행 슬래시를 생략해야 합니다.

### Azure 웹 사이트에 To Go API 게시

1. Visual Studio로 전환하고 ToGoAPI 프로젝트로 이동합니다.  솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다. **가져오기**를 클릭하고, 다운로드한 To Go API 게시 프로필을 가져옵니다.
6. **연결** 탭에서 https가 되도록 대상 URL을 업데이트합니다(예: https://togo-constoso.azurewebsites.net). **다음**을 클릭합니다.
7. **설정** 탭에서 **조직 인증 사용**이 선택되지 않았는지 확인합니다.  **게시**를 클릭합니다.
8. Visual Studio에서 프로젝트를 게시하고 브라우저가 자동으로 프로젝트의 URL로 열립니다.  프로젝트의 기본 웹 페이지가 표시되면 게시에 성공한 것입니다.

### Azure 웹 사이트에 To Do SPA 게시

1. Visual Studio로 전환하고 TodoSPA 프로젝트로 이동합니다.  솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.  **가져오기**를 클릭하고, 다운로드한 To Do SPA .publishsettings 파일을 가져옵니다.
6. **연결** 탭에서 https가 되도록 대상 URL을 업데이트합니다(https://todo-contoso.azurewebsites.net).  **다음**을 클릭합니다.
7. **설정** 탭에서 **조직 인증 사용**이 선택되지 않았는지 확인합니다.  **게시**를 클릭합니다.
8. Visual Studio에서 프로젝트를 게시하고 브라우저가 자동으로 프로젝트의 URL로 열립니다.  프로젝트의 기본 웹 페이지가 표시되면 게시에 성공한 것입니다.

### 디렉터리 테넌트에서 To Do SPA 구성 업데이트

1. [Azure 관리 포털](https://manage.windowsazure.com)로 이동합니다.
2. 왼쪽 탐색 창에서 **Active Directory**를 클릭하고 테넌트를 선택합니다.
3. **응용 프로그램** 탭에서 **To Do SPA** 응용 프로그램을 선택합니다.
4. **구성** 탭에서 **로그온 URL** 및 **회신 URL** 필드를 SPA 주소(예: https://todo-contoso.azurewebsites.net)로 업데이트합니다.  구성을 저장합니다.

## 코드 정보

인증 논리가 포함된 키 파일은 다음과 같습니다.

- **App.js** - ADAL 모듈 종속성을 삽입하고, 프로토콜과 Azure AD의 상호 작용을 구동하기 위해 ADAL에서 사용하는 앱 구성 값을 제공하며, 이전 인증 없이 액세스되면 안 되는 경로를 나타냅니다.

- **index.html** - adal.js에 대한 참조를 포함합니다.

- **HomeController.js** - ADAL의 login() 및 logOut() 메서드를 활용하는 방법을 보여 줍니다.

- **UserDataController.js** - 캐시된 id_token에서 사용자 정보를 추출하는 방법을 보여 줍니다.
   
이 자습서를 만드는 데 도움을 준 @matvelloso에게 감사드립니다.


## 다음 단계

Azure AD를 사용하여 웹 응용 프로그램 및 Web API에 인증 및 권한 부여를 추가하는 데 도움이 되는 몇 가지 추가 리소스는 다음과 같습니다. 

+ [Azure Active Directory 코드 샘플](https://msdn.microsoft.com/library/azure/dn646737.aspx)
+ [Azure AD의 인증 시나리오](https://msdn.microsoft.com/library/azure/dn499820.aspx)





<!--HONumber=52-->