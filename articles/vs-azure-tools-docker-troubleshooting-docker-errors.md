---
title: "Visual Studio를 사용하여 Windows에서 Docker 클라이언트 오류 문제 해결 | Microsoft Docs"
description: "Visual Studio를 사용하여 웹앱을 만들고 Docker에 배포하는 경우 발생하는 문제를 해결합니다."
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 346f70b9-7b52-4688-a8e8-8f53869618d3
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
translationtype: Human Translation
ms.sourcegitcommit: 01c10d04606e15082c8842ad87f617703a00c903
ms.openlocfilehash: cbb376dae88d39e965838de74d90158691b59f90


---

# <a name="troubleshooting-visual-studio-docker-development"></a>Visual Studio Docker 개발 문제 해결

Docker 미리 보기를 위해 Visual Studio 도구로 작업할 경우, 미리 보기 특성으로 인해 몇 가지 문제가 발생할 수도 있습니다.
다음은 몇 가지 일반적인 문제 및 해결 방법입니다.  

## <a name="visual-studio-2017-rc"></a>Visual Studio 2017 RC

### <a name="linux-containers"></a>**Linux 컨테이너**

###  <a name="build-errors-occur-when-debugging-a-net-core-web-or-console-application"></a>.NET Core 웹 또는 콘솔 응용 프로그램을 디버그할 때 빌드 오류가 발생합니다.  

이 오류는 프로젝트가 Windows용 Docker에 있을 때 드라이브 공유와 관련이 있을 수 있습니다.  다음과 같은 오류가 나타날 수 있습니다.

```
The "PrepareForLaunch" task failed unexpectedly.
Microsoft.DotNet.Docker.CommandLineClientException: Creating network "webapplication13628050196_default" with the default driver
Building webapplication1
Creating webapplication13628050196_webapplication1_1
ERROR: for webapplication1  Cannot create container for service webapplication1: C: drive is not shared. Please share it in Docker for Windows Settings
```
이 오류를 해결하려면 Windows용 Docker 시스템 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 "설정..."을 선택합니다.  "공유 드라이브" 탭을 클릭하고 프로젝트가 있는 드라이브 문자를 공유합니다.

### <a name="windows-containers"></a>**Windows 컨테이너**

다음은 Windows 컨테이너에서 .NET Framework 웹 및 콘솔 응용 프로그램을 디버그할 때의 문제 해결과 관련이 있습니다.

### <a name="pre-requisites"></a>필수 구성 요소

1. .NET Core 및 Docker(미리 보기) 워크로드가 설치된 Visual Studio 2017 RC(또는 이상)
2. 최신 Windows 패치가 설치된 Windows 10 1주년 업데이트로 업데이트
3. Windows용 Docker(베타) - https://docs.docker.com/docker-for-windows/(버전 1.12.2-beta28 7813 이상)
4. Windows용 Docker 시스템 트레이 아이콘에서 "Windows 컨테이너로 전환”을 선택합니다.  컴퓨터가 다시 시작되면 이 설정이 유지되는지 확인합니다.

### <a name="clicking-docker-debug-project-results-in-the-error--client-version-122-is-too-old--minimum-supported-api-version-is-124-please-upgrade-your-client-to-a-newer-version"></a>**Docker: 프로젝트 디버그**를 클릭하면 "클라이언트 버전이 1.22가 너무 오래 되었습니다.  지원되는 최소 API 버전은 1.24입니다. 클라이언트를 최신 버전으로 업그레이드하세요." 오류가 표시됩니다.

Windows용 Docker 버전 1.13 RC2 beta31(9123) 이상을 사용하려면 Docker Compose 버전 '2.1을 사용해야 합니다.   이 문제를 해결하려면 프로젝트 내의 docker compose*.yml 파일에서 버전 '2'가 나오는 모든 경우를 변경합니다. Visual Studio가 프로젝트에 추가하는 기본 템플릿은 이후 출시되는 도구에서 업데이트될 예정입니다. 

### <a name="console-output-does-not-appear-in-visual-studios-output-window-while-debugging-a-console-application"></a>콘솔 응용 프로그램을 디버그하는 동안 Visual Studio의 출력 창에 콘솔 출력이 나타나지 않습니다.

이것은 현재 이 시나리오에 맞게 디자인되지 않은 Visual Studio 디버거(msvsmon.exe)의 알려진 문제입니다.  이후 릴리스에서 이에 대한 지원이 제공될 것으로 예상됩니다.  Visual Studio에서 콘솔 응용 프로그램의 출력을 보려면 "디버그하지 않고 시작"에 해당하는 "Docker:프로젝트 시작"을 사용합니다.

### <a name="debugging-web-applications-with-the-release-configuration-fails-with-403-forbidden-error"></a>릴리스 구성을 사용하여 웹 응용 프로그램을 디버그하면 (403) 사용 권한 없음 오류를 나타내며 실패합니다.

이 문제를 해결하려면 솔루션에서 web.release.config 파일을 열고 다음 줄을 주석 처리하거나 삭제합니다.

```
<compilation xdt:Transform="RemoveAttributes(debug)" />
```

### <a name="when-switching-to-windows-containers-you-could-potentially-see-an-error-dialog-stating-error-response-from-daemon-io-timeout"></a>Windows 컨테이너로 전환하면 "디먼의 오류 응답: I/O 시간 초과" 오류 대화 상자가 표시될 수 있습니다.

Windows용 Docker의 이 문제는 https://github.com/docker/for-win/issues/178에서 추적할 수 있습니다.


## <a name="visual-studio-2015"></a>Visual Studio 2015

### <a name="linux-containers"></a>**Linux 컨테이너**

### <a name="unable-to-validate-volume-mapping"></a>볼륨 매핑의 유효성을 검사할 수 없음
볼륨 매핑의 경우 응용 프로그램의 소스 코드 및 이진 파일을 컨테이너에 있는 앱 폴더와 공유해야 합니다.  특정 볼륨 매핑은 docker-compose.dev.debug.yml 및 docker-compose.dev.release.yml 파일 내에 포함되어 있습니다. 호스트 컴퓨터에서 파일이 변경되면 컨테이너는 비슷한 폴더 구조에서 이러한 변경 내용을 반영합니다.

볼륨 매핑을 사용하지 않으려면 Windows용 Docker "모비" 트레이 아이콘에서 **설정...**을 열고 **드라이브 공유** 탭을 선택합니다.  %USERPROFILE%이 상주하는 드라이브 문자뿐만 아니라 프로젝트를 호스트하는 드라이브 문자를 선택한 다음 **적용**을 클릭하여 공유하도록 합니다.

볼륨 매핑이 작동하는지를 테스트하려면 하나 이상의 드라이브가 공유된 경우 Visual Studio 내에서 다시 빌드 또는 F5를 사용하거나 명령 프롬프트에서 다음을 사용해 봅니다.

*Windows 명령 프롬프트에서*

> [!Note]
> 이 예제에서는 사용자 폴더가 "C" 드라이브에 있으며 공유되었다고 가정합니다.
> 다른 드라이브를 공유하는 경우 필요에 따라 업데이트합니다.

```
docker run -it -v /c/Users/Public:/wormhole busybox
```

*Linux 컨테이너에서*

```
/ # ls
```

Users/Public 폴더의 디렉토리 목록이 표시됩니다.
아무 파일도 표시되지 않고, /c/Users/Public 폴더는 비어 있지 않다면, 볼륨 매핑이 제대로 구성되지 않았습니다.

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

`/c/Users/Public` 디렉토리의 내용을 보려면 웜홀 디렉토리로 변경합니다.

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     desktop.ini
Documents        Libraries        Pictures
/wormhole #
```

> [!Note]
> Linux VM에서 작업할 때 컨테이너 파일 시스템은 대/소문자를 구분합니다.

##<a name="build--prepareforbuild-task-failed-unexpectedly"></a>빌드 : "PrepareForBuild" 태스크가 예기치 않게 실패했습니다.

Microsoft.DotNet.Docker.CommandLine.ClientException: 연결하려는 동안 오류가 발생했습니다.

기본 Docker 호스트가 실행 중인지 확인합니다. 명령 프롬프트를 열고 다음을 실행합니다.

```
docker info
```

오류를 반환하는 경우 **Windows용 Docker** 데스크톱 앱을 시작하려고 시도합니다.  데스크톱 앱을 실행하는 경우 트레이에 **모비** 아이콘이 표시되어야 합니다. 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 **설정**을 엽니다.  **다시 설정** 탭 및 **Docker 다시 시작..**을 차례로 클릭합니다.

##<a name="manually-upgrading-from-version-031-to-040"></a>0.31에서 0.40 버전으로 수동 업그레이드


1. 프로젝트 백업
1. 프로젝트에서 다음 파일을 삭제합니다.

    ```
      Dockerfile
      Dockerfile.debug
      DockerTask.ps1
      docker-compose-yml
      docker-compose.debug.yml
      .dockerignore
      Properties\Docker.props
      Properties\Docker.targets
    ```

1. 솔루션을 닫고 .xproj 파일에서 다음 줄을 제거합니다.

    ```
      <DockerToolsMinVersion>0.xx</DockerToolsMinVersion>
      <Import Project="Properties\Docker.props" />
      <Import Project="Properties\Docker.targets" />
    ```

1. 솔루션 다시 열기
1. Properties\launchSettings.json 파일에서 다음 줄을 제거합니다.

    ```
      "Docker": {
        "executablePath": "%WINDIR%\\System32\\WindowsPowerShell\\v1.0\\powershell.exe",
        "commandLineArgs": "-ExecutionPolicy RemoteSigned .\\DockerTask.ps1 -Run -Environment $(Configuration) -Machine '$(DockerMachineName)'"
      }
    ```

1. publishOptions의 project.json에서 Docker에 관련된 다음 파일을 제거합니다.

    ```
    "publishOptions": {
      "include": [
        ...
        "docker-compose.yml",
        "docker-compose.debug.yml",
        "Dockerfile.debug",
        "Dockerfile",
        ".dockerignore"
      ]
    },
    ```

1. 이전 버전을 제거하고 Docker 도구 0.40을 설치한 다음 ASP.Net Core Web 또는 콘솔 응용 프로그램에 대한 상황에 맞는 메뉴에서 다시 **Docker 지원 추가**를 클릭합니다. 이렇게 하면 프로젝트에 새 필수 Docker 아티팩트가 추가됩니다.

## <a name="an-error-dialog-occurs-when-attempting-to-add-docker-support-or-debug-f5-an-aspnet-core-application-in-a-container"></a>컨테이너에서 **Docker 지원 추가** 또는 ASP.NET Core 응용 프로그램 디버그(F5)를 시도할 때 오류 대화 상자가 나타납니다.

확장을 제거하고 설치한 후에 Visual Studio의 MEF(Managed Extensibility Framework) 캐시가 손상된 것을 발견할 수 있습니다. 이 경우에는 Docker 지원을 추가 및/또는 ASP.NET Core 응용 프로그램을 실행 또는 디버그(F5)할 때 다양한 오류 대화 상자가 나타날 수 있습니다. 임시 해결 방법으로 MEF 캐시를 삭제하고 다시 생성하려면 다음 단계를 실행합니다.

1. Visual Studio의 모든 인스턴스 닫기
1. %USERPROFILE%\AppData\Local\Microsoft\VisualStudio\14.0\ 열기
1. 다음 폴더 삭제
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. Visual Studio 열기
1. 시나리오 다시 시도



<!--HONumber=Dec16_HO2-->


