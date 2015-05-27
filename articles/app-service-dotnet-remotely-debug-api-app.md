<properties 
	pageTitle="Azure 앱 서비스에서 API 앱 디버그" 
	description="Visual Studio를 사용하여 Azure 앱 서비스에서 실행되는 동안 API 앱을 디버그하는 방법을 알아봅니다." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
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

# Azure 앱 서비스에서 API 앱 디버그 

## 개요

이 자습서에서는 ASP.NET Web API가 [Azure 앱 서비스](app-service-value-prop-what-is.md)의 [API 앱](app-service-api-apps-why-best-platform.md)에서 실행되는 동안 해당 코드를 디버그합니다. 이 자습서는 이 시리즈의 이전 자습서에서 [만들고](app-service-dotnet-create-api-app.md) [배포](app-service-dotnet-deploy-api-app.md)한 API 앱을 사용합니다.

먼저 Visual Studio의 **API 앱 클라이언트** 기능을 사용하여 배포된 API 앱을 호출하는 클라이언트 코드를 생성합니다. 그런 다음 클라우드에서 API 앱이 라이브로 실행 중인 상태에서 클라이언트 앱과 API 앱을 동시에 디버그합니다.

## API 앱 클라이언트 생성 

Visual Studio의 API 앱 도구를 사용하면 데스크톱, 스토어 및 모바일 앱에서 Azure API 앱을 호출하는 C# 코드를 쉽게 생성할 수 있습니다.

1. Visual Studio에서 [API 앱 만들기](app-service-dotnet-create-api-app.md) 항목의 API 앱이 포함된 솔루션을 엽니다. 

2. **솔루션 탐색기**에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **추가** > **새 프로젝트**를 선택합니다.

	![새 프로젝트 추가](./media/app-service-dotnet-remotely-debug-api-app/01-add-new-project-v3.png)

3. **새 프로젝트 추가** 대화 상자에서 다음 단계를 수행합니다.

	1. **Windows 데스크톱** 범주를 선택합니다.
	
	2. **콘솔 응용 프로그램** 프로젝트 템플릿을 선택합니다.
	
	3. 프로젝트의 이름을 지정합니다.
	
	4. **확인**을 클릭하여 기존 솔루션에 새 프로젝트를 생성합니다.
	
	![새 프로젝트 추가](./media/app-service-dotnet-remotely-debug-api-app/02-contact-list-console-project-v3.png)

4. 새로 만든 콘솔 응용 프로그램 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **Azure API 앱 클라이언트**를 선택합니다.

	![새 클라이언트 추가](./media/app-service-dotnet-remotely-debug-api-app/03-add-azure-api-client-v3.png)
	
5. **Microsoft Azure API 앱 클라이언트 추가** 대화 상자에서 다음 단계를 수행합니다.

	1. **다운로드** 옵션을 선택합니다. 
	
	2. 드롭다운 목록에서 이전에 만든 API 앱을 선택합니다.
	
	3. **확인**을 클릭합니다.

	![생성 화면](./media/app-service-dotnet-remotely-debug-api-app/04-select-the-api-v3.png)

	마법사가 API 메타데이터 파일을 다운로드하고 API 앱을 호출하기 위한 형식화된 인터페이스를 생성합니다.

	![생성 발생](./media/app-service-dotnet-remotely-debug-api-app/05-metadata-downloading-v3.png)

	코드 생성이 완료되고 나면 솔루션 탐색기에 API 앱의 이름으로 만들어진 새 폴더가 표시됩니다. 이 폴더에는 클라이언트 및 데이터 모델을 구현하는 코드가 들어 있습니다.

	![생성 완료](./media/app-service-dotnet-remotely-debug-api-app/06-code-gen-output-v3.png)

6. 프로젝트 루트에서 **Program.cs** 파일을 열고 **Main** 메서드를 다음 코드로 바꿉니다.

		static void Main(string[] args)
	    {
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
	    }

## API 앱 클라이언트 테스트

API 앱을 코딩했으면 브라우저에서 코드를 테스트해야 합니다.

1. **솔루션 탐색기**를 엽니다.

2. 이전 섹션에서 만든 콘솔 응용 프로그램을 마우스 오른쪽 단추로 클릭합니다.

3. 콘솔 응용 프로그램의 상황에 맞는 메뉴에서 **디버그 > 새 인스턴스 시작**을 선택합니다.

4. 콘솔 창이 열리고 모든 연락처가 표시됩니다.

	![콘솔 앱 실행](./media/app-service-dotnet-remotely-debug-api-app/running-console-app.png)

5. **Enter** 키를 눌러 콘솔 창을 해제합니다.

## API 앱 디버그 

API 앱과 해당 클라이언트가 코딩 및 테스트되었으므로 이제 디버그하는 방법을 살펴보겠습니다.

1. Visual Studio **보기** 메뉴에서 **서버 탐색기**를 선택합니다. 

2. **서버 탐색기**에서 **Azure > 앱 서비스** 노드를 확장합니다.

3. API 앱을 배포할 때 만든 리소스 그룹을 찾습니다.

4. 리소스 그룹에서 API 앱 노드를 마우스 오른쪽 단추로 클릭하고 **디버거 연결**을 선택합니다.

	![디버거 연결](./media/app-service-dotnet-remotely-debug-api-app/08-attach-debugger-v3.png)

	원격 디버거가 연결하려고 합니다. 연결을 설정하기 위해 **디버거 연결**을 다시 클릭해야 하는 경우도 있으므로 연결에 실패할 경우 다시 시도하세요.

	![디버거 연결](./media/app-service-dotnet-remotely-debug-api-app/09-attaching-v3.png)

16. 연결이 설정된 후 API 앱 프로젝트에서 **ContactsController.cs** 파일을 열고 `Get` 및 `Post` 메서드에 중단점을 추가합니다. 처음에는 이러한 메서드가 활성 상태로 표시되지 않을 수 있지만 원격 디버거가 연결되면 디버그할 준비가 된 것입니다.

	![컨트롤러에 중단점 적용](./media/app-service-dotnet-remotely-debug-api-app/10-breakpoints-v3.png)

17. 디버그하려면 **솔루션 탐색기**에서 콘솔 앱을 마우스 오른쪽 단추로 클릭하고 **디버그** > **새 인스턴스 시작**을 선택합니다. 이제 API 앱을 원격으로, 클라이언트 앱을 로컬로 디버그하고 데이터의 전체 흐름을 볼 수 있습니다.

	다음 스크린샷은 `Post` 메서드의 중단점이 적중된 경우의 디버거를 보여 줍니다. 클라이언트의 연락처 데이터가 강력한 형식의 `Contact` 개체로 역직렬화된 것을 볼 수 있습니다.

	![API를 적중하는 로컬 클라이언트 디버그](./media/app-service-dotnet-remotely-debug-api-app/12-debugging-live-v3.png)

## 다음 단계

API 앱용 원격 디버깅을 사용하면 Azure 앱 서비스에서 코드가 어떻게 실행되고 있는지 쉽게 확인할 수 있습니다. Visual Studio IDE에서 바로 Azure API 앱에 대한 다양한 진단 및 디버깅 데이터를 사용할 수 있습니다.

앱 서비스 API 앱은 웹 서비스를 호스트하기 위한 추가 기능이 있는 앱 서비스 웹앱이므로 웹앱에 사용하는 것과 동일한 디버깅 및 문제 해결 도구를 API 앱에 사용할 수 있습니다. 자세한 내용은 [Visual Studio를 사용하여 Azure 앱 서비스에서 웹앱 문제 해결](web-sites-dotnet-troubleshoot-visual-studio.md)을 참조하세요.

이 시리즈에서 만든 API 앱은 누구든지 공개적으로 호출할 수 있습니다. 인증된 사용자만 호출할 수 있도록 API 앱을 보호하는 방법에 대한 자세한 내용은 [API 앱 보호: Azure Active Directory 또는 소셜 공급자 인증 추가](app-service-api-dotnet-add-authentication.md)를 참조하세요.

<!--HONumber=54-->