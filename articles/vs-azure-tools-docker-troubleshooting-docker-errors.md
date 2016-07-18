<properties
   pageTitle="Visual Studio를 사용하여 Windows에서 Docker 클라이언트 오류 문제해결 | Microsoft Azure"
   description="Visual Studio를 사용하여 웹앱을 만들고 Docker에 배포하는 경우 발생하는 문제를 해결합니다."
   services="azure-container-service"
   documentationCenter="na"
   authors="allclark"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="allclark" />

# Visual Studio Docker 개발 문제 해결

Docker 미리 보기를 위해 Visual Studio 도구로 작업할 경우, 미리 보기 특성으로 인해 몇 가지 문제가 발생할 수도 있습니다. 다음은 몇 가지 일반적인 문제 및 해결 방법입니다.

##Docker 지원에 대한 Program.cs를 구성하지 못했습니다.

Docker 지원을 추가할 때, `.UseUrls(Environment.GetEnvironmentVariable("ASPNETCORE_SERVER.URLS"))`를 WebHostBuilder()에 추가해야 합니다. Program.cs `Main()` 함수나 새 클래스 WebHostBuilder를 찾을 수 없는 경우, 경고가 표시됩니다. docker 컨테이너 내에서 실행 시 localhost 너머에서 들어오는 트래픽을 Kestrel이 수신하도록 설정하는데 `.UseUrls()`가 필요합니다. 완료 시 일반적인 코드는 다음과 같이 표시됩니다.

```
public class Program
{
    public static void Main(string[] args)
    {
        var host = new WebHostBuilder()
            .UseUrls(Environment.GetEnvironmentVariable("ASPNETCORE_URLS") ?? String.Empty)
            .UseKestrel()
            .UseContentRoot(Directory.GetCurrentDirectory() ?? "")
            .UseIISIntegration()
            .UseStartup<Startup>()
            .Build();

        host.Run();
    }
}
```

UseUrls()은 들어오는 URL 트래픽을 WebHost가 수신하도록 구성했습니다. [Visual Studio용 Docker 도구](http://aka.ms/DockerToolsForVS)는 dockerfile.debug/release 모드에서 환경 변수를 다음과 같이 구성합니다.

```
# Configure the listening port to 80
ENV ASPNETCORE_SERVER.URLS http://*:80
```

## 볼륨 매핑이 작동하지 않습니다.
편집 및 새로 고침 기능을 사용하도록 설정하려면, 프로젝트의 소스 코드를 컨테이너 내의 .app 폴더와 공유하도록 볼륨 매핑을 구성합니다. 호스트 컴퓨터에서 파일들이 변경될 때, 컨테이너 /app 디렉토리는 동일한 디렉토리를 사용합니다. Docker-compose.debug.yml에서 다음 구성은 볼륨 매핑을 사용하도록 설정합니다.

```
volumes:
    - ..:/app
```

볼륨 매핑이 작동하는지 테스트 하려면, 다음 명령을 실행해 보세요.

**Windows에서**

```
docker run -it -v /c/Users/Public:/wormhole busybox
/ # ls
```

Users/Public 폴더의 디렉토리 목록이 표시됩니다. 아무 파일도 표시되지 않고, /c/Users/Public 폴더는 비어 있지 않다면, 볼륨 매핑이 제대로 구성되지 않았습니다.

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

`/c/Users/Public` 디렉토리의 내용을 보려면 웜홀 디렉토리로 변경합니다.

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     a.txt
Documents        Libraries        Pictures         desktop.ini
/wormhole #
```

**참고:** *Linux VM에서 작업할 때, 컨테이너 파일 시스템은 대/소문자를 구분합니다.*

내용을 볼 수 없는 경우 다음을 시도합니다.

**윈도우용 Docke 베타**
- 시스템 트레이에 모비 아이콘이 있는지 살피고, 아이콘이 흰색이고 기능 중인지 확인하여 Windows용 Docker 데스크톱 앱이 실행 중인지 확인합니다.
- 시스템 트레이에 있는 모비 아이콘을 마우스 오른쪽 단추로 클릭하여, 설정을 선택하고 **공유 드라이브 관리...**를 클릭하여 볼륨 매핑이 구성되어 있는지 확인합니다.

**VirtualBox가 있는 Docker 도구 상자**

기본적으로 VirtualBox에서는 `C:\Users`를 `c:/Users`로 공유합니다. 가능한 경우 프로젝트를 이 디렉토리 아래로 옮깁니다. 그렇지 않은 경우, VirtualBox [공유 폴더](https://www.virtualbox.org/manual/ch04.html#sharedfolders)에 수동으로 추가할 수 있습니다.
	
##빌드: 이미지를 빌드하지 못했습니다. TLS 연결 확인 중 오류 발생: 호스트가 실행되고 있지 않습니다.

- 기본 Docker 호스트가 실행 중인지 확인합니다. [Docker 클라이언트 구성](./vs-azure-tools-docker-setup.md) 문서를 참조하세요.

##Microsoft Edge를 기본 브라우저로 사용

Microsoft Edge 브라우저를 사용하는 경우 Edge에서 IP 주소를 보안되지 않은 상태로 간주하므로 사이트가 열리지 않을 수 있습니다. 이를 해결하려면 다음 단계를 수행합니다.

1. **인터넷 옵션**으로 이동합니다.
    - Windows 10에서는 Windows 실행 상자에 `Internet Options`을 입력하면 됩니다.
    - Internet Explorer에서는 **설정** 메뉴로 이동하고 **인터넷 옵션**을 선택할 수 있습니다.
1. 나타나면 **인터넷 옵션**을 선택합니다.
1. **보안** 탭을 선택합니다.
1. **로컬 인트라넷** 영역을 선택합니다.
1. **사이트**를 선택합니다.
1. 목록에서 가상 컴퓨터의 IP(이 경우 Docker 호스트)를 추가합니다.
1. Edge에서 페이지를 새로 고치면 사이트가 실행 중임이 표시됩니다.
1. 이 문제에 대한 자세한 내용은 Scott Hanselman의 블로그 게시물인 [Microsoft Edge can't see or open VirtualBox-hosted local web sites](http://www.hanselman.com/blog/FixedMicrosoftEdgeCantSeeOrOpenVirtualBoxhostedLocalWebSites.aspx)(Microsoft Edge에서 VirtualBox 호스트된 로컬 웹 사이트를 보거나 열 수 없음)를 참조하세요.

##문제 해결 버전 0.15 이전


###앱을 실행하면 PowerShell이 열리고 오류를 표시한 다음 닫힙니다. 브라우저 페이지가 열리지 않습니다.

이는 `docker-compose-up` 중 오류일 수 있습니다. 오류를 확인하려면 다음 단계를 수행합니다.

1. `Properties\launchSettings.json` 파일을 엽니다.
1. Docker 항목을 찾습니다.
1. 다음과 같이 시작하는 줄을 찾습니다.

    ```
    "commandLineArgs": "-ExecutionPolicy RemoteSigned …”
    ```
	
1. `-noexit` 매개 변수를 추가하면 이제 줄이 다음과 유사합니다. 이렇게 하면 PowerShell이 계속 열려 있어서 오류를 확인할 수 있습니다.

    ```
	"commandLineArgs": "-noexit -ExecutionPolicy RemoteSigned …”
    ```

<!---HONumber=AcomDC_0706_2016-->