<properties
   pageTitle="원격 Docker 호스트에 ASP.NET 컨테이너 배포 | Microsoft Azure"
   description="Visual Studio Tools for Docker를 사용하여 Azure Docket 호스트 컴퓨터에서 실행 중인 Docker 컨테이너에 ASP.NET 5 웹 앱을 게시하는 방법에 대해 설명합니다."   
   services="visual-studio-online"
   documentationCenter=".net"
   authors="tomarcher"
   manager="douge"
   editor=""/>

<tags
   ms.service="visual-studio-online"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/08/2016"
   ms.author="tarcher"/>

# 원격 Docker 호스트에 ASP.NET 컨테이너 배포

## 개요
Docker는 가상 컴퓨터와 몇 가지 측면에서 비슷하며 응용 프로그램과 서비스를 호스트하는 데 사용할 수 있는 간단한 컨테이너 엔진입니다. Visual Studio는 Ubuntu, CoreOS, Windows에 Docker를 지원합니다. 이 자습서에서는 [Visual Studio 2015 Tools for Docker](http://aka.ms/DockerToolsForVS) 확장을 사용하여 Azure에서 Docker 호스트에 ASP.NET 5 앱을 게시하는 단계에 대해 설명합니다.

## 1\. 필수 조건
이 자습서를 완료하려면 다음 작업을 수행해야 합니다.

- [Azure에서 docker-machine을 사용하는 방법](./virtual-machines/virtual-machines-linux-classic-docker-machine.md)의 설명에 따라 Azure Docker 호스트 VM을 만듭니다.
- [Visual Studio 2015](https://www.visualstudio.com/ko-KR/downloads/download-visual-studio-vs.aspx)를 설치합니다.
- [Visual Studio 2015 Tools for Docker - Preview](http://aka.ms/DockerToolsForVS)를 설치합니다.

## 2\. ASP.NET 5 웹 앱 만들기
다음 단계에서는 이 자습서에서 사용할 기본적인 ASP.NET 5 앱을 만드는 과정을 안내합니다.

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## 3\. Docker 지원 추가

[AZURE.INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## 4\. 원격 Docker 호스트를 가리키도록 지정

1.  Visual Studio **솔루션 탐색기**에서 **속성** 폴더를 찾아 확장합니다.
1.  *Docker.props* 파일을 엽니다.

    ![Docker.props 파일 열기][0]

1.  **DockerMachineName**의 값을 원격 Docker 호스트의 이름으로 변경합니다. 원격 Docker 호스트의 이름을 모르는 경우 Windows PowerShell 프롬프트에서 ```docker-machine ls```를 실행하여 원하는 호스트에 대해 **Name** 열 아래에 나열된 값을 사용합니다.

    ![Docker 컴퓨터 이름 변경][1]

1.  Visual Studio를 다시 시작합니다.

## 5\. Azure Docker 호스트 끝점 구성
나중에 브라우저에서 앱을 볼 수 있도록 Visual Studio에서 Azure로 응용 프로그램을 배포하기 전에 끝점 80을 Docker 호스트 가상 컴퓨터에 추가합니다. [Azure 클래식 포털](http://go.microsoft.com/fwlink/?LinkID=213885)이나 Windows PowerShell을 통해 이 작업을 수행할 수 있습니다.

- **[Azure 클래식 포털](http://go.microsoft.com/fwlink/?LinkID=213885)을 사용하여 Azure Docker 호스트 끝점 구성**

    1.  [Azure 클래식 포털](http://go.microsoft.com/fwlink/?LinkID=213885)로 이동합니다. 
    
    1.  **가상 컴퓨터**를 탭합니다.
    
    1.  Docker 호스트 가상 컴퓨터를 선택합니다.
    
    1.  **끝점** 탭을 탭합니다.
    
    1.  페이지 맨 아래에 있는 **추가**를 탭합니다.
    
    1.  지침에 따라 기본적으로 배포 스크립트에서 사용되는 포트 80을 표시합니다.

- **Windows PowerShell을 사용하여 Azure Docker 호스트 끝점 구성**

    1. Windows PowerShell을 엽니다.
    1. Windows PowerShell 프롬프트에서 다음 명령을 입력합니다. 꺾쇠 괄호 안의 값은 실제 환경에 맞게 변경합니다.  

        ```PowerShell
        C:\PS>Get-AzureVM -ServiceName "<your_cloud_service_name>" -Name "<your_vm_name>" | Add-AzureEndpoint -Name "<endpoint_name>" -Protocol "tcp" -PublicPort 80 -LocalPort 80 | Update-AzureVM
        ```

## 6\. 앱 빌드 및 실행
원격 호스트로 배포할 때는 편집 및 새로 고침 개발에 사용되는 볼륨 매핑 기능이 작동하지 않습니다. 그러므로 앱을 빌드할 때는 볼륨 매핑 구성이 사용되지 않도록 *릴리스 구성*을 사용해야 합니다. 다음 단계에 따라 앱을 실행합니다.

1.  Visual Studio 도구 모음에서 **릴리스** 구성을 선택합니다.

1.  시작 대상을 **Docker**로 변경합니다.

1.  **Docker** 아이콘을 탭하여 앱을 빌드하고 실행합니다.

![앱 시작][2]

다음 화면과 같은 결과가 표시되어야 합니다.

![앱 보기][3]

[0]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png

<!---HONumber=AcomDC_0511_2016-->