<properties 
	pageTitle="Azure 앱 서비스 API 앱을 원격으로 디버그" 
	description="Visual Studio를 사용하여 Azure 앱 서비스 API 앱을 원격으로 디버그" 
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

# Azure 앱 서비스 API 앱을 원격으로 디버그 

## 개요

Visual Studio의 원격 디버깅 기능이 Azure 앱 서비스의 API 앱에 대한 지원을 포함하도록 확장되었습니다. 즉, 익숙한 디버깅 도구를 사용하여 Azure에서 라이브로 실행 중인 코드를 확인할 수 있습니다. 이 항목에서는 Visual Studio의 **API 앱 클라이언트**를 사용하여, 배포된 API 앱을 호출하는 클라이언트 코드를 생성하는 방법을 보여 줍니다. 그런 다음 클라우드에서 API 앱이 라이브로 실행 중인 상태에서 클라이언트 앱과 API 앱을 동시에 디버그합니다.

이 자습서는 3부로 구성된 시리즈의 마지막 자습서입니다.

1. [API 앱 만들기](app-service-dotnet-create-api-app.md)에서는 API 앱 프로젝트를 만들었습니다. 
* [API 앱 배포](app-service-dotnet-deploy-api-app.md)에서는 API 앱을 Azure 구독에 배포했습니다.
* 이 자습서에서는 Visual Studio를 사용하여 Azure에서 코드가 실행되는 동안 코드를 원격으로 디버그합니다.

## API 앱 클라이언트 생성 

Visual Studio의 API 앱 도구를 사용하면 데스크톱, 스토어 및 모바일 앱에서 Azure API 앱을 호출하는 C# 코드를 쉽게 생성할 수 있습니다. 

Visual Studio에서 [첫 번째](app-service-dotnet-create-api-app.md) 자습서의 API 앱이 포함된 솔루션을 엽니다. 솔루션을 마우스 오른쪽 단추로 클릭하고 **추가** > **새 프로젝트**를 선택합니다.

![Add a new project](./media/app-service-dotnet-remotely-debug-api-app/01-add-new-project-v3.png)

**Windows 데스크톱** 범주 및 **콘솔 응용 프로그램** 프로젝트 템플릿을 선택합니다.

![Add a new project](./media/app-service-dotnet-remotely-debug-api-app/02-contact-list-console-project-v3.png)

콘솔 응용 프로그램 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **Azure API 앱 클라이언트**를 선택합니다. 

![Add a new Client](./media/app-service-dotnet-remotely-debug-api-app/03-add-azure-api-client-v3.png)
	
대화 상자에서 **다운로드** 옵션을 선택합니다. 드롭다운 목록에서 이전에 만든 API 앱을 선택합니다. **확인**을 클릭합니다. 

![Generation Screen](./media/app-service-dotnet-remotely-debug-api-app/04-select-the-api-v3.png)

마법사가 API 메타데이터 파일을 다운로드하고 API 앱을 호출하기 위한 형식화된 인터페이스를 생성합니다.

![Generation Happening](./media/app-service-dotnet-remotely-debug-api-app/05-metadata-downloading-v3.png)

코드 생성이 완료되고 나면 솔루션 탐색기에 API 앱의 이름으로 만들어진 새 폴더가 표시됩니다. 이 폴더에는 클라이언트 및 데이터 모델을 구현하는 코드가 들어 있습니다. 

![Generation Complete](./media/app-service-dotnet-remotely-debug-api-app/06-code-gen-output-v3.png)

프로젝트 루트에서 **Program.cs** 파일을 열고 **Main** 메서드를 다음 코드로 바꿉니다. 

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

**보기** 메뉴에서 **서버 탐색기**를 선택합니다. 서버 탐색기 창에서 *Azure > 앱 서비스** 노드를 확장합니다. API 앱을 배포할 때 만든 리소스 그룹을 찾습니다. API 앱 노드를 마우스 오른쪽 단추로 클릭하고 **디버거 연결**을 선택합니다. 

![Attaching debugger](./media/app-service-dotnet-remotely-debug-api-app/08-attach-debugger-v3.png)

원격 디버거가 연결하려고 합니다. 일부 경우 연결을 설정하기 위해 **디버거 연결**을 다시 클릭해야 할 수도 있으므로 연결에 실패하는 경우 다시 시도하세요.

![Attaching debugger](./media/app-service-dotnet-remotely-debug-api-app/09-attaching-v3.png)

연결이 설정된 후 API 앱 프로젝트에서 **ContactsController.cs** 파일을 열고 `Get` 및 `Post` 메서드에 중단점을 추가합니다. 처음에는 이러한 메서드가 활성 상태로 표시되지 않을 수 있지만 원격 디버거가 연결되면 디버그할 준비가 된 것입니다. 

![Applying breakpoints to controller](./media/app-service-dotnet-remotely-debug-api-app/10-breakpoints-v3.png)

디버그하려면 솔루션 탐색기에서 콘솔 앱을 마우스 오른쪽 단추로 클릭하고 **디버그** > **새 인스턴스 시작**을 선택합니다. 이제 API 앱을 원격으로, 클라이언트 앱을 로컬로 디버그하고 데이터의 전체 흐름을 볼 수 있습니다. 

다음 스크린샷은 `Post` 메서드의 중단점이 적중된 경우의 디버거를 보여 줍니다. 클라이언트의 연락처 데이터가 강력한 형식의 `Contact` 개체로 역직렬화된 것을 볼 수 있습니다. 

![Debugging the local client to hit the API](./media/app-service-dotnet-remotely-debug-api-app/12-debugging-live-v3.png)

## 요약

API 앱용 원격 디버깅을 사용하면 Azure 앱 서비스에서 코드가 어떻게 실행되고 있는지 쉽게 확인할 수 있습니다. 원격으로 실행되고 있는 Azure API 앱에 대한 다양한 진단 및 디버깅 데이터를 Visual Studio IDE에서 바로 사용할 수 있습니다.


<!--HONumber=49-->