---
title: Azure Cosmos DB 에뮬레이터를 사용 하 여 로컬로 설치 및 개발
description: Windows, Linux, macOS 및 Windows docker 환경에서 Azure Cosmos DB 에뮬레이터를 설치 하 고 사용 하는 방법에 대해 알아봅니다. 에뮬레이터를 사용 하 여 Azure 구독을 만들지 않고 무료로 응용 프로그램을 개발 하 고 로컬로 테스트할 수 있습니다.
ms.service: cosmos-db
ms.topic: how-to
author: markjbrown
ms.author: mjbrown
ms.date: 09/22/2020
ms.custom: devx-track-csharp, contperfq1
ms.openlocfilehash: 64da8084ec8d40e17a0005f2e70486c7d51bf640
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91627598"
---
# <a name="install-and-use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>로컬 개발 및 테스트에 Azure Cosmos 에뮬레이터 설치 및 사용

Azure Cosmos emulator는 개발 목적으로 Azure Cosmos DB 서비스를 에뮬레이트하는 로컬 환경을 제공 합니다. Azure Cosmos 에뮬레이터를 사용 하 여 Azure 구독을 만들거나 비용을 발생 시 키 지 않고도 로컬에서 응용 프로그램을 개발 하 고 테스트할 수 있습니다. Azure Cosmos emulator에서 응용 프로그램이 작동 하는 방식에 만족 하는 경우 클라우드에서 Azure Cosmos 계정을 사용 하도록 전환할 수 있습니다. 이 문서에서는 Windows, Linux, macOS 및 Windows docker 환경에서 에뮬레이터를 설치 하 고 사용 하는 방법을 설명 합니다.

## <a name="download-the-emulator"></a>에뮬레이터 다운로드

시작 하려면 로컬 컴퓨터에 최신 버전의 Azure Cosmos 에뮬레이터를 다운로드 하 여 설치 합니다. [Emulator 릴리스 정보](local-emulator-release-notes.md) 문서에서는 사용 가능한 모든 버전 및 각 릴리스에 적용 된 기능 업데이트를 나열 합니다.

:::image type="icon" source="media/local-emulator/download-icon.png" border="false":::**[Azure Cosmos 에뮬레이터 다운로드](https://aka.ms/cosmosdb-emulator)**

[SQL](local-emulator.md#sql-api), [Cassandra](local-emulator.md#cassandra-api), [MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb), [Gremlin](local-emulator.md#gremlin-api)및 [Table](local-emulator.md#table-api) API 계정을 사용 하 여 Azure Cosmos emulator를 사용 하 여 응용 프로그램을 개발할 수 있습니다. 현재 에뮬레이터의 데이터 탐색기는 SQL 데이터 보기만을 완벽 하 게 지원 합니다. MongoDB, Gremlin/Graph 및 Cassandra client 응용 프로그램을 사용 하 여 만든 데이터는 현재 볼 수 없습니다. 자세한 내용은 다양 한 Api에서 [emulator 끝점에 연결 하는 방법](#connect-with-emulator-apis) 을 참조 하세요.

## <a name="how-does-the-emulator-work"></a>에뮬레이터는 어떻게 작동 하나요?

Azure Cosmos emulator는 Azure Cosmos DB 서비스에 대 한 고화질 에뮬레이션을 제공 합니다. Azure Cosmos DB와 동일한 기능을 지원 합니다. 여기에는 데이터 만들기, 데이터 쿼리, 컨테이너 프로 비전 및 확장, 저장 프로시저 및 트리거 실행이 포함 됩니다. Azure Cosmos 에뮬레이터를 사용 하 여 응용 프로그램을 개발 및 테스트 하 고, Azure Cosmos DB 연결 끝점을 업데이트 하 여 글로벌 규모로 Azure에 배포할 수 있습니다.

Azure Cosmos DB 서비스의 에뮬레이션이 충실한 경우 에뮬레이터의 구현은 서비스와 다릅니다. 예를 들어 에뮬레이터는 로컬 파일 시스템(지속성) 및 HTTPS 프로토콜 스택(연결성)과 같은 표준 OS 구성 요소를 사용합니다. 전역 복제, 읽기/쓰기에 대 한 1 자리 밀리초 대기 시간, 튜닝 가능한 일관성 수준 등 Azure 인프라에 의존 하는 기능은 에뮬레이터를 사용 하는 경우 적용 되지 않습니다.

[Azure Cosmos DB 데이터 마이그레이션 도구](https://github.com/azure/azure-documentdb-datamigrationtool)를 사용 하 여 Azure Cosmos 에뮬레이터와 Azure Cosmos DB 서비스 간에 데이터를 마이그레이션할 수 있습니다.

## <a name="differences-between-the-emulator-and-the-cloud-service"></a>에뮬레이터와 클라우드 서비스의 차이점

Azure Cosmos 에뮬레이터는 로컬 개발자 워크스테이션에서 실행 되는 에뮬레이트된 환경을 제공 하기 때문에 클라우드에서 에뮬레이터와 Azure Cosmos 계정 사이에 몇 가지 차이점이 있습니다.

* 현재 에뮬레이터의 **데이터 탐색기** 창은 SQL API 클라이언트만 완전히 지원 합니다. MongoDB, Table, Graph 및 Cassandra Api와 같은 Azure Cosmos DB Api에 대 한 **데이터 탐색기** 보기 및 작업은 완전히 지원 되지 않습니다.

* 에뮬레이터는 단일 고정 계정과 잘 알려진 기본 키만 지원 합니다. Azure Cosmos 에뮬레이터를 사용 하는 경우 키를 다시 생성할 수 없지만 [명령줄](emulator-command-line-parameters.md) 옵션을 사용 하 여 기본 키를 변경할 수 있습니다.

* 에뮬레이터를 사용 하면 [프로 비전 된 처리량](set-throughput.md) 모드 에서만 Azure Cosmos 계정을 만들 수 있습니다. 현재는 [서버](serverless.md) 를 사용 하지 않는 모드를 지원 하지 않습니다.

* 에뮬레이터는 확장 가능한 서비스가 아니므로 많은 수의 컨테이너를 지원 하지 않습니다. Azure Cosmos 에뮬레이터를 사용 하는 경우 기본적으로 400 r u/초에 최대 25 개의 고정 크기 컨테이너를 만들 수 있습니다 (Azure Cosmos DB Sdk 에서만 지원 됨) 또는 5 개의 무제한 컨테이너를 만들 수 있습니다. 이 값을 변경 하는 방법에 대 한 자세한 내용은 [파티션 개수 값 설정](emulator-command-line-parameters.md#set-partitioncount) 문서를 참조 하세요.

* 에뮬레이터는 클라우드 서비스와 같은 다양 한 [Azure Cosmos DB 일관성 수준을](consistency-levels.md) 제공 하지 않습니다.

* 에뮬레이터는 [다중 지역 복제](distribute-data-globally.md)를 제공 하지 않습니다.

* Azure Cosmos 에뮬레이터의 복사본은 Azure Cosmos DB 서비스의 최신 변경 내용으로 항상 최신 상태가 아닐 수 있으므로 항상 [Azure Cosmos DB capacity planner](estimate-ru-with-capacity-planner.md) 를 참조 하 여 응용 프로그램의 요구 사항을 정확 하 게 예측 해야 합니다.

* 에뮬레이터는 최대 ID 속성 크기인 254 자를 지원 합니다.

## <a name="install-the-emulator"></a>에뮬레이터 설치

에뮬레이터를 설치 하기 전에 다음 하드웨어 및 소프트웨어 요구 사항이 있는지 확인 합니다.

* 소프트웨어 요구 사항:
  * 현재 Windows Server 2012 R2, Windows Server 2016, 2019 또는 Windows 8, 10 개의 호스트 OS가 지원 됩니다. Active Directory 사용 하도록 설정 된 호스트 OS는 현재 지원 되지 않습니다.
  * 64비트 운영 체제

* 최소 하드웨어 요구 사항:
  * 2GB RAM
  * 10GB의 하드 디스크 여유 공간

* Azure Cosmos 에뮬레이터를 설치, 구성 및 실행 하려면 컴퓨터에 대 한 관리자 권한이 있어야 합니다. 에뮬레이터가 인증서를 추가 하 고 해당 서비스를 실행 하기 위해 방화벽 규칙도 설정 합니다. 따라서 에뮬레이터에서 이러한 작업을 실행 하려면 관리자 권한이 필요 합니다.

시작 하려면 로컬 컴퓨터에 최신 버전의 [Azure Cosmos 에뮬레이터](https://aka.ms/cosmosdb-emulator) 를 다운로드 하 여 설치 합니다. 에뮬레이터를 설치할 때 문제가 발생할 경우 디버깅 하려면 [에뮬레이터 문제 해결](troubleshoot-local-emulator.md) 문서를 참조 하세요.

시스템 요구 사항에 따라이 문서의 다음 섹션에 설명 된 대로 [Windows](#run-on-windows), [Windows용 Docker](#run-on-windows-docker), [Linux 또는 macos](#run-on-linux-macos) 에서 에뮬레이터를 실행할 수 있습니다.

## <a name="check-for-emulator-updates"></a>에뮬레이터 업데이트 확인

에뮬레이터의 각 버전에는 기능 업데이트 또는 버그 수정이 포함 되어 있습니다. 사용 가능한 버전을 확인 하려면 [emulator 릴리스 정보](local-emulator-release-notes.md) 문서를 참조 하세요.

설치 후 기본 설정을 사용한 경우 에뮬레이터에 해당 하는 데이터 는%LOCALAPPDATA%\CosmosDBEmulator 위치에 저장 됩니다. 선택적 데이터 경로 설정을 사용 하 여 다른 위치를 구성할 수 있습니다. 이것은 `/DataPath=PREFERRED_LOCATION` [명령줄 매개 변수로](emulator-command-line-parameters.md)서입니다. 다른 버전을 사용 하는 경우 Azure Cosmos emulator의 특정 버전에서 만든 데이터에 액세스할 수 없습니다. 장기적으로 데이터를 유지 해야 하는 경우 Azure Cosmos 에뮬레이터 대신 Azure Cosmos 계정에 해당 데이터를 저장 하는 것이 좋습니다.

## <a name="use-the-emulator-on-windows"></a><a id="run-on-windows"></a>Windows에서 에뮬레이터 사용

Azure Cosmos 에뮬레이터는 `C:\Program Files\Azure Cosmos DB Emulator` 기본적으로 위치에 설치 됩니다. Windows에서 Azure Cosmos 에뮬레이터를 시작 하려면 **시작** 단추를 선택 하거나 windows 키를 누릅니다. **Azure Cosmos Emulator** 입력을 시작하고 애플리케이션 목록에서 에뮬레이터를 선택합니다.

:::image type="content" source="./media/local-emulator/database-local-emulator-start.png" alt-text="시작 단추를 선택 하거나, Windows 키를 누르고, Azure Cosmos emulator를 입력 하기 시작 하 고, 응용 프로그램 목록에서 에뮬레이터를 선택 합니다.":::

에뮬레이터가 시작 되 면 Windows 작업 표시줄 알림 영역에 아이콘이 표시 됩니다. 그러면 브라우저에서이 URL url의 Azure Cosmos 데이터 탐색기가 자동으로 열립니다 `https://localhost:8081/_explorer/index.html` .

:::image type="content" source="./media/local-emulator/database-local-emulator-taskbar.png" alt-text="시작 단추를 선택 하거나, Windows 키를 누르고, Azure Cosmos emulator를 입력 하기 시작 하 고, 응용 프로그램 목록에서 에뮬레이터를 선택 합니다.":::

명령줄 또는 PowerShell 명령에서 에뮬레이터를 시작 하 고 중지할 수도 있습니다. 자세한 내용은 [명령줄 도구 참조](emulator-command-line-parameters.md) 문서를 참조 하세요.

기본적으로 Azure Cosmos 에뮬레이터는 포트 8081에서 수신 대기 하는 로컬 컴퓨터 ("localhost")에서 실행 됩니다. 주소가 `https://localhost:8081/_explorer/index.html`로 표시됩니다. 탐색기를 닫고 나중에 다시 열려면 브라우저에서 URL을 열거나 아래와 같이 Windows 트레이 아이콘의 Azure Cosmos 에뮬레이터에서 시작할 수 있습니다.

:::image type="content" source="./media/local-emulator/database-local-emulator-data-explorer-launcher.png" alt-text="시작 단추를 선택 하거나, Windows 키를 누르고, Azure Cosmos emulator를 입력 하기 시작 하 고, 응용 프로그램 목록에서 에뮬레이터를 선택 합니다.":::

## <a name="use-the-emulator-on-docker-for-windows"></a><a id="run-on-windows-docker"></a>Windows용 Docker 에뮬레이터 사용

Windows Docker 컨테이너에서 Azure Cosmos 에뮬레이터를 실행할 수 있습니다. 및 자세한 내용은 docker pull 명령에 대 한 [Docker 허브](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) 및 [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) 를 참조 하세요 `Dockerfile` . 현재 에뮬레이터는 Oracle Linux에 대 한 Docker에서 작동 하지 않습니다. Windows용 Docker에서 에뮬레이터를 실행 하려면 다음 지침을 따르십시오.

1. [Windows용 Docker](https://www.docker.com/docker-windows) 설치한 후 도구 모음에서 Docker 아이콘을 마우스 오른쪽 단추로 클릭 하 고 **windows 컨테이너로 전환**을 선택 하 여 windows 컨테이너로 전환 합니다.

1. 다음으로 즐겨 찾는 셸에서 다음 명령을 실행하여 Docker 허브에서 에뮬레이터 이미지를 끌어옵니다.

   ```bash
   docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```

1. 이미지를 시작 하려면 명령줄 또는 PowerShell 환경에 따라 다음 명령을 실행 합니다.

   # <a name="command-line"></a>[명령줄](#tab/cli)

   ```bash

   md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```
   Windows 기반 Docker 이미지는 일반적으로 모든 Windows 호스트 OS와 호환 되지 않을 수 있습니다. 예를 들어 기본 Azure Cosmos emulator 이미지는 Windows 10 및 Windows Server 2016에만 호환 됩니다. Windows Server 2019와 호환 되는 이미지가 필요한 경우 다음 명령을 대신 실행 합니다.

   ```bash
   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%hostDirectory%,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/winsrv2019/azure-cosmos-emulator:latest
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell

   md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

   ```

   응답은 다음과 유사합니다.

   ```bash
   Starting emulator
   Emulator Endpoint: https://172.20.229.193:8081/
   Primary Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
   Exporting SSL Certificate
   You can import the SSL certificate from an administrator command prompt on the host by running:
   cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
   powershell .\importcert.ps1
   --------------------------------------------------------------------------------------------------
   Starting interactive shell
   ```
   ---

   > [!NOTE]
   > 명령을 실행할 때 `docker run` 포트 충돌 오류가 표시 되는 경우 (지정 된 포트가 이미 사용 중인 경우) 포트 번호를 변경 하 여 사용자 지정 포트를 전달 합니다. 예를 들어 "-p 8081:8081" 매개 변수를 "-p 443:8081"로 변경할 수 있습니다.

1. 이제 응답의 에뮬레이터 끝점 및 기본 키를 사용 하 여 TLS/SSL 인증서를 호스트로 가져옵니다. TLS/SSL 인증서를 가져오려면 관리자 명령 프롬프트에서 다음 단계를 실행 합니다.

   # <a name="command-line"></a>[명령줄](#tab/cli)

   ```bash
   cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
   powershell .\importcert.ps1
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell
   cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
   .\importcert.ps1
   ```
   ---

1. 에뮬레이터가 시작 된 후 대화형 셸을 닫으면 에뮬레이터의 컨테이너가 종료 됩니다. 데이터 탐색기를 다시 열려면 브라우저에서 다음 URL로 이동 합니다. 에뮬레이터 엔드포인트가 위에 표시된 응답 메시지에 제공됩니다.

   `https://<emulator endpoint provided in response>/_explorer/index.html`

Linux docker 컨테이너에서 실행 되는 .NET 클라이언트 응용 프로그램이 있고 호스트 컴퓨터에서 Azure Cosmos emulator를 실행 하는 경우 다음 섹션의 지침을 사용 하 여 인증서를 Linux docker 컨테이너로 가져옵니다.

### <a name="regenerate-the-emulator-certificates-when-running-on-a-docker-container"></a>Docker 컨테이너에서 실행 되는 경우 에뮬레이터 인증서 다시 생성

Docker 컨테이너에서 에뮬레이터를 실행 하는 경우 해당 컨테이너를 중지 하 고 다시 시작할 때마다 에뮬레이터와 연결 된 인증서가 다시 생성 됩니다. 때문에 각 컨테이너가 시작 된 후 인증서를 다시 가져와야 합니다. 이 제한을 해결 하기 위해 Docker 작성 파일을 사용 하 여 Docker 컨테이너를 특정 IP 주소 및 컨테이너 이미지에 바인딩할 수 있습니다.

예를 들어 Docker 작성 파일 내에서 다음 구성을 사용할 수 있습니다. 요구 사항에 따라 형식을 지정 해야 합니다. 

```yml
version: '2.4' # Do not upgrade to 3.x yet, unless you plan to use swarm/docker stack: https://github.com/docker/compose/issues/4513

networks:
  default:
    external: false
    ipam:
      driver: default
      config:
        - subnet: "172.16.238.0/24"

services:

  # First create a directory that will hold the emulator traces and certificate to be imported
  # set hostDirectory=C:\emulator\bind-mount
  # mkdir %hostDirectory%

  cosmosdb:
    container_name: "azurecosmosemulator"
    hostname: "azurecosmosemulator"
    image: 'mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator'
    platform: windows
    tty: true
    mem_limit: 3GB
    ports:
        - '8081:8081'
        - '8900:8900'
        - '8901:8901'
        - '8902:8902'
        - '10250:10250'
        - '10251:10251'
        - '10252:10252'
        - '10253:10253'
        - '10254:10254'
        - '10255:10255'
        - '10256:10256'
        - '10350:10350'
    networks:
      default:
        ipv4_address: 172.16.238.246
    volumes:
        - '${hostDirectory}:C:\CosmosDB.Emulator\bind-mount'
```

## <a name="use-the-emulator-on-linux-or-macos"></a><a id="run-on-linux-macos"></a>Linux 또는 macOS에서 에뮬레이터 사용

현재 Azure Cosmos 에뮬레이터는 Windows 에서만 실행할 수 있습니다. Linux 또는 macOS를 사용 하는 경우에는 위 도선 또는 VirtualBox와 같은 하이퍼바이저에서 호스트 되는 Windows 가상 컴퓨터에서 에뮬레이터를 실행할 수 있습니다.

> [!NOTE]
> 하이퍼바이저에서 호스트 되는 Windows 가상 컴퓨터를 다시 시작할 때마다 가상 컴퓨터의 IP 주소가 변경 되기 때문에 인증서를 다시 가져와야 합니다. IP 주소를 유지 하도록 가상 컴퓨터를 구성한 경우에는 인증서를 가져올 필요가 없습니다.

Linux 또는 macOS 환경에서 에뮬레이터를 사용 하려면 다음 단계를 사용 합니다.

1. Windows 가상 머신에서 다음 명령을 실행 하 여 IPv4 주소를 기록해 둡니다.

   ```bash
   ipconfig.exe
   ```

1. 응용 프로그램 내에서 대신에서 반환 된 IPv4 주소를 사용 하도록 끝점 URL을 변경 합니다 `ipconfig.exe` `localhost` .

1. Windows VM에서 다음 옵션을 사용 하 여 명령줄에서 Azure Cosmos emulator를 시작 합니다. 명령줄에서 지 원하는 매개 변수에 대 한 자세한 내용은 [에뮬레이터 명령줄 도구 참조](emulator-command-line-parameters.md):

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM +4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
   ```

1. 마지막으로 Linux 또는 Mac 환경에서 실행 중인 응용 프로그램과 에뮬레이터 간에 인증서 신뢰 프로세스를 해결 해야 합니다. 다음 두 가지 옵션 중 하나를 사용 하 여 인증서를 확인할 수 있습니다.

   1. [에뮬레이터 TLS/SSL 인증서를 Linux 또는 Mac 환경으로 가져오기](#import-certificate) 또는
   2. [응용 프로그램에서 TLS/SSL 유효성 검사를 사용 하지 않도록 설정](#disable-ssl-validation)

### <a name="option-1-import-the-emulator-tlsssl-certificate"></a><a id="import-certificate"></a>옵션 1: 에뮬레이터 TLS/SSL 인증서 가져오기

다음 섹션에서는 에뮬레이터 TLS/SSL 인증서를 Linux 및 macOS 환경으로 가져오는 방법을 보여 줍니다.

#### <a name="linux-environment"></a>Linux 환경

Linux에서 작업하는 경우 .NET은 OpenSSL을 사용하여 유효성 검사를 수행합니다.

1. [인증서를 PFX 형식으로 내보냅니다](local-emulator-export-ssl-certificates.md#export-emulator-certificate). PFX 옵션은 개인 키를 내보내도록 선택할 때 사용할 수 있습니다.

1. 해당 PFX 파일을 Linux 환경에 복사합니다.

1. PFX 파일을 CRT 파일로 변환

   ```bash
   openssl pkcs12 -in YourPFX.pfx -clcerts -nokeys -out YourCTR.crt
   ```

1. Linux 배포에서 사용자 지정 인증서가 포함된 폴더에 CRT 파일을 복사합니다. 일반적으로 Debian 배포판에서는 `/usr/local/share/ca-certificates/`에 있습니다.

   ```bash
   cp YourCTR.crt /usr/local/share/ca-certificates/
   ```

1. TLS/SSL 인증서를 업데이트 합니다. 그러면 폴더가 업데이트 됩니다 `/etc/ssl/certs/` .

   ```bash
   update-ca-certificates
   ```

#### <a name="macos-environment"></a>macOS 환경

Mac에서 작업하는 경우 다음 단계를 사용합니다.

1. [인증서를 PFX 형식으로 내보냅니다](local-emulator-export-ssl-certificates.md#export-emulator-certificate). PFX 옵션은 개인 키를 내보내도록 선택할 때 사용할 수 있습니다.

1. 해당 PFX 파일을 Mac 환경에 복사합니다.

1. *키 집합 액세스* 애플리케이션을 열고 PFX 파일을 가져옵니다.

1. 인증서 목록을 열고 이름이 `localhost`인 인증서를 식별합니다.

1. 특정 항목에 대한 상황에 맞는 메뉴를 열고 *항목 가져오기*를 선택하고 *신뢰* > *이 인증서를 사용할 경우* 옵션에서 *항상 신뢰*를 선택합니다. 

   :::image type="content" source="./media/local-emulator/mac-trust-certificate.png" alt-text="시작 단추를 선택 하거나, Windows 키를 누르고, Azure Cosmos emulator를 입력 하기 시작 하 고, 응용 프로그램 목록에서 에뮬레이터를 선택 합니다.":::
  
### <a name="option-2-disable-the-ssl-validation-in-the-application"></a><a id="disable-ssl-validation"></a>옵션 2: 응용 프로그램에서 SSL 유효성 검사를 사용 하지 않도록 설정

SSL 유효성 검사를 사용 하지 않도록 설정 하는 것은 개발 목적 으로만 권장 되며 프로덕션 환경에서 실행 하는 경우에는 수행할 수 없습니다. 다음 예에서는 .NET 및 Node.js 응용 프로그램에 대해 SSL 유효성 검사를 사용 하지 않도록 설정 하는 방법을 보여 줍니다.

# <a name="net-standard-21"></a>[.NET Standard 2.1 이상](#tab/ssl-netstd21)

.NET Standard 2.1 이상과 호환 되는 프레임 워크에서 실행 되는 응용 프로그램의 경우를 활용할 수 있습니다 `CosmosClientOptions.HttpClientFactory` .

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard21)]

# <a name="net-standard-20"></a>[.NET Standard 2.0](#tab/ssl-netstd20)

.NET Standard 2.0와 호환 되는 프레임 워크에서 실행 되는 응용 프로그램의 경우를 활용할 수 있습니다 `CosmosClientOptions.HttpClientFactory` .

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard20)]

# <a name="nodejs"></a>[Node.JS](#tab/ssl-nodejs)

Node.js 응용 프로그램의 경우 `package.json` `NODE_TLS_REJECT_UNAUTHORIZED` 응용 프로그램을 시작 하는 동안를 설정 하도록 파일을 수정할 수 있습니다.

```json
"start": NODE_TLS_REJECT_UNAUTHORIZED=0 node app.js
```
--- 

## <a name="enable-access-to-emulator-on-a-local-network"></a>로컬 네트워크의 에뮬레이터에 대 한 액세스 사용

단일 네트워크를 사용 하는 컴퓨터가 여러 대 있고 한 컴퓨터에서 에뮬레이터를 설정 하 고 다른 컴퓨터에서 해당 에뮬레이터에 액세스 하려는 경우 이 경우 로컬 네트워크의 에뮬레이터에 대 한 액세스를 사용 하도록 설정 해야 합니다.

로컬 네트워크에서 에뮬레이터를 실행할 수 있습니다. 네트워크 액세스를 사용하도록 설정하려면 [명령줄](emulator-command-line-parameters.md)에 `/AllowNetworkAccess` 옵션을 지정합니다. 여기에는 `/Key=key_string` 또는 `/KeyFile=file_name`도 지정해야 합니다. `/GenKeyFile=file_name`을 사용하여 미리 설정된 임의 키로 파일을 생성할 수 있습니다. 그런 다음, `/KeyFile=file_name` 또는 `/Key=contents_of_file`에 전달합니다.

네트워크 액세스를 처음으로 사용 하도록 설정 하려면 사용자가 에뮬레이터를 종료 하 고 에뮬레이터의 데이터 디렉터리 *%LOCALAPPDATA%\CosmosDBEmulator*를 삭제 해야 합니다.

## <a name="authenticate-connections-when-using-emulator"></a><a id="authenticate-requests"></a>에뮬레이터를 사용 하는 경우 연결 인증

클라우드의 Azure Cosmos DB와 마찬가지로 Azure Cosmos 에뮬레이터에 대해 수행 하는 모든 요청은 인증 되어야 합니다. Azure Cosmos emulator는 TLS를 통한 보안 통신만 지원 합니다. Azure Cosmos emulator는 단일 고정 계정 및 기본 키 인증에 대 한 잘 알려진 인증 키를 지원 합니다. Azure Cosmos Emulator에서 사용할 수 있는 자격 증명은 이 계정과 키뿐입니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> Azure Cosmos emulator에서 지 원하는 기본 키는 에뮬레이터 에서만 사용할 수 있습니다. Azure Cosmos Emulator에서는 프로덕션 Azure Cosmos DB 계정 및 키를 사용할 수 없습니다.

> [!NOTE]
> /Skey 옵션을 사용 하 여 에뮬레이터를 시작한 경우 기본 키 대신 생성 된 키를 사용 합니다 `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` . /Key 옵션에 대한 자세한 내용은 [명령줄 도구 참조](emulator-command-line-parameters.md)를 참조하세요.

## <a name="connect-to-different-apis-with-the-emulator"></a><a id="connect-with-emulator-apis"></a>에뮬레이터를 사용 하 여 다른 Api에 연결

### <a name="sql-api"></a>SQL API

Azure Cosmos emulator가 데스크톱에서 실행 되 면 지원 되는 [AZURE COSMOS DB SDK](sql-api-sdk-dotnet-standard.md) 또는 [Azure Cosmos DB REST API](/rest/api/cosmos-db/) 를 사용 하 여 에뮬레이터와 상호 작용할 수 있습니다. Azure Cosmos 에뮬레이터에는 SQL API 용 컨테이너 또는 Mongo DB API에 대 한 Azure Cosmos DB를 만들 수 있는 기본 제공 데이터 탐색기도 포함 되어 있습니다. 데이터 탐색기를 사용 하 여 코드를 작성 하지 않고도 항목을 보고 편집할 수 있습니다.

```csharp
// Connect to the Azure Cosmos emulator running locally
CosmosClient client = new CosmosClient(
   "https://localhost:8081", 
    "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB의 API for MongoDB

Azure Cosmos emulator가 데스크톱에서 실행 되 면 [MongoDB에 대해 Azure Cosmos DB의 API](mongodb-introduction.md) 를 사용 하 여 에뮬레이터와 상호 작용할 수 있습니다. "/EnableMongoDbEndpoint"를 사용 하는 관리자 권한으로 [명령 프롬프트](emulator-command-line-parameters.md) 에서 에뮬레이터를 시작 합니다. 그리고 다음 연결 문자열을 사용하여 MongoDB API 계정에 연결합니다.

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>테이블 API

Azure Cosmos emulator가 데스크톱에서 실행 되 면 [Azure Cosmos DB TABLE API SDK](table-storage-how-to-use-dotnet.md) 를 사용 하 여 에뮬레이터와 상호 작용할 수 있습니다. "/EnableTableEndpoint"를 사용 하는 관리자 권한으로 [명령 프롬프트](emulator-command-line-parameters.md) 에서 에뮬레이터를 시작 합니다. 그런 다음, 아래 코드를 실행하여 Table API 계정에 연결합니다.

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Table;
using CloudTable = Microsoft.WindowsAzure.Storage.Table.CloudTable;
using CloudTableClient = Microsoft.WindowsAzure.Storage.Table.CloudTableClient;

string connectionString = "DefaultEndpointsProtocol=http;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=http://localhost:8902/;";

CloudStorageAccount account = CloudStorageAccount.Parse(connectionString);
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("testtable");
table.CreateIfNotExists();
table.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
```

### <a name="cassandra-api"></a>Cassandra API

관리자 [명령 프롬프트](emulator-command-line-parameters.md) 에서 "/EnableCassandraEndpoint"를 사용 하 여 에뮬레이터를 시작 합니다. 또는 환경 변수 `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true`를 설정할 수도 있습니다.

1. [Python 2.7 설치](https://www.python.org/downloads/release/python-2716/)

1. [Cassandra CLI/CQLSH 설치](https://cassandra.apache.org/download/)

1. 일반 명령 프롬프트 창에서 다음 명령을 실행합니다.

   ```bash
   set Path=c:\Python27;%Path%
   cd /d C:\sdk\apache-cassandra-3.11.3\bin
   set SSL_VERSION=TLSv1_2
   set SSL_VALIDATE=false
   cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
   ```

1. CQLSH 셸에서 다음 명령을 실행하여 Cassandra 엔드포인트에 연결합니다.

   ```bash
   CREATE KEYSPACE MyKeySpace WITH replication = {'class':'MyClass', 'replication_factor': 1};
   DESCRIBE keyspaces;
   USE mykeyspace;
   CREATE table table1(my_id int PRIMARY KEY, my_name text, my_desc text);
   INSERT into table1 (my_id, my_name, my_desc) values( 1, 'name1', 'description 1');
   SELECT * from table1;
   EXIT
   ```

### <a name="gremlin-api"></a>Gremlin API

관리자 [명령 프롬프트](emulator-command-line-parameters.md)에서 "/EnableGremlinEndpoint"를 사용 하 여 에뮬레이터를 시작 합니다. 또는 환경 변수 `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`를 설정할 수도 있습니다.

1. [apache-tinkerpop-gremlin-console-3.3.4를 설치합니다](https://archive.apache.org/dist/tinkerpop/3.3.4).

1. 에뮬레이터의 데이터 탐색기에서 데이터베이스 "db1" 및 컬렉션 "coll1"를 만듭니다. 파티션 키에 대해 "/name"를 선택 합니다.

1. 일반 명령 프롬프트 창에서 다음 명령을 실행합니다.

   ```bash
   cd /d C:\sdk\apache-tinkerpop-gremlin-console-3.3.4-bin\apache-tinkerpop-gremlin-console-3.3.4
  
   copy /y conf\remote.yaml conf\remote-localcompute.yaml
   notepad.exe conf\remote-localcompute.yaml
     hosts: [localhost]
     port: 8901
     username: /dbs/db1/colls/coll1
     password: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
     connectionPool: {
     enableSsl: false}
     serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0,
     config: { serializeResultToString: true  }}

   bin\gremlin.bat
   ```

1. Gremlin shell에서 다음 명령을 실행 하 여 Gremlin 끝점에 연결 합니다.

   ```bash
   :remote connect tinkerpop.server conf/remote-localcompute.yaml
   :remote console
   :> g.V()
   :> g.addV('person1').property(id, '1').property('name', 'somename1')
   :> g.addV('person2').property(id, '2').property('name', 'somename2')
   :> g.V()
   ```

## <a name="uninstall-the-local-emulator"></a><a id="uninstall"></a>로컬 에뮬레이터 제거

다음 단계를 사용 하 여 에뮬레이터를 제거 합니다.

1. 시스템 트레이에서 **Azure Cosmos 에뮬레이터** 아이콘을 마우스 오른쪽 단추로 클릭 한 다음 **끝내기**를 선택 하 여 로컬 에뮬레이터의 모든 열린 인스턴스를 종료 합니다. 모든 인스턴스를 종료하는 데는 1분 정도 걸립니다.

1. Windows 검색 상자에 **앱 & 기능** 을 입력 하 고 **앱 & 기능 (시스템 설정)** 결과를 선택 합니다.

1. 앱 목록에서 **Azure Cosmos DB Emulator**로 스크롤하고, **제거**를 클릭 한 다음, 확인을 클릭 하 고 **제거** 를 다시 선택 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 무료 로컬 개발을 위해 로컬 에뮬레이터를 사용 하는 방법을 알아보았습니다. 이제 다음 문서를 진행할 수 있습니다.

* [Java, Python 및 Node.js apps와 함께 사용 하기 위해 Azure Cosmos 에뮬레이터 인증서를 내보냅니다.](local-emulator-export-ssl-certificates.md)
* [명령줄 매개 변수 및 PowerShell 명령을 사용 하 여 에뮬레이터 제어](emulator-command-line-parameters.md)
* [에뮬레이터를 사용 하 여 문제 디버그](troubleshoot-local-emulator.md)
