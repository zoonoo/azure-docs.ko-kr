---
title: Synapse Studio 연결 문제 해결
description: PowerShell을 사용하여 Azure Synapse Studio 연결 문제 해결
author: saveenr
ms.service: synapse-analytics
ms.subservice: troubleshooting
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 431ceed6f6f272c4473b6fc4e5b59941b4a94b5e
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108143218"
---
# <a name="troubleshoot-synapse-studio-connectivity-with-powershell"></a>PowerShell을 사용한 Synapse Studio 연결 문제 해결

Azure Synapse Studio는 정상적으로 작동하는 웹 API 엔드포인트의 집합에 따라 달라집니다. 이 가이드는 다음과 같은 경우 연결 문제의 원인을 파악하는 데 도움이 됩니다.
- Azure Synapse Studio에 액세스하기 위해 로컬 네트워크(예: 회사 방화벽 뒤에 있는 네트워크)를 구성합니다.
- Azure Synapse Studio를 사용할 때 연결 문제가 발생합니다.

## <a name="prerequisite"></a>필수 조건

* Windows의 PowerShell 5.0 이상 버전 또는
* Windows 또는 Linux의 PowerShell Core 6.0 이상 버전.

## <a name="troubleshooting-steps"></a>문제 해결 단계

다음 링크를 마우스 오른쪽 단추로 클릭하고 “다른 이름으로 대상 저장”을 선택합니다.

- [Test-AzureSynapse.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

또는 링크를 직접 열어 열려 있는 스크립트 파일을 저장할 수 있습니다. 나중에 변경될 수 있으므로 위의 링크 주소는 저장하지 마세요.

파일 탐색기에서 다운로드한 스크립트 파일을 마우스 오른쪽 단추로 클릭하고 "PowerShell에서 실행"을 선택합니다.

![PowerShell을 사용하여 다운로드한 스크립트 파일 실행](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

메시지가 표시되면 현재 문제가 발생하거나 연결을 테스트하려는 Azure Synapse 작업 영역 이름을 입력하고 Enter 키를 누릅니다.

![작업 영역 이름 입력](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

진단 세션이 시작됩니다. 완료할 때까지 기다립니다.

![진단이 완료될 때까지 대기](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

끝에 진단 요약이 표시됩니다. PC에서 하나 이상의 엔드포인트에 연결할 수 없는 경우 "요약" 섹션에 몇 가지 제안이 표시됩니다.

![진단 요약 검토](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

또한 이 세션의 진단 로그 파일은 문제 해결 스크립트와 동일한 폴더에 생성됩니다. 해당 위치는 "일반 팁" 섹션(`D:\TestAzureSynapse_2020....log`)에 표시됩니다. 필요한 경우 지원 팀에 이 파일을 보낼 수 있습니다.

네트워크 관리자이고 Azure Synapse Studio에 대한 방화벽 구성을 튜닝하는 경우 "요약" 섹션 위에 표시된 기술 세부 정보가 도움이 될 수 있습니다.

* "통과"로 표시된 모든 테스트 항목(요청)은 HTTP 상태 코드에 관계없이 연결 테스트를 통과했음을 의미합니다.
 실패한 요청의 경우 이유는 `NamedResolutionFailure` 또는 `ConnectFailure`와 같이 노란색으로 표시됩니다. 이러한 이유는 네트워크 환경에 잘못된 구성이 있는지 파악하는 데 도움이 될 수 있습니다.


## <a name="next-steps"></a>다음 단계
이전 단계가 이슈를 해결하는 데 도움이 되지 않는 경우, [지원 티켓을 만듭니다](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).