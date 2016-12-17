---
title: "DocumentDB 에뮬레이터를 사용하여 로컬로 개발 | Microsoft Docs"
description: "DocumentDB 에뮬레이터를 사용하여 Azure 구독을 구입하지 않고도 무료로 로컬에서 응용 프로그램을 개발하고 테스트할 수 있습니다."
services: documentdb
documentationcenter: 
keywords: "DocumentDB 에뮬레이터"
author: arramac
manager: jhubbard
editor: 
ms.assetid: 90b379a6-426b-4915-9635-822f1a138656
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 363ef90321132b77a7ba09a826deca9e8a7da360
ms.openlocfilehash: 218a964318ceb2935e403a793990fb0bf9233542


---
# <a name="use-the-azure-documentdb-emulator-for-development-and-testing"></a>개발 및 테스트에 Azure 에뮬레이터 사용

[**에뮬레이터 다운로드**](https://aka.ms/documentdb-emulator)

Azure 에뮬레이터는 개발 목적으로 Azure DocumentDB 서비스를 에뮬레이트하는 로컬 환경을 제공합니다. DocumentDB 에뮬레이터를 사용하면 Azure 구독을 구입하거나 비용을 발생시키지 않고도 로컬에서 응용 프로그램을 테스트할 수 있습니다. DocumentDB 에뮬레이터에서 응용 프로그램이 작동하는 방식에 만족하는 경우 Azure DocumentDB 계정을 클라우드에서 사용하도록 전환할 수 있습니다.

Kirill Gavrylyuk가 DocumentDB 에뮬레이터를 시작하는 방법을 보여 주는 다음 비디오를 시청하는 것으로 시작하는 것이 좋습니다.

> [!VIDEO https://channel9.msdn.com/Events/Connect/2016/192/player]
> 
> 

## <a name="documentdb-emulator-system-requirements"></a>DocumentDB 에뮬레이터 시스템 요구 사항
DocumentDB 에뮬레이터에는 다음과 같은 하드웨어 및 소프트웨어 요구 사항이 있습니다.

* 소프트웨어 요구 사항
  * Windows Server 2012 R2, Windows Server 2016 또는 Windows 10
*   최소 하드웨어 요구 사항
  * 2GB RAM
  * 10GB의 하드 디스크 여유 공간

## <a name="installing-the-documentdb-emulator"></a>DocumentDB 에뮬레이터 설치
[Microsoft 다운로드 센터](https://aka.ms/documentdb-emulator)에서 DocumentDB 에뮬레이터를 다운로드하여 설치할 수 있습니다. DocumentDB 에뮬레이터를 설치, 구성 및 실행하려면 컴퓨터에 대한 관리 권한이 있어야 합니다.

> [!NOTE]
> DocumentDB 에뮬레이터를 설치, 구성 및 실행하려면 컴퓨터에 대한 관리 권한이 있어야 합니다.

## <a name="checking-for-documentdb-emulator-updates"></a>DocumentDB 에뮬레이터 업데이트 확인
DocumentDB 에뮬레이터에는 DocumentDB 내에 저장된 데이터를 찾아보고, 새 컬렉션을 만들고, 새 업데이트를 다운로드할 수 있을 때 이를 알려 주는 기본 제공 Azure DocumentDB 데이터 탐색기가 포함되어 있습니다. 

> [!NOTE]
> DocumentDB 에뮬레이터의 특정 버전에서 만든 데이터에 다른 버전에서 액세스하지 못할 수도 있습니다. 데이터를 장기간 보존하려면 DocumentDB 에뮬레이터 대신 Azure DocumentDB 계정에 저장하는 것이 좋습니다. 

## <a name="how-the-documentdb-emulator-works"></a>DocumentDB 에뮬레이터 작동 방식
DocumentDB 에뮬레이터는 DocumentDB 서비스의 충실도 높은 에뮬레이션을 제공합니다. JSON 문서 만들기 및 쿼리, 컬렉션 프로비전 및 확장, 저장 프로시저 및 트리거 실행을 비롯하여 Azure DocumentDB으로 동일한 기능을 지원합니다. DocumentDB 에뮬레이터를 사용하여 응용 프로그램을 개발 및 테스트하고 DocumentDB에 대한 연결 끝점에 대한 단일 구성을 변경하여 글로벌 규모로 Azure에 배포할 수 있습니다.

실제 DocumentDB 서비스의 충실도 높은 로컬 에뮬레이션을 만들었지만 DocumentDB 에뮬레이터의 구현운 서비스의 구현과 다릅니다. 예를 들어 DocumentDB 에뮬레이터는 로컬 파일 시스템(지속성) 및 HTTPS 프로토콜 스택(연결성)과 같은 표준 OS 구성 요소를 사용합니다. 따라서 전역 복제, 한 자리 밀리초 읽기/쓰기 대기 시간, 튜닝 가능한 일관성 수준 등 Azure 인프라를 기반으로 하는 일부 기능은 DocumentDB 에뮬레이터를 통해 사용할 수 없습니다.


## <a name="authenticating-requests-against-the-documentdb-emulator"></a>DocumentDB 에뮬레이터에 대한 요청 인증
클라우드의 Azure 문서와 마찬가지로 DocumentDB 에뮬레이터에 대한 모든 요청을 인증해야 합니다. DocumentDB 에뮬레이터는 단일 고정 계정과 마스터 키 인증에 대해 알려진 인증 키를 지원합니다. DocumentDB 에뮬레이터에서 사용할 수 있는 자격 증명은 이 계정과 키뿐입니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

    Account name: localhost:<port>
    Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==

> [!NOTE]
> DocumentDB 에뮬레이터에서 지원하는 마스터 키는 에뮬레이터 전용입니다. 프로덕션 DocumentDB 계정과 키를 DocumentDB 에뮬레이터에서 사용할 수는 없습니다. 

또한 Azure DocumentDB 서비스와 마찬가지로 DocumentDB 에뮬레이터는 SSL을 통한 보안 통신만 지원합니다.

## <a name="start-and-initialize-the-documentdb-emulator"></a>DocumentDB 에뮬레이터 시작 및 초기화
Azure DocumentDB 에뮬레이터를 시작하려면 시작 단추를 선택하거나 Windows 키를 누릅니다. **DocumentDB 에뮬레이터** 입력을 시작하고 응용 프로그램 목록에서 Azure 저장소 에뮬레이터를 선택합니다. 

![시작 단추를 선택하거나 Windows 키를 누르고 **DocumentDB 에뮬레이터** 입력을 시작한 후 응용 프로그램 목록에서 에뮬레이터를 선택합니다.](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-start.png)

에뮬레이터를 실행 하는 경우 Windows 작업 표시줄 알림 영역에 아이콘이 표시 됩니다.

![DocumentDB 로컬 에뮬레이터 작업 표시줄 알림](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-taskbar.png)

DocumentDB 에뮬레이터는 기본적으로 `C:\Program Files\Azure DocumentDB Emulator` 디렉터리에 설치됩니다. 명령줄에서 에뮬레이터를 시작 및 중지할 수도 있습니다. 자세한 내용은 [명령줄 도구 참조](#command-line)를 참조하세요.

## <a name="developing-with-the-documentdb-emulator"></a>DocumentDB 에뮬레이터를 사용한 개발
DocumentDB 에뮬레이터를 데스크톱에서 실행하는 경우 지원되는 [DocumentDB SDK](documentdb-sdk-dotnet.md) 또는 [DocumentDB REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx)를 사용하여 에뮬레이터와 상호 작용할 수 있습니다. DocumentDB 에뮬레이터에는 코드를 작성하지 않고도 컬렉션을 만들고 문서를 확인 및 편집할 수 있는 기본 제공 데이터 탐색기도 포함되어 있습니다. 

    // Connect to the DocumentDB Emulator running locally
    DocumentClient client = new DocumentClient(
        new Uri("https://localhost:8081"), 
        "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

[DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio)와 같은 기존 도구를 사용하여 DocumentDB 에뮬레이터에 연결할 수 있습니다. 또한 [DocumentDB 데이터 마이그레이션 도구](https://github.com/azure/azure-documentdb-datamigrationtool)를 사용하여 DocumentDB Emulator와 Azure DocumentDB 서비스 간에 데이터를 마이그레이션할 수 있습니다.

## <a name="a-idcommand-lineadocumentdb-emulator-command-line-tool-reference"></a><a id="command-line"></a>DocumentDB 에뮬레이터 명령줄 도구 참조
설치 위치에서 명령줄을 사용하여 에뮬레이터를 시작 및 중지하고, 옵션을 구성하고, 다른 작업을 수행할 수 있습니다.

### <a name="command-line-syntax"></a>명령줄 구문

    DocumentDB.LocalEmulator.exe [/shutdown] [/datapath] [/port] [/mongoport] [/directports] [/key] [/?]

옵션 목록을 보려면 명령 프롬프트에 `DocumentDB.LocalEmulator.exe /?` 을(를) 입력합니다.

<table>
<tr>
  <td><strong>옵션</strong></td>
  <td><strong>설명</strong></td>
  <td><strong>명령</strong></td>
  <td><strong>인수</strong></td>
</tr>
<tr>
  <td>[인수 없음]</td>
  <td>기본 설정으로 DocumentDB 에뮬레이터를 시작합니다.</td>
  <td>DocumentDB.LocalEmulator.exe</td>
  <td></td>
</tr>
<tr>
  <td>Shutdown</td>
  <td>DocumentDB 에뮬레이터를 종료합니다.</td>
  <td>DocumentDB.LocalEmulator.exe /Shutdown</td>
  <td></td>
</tr>
<tr>
  <td>도움말</td>
  <td>명령줄 인수 목록을 표시합니다.</td>
  <td>DocumentDB.LocalEmulator.exe /?</td>
  <td></td>
</tr>
<tr>
  <td>Datapath</td>
  <td>데이터 파일을 저장할 경로를 지정합니다.</td>
  <td>DocumentDB.LocalEmulator.exe /datapath=&lt;datapath&gt;</td>
  <td>&lt;datapath&gt;: 액세스 가능한 경로</td>
</tr>
<tr>
  <td>포트</td>
  <td>에뮬레이터에 사용할 포트 번호를 지정합니다.  기본값은 8081입니다.</td>
  <td>DocumentDB.LocalEmulator.exe /port=&lt;port&gt;</td>
  <td>&lt;port&gt;: 단일 포트 번호</td>
</tr>
<tr>
  <td>Mongoport</td>
  <td>MongoDB 호환성 API에 사용할 포트 번호를 지정합니다. 기본값은 10250입니다.</td>
  <td>DocumentDB.LocalEmulator.exe /mongoport=&lt;mongoport&gt;</td>
  <td>&lt;mongoport&gt;: 단일 포트 번호</td>
</tr>
<tr>
  <td>Directports</td>
  <td>직접 연결에 사용할 포트를 지정합니다.  기본값은 10251,10252,10253,10254입니다.</td>
  <td>DocumentDB.LocalEmulator.exe /directports:&lt;directports&gt;</td>
  <td>&lt;directports&gt;: 쉼표로 구분된 4개의 포트 목록</td>
</tr>
<tr>
  <td>키</td>
  <td>에뮬레이터에 대한 권한 부여 키입니다. 키는 64바이트 벡터의 base 64 인코딩이어야 합니다.</td>
  <td>DocumentDB.LocalEmulator.exe /key:&lt;key&gt;</td>
  <td>&lt;key&gt;: 키는 64바이트 벡터의 base 64 인코딩이어야 합니다.</td>
</tr>
<tr>
  <td>EnableThrottling</td>
  <td>요청 제한 동작을 사용하도록 지정합니다.</td>
  <td>DocumentDB.LocalEmulator.exe /enablethrottling</td>
  <td></td>
</tr>
<tr>
  <td>DisableThrottling</td>
  <td>요청 제한 동작을 사용하지 않도록 지정합니다.</td>
  <td>DocumentDB.LocalEmulator.exe /disablethrottling</td>
  <td></td>
</tr>
</table>

## <a name="differences-between-the-documentdb-emulator-and-azure-documentdb"></a>DocumentDB 에뮬레이터와 Azure DocumentDB 간의 차이점 
DocumentDB 에뮬레이터는 로컬 개발자 워크스테이션에서 실행되는 에뮬레이트된 환경을 제공하기 때문에 클라우드의 Azure DocumentDB 계정과 기능 면에서 몇 가지 차이가 있습니다.

* DocumentDB 에뮬레이터는 단일 고정 계정과 알려진 마스터 키만 지원합니다.  DocumentDB 에뮬레이터에서는 키를 다시 생성할 수 없습니다.
* DocumentDB 에뮬레이터는 확장 가능한 서비스가 아니며 많은 컬렉션을 지원하지 않습니다.
* DocumentDB 에뮬레이터는 여러 [DocumentDB 일관성 수준](documentdb-consistency-levels.md)을 시뮬레이션하지 않습니다.
* DocumentDB 에뮬레이터는 [다중 지역 복제](documentdb-distribute-data-globally.md)를 시뮬레이션하지 않습니다.
* DocumentDB 에뮬레이터는 Azure DocumentDB 서비스에서 사용할 수 있는 서비스 할당량 재정의(예: 문서 크기 제한, 향상된 분할된 컬렉션 저장소)를 지원하지 않습니다.
* DocumentDB 에뮬레이터는 Azure DocumentDB 서비스와 유사한 요청 요금을 반환하지만 Azure DocumentDB 서비스를 활용하는 응용 프로그램에 대한 프로비전된 처리량 요구 사항을 예측하는 데 사용할 수 없습니다. 프로덕션 처리량 수요를 정확하게 예상하려면 [DocumentDB 용량 플래너](https://www.documentdb.com/capacityplanner)를 사용합니다.
* DocumentDB 에뮬레이터는 데이터를 유지하지만 Azure DocumentDB 서비스를 활용하는 응용 프로그램에 대한 데이터 및 인덱스 저장소 요구 사항을 예측하는 데 사용할 수 없습니다. 프로덕션 저장소 수요를 정확하게 예상하려면 [DocumentDB 용량 플래너](https://www.documentdb.com/capacityplanner)를 사용합니다.


## <a name="next-steps"></a>다음 단계
* DocumentDB에 대해 자세히 알아보려면 [Azure DocumentDB 소개](documentdb-introduction.md)를 참조하세요.
* DocumentDB 에뮬레이터를 기반으로 개발을 시작하려면 [지원되는 DocumentDB SDK](documentdb-sdk-dotnet.md) 중 하나를 다운로드하세요.



<!--HONumber=Nov16_HO3-->


