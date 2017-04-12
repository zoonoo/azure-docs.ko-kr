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
ms.sourcegitcommit: 649cc1a78f3a9f343533cb18fb7d763e4f9ea196
ms.openlocfilehash: 89fa04a1107b6abb49aefd68066443717ac9b731
ms.lasthandoff: 02/04/2017


---

# <a name="troubleshoot-visual-studio-docker-development"></a>Visual Studio Docker 개발 문제 해결

Visual Studio Tools for Docker 미리 보기로 작업하는 경우 미리 보기의 특성으로 인해 몇 가지 문제가 발생할 수도 있습니다.
다음은 몇 가지 일반적인 문제 및 해결 방법입니다.  

## <a name="visual-studio-2017-rc"></a>Visual Studio 2017 RC

### <a name="linux-containers"></a>**Linux 컨테이너**

####  <a name="build-errors-occur-when-debugging-a-net-core-web-or-console-application"></a>.NET Core 웹 또는 콘솔 응용 프로그램을 디버그할 때 빌드 오류가 발생합니다.  

이 오류는 프로젝트가 Windows용 Docker에 있을 때 드라이브 공유와 관련이 있을 수 있습니다.  다음과 같은 오류가 나타날 수 있습니다.

```
The "PrepareForLaunch" task failed unexpectedly.
Microsoft.DotNet.Docker.CommandLineClientException: Creating network "webapplication13628050196_default" with the default driver
Building webapplication1
Creating webapplication13628050196_webapplication1_1
ERROR: for webapplication1  Cannot create container for service webapplication1: C: drive is not shared. Please share it in Docker for Windows Settings
```
이 문제를 해결하려면:

1. 알림 영역에서 **Windows용 Docker**를 마우스 오른쪽 단추로 클릭한 다음 **설정**을 선택합니다.  
2. **공유 드라이브**를 선택하고 프로젝트가 있는 드라이브를 공유합니다.

### <a name="windows-containers"></a>**Windows 컨테이너**

다음 문제는 Windows 컨테이너에서 .NET Framework 웹 및 콘솔 응용 프로그램 디버깅과 관련이 있습니다.

#### <a name="prerequisites"></a>필수 조건

1. .NET Core 및 Docker 미리 보기 워크로드가 있는 Visual Studio 2017 RC(또는 이상)를 설치해야 합니다.
2. 최신 Windows 업데이트 패치가 있는 Windows 10 1주년 업데이트 특히 [KB3194798](https://support.microsoft.com/en-us/help/3194798/cumulative-update-for-windows-10-version-1607-and-windows-server-2016-october-11,-2016)을 설치해야 합니다. 
3. [Windows용 Docker](https://docs.docker.com/docker-for-windows/)(빌드 1.13.0 이상)를 설치해야 합니다.
4. **Windows 컨테이너로 전환**을 선택해야 합니다. 알림 영역에서 **Windows용 Docker**를 클릭한 다음 **Windows 컨테이너로 전환**을 선택합니다. 컴퓨터가 다시 시작되면 이 설정이 유지되는지 확인합니다.

#### <a name="console-output-does-not-appear-in-visual-studios-output-window-while-debugging-a-console-application"></a>콘솔 응용 프로그램을 디버그하는 동안 Visual Studio의 출력 창에 콘솔 출력이 나타나지 않습니다.

이것은 현재 이 시나리오에 맞게 디자인되지 않은 Visual Studio 디버거(msvsmon.exe)의 알려진 문제입니다. 이 시나리오에 대한 지원은 이후 버전에 포함될 수 있습니다. Visual Studio에서 콘솔 응용 프로그램의 출력을 보려면 **디버그하지 않고 시작**에 해당하는 **Docker: 프로젝트 시작**을 사용합니다.

#### <a name="debugging-web-applications-with-the-release-configuration-fails-with-403-forbidden-error"></a>릴리스 구성을 사용하여 웹 응용 프로그램을 디버그하면 (403) 사용 권한 없음 오류를 나타내며 실패합니다.

이 문제를 해결하려면 솔루션에서 web.release.config를 열고 다음 줄을 주석 처리하거나 삭제합니다.

```
<compilation xdt:Transform="RemoveAttributes(debug)" />
```

## <a name="visual-studio-2015"></a>Visual Studio 2015

### <a name="linux-containers"></a>**Linux 컨테이너**

#### <a name="unable-to-validate-volume-mapping"></a>볼륨 매핑의 유효성을 검사할 수 없음
볼륨 매핑의 경우 응용 프로그램의 소스 코드 및 이진 파일을 컨테이너에 있는 앱 폴더와 공유해야 합니다.  특정 볼륨 매핑은 docker-compose.dev.debug.yml 및 docker-compose.dev.release.yml 내에 포함되어 있습니다. 호스트 컴퓨터에서 파일이 변경되면 컨테이너는 비슷한 폴더 구조에서 이러한 변경 내용을 반영합니다.

볼륨 매핑을 활성화하려면:

1. 알림 영역에서 **모비**를 클릭하고 **설정**을 선택합니다.
2. **공유 드라이브**를 선택합니다.
3. 프로젝트를 호스팅하는 드라이브와 %USERPROFILE%이 있는 드라이브를 선택합니다.
4. **Apply**를 클릭합니다.

볼륨 매핑이 작동하는지를 테스트하려면 하나 이상의 드라이브가 공유된 후 Visual Studio 내에서 다시 빌드하고 F5를 선택하거나 명령 프롬프트에서 다음 코드를 실행합니다.

> [!NOTE]
> 이 예제에서는 사용자 폴더가 드라이브 C에 있으며 공유되었다고 가정합니다.
> 다른 드라이브를 공유하는 경우 필요에 따라 수정합니다.

```
docker run -it -v /c/Users/Public:/wormhole busybox
```

Linux 컨테이너에서 다음 코드를 실행합니다.

```
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
Desktop          Host             NuGet.Config     desktop.ini
Documents        Libraries        Pictures
/wormhole #
```

> [!NOTE]
> Linux VM에서 작업하는 경우 컨테이너 파일 시스템은 대/소문자를 구분합니다.

## <a name="build-prepareforbuild-task-failed-unexpectedly"></a>빌드 : "PrepareForBuild" 태스크가 예기치 않게 실패했습니다.

Microsoft.DotNet.Docker.CommandLine.ClientException: 연결하려는 동안 오류가 발생했습니다.

기본 Docker 호스트가 실행 중인지 확인합니다. 명령 프롬프트를 열고 다음을 실행합니다.

```
docker info
```

오류를 반환하는 경우 **Windows용 Docker** 데스크톱 앱을 시작하려고 시도합니다. 데스크톱 앱을 실행하는 경우 알림 영역에 **모비**가 표시되어야 합니다. **모비**를 마우스 오른쪽 단추로 클릭하고 **설정**을 엽니다. **다시 설정**을 클릭한 다음 Docker를 다시 시작합니다.

## <a name="an-error-dialog-occurs-when-attempting-to-add-docker-support-or-debug-f5-an-aspnet-core-application-in-a-container"></a>컨테이너에서 Docker 지원 추가 또는 ASP.NET Core 응용 프로그램 디버그(F5)를 시도할 때 오류 대화 상자가 나타납니다.

확장을 제거하고 설치한 후에 Visual Studio의 MEF(Managed Extensibility Framework) 캐시가 손상될 수 있습니다. 이 경우에는 Docker 지원을 추가 및/또는 ASP.NET Core 응용 프로그램을 실행 또는 디버그(F5)할 때 다양한 오류 메시지가 나타날 수 있습니다. 임시 해결 방법으로 MEF 캐시를 삭제하고 다시 생성하려면 다음 단계를 사용합니다.

1. Visual Studio의 모든 인스턴스를 닫습니다.
1. %USERPROFILE%\AppData\Local\Microsoft\VisualStudio\14.0\을 엽니다.
1. 다음 폴더를 삭제합니다.
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. Visual Studio를 엽니다.
1. 시나리오를 다시 시도합니다.

