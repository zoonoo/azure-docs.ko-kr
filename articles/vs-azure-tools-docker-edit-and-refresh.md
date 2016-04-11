<properties
   pageTitle="편집 및 새로 고침을 사용하여 로컬 Docker 컨테이너에서 앱 디버그 | Microsoft Azure"
   description="편집 및 새로 고침을 통해 로컬 Docker 컨테이너에서 실행 중인 앱을 수정하고 컨테이너를 새로 고치는 방법을 알아봅니다."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="03/25/2016"
   ms.author="tarcher" />

# 편집 및 새로 고침을 사용하여 로컬 Docker 컨테이너에서 앱 디버그

## 개요
Visual Studio Tools for Docker는 코드를 변경할 때마다 컨테이너를 다시 시작하지 않고도 Docker 컨테이너에서 로컬로 응용 프로그램을 개발 및 테스트할 수 있는 편리한 방법을 제공합니다. 이 문서에서는 "편집 및 새로 고침" 기능을 사용하여 로컬 Docker 컨테이너에서 ASP.NET 5 웹앱을 시작하고 필요한 내용을 변경한 다음 브라우저를 새로 고쳐 변경 내용을 확인하는 방법을 설명합니다.

## 필수 조건
다음과 같은 도구를 설치해야 합니다.

- [Visual Studio 2015 업데이트 1](https://go.microsoft.com/fwlink/?LinkId=691979)
- [Microsoft ASP.NET 및 Web Tools 2015 RC](https://go.microsoft.com/fwlink/?LinkId=627627)
- [Docker 도구 상자](https://www.docker.com/products/overview#/docker_toolbox)
- [Visual Studio 2015 Tools for Docker](https://aka.ms/DockerToolsForVS)
- [Docker 클라이언트 구성](./vs-azure-tools-docker-setup.md)

> [AZURE.NOTE] 이전 버전의 Visual Studio 2015 Tools for Docker가 설치된 경우 최신 버전을 설치하기 전에 제어판에서 제거해야 합니다.

## 로컬 Docker 컨테이너에서 실행 중인 앱 편집
Visual Studio 2015 Tools for Docker를 통해 ASP.NET 5 웹앱 개발자는 Docker 컨테이너에서 해당 응용 프로그램을 테스트하고 실행하며 Visual Studio에서 응용 프로그램을 변경하고 브라우저를 새로 고쳐서 컨테이너 내부에서 실행 중인 앱에 적용된 변경 내용을 확인할 수 있습니다.

1. Visual Studio 메뉴에서 **파일 > 새로 만들기 > 프로젝트**를 선택합니다. 

1. **새 프로젝트** 대화 상자의 **템플릿** 섹션에서 **Visual C# > 웹**을 선택합니다.

1. **ASP.NET 웹 응용 프로그램**을 선택합니다.

1. 새 응용 프로그램에 이름을 지정(또는 기본값 사용)합니다.

1. **확인**을 탭합니다.

1. **ASP.NET 5 템플릿**에서 **ASP.NET 웹 응용 프로그램**을 선택합니다.

1. **확인**을 탭합니다.

1. Visual Studio 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가 > Docker 지원**을 선택합니다.

	![][0]
 
1. 다음 파일이 프로젝트 노드 아래에 만들어집니다.

	![][1]

1. 솔루션 구성을 `Debug`로 설정하고 **&lt;F5>** 키를 눌러 응용 프로그램을 로컬로 테스트하기 시작합니다.

1. 컨테이너 이미지가 빌드되고 Docker 컨테이너에서 실행되면 PowerShell 콘솔은 기본 브라우저에서 웹앱을 시작하려고 합니다. Microsoft Edge 브라우저를 사용하는 경우 [문제 해결](#troubleshooting) 섹션을 참조하세요.

1. Visual Studio로 돌아가서 `Views\Home\Index.cshtml`을 엽니다.

1. 파일의 끝에 다음 HTML 콘텐츠를 추가하고 변경 내용을 저장합니다.

		<div>
			<h1>Hello from Docker Container!</h1>
		</div>

1.	브라우저로 전환하고 새로 고칩니다.

1.	홈페이지의 끝으로 스크롤하면 적용된 변경 내용이 표시됩니다. 사이트에서 다시 컴파일하는 데 몇 초가 걸릴 수 있으므로 변경 내용이 즉시 반영되지 않은 경우 브라우저를 다시 새로 고치기만 하면 됩니다.

##문제 해결 

- **앱을 실행하면 PowerShell이 열리고 오류를 표시한 다음 닫힙니다. 브라우저 페이지가 열리지 않습니다.**

	이는 `docker-compose-up` 중 오류일 수 있습니다. 오류를 확인하려면 다음 단계를 수행합니다.

	1. `Properties\launchSettings.json` 파일을 엽니다.
	
	1. Docker 항목을 찾습니다.
	
	1. 다음과 같이 시작하는 줄을 찾습니다.

			"commandLineArgs": "-ExecutionPolicy RemoteSigned …”
	
	1. `-noexit` 매개 변수를 추가하면 이제 줄이 다음과 유사합니다. 이렇게 하면 PowerShell이 계속 열려 있어서 오류를 확인할 수 있습니다.

			"commandLineArgs": "-noexit -ExecutionPolicy RemoteSigned …”

- **빌드: 이미지를 빌드하지 못했습니다. TLS 연결 확인 중 오류 발생: 호스트가 실행되고 있지 않습니다.**

	기본 Docker 호스트가 실행 중인지 확인합니다. [Docker 클라이언트 구성](./vs-azure-tools-docker-setup.md) 문서를 참조하세요.

- **볼륨 매핑을 찾을 수 없음**

	기본적으로 VirtualBox에서는 `C:\Users`를 `c:/Users`로 공유합니다. 프로젝트가 `c:\Users` 아래 없는 경우 수동으로 VirtualBox [공유 폴더](https://www.virtualbox.org/manual/ch04.html#sharedfolders)에 추가합니다.

- **Microsoft Edge를 기본 브라우저로 사용**

	Microsoft Edge 브라우저를 사용하는 경우 Edge에서 IP 주소를 보안되지 않은 상태로 간주하므로 사이트가 열리지 않을 수 있습니다. 이를 해결하려면 다음 단계를 수행합니다.
	1. Windows 실행 상자에서 `Internet Options`를 입력합니다.
	2. 나타나면 **인터넷 옵션**을 탭합니다. 
	2. **보안**을 탭합니다.
	3. **로컬 인트라넷** 영역을 선택합니다.
	4. **사이트**를 탭합니다. 
	5. 목록에서 가상 컴퓨터의 IP(이 경우 Docker 호스트)를 추가합니다. 
	6. Edge에서 페이지를 새로 고치면 사이트가 실행 중임이 표시됩니다. 
	7. 이 문제에 대한 자세한 내용은 Scott Hanselman의 블로그 게시물인 [Microsoft Edge can't see or open VirtualBox-hosted local web sites](http://www.hanselman.com/blog/FixedMicrosoftEdgeCantSeeOrOpenVirtualBoxhostedLocalWebSites.aspx)(Microsoft Edge에서 VirtualBox 호스트된 로컬 웹 사이트를 보거나 열 수 없음)를 참조하세요.

[0]: ./media/vs-azure-tools-docker-edit-and-refresh/add-docker-support.png
[1]: ./media/vs-azure-tools-docker-edit-and-refresh/docker-files-added.png

<!---HONumber=AcomDC_0330_2016-->