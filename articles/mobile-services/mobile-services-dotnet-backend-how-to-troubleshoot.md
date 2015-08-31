<properties
	pageTitle="모바일 서비스 .NET 백 엔드 문제 해결 | Microsoft Azure"
	description=".NET 백 엔드를 사용하여 모바일 서비스와 관련된 문제를 진단 및 해결하는 방법에 대해 알아봅니다."
	services="mobile-services"
	documentationCenter=""
	authors="wesmc7777"
	manager="dwrede"
	editor="mollybos"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="08/18/2015" 
	ms.author="wesmc;ricksal"/>

# 모바일 서비스 .NET 백 엔드 문제 해결

모바일 서비스를 사용한 개발 작업은 일반적으로 쉽고 간편하지만 경우에 따라 문제가 발생할 수도 있습니다. 이 자습서에서는 모바일 서비스 .NET 백 엔드에서 발생할 수 있는 공통적인 문제를 해결할 수 있는 몇 가지 기법에 대해 설명합니다.

1. [HTTP 디버깅](#HttpDebugging)
2. [런타임 디버깅](#RuntimeDebugging)
3. [진단 로그 분석](#Logs)
4. [클라우드 어셈블리 확인 디버깅](#AssemblyResolution)
5. [Entity Framework 마이그레이션 문제 해결](#EFMigrations)

<a name="HttpDebugging"></a>
## HTTP 디버깅

모바일 서비스를 사용해서 앱을 개발할 때는 일반적으로 현재 사용 중인 플랫폼(Windows 스토어, iOS, Android 등)을 위한 모바일 서비스 클라이언트 SDK를 사용합니다. 하지만 일부 경우에는 HTTP 수준으로 낮추어서 네트워크에서 발생하는 원시 호출을 관찰해야 할 수 있습니다. 이러한 접근 방식은 특히 연결 및 직렬화 문제를 디버그할 때 유용합니다. 모바일 서비스 .NET 백 엔드에서는 이러한 접근 방식과 함께 Visual Studio 로컬 및 원격 디버깅(다음 섹션에서 자세히 설명)을 사용해서 서비스 호출 전 HTTP 호출에 사용되는 경로를 완전히 이해할 수 있습니다.

HTTP 트래픽을 전송 및 검사하기 위해서는 아무 HTTP 디버거나 사용할 수 있습니다. [Fiddler](http://www.telerik.com/fiddler)는 이러한 목적으로 개발자가 자주 사용하는 인기 있는 도구입니다. 개발 작업을 쉽게 수행할 수 있도록 모바일 서비스에는 외부 도구를 사용할 필요 없이 모바일 서비스에서 직접 사용할 수 있는 웹 기반 HTTP 디버거("테스트 클라이언트")가 포함되어 있습니다. 모바일 서비스를 로컬로 호스트할 때는 [http://localhost:59233](http://localhost:59233)과 같은 URI로 사용할 수 있으며, 클라우드에서 호스트할 때는 [http://todo-list.azure-mobile.net](http://todo-list.azure-mobile.net) 형식의 URI가 사용됩니다. 다음 단계는 서비스 호스팅 위치에 관계없이 동일하게 작동합니다.

1. 시작하려면 **Visual Studio 2013 업데이트 2** 이상에서 모바일 서비스 서버 프로젝트를 엽니다. 아직 프로젝트가 없으면 **파일**, **새로 만들기**, **프로젝트**를 선택한 후 **클라우드** 노드를 선택하고** Microsoft Azure 모바일 서비스** 템플릿을 선택하여 만들 수 있습니다.
2. **F5**를 누르면 프로젝트가 빌드되고 실행됩니다. 시작 페이지에서 **try it out(사용해 보기)**를 선택합니다.

    >[AZURE.NOTE]서비스가 로컬로 호스트되는 경우 링크를 클릭하면 다음 페이지로 이동됩니다. 하지만 클라우드에서 호스팅할 경우에는 자격 증명 집합을 입력하라는 메시지가 표시됩니다. 이 메시지는 인증되지 않은 사용자가 API 및 페이로드에 대한 정보에 액세스할 수 없도록 보장하기 위한 것입니다. 페이지를 표시하려면 **빈 사용자 이름** 및 **응용 프로그램 키**(암호)를 사용해서 로그인해야 합니다. 응용 프로그램 키는 **Azure 관리 포털**에서 모바일 서비스에 대한 **대시보드** 탭으로 이동하고 **키 관리**를 선택하여 확인할 수 있습니다.
    >
    > ![도움말 페이지에 액세스하기 위한 인증 프롬프트][HelpPageAuth]

3. 표시된 페이지("도움말 페이지")에는 모바일 서비스를 사용할 수 있는 모든 HTTP API 목록이 표시됩니다. API 중 하나를 선택합니다. Visual Studio에서 모바일 서비스 프로젝트 템플릿 사용을 시작한 경우 **GET tables/TodoItem**이 표시됩니다.

    ![도움말 페이지][HelpPage]

4. 결과 페이지에는 API에 필요한 요청 및 응답에 대한 설명서 및 JSON 예제가 표시됩니다. **try it out(사용해 보기)** 단추를 선택합니다.

    ![API의 테스트 페이지][HelpPageApi]

5. 이 "테스트 클라이언트"를 사용하면 API를 시험하기 위한 HTTP 요청을 전송할 수 있습니다. **보내기**를 선택합니다.

    ![테스트 클라이언트][TestClient]

6. 모바일 서비스로부터 전송되는 HTTP 응답을 브라우저 창에서 바로 볼 수 있습니다.

    ![테스트 클라이언트의 HTTP 응답][TestClientResponse]

이제 웹 브라우저에 따라 모바일 서비스에서 제공되는 다른 HTTP API를 살펴볼 준비가 되었습니다.

<a name="RuntimeDebugging"></a>
## 런타임 디버깅

.NET 백 엔드의 주요 기능 중 하나는 서비스 코드를 로컬로 디버그하는 기능이지만, 클라우드 환경에서 라이브로 실행되는 코드를 디버그할 수도 있습니다. 다음 단계를 수행하세요.

1. **Visual Studio 2013 업데이트 2** 이상에서 디버그하려는 모바일 서비스 프로젝트를 엽니다.
2. 기호 로드를 구성합니다. **디버그** 메뉴로 이동하고 **옵션 및 설정**을 선택합니다. **내 코드만 사용**이 선택 취소되었고 **소스 서버 지원 사용**이 선택되었는지 확인합니다.

    ![기호 로드 구성][SymbolLoading]

3. 왼쪽에서 **기호** 노드를 선택하고 [http://srv.symbolsource.org/pdb/Public](http://srv.symbolsource.org/pdb/Public) URI를 사용해서 (SymbolSource)[http://symbolsource.org] 서버에 참조를 추가합니다. 모바일 서비스 .NET 백 엔드의 기호는 모든 새 릴리스에 제공됩니다.

    ![기호 서버 구성][SymbolServer]

4. 디버그하려는 코드 조각에 중단점을 설정합니다. 예를 들어 Visual Studio의 모바일 서비스 프로젝트 템플릿과 함께 제공되는 **TodoItemController**의 **GetAllTodoItems()** 메서드에 중단점을 설정합니다.
5. **F5**를 눌러서 서비스를 로컬로 디버그합니다. 처음 로드할 때는 Visual Studio가 모바일 서비스 .NET 백 엔드에 대한 기호를 다운로드하기 때문에 속도가 느릴 수 있습니다.
6. HTTP 디버깅에 대한 이전 섹션에 설명된 것처럼 테스트 클라이언트를 사용해서 중단점을 설정하는 메서드에 HTTP 요청을 보냅니다. 예를 들어 도움말 페이지에서 **GET tables/TodoItem**을 선택하고 **시험해보기** 및 **보내기**를 선택하여 **GetAllTodoItems()** 메서드에 요청을 보낼 수 있습니다.
7. Visual Studio에서 사용자가 설정한 중단점에서 실행이 중단되고 소스 코드를 포함하는 전체 스택 추적을 Visual Studio의 **호출 스택** 창에서 사용할 수 있습니다.

    ![중단점 도착][Breakpoint]

8. 이제 서비스를 Azure에 게시할 수 있으며, 위에서 한 것처럼 디버깅을 사용할 수 있습니다. **솔루션 탐색기**에서 프로젝트를 선택하고 **게시**를 선택하여 프로젝트를 게시합니다.
9. 게시 마법사의 **설정** 탭에서 **디버그** 구성을 선택합니다. 그러면 관련 디버깅 기호가 코드와 함께 게시됩니다.

    ![디버그 게시][PublishDebug]

10. 서비스가 성공적으로 게시되었으면 **서버 탐색기**를 열고 **Azure** 및 **모바일 서비스** 노드를 확장합니다. 필요한 경우 로그인합니다.
11. 바로 전에 게시한 모바일 서비스를 마우스 오른쪽 단추로 클릭하고 **디버거 연결**을 선택합니다.

    ![디버거 연결][AttachDebugger]

12. 6단계에서 수행한 것처럼 HTTP 요청을 보내서 중단점을 설정한 메서드를 호출합니다. 이번에는 Azure에서 호스팅되는 모바일 서비스에 대해 도움말 페이지 및 테스트 클라이언트를 사용합니다.
13. Visual Studio가 중단점에서 멈춥니다.

이제 로컬 및 Azure의 라이브 모바일 서비스에 대해 디버그할 때 Visual Studio 디버거의 모든 기능에 액세스할 수 있습니다.

<a name="Logs"></a>
## 진단 로그 분석

모바일 서비스가 고객의 요청을 처리할 때는 여러 가지 유용한 진단 정보를 생성하고 발생한 모든 예외를 캡처합니다. 이 외에도 [**TableController**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.apiservices.log.aspx)의 [**Services**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.tables.tablecontroller.services.aspx) 속성에서 제공되는 [**Log**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.tables.tablecontroller.aspx) 속성을 활용해서 추가 로그로 컨트롤러 코드를 계측할 수 있습니다.

로컬로 디버그할 때는 로그가 Visual Studio **출력** 창에 표시됩니다.

![Visual Studio 출력 창의 로그][LogsOutputWindow]

Azure에 서비스를 게시한 후에는 Visual Studio의 **서버 탐색기**에서 모바일 서비스를 마우스 오른쪽 단추로 클릭한 후 **로그 보기**를 선택하여 클라우드에서 실행되는 서비스 인스턴스에 대한 로그를 사용할 수 있습니다.

![Visual Studio 서버 탐색기의 로그][LogsServerExplorer]

모바일 서비스에 대한 **로그** 탭의 **Azure 관리 포털**에서도 동일한 로그를 사용할 수 있습니다.

![Azure 관리 포털의 로그][LogsPortal]

<a name="AssemblyResolution"></a>
## 클라우드 어셈블리 확인 디버깅

모바일 서비스를 Azure에 게시하면 컨트롤러 코드를 호스팅하는 HTTP 파이프라인에 업그레이드 및 패치를 효율적으로 적용할 수 있도록 모바일 서비스 호스팅 환경에서 로드됩니다. 여기에는 [.NET 백 엔드 NuGet 패키지](http://www.nuget.org/packages?q=%22mobile+services+.net+backend%22)에서 참조하는 모든 어셈블리가 포함됩니다. 팀은 해당 어셈블리의 최신 버전을 사용하도록 서비스를 계속 업데이트합니다.

일부 경우에는 필요한 어셈블리의 *다른 주 버전*을 참조하여 버전 관리를 도입할 수 있습니다(다른 *부* 버전 허용). 이러한 경우는 NuGet이 모바일 서비스 .NET 백 엔드에서 사용되는 패키지 중 하나의 최신 버전으로 업그레이드하라는 메시지가 표시될 때 발생합니다.

>[AZURE.NOTE]모바일 서비스는 현재 ASP.NET 5.1과 호환되며 ASP.NET 5.2는 현재 지원되지 않습니다. ASP.NET NuGet 패키지를 5.2.*로 업그레이드하면 배포 후에 오류가 발생할 수 있습니다.

이러한 패키지를 업그레이드할 경우, 업데이트된 서비스를 Azure에 게시하면 충돌을 나타내는 경고 페이지가 표시됩니다.

![어셈블리 로드 충돌을 나타내는 도움말 페이지][HelpConflict]

이 페이지에는 서비스 로그에 기록되는 다음 항목과 비슷한 예외 메시지가 함께 표시됩니다.

    Found conflicts between different versions of the same dependent assembly 'Microsoft.ServiceBus': 2.2.0.0, 2.3.0.0. Please change your project to use version '2.2.0.0' which is the one currently supported by the hosting environment.

이 문제는 쉽게 해결할 수 있습니다. 필요한 어셈블리의 지원되는 버전으로 되돌리고 서비스를 다시 게시하면 됩니다.

<a name="EFMigrations"></a>
## Entity Framework 마이그레이션 문제 해결

SQL 데이터베이스와 함께 모바일 서비스 .NET 백 엔드를 사용할 때는 사용자가 데이터베이스를 쿼리하고 여기에 개체를 저장할 수 있게 해주는 데이터 액세스 기술로 EF(Entity Framework)가 사용됩니다. EF가 개발자를 위해 처리하는 한 가지 중요한 특징은 코드에 지정된 모델 클래스가 변경될 때 데이터베이스 열(*스키마*)이 변경되는 방식입니다. 이 프로세스를 [Code First 마이그레이션](http://msdn.microsoft.com/data/jj591621)이라고 합니다.

마이그레이션은 복잡할 수 있으며 작업을 성공하기 위해서는 데이터베이스 상태가 EF 모델과 동기화된 상태로 유지되어야 합니다. 모바일 서비스의 마이그레이션 처리 방법과 발생 가능한 오류에 대한 자세한 내용은 [.NET 백 엔드 모바일 서비스에 대한 데이터 모델 변경 방법](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md)을 참조하세요.

<!-- IMAGES -->

[HelpPageAuth]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/6.png
[HelpPage]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/7.png
[HelpPageApi]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/8.png
[TestClient]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/9.png
[TestClientResponse]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/10.png
[SymbolLoading]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/1.png
[SymbolServer]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/2.png
[Breakpoint]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/3.png
[PublishDebug]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/4.png
[AttachDebugger]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/5.png
[LogsOutputWindow]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/11.png
[LogsServerExplorer]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/12.png
[LogsPortal]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/13.png
[HelpConflict]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/14.png

<!---HONumber=August15_HO8-->