---
title: Azure Cosmos DB 에뮬레이터를 사용하는 경우 문제 해결
description: Azure Cosmos DB 에뮬레이터를 사용하는 경우 서비스를 사용할 수 없음, 인증서, 암호화 및 버전 관리 문제를 해결하는 방법에 대해 알아봅니다.
ms.service: cosmos-db
ms.topic: troubleshooting
ms.author: esarroyo
author: StefArroyo
ms.date: 09/17/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: e255388432c51562b254bf86c52090a50cb8e4a5
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113358906"
---
# <a name="troubleshoot-issues-when-using-the-azure-cosmos-db-emulator"></a>Azure Cosmos DB 에뮬레이터를 사용하는 경우 문제 해결
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB 에뮬레이터는 개발 목적으로 Azure Cosmos DB 서비스를 에뮬레이트하는 로컬 환경을 제공합니다. 이 문서의 팁을 사용하여 Azure Cosmos DB 에뮬레이터를 설치하거나 사용할 때 발생하는 문제를 해결할 수 있습니다. 

새 버전의 에뮬레이터를 설치했는데 오류가 발생하는 경우 데이터를 다시 설정합니다. 시스템 트레이에서 Azure Cosmos DB 에뮬레이터 아이콘을 마우스 오른쪽 단추로 클릭한 다음, 데이터 다시 설정...을 클릭하여 데이터를 다시 설정할 수 있습니다. 이렇게 해도 오류가 해결되지 않으면 에뮬레이터와 에뮬레이터의 이전 버전(있는 경우)을 제거하고 *C:\Program files\Azure Cosmos DB Emulator* 디렉터리를 제거한 다음, 에뮬레이터를 다시 설치합니다. 지침은 [로컬 에뮬레이터 제거](local-emulator.md#uninstall)를 참조하세요. 또는 데이터를 다시 설정할 수 없다면 `%LOCALAPPDATA%\CosmosDBEmulator` 위치로 이동하여 폴더를 삭제합니다.

## <a name="troubleshoot-corrupted-windows-performance-counters"></a>손상된 Windows 성능 카운터 문제 해결

* Azure Cosmos DB 에뮬레이터가 충돌하는 경우 `%LOCALAPPDATA%\CrashDumps` 폴더에서 덤프 파일을 수집하고 압축한 다음 [Azure Portal](https://portal.azure.com)에서 지원 티켓을 엽니다.

* `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe`에서 크래시가 발생하는 경우에는 성능 카운터가 손상된 상태에서 발생하는 증상일 수 있습니다. 일반적으로 관리자 명령 프롬프트에서 다음 명령을 실행하면 문제가 해결됩니다.

  ```cmd
  lodctr /R
   ```

## <a name="troubleshoot-connectivity-issues"></a>연결 문제 해결

* 연결 문제가 발생하는 경우 [추적 파일을 수집](#trace-files)하고 압축하여 [Azure Portal](https://portal.azure.com)에서 지원 티켓을 엽니다.

* **서비스를 사용할 수 없음** 메시지가 나타나는 경우 에뮬레이터에서 네트워크 스택을 초기화하지 못했을 수 있습니다. 해당 네트워크 필터 드라이버로 인해 문제가 발생할 수 있으므로 Pulse 보안 클라이언트 또는 Juniper 네트워크 클라이언트를 설치했는지 확인합니다. 일반적으로 타사 네트워크 필터 드라이버를 제거하면 문제가 해결됩니다. 또는 /DisableRIO를 사용하여 에뮬레이터를 시작하면 에뮬레이터 네트워크 통신이 일반 Winsock으로 전환됩니다. 

* **"사용할 수 없음", "메시지": "전송 프로토콜 또는 암호화에서 금지된 암호화로 요청이 이루어지고 있습니다. 계정 SSL/TLS 최소 허용 프로토콜 설정을 확인하세요... "** 연결 문제가 발생한 경우, 이 문제는 OS의 글로벌 변경(예: Insider Preview 빌드 20170) 또는 TLS 1.3를 기본값으로 사용하는 브라우저 설정으로 인해 발생할 수 있습니다. Cosmos 에뮬레이터에 대한 요청을 실행하기 위해 SDK를 사용하는 경우와 같은 유사한 오류가 발생할 수 있습니다. 예를 들어 **Microsoft.Azure.Documents.DocumentClientException: 전송 프로토콜 또는 암호화에서 금지된 암호화로 요청이 이루어지고 있습니다. 계정 SSL/TLS 최소 허용 프로토콜 설정을 확인하세요** 와 같습니다. Cosmos 에뮬레이터는 TLS 1.2 프로토콜만 수락하고 사용하므로 이번에는 이 작업을 수행해야 합니다. 권장 해결 방법은 설정을 변경하고 기본값을 TLS 1.2로 변경하는 것입니다. IIS 관리자의 경우 "사이트" -> "기본 웹 사이트"로 이동하고 포트 8081에 대한 "사이트 바인딩"을 찾은 후 편집하여 TLS 1.3을 사용하지 않도록 설정합니다. "설정" 옵션을 통해 웹 브라우저에 대해 유사한 작업을 수행할 수 있습니다.

* 에뮬레이터 실행 중에 컴퓨터가 절전 모드가 되거나 OS 업데이트를 실행할 경우 **서비스를 현재 사용할 수 없음** 메시지가 표시될 수 있습니다. Windows 알림 트레이에 표시되는 아이콘을 마우스 오른쪽 단추로 클릭하여 **데이터 다시 설정** 을 선택하여 에뮬레이터 데이터를 다시 설정합니다.

## <a name="collect-trace-files"></a><a id="trace-files"></a>추적 파일 수집

디버깅 추적을 수집하려면 관리 명령 프롬프트에서 다음 명령을 실행합니다.

1. 에뮬레이터가 설치된 경로로 이동합니다.

   ```bash
   cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"
   ```

1. 에뮬레이터를 종료하고 시스템 트레이를 확인하여 프로그램이 종료되었는지 확인합니다. 완료하는 데 1분 정도 걸릴 수 있습니다. Azure Cosmos DB 에뮬레이터 사용자 인터페이스에서 **종료** 를 선택해도 됩니다.

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /shutdown
   ```

1. 다음 명령으로 로깅을 시작합니다.

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /startwprtraces
   ```

1. 에뮬레이터를 시작합니다.

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe
   ```

1. 문제를 재현합니다. 데이터 탐색기가 작동하지 않는 경우 브라우저가 몇 초 간 열리고 오류를 catch할 때까지 기다리면 됩니다.

1. 다음 명령을 사용하여 로깅을 중지합니다.

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /stopwprtraces
   ```
   
1. `%ProgramFiles%\Azure Cosmos DB Emulator` 경로로 이동하여 *docdbemulator_000001.etl* 파일을 찾습니다.

1. [Azure Portal](https://portal.azure.com)에서 지원 티켓을 열고 재현 단계와 함께 .etl 파일을 포함합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 로컬 에뮬레이터를 사용하여 문제를 디버그하는 방법을 배웠습니다. 이제 다음 문서로 진행할 수 있습니다.

* [Java, Python 및 Node.js 앱에서 사용할 Azure Cosmos DB 에뮬레이터 인증서 내보내기](local-emulator-export-ssl-certificates.md)
* [명령줄 매개 변수 및 PowerShell 명령을 사용하여 에뮬레이터 제어](emulator-command-line-parameters.md)
