---
title: Azurite 오픈 소스 에뮬레이터를 사용 하 여 blob storage 개발 및 테스트 (미리 보기)
description: Azurite 오픈 소스 에뮬레이터 (미리 보기)는 Azure Blob storage 응용 프로그램을 테스트 하기 위한 무료 로컬 환경을 제공 합니다.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 06/12/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: ebecd6cf9af5395e4da2b395ca9b2ff974a75409
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721693"
---
# <a name="use-the-azurite-open-source-emulator-for-blob-storage-development-and-testing-preview"></a>Azurite 오픈 소스 에뮬레이터를 사용 하 여 blob storage 개발 및 테스트 (미리 보기)

Azurite 버전 3 오픈 소스 에뮬레이터 (미리 보기)는 Azure Blob storage 응용 프로그램을 테스트 하기 위한 무료 로컬 환경을 제공 합니다. 응용 프로그램이 로컬에서 작동 하는 방식에 만족 하는 경우 클라우드에서 Azure Storage 계정을 사용 하도록 전환 합니다. 에뮬레이터는 Windows, Linux 및 MacOS에서 플랫폼 간 지원을 제공 합니다. Azurite v3은 Azure Blob service에서 구현 하는 Api를 지원 합니다.

Azurite는 향후 저장소 에뮬레이터 플랫폼입니다. Azurite는 [Azure Storage 에뮬레이터](storage-use-emulator.md)를 대체 합니다. Azurite는 최신 버전의 Azure Storage Api를 지원 하도록 계속 업데이트 됩니다.

로컬 시스템에서 Azurite를 설치 하 고 실행 하는 여러 가지 방법이 있습니다.

  1. [Azurite Visual Studio Code 확장을 설치 하 고 실행 합니다.](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [NPM를 사용 하 여 Azurite 설치 및 실행](#install-and-run-azurite-by-using-npm)
  1. [Azurite Docker 이미지를 설치 하 고 실행 합니다.](#install-and-run-the-azurite-docker-image)
  1. [GitHub 리포지토리에서 Azurite 복제, 빌드 및 실행](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>Azurite Visual Studio Code 확장을 설치 하 고 실행 합니다.

Visual Studio Code 내에서 **확장** 창을 선택 하 고 **확장: MARKETPLACE**에서 *Azurite* 를 검색 합니다.

![Visual Studio Code 확장 마켓플레이스](media/storage-use-azurite/azurite-vs-code-extension.png)

또는 브라우저에서 [VS Code 확장 시장](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) 으로 이동 합니다. **설치** 단추를 선택 하 Visual Studio Code를 열고 Azurite 확장 페이지로 직접 이동 합니다.

VS Code 상태 표시줄에서 **Azurite Blob Service** 를 클릭 하거나 VS Code 명령 팔레트에서 다음 명령을 실행 하 여 Azurite를 신속 하 게 시작 하거나 닫을 수 있습니다. 명령 팔레트를 열려면 VS Code에서 **F1** 키를 누릅니다.

확장은 다음과 같은 Visual Studio Code 명령을 지원 합니다.

   * **Azurite: 시작** -모든 Azurite services 시작
   * **Azurite:**  모든 Azurite 서비스 닫기
   * **Azurite: Clean** -모든 Azurite services 지 속성 데이터를 다시 설정 합니다.
   * **Azurite: 시작** -blob 시작 blob service
   * **Azurite: 닫기** -blob blob service 닫기
   * **Azurite: 클린** blob 클린 blob 서비스

Visual Studio Code 내에서 Azurite를 구성 하려면 확장 창을 선택 하 고 **Azurite**를 마우스 오른쪽 단추로 클릭 합니다. **확장 설정 구성**을 선택 합니다.

![Azurite 확장 설정 구성](media/storage-use-azurite/azurite-configure-extension-settings.png)

다음 설정이 지원 됩니다.

   * **Azurite: Blob 호스트** -Blob service 수신 대기 끝점입니다. 기본 설정은 127.0.0.1입니다.
   * **Azurite: Blob 포트** -Blob service 수신 포트입니다. 기본 포트는 1만입니다.
   * **Azurite: 디버그** -Azurite 채널에 디버그 로그를 출력 합니다. 기본값은 **false**입니다.
   * **Azurite: Location** -작업 영역 위치 경로입니다. 기본값은 Visual Studio Code 작업 폴더입니다.
   * **Azurite: 자동** -자동 모드는 액세스 로그를 사용 하지 않도록 설정 합니다. 기본값은 **false**입니다.

## <a name="install-and-run-azurite-by-using-npm"></a>NPM를 사용 하 여 Azurite 설치 및 실행

이 설치 방법을 사용 하려면 [node.js 버전 8.0 이상이](https://nodejs.org) 설치 되어 있어야 합니다. **npm** 는 모든 node.js 설치에 포함 된 패키지 관리 도구입니다. Node.js를 설치한 후 다음 **npm** 명령을 실행 하 여 Azurite를 설치 합니다.

```console
npm install -g azurite
```

Azurite를 설치한 후 [명령줄에서 Azurite 실행](#run-azurite-from-a-command-line)을 참조 하세요.

## <a name="install-and-run-the-azurite-docker-image"></a>Azurite Docker 이미지를 설치 하 고 실행 합니다.

다음 명령을 사용 하 여 [Dockerhub](https://hub.docker.com/) 를 사용 하 여 [최신 Azurite 이미지](https://hub.docker.com/_/microsoft-azure-storage-azurite) 를 가져옵니다.

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Azurite Docker 이미지를 실행 합니다**.

다음 명령은 Azurite Docker 이미지를 실행 합니다. 매개 `-p 10000:10000` 변수는 호스트 컴퓨터의 포트 1만에서 Docker 인스턴스로 요청을 리디렉션합니다.

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite
```

**작업 영역 위치 지정**:

다음 예제에서 매개 변수는 `-v c:/azurite:/data` 를 Azurite `c:/azurite` 지속형 데이터 위치로 지정 합니다.

```console
docker run -p 10000:10000 -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**모든 Azurite 매개 변수를 설정**합니다.

이 예제에서는 모든 명령줄 매개 변수를 설정 하는 방법을 보여 줍니다. 아래 매개 변수는 모두 단일 명령줄에 배치 해야 합니다.

```console
docker run -p 8888:8888
           -v c:/azurite:/workspace mcr.microsoft.com/azure-storage/azurite azurite
           -l /workspace
           -d /workspace/debug.log
           --blobPort 8888
           --blobHost 0.0.0.0
```

시작 시 Azurite 구성에 대 한 자세한 내용은 [명령줄 옵션](#command-line-options) 을 참조 하세요.

## <a name="clone-build-and-run-azurite-from-the-github-repository"></a>GitHub 리포지토리에서 Azurite 복제, 빌드 및 실행

이 설치 방법을 사용 하려면 [Git](https://git-scm.com/) 가 설치 되어 있어야 합니다. 다음 콘솔 명령을 사용 하 여 Azurite 프로젝트에 대 한 [GitHub 리포지토리](https://github.com/azure/azurite) 를 복제 합니다.

```console
git clone https://github.com/Azure/Azurite.git
```

소스 코드를 복제 한 후 복제 된 리포지토리의 루트에서 다음 명령을 실행 하 여 Azurite를 빌드하고 설치 합니다.

```console
npm install
npm run build
npm install -g
```

Azurite를 설치 하 고 빌드한 후 [명령줄에서 Azurite 실행](#run-azurite-from-a-command-line)을 참조 하세요.

## <a name="run-azurite-from-a-command-line"></a>명령줄에서 Azurite 실행

> [!NOTE]
> Visual Studio Code 확장명만 설치한 경우 명령줄에서 Azurite를 실행할 수 없습니다. 대신 VS Code 명령 팔레트를 사용 합니다. 자세한 내용은 [Azurite Visual Studio Code 확장 설치 및 실행](#install-and-run-the-azurite-visual-studio-code-extension)을 참조 하세요.

명령줄을 사용 하 여 즉시 시작 하려면 **c:\azurite**라는 디렉터리를 만든 후 다음 명령을 실행 하 여 azurite를 시작 합니다.

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

이 명령은 Azurite에 게 **c:\azurite**의 특정 디렉터리에 모든 데이터를 저장 하도록 지시 합니다. **--Location** 옵션을 생략 하면 현재 작업 디렉터리가 사용 됩니다.

## <a name="command-line-options"></a>명령줄 옵션

이 섹션에서는 Azurite를 시작할 때 사용할 수 있는 명령줄 스위치에 대해 자세히 설명 합니다. 모든 명령줄 스위치는 선택 사항입니다.

```console
C:\Azurite> azurite [--blobHost <IP address>] [--blobPort <port address>]
    [-l | --location <workspace path>] [-s | --silent] [-d | --debug <log file path>]
```

\- **L** 스위치는-- **location**에 대 한 바로 가기입니다.- **s** 는 **--silent**의 바로 가기입니다.- **d** 는 **--debug**의 바로 가기입니다.

### <a name="listening-host"></a>수신 대기 호스트

**선택 사항** 기본적으로 Azurite는 로컬 서버로 127.0.0.1을 수신 합니다. **--Blobhost** 스위치를 사용 하 여 주소를 요구 사항으로 설정 합니다.

로컬 컴퓨터 에서만 요청 수락:

```console
azurite --blobHost 127.0.0.1
```

원격 요청 허용:

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> 원격 요청을 허용 하면 시스템이 외부 공격에 취약 해질 수 있습니다.

### <a name="listening-port-configuration"></a>수신 대기 포트 구성

**선택 사항** 기본적으로 Azurite는 포트 1만에서 Blob service를 수신 대기 합니다. **--Blobport** 스위치를 사용 하 여 필요한 수신 대기 포트를 지정 합니다.

> [!NOTE]
> 사용자 지정 된 포트를 사용한 후 Azure Storage 도구나 Sdk에서 연결 문자열 또는 해당 구성을 업데이트 해야 합니다.

Blob service 수신 대기 포트를 사용자 지정 합니다.

```console
azurite --blobPort 8888
```

시스템에서 사용 가능한 포트를 자동으로 선택 하도록 합니다.

```console
azurite --blobPort 0
```

Azurite을 시작 하는 동안 사용 중인 포트가 표시 됩니다.

### <a name="workspace-path"></a>작업 영역 경로

**선택 사항** Azurite는 실행 중에 데이터를 로컬 디스크에 저장 합니다. **--Location** 스위치를 사용 하 여 경로를 작업 영역 위치로 지정 합니다. 기본적으로 현재 프로세스 작업 디렉터리가 사용 됩니다.

```console
azurite --location c:\azurite
```

```console
azurite -l c:\azurite
```

### <a name="access-log"></a>액세스 로그

**선택 사항** 기본적으로 액세스 로그는 콘솔 창에 표시 됩니다. **--자동** 스위치를 사용 하 여 액세스 로그 표시를 사용 하지 않도록 설정 합니다.

```console
azurite --silent
```

```console
azurite -s
```

### <a name="debug-log"></a>디버그 로그

**선택 사항** 디버그 로그에는 모든 요청 및 예외 스택 추적에 대 한 자세한 정보가 포함 되어 있습니다. **--Debug** 스위치에 올바른 로컬 파일 경로를 제공 하 여 디버그 로그를 사용 하도록 설정 합니다.

```console
azurite --debug path/debug.log
```

```console
azurite -d path/debug.log
```

## <a name="authorization-for-tools-and-sdks"></a>도구 및 Sdk에 대 한 권한 부여

인증 전략을 사용 하 여 [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)와 같은 Azure Storage sdk 또는 도구에서 Azurite에 연결 합니다. 인증이 필요합니다. Azurite는 공유 키 및 SAS (공유 액세스 서명)를 사용한 권한 부여를 지원 합니다. Azurite는 공용 컨테이너에 대 한 익명 액세스도 지원 합니다.

### <a name="well-known-storage-account-and-key"></a>잘 알려진 저장소 계정 및 키

Azurite에서 다음 계정 이름 및 키를 사용할 수 있습니다. 레거시 Azure 저장소 에뮬레이터에서 사용 하는 것과 동일한 잘 알려진 계정 및 키입니다.

* 계정 이름:`devstoreaccount1`
* 계정 키:`Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

> [!NOTE]
> Azurite는 SharedKey 인증 외에도 계정 및 서비스 SAS 인증을 지원 합니다. 공용 액세스를 허용 하도록 컨테이너를 설정 하는 경우에도 익명 액세스를 사용할 수 있습니다.

### <a name="connection-string"></a>연결 문자열

응용 프로그램에서 Azurite에 연결 하는 가장 쉬운 방법은 *UseDevelopmentStorage = true*바로 가기를 참조 하는 응용 프로그램의 구성 파일에서 연결 문자열을 구성 하는 것입니다. 다음은 *app.config* 파일에 있는 연결 문자열의 예입니다.

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

자세한 내용은 [Azure Storage 연결 문자열 구성](storage-configure-connection-string.md)을 참조하세요.

### <a name="storage-explorer"></a>Storage 탐색기

Azure Storage 탐색기에서 **계정 추가** 아이콘을 클릭 하 여 Azurite에 연결한 다음 **로컬 에뮬레이터에** 연결을 선택 하 고 **연결**을 클릭 합니다.

## <a name="differences-between-azurite-and-azure-storage"></a>Azurite와 Azure Storage 간의 차이점

Azurite의 로컬 인스턴스와 클라우드의 Azure Storage 계정 사이에는 기능적인 차이점이 있습니다.

### <a name="endpoint-and-connection-url"></a>끝점 및 연결 URL

Azurite에 대 한 서비스 끝점은 Azure Storage 계정의 끝점과 다릅니다. 로컬 컴퓨터는 도메인 이름 확인을 수행 하지 않습니다. Azurite 끝점은 로컬 주소 여야 합니다.

Azure Storage 계정에서 리소스에 주소를 표시 하는 경우 계정 이름은 URI 호스트 이름의 일부입니다. 주소를 지정 하는 리소스는 URI 경로의 일부입니다.

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

다음 URI는 Azure Storage 계정의 blob에 대 한 올바른 주소입니다.

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

로컬 컴퓨터가 도메인 이름 확인을 수행 하지 않기 때문에 계정 이름은 호스트 이름이 아닌 URI 경로의 일부입니다. Azurite의 리소스에 대해 다음 URI 형식을 사용 합니다.

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Azurite에서 blob에 액세스 하는 데 사용할 수 있는 주소는 다음과 같습니다.

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>크기 조정 및 성능

Azurite는 확장 가능한 저장소 서비스가 아니므로 많은 수의 동시 클라이언트를 지원 하지 않습니다. 성능 보장은 없습니다. Azurite은 개발 및 테스트 목적으로 작성 되었습니다.

### <a name="error-handling"></a>오류 처리

Azurite는 Azure Storage 오류 처리 논리와 맞춰져 있지만 차이가 있습니다. 예를 들어 오류 메시지는 서로 다를 수 있으며, 오류 상태 코드는 정렬 됩니다.

### <a name="ra-grs"></a>RA-GRS

Azurite는 읽기 액세스 지역 중복 복제 (RA-GRS)를 지원 합니다. 저장소 리소스의 경우 계정 이름에 **-secondary** 를 추가 하 여 보조 위치에 액세스 합니다. 예를 들어 Azurite의 읽기 전용 보조 데이터베이스를 사용 하 여 blob에 액세스 하기 위해 다음 주소를 사용할 수 있습니다.

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## <a name="azurite-is-open-source"></a>Azurite는 오픈 소스입니다.

Azurite에 대 한 기여 및 제안은 환영 합니다. 예정 된 기능 및 버그 수정에 대해 추적 중인 마일스 톤 및 작업 항목에 대해서는 Azurite [github 프로젝트](https://github.com/Azure/Azurite/projects) 페이지 또는 [github 문제](https://github.com/Azure/Azurite/issues) 를 참조 하세요. 자세한 작업 항목은 GitHub 에서도 추적 됩니다.

## <a name="next-steps"></a>다음 단계

* [개발 및 테스트에 azure storage 에뮬레이터를 사용](storage-use-emulator.md) 하 여 Azurite로 대체 되는 레거시 Azure storage 에뮬레이터를 사용 합니다.
* [Azure Storage 연결 문자열 구성](storage-configure-connection-string.md) 올바른 Azure Storage 연결 문자열을 조합 하는 방법을 설명 합니다.
