---
title: Azure Cosmos 에뮬레이터를 사용 하는 경우 문제 해결
description: Azure Cosmos 에뮬레이터를 사용 하는 경우 서비스를 사용할 수 없음, 인증서, 암호화 및 버전 관리 문제를 해결 하는 방법에 대해 알아봅니다.
ms.service: cosmos-db
ms.topic: troubleshooting
author: markjbrown
ms.author: mjbrown
ms.date: 09/17/2020
ms.custom: contperfq1
ms.openlocfilehash: af9122aaa0233fe5248f31ffe805e01a98831eae
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91447420"
---
# <a name="troubleshoot-issues-when-using-the-azure-cosmos-emulator"></a>Azure Cosmos 에뮬레이터를 사용 하는 경우 문제 해결

Azure Cosmos emulator는 개발 목적으로 Azure Cosmos DB 서비스를 에뮬레이트하는 로컬 환경을 제공 합니다. 이 문서의 팁을 사용 하 여 Azure Cosmos 에뮬레이터를 설치 하거나 사용할 때 발생 하는 문제를 해결할 수 있습니다. 

새 버전의 에뮬레이터를 설치했는데 오류가 발생하는 경우 데이터를 다시 설정합니다. 시스템 트레이에서 Azure Cosmos 에뮬레이터 아이콘을 마우스 오른쪽 단추로 클릭 한 다음 데이터 다시 설정 ...을 클릭 하 여 데이터를 다시 설정할 수 있습니다. 그래도 오류가 해결 되지 않으면 에뮬레이터와 에뮬레이터의 이전 버전을 제거할 수 있습니다 .이 경우에는 *C:\Program files\Azure Cosmos DB emulator* 디렉터리를 제거 하 고 에뮬레이터를 다시 설치 합니다. 지침은 [로컬 에뮬레이터 제거](local-emulator.md#uninstall)를 참조하세요. 또는 데이터를 다시 설정 하는 것이 작동 하지 않으면 위치로 이동 하 여 `%LOCALAPPDATA%\CosmosDBEmulator` 폴더를 삭제 합니다.

## <a name="troubleshoot-corrupted-windows-performance-counters"></a>손상 된 windows 성능 카운터 문제 해결

* Azure Cosmos 에뮬레이터가 충돌 하는 경우 폴더에서 덤프 파일 `%LOCALAPPDATA%\CrashDumps` 을 수집 하 고 압축 한 다음 [Azure Portal](https://portal.azure.com)에서 지원 티켓을 엽니다.

* `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe`에서 크래시가 발생하는 경우에는 성능 카운터가 손상된 상태에서 발생하는 증상일 수 있습니다. 일반적으로 관리자 명령 프롬프트에서 다음 명령을 실행하면 문제가 해결됩니다.

  ```cmd
  lodctr /R
   ```

## <a name="troubleshoot-connectivity-issues"></a>연결 문제 해결

* 연결 문제가 발생하는 경우 [추적 파일을 수집](#trace-files)하고 압축하여 [Azure Portal](https://portal.azure.com)에서 지원 티켓을 엽니다.

* **서비스를 사용할 수 없음** 메시지가 나타나는 경우 에뮬레이터에서 네트워크 스택을 초기화하지 못했을 수 있습니다. 해당 네트워크 필터 드라이버로 인해 문제가 발생할 수 있으므로 Pulse 보안 클라이언트 또는 Juniper 네트워크 클라이언트를 설치했는지 확인합니다. 일반적으로 타사 네트워크 필터 드라이버를 제거하면 문제가 해결됩니다. 또는 /DisableRIO를 사용하여 에뮬레이터를 시작하면 에뮬레이터 네트워크 통신이 일반 Winsock으로 전환됩니다. 

* "사용할 수 **없음", "메시지": "요청을 전송 프로토콜 또는 암호화에서 사용할 수 없는 암호화로 만들고 있습니다. 계정 SSL/TLS 최소 허용 프로토콜 설정 ... "** 연결 문제를 확인 합니다 .이 문제는 OS의 글로벌 변경 (예: Insider Preview 빌드 20170) 또는 TLS 1.3를 기본값으로 사용 하는 브라우저 설정으로 인해 발생할 수 있습니다. Cosmos 에뮬레이터에 대 한 요청을 실행 하는 데 SDK를 사용 하는 경우와 같은 유사한 오류가 발생할 수 있습니다. 예를 들어 **Microsoft.Azure.Documents.DocumentClientException: 요청이 전송 프로토콜 또는 암호에서 금지 된 암호화를 사용 하 여 수행 됩니다. 계정 SSL/TLS 허용 된 최소 프로토콜 설정을 확인**하세요. Cosmos 에뮬레이터는 TLS 1.2 프로토콜만 수락하고 사용하므로 이번에는 이 작업을 수행해야 합니다. 권장 되는 해결 방법은 설정을 변경 하 고 기본값을 TLS 1.2로 변경 하는 것입니다. 예를 들어, IIS 관리자에서 "사이트" > "기본 웹 사이트"로 이동 하 고 포트 8081에 대 한 "사이트 바인딩"을 찾은 후이를 편집 하 여 TLS 1.3을 사용 하지 않도록 설정 합니다. "설정" 옵션을 통해 웹 브라우저에 대해 유사한 작업을 수행할 수 있습니다.

* 에뮬레이터 실행 중에 컴퓨터가 절전 모드가 되거나 OS 업데이트를 실행할 경우 **서비스를 현재 사용할 수 없음** 메시지가 표시될 수 있습니다. Windows 알림 트레이에 표시되는 아이콘을 마우스 오른쪽 단추로 클릭하여 **데이터 다시 설정**을 선택하여 에뮬레이터 데이터를 다시 설정합니다.

## <a name="collect-trace-files"></a><a id="trace-files"></a>추적 파일 수집

디버깅 추적을 수집하려면 관리 명령 프롬프트에서 다음 명령을 실행합니다.

1. 에뮬레이터가 설치 된 경로로 이동 합니다.

   ```bash
   cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"
   ```

1. 에뮬레이터를 종료 하 고 시스템 트레이를 시청 하 여 프로그램이 종료 되었는지 확인 합니다. 완료 하는 데 1 분 정도 걸릴 수 있습니다. Azure Cosmos emulator 사용자 인터페이스에서 **끝내기** 를 선택할 수도 있습니다.

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /shutdown
   ```

1. 다음 명령을 사용 하 여 로깅을 시작 합니다.

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /startwprtraces
   ```

1. 에뮬레이터를 시작 합니다.

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe
   ```

1. 문제를 재현합니다. 데이터 탐색기가 작동 하지 않는 경우 몇 초 동안 브라우저를 열어 오류를 catch 할 때까지 기다려야 합니다.

1. 다음 명령을 사용 하 여 로깅을 중지 합니다.

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /stopwprtraces
   ```
   
1. 경로로 이동 하 여 `%ProgramFiles%\Azure Cosmos DB Emulator` *docdbemulator_000001 .etl* 파일을 찾습니다.

1. [Azure Portal](https://portal.azure.com)에서 지원 티켓을 열고 재현 단계와 함께 .etl 파일을 포함합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 로컬 에뮬레이터를 사용 하 여 문제를 디버그 하는 방법을 배웠습니다. 이제 다음 문서를 진행할 수 있습니다.

* [Java, Python 및 Node.js apps와 함께 사용 하기 위해 Azure Cosmos 에뮬레이터 인증서를 내보냅니다.](local-emulator-export-ssl-certificates.md)
* [명령줄 매개 변수 및 PowerShell 명령을 사용 하 여 에뮬레이터 제어](emulator-command-line-parameters.md)
