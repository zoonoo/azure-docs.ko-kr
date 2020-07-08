---
title: 업데이트 관리를 위해 Automation 계정에서 작업 영역 연결 해제
description: 이 문서에서는 업데이트 관리를 위해 Automation 계정에서 Log Analytics 작업 영역의 연결을 해제하는 방법을 설명합니다.
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 9129d10071a4c8da0376cbad3d64c10cbaceb8b9
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835787"
---
# <a name="unlink-workspace-from-automation-account-for-update-management"></a>업데이트 관리를 위해 Automation 계정에서 작업 영역 연결 해제

[업데이트 관리](automation-update-management.md) 작업 중에 Automation 계정을 Log Analytics 작업 영역과 통합하지 않도록 결정할 수 있습니다. 이 문서에서는 계정에서 작업 영역을 연결 해제하는 방법을 설명합니다.

1. https://portal.azure.com 에서 Azure에 로그인합니다.

2. VM에 대한 업데이트 관리를 제거합니다. [업데이트 관리에서 VM 제거](automation-remove-vms-from-update-management.md)를 참조하세요.

3. 업데이트 관리에 이전 버전의 Azure SQL 모니터링이 포함된 경우 기능을 설정하면 제거해야 할 Automation 자산이 생성되었을 수도 있습니다. 업데이트 관리의 경우 더 이상 필요하지 않은 다음 항목을 제거할 수도 있습니다.

   * 업데이트 일정 - 각 항목에는 사용자가 만든 업데이트 배포와 일치하는 이름이 있습니다.
   * 업데이트 관리용으로 만든 Hybrid Worker 그룹 - 각 그룹에는 machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8과 비슷하게 이름이 지정됩니다.

4. Automation 계정을 열고 왼쪽의 **관련 리소스** 아래에서 **연결된 작업 영역**을 선택합니다.

5. 작업 영역 연결 해제 페이지에서 **작업 영역 연결 해제**를 클릭하고 프롬프트에 응답합니다.

   ![작업 영역 연결 해제 페이지](media/automation-unlink-workspace-update-management/automation-unlink-workspace-blade.png).

6. Azure Automation이 Log Analytics 작업 영역에서 연결을 해제하는 동안 메뉴의 **알림**에서 진행 상황을 추적할 수 있습니다.

또는 작업 영역 내 Automation 계정에서 Log Analytics 작업 영역의 연결을 해제할 수 있습니다.

1. 작업 영역의 **관련 리소스** 아래에서 **Automation 계정**을 선택합니다. 
2. Automation 계정 페이지에서 **계정 연결 해제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

* 기능을 사용하려면 [Azure VM에 대한 업데이트 및 패치 관리](automation-tutorial-update-management.md)를 참조하세요.
* 기능 오류를 해결하려면 [업데이트 관리 문제 해결](troubleshoot/update-management.md)을 참조하세요.
* Windows 업데이트 에이전트 오류를 해결하려면 [Windows 업데이트 에이전트 문제 해결](troubleshoot/update-agent-issues.md)을 참조하세요.
* Linux 업데이트 에이전트 오류를 해결하려면 [Linux 업데이트 에이전트 문제 해결](troubleshoot/update-agent-issues-linux.md)을 참조하세요.
