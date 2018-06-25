---
title: Azure Automation에서 여러 VM에 대한 업데이트 관리, 변경 내용 추적 및 인벤토리 솔루션을 등록하는 방법
description: Azure Automation에 포함된 업데이트 관리, 변경 내용 추적 및 인벤토리 솔루션을 사용하여 Azure Virtual Machine을 등록하는 방법을 알아봅니다.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: article
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 0a624d850b8c3260acb24cb17566090e8ad0043e
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233940"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>여러 VM에서 업데이트 관리, 변경 내용 추적 및 인벤토리 솔루션 사용

Azure Automation은 운영 체제 보안 업데이트를 관리하고, 변경 내용을 추적하며, 컴퓨터에 설치된 항목을 재고 자산으로 처리(인벤토리)하기 위한 솔루션을 제공합니다. 컴퓨터를 등록하는 여러 가지 방법이 있습니다. [가상 머신](automation-onboard-solutions-from-vm.md)에서 솔루션을 등록할 수도 있고, 가상 머신을 검색할 때 [Automation 계정](automation-onboard-solutions-from-automation-account.md)에서 등록할 수도 있고, [Runbook](automation-onboard-solutions.md)에서 등록할 수도 있습니다. 이 문서에서는 Azure에서 가상 머신을 검색할 때 솔루션을 등록하는 방법을 다룹니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

https://portal.azure.com에서 Azure에 로그인

## <a name="enable-solutions"></a>솔루션 사용

Azure Portal에서 **가상 머신**으로 이동합니다.

확인란을 사용하여 변경 내용 추적 및 인벤토리 또는 업데이트 관리를 등록할 가상 머신을 선택합니다. 한 번에 최대 3개의 리소스 그룹을 등록할 수 있습니다.

![VM 목록](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> 필터 컨트롤을 사용하여 가상 머신 목록을 수정한 후 맨 위에 있는 확인란을 클릭하여 목록의 가상 머신을 모두 선택합니다.

명령 모음에서 **서비스**를 클릭하고 **변경 내용 추적**, **인벤토리** 또는 **업데이트 관리**를 선택합니다.

> [!NOTE]
> **변경 내용 추적** 및 **인벤토리**는 동일한 솔루션을 사용하며, 하나가 활성화되면 다른 솔루션도 활성화됩니다.

다음 이미지는 업데이트 관리에 대한 것입니다. 변경 내용 추적 및 인벤토리의 레이아웃과 동작도 동일합니다.

가상 머신 목록은 동일한 구독 및 위치에 있는 가상 머신만 표시하도록 필터링됩니다. 가상 머신이 3개 초과 리소스 그룹에 있는 경우 처음 3개 리소스 그룹이 선택됩니다.

필터 컨트롤을 사용하여 다른 구독, 위치 및 리소스 그룹의 가상 머신을 선택할 수 있습니다.

![업데이트 관리 솔루션 등록](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Log Analytics 작업 영역 및 Automation 계정에 대한 선택 항목을 검토합니다. 새 작업 영역 및 Automation 계정은 기본적으로 선택됩니다. 기존 Log Analytics 작업 영역 및 Automation 계정을 사용하려면 **구성** 페이지에서 **변경**을 클릭하여 기존 항목을 선택하면 됩니다. 완료하면 **저장**을 클릭합니다.

![작업 영역 및 계정 선택](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

사용하지 않을 가상 머신 옆에 있는 확인란을 선택 취소합니다. 사용할 수 없는 가상 머신은 이미 선택이 취소되어 있습니다.

**사용**을 클릭하여 솔루션을 사용하도록 설정합니다. 솔루션을 사용하도록 설정하는 데 최대 15분이 걸립니다.

## <a name="troubleshooting"></a>문제 해결

여러 컴퓨터를 등록할 때 일부 컴퓨터가 **사용할 수 없음**으로 표시될 수 있습니다. 일부 컴퓨터를 사용할 수 없는 여러 가지 이유가 있습니다. 다음 섹션에서는 컴퓨터를 등록하려고 시도하면 VM 상태가 **사용할 수 없음**으로 표시되는 이유를 설명합니다.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>VM이 다른 작업 영역('\<workspaceName\>')에 보고합니다.  활성화에 사용하도록 구성 변경

**원인**: 이 오류는 등록하려는 VM이 다른 작업 영역에 보고함을 나타냅니다.

**해결 방법**: **구성으로 사용**을 클릭하여 대상 Automation 계정 및 Log Analytics 작업 영역을 변경합니다.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>VM이 이 구독에서 사용할 수 있는 작업 영역에 보고

**원인**: 가상 머신이 보고하는 작업 영역의 상태가 다음과 같습니다.

* 다른 구독에 있습니다.
* 더 이상 존재하지 않습니다.
* 액세스 권한이 없는 리소스 그룹에 있습니다.

**해결 방법**: VM이 보고하는 작업 영역과 연결된 Automation 계정을 찾아서 범위 구성을 변경하여 가상 머신을 등록합니다.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>VM 운영 체제 버전 또는 배포가 지원되지 않음

**원인:** 솔루션이 일부 Linux 배포판 또는 일부 Windows에 지원되지 않습니다.

**해결 방법:** [지원되는 클라이언트 목록](automation-update-management.md#clients)을 참조하세요.

### <a name="classic-vms-cannot-be-enabled"></a>클래식 VM을 사용할 수 없음

**원인**: 클래식 배포 모델을 사용하는 VM이 지원되지 않습니다.

**해결 방법**: 가상 머신을 리소스 관리자 배포 모델로 마이그레이션합니다. 작업 방법은 [클래식 배포 모델 리소스 마이그레이션](../virtual-machines/windows/migration-classic-resource-manager-overview.md)을 참조하세요.

### <a name="vm-is-stopped-deallocated"></a>VM이 중지되었습니다. (할당 취소됨)

**원인**: 가상 머신의 상태가 **실행 중**이 아닙니다.

**해결 방법**: VM을 솔루션에 등록하려면 VM이 실행 중이어야 합니다. 페이지에서 나가지 말고 **VM 시작** 인라인 링크를 클릭하여 VM을 시작합니다.

## <a name="next-steps"></a>다음 단계

가상 머신에 솔루션을 사용하도록 설정했으니, 업데이트 관리 개요 문서를 방문하여 컴퓨터의 업데이트 평가를 살펴보는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [업데이트 관리 - 업데이트 평가 보기](./automation-update-management.md#viewing-update-assessments)

솔루션 및 솔루션 사용 방법에 대한 추가 자습서:

* [자습서 - VM 업데이트 관리](automation-tutorial-update-management.md)

* [자습서 - VM에 설치된 소프트웨어 식별](automation-tutorial-installed-software.md)

* [자습서 - VM에 대한 변경 내용 문제 해결](automation-tutorial-troubleshoot-changes.md)