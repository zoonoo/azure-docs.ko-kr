---
title: Azure Cosmos DB 에뮬레이터에 대 한 명령줄 및 PowerShell 참조
description: Azure Cosmos DB 에뮬레이터에 대 한 명령줄 매개 변수와 PowerShell을 사용 하 여 에뮬레이터를 제어 하는 방법 및 에뮬레이터 내에서 만들 수 있는 컨테이너 수를 변경 하는 방법에 대해 알아봅니다.
ms.service: cosmos-db
ms.topic: how-to
author: markjbrown
ms.author: mjbrown
ms.date: 09/17/2020
ms.custom: contperfq1
ms.openlocfilehash: f8bcadf25ac8e001657f2be012f99ddb507e672d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91445173"
---
# <a name="command-line-and-powershell-reference-for-azure-cosmos-db-emulator"></a>Azure Cosmos DB 에뮬레이터에 대 한 명령줄 및 PowerShell 참조

Azure Cosmos emulator는 로컬 개발을 위해 Azure Cosmos DB 서비스를 에뮬레이트하는 로컬 환경을 제공 합니다. [에뮬레이터를 설치한](local-emulator.md)후 명령줄 및 PowerShell 명령을 사용 하 여 에뮬레이터를 제어할 수 있습니다. 이 문서에서는 명령줄 및 PowerShell 명령을 사용 하 여 에뮬레이터를 시작 및 중지 하 고, 옵션을 구성 하 고, 기타 작업을 수행 하는 방법을 설명 합니다. 설치 위치에서 명령을 실행 해야 합니다.

##  <a name="manage-the-emulator-with-command-line-syntax"></a><a id="command-line"></a>명령줄 구문을 사용 하 여 에뮬레이터 관리

```cmd
Microsoft.Azure.Cosmos.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]
```

옵션 목록을 보려면 명령 프롬프트에 `Microsoft.Azure.Cosmos.Emulator.exe /?` 을(를) 입력합니다.

|**옵션** | **설명** | **명령**| **인수**|
|---|---|---|---|
|[인수 없음] | 기본 설정을 사용 하 여 Azure Cosmos emulator를 시작 합니다. |Microsoft.Azure.Cosmos.Emulator.exe| |
|[도움말] |지원되는 명령줄 인수 목록을 표시합니다.|Microsoft.Azure.Cosmos.Emulator.exe /? | |
| GetStatus |Azure Cosmos Emulator의 상태를 구합니다. 상태는 종료 코드에 의해 표시됩니다. 1 = 시작, 2 = 실행, 3 = 정지. 부정적인 종료 코드는 오류가 발생 했음을 나타냅니다. 어떤 출력도 생성되지 않습니다. | Microsoft.Azure.Cosmos.Emulator.exe /GetStatus| |
| Shutdown| Azure Cosmos Emulator를 종료합니다.| Microsoft.Azure.Cosmos.Emulator.exe /Shutdown | |
|DataPath | 데이터 파일을 저장할 경로를 지정합니다. 기본값은 %LocalAppdata%\CosmosDBEmulator입니다. | Microsoft.Azure.Cosmos.Emulator.exe /DataPath=\<datapath\> | \<datapath\>: 액세스할 수 있는 경로 |
|포트 | 에뮬레이터에 사용할 포트 번호를 지정합니다. 기본값은 8081입니다. |Microsoft.Azure.Cosmos.Emulator.exe /Port=\<port\> | \<port\>: 단일 포트 번호 |
| ComputePort | Compute Interop 게이트웨이 서비스에 사용하도록 지정된 포트 번호입니다. 게이트웨이의 HTTP 엔드포인트 프로브 포트는 ComputePort + 79로 계산됩니다. 따라서 ComputePort 및 ComputePort + 79가 열리고 사용할 수 있어야 합니다. 기본값은 8900입니다. | Microsoft.Azure.Cosmos.Emulator.exe /ComputePort=\<computeport\> | \<computeport\>: 단일 포트 번호 |
| EnableMongoDbEndpoint=3.2 | MongoDB API 3.2 사용 | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.2 | |
| EnableMongoDbEndpoint=3.6 | MongoDB API 3.6 사용 | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.6 | |
| MongoPort | MongoDB 호환성 API에 사용할 포트 번호를 지정합니다. 기본값은 10255입니다. |Microsoft.Azure.Cosmos.Emulator.exe /MongoPort=\<mongoport\>|\<mongoport\>: 단일 포트 번호|
| EnableCassandraEndpoint | Cassandra API를 사용하도록 설정 | Microsoft.Azure.Cosmos.Emulator.exe /EnableCassandraEndpoint | |
| CassandraPort | Cassandra 엔드포인트에 사용할 포트 번호를 지정합니다. 기본값은 10350입니다. | Microsoft.Azure.Cosmos.Emulator.exe /CassandraPort=\<cassandraport\> | \<cassandraport\>: 단일 포트 번호 |
| EnableGremlinEndpoint | Gremlin API를 사용하도록 설정 | Microsoft.Azure.Cosmos.Emulator.exe /EnableGremlinEndpoint | |
| GremlinPort | Gremlin 엔드포인트에 사용할 포트 번호입니다. 기본값은 8901입니다. | Microsoft.Azure.Cosmos.Emulator.exe /GremlinPort=\<port\> | \<port\>: 단일 포트 번호 |
|EnableTableEndpoint | Azure Table API를 사용하도록 설정 | Microsoft.Azure.Cosmos.Emulator.exe /EnableTableEndpoint | |
|TablePort | Azure 테이블 엔드포인트에 사용할 포트 번호입니다. 기본값은 8902입니다. | Microsoft.Azure.Cosmos.Emulator.exe /TablePort=\<port\> | \<port\>: 단일 포트 번호|
| KeyFile | 지정된 파일에서 권한 부여 키를 읽어옵니다. keyfile을 생성하려면 /GenKeyFile 옵션을 사용합니다. | Microsoft.Azure.Cosmos.Emulator.exe /KeyFile=\<file_name\> | \<file_name\>: 파일의 경로 |
| ResetDataPath | 지정된 경로에 있는 모든 파일을 재귀적으로 제거합니다. 경로를 지정하지 않으면 기본값은 %LOCALAPPDATA%\CosmosDbEmulator입니다. | Microsoft.Azure.Cosmos.Emulator.exe /ResetDataPath=\<path> | \<path\>: 파일 경로  |
| StartTraces  |  LOGMAN을 사용하여 디버그 추적 로그 수집을 시작합니다. | Microsoft.Azure.Cosmos.Emulator.exe /StartTraces | |
| StopTraces     | LOGMAN을 사용하여 디버그 추적 로그 수집을 중지합니다. | Microsoft.Azure.Cosmos.Emulator.exe /StopTraces  | |
| StartWprTraces  |  Windows 성능 기록 도구를 사용하여 디버그 추적 로그 수집을 시작합니다. | Microsoft.Azure.Cosmos.Emulator.exe /StartWprTraces | |
| StopWprTraces     | Windows 성능 기록 도구를 사용하여 디버그 추적 로그 수집을 중지합니다. | Microsoft.Azure.Cosmos.Emulator.exe /StopWprTraces  | |
|FailOnSslCertificateNameMismatch | 기본적으로 에뮬레이터는 자체 서명 된 TLS/SSL 인증서를 다시 생성 합니다. 인증서의 SAN에는 에뮬레이터 호스트의 도메인 이름, 로컬 IPv4 주소, ' localhost ' 및 ' 127.0.0.1 '이 포함 되어 있지 않습니다. 이 옵션을 사용하면 시작할 때 에뮬레이터가 실패합니다. 그러면 /GenCert 옵션을 사용하여 자체 서명된 TLS/SSL 인증서를 새로 만들고 설치해야 합니다. | Microsoft.Azure.Cosmos.Emulator.exe /FailOnSslCertificateNameMismatch  | |
| GenCert | 자체 서명된 TLS/SSL 인증서를 새로 생성하고 설치합니다. 필요에 따라 네트워크를 통해 에뮬레이터에 액세스 하기 위한 쉼표로 구분 된 추가 DNS 이름 목록을 포함 합니다. | Microsoft.Azure.Cosmos.Emulator.exe /GenCert=\<dns-names\> |\<dns-names\>: 추가 dns 이름의 쉼표로 구분된 목록(선택 사항)  |
| DirectPorts |직접 연결에 사용할 포트를 지정합니다. 기본값은 10251,10252,10253,10254입니다. | Microsoft.Azure.Cosmos.Emulator.exe /DirectPorts:\<directports\> | \<directports\>: 쉼표로 구분된 4개 포트 목록 |
| 키 |에뮬레이터에 대한 권한 부여 키입니다. 키는 64바이트 벡터의 base 64 인코딩이어야 합니다. | Microsoft.Azure.Cosmos.Emulator.exe /Key:\<key\> | \<key\>: 키는 64바이트 벡터의 base 64 인코딩이어야 합니다.|
| EnableRateLimiting | 요청 속도 제한 동작을 사용하도록 지정합니다. |Microsoft.Azure.Cosmos.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |요청 속도 제한 동작을 사용하지 않도록 지정합니다. |Microsoft.Azure.Cosmos.Emulator.exe /DisableRateLimiting | |
| NoUI | 에뮬레이터 사용자 인터페이스를 표시하지 않습니다. | Microsoft.Azure.Cosmos.Emulator.exe /NoUI | |
| NoExplorer | 시작 시 데이터 탐색기를 표시하지 않습니다. |Microsoft.Azure.Cosmos.Emulator.exe /NoExplorer | | 
| PartitionCount | 분할된 컨테이너의 최대 수를 지정합니다. 자세한 내용은 [컨테이너 수 변경](#set-partitioncount)을 참조하세요. | Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=\<partitioncount\> | \<partitioncount\>: 허용되는 단일 파티션 컨테이너의 최대 수입니다. 기본값은 25입니다. 허용되는 최대값은 250입니다.|
| DefaultPartitionCount| 분할된 컨테이너에 대한 기본 파티션 수를 지정합니다. | Microsoft.Azure.Cosmos.Emulator.exe /DefaultPartitionCount=\<defaultpartitioncount\> | \<defaultpartitioncount\> 기본값은 25입니다.|
| AllowNetworkAccess | 네트워크를 통해 에뮬레이터에 액세스할 수 있도록 합니다. /Key=\<key_string\> 또는 /KeyFile=\<file_name\>을 전달하여 네트워크 액세스를 활성화해야 합니다. | Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=\<key_string\> 또는 Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /KeyFile=\<file_name\>| |
| NoFirewall | /AllowNetworkAccess 옵션이 사용되는 경우 방화벽 규칙을 조정하지 마십시오. |Microsoft.Azure.Cosmos.Emulator.exe /NoFirewall | |
| GenKeyFile | 새 인증 키를 생성하고 지정된 파일에 저장합니다. 생성된 키를 /Key 또는 /KeyFile 옵션과 함께 사용할 수 있습니다. | Microsoft.Azure.Cosmos.Emulator.exe /GenKeyFile=\<path to key file\> | |
| 일관성 | 계정의 기본 일관성 수준을 설정합니다. | Microsoft.Azure.Cosmos.Emulator.exe /Consistency=\<consistency\> | \<consistency\>: 값은 [일관성 수준](consistency-levels.md) Session, Strong, Eventual 또는 BoundedStaleness 중 하나여야 합니다. 기본값은 Session입니다. |
| ? | 도움말 메시지를 표시합니다.| | |

## <a name="manage-the-emulator-with-powershell"></a>PowerShell을 사용 하 여 에뮬레이터 관리

에뮬레이터는 서비스를 시작, 중지, 제거, 검색하고 상태를 검색할 수 있는 PowerShell 모듈과 함께 제공 됩니다. 다음 cmdlet을 실행하여 PowerShell 모듈을 사용합니다.

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

또는 `PSModulesPath`에 `PSModules` 디렉터리를 배치하고 다음 명령을 참고하여 가져옵니다.

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

다음은 PowerShell에서 에뮬레이터를 제어하는 명령을 요약한 것입니다.

### `Get-CosmosDbEmulatorStatus`

**구문**

`Get-CosmosDbEmulatorStatus`

**주의**

ServiceControllerStatus 값 ServiceControllerStatus.StartPending, ServiceControllerStatus.Running 또는 ServiceControllerStatus.Stopped 중에서 하나를 반환합니다.

### `Start-CosmosDbEmulator`

**구문**

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

**주의**

에뮬레이터를 시작합니다. 기본적으로 이 명령은 에뮬레이터가 요청을 수락할 준비가 될 때까지 대기합니다. 에뮬레이터가 시작하자 마자 cmdlet을 반환하고자 하는 경우 -NoWait 옵션을 사용하십시오.

### `Stop-CosmosDbEmulator`

**구문**

 `Stop-CosmosDbEmulator [-NoWait]`

**주의**

에뮬레이터를 중지합니다. 기본적으로 이 명령은 에뮬레이터가 완전히 종료할 때까지 대기합니다. 에뮬레이터가 종료를 시작하자 마자 cmdlet을 반환하고자 하는 경우 -NoWait 옵션을 사용하십시오.

### `Uninstall-CosmosDbEmulator`

**구문**

`Uninstall-CosmosDbEmulator [-RemoveData]`

**주의**

에뮬레이터를 제거하고 $env:LOCALAPPDATA\CosmosDbEmulator의 전체 콘텐츠를 선택적으로 제거합니다.
Cmdlet는 에뮬레이터가 제거되기 전에 중지되도록 합니다.

## <a name="change-the-number-of-default-containers"></a><a id="set-partitioncount"></a>기본 컨테이너의 수를 변경 합니다.

기본적으로 고정 크기 컨테이너를 25개까지 만들거나(Azure Cosmos DB SDK를 통해서만 지원됨) Azure Cosmos Emulator를 사용하여 무제한 컨테이너 5개를 만들 수 있습니다. **PartitionCount** 값을 수정하여 최대 250개의 고정 크기 컨테이너나 50개의 무제한 컨테이너를 만들거나 250개의 고정 크기 컨테이너를 초과하지 않는 범위에서 두 가지 조합을 만들 수 있습니다(무제한 컨테이너 1개 = 고정 크기 컨테이너 5개). 단, 200개를 초과하는 고정 크기 컨테이너로 실행되도록 에뮬레이터를 설정하는 것은 권장하지 않습니다. 디스크 IO 작업에 추가되는 오버헤드로 인해, 엔드포인트 API를 사용할 때 예기치 않은 시간 초과가 발생합니다.

현재 파티션 수가 초과된 후에 컨테이너를 만들려고 하면 에뮬레이터에서 다음 메시지와 함께 ServiceUnavailable 예외를 throw합니다.

> 죄송 합니다. 현재이 지역에서 많은 수요가 발생 하 고 있으며 지금은 요청을 수행할 수 없습니다. We work continuously to bring more and more capacity online, and encourage you to try again. (당사는 온라인 용량을 늘리기 위해 지속적으로 노력하고 있습니다. 다시 시도해 주십시오.)
> 활동 id: 12345678-1234-1234-1234-123456789abc

Azure Cosmos Emulator에서 사용 가능한 컨테이너 수를 변경하려면 다음 단계를 수행합니다.

1. 시스템 트레이에서 **Azure Cosmos DB 에뮬레이터** 아이콘을 마우스 오른쪽 단추로 클릭 한 다음 **데이터 다시 설정**...을 클릭 하 여 모든 로컬 Azure Cosmos 에뮬레이터 데이터를 삭제 합니다.

1. `%LOCALAPPDATA%\CosmosDBEmulator` 폴더의 에뮬레이터 데이터를 모두 삭제합니다.

1. **Azure Cosmos DB 에뮬레이터** 아이콘을 마우스 오른쪽 단추로 클릭한 다음 **마침**을 클릭하여 열려 있는 모든 인스턴스를 종료합니다. 모든 인스턴스를 종료하는 데는 1분 정도 걸립니다.

1. 최신 버전의 [Azure Cosmos Emulator](https://aka.ms/cosmosdb-emulator)를 설치합니다.

1. 250 이하의 값을 설정하여 PartitionCount 플래그로 에뮬레이터를 시작합니다. 예: `C:\Program Files\Azure Cosmos DB Emulator> Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=100`
 
## <a name="next-steps"></a>다음 단계

* [Java, Python 및 Node.js apps와 함께 사용 하기 위해 Azure Cosmos 에뮬레이터 인증서를 내보냅니다.](local-emulator-export-ssl-certificates.md)
* [에뮬레이터를 사용 하 여 문제 디버그](troubleshoot-local-emulator.md)
