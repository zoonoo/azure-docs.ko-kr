---
title: "Azure Automation 계정 구성 유효성 검사 | Microsoft Docs"
description: "이 문서에서는 Automation 계정 구성이 제대로 설정되었는지 확인하는 방법을 설명합니다."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/14/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 5bed2616e15a2e5f52e79d0c28159b568e7a1de3
ms.lasthandoff: 04/15/2017

---

# <a name="test-azure-automation-run-as-account-authentication"></a>Azure Automation 실행 계정 인증 테스트
Automation 계정이 성공적으로 만들어지면 새로 만들어지거나 업데이트된 Automation 실행 계정을 사용하여 Azure Resource Manager 또는 Azure 클래식 배포에서 성공적으로 인증할 수 있는지를 확인하는 간단한 테스트를 수행할 수 있습니다.    

## <a name="automation-run-as-authentication"></a>Automation 실행 인증

1. Azure Portal에서 이전에 만든 Automation 계정을 엽니다.  
2. **Runbook** 타일을 클릭하여 Runbook 목록을 엽니다.
3. **AzureAutomationTutorialScript** Runbook을 선택한 다음 **시작**을 클릭하여 Runbook을 시작합니다.  Runbook을 시작할지 확인하는 메시지가 나타납니다.
4. [Runbook 작업](automation-runbook-execution.md) 이 만들어지고, 작업 블레이드가 표시되며, **작업 요약** 타일에 작업 상태가 표시됩니다.  
5. 작업 상태는 클라우드의 Runbook 작업자가 사용 가능해질 때까지 기다리고 있음을 나타내는 *대기 중* 으로 시작합니다. 작업자가 작업을 요구한 경우, *시작 중*으로 바뀐 다음 Runbook이 실제로 실행되기 시작하면 *실행 중*으로 바뀝니다.  
6. Runbook 작업이 완료되면 **완료됨** 상태가 나타나야 합니다.<br> ![보안 주체 Runbook 테스트](media/automation-verify-runas-authentication/job-summary-automationtutorialscript.png)<br>
7. Runbook의 자세한 결과를 보려면 **출력** 타일을 클릭합니다.
8. **출력** 블레이드에서 리소스 그룹에서 사용할 수 있는 모든 리소스의 목록이 성공적으로 인증되고 반환되는 것을 볼 수 있습니다.
9. **출력** 블레이드를 닫으면 **작업 요약** 블레이드로 돌아갑니다.
10. **작업 요약**과 해당 **AzureAutomationTutorialScript** Runbook 블레이드를 닫습니다.

## <a name="classic-run-as-authentication"></a>클래식 실행 인증
클래식 배포 모델에서 리소스를 관리하는 경우 다음 단계를 수행하여 새 클래식 실행 계정을 사용하여 성공적으로 인증할 수 있는지 확인합니다.     

1. Azure Portal에서 이전에 만든 Automation 계정을 엽니다.  
2. **Runbook** 타일을 클릭하여 Runbook 목록을 엽니다.
3. **AzureClassicAutomationTutorialScript** Runbook을 선택한 다음 **시작**을 클릭하여 Runbook을 시작합니다.  Runbook을 시작할지 확인하는 메시지가 나타납니다.
4. [Runbook 작업](automation-runbook-execution.md) 이 만들어지고, 작업 블레이드가 표시되며, **작업 요약** 타일에 작업 상태가 표시됩니다.  
5. 작업 상태는 클라우드의 Runbook 작업자가 사용 가능해질 때까지 기다리고 있음을 나타내는 *대기 중* 으로 시작합니다. 작업자가 작업을 요구한 경우, *시작 중*으로 바뀐 다음 Runbook이 실제로 실행되기 시작하면 *실행 중*으로 바뀝니다.  
6. Runbook 작업이 완료되면 **완료됨** 상태가 나타나야 합니다.<br><br> ![보안 주체 Runbook 테스트](media/automation-verify-runas-authentication/job-summary-automationclassictutorialscript.png)<br>  
7. Runbook의 자세한 결과를 보려면 **출력** 타일을 클릭합니다.
8. **출력** 블레이드에서 구독의 모든 클래식 VM 목록이 성공적으로 인증되고 반환되는 것을 볼 수 있어야 합니다.
9. **출력** 블레이드를 닫으면 **작업 요약** 블레이드로 돌아갑니다.
10. **작업 요약**과 해당 **AzureClassicAutomationTutorialScript** Runbook 블레이드를 닫습니다.

## <a name="next-steps"></a>다음 단계
* PowerShell Runbook을 시작하려면 [내 첫 번째 PowerShell Runbook](automation-first-runbook-textual-powershell.md)을 참조하세요.
* 그래픽 작성에 대해 자세히 알아보려면 [Azure Automation에서 그래픽 작성](automation-graphical-authoring-intro.md)을 참조하세요.

