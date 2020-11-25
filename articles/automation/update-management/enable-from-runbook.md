---
title: Runbook에서 Azure Automation 업데이트 관리 사용
description: 이 문서에서는 Runbook에서 업데이트 관리를 사용하도록 설정하는 방법을 설명합니다.
services: automation
ms.topic: conceptual
ms.date: 11/24/2020
ms.custom: mvc
ms.openlocfilehash: 5a9f12a823a22bfb48ccb4482d3402464aa77fea
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95908366"
---
# <a name="enable-update-management-from-a-runbook"></a>Runbook에서 업데이트 관리 사용

이 문서에서는 Runbook을 사용하여 사용자 환경의 VM에 [업데이트 관리](overview.md) 기능을 사용하도록 설정하는 방법을 설명합니다. 대규모로 Azure Vm을 사용 하도록 설정 하려면 업데이트 관리를 사용 하 여 기존 VM을 사용 하도록 설정 해야 합니다.

> [!NOTE]
> 업데이트 관리를 사용하도록 설정할 때 특정 Azure 지역에서만 Log Analytics 작업 영역 및 Automation 계정을 연결할 수 있습니다. 지원되는 매핑 쌍 목록은 [Automation 계정의 지역 매핑 및 Log Analytics 작업 영역](../how-to/region-mappings.md)을 참조하세요.

이 메서드는 두 개의 runbook을 사용 합니다.

* **Enable-multiplesolution** -구성 정보를 묻는 메시지를 표시 하 고, 지정 된 VM을 쿼리하고, 기타 유효성 검사를 수행 하 고, 지정 된 리소스 그룹 내의 각 vm에 대 한 업데이트 관리를 구성 하기 위해 **-automationsolution** runbook을 호출 하는 기본 runbook입니다.
* **사용-AutomationSolution** -대상 리소스 그룹에 지정 된 하나 이상의 vm에 대 한 업데이트 관리를 사용 하도록 설정 합니다. 필수 구성 요소가 충족 되는지 확인 하 고, VM 확장이 설치 되어 있는지 Log Analytics 확인 하 고, 찾을 수 없는 경우 설치 하 고, 자동화 계정에 연결 된 지정 된 Log Analytics 작업 영역의 범위 구성에 Vm을 추가 합니다.

## <a name="prerequisites"></a>필수 구성 요소

* 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* 컴퓨터를 관리하기 위한 [Automation 계정](../automation-security-overview.md)
* [Log Analytics 작업 영역](../../azure-monitor/platform/design-logs-deployment.md)
* [가상 머신](../../virtual-machines/windows/quick-create-portal.md).
* 두 가지 자동화 자산으로, **사용 하도록 설정-AutomationSolution** runbook에서 사용 됩니다. 이 runbook이 Automation 계정에 아직 없는 경우 처음 실행 하는 동안 **enable-multiplesolution** runbook에서 자동으로 가져옵니다.
    * *LASolutionSubscriptionId*: Log Analytics 작업 영역이 있는의 구독 ID입니다.
    * *LASolutionWorkspaceId*: Automation 계정에 연결 된 Log Analytics 작업 영역의 작업 영역 ID입니다.

    이러한 변수는 등록 VM의 작업 영역을 구성 하는 데 사용 되며 수동으로 만들어야 합니다. 이러한 항목을 지정 하지 않으면 스크립트는 먼저 구독에서 업데이트 관리 VM 등록을 검색 한 다음 Automation 계정이 있는 구독을 검색 한 다음 사용자 계정이 액세스할 수 있는 다른 모든 구독을 검색 합니다. 제대로 구성 되지 않은 경우 컴퓨터가 임의의 Log Analytics 작업 영역에 등록 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="enable-update-management"></a>업데이트 관리 사용

1. Azure Portal에서 **Automation 계정** 으로 이동 합니다. **Automation 계정** 페이지의 목록에서 계정을 선택 합니다.

2. Automation 계정의 **업데이트 관리** 아래에서 **업데이트 관리** 를 선택합니다.

3. Log Analytics 작업 영역을 선택한 다음, **사용** 을 클릭합니다. 업데이트 관리를 사용 하도록 설정 하는 동안 배너가 표시 됩니다.

    ![업데이트 관리 사용](media/enable-from-runbook/enable-update-management.png)

## <a name="install-and-update-modules"></a>모듈 설치 및 업데이트

Runbook을 사용 하 여 Vm에 대 한 업데이트 관리를 성공적으로 사용 하려면 최신 Azure 모듈로 업데이트 하 고 [OperationalInsights](/powershell/module/azurerm.operationalinsights) 모듈을 가져와야 합니다.

1. Automation 계정의 **공유 리소스** 에서 **모듈** 을 선택합니다.

2. **Azure 모듈 업데이트** 를 선택하여 Azure 모듈을 최신 버전으로 업데이트합니다.

3. **예** 를 클릭하여 기존의 모든 Azure 모듈을 최신 버전으로 업데이트합니다.

    ![모듈 업데이트](media/enable-from-runbook/update-modules.png)

4. **공유 리소스** 에서 **모듈** 로 돌아갑니다.

5. **갤러리 찾아보기** 를 선택하여 모듈 갤러리를 엽니다.

6. `AzureRM.OperationalInsights`를 검색하고 이 모듈을 Automation 계정으로 가져옵니다.

    ![OperationalInsights 모듈 가져오기](media/enable-from-runbook/import-operational-insights-module-azurerm.png)

## <a name="select-azure-vm-to-manage"></a>관리할 Azure VM 선택

업데이트 관리를 사용하도록 설정하면 Azure VM을 추가하여 업데이트를 받을 수 있습니다.

1. Automation 계정에서 **업데이트 관리** 섹션 아래에 있는 **업데이트 관리** 를 선택 합니다.

2. **Azure VM 추가** 를 선택하여 VM을 추가합니다.

3. 목록에서 VM을 선택 하 고 **사용** 을 클릭 하 여 관리를 위한 vm을 구성 합니다.

   ![VM에 업데이트 관리 사용](media/enable-from-runbook/enable-update-management-vm.png)

    > [!NOTE]
    > 업데이트 관리 설정이 완료되기 전에 다른 기능을 사용하도록 설정하려고 하면 다음 메시지가 표시됩니다. `Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="import-a-runbook-to-enable-update-management"></a>업데이트 관리를 사용하도록 설정할 Runbook 가져오기

1. Automation 계정의 **프로세스 자동화** 에서 **Runbook** 을 선택합니다.

2. **갤러리 찾아보기** 를 선택합니다.

3. **업데이트 및 변경 내용 추적** 을 검색 합니다.

4. Runbook을 선택 하 고 **원본 보기** 페이지에서 **가져오기** 를 클릭 합니다.

5. **확인** 을 클릭하여 Runbook을 Automation 계정으로 가져옵니다.

   ![설정할 Runbook 가져오기](media/enable-from-runbook/import-from-gallery.png)

6. **Runbook** 페이지에서 **enable-multiplesolution** Runbook을 선택한 다음 **편집** 을 클릭 합니다. 텍스트 편집기에서  **게시** 를 선택 합니다.

7. 확인 메시지가 표시 되 면 **예** 를 클릭 하 여 runbook을 게시 합니다.

## <a name="start-the-runbook"></a>Runbook을 시작합니다.

이 Runbook을 시작하려면 Azure VM에 대한 업데이트 관리가 사용하도록 설정되어 있어야 합니다. 대상 리소스 그룹에서 하나 이상의 Vm을 구성 하기 위해 기능이 사용 하도록 설정 된 기존 VM 및 리소스 그룹이 필요 합니다.

1. **Enable-MultipleSolution** Runbook을 엽니다.

   ![다중 솔루션 Runbook](media/enable-from-runbook/runbook-overview.png)

2. 시작 단추를 클릭하고 다음 필드에 매개 변수 값을 입력합니다.

   * **VMNAME** - 업데이트 관리에 추가할 기존 VM의 이름입니다. 리소스 그룹의 모든 VM을 추가하려면 이 필드를 비워 두세요.
   * **VMRESOURCEGROUP** - 사용하도록 설정할 VM의 리소스 그룹 이름입니다.
   * **SUBSCRIPTIONID** - 사용하도록 설정할 새 VM의 구독 ID입니다. 작업 영역의 구독을 사용하려면 이 필드를 비워 두세요. 다른 구독 ID를 사용하는 경우 Automation 계정의 실행 계정을 구독의 기여자로 추가합니다.
   * **ALREADYONBOARDEDVM** - 이미 수동으로 업데이트를 사용하도록 설정한 VM의 이름입니다.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** - VM이 속한 리소스 그룹의 이름입니다.
   * **SOLUTIONTYPE** - **업데이트** 를 입력합니다.

   ![Enable-MultipleSolution Runbook 매개 변수](media/enable-from-runbook/runbook-parameters.png)

3. **확인** 을 선택하여 Runbook 작업을 시작합니다.

4. **작업** 페이지에서 runbook 작업 및 모든 오류에 대 한 진행률을 모니터링 합니다.

## <a name="next-steps"></a>다음 단계

* Vm에 대 한 업데이트 관리를 사용 하려면 [vm에 대 한 업데이트 및 패치 관리](manage-updates-for-vm.md)를 참조 하세요.

* 일반적인 업데이트 관리 오류를 해결하려면 [업데이트 관리 문제 해결](../troubleshoot/update-management.md)을 참조하세요.
