---
title: 문제 해결 Azure 시냅스 스튜디오 (미리 보기) 연결 파워 쉘
description: PowerShell을 사용하여 Azure 시냅스 스튜디오 연결 문제 해결
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: bbc985407a6cb56f4f1b539f514ab092b5f7d0de
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431476"
---
# <a name="diagnose-azure-synapse-studio-preview-connectivity-issues-with-powershell-script"></a>PowerShell 스크립트로 Azure 시냅스 스튜디오(미리 보기) 연결 문제 진단

Azure Synapse Studio(미리 보기)는 제대로 작동하려면 웹 API 끝점 집합에 따라 다릅니다. 이 가이드는 다음과 같은 경우 연결 문제의 원인을 식별하는 데 도움이 됩니다.
- Azure Synapse Studio에 액세스하기 위해 로컬 네트워크(예: 회사 방화벽 뒤의 네트워크)를 구성합니다.
- Azure Synapse Studio를 사용하여 연결 문제가 발생합니다.

## <a name="prerequisite"></a>필수 요소

* 윈도우에서 PowerShell 5.0 이상 버전 또는
* 윈도우 또는 리눅스에서 파워 쉘 코어 6.0 이상 버전.

## <a name="troubleshooting-steps"></a>문제 해결 단계

다음 링크를 마우스 오른쪽 단추로 클릭하고 "대상 저장"을 클릭합니다.

- [테스트-AzureSynapse.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

또는 링크를 직접 열고 열린 스크립트 파일을 저장할 수 있습니다. 위의 링크의 주소를 저장하지 마십시오, 그것은 미래에 변경 될 수 있으므로.

파일 탐색기에서 다운로드한 스크립트 파일을 마우스 오른쪽 단추로 클릭하고 "PowerShell으로 실행"을 클릭합니다.

![PowerShell으로 다운로드한 스크립트 파일 실행](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

메시지가 표시되면 현재 문제가 발생하거나 연결을 테스트하려는 Azure Synapse 작업 영역 이름을 입력하고 enter키키키키누릅니다.

![작업 영역 이름 입력](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

진단 세션이 시작됩니다. 완료할 때까지 기다립니다.

![진단이 완료될 때까지 기다립니다.](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

결국 진단 요약이 표시됩니다. PC가 하나 이상의 끝점에 연결할 수 없는 경우 "요약" 섹션에 몇 가지 제안 사항이 표시됩니다.

![진단 요약 검토](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

또한 이 세션의 진단 로그 파일은 문제 해결 스크립트와 동일한 폴더에 생성됩니다. 해당 위치는 "일반 팁" 섹션()에`D:\TestAzureSynapse_2020....log`표시됩니다. 필요한 경우 이 파일을 기술 지원팀에 보낼 수 있습니다.

네트워크 관리자이고 Azure Synapse Studio에 대한 방화벽 구성을 조정하는 경우 "요약" 섹션 위에 표시된 기술 세부 정보가 도움이 될 수 있습니다.

* "통과됨"으로 표시된 모든 테스트 항목(요청)은 HTTP 상태 코드에 관계없이 연결 테스트를 통과했다는 의미입니다.
 실패한 요청의 경우 그 이유는 `NamedResolutionFailure` 또는 `ConnectFailure`노란색으로 표시됩니다. 이러한 이유로 인해 네트워크 환경에 잘못된 구성이 있는지 여부를 파악하는 데 도움이 될 수 있습니다.


## <a name="next-steps"></a>다음 단계
이전 단계에서 해결에 도움이 되지 않는 경우 [지원 티켓 만들기](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md)를 발급합니다.
