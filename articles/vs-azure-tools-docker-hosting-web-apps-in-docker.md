<properties
   pageTitle="Docker에서 웹 응용 프로그램 호스팅 | Microsoft Azure"
   description="Visual Studio를 사용하여 Docker 컨테이너의 웹앱에 호스트하는 방법을 알아봅니다. "
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
   ms.date="02/10/2016"
   ms.author="tarcher" />

# Docker에서 웹앱 호스팅

[Docker](https://www.docker.com/whatisdocker/)는 가상 컴퓨터와 몇 가지 유사한 점을 가지고 있는 간단한 컨테이너 엔진으로써, 응용 프로그램을 호스트하거나 서비스를 이용할 수 있습니다. Visual Studio는 Ubuntu, CoreOS, Windows에 Docker를 지원합니다.

Azure의 Ubuntu Linux 가상 컴퓨터(이후 Docker 호스트라고 함)에 ASP.NET 5 앱을 게시하는 **Docker용 Visual Studio 2015 Tools** 확장을 사용하는 방법을 보여 줍니다. Windows 컨테이너에 게시하는데 동일한 환경이 사용됩니다.

앱을 Docker 호스트에 게시한 후 Docker 명령줄 도구를 사용하여 앱을 게시한 컨테이너와 상호 작용할 수 있습니다.

## 새 Docker 컨테이너를 만들고 게시합니다.

다음 섹션에서는 새로운 ASP.NET 5 웹 응용 프로그램 프로젝트와 컨테이너 호스트를 만들고 Docker 컨테이너에서 웹앱 프로젝트를 빌드하고 실행합니다. 시작하려면 [Docker용 Visual Studio 2015 도구](https://aka.ms/DockerToolsForVS)를 다운로드하고 설치합니다.

### ASP.NET 5 웹 응용 프로그램 프로젝트를 추가합니다.

1. Visual Studio 메뉴에서 **파일 > 새로 만들기 > 프로젝트**를 선택합니다. 

1. **새 프로젝트** 대화 상자의 **템플릿** 섹션에서 **Visual C#** > **웹**을 선택합니다.

1. **ASP.NET 웹 응용 프로그램**을 선택하고 새 프로젝트 이름을 지정합니다. (이 문서의 스크린샷은 기본 이름 **WebApplication1**을 사용합니다).

1. **확인**을 탭합니다.

1. Docker에서 웹앱이 호스트/실행되기 때문에 **클라우드에 호스트** 확인란이 선택되어 있으면 선택을 취소하고 **확인**을 탭합니다.

  ![][0]

  일반적으로 이 시점에서 유용한 작업을 수행할 수 있도록 웹앱에 코드를 추가합니다. 이 예제에서는 간단하게 Docker에 집중하겠습니다. (기존 ASP.NET 5 웹앱 또한 사용하도록 선택할 수 있습니다.)

### 프로젝트 게시
프로젝트를 만들거나 기존 프로젝트를 연 후 다음 단계에 따라 프로젝트를 Azure의 Docker 컨테이너에 게시합니다.

1. Visual Studio 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 프로젝트의 상황에 맞는 메뉴에서 **게시**를 선택합니다.

1. **웹 게시** 대화 상자의 **게시 대상 선택** 섹션에서 **Docker 컨테이너**를 탭합니다.

    Docker 컨테이너 옵션이 표시되지 않으면 Docker용 Visual Studio 2015 Tools를 설치했는지, 그리고 이전 섹션에서 ASP.NET 5 웹 사이트 템플릿을 선택했는지 확인하세요.

    ![][1]

    **Docker 가상 컴퓨터 선택** 대화 상자에서 프로젝트를 게시하려는 Docker 호스트를 지정할 수 있습니다. 새 Docker 호스트를 생성하거나 Azure 또는 다른 곳에서 호스트 된 기존 가상 컴퓨터를 선택할 수 있습니다. 이 예제의 이후 부분에서 새 Azure Docker 호스트를 만들겠습니다.

1. Azure 계정에 이미 로그인했다면 5단계로 건너뜁니다. 계정에 로그인하지 않았다면 **계정 추가**를 탭합니다.

    ![][2]

1. **Visual Studio로 로그인** 대화 상자에서 Azure 구독에 대한 메일 계정을 입력하고 **계속**을 탭합니다.

1. **새로 만들기**를 탭하여 새 Azure Docker VM을 만들고 **확인**을 탭합니다.

    ![][3]

    기존 Docker 호스트도 사용할 수도 있습니다. 이렇게 하려면 **새로 만들기**를 탭하지 말고 **기존 Azure Docker 가상 컴퓨터** 드롭다운 목록에서 선택하세요. 목록은 단지 컨테이너 호스트에 제한되지 않고 Azure 테넌트의 모든 VM이 나열됩니다.

    대신, 사용자 지정 Docker 호스트에 게시를 선택할 수 있습니다. 자세한 내용은 이 항목의 뒤에 나오는 [사용자 지정 Docker 호스트 제공](#provide-a-custom-docker-host) 섹션을 참조하세요.

1. **Microsoft Azure에서 가상 컴퓨터 만들기** 대화 상자에 다음 정보를 입력하세요. 완료되면 **확인**을 탭합니다. 구성된 Docker 확장용 Linux 가상 컴퓨터를 만듭니다.

    ![][4]

    이제 Windows Server 2016 Technical Preiew 3(TP3)를 사용한 Windows 컨테이너 호스트 생성 옵션을 사용할 수 있습니다.

    ![][8]

	|속성 이름|설정|
	|---|---|
	|위치|이 설정을 로캘에 가장 가까운 지역으로 변경합니다.|
	|DNS 이름|가상 컴퓨터의 고유 이름을 입력합니다. Azure에서 이름이 승인되면 오른쪽에 흰색 확인 표시가 있는 녹색 원이 나타납니다. 이름이 승인되지 않으면, 흰색 X 표시가 있는 빨간색 원이 나타납니다. 이 경우에는 새로운 고유 이름을 입력합니다.|
	|이미지|Docker 호스트에서 사용할 OS 이미지가 있다면 선택합니다. 이 예제에서는 Ubuntu 서버 이미지를 선택합니다. (Windows Server 이미지는 사용 가능한 이미지 목록에서 현재 사용 가능합니다.)|
	|사용자 이름|가상 컴퓨터의 고유 사용자 이름을 입력합니다.|
	|암호|사용자의 암호를 입력하고 확인합니다.|
	|인증서 디렉터리 |Docker 인증서가 저장된 디렉터리를 지정합니다. 새 디렉터리를 만들거나 기존 디렉터리를 가리킬 수 있지만 기본 인증서 디렉터리(C:\\Users\\[*username*]\\.docker)를 사용하는 것이 좋습니다. 그렇지 않으면, 같은 호스트를 다른 프로젝트 또는 시스템에 재사용할 경우 인증 옵션이 자동으로 검색되지 않습니다.|

1. **인증서 디렉터리** 항목 옆의 줄임표(...)를 탭한 다음 Docker 인증서에 대한 새 디렉터리를 만들거나 기존 Docker 인증서 디렉터리로 이동합니다.

    Docker 인증서가 필요로 하는 가상 컴퓨터를 찾을 수 없는 경우, 느낌표 아이콘이 항목 옆에 나타납니다. 이 표시는 요구되는 인증서를 찾을 수 없다는 것을 알려주며, 이 표시가 계속되면 기존의 모든 인증서는 삭제되고 재생성됩니다.

1. VM 만들기를 시작하려면 **확인**을 탭합니다. 가상 컴퓨터를 Azure에 만드는 중이라는 메시지가 표시됩니다.

    Visual Studio는 ARM(Azure 리소스 관리자) 템플릿 파일, 매개 변수 파일 및 PowerShell 스크립트를 만들어 나중에 명령을 다시 실행할 수 있게 합니다.

    ![][7]

    Visual Studio는 작업의 진행 상태를 **출력** 창에 표시합니다. Visual Studio는 VM을 배포하도록 PowerShell 스크립트를 호출합니다. 스크립트는 Azure PowerShell cmdlet을 사용하여 Azure 리소스 그룹을 배포합니다. Docker 호스트를 수동으로 생성할 때와 마찬가지로 나중에 다른 PowerShell 스크립트는 게시하기 위해 문제 Docker 명령을 사용합니다.

    Docker 호스트 프로비전은 시간이 걸릴 수 있으므로 작업이 언제 완료되는지 보려면 Visual Studio 출력 창에서 상태를 확인하세요.

1. Azure에 Docker 호스트를 완벽히 프로비전하면, Azure 포털에서 계정을 확인할 수 있습니다. Azure 포털의 **가상 컴퓨터** 범주에서 새로운 가상 컴퓨터를 확인할 수 있습니다.

1. Docker 호스트가 준비되면 돌아가서 웹앱 프로젝트를 게시합니다. **솔루션 탐색기**의 웹 응용 프로그램 프로젝트 노드에 대한 상황에 맞는 메뉴에서 **게시**를 탭합니다. Visual Studio는 생성된 VM에 따라 게시 파일을 만듭니다.

1. **웹 게시** 대화 상자의 **연결** 탭에서 **연결 유효성 검사**을 탭하여 Docker 호스트가 준비되었는지 확인합니다. 제대로 연결되면 **게시**를 탭하여 웹앱을 게시합니다.

    처음으로 Docker 호스트에 앱을 게시할 때, Docker 파일에서 참조되는 기본 이미지 중 하나를 다운로드 하는 데 시간이 걸립니다(예: **FROM** *imagename*).

    참고로 Docker 파일은 운영 체제에 따라 다릅니다. 다른 OS에 다시 게시하려는 경우 Visual Studio가 대상 OS에 따라 새로운 기본 값을 생성할 수 있도록 Docker 파일의 이름을 다시 지어야합니다. 예를 들어 처음에 Linux 컨테이너에 게시하고 나중에 Windows에 게시하려는 경우 Docker 파일을 DockerLinux와 같은 의미 있는 이름으로 바꿔야 합니다. 그런 다음 Windows에 다시 게시할 때, Visual Studio는 Windows에 대한 기본 Docker 파일을 다시 생성합니다. 나중에 둘 중에 하나를 다시 게시할 때, OS에 대한 적절한 Docker 파일만 선택하면 됩니다.

## 사용자 지정 Docker 호스트를 제공합니다.

이전 섹션에서는 Azure에 호스트되는 Docker 가상 컴퓨터를 만드는 방법을 보았습니다. 그러나 다른 곳에 이미 기존 Docker 호스트가 있는 경우 Azure 대신 해당 호스트에 게시할 수 있습니다.

### 사용자 지정 Docker 호스트를 제공하는 방법

1. **Docker 가상 컴퓨터 선택** 대화 상자에서 **사용자 지정 Docker 호스트** 확인란을 선택하세요.

    ![][5]

1. **확인**을 탭합니다.

1. **웹 게시** 대화 상자에서 **CustomDockerHost** 섹션의 설정(예: 서버 URL, 이미지 이름, Docker 파일 위치, 호스트 및 컨테이너 포트 번호)에 값을 추가합니다.

    **Docker 고급 옵션** 섹션에서 Docker 명령 줄과 마찬가지로 인증 및 실행 옵션을 보거나 변경할 수 있습니다.

    ![][6]

1. 필요한 값을 모두 입력한 후 **연결 유효성 검사**를 탭하여 Docker 호스트에 대한 연결이 제대로 작동하는지 확인합니다.

1. 연결이 제대로 작동되면 **다음**을 탭하여 게시될 구성 요소의 목록을 보거나 **게시**를 탭하여 즉시 프로젝트를 게시할 수 있습니다.

## Docker 호스트 테스트

프로젝트가 Azure의 Docker 호스트에 게시되면 해당 설정을 선택하여 테스트할 수 있습니다. Docker 명령줄 도구는 Visual Studio 확장과 함께 설치되므로 Windows 명령 프롬프트에서 바로 Docker에 대한 명령을 실행할 수 있습니다.

아래는 Azure에 배포된 Docker 호스트와의 통신에 대한 과정입니다.

### Docker 호스트를 테스트하는 방법

1. Windows 명령 프롬프트를 엽니다.

1. Docker 호스트를 할당하고 환경 변수를 확인합니다. 명령 프롬프트에 다음 명령을 입력하여 실행할 수 있습니다. (Docker 호스트 이름을 *NameofAzureVM*으로 대체하고 만들어진 지역을 *Region*으로 대체합니다.)

    ```
    Set DOCKER_HOST=tcp://[NameOfAzureVM].[Region].cloudapp.azure.com:2376
    Set DOCKER_TLS_VERIFY=1
    ```

    이러한 환경 변수를 설정하면 실행하는 모든 Docker 명령에 대해 수동으로 이 정보를 지정할 필요가 없습니다.

1. 이제 Docker 호스트가 존재하고 제대로 작동하는지 테스트하는 다음과 같은 명령을 실행할 수 있습니다.

	|명령 줄|설명|
	|---|---|
	|`docker info`|Docker 버전 정보를 가져옵니다.|
	|`docker ps`|컨테이너 실행의 목록을 가져옵니다.|
	|`docker ps –a`|중지된 것을 포함한 컨테이너의 목록을 가져옵니다.|
	|`docker logs <Docker container name>`|지정된 컨테이너의 로그를 가져옵니다.|
	|`docker images`|이미지의 목록을 가져옵니다.|

    Docker 명령의 모든 목록을 보려면 명령 프롬프트에 `docker` 명령을 입력하고 **&lt;Enter>** 키를 누릅니다. 자세한 내용은 [Docker 명령줄](https://docs.docker.com/reference/commandline/cli/) 설명서를 참조하세요.

## 다음 단계

이제 Docker 호스트를 갖추고 있으므로 Docker 명령을 실행할 수 있습니다. Docker에 대한 자세한 내용은[Docker 설명서](https://docs.docker.com/) 및 [Docker 온라인 자습서](https://www.docker.com/tryit/)를 참조하세요.

Azure에서 Linux에 대한 Docker 가상 컴퓨터 확장을 사용하는 방법에 대한 자세한 내용은 [Azure에서 Linux에 대한 Docker 가상 컴퓨터 확장](/virtual-machines/virtual-machines-docker-vm-extension.md)을 참조하세요.

Visual Studio의 Dcoker 사용에 관한 문제는 [Visual Studio를 사용하여 Windows의 Docker 클라이언트 오류 문제 해결](vs-azure-tools-docker-troubleshooting-docker-errors.md)을 참조하세요.

[0]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796678.png
[1]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796679.png
[2]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796680.png
[3]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796681.png
[4]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796682.png
[5]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796683.png
[6]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796684.png
[7]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796685.png
[8]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796686.png

<!---HONumber=AcomDC_0211_2016-->