---
title: "Azure Cosmos DB 에뮬레이터를 사용하여 로컬로 개발 | Microsoft Docs"
description: "Azure Cosmos DB 에뮬레이터를 사용하여 Azure 구독을 구입하지 않고도 무료로 로컬에서 응용 프로그램을 개발하고 테스트할 수 있습니다."
services: cosmosdb
documentationcenter: 
keywords: "Azure Cosmos DB 에뮬레이터"
author: arramac
manager: jhubbard
editor: 
ms.assetid: 90b379a6-426b-4915-9635-822f1a138656
ms.service: cosmosdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 517819bfa17cb90a6f940200620e90ad352929d2
ms.contentlocale: ko-kr
ms.lasthandoff: 05/11/2017


---
# <a name="use-the-azure-cosmos-db-emulator-for-local-development-and-testing"></a>로컬 개발 및 테스트에 Azure Cosmos DB 에뮬레이터 사용

<table>
<tr>
  <td><strong>이진 파일</strong></td>
  <td>[MSI 다운로드](https://aka.ms/documentdb-emulator)</td>
</tr>
<tr>
  <td><strong>Docker</strong></td>
  <td>[Docker 허브](https://hub.docker.com/r/microsoft/azure-documentdb-emulator/)</td>
</tr>
<tr>
  <td><strong>Docker 원본</strong></td>
  <td>[Github](https://github.com/azure/azure-documentdb-emulator-docker)</td>
</tr>
</table>
  
Azure Cosmos DB 에뮬레이터는 개발 목적으로 Azure Cosmos DB 서비스를 에뮬레이트하는 로컬 환경을 제공합니다. Azure Cosmos DB 에뮬레이터를 사용하면 Azure 구독을 구입하거나 비용을 발생시키지 않고도 로컬에서 응용 프로그램을 테스트할 수 있습니다. Azure Cosmos DB 에뮬레이터에서 응용 프로그램이 작동하는 방식에 만족하는 경우 Azure Cosmos DB 계정을 클라우드에서 사용하도록 전환할 수 있습니다.

이 문서에서 다루는 작업은 다음과 같습니다. 

> [!div class="checklist"]
> * 에뮬레이터 설치
> * Windows용 Docker에서 에뮬레이터 실행
> * 요청 인증
> * 에뮬레이터에서 데이터 탐색기 사용
> * SSL 인증서 내보내기
> * 명령줄에서 에뮬레이터 호출
> * 추적 파일 수집

Kirill Gavrylyuk가 Azure Cosmos DB 에뮬레이터를 시작하는 방법을 보여 주는 다음 비디오를 시청하는 것으로 시작하는 것이 좋습니다.

> [!VIDEO https://channel9.msdn.com/Events/Connect/2016/192/player]
> 
> 

## <a name="system-requirements"></a>시스템 요구 사항
Azure Cosmos DB 에뮬레이터에는 다음과 같은 하드웨어 및 소프트웨어 요구 사항이 있습니다.

* 소프트웨어 요구 사항
  * Windows Server 2012 R2, Windows Server 2016 또는 Windows 10
*    최소 하드웨어 요구 사항
  *    2GB RAM
  *    10GB의 하드 디스크 여유 공간

## <a name="installation"></a>설치
[Microsoft 다운로드 센터](https://aka.ms/documentdb-emulator)에서 Azure Cosmos DB 에뮬레이터를 다운로드하여 설치할 수 있습니다. 

> [!NOTE]
> Azure Cosmos DB 에뮬레이터를 설치, 구성 및 실행하려면 컴퓨터에 대한 관리 권한이 있어야 합니다.

## <a name="running-on-docker-for-windows"></a>Windows용 Docker에서 실행

Azure Cosmos DB 에뮬레이터는 Windows용 Docker에서 실행할 수 있습니다. 이 에뮬레이터는 Oracle Linux용 Docker에서 작동하지 않습니다.

[Windows용 Docker](https://www.docker.com/docker-windows)를 설치한 경우 즐겨 찾는 셸(PowerShell, cmd.exe 등)에서 다음 명령을 실행하여 Docker 허브에서 에뮬레이터 이미지를 끌어올 수 있습니다.

```      
docker pull microsoft/azure-documentdb-emulator 
```
이미지를 시작하려면 다음 명령을 실행합니다.

``` 
md %LOCALAPPDATA%\DocumentDBEmulatorCert 2>nul
docker run -v %LOCALAPPDATA%\DocumentDBEmulatorCert:c:\DocumentDBEmulator\DocumentDBEmulatorCert -P -t -i microsoft/azure-documentdb-emulator 
```

응답은 다음과 유사합니다.

```
Starting Emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\DocumentDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
``` 

에뮬레이터가 시작된 후 대화형 셸을 닫으면 에뮬레이터의 컨테이너가 종료됩니다.

클라이언트의 응답에서 끝점 및 마스터 키를 사용하고 SSL 인증서를 호스트로 가져옵니다. SSL 인증서를 가져오려면 관리자 명령 프롬프트에서 다음을 수행합니다.

```
cd %LOCALAPPDATA%\DocumentDBEmulatorCert
powershell .\importcert.ps1
```

## <a name="checking-for-updates"></a>업데이트 확인
Azure Cosmos DB 에뮬레이터에는 Azure Cosmos DB 내에 저장된 데이터를 찾아보고, 새 컬렉션을 만들고, 새 업데이트를 다운로드할 수 있을 때 이를 알려 주는 기본 제공 Azure Cosmos DB 데이터 탐색기가 포함되어 있습니다. 

> [!NOTE]
> Azure Cosmos DB 에뮬레이터의 특정 버전에서 만든 데이터에 다른 버전에서 액세스하지 못할 수도 있습니다. 데이터를 장기간 보존하려면 Azure Cosmos DB 에뮬레이터 대신 Azure Cosmos DB 계정에 저장하는 것이 좋습니다. 

## <a name="how-the-emulator-works"></a>에뮬레이터의 작동 원리
Azure Cosmos DB 에뮬레이터는 신뢰도 있는 Azure Cosmos DB 서비스의 에뮬레이션을 제공합니다. JSON 문서 만들기 및 쿼리, 컬렉션 프로비전 및 확장, 저장 프로시저 및 트리거 실행을 비롯하여 Azure Cosmos DB으로 동일한 기능을 지원합니다. Azure Cosmos DB 에뮬레이터를 사용하여 응용 프로그램을 개발 및 테스트하고 Azure Cosmos DB에 대한 연결 끝점에 대한 단일 구성을 변경하여 글로벌 규모로 Azure에 배포할 수 있습니다.

실제 Azure Cosmos DB 서비스의 충실도 높은 로컬 에뮬레이션을 만들었지만 Azure Cosmos DB 에뮬레이터의 구현은 서비스의 구현과 다릅니다. 예를 들어 Azure Cosmos DB 에뮬레이터는 로컬 파일 시스템(지속성) 및 HTTPS 프로토콜 스택(연결성)과 같은 표준 OS 구성 요소를 사용합니다. 따라서 전역 복제, 한 자리 밀리초 읽기/쓰기 대기 시간, 튜닝 가능한 일관성 수준 등 Azure 인프라를 기반으로 하는 일부 기능은 Azure Cosmos DB 에뮬레이터를 통해 사용할 수 없습니다.


## <a name="authenticating-requests"></a>요청 인증
클라우드의 Azure 문서와 마찬가지로 Azure Cosmos DB 에뮬레이터에 대한 모든 요청을 인증해야 합니다. Azure Cosmos DB 에뮬레이터는 단일 고정 계정과 마스터 키 인증에 대해 알려진 인증 키를 지원합니다. Azure Cosmos DB 에뮬레이터에서 사용할 수 있는 자격 증명은 이 계정과 키뿐입니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

    Account name: localhost:<port>
    Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==

> [!NOTE]
> Azure Cosmos DB 에뮬레이터에서 지원하는 마스터 키는 에뮬레이터 전용입니다. Azure Cosmos DB 에뮬레이터에서 프로덕션 Azure Cosmos DB 계정 및 키를 사용할 수 없습니다. 

또한 Azure Cosmos DB 서비스와 마찬가지로 Azure Cosmos DB 에뮬레이터는 SSL을 통한 보안 통신만 지원합니다.

## <a name="start-and-initialize-the-emulator"></a>에뮬레이터 시작 및 초기화

Azure Cosmos DB 에뮬레이터를 시작하려면 시작 단추를 선택하거나 Windows 키를 누릅니다. **Azure Cosmos DB 에뮬레이터** 입력을 시작하고 응용 프로그램 목록에서 해당 에뮬레이터를 선택합니다. 

![시작 단추를 선택하거나 Windows 키를 누르고 **Azure Cosmos DB 에뮬레이터** 입력을 시작한 후 응용 프로그램 목록에서 해당 에뮬레이터를 선택합니다.](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-start.png)

에뮬레이터를 실행 하는 경우 Windows 작업 표시줄 알림 영역에 아이콘이 표시 됩니다. 기본적으로 Azure Cosmos DB 에뮬레이터는 포트 8081에서 수신 대기하는 로컬 컴퓨터("localhost")에서 실행됩니다.

![Azure Cosmos DB 로컬 에뮬레이터 작업 표시줄 알림](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-taskbar.png)

Azure Cosmos DB 에뮬레이터는 기본적으로 `C:\Program Files\Azure Cosmos DB Emulator` 디렉터리에 설치됩니다. 명령줄에서 에뮬레이터를 시작 및 중지할 수도 있습니다. 자세한 내용은 [명령줄 도구 참조](#command-line)를 참조하세요.

## <a name="start-data-explorer"></a>데이터 탐색기 시작

Azure Cosmos DB 에뮬레이터를 시작하면 브라우저에서 Azure Cosmos DB 데이터 탐색기가 자동으로 열립니다. 주소는 [https://localhost:8081/_explorer/index.html](https://localhost:8081/_explorer/index.html)로 표시됩니다. 탐색기를 닫고 나중에 다시 열고 싶은 경우, 브라우저에서 URL을 열거나 아래와 같이 Windows 트레이 아이콘의 Azure Cosmos DB 에뮬레이터에서 실행할 수 있습니다.

![Azure Cosmos DB의 로컬 에뮬레이터 데이터 탐색기 시작 관리자](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-data-explorer-launcher.png)

## <a name="developing-with-the-emulator"></a>에뮬레이터를 사용한 개발
Azure Cosmos DB 에뮬레이터를 데스크톱에서 실행하는 경우 지원되는 [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) 또는 [Azure Cosmos DB REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx)를 사용하여 에뮬레이터와 상호 작용할 수 있습니다. Azure Cosmos DB 에뮬레이터에는 코드를 작성하지 않고도 컬렉션을 만들고 문서를 확인 및 편집할 수 있는 기본 제공 데이터 탐색기도 포함되어 있습니다. 

    // Connect to the Azure Cosmos DB Emulator running locally
    DocumentClient client = new DocumentClient(
        new Uri("https://localhost:8081"), 
        "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

[MongoDB에 대한 Azure Cosmos DB 프로토콜 지원](documentdb-protocol-mongodb.md)을 사용할 경우에는 다음 연결 문자열을 사용하세요.

    mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10250/admin?ssl=true&3t.sslSelfSignedCerts=true

[Azure Cosmos DB Studio](https://github.com/mingaliu/Azure Cosmos DBStudio)와 같은 기존 도구를 사용하여 Azure Cosmos DB 에뮬레이터에 연결할 수 있습니다. 또한 [Azure Cosmos DB 데이터 마이그레이션 도구](https://github.com/azure/azure-documentdb-datamigrationtool)를 사용하여 Azure Cosmos DB 에뮬레이터와 Azure Cosmos DB 서비스 간에 데이터를 마이그레이션할 수 있습니다.

기본적으로 Azure Cosmos DB 에뮬레이터를 사용하여 최대 25개의 단일 파티션의 컬렉션 또는 분할된 컬렉션 하나를 만들 수 있습니다. 이 값을 변경하는 방법에 대한 자세한 내용은 [PartitionCount 값 설정](#set-partitioncount)을 참조하세요.

## <a name="export-the-ssl-certificate"></a>SSL 인증서 내보내기

.NET 언어 및 런타임은 Windows 인증서 저장소를 사용하여 Azure Cosmos DB 로컬 에뮬레이터에 안전하게 연결합니다. 다른 언어의 경우 해당 언어만의 인증서 관리 및 사용 방법이 있습니다. Java는 자체의 고유 [인증서 저장소](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html)를 사용하고 Python은 [소켓 래퍼](https://docs.python.org/2/library/ssl.html)를 사용합니다.

Windows 인증서 저장소와 통합되지 않는 런타임 및 언어와 함께 사용할 인증서를 가져오기 위해 Windows 인증서 관리자를 사용하여 인증서를 내보내야 합니다. certlm.msc를 실행하거나 [Azure Cosmos DB 에뮬레이터 인증서 내보내기](./documentdb-nosql-local-emulator-export-ssl-certificates.md)의 단계별 지침을 수행하여 이 작업을 시작할 수 있습니다. 인증서 관리자가 실행되면 아래에 표시된 대로 개인 인증서를 열고 BASE-64 인코딩 X.509(.cer) 파일로 "Azure Cosmos DBEmulatorCertificate"라는 이름의 인증서를 내보냅니다.

![Azure Cosmos DB 로컬 에뮬레이터 SSL 인증서](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-ssl_certificate.png)

[Java CA 인증서 저장소에 인증서 추가](https://docs.microsoft.com/azure/java-add-certificate-ca-store)의 지침을 따라 X.509 인증서를 Java 인증서 저장소로 가져올 수 있습니다. 인증서를 cacerts 저장소에 가져온 후 Java 및 MongoDB 응용 프로그램을 Azure Cosmos DB 에뮬레이터에 연결할 수 있습니다.

Python 및 Node.js SDK에서 에뮬레이터에 연결하면 SSL 확인이 비활성화됩니다.

## <a id="command-line"></a>명령줄 도구 참조
설치 위치에서 명령줄을 사용하여 에뮬레이터를 시작 및 중지하고, 옵션을 구성하고, 다른 작업을 수행할 수 있습니다.

### <a name="command-line-syntax"></a>명령줄 구문

    DocumentDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/?]

옵션 목록을 보려면 명령 프롬프트에 `DocumentDB.Emulator.exe /?` 을(를) 입력합니다.

<table>
<tr>
  <td><strong>옵션</strong></td>
  <td><strong>설명</strong></td>
  <td><strong>명령</strong></td>
  <td><strong>인수</strong></td>
</tr>
<tr>
  <td>[인수 없음]</td>
  <td>기본 설정으로 Azure Cosmos DB 에뮬레이터를 시작합니다.</td>
  <td>DocumentDB.Emulator.exe</td>
  <td></td>
</tr>
<tr>
  <td>[도움말]</td>
  <td>지원되는 명령줄 인수 목록을 표시합니다.</td>
  <td>DocumentDB.Emulator.exe /?</td>
  <td></td>
</tr>
<tr>
  <td>Shutdown</td>
  <td>Azure Cosmos DB 에뮬레이터를 종료합니다.</td>
  <td>DocumentDB.Emulator.exe /Shutdown</td>
  <td></td>
</tr>
<tr>
  <td>DataPath</td>
  <td>데이터 파일을 저장할 경로를 지정합니다. 기본값은 %LocalAppdata%\DocumentDBEmulator입니다.</td>
  <td>DocumentDB.Emulator.exe /DataPath=&lt;datapath&gt;</td>
  <td>&lt;datapath&gt;: 액세스 가능한 경로</td>
</tr>
<tr>
  <td>포트</td>
  <td>에뮬레이터에 사용할 포트 번호를 지정합니다.  기본값은 8081입니다.</td>
  <td>DocumentDB.Emulator.exe /Port=&lt;port&gt;</td>
  <td>&lt;port&gt;: 단일 포트 번호</td>
</tr>
<tr>
  <td>MongoPort</td>
  <td>MongoDB 호환성 API에 사용할 포트 번호를 지정합니다. 기본값은 10250입니다.</td>
  <td>DocumentDB.Emulator.exe /MongoPort=&lt;mongoport&gt;</td>
  <td>&lt;mongoport&gt;: 단일 포트 번호</td>
</tr>
<tr>
  <td>DirectPorts</td>
  <td>직접 연결에 사용할 포트를 지정합니다. 기본값은 10251,10252,10253,10254입니다.</td>
  <td>DocumentDB.Emulator.exe /DirectPorts:&lt;directports&gt;</td>
  <td>&lt;directports&gt;: 쉼표로 구분된 4개의 포트 목록</td>
</tr>
<tr>
  <td>키</td>
  <td>에뮬레이터에 대한 권한 부여 키입니다. 키는 64바이트 벡터의 base 64 인코딩이어야 합니다.</td>
  <td>DocumentDB.Emulator.exe /Key:&lt;key&gt;</td>
  <td>&lt;key&gt;: 키는 64바이트 벡터의 base 64 인코딩이어야 합니다.</td>
</tr>
<tr>
  <td>EnableRateLimiting</td>
  <td>요청 속도 제한 동작을 사용하도록 지정합니다.</td>
  <td>DocumentDB.Emulator.exe /EnableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>DisableRateLimiting</td>
  <td>요청 속도 제한 동작을 사용하지 않도록 지정합니다.</td>
  <td>DocumentDB.Emulator.exe /DisableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>NoUI</td>
  <td>에뮬레이터 사용자 인터페이스를 표시하지 않습니다.</td>
  <td>DocumentDB.Emulator.exe /NoUI</td>
  <td></td>
</tr>
<tr>
  <td>NoExplorer</td>
  <td>시작 시 문서 탐색기를 표시하지 않습니다.</td>
  <td>DocumentDB.Emulator.exe /NoExplorer</td>
  <td></td>
</tr>
<tr>
  <td>PartitionCount</td>
  <td>분할된 컬렉션의 최대 수를 지정합니다. 자세한 내용은 [컬렉션 수 변경](#set-partitioncount)을 참조하세요.</td>
  <td>DocumentDB.Emulator.exe /PartitionCount=&lt;partitioncount&gt;</td>
  <td>&lt;partitioncount&gt;: 허용되는 단일 파티션 컬렉션의 최대 수입니다. 기본값은 25입니다. 허용되는 최대값은 250입니다.</td>
</tr>
</table>

## <a name="differences-between-the-azure-cosmos-db-emulator-and-azure-cosmos-db"></a>Azure Cosmos DB 에뮬레이터와 Azure Cosmos DB 간 차이점 
Azure Cosmos DB 에뮬레이터는 로컬 개발자 워크스테이션에서 실행되는 에뮬레이트된 환경을 제공하기 때문에 클라우드의 Azure Cosmos DB 계정과 기능 면에서 몇 가지 차이가 있습니다.

* Azure Cosmos DB 에뮬레이터는 단일 고정 계정과 알려진 마스터 키만 지원합니다.  Azure Cosmos DB 에뮬레이터에서는 키를 다시 생성할 수 없습니다.
* Azure Cosmos DB 에뮬레이터는 확장 가능한 서비스가 아니며 많은 컬렉션을 지원하지 않습니다.
* Azure Cosmos DB 에뮬레이터는 여러 [Azure Cosmos DB 일관성 수준](documentdb-consistency-levels.md)을 시뮬레이션하지 않습니다.
* Azure Cosmos DB 에뮬레이터는 [다중 지역 복제](documentdb-distribute-data-globally.md)를 시뮬레이션하지 않습니다.
* Azure Cosmos DB 에뮬레이터는 Azure Cosmos DB 서비스에서 사용할 수 있는 서비스 할당량 재정의(예: 문서 크기 제한, 향상된 분할된 컬렉션 저장소)를 지원하지 않습니다.
* Azure Cosmos DB 에뮬레이터의 복사본은 최신 Azure Cosmos DB 서비스가 포함된 가장 최근의 변경 사항이 적용된 최신 에뮬레이터가 아닐 수 있으므로 [Azure Cosmos DB Capacity Planner](https://www.documentdb.com/capacityplanner)를 실행하여 응용 프로그램에 요구되는 프로덕션 처리량(RU)을 정확하게 예측해야 합니다.

## <a id="set-partitioncount"></a>컬렉션 수 변경

기본적으로 Azure Cosmos DB 에뮬레이터를 사용하여 최대 25개의 단일 파티션의 컬렉션 또는 분할된 컬렉션 하나를 만들 수 있습니다. **PartitionCount** 값을 수정하여는 최대 250개의 단일 파티션 컬렉션 또는 10개의 분할된 컬렉션을 만들거나, 합쳐서 250개의 단일 파티션을 초과하지 않는 두 컬렉션 조합을 만들 수 있습니다(분할된 컬렉션 1개 = 단일 파티션 컬렉션 25개).

현재 파티션 수가 초과된 후에 컬렉션을 만들려고 하면 에뮬레이터에서 다음 메시지와 함께 ServiceUnavailable 예외를 throw합니다.

    Sorry, we are currently experiencing high demand in this region, 
    and cannot fulfill your request at this time. We work continuously 
    to bring more and more capacity online, and encourage you to try again. 
    Please do not hesitate to email docdbswat@microsoft.com at any time or 
    for any reason. ActivityId: 29da65cc-fba1-45f9-b82c-bf01d78a1f91

Azure Cosmos DB 에뮬레이터에서 사용할 수 있는 컬렉션의 수를 변경하려면 다음을 수행합니다.

1. 시스템 트레이에서 **Azure Cosmos DB 에뮬레이터** 아이콘을 마우스 오른쪽 단추로 클릭한 다음 **데이터 다시 설정...**을 클릭하여 모든 로컬 Azure Cosmos DB 에뮬레이터 데이터를 삭제합니다.
2. C:\Users\user_name\AppData\Local\DocumentDBEmulator 폴더에 있는 모든 에뮬레이터 데이터를 삭제합니다.
3. **Azure Cosmos DB 에뮬레이터** 아이콘을 마우스 오른쪽 단추로 클릭한 다음 **마침**을 클릭하여 열려 있는 모든 인스턴스를 종료합니다. 모든 인스턴스를 종료하는 데는 1분 정도 걸립니다.
4. 최신 버전의 [Azure Cosmos DB 에뮬레이터](https://aka.ms/documentdb-emulator)를 설치합니다.
5. 250 이하의 값을 설정하여 PartitionCount 플래그로 에뮬레이터를 시작합니다. 예: `C:\Program Files\DocumentDB Emulator>DocumentDB.Emulator.exe /PartitionCount=100`

## <a name="troubleshooting"></a>문제 해결

다음은 Azure Cosmos DB 에뮬레이터에서 발생하는 문제 해결을 도와주는 팁입니다.

- Azure Cosmos DB 에뮬레이터가 충돌하는 경우 c:\Users\user_name\AppData\Local\CrashDumps 폴더에서 덤프 파일을 수집하고, 압축하고, 전자 메일에 첨부하여 [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com)으로 보냅니다.

- DocumentDB.StartupEntryPoint.exe에서 충돌을 경험하는 경우 관리자 명령 프롬프트에서 다음 명령을 실행합니다. `lodctr /R` 

- 연결 문제가 발생하는 경우 [추적 파일을 수집](#trace-files)하고, 압축하고, 전자 메일에 첨부하여 [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com)으로 보냅니다.

### <a id="trace-files"></a>추적 파일 수집

디버깅 추적을 수집하려면 관리 명령 프롬프트에서 다음 명령을 실행합니다.

1. `cd /d "%ProgramFiles%\DocumentDB Emulator"`
2. `DocumentDB.Emulator.exe /shutdown` 프로그램이 종료되었는지 시스템 트레이를 확인합니다. 프로그램 종료에 1분 정도 걸릴 수 있습니다. DocumentDB 에뮬레이터 사용자 인터페이스에서 **종료**를 클릭해도 됩니다.
3. `DocumentDB.Emulator.exe /starttraces`
4. `DocumentDB.Emulator.exe`
5. 문제를 재현합니다. 데이터 탐색기가 작동하지 않는 경우 브라우저가 몇 초 간 열리고 오류를 catch할 때까지 기다리면 됩니다.
5. `DocumentDB.Emulator.exe /stoptraces`
6. `%ProgramFiles%\DocumentDB Emulator`로 이동하여 docdbemulator_000001.etl 파일을 찾습니다.
7. 재현 단계와 마찬가지로 디버깅을 위해 .etl 파일을 [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com)으로 보냅니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * 로컬 에뮬레이터 설치
> * Windows용 Docker에서 에뮬레이터 실행
> * 요청 인증
> * 에뮬레이터에서 데이터 탐색기 사용
> * SSL 인증서 내보내기
> * 명령줄에서 에뮬레이터 호출
> * 추적 파일 수집

이 자습서에서는 무료 로컬 개발을 위해 로컬 에뮬레이터를 사용하는 방법을 살펴보았습니다. 이제 다음 자습서로 진행하여 에뮬레이터 SSL 인증서를 내보내는 방법을 알아볼 수 있습니다. 

> [!div class="nextstepaction"]
> [Azure Cosmos DB 에뮬레이터 인증서 내보내기](documentdb-nosql-local-emulator-export-ssl-certificates.md)

