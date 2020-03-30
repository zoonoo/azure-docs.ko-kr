---
title: 로컬 Azure 저장소 개발에 Azurite 에뮬레이터 사용
description: Azurite 오픈 소스 에뮬레이터(미리 보기)는 Azure 저장소 응용 프로그램을 테스트하기 위한 무료 로컬 환경을 제공합니다.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/31/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 5e1fce0852a4e820d7ee0af626ce3fddf6773750
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76029932"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development-and-testing-preview"></a>로컬 Azure 저장소 개발 및 테스트에 Azurite 에뮬레이터 사용(미리 보기)

Azurite 버전 3.2 오픈 소스 에뮬레이터(미리 보기)는 Azure Blob 및 큐 저장소 응용 프로그램을 테스트하기 위한 무료 로컬 환경을 제공합니다. 응용 프로그램이 로컬에서 작동하는 방식에 만족하면 클라우드에서 Azure Storage 계정을 사용하는 것으로 전환합니다. 에뮬레이터는 Windows, Linux 및 MacOS에서 플랫폼 간 지원을 제공합니다. Azurite v3는 Azure Blob 서비스에서 구현한 API를 지원합니다.

Azurite는 미래의 스토리지 에뮬레이터 플랫폼입니다. Azurite는 [Azure 저장소 에뮬레이터를](storage-use-emulator.md)대체합니다. Azurite는 Azure 저장소 API의 최신 버전을 지원하도록 계속 업데이트됩니다.

로컬 시스템에서 Azurite를 설치하고 실행하는 방법에는 여러 가지가 있습니다.

  1. [Azurite 비주얼 스튜디오 코드 확장 설치 및 실행](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [NPM을 사용하여 Azurite 설치 및 실행](#install-and-run-azurite-by-using-npm)
  1. [Azurite Docker 이미지 설치 및 실행](#install-and-run-the-azurite-docker-image)
  1. [GitHub 리포지토리에서 Azurite를 복제, 빌드 및 실행](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>Azurite 비주얼 스튜디오 코드 확장 설치 및 실행

비주얼 스튜디오 코드 내에서 **확장** 창을 선택하고 확장:MARKETPLACE에서 *Azurite를* **검색합니다.**

![비주얼 스튜디오 코드 확장 마켓플레이스](media/storage-use-azurite/azurite-vs-code-extension.png)

또는 브라우저에서 [VS 코드 확장 시장으로](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) 이동합니다. **설치** 버튼을 선택하여 Visual Studio 코드를 열고 Azurite 확장 페이지로 바로 이동합니다.

VS 코드 상태 표시줄에서 **[Azurite Blob Service]** 또는 **[Azurite Queue Service]를** 클릭하거나 VS Code 명령 팔레트에서 다음 명령을 실행하여 Azurite를 빠르게 시작하거나 닫을 수 있습니다. 명령 팔레트를 열려면 VS 코드에서 **F1을** 누릅니다.

확장은 다음과 같은 Visual Studio 코드 명령을 지원합니다.

   * **아즈라이트: 시작** - 모든 아즈라이트 서비스 시작
   * **아즈리트: 닫기** - 모든 아즈라이트 서비스 닫기
   * **Azurite: 정리** - 모든 Azurite 서비스 지속성 데이터 재설정
   * **Azurite: Blob 서비스 시작** - Blob 서비스 시작
   * **Azurite: Blob 닫기 서비스** - 닫기 Blob 서비스
   * **아주르트: 클린 블럭 서비스** - 클린 블럭 서비스
   * **Azurite: 큐 서비스 시작** - 큐 서비스 시작
   * **Azurite: 대기열 서비스 닫기** - 큐 서비스 닫기
   * **Azurite: 클린 큐 서비스** - 클린 큐 서비스

비주얼 스튜디오 코드 내에서 Azurite를 구성하려면 확장 창을 선택합니다. **Azurite의** **관리(기어)** 아이콘을 선택합니다. **확장 설정 구성을**선택합니다.

![Azurite 확장 설정을 구성](media/storage-use-azurite/azurite-configure-extension-settings.png)

다음 설정이 지원됩니다.

   * **Azurite: Blob 호스트** - Blob 서비스 수신 대기 끝점. 기본 설정은 127.0.0.1입니다.
   * **Azurite: Blob 포트** - Blob 서비스 청취 포트. 기본 포트는 10000입니다.
   * **Azurite: 디버그** - 디버그 로그를 Azurite 채널로 출력합니다. 기본값은 **false**입니다.
   * **Azurite: 위치** - 작업 영역 위치 경로입니다. 기본값은 시각적 스튜디오 코드 작업 폴더입니다.
   * **Azurite: 큐 호스트** - 큐 서비스 수신 대기 끝점입니다. 기본 설정은 127.0.0.1입니다.
   * **Azurite: 큐 포트** - 큐 서비스 수신 대기 포트입니다. 기본 포트는 10001입니다.
   * **Azurite: 자동** 모드는 액세스 로그를 비활성화합니다. 기본값은 **false**입니다.

## <a name="install-and-run-azurite-by-using-npm"></a>NPM을 사용하여 Azurite 설치 및 실행

이 설치 방법을 사용하려면 [Node.js 버전 8.0 이상에](https://nodejs.org) 설치해야 합니다. **npm은** 모든 Node.js 설치에 포함된 패키지 관리 도구입니다. Node.js를 설치한 후 다음 **npm** 명령을 실행하여 Azurite를 설치합니다.

```console
npm install -g azurite
```

Azurite를 설치한 후 [명령줄에서 Azurite 실행을](#run-azurite-from-a-command-line)참조하십시오.

## <a name="install-and-run-the-azurite-docker-image"></a>Azurite Docker 이미지 설치 및 실행

[DockerHub를](https://hub.docker.com/) 사용하여 다음 명령을 사용하여 [최신 Azurite 이미지를](https://hub.docker.com/_/microsoft-azure-storage-azurite) 가져옵니다.

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Azurite 도커 이미지 실행**:

다음 명령은 Azurite Docker 이미지를 실행합니다. 매개 `-p 10000:10000` 변수는 호스트 컴퓨터의 포트 10000에서 Docker 인스턴스로 요청을 리디렉션합니다.

```console
docker run -p 10000:10000 -p 10001:10001 mcr.microsoft.com/azure-storage/azurite
```

**작업 영역 위치 지정:**

다음 예제에서 매개 `-v c:/azurite:/data` 변수는 azurite 가 데이터 위치를 유지함에 따라 *c:/azurite를* 지정합니다. Docker 명령을 실행하기 전에 디렉터리 *c:/azurite를*만들어야 합니다.

```console
docker run -p 10000:10000 -p 10001:10001 -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Blob 서비스만 실행**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

**모든 Azurite 매개 변수 를 설정 :**

이 예제에서는 모든 명령줄 매개 변수를 설정하는 방법을 보여 주십습니다. 아래의 모든 매개 변수는 단일 명령줄에 배치되어야 합니다.

```console
docker run -p 8888:8888
           -p 9999:9999
           -v c:/azurite:/workspace mcr.microsoft.com/azure-storage/azurite azurite
           -l /workspace
           -d /workspace/debug.log
           --blobPort 8888
           --blobHost 0.0.0.0
           --queuePort 9999
           --queueHost 0.0.0.0
```

시작 시 Azurite 구성에 대한 자세한 내용은 [명령줄 옵션을](#command-line-options) 참조하십시오.

## <a name="clone-build-and-run-azurite-from-the-github-repository"></a>GitHub 리포지토리에서 Azurite를 복제, 빌드 및 실행

이 설치 방법은 [Git가](https://git-scm.com/) 설치되어 있어야 합니다. 다음 콘솔 명령을 사용하여 Azurite 프로젝트에 대한 [GitHub 리포지토리를](https://github.com/azure/azurite) 복제합니다.

```console
git clone https://github.com/Azure/Azurite.git
```

소스 코드를 복제한 후 복제된 리포지토리의 루트에서 다음 명령을 실행하여 Azurite를 빌드하고 설치합니다.

```console
npm install
npm run build
npm install -g
```

Azurite를 설치하고 구축한 후 [명령줄에서 Azurite 실행을](#run-azurite-from-a-command-line)참조하십시오.

## <a name="run-azurite-from-a-command-line"></a>명령줄에서 Azurite 실행

> [!NOTE]
> Visual Studio 코드 확장만 설치한 경우 명령줄에서 Azurite를 실행할 수 없습니다. 대신 VS Code 명령 팔레트를 사용합니다. 자세한 내용은 [Azurite 시각적 스튜디오 코드 확장 설치 및 실행을](#install-and-run-the-azurite-visual-studio-code-extension)참조하십시오.

명령줄로 즉시 시작하려면 **c:\azurite라는**디렉토리를 만든 다음 다음 명령을 실행하여 Azurite를 시작합니다.

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

이 명령은 Azurite에게 특정 디렉터리 **c:\azurite에**모든 데이터를 저장하도록 지시합니다. **--location** 옵션을 생략하면 현재 작업 디렉토리를 사용합니다.

## <a name="command-line-options"></a>명령줄 옵션

이 섹션에서는 Azurite를 시작할 때 사용할 수 있는 명령줄 스위치에 대해 자세히 설명합니다. 모든 명령줄 스위치는 선택 사항입니다.

```console
C:\Azurite> azurite [--blobHost <IP address>] [--blobPort <port address>] 
    [-d | --debug <log file path>] [-l | --location <workspace path>]
    [--queueHost <IP address>] [--queuePort <port address>]
    [-s | --silent] [-h | --help]
```

**-d는** **--debug에**대한 바로 가기, **-l** 스위치는 **--location에**대한 바로 가기이며 **-s는** **--silent에**대한 바로 가기이며 **-h는** **--help에**대한 바로 가기입니다.

### <a name="blob-listening-host"></a>Blob 듣기 호스트

**선택 사항** 기본적으로 Azurite는 로컬 서버로 127.0.0.1을 수신대기합니다. **--blobHost** 스위치를 사용하여 요구 사항에 맞게 주소를 설정합니다.

로컬 컴퓨터에서만 요청을 수락합니다.

```console
azurite --blobHost 127.0.0.1
```

원격 요청 허용:

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> 원격 요청을 허용하면 시스템이 외부 공격에 취약해질 수 있습니다.

### <a name="blob-listening-port-configuration"></a>Blob 청취 포트 구성

**선택 사항** 기본적으로 Azurite는 포트 10000에서 Blob 서비스를 수신 대기합니다. **--blobPort** 스위치를 사용하여 필요한 수신 대기 포트를 지정합니다.

> [!NOTE]
> 사용자 지정된 포트를 사용한 후에는 Azure Storage 도구 또는 SDK에서 연결 문자열 또는 해당 구성을 업데이트해야 합니다.

Blob 서비스 수신 대기 포트 사용자 지정:

```console
azurite --blobPort 8888
```

시스템에서 사용 가능한 포트를 자동으로 선택하도록 합니다.

```console
azurite --blobPort 0
```

사용 중 포트는 Azurite 시작 중에 표시됩니다.

### <a name="queue-listening-host"></a>큐 수신 대기 호스트

**선택 사항** 기본적으로 Azurite는 로컬 서버로 127.0.0.1을 수신대기합니다. **--queueHost** 스위치를 사용하여 요구 사항에 맞게 주소를 설정합니다.

로컬 컴퓨터에서만 요청을 수락합니다.

```console
azurite --queueHost 127.0.0.1
```

원격 요청 허용:

```console
azurite --queueHost 0.0.0.0
```

> [!CAUTION]
> 원격 요청을 허용하면 시스템이 외부 공격에 취약해질 수 있습니다.

### <a name="queue-listening-port-configuration"></a>큐 수신 대기 포트 구성

**선택 사항** 기본적으로 Azurite는 포트 10001에서 Queue 서비스를 수신 대기합니다. **--queuePort** 스위치를 사용하여 필요한 수신 대기 포트를 지정합니다.

> [!NOTE]
> 사용자 지정된 포트를 사용한 후에는 Azure Storage 도구 또는 SDK에서 연결 문자열 또는 해당 구성을 업데이트해야 합니다.

Queue 서비스 수신 대기 포트 사용자 지정:

```console
azurite --queuePort 8888
```

시스템에서 사용 가능한 포트를 자동으로 선택하도록 합니다.

```console
azurite --queuePort 0
```

사용 중 포트는 Azurite 시작 중에 표시됩니다.

### <a name="workspace-path"></a>작업 영역 경로

**선택 사항** Azurite는 실행 하는 동안 로컬 디스크에 데이터를 저장 합니다. **--위치** 스위치를 사용하여 경로를 작업 영역 위치로 지정합니다. 기본적으로 현재 프로세스 작업 디렉토리가 사용됩니다.

```console
azurite --location c:\azurite
```

```console
azurite -l c:\azurite
```

### <a name="access-log"></a>액세스 로그

**선택 사항** 기본적으로 액세스 로그는 콘솔 창에 표시됩니다. **--silent** 스위치를 사용하여 액세스 로그의 표시를 비활성화합니다.

```console
azurite --silent
```

```console
azurite -s
```

### <a name="debug-log"></a>디버그 로그

**선택 사항** 디버그 로그에는 모든 요청 및 예외 스택 추적에 대한 자세한 정보가 포함됩니다. --debug 스위치에 유효한 로컬 파일 경로를 제공하여 **디버그 로그를** 활성화합니다.

```console
azurite --debug path/debug.log
```

```console
azurite -d path/debug.log
```

### <a name="loose-mode"></a>느슨한 모드

**선택 사항** 기본적으로 Azurite는 지원되지 않는 요청 헤더 및 매개 변수를 차단하기 위해 엄격 모드를 적용합니다. **--느슨한** 스위치를 사용하여 엄격 모드를 비활성화합니다.

```console
azurite --loose
```

대문자 'L' 바로 가기 스위치를 참고하십시오.

```console
azurite -L
```

## <a name="authorization-for-tools-and-sdks"></a>도구 및 SDK에 대한 권한 부여

인증 전략을 사용하여 Azure 저장소 SDK 또는 [Azure 저장소 탐색기와](https://azure.microsoft.com/features/storage-explorer/)같은 도구에서 Azurite에 연결합니다. 인증이 필요합니다. Azurite는 공유 키 및 공유 액세스 서명(SAS)으로 인증을 지원합니다. Azurite는 공용 컨테이너에 대한 익명 액세스를 지원합니다.

### <a name="well-known-storage-account-and-key"></a>잘 알려진 저장소 계정 및 키

Azurite와 함께 다음 계정 이름과 키를 사용할 수 있습니다. 레거시 Azure 저장소 에뮬레이터에서 사용하는 것과 동일한 잘 알려진 계정 및 키입니다.

* 계정 이름:`devstoreaccount1`
* 계정 키:`Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

> [!NOTE]
> Azurite는 SharedKey 인증 외에도 계정 및 서비스 SAS 인증을 지원합니다. 공용 액세스를 허용하도록 컨테이너를 설정한 경우에도 익명 액세스를 사용할 수 있습니다.

### <a name="connection-string"></a>연결 문자열

응용 프로그램에서 Azurite에 연결하는 가장 쉬운 방법은 *응용*프로그램의 구성 파일에 연결 문자열을 구성하는 것입니다. *다음은 app.config* 파일의 연결 문자열의 예입니다.

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

자세한 내용은 [Azure Storage 연결 문자열 구성](storage-configure-connection-string.md)을 참조하세요.

### <a name="custom-storage-accounts-and-keys"></a>사용자 지정 저장소 계정 및 키

Azurite는 환경 변수를 다음과 같은 `AZURITE_ACCOUNTS` 형식으로 설정하여 사용자 `account1:key1[:key2];account2:key1[:key2];...`지정 저장소 계정 이름과 키를 지원합니다.

예를 들어 하나의 키가 있는 사용자 지정 저장소 계정을 사용합니다.

```cmd
set AZURITE_ACCOUNTS="account1:key1"
```

또는 각각 2개의 키가 있는 여러 저장소 계정을 사용합니다.

```cmd
set AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

Azurite는 기본적으로 1분마다 환경 변수의 사용자 지정 계정 이름과 키를 새로 고칩니다. 이 기능을 사용하면 Azurite를 다시 시작하지 않고도 계정 키를 동적으로 회전하거나 새 저장소 계정을 추가할 수 있습니다.

> [!NOTE]
> 사용자 `devstoreaccount1` 지정 저장소 계정을 설정할 때 기본 저장소 계정이 비활성화됩니다.

> [!NOTE]
> 사용자 지정 계정 이름과 키를 사용할 때 연결 문자열을 적절하게 업데이트합니다.

> [!NOTE]
> 키워드를 `export` 사용하여 Linux 환경에서 환경 변수를 설정하고 `set` Windows에서 사용합니다.

### <a name="storage-explorer"></a>Storage Explorer

Azure 저장소 탐색기에서 **계정 추가** 아이콘을 클릭하여 Azurite에 연결한 다음 **로컬 에뮬레이터에 연결한 다음** **연결을**클릭합니다.

## <a name="differences-between-azurite-and-azure-storage"></a>아주리테 스토리지와 Azure 저장소의 차이점

Azurite의 로컬 인스턴스와 클라우드의 Azure Storage 계정 간에는 기능적 차이가 있습니다.

### <a name="endpoint-and-connection-url"></a>끝점 및 연결 URL

Azurite의 서비스 끝점은 Azure Storage 계정의 끝점과 다릅니다. 로컬 컴퓨터는 도메인 이름 확인을 수행하지 않으므로 Azurite 끝점이 로컬 주소여야 합니다.

Azure Storage 계정에서 리소스를 처리하는 경우 계정 이름은 URI 호스트 이름의 일부입니다. 해결 중인 리소스는 URI 경로의 일부입니다.

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

다음 URI는 Azure Storage 계정의 Blob에 대한 유효한 주소입니다.

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

로컬 컴퓨터가 도메인 이름 확인을 수행하지 않으므로 계정 이름은 호스트 이름 대신 URI 경로의 일부입니다. Azurite의 리소스에 다음 URI 형식을 사용합니다.

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

다음 주소는 Azurite의 Blob에 액세스하는 데 사용될 수 있습니다.

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>크기 조정 및 성능

Azurite는 확장 가능한 저장소 서비스가 아니며 많은 수의 동시 클라이언트를 지원하지 않습니다. 성능 보장은 없습니다. Azurite는 개발 및 테스트 목적으로 사용됩니다.

### <a name="error-handling"></a>오류 처리

AzuriteAzure 저장소 오류 처리 논리와 정렬 되어 있지만 차이점이 있습니다. 예를 들어 오류 메시지는 다를 수 있지만 오류 상태 코드는 정렬됩니다.

### <a name="ra-grs"></a>RA-GRS

Azurite는 읽기 액세스 지리적 중복 복제(RA-GRS)를 지원합니다. 저장소 리소스의 경우 계정 이름에 **-secondary를** 더하여 보조 위치에 액세스합니다. 예를 들어 다음 주소는 Azurite의 읽기 전용 보조 보조를 사용하여 Blob에 액세스하는 데 사용할 수 있습니다.

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## <a name="azurite-is-open-source"></a>아즈라이트는 오픈 소스입니다

아주리테에 대한 기여와 제안을 환영합니다. Azurite [GitHub 프로젝트](https://github.com/Azure/Azurite/projects) 페이지 또는 [GitHub 문제로](https://github.com/Azure/Azurite/issues) 이동하여 예정된 기능 및 버그 수정에 대해 추적할 수 있는 이정표 및 작업 항목에 대해 설명합니다. 자세한 작업 항목은 GitHub에서도 추적됩니다.

## <a name="next-steps"></a>다음 단계

* 개발 및 테스트 문서 [에뮬레이터를 사용 하 여](storage-use-emulator.md) Azurite에 의해 대체 되는 레거시 Azure 저장소 에뮬레이터입니다.
* [Azure 저장소 연결 문자열 구성](storage-configure-connection-string.md) 유효한 Azure STorage 연결 문자열을 어셈블하는 방법을 설명합니다.
