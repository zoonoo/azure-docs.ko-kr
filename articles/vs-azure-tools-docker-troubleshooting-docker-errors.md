<properties
   pageTitle="Visual Studio를 사용하여 Windows에서 Docker 클라이언트 오류 문제해결 | Microsoft Azure"
   description="Visual Studio를 사용하여 웹앱을 만들고 Docker에 배포하는 경우 발생하는 문제를 해결합니다."
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
   ms.date="04/18/2016"
   ms.author="tarcher" />

# Docker 오류 문제 해결

앱의 Docker 컨테이너에 대한 모든 설정을 구성한 다음 설정 및 경로가 올바른지 확인해야 합니다. Visual Studio는 이 작업을 수행할 수 있도록 게시 대화 상자에서 유효성 검사 단추를 제공합니다.

이 항목은 Docker에서 Visual Studio 앱을 호스트할 때 발생할 수 있는 가장 일반적인 문제를 진단하고 수정하거나 해결하는 데 도움이 됩니다. 더 많은 문제가 나타날 때 이 항목에 추가됩니다.

## 웹 게시 대화 상자에서 Docker 호스트 연결에 대한 유효성 검사를 시도하면 오류 메시지가 나타납니다.

이 문제에 대한 가능한 해결책은 다음과 같습니다.

- **게시** 대화 상자의 **연결** 탭에서 **서버 URL**이 올바른지 확인하고 **서버 URL**의 후행:`:<port_number>`이 Docker 데몬에서 수신 중인 포트인지 확인합니다.

- **게시** 대화 상자의 **연결** 탭에서 **Docker 고급 옵션** 섹션을 확장하고 올바른 **인증** 옵션이 지정되어 있는지 확인합니다.
  - 서버의 Docker 데몬이 TLS 보안을 사용하도록 구성되어 있는 경우 Windows Docker 명령줄 인터페이스(docker.exe)가 `<%userprofile%>\.docker` 폴더 아래에서 기본적으로 클라이언트 키(key.pem) 및 인증서(cert.pem)를 찾습니다. 이러한 항목이 없는 경우 OpenSSL을 사용하여 생성해야 합니다. TLS의 Docker 구성에 대한 자세한 내용은 [HTTPS를 사용한 Docker 데몬 소켓 보호](https://docs.docker.com/articles/https/)를 참조하세요.

	Docker가 Linux 서버에 대한 Windows 클라이언트에서 제대로 인증되었는지를 확인하는 한 가지 방법은 미리 보기 텍스트 상자의 콘텐츠를 새 명령 창에 복사하고 다음과 같이 `<command>`을 “info”로 변경하는 것입니다.

    ```
    // This example assumes the Docker daemon is configured to use the default port
    // of 2376 to listen for connections.docker.

    --tls -H tcp://contoso.cloudapp.net:2376 info
    ```

    클라이언트 인증서 및 키 파일을 .docker 폴더에 복사하는 대체 방법으로 다음 매개 변수를 추가하여 **인증** 옵션을 변경할 수 있습니다.

    ```
    --tls --tlscert=C:\mycert\cert.pem --tlskey=C:\mycert\key.pem
    ```
- Docker 호스트 컴퓨터의 Docker 데몬이 버전 1.6 이상인지 확인하십시오.

## Docker 폴더에서 클라이언트 인증서가 없는 자체 인증서 사용 시 시간 초과 오류

Visual Studio에서 Docker 호스트를 만들 때 사용자 고유 인증서를 사용하도록 선택하는 경우(즉, **Microsoft Azure 대화 상자의 가상 컴퓨터 만들기** 대화 상자에서 **Docker 인증서 자동 생성** 확인란 선택을 취소하는 경우) 클라이언트 인증서 및 키 파일(cert.pem 및 key.pem)을 Docker 폴더(`<%userprofile%>\.docker`)에 복사해야 합니다. 그렇지 않은 경우 프로젝트를 게시하면 1시간 이내에 시간 초과 오류를 받게 되며 게시 작업이 실패합니다.

## Docker 컨테이너에 게시할 때 필요한 PowerShell 3.0

운영 체제가 Windows 7 또는 Windows Server 2008인 경우 Docker 컨테이너에 게시하려면 PowerShell 3.0을 설치해야 합니다. PowerShell 3.0은 [Windows Management Framework 3.0](https://www.microsoft.com/ko-KR/download/details.aspx?id=34595)에 포함되어 있습니다. 설치 후 시스템을 다시 부팅해야 합니다.

다른 해결 방법으로 이미 PowerShell 3.0이 있는 Windows 8.1 또는 Windows 10으로 업그레이드할 수 있습니다.

## 자동으로 닫히지 않는 PowerShell 창

VM을 만든 후 때로는 PowerShell 창이 자동으로 닫히지 않습니다. 이 창을 닫으면 Visual Studio도 닫힙니다. 창은 Visual Studio 또는 Docker 도구 기능에 영향을 주지 않으므로 작업을 완료할 때까지 열어두십시오.

## FAQ

Q: Visual Studio 도구를 사용하여 Azure에서 Linux 컴퓨터가 활성화된 새 Docker를 어떻게 만드나요?

A: 이 작업을 수행하는 방법에 대한 정보는 [Docker에서 웹앱 호스팅](vs-azure-tools-docker-hosting-web-apps-in-docker.md)을 참조하세요.

Q: 어떤 Visual Studio 프로젝트 템플릿이 Linux Docker 컨테이너에 게시하는 데 지원됩니까?

A: Visual Studio는 현재 다음과 같은 C# 콘솔 응용 프로그램(패키지) 및 C# ASP.NET 5 미리 보기 웹 템플릿을 지원합니다.

- 비어 있음

- Web API

- 웹 응용 프로그램

Q: 명령줄에서 MSBUILD를 사용하여 어떻게 제 ASP.NET 5 웹 또는 콘솔 프로젝트를 Docker에 게시하나요?

A: 다음 MSBuild 명령을 사용합니다.

    `msbuild <projectname.xproj> /p:deployOnBuild=true;publishProfile=<profilename>`

Q: 명령줄에서 PowerShell을 사용하여 어떻게 저의 ASP.NET 5 웹 또는 콘솔 프로젝트를 Docker에 게시하나요?

A: 다음 PowerShell 명령을 사용합니다.

```
.\contoso-Docker-publish.ps1 -packOutput $env:USERPROFILE\AppData\Local\Temp\PublishTemp -pubxmlFile .\contoso-Docker.pubxml
```

Q: Docker에 제 Linux 서버가 설치되어 있습니다. **웹 게시** 대화 상자에 이를 지정하려면 어떻게 해야 하나요?

A: [Docker에서 웹앱 호스팅](vs-azure-tools-docker-hosting-web-apps-in-docker.md) 항목에서 **Docker 호스트 사용자 지정 제공** 섹션을 참조하십시오.

Q: Docker에 설치된 제 Linux 서버를 사용 중입니다. TLS를 사용하여 인증을 구성하기 위해 키와 인증서를 생성하려면 어떻게 해야 하나요?

A: 한 가지 방법은 서버에서 OpenSSL을 사용하여 CA, 서버 및 클라이언트에 필요한 인증서 및 키를 생성하는 것입니다. 그러면 타사 소프트웨어를 사용하여 SSH/SFTP 연결을 설정할 수 있으며 그런 다음 로컬 Windows 개발 컴퓨터에 인증서를 복사할 수 있습니다. 기본적으로 Docker(CLI)는 `<userprofile>\.docker` 폴더에 있는 인증서 사용을 시도합니다.

다른 옵션은 Windows용 OpenSSL을 다운로드하고 필요한 인증서 및 키를 생성한 다음 Linux 컴퓨터에 CA, 서버 인증서 및 키를 업로드하는 것입니다. Docker의 보안 연결 설정에 대한 자세한 내용은 [HTTPS를 사용한 Docker 데몬 소켓 보호](https://docs.docker.com/articles/https/)를 참조하세요.

<!---HONumber=AcomDC_0420_2016-->