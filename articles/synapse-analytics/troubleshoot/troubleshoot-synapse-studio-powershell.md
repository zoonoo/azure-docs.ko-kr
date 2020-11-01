---
title: Synapse Studio 연결 문제 해결
description: PowerShell을 사용 하 여 Azure Synapse Studio 연결 문제 해결
author: saveenr
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: e5e433fd857f638c1c13e4545c19e0b6314ee62e
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146509"
---
# <a name="troubleshoot-synapse-studio-connectivity-with-powershell"></a>PowerShell을 사용 하 여 Synapse Studio 연결 문제 해결

Azure Synapse Studio (미리 보기)는 제대로 작동 하는 웹 API 끝점의 집합에 따라 달라 집니다. 이 가이드는 다음과 같은 경우 연결 문제의 원인을 파악 하는 데 도움이 됩니다.
- Azure Synapse Studio에 액세스 하기 위해 로컬 네트워크 (예: 회사 방화벽 뒤에 있는 네트워크)를 구성 합니다.
- Azure Synapse Studio를 사용 하 여 연결 문제가 발생 했습니다.

## <a name="prerequisite"></a>필수 조건

* Windows의 PowerShell 5.0 이상 버전 또는
* Windows 또는 Linux의 PowerShell Core 6.0 이상 버전

## <a name="troubleshooting-steps"></a>문제 해결 단계

다음 링크를 마우스 오른쪽 단추로 클릭 하 고 "다른 이름으로 대상 저장"을 선택 합니다.

- [Test-AzureSynapse.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

또는 링크를 직접 열고 열려 있는 스크립트 파일을 저장할 수 있습니다. 나중에 변경 될 수 있으므로 위의 링크 주소를 저장 하지 마세요.

파일 탐색기에서 다운로드 한 스크립트 파일을 마우스 오른쪽 단추로 클릭 하 고 "PowerShell에서 실행"을 선택 합니다.

![PowerShell을 사용 하 여 다운로드 한 스크립트 파일 실행](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

메시지가 표시 되 면 현재 문제가 발생 하거나 연결을 테스트 하려는 Azure Synapse 작업 영역 이름을 입력 하 고 enter 키를 누릅니다.

![작업 영역 이름 입력](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

진단 세션이 시작 됩니다. 완료할 때까지 기다립니다.

![진단이 완료 될 때까지 대기](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

끝에는 진단 요약이 표시 됩니다. PC에서 하나 이상의 끝점에 연결할 수 없는 경우 "요약" 섹션에 몇 가지 제안이 표시 됩니다.

![진단 요약 검토](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

또한이 세션의 진단 로그 파일은 문제 해결 스크립트와 동일한 폴더에 생성 됩니다. 해당 위치는 "일반 팁" 섹션 ()에 표시 됩니다 `D:\TestAzureSynapse_2020....log` . 필요한 경우 기술 지원 서비스에이 파일을 보낼 수 있습니다.

네트워크 관리자이 고 Azure Synapse Studio에 대 한 방화벽 구성을 튜닝 하는 경우 "요약" 섹션 위에 표시 된 기술 세부 정보가 도움이 될 수 있습니다.

* "통과"로 표시 된 모든 테스트 항목 (요청)은 HTTP 상태 코드에 관계 없이 연결 테스트를 통과 했음을 의미 합니다.
 실패 한 요청의 경우 이유는 또는와 같이 노란색으로 표시 됩니다 `NamedResolutionFailure` `ConnectFailure` . 이러한 이유는 네트워크 환경에 잘못 된 구성이 있는지 파악 하는 데 도움이 될 수 있습니다.


## <a name="next-steps"></a>다음 단계
이전 단계에서 문제를 해결 하는 데 도움이 되지 않는 경우 [지원 티켓을 만듭니다](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).
