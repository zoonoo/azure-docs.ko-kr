---
title: Azure Cosmos DB 에뮬레이터를 사용하여 로컬로 설치 및 개발
description: Windows, Linux, macOS 및 Windows docker 환경에서 Azure Cosmos DB 에뮬레이터를 설치하고 사용하는 방법을 알아봅니다. 이 에뮬레이터를 사용하여 Azure 구독을 구입하지 않고도 무료로 로컬에서 애플리케이션을 개발하고 테스트할 수 있습니다.
ms.service: cosmos-db
ms.topic: how-to
author: StefArroyo
ms.author: esarroyo
ms.date: 09/22/2020
ms.custom: devx-track-csharp, contperf-fy21q1
ms.openlocfilehash: 3c8e2559d1dc8fce27815d498578d352fa9fe498
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110372828"
---
# <a name="install-and-use-the-azure-cosmos-db-emulator-for-local-development-and-testing"></a>로컬 개발 및 테스트에 Azure Cosmos DB 에뮬레이터 설치 및 사용
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB 에뮬레이터는 개발 목적으로 Azure Cosmos DB 서비스를 에뮬레이트하는 로컬 환경을 제공합니다. Azure Cosmos DB 에뮬레이터를 사용하면 Azure 구독을 구입하거나 비용을 발생시키지 않고도 로컬에서 애플리케이션을 테스트할 수 있습니다. Azure Cosmos DB 에뮬레이터에서 애플리케이션이 작동하는 방식에 만족하는 경우 Azure Cosmos 계정을 클라우드에서 사용하도록 전환할 수 있습니다. 이 문서에서는 Windows, Linux, macOS 및 Windows docker 환경에서 에뮬레이터를 설치하고 사용하는 방법을 설명합니다.

## <a name="download-the-emulator"></a>에뮬레이터 다운로드

시작하려면 로컬 컴퓨터에 최신 버전의 Azure Cosmos DB 에뮬레이터를 다운로드하여 설치합니다. [에뮬레이터 릴리스 정보](local-emulator-release-notes.md) 문서에서는 사용 가능한 모든 버전 및 각 릴리스에 적용된 기능 업데이트를 나열합니다.

:::image type="icon" source="media/local-emulator/download-icon.png" border="false"::: **[Azure Cosmos DB 에뮬레이터 다운로드](https://aka.ms/cosmosdb-emulator)**

[SQL](local-emulator.md#sql-api), [Cassandra](local-emulator.md#cassandra-api), [MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb), [Gremlin](local-emulator.md#gremlin-api) 및 [Table](local-emulator.md#table-api) API 계정으로 Azure Cosmos DB 에뮬레이터를 사용하여 애플리케이션을 개발할 수 있습니다. 현재 에뮬레이터의 데이터 탐색기는 SQL 데이터 보기만을 완벽하게 지원합니다. MongoDB, Gremlin/Graph 및 Cassandra 클라이언트 애플리케이션을 사용하여 만든 데이터는 현재 볼 수 없습니다. 자세한 내용은 다양한 API에서 [에뮬레이터 엔드포인트에 연결하는 방법](#connect-with-emulator-apis)을 참조하세요.

## <a name="how-does-the-emulator-work"></a>에뮬레이터 작동 방식

Azure Cosmos DB 에뮬레이터는 신뢰도 있는 Azure Cosmos DB 서비스의 에뮬레이션을 제공합니다. Azure Cosmos DB와 동일한 기능을 지원합니다. 여기에는 데이터 만들기 및 쿼리, 컨테이너 프로비저닝 및 스케일링, 저장 프로시저 및 트리거 실행이 포함됩니다. Azure Cosmos DB 에뮬레이터를 사용하여 애플리케이션을 개발 및 테스트하고 Azure Cosmos DB 연결 엔드포인트를 업데이트하여 글로벌 규모로 Azure에 배포할 수 있습니다.

Azure Cosmos DB 서비스의 에뮬레이션이 충실한 경우 에뮬레이터의 구현은 서비스와 다릅니다. 예를 들어 에뮬레이터는 로컬 파일 시스템(지속성) 및 HTTPS 프로토콜 스택(연결성)과 같은 표준 OS 구성 요소를 사용합니다. 따라서 에뮬레이터를 사용할 때 글로벌 복제, 한 자리 밀리초 읽기/쓰기 대기 시간, 튜닝 가능한 일관성 수준 등 Azure 인프라를 기반으로 하는 기능은 적용할 수 없습니다.

[Azure Cosmos DB 데이터 마이그레이션 도구](https://github.com/azure/azure-documentdb-datamigrationtool)를 사용하여 Azure Cosmos DB Emulator와 Azure Cosmos DB 서비스 간에 데이터를 마이그레이션할 수 있습니다.

## <a name="differences-between-the-emulator-and-the-cloud-service"></a>에뮬레이터와 클라우드 서비스 간 차이

Azure Cosmos DB 에뮬레이터는 로컬 개발자 워크스테이션에서 실행되는 에뮬레이트된 환경을 제공하기 때문에 에뮬레이터와 클라우드의 Azure Cosmos 계정 사이에는 기능 면에서 다음과 같은 몇 가지 차이가 있습니다.

* 현재 에뮬레이터의 **데이터 탐색기** 창은 SQL API 클라이언트만 완전히 지원합니다. **데이터 탐색기** 뷰 및 Azure Cosmos DB API에 대한 작업(예: MongoDB, Table, Graph 및 Cassandra API)은 완전히 지원되지 않습니다.

* 에뮬레이터는 단일 고정 계정과 잘 알려진 기본 키만 지원합니다. Azure Cosmos DB 에뮬레이터를 사용하는 경우 키를 다시 생성할 수 없지만 [명령줄](emulator-command-line-parameters.md) 옵션을 사용하여 기본 키를 변경할 수 있습니다.

* 에뮬레이터를 사용하면 [프로비저닝된 처리량](set-throughput.md) 모드에서만 Azure Cosmos 계정을 만들 수 있습니다. 현재는 [서버리스](serverless.md) 모드는 지원하지 않습니다.

* 에뮬레이터는 확장성 있는 서비스가 아니며 많은 수의 컨테이너를 지원하지 않습니다. Azure Cosmos DB 에뮬레이터를 사용할 때 기본적으로 400 RU/s에서 고정 크기 컨테이너를 25개까지 만들거나(Azure Cosmos DB SDK를 통해서만 지원됨) 무제한 컨테이너 5개를 만들 수 있습니다. 이 값을 변경하는 방법에 대한 자세한 내용은 [PartitionCount 값 설정](emulator-command-line-parameters.md#set-partitioncount) 문서를 참조하세요.

* 에뮬레이터는 클라우드 서비스와 같은 다양한 [Azure Cosmos DB 일관성 수준](consistency-levels.md)을 제공하지 않습니다.

* 에뮬레이터는 [다중 지역 복제](distribute-data-globally.md)를 제공하지 않습니다.

* Azure Cosmos DB Emulator의 복사본은 항상 Azure Cosmos DB 서비스의 최신 변경 사항이 적용된 최신 상태로 항상 유지되지 않을 수도 있으므로 [Azure Cosmos DB Capacity Planner](estimate-ru-with-capacity-planner.md)를 사용하여 애플리케이션에 요구되는 처리량(RU)을 정확하게 예측해야 합니다.

* 에뮬레이터는 최대 ID 속성 크기인 254자를 지원합니다.

## <a name="install-the-emulator"></a>에뮬레이터 설치

에뮬레이터를 설치하기 전에 다음 하드웨어 및 소프트웨어 요구 사항이 충족되었는지 확인합니다.

* 소프트웨어 요구 사항:
  * 현재 Windows Server 2016, 2019 또는 Windows 10 호스트 OS가 지원됩니다. Active Directory를 사용하도록 설정한 호스트 OS는 현재 지원되지 않습니다.
  * 64비트 운영 체제

* 최소 하드웨어 요구 사항:
  * 2GB RAM
  * 10GB의 하드 디스크 여유 공간

* Azure Cosmos DB 에뮬레이터를 설치, 구성 및 실행하려면 컴퓨터에 대한 관리 권한이 있어야 합니다. 에뮬레이터는 인증서를 추가하고 서비스를 실행하기 위해 방화벽 규칙도 설정합니다. 따라서 에뮬레이터에서 이러한 작업을 실행하려면 관리자 권한이 필요합니다.

시작하려면 로컬 컴퓨터에 최신 버전의 [Azure Cosmos DB 에뮬레이터](https://aka.ms/cosmosdb-emulator) 를 다운로드하여 설치합니다. 에뮬레이터를 설치할 때 문제가 발생할 경우 디버깅하려면 [에뮬레이터 문제 해결](troubleshoot-local-emulator.md) 문서를 참조하세요.

시스템 요구 사항에 따라 이 문서의 다음 섹션에 설명된 대로 [Windows](#run-on-windows), [Windows용 Docker](local-emulator-on-docker-windows.md), [Linux 또는 macos](#run-on-linux-macos)에서 에뮬레이터를 실행할 수 있습니다.

## <a name="check-for-emulator-updates"></a>에뮬레이터 업데이트 확인

에뮬레이터의 각 버전에는 기능 업데이트 또는 버그 수정이 포함되어 있습니다. 사용 가능한 버전을 확인하려면 [에뮬레이터 릴리스 정보](local-emulator-release-notes.md) 문서를 참조하세요.

설치 후에 기본 설정을 사용한 경우 에뮬레이터에 해당하는 데이터 는 %LOCALAPPDATA%\CosmosDBEmulator 위치에 저장됩니다. 선택적 데이터 경로 설정을 사용하여 다른 위치를 구성할 수 있습니다. 즉, `/DataPath=PREFERRED_LOCATION`을 [명령줄 매개 변수](emulator-command-line-parameters.md)로 사용합니다. Azure Cosmos DB 에뮬레이터의 특정 버전에서 만든 데이터에 다른 버전에서 액세스하지 못할 수도 있습니다. 데이터를 장기간 보존하려면 Azure Cosmos DB 에뮬레이터 대신 Azure Cosmos 계정에 저장하는 것이 좋습니다.

## <a name="use-the-emulator-on-windows"></a><a id="run-on-windows"></a>Windows에서 에뮬레이터 사용

Azure Cosmos DB 에뮬레이터는 기본적으로 `C:\Program Files\Azure Cosmos DB Emulator` 위치에 설치됩니다. Windows에서 Azure Cosmos DB 에뮬레이터를 시작하려면 **시작** 단추를 선택하거나 Windows 키를 누릅니다. **Azure Cosmos DB 에뮬레이터** 입력을 시작하고 애플리케이션 목록에서 해당 에뮬레이터를 선택합니다.

:::image type="content" source="./media/local-emulator/database-local-emulator-start.png" alt-text="시작 단추를 선택하거나 Windows 키를 누르고 Azure Cosmos DB 에뮬레이터 입력을 시작한 후 애플리케이션 목록에서 해당 에뮬레이터를 선택합니다.":::

에뮬레이터가 시작되면 Windows 작업 표시줄 알림 영역에 아이콘이 표시됩니다. 그러면 브라우저의 이 `https://localhost:8081/_explorer/index.html` URL에서 Azure Cosmos 데이터 탐색기가 자동으로 열립니다.

:::image type="content" source="./media/local-emulator/database-local-emulator-taskbar.png" alt-text="Azure Cosmos DB 로컬 에뮬레이터 작업 표시줄 알림":::

명령줄 또는 PowerShell 명령에서 에뮬레이터를 시작 및 중지할 수도 있습니다. 자세한 내용은 [명령줄 도구 참조](emulator-command-line-parameters.md) 문서를 참조하세요.

기본적으로 Azure Cosmos DB 에뮬레이터는 포트 8081에서 수신 대기하는 로컬 컴퓨터("localhost")에서 실행됩니다. 주소가 `https://localhost:8081/_explorer/index.html`로 표시됩니다. 탐색기를 닫고 나중에 다시 열고 싶은 경우, 브라우저에서 URL을 열거나 아래와 같이 Windows 트레이 아이콘의 Azure Cosmos DB 에뮬레이터에서 실행할 수 있습니다.

:::image type="content" source="./media/local-emulator/database-local-emulator-data-explorer-launcher.png" alt-text="Azure Cosmos 로컬 에뮬레이터 데이터 탐색기 시작 관리자":::

## <a name="use-the-emulator-on-linux-or-macos"></a><a id="run-on-linux-macos"></a>Linux 또는 macOS에서 에뮬레이터 사용

현재 Azure Cosmos DB 에뮬레이터는 Windows에서만 실행할 수 있습니다. Linux 또는 macOS를 사용 중인 경우 Parallels 또는 VirtualBox 등 하이퍼바이저가 호스트된 Windows 가상 머신에서 에뮬레이터를 실행할 수 있습니다.

> [!NOTE]
> 하이퍼바이저에서 호스트되는 Windows 가상 머신을 다시 시작할 때마다 가상 머신의 IP 주소가 변경되기 때문에 인증서를 다시 가져와야 합니다. IP 주소를 유지하도록 가상 머신을 구성한 경우에는 인증서를 가져올 필요가 없습니다.

Linux 또는 macOS 환경에서 에뮬레이터를 사용하려면 다음 단계를 사용합니다.

1. Windows 가상 머신에서 다음 명령을 실행하고 IPv4 주소를 기록해 둡니다.

   ```bash
   ipconfig.exe
   ```

1. 애플리케이션 내에서 `localhost` 대신 `ipconfig.exe`에서 반환된 IPv4 주소를 사용하도록 엔드포인트 URL을 변경합니다.

1. Windows VM에서 다음 옵션을 사용하여 명령줄에서 Azure Cosmos DB 에뮬레이터를 시작합니다. 명령줄에서 지원하는 매개 변수에 대한 자세한 내용은 [에뮬레이터 명령줄 도구 참조](emulator-command-line-parameters.md)를 참조하세요.

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
   ```

1. 마지막으로 Linux 또는 Mac 환경에서 실행 중인 애플리케이션과 에뮬레이터 간에 인증서 신뢰 프로세스를 해결해야 합니다. 다음 두 가지 옵션 중 하나를 사용하여 인증서를 확인할 수 있습니다.

   1. [에뮬레이터 TLS/SSL 인증서를 Linux 또는 Mac 환경으로 가져오기](#import-certificate) 또는
   2. [애플리케이션에서 TLS/SSL 유효성 검사를 사용하지 않도록 설정](#disable-ssl-validation)

### <a name="option-1-import-the-emulator-tlsssl-certificate"></a><a id="import-certificate"></a>옵션 1: 에뮬레이터 TLS/SSL 인증서 가져오기

다음 섹션에서는 에뮬레이터 TLS/SSL 인증서를 Linux 및 macOS 환경으로 가져오는 방법을 보여 줍니다.

#### <a name="linux-environment"></a>Linux 환경

Linux에서 작업하는 경우 .NET은 OpenSSL을 사용하여 유효성 검사를 수행합니다.

1. [PFX 형식으로 인증서 내보내기](local-emulator-export-ssl-certificates.md#export-emulator-certificate). PFX 옵션은 프라이빗 키를 내보내도록 선택할 때 사용할 수 있습니다.

1. 해당 PFX 파일을 Linux 환경에 복사합니다.

1. PFX 파일을 CRT 파일로 변환

   ```bash
   openssl pkcs12 -in YourPFX.pfx -clcerts -nokeys -out YourCTR.crt
   ```

1. Linux 배포에서 사용자 지정 인증서가 포함된 폴더에 CRT 파일을 복사합니다. 일반적으로 Debian 배포판에서는 `/usr/local/share/ca-certificates/`에 있습니다.

   ```bash
   cp YourCTR.crt /usr/local/share/ca-certificates/
   ```

1. TLS/SSL 인증서를 업데이트합니다. 그러면 `/etc/ssl/certs/` 폴더가 업데이트됩니다.

   ```bash
   update-ca-certificates
   ```

#### <a name="macos-environment"></a>macOS 환경

Mac에서 작업하는 경우 다음 단계를 사용합니다.

1. [PFX 형식으로 인증서 내보내기](local-emulator-export-ssl-certificates.md#export-emulator-certificate). PFX 옵션은 프라이빗 키를 내보내도록 선택할 때 사용할 수 있습니다.

1. 해당 PFX 파일을 Mac 환경에 복사합니다.

1. *키 집합 액세스* 애플리케이션을 열고 PFX 파일을 가져옵니다.

1. 인증서 목록을 열고 이름이 `localhost`인 인증서를 식별합니다.

1. 특정 항목에 대한 상황에 맞는 메뉴를 열고 *항목 가져오기* 를 선택하고 *신뢰* > *이 인증서를 사용할 경우* 옵션에서 *항상 신뢰* 를 선택합니다. 

   :::image type="content" source="./media/local-emulator/mac-trust-certificate.png" alt-text="특정 항목에 대한 상황에 맞는 메뉴를 열고 항목 가져오기를 선택하고 신뢰 - 이 인증서를 사용할 경우 옵션에서 항상 신뢰를 선택합니다.":::
  
### <a name="option-2-disable-the-ssl-validation-in-the-application"></a><a id="disable-ssl-validation"></a>옵션 2: 애플리케이션에서 SSL 유효성 검사를 사용하지 않도록 설정

SSL 유효성 검사를 사용하지 않도록 설정하는 것은 개발 목적으로만 권장되며 프로덕션 환경에서 실행하는 경우에는 수행할 수 없습니다. 다음 예에서는 .NET 및 Node.js 애플리케이션에 대해 SSL 유효성 검사를 사용하지 않도록 설정하는 방법을 보여 줍니다.

# <a name="net-standard-21"></a>[.NET Standard 2.1+](#tab/ssl-netstd21)

.NET Standard 2.1 이상과 호환되는 프레임워크에서 실행되는 애플리케이션의 경우 `CosmosClientOptions.HttpClientFactory`를 활용할 수 있습니다.

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard21)]

# <a name="net-standard-20"></a>[.NET Standard 2.0](#tab/ssl-netstd20)

.NET Standard 2.0과 호환되는 프레임워크에서 실행되는 애플리케이션의 경우 `CosmosClientOptions.HttpClientFactory`를 활용할 수 있습니다.

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard20)]

# <a name="nodejs"></a>[Node.JS](#tab/ssl-nodejs)

Node.js 애플리케이션의 경우 애플리케이션을 시작하는 동안 `package.json` 파일을 `NODE_TLS_REJECT_UNAUTHORIZED`로 설정하도록 수정할 수 있습니다.

```json
"start": NODE_TLS_REJECT_UNAUTHORIZED=0 node app.js
```
--- 

## <a name="enable-access-to-emulator-on-a-local-network"></a>로컬 네트워크의 에뮬레이터에 대한 액세스 사용

단일 네트워크를 사용하는 여러 대의 머신이 있고 한 머신에서 에뮬레이터를 설정하고 다른 머신에서 해당 에뮬레이터에 액세스하려고 합니다. 이 경우 로컬 네트워크의 에뮬레이터에 대한 액세스를 사용하도록 설정해야 합니다.

로컬 네트워크에서 에뮬레이터를 실행할 수 있습니다. 네트워크 액세스를 사용하도록 설정하려면 [명령줄](emulator-command-line-parameters.md)에 `/AllowNetworkAccess` 옵션을 지정합니다. 여기에는 `/Key=key_string` 또는 `/KeyFile=file_name`도 지정해야 합니다. `/GenKeyFile=file_name`을 사용하여 미리 설정된 임의 키로 파일을 생성할 수 있습니다. 그런 다음, `/KeyFile=file_name` 또는 `/Key=contents_of_file`에 전달합니다.

처음에 네트워크 액세스를 사용하도록 설정하려면 사용자는 에뮬레이터를 종료하고 에뮬레이터의 데이터 디렉터리( *%LOCALAPPDATA%\CosmosDBEmulator*)를 삭제해야 합니다.

## <a name="authenticate-connections-when-using-emulator"></a><a id="authenticate-requests"></a>에뮬레이터를 사용하는 경우 연결 인증

클라우드의 Azure Cosmos DB와 마찬가지로 Azure Cosmos DB 에뮬레이터에 대한 모든 요청을 인증해야 합니다. Azure Cosmos DB 에뮬레이터는 TLS를 통한 보안 통신만 지원합니다. Azure Cosmos DB 에뮬레이터는 단일 고정 계정과 기본 키 인증에 대해 알려진 인증 키를 지원합니다. Azure Cosmos DB 에뮬레이터에서 사용할 수 있는 자격 증명은 이 계정과 키뿐입니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> Azure Cosmos DB 에뮬레이터에서 지원하는 기본 키는 에뮬레이터 전용입니다. Azure Cosmos DB 에뮬레이터에서 프로덕션 Azure Cosmos DB 계정 및 키를 사용할 수 없습니다.

> [!NOTE]
> /Key 옵션을 사용하여 에뮬레이터를 시작한 경우에는 기본 키 `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` 대신 생성된 키를 사용하세요. /Key 옵션에 대한 자세한 내용은 [명령줄 도구 참조](emulator-command-line-parameters.md)를 참조하세요.

## <a name="connect-to-different-apis-with-the-emulator"></a><a id="connect-with-emulator-apis"></a>에뮬레이터를 사용하여 다른 API에 연결

### <a name="sql-api"></a>SQL API

Azure Cosmos DB 에뮬레이터를 데스크톱에서 실행하는 경우 지원되는 [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md) 또는 [Azure Cosmos DB REST API](/rest/api/cosmos-db/)를 사용하여 에뮬레이터와 상호 작용할 수 있습니다. Azure Cosmos DB Emulator에는 SQL API용 컨테이너 또는 Mongodb API용 Azure Cosmos DB를 만들 수 있는 기본 제공 데이터 탐색기도 포함되어 있습니다. 데이터 탐색기를 사용하여 코드를 작성하지 않고도 항목을 보고 편집할 수 있습니다.

```csharp
// Connect to the Azure Cosmos DB Emulator running locally
CosmosClient client = new CosmosClient(
   "https://localhost:8081", 
    "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB의 API for MongoDB

Azure Cosmos DB 에뮬레이터를 데스크톱에서 실행한 후에는 [Azure Cosmos DB의 API for MongoDB](mongodb-introduction.md)를 사용하여 에뮬레이터와 상호 작용할 수 있습니다. "/EnableMongoDbEndpoint"를 사용하여 [명령 프롬프트](emulator-command-line-parameters.md)에서 관리자로 에뮬레이터를 시작합니다. 그리고 다음 연결 문자열을 사용하여 MongoDB API 계정에 연결합니다.

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>테이블 API

Azure Cosmos DB 에뮬레이터를 데스크톱에서 실행하면, [Azure Cosmos DB Table API SDK](./tutorial-develop-table-dotnet.md)를 사용하여 에뮬레이터와 상호 작용할 수 있습니다. "/EnableTableEndpoint"를 사용하여 [명령 프롬프트](emulator-command-line-parameters.md)에서 관리자로 에뮬레이터를 시작합니다. 그런 다음, 아래 코드를 실행하여 Table API 계정에 연결합니다.

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

"/EnableCassandraEndpoint"를 사용하여 관리자 [명령 프롬프트](emulator-command-line-parameters.md)에서 에뮬레이터를 시작합니다. 또는 환경 변수 `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true`를 설정할 수도 있습니다.

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

"/EnableGremlinEndpoint"를 사용하여 관리자 [명령 프롬프트](emulator-command-line-parameters.md)에서 에뮬레이터를 시작합니다. 또는 환경 변수 `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`를 설정할 수도 있습니다.

1. [apache-tinkerpop-gremlin-console-3.3.4를 설치합니다](https://archive.apache.org/dist/tinkerpop/3.3.4).

1. 에뮬레이터의 데이터 탐색기에서 "db1" 데이터베이스와 "coll1" 컬렉션을 생성합니다. 파티션 키의 경우 "/name"을 선택합니다.

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

1. Gremlin 셸에서 다음 명령을 실행하여 Gremlin 엔드포인트에 연결합니다.

   ```bash
   :remote connect tinkerpop.server conf/remote-localcompute.yaml
   :remote console
   :> g.V()
   :> g.addV('person1').property(id, '1').property('name', 'somename1')
   :> g.addV('person2').property(id, '2').property('name', 'somename2')
   :> g.V()
   ```

## <a name="uninstall-the-local-emulator"></a><a id="uninstall"></a>로컬 에뮬레이터 제거

다음 단계를 사용하여 에뮬레이터를 제거합니다.

1. 시스템 트레이에서 **Azure Cosmos DB 에뮬레이터** 아이콘을 마우스 오른쪽 단추로 클릭한 다음, **마침** 을 클릭하여 로컬 에뮬레이터의 열려 있는 모든 인스턴스를 종료합니다. 모든 인스턴스를 종료하는 데는 1분 정도 걸립니다.

1. Windows 검색 상자에 **앱 및 기능** 을 입력하고 **앱 및 기능(시스템 설정)** 결과를 선택합니다.

1. 앱 목록에서 **Azure Cosmos DB 에뮬레이터** 로 스크롤하여 선택하고, **제거** 를 클릭한 다음, 확인하고 **제거** 를 다시 선택합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 무료 로컬 개발을 위해 로컬 에뮬레이터를 사용하는 방법을 살펴보았습니다. 이제 다음 문서로 진행할 수 있습니다.

* [Java, Python 및 Node.js 앱에서 사용할 Azure Cosmos DB 에뮬레이터 인증서 내보내기](local-emulator-export-ssl-certificates.md)
* [명령줄 매개 변수 및 PowerShell 명령을 사용하여 에뮬레이터 제어](emulator-command-line-parameters.md)
* [에뮬레이터를 사용하여 이슈 디버그](troubleshoot-local-emulator.md)
