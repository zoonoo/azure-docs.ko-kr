---
title: 로컬 Docker 컨테이너에서 앱 디버깅 | Microsoft Docs
description: 로컬 Docker 컨테이너에서 실행 중인 앱을 수정하고, 편집 및 새로 고침을 통해 컨테이너를 새로 고치고, 디버깅 중단점을 설정하는 방법을 알아봅니다.
services: container-service
author: ghogen
manager: douge
ms.assetid: 480e3062-aae7-48ef-9701-e4f9ea041382
ms.service: multiple
ms.topic: article
ms.workload: multiple
ms.date: 09/11/2018
ms.author: ghogen
ms.openlocfilehash: 8844992ffbed2d57ad54a5f0e4a2d0b05c1d277a
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50968731"
---
# <a name="debugging-apps-in-a-local-docker-container"></a>로컬 Docker 컨테이너에서 앱 디버깅
## <a name="overview"></a>개요
Visual Studio 2017은 Docker 컨테이너에서 로컬로 응용 프로그램을 개발하고 유효성을 검사하는 일관된 방법을 제공합니다.
코드를 변경할 때마다 컨테이너를 다시 시작할 필요가 없습니다.
이 문서에서는 "편집 및 새로 고침" 기능을 사용하여 로컬 Docker 컨테이너에서 ASP.NET Core 웹앱을 시작하고 필요한 내용을 변경한 다음 브라우저를 새로 고쳐 변경 내용을 확인하는 방법을 설명합니다.
또한 이 문서에서는 디버깅을 위한 중단점 설정 방법도 보여 줍니다.

## <a name="prerequisites"></a>필수 조건
다음과 같은 도구를 설치해야 합니다.

* 웹 개발 워크로드가 설치된 [Visual Studio 2017](https://www.visualstudio.com/downloads/)

로컬에서 Docker 컨테이너를 실행하려면 로컬 Docker 클라이언트가 필요합니다.
[Docker 도구 상자](https://www.docker.com/products/docker-toolbox)는 Hyper-V를 비활성화한 후 사용할 수 있습니다. 또는 [Windows용 Docker](https://www.docker.com/get-docker)(Hyper-V를 사용하고 Windows 10을 필요로 함)를 사용할 수 있습니다.

Docker 도구 상자를 사용하는 경우 [Docker 클라이언트를 구성](vs-azure-tools-docker-setup.md)해야 합니다.

## <a name="1-create-a-web-app"></a>1. 웹앱 만들기
[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-edit-your-code-and-refresh"></a>2. 코드 편집 및 새로 고침
변경을 신속하게 반복할 수 있도록, 애플리케이션을 컨테이너에서 시작하고, IIS Express에서 하는 것처럼 변경 내용을 보며, 계속해서 변경해 나갈 수 있습니다.

1. 솔루션 구성을 `Debug`로 설정하고 **&lt;CTRL + F5>** 키를 눌러 Docker 이미지를 빌드하고 로컬에서 실행합니다.

    컨테이너 이미지가 빌드되고 Docker 컨테이너에서 실행되면 Visual Studio는 기본 브라우저에서 웹앱을 시작하려고 합니다.
    Microsoft Edge 브라우저를 사용하거나 달리 오류가 있는 경우 [문제 해결](vs-azure-tools-docker-troubleshooting-docker-errors.md) 섹션을 참조하세요.
2. 정보 페이지로 이동하며 정보 페이지에서 변경을 수행하게 됩니다.
3. Visual Studio로 돌아가서 `Views\Home\About.cshtml`을 엽니다.
4. 파일의 끝에 다음 HTML 콘텐츠를 추가하고 변경 내용을 저장합니다.

    ```
    <h1>Hello from a Docker Container!</h1>
    ```
5. 출력 창을 보는 가운데 .NET 빌드가 완료되고 이러한 줄이 표시될 때 브라우저로 전환하고 정보 페이지를 새로 고칩니다.

   ```
   Now listening on: http://*:80
   Application started. Press Ctrl+C to shut down
   ```

6. 변경 내용이 적용되었습니다.

## <a name="3-debug-with-breakpoints"></a>3. 중단점으로 디버깅
흔히 변경은 Visual Studio의 디버깅 기능을 활용한 추가적인 검사를 필요로 합니다.

1. Visual Studio로 돌아가서 `About.cshtml.cs`
2. OnGet() 메서드의 내용을 다음과 같이 바꿉니다.

   ```cs
       Message = "Your application description page from within a Container";
   ```

3. 코드 줄의 왼쪽에 중단점을 설정합니다.
4. **&lt;F5>** 키를 눌러 디버깅을 시작합니다.
5. 중단점에 도달하려면 정보 페이지로 이동합니다.
6. Visual Studio로 전환하여 중단점을 보고, 메시지의 값을 검사합니다.

   ![][2]

## <a name="summary"></a>요약
Visual Studio 2017의 Docker 지원을 통해 Docker 컨테이너 내에서 개발하는 프로덕션 현실감과 함께 로컬에서 작업하는 생산성을 얻을 수 있습니다.

## <a name="troubleshooting"></a>문제 해결
[Visual Studio Docker 개발 문제 해결](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## <a name="more-about-docker-with-visual-studio-windows-and-azure"></a>Visual Studio, Windows 및 Azure와 함께 Docker에 대해 자세히 알아보기
* [Visual Studio에서 컨테이너 개발](/visualstudio/containers) - 컨테이너 개발 허브 페이지
* [Azure Pipelines용 Docker Integration](http://aka.ms/dockertoolsforvsts) - Docker 컨테이너 빌드 및 배포
* [Visual Studio Code용 Docker 도구](http://aka.ms/dockertoolsforvscode) - 향후 제공될 더 많은 E2E 시나리오와 함께, docker 파일을 편집하기 위한 언어 서비스
* [Windows 컨테이너 정보](http://aka.ms/containers)- Windows Server 및 Nano Server 정보
* [Azure Container Service](https://azure.microsoft.com/services/kubernetes-service/) - [Azure Kubernetes Service 설명서](/azure/aks)

## <a name="various-docker-tools"></a>다양한 Docker 도구
[일부 뛰어난 docker 도구 (Steve Lasker의 블로그)](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## <a name="good-articles"></a>좋은 문서
[NGINX의 마이크로 서비스 소개](https://www.nginx.com/blog/introduction-to-microservices/)

## <a name="presentations"></a>프레젠테이션
* [Steve Lasker: VS 라이브 Las Vegas 2016 - Docker e2e](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
* [ASP.NET Core @ 빌드 2016 - Where You At Demo 소개](https://channel9.msdn.com/Events/Build/2016/B810)
* [컨테이너에서 .NET 앱 개발, Channel 9](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png
