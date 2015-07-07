<properties
   pageTitle="Visual Studio 코드에서 5 ASP.NET 웹앱 만들기"
   description="이 자습서에서는 Visual Studio Code를 사용하여 ASP.NET 5 웹앱을 만드는 방법을 보여 줍니다."
   services="app-service\web"
   documentationCenter=".net"
   authors="erikre"
   manager="wpickett"
   editor="jimbe"/>

<tags
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/14/2015" 
	ms.author="erikre;tarcher"/>

# Visual Studio 코드에서 5 ASP.NET 웹앱 만들기

## 개요

이 자습서에서는 [VS Code(Visual Studio Code)](http://code.visualstudio.com//Docs/whyvscode)를 사용하여 ASP.NET 5 웹앱을 만들어 [Azure 앱 서비스](../app-service/app-service-value-prop-what-is.md)에 배포하는 방법에 대해 알아봅니다. ASP.NET 5에서는 ASP.NET을 완전히 다시 디자인했습니다. ASP.NET 5는 .NET을 사용하여 최신 클라우드 기반 웹앱을 빌드하기 위한 새로운 오픈 소스 크로스 플랫폼 프레임 워크입니다. 자세한 내용은 [ASP.NET 5 소개](http://docs.asp.net/en/latest/conceptual-overview/aspnet.html)를 참조하세요. Azure 앱 서비스 웹앱에 대한 자세한 내용은 [웹앱 개요](app-service-web-overview.md)를 참조하세요.

[AZURE.INCLUDE [app-service-web-try-app-service.md](../../includes/app-service-web-try-app-service.md)]

## 필수 조건  

* [VS Code](http://code.visualstudio.com/Docs/setup)를 설치합니다.
* [Node.js](http://nodejs.org/download/) 설치 - [Node.js](http://nodejs.org/)는 JavaScript를 사용하여 빠르고 확장성이 뛰어난 서버 응용 프로그램을 빌드하기 위한 플랫폼입니다. 노드는 런타임(노드)이고 [npm](http://www.npmjs.com/)은 노드 모듈에 대한 패키지 관리자입니다. 이 자습서에서는 npm을 사용하여 ASP.NET 5 웹앱을 스캐폴딩합니다.
* Git 설치 - [Chocolatey](https://chocolatey.org/packages/git) 또는 [git-scm.com](http://git-scm.com/downloads)에서 설치할 수 있습니다. Git를 처음 사용하는 경우 [git-scm.com](http://git-scm.com/downloads)을 선택하고 GitBash와 함께 Windows 명령 프롬프트에서 Git를 사용하는 옵션을 선택합니다. Git를 설치한 후 자습서의 뒤에 나오는 VS Code에서 커밋을 수행할 때 필요하므로 Git 사용자 이름과 전자 메일을 설정해야 합니다.  

## ASP.NET 5 및 DNX 설치
ASP.NET 5/DNX는 OS X, Linux 및 Windows에서 실행되는 최신 클라우드 및 웹앱을 빌드하기 위한 린(lean) .NET 스택입니다. ASP.NET 5/DNX는 클라우드에 배포되거나 온-프레미스로 실행될 앱에 최적화된 개발 프레임을 제공하기 위해 처음부터 다시 빌드되었습니다. 오버헤드를 최소화하는 모듈식 구성 요소로 구성되므로 솔루션을 구성하는 동안 유연성이 그대로 유지됩니다.

> [AZURE.NOTE]OS X 및 Linux의 ASP.NET 5 및 DNX(.NET 실행 환경)는 초기 베타/미리 보기 상태에 있습니다.

이 자습서는 ASP.NET 5와 DNX의 최신 개발 버전으로 응용 프로그램 빌드를 시작하도록 설계되었습니다. 다음은 Windows에 특정한 지침입니다. OS X, Linux 및 Windows에 대한 자세한 설치 지침은 [ASP.NET 5 및 DNX 설치](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx)를 참조하세요.

1. Windows에서.NET 버전 관리자(DNVM)를 설치하려면 명령 프롬프트를 열고 다음 명령을 실행합니다.

		@powershell -NoProfile -ExecutionPolicy unrestricted -Command "&{$Branch='dev';iex ((new-object net.webclient).DownloadString('https://raw.githubusercontent.com/aspnet/Home/dev/dnvminstall.ps1'))}"

	이 명령은 DNVM 스크립트를 다운로드하여 사용자 프로필 디렉터리에 배치합니다.

2. DNVM 설치를 완료하려면 Windows를 다시 시작합니다.

3. 명령 프롬프트를 열고 다음을 입력하여 DNVM의 위치를 확인합니다.

		where dnvm

	명령 프롬프트에 다음과 비슷한 경로가 표시됩니다.

	![dnvm 위치](./media/web-sites-create-web-app-using-vscode/00-where-dnvm.png)

4. DNVM을 설치했으므로 이제 DNVM을 사용하여 응용 프로그램을 실행할 DNX를 다운로드해야 합니다. 명령 프롬프트에서 다음을 실행합니다.

		dnvm upgrade

5. DNVM을 확인하고 명령 프롬프트에서 다음을 입력하여 활성 런타임을 확인합니다.

		dnvm list

	명령 프롬프트에 활성 런타임에 대한 세부 정보가 표시됩니다.

	![DNVM 위치](./media/web-sites-create-web-app-using-vscode/00b-dnvm-list.png)

6. DNX 런타임이 둘 이상 나열되는 경우 명령 프롬프트에 다음과 같이 입력하여 활성 DNX 런타임을 이 자습서의 뒷부분에서 웹앱을 만들 때 ASP.NET 5 생성기에서 사용되는 것과 같은 버전으로 설정합니다.

		dnvm use 1.0.0-beta4 –p

> [AZURE.NOTE]OS X, Linux 및 Windows에 대한 자세한 설치 지침은 [ASP.NET 5 및 DNX 설치](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx)를 참조하세요.

## 웹앱 만들기 

이 섹션에서는 새 앱 ASP.NET 웹앱을 스캐폴딩하는 방법을 보여 줍니다. NPM(Node Package Manager)을 사용하여 [Yeoman](http://yeoman.io/)(응용 프로그램 스캐폴딩 도구 - Visual Studio **파일 > 새 프로젝트** 작업에 해당하는 VS Code), [Grunt](http://gruntjs.com/)(JavaScript Task Runner) 및 [Bower](http://bower.io/)(클라이언트측 패키지 관리자)를 설치합니다.

1. 관리자 권한으로 명령 프롬프트를 열고 ASP.NET 프로젝트를 만들 위치로 이동합니다.

2. Yeoman 및 지원 도구를 설치하려면 명령 프롬프트에 다음을 입력합니다.

		npm install -g yo grunt-cli generator-aspnet bower

3. 프로젝트 폴더를 만들고 앱을 스캐폴딩하려면 명령 프롬프트에 다음을 입력합니다.

		yo aspnet

4. ASP.NET 5 생성기 메뉴에서 화살표 키를 사용하여 **웹 응용 프로그램** 유형을 선택하고 &lt;Enter> 키를 누릅니다.

	![Yeoman - ASP.NET 5 생성기](./media/web-sites-create-web-app-using-vscode/01-yo-aspnet.png)

5. 새 ASP.NET 웹앱의 이름을 **SampleWebApp**으로 설정합니다. 이 이름은 자습서 전체에서 사용되므로 다른 이름을 선택할 경우 각 **SampleWebApp** 항목에 대해 해당 이름을 대체해야 합니다. &lt;Enter> 키를 누르면 Yeoman에서 **SampleWebApp**이라는 새 폴더와 새 앱에 필요한 파일을 만듭니다.

6. 명령 프롬프트에서 다음을 입력하여 VS Code를 엽니다.

		code .

7. VS Code에서 **파일 > 폴더 열기**를 선택하고 ASP.NET 웹앱이 들어 있는 폴더를 선택합니다.

	![폴더 선택 대화 상자](./media/web-sites-create-web-app-using-vscode/02-open-folder.png)

	VS Code는 프로젝트를 로드하고 **탐색** 창에 파일을 표시합니다.

	![SampleWebApp 프로젝트를 표시하는 VSCode](./media/web-sites-create-web-app-using-vscode/03-vscode-project.png)

8. **보기 > 명령 팔레트**를 선택합니다.

9. **명령 팔레트**에서 다음 명령을 입력합니다.

		dnx:dnu restore - (SampleWebApp)

	입력을 시작하면 목록에서 전체 명령줄이 표시됩니다.

	![복원 명령](./media/web-sites-create-web-app-using-vscode/04-dnu-restore.png)

	복원 명령은 응용 프로그램을 실행하는 데 필요한 NuGet 패키지를 설치합니다. 준비가 되면 명령 프롬프트에 **복원 완료**가 표시됩니다.

## 로컬로 웹앱 실행

웹앱을 만들고 앱에 대한 모든 NuGet 패키지를 검색했으므로 이제 웹앱을 로컬로 실행할 수 있습니다.

1. VS Code의 **명령 팔레트**에서 다음을 입력하여 앱을 로컬로 실행합니다.

		dnx: kestrel - (SampleWebApp, Microsoft.AspNet.Hosting --server Kestrel --server.urls http://localhost:5001

	명령 창에 *시작됨*이 표시됩니다. 명령 창에 *시작됨*이 표시되지 않는 경우 VS Code의 왼쪽 아래에서 프로젝트에 오류가 있는지 확인합니다.

5. 브라우저를 열고 다음 URL로 이동합니다.

	**http://localhost:5001**로 바꿉니다.

	웹앱의 기본 페이지가 다음과 같이 표시됩니다.

	![브라우저의 로컬 웹앱](./media/web-sites-create-web-app-using-vscode/08-web-app.png)

## Azure 미리 보기 포털에 웹 앱 만들기

다음 단계에서는 Azure Preview 포털에서 웹앱을 만드는 과정을 안내합니다.

1. [Azure Preview 포털](https://portal.azure.com)에 로그인합니다.

2. 포털의 왼쪽 위에서 **새로 만들기**를 클릭합니다.

3. **웹앱 > 웹앱**을 클릭합니다.

	![Azure 새 웹앱](./media/web-sites-create-web-app-using-vscode/09-azure-newwebapp.png)

4. **이름**에 대한 값을 입력합니다(예: **SampleWebAppDemo**). 이 이름은 고유해야 하며 사용자가 이름을 입력할 때 포털에서 해당 이름을 적용합니다. 따라서 다른 값을 입력할 경우 이 자습서에 표시되는 각 **SampleWebAppDemo** 항목을 해당 값으로 대체해야 합니다.

5. 기존 **앱 서비스 계획**을 선택하거나 새 앱 서비스 계획을 만듭니다. 새 계획을 만드는 경우 가격 책정 계층, 위치 및 기타 옵션을 선택합니다. 앱 서비스 계획에 대한 자세한 내용은 [Azure 앱 서비스 계획의 포괄 개요](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)를 참조하세요.

	![Azure 새 웹앱 블레이드](./media/web-sites-create-web-app-using-vscode/10-azure-newappblade.png)

6. **만들기**를 클릭합니다.

	![웹앱 블레이드](./media/web-sites-create-web-app-using-vscode/11-azure-webappblade.png)

## 새 웹앱에 Git 게시 사용

Git는 Azure 앱 서비스 웹앱을 배포하는 데 사용할 수 있는 분산된 버전 제어 시스템입니다. 웹앱에 대해 작성한 코드를 로컬 Git 리포지토리에 저장하고, 원격 리포지토리로 푸시하여 Azure에 코드를 배포합니다.

1. [Azure Preview 포털](https://portal.azure.com)에 로그인합니다.

2. **모두 찾아보기**를 클릭합니다.

3. **웹앱**을 클릭하여 Azure 구독과 연결된 웹앱의 목록을 확인합니다.

4. 이 자습서에서 만든 웹앱을 선택합니다.

5. 웹앱 블레이드에서 **배포** 섹션까지 아래로 스크롤하고 **연속 배포 설정**을 클릭합니다.

	![Azure 웹앱 호스트](./media/web-sites-create-web-app-using-vscode/14-azure-deployment.png)

6. **원본 선택 > 로컬 Git 리포지토리**를 클릭합니다.

7. **확인**을 클릭합니다.

	![Azure 로컬 Git 리포지토리](./media/web-sites-create-web-app-using-vscode/15-azure-localrepository.png)

8. 이전에 웹앱 또는 다른 앱 서비스 앱을 게시하기 위해 배포 자격 증명을 설정하지 않은 경우 지금 설정합니다.

	* 배포 자격 증명을 설정할 **FTP**를 클릭합니다.

	* 사용자 이름 및 암호를 만듭니다. 나중에 Git를 설정할 때 이 암호가 필요합니다.

	* **Save**를 클릭합니다.

	![Azure 배포 자격 증명](./media/web-sites-create-web-app-using-vscode/16-azure-credentials.png)

9. 웹앱의 블레이드에서 **설정 > 속성**을 클릭합니다. 배포할 원격 Git 리포지토리의 URL이 **
10.  URL** 아래에 표시됩니다.

10. 이 자습서에서 나중에 사용할 **GIT URL** 값을 복사합니다.

	![Azure Git URL](./media/web-sites-create-web-app-using-vscode/17-azure-giturl.png)

## Azure 앱 서비스에 웹앱 게시

이 섹션에서는 로컬 Git 리포지토리를 만들고 해당 리포지토리에서 Azure로 푸시하여 웹앱을 Azure에 배포합니다.

1. VS Code의 왼쪽 탐색 모음에서 **Git** 옵션을 선택합니다.

	![VS Code의 Git 아이콘](./media/web-sites-create-web-app-using-vscode/git-icon.png)

2. **git 리포지토리 초기화**를 선택하여 작업 공간이 git 소스 제어 아래에 오도록 합니다.

	![Git 초기화](./media/web-sites-create-web-app-using-vscode/19-initgit.png)

3. 커밋 메시지를 추가하고 **모두 커밋** 확인 아이콘을 클릭합니다.

	![Git 모두 커밋](./media/web-sites-create-web-app-using-vscode/20-git-commit.png)

4. Git 처리가 완료되면 **변경 내용** 아래 Git 창에 나열된 파일이 없습니다.

	![Git 변경 내용 없음](./media/web-sites-create-web-app-using-vscode/no-changes.png)

5. 명령 프롬프트를 열고 디렉터리를 웹앱 하우징 디렉터리로 변경합니다.

6. 앞에서 복사한 Git URL(“.git”로 종료됨)을 사용하여 웹앱에 업데이트를 푸시하기 위한 원격 참조를 만듭니다.

		git remote add azure [URL for remote repository]

7. 다음 명령을 입력하여 변경 내용을 Azure에 푸시합니다.

		git push azure master

	이전에 만든 암호를 입력하라는 메시지가 나타납니다. **참고: 암호는 표시되지 않습니다.**

	위 명령의 출력은 배포에 성공했다는 메시지로 종료됩니다.

		remote: Deployment successful.
		To https://user@testsite.scm.azurewebsites.net/testsite.git
		[new branch]      master -> master

> [AZURE.NOTE]앱을 변경한 경우 VS Code에서 **모두 커밋**을 선택한 다음 명령 프롬프트에서 **git push azure master** 명령을 입력하여 다시 게시할 수 있습니다.

## Azure에서 앱 실행
웹앱을 배포했으므로 이제 Azure에서 호스트된 상태에서 앱을 실행해 보겠습니다.

이 작업은 두 가지 방법으로 수행할 수 있습니다.

* 브라우저를 열고 웹앱의 이름을 다음과 같이 입력합니다.   

		http://SampleWebAppDemo.azurewebsites.net
 
* Azure Preview 포털에서 웹앱에 대한 웹앱 블레이드를 찾은 다음 **찾아보기**를 클릭하여 기본 브라우저에서 앱을 확인합니다.

![Azure 웹앱](./media/web-sites-create-web-app-using-vscode/21-azurewebapp.png)

## 요약
이 자습서에서는 VS Code에서 웹앱을 만들고 Azure에 배포하는 방법을 알아보았습니다. VS Code에 대한 자세한 내용은 [Visual Studio Code를 선택해야 하는 이유?](https://code.visualstudio.com/Docs/)를 참조하세요. 앱 서비스 웹앱에 대한 자세한 내용은 [웹앱 개요](app-service-web-overview.md)를 참조하세요.

<!---HONumber=62-->