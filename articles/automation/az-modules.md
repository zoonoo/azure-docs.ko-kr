---
title: Azure Automation의 Az 모듈 지원
description: 이 문서에서는 Azure 자동화에서 Az 모듈 사용에 대한 정보를 제공합니다.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: 776834937d81a3ba84e3c1db56496a7d951d7982
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548354"
---
# <a name="az-module-support-in-azure-automation"></a>Azure Automation의 Az 모듈 지원

Azure 자동화는 Runbook에서 [Azure PowerShell Az 모듈의](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) 사용을 지원합니다. Az 모듈은 새 또는 기존 자동화 계정에서 자동으로 가져오지 않습니다. 

## <a name="considerations"></a>고려 사항

Azure Automation에서 Az 모듈을 사용할 경우 여러 가지 사항을 고려해야 합니다. Runbook 및 모듈은 자동화 계정의 상위 수준 솔루션에서 사용할 수 있습니다. Runbook을 편집하거나 모듈을 업그레이드하면 Runbook에 문제가 발생할 수 있습니다. 새 `Az` 모듈을 가져오기 전에 별도의 자동화 계정에서 모든 Runbook 및 솔루션을 신중하게 테스트해야 합니다. 모듈을 수정하면 [시작/중지](automation-solution-vm-management.md) 솔루션에 부정적인 영향을 줄 수 있습니다. 솔루션이 포함된 자동화 계정의 모듈 및 runbook을 변경하는 것은 권장되지 않습니다. 이 동작은 Az 모듈과 관련이 없습니다. 자동화 계정에 변경 사항을 도입할 때 이 동작을 고려해야 합니다.

자동화 계정에서 `Az` 모듈을 가져오면 Runbook에서 사용하는 PowerShell 세션에서 모듈을 자동으로 가져오지 않습니다. 다음과 같은 상황에서 모듈을 PowerShell 세션으로 가져올 수 있습니다.

* 모듈의 cmdlet이 Runbook에서 호출될 때
* Runbook이 `Import-Module` cmdlet을 사용하여 명시적으로 가져올 때
* 모듈을 사용하는 다른 모듈을 PowerShell 세션으로 가져올 때

> [!IMPORTANT]
> 자동화 계정의 Runbook이 RunBook에서 사용되는 PowerShell `AzureRM` 세션에 만 가져오거나 `Az` 모듈을 가져오는지, 둘 다 아닌지 확인하는 것이 중요합니다. Runbook에서 `Az` 이전에 `AzureRM` 가져온 경우 Runbook이 완료되지만 작업 스트림에 [Get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) cmdlet을 참조하는 오류가 나타나고 cmdlet이 제대로 실행되지 않을 수 있습니다. 가져온 `AzureRM` 다음 `Az`Runbook이 계속 완료되지만 작업 스트림에서 동일한 세션에서 가져오거나 `Az` `AzureRM` 동일한 Runbook에서 사용할 수 없다는 오류가 나타납니다.

## <a name="migrating-to-az-modules"></a>Az 모듈로 마이그레이션

테스트 자동화 계정에서 Az 모듈로의 마이그레이션을 테스트하는 것이 좋습니다. 해당 자동화 계정이 만들어지면 이 섹션의 지침을 사용하여 모듈을 사용할 수 있습니다.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-cmdlets"></a>AzureRM cmdlet을 사용하는 모든 Runbook을 중지하고 예약 취소

`AzureRM` cmdlet을 사용하는 기존 Runbook을 실행하지 않으려면 `AzureRM` 모듈을 사용하는 모든 Runbook을 중지한 후 예약 취소합니다. 이 예제와 유사한 코드를 실행하여 어떤 일정이 존재하고 제거할 일정을 확인할 수 있습니다.

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

각 일정을 별도로 검토하여 필요한 경우 나중에 Runbook에 맞게 일정을 변경할 수 있도록 하는 것이 중요합니다.

### <a name="import-the-az-modules"></a>Az 모듈 가져오기

Runbook에 필요한 Az 모듈만 가져옵니다. 롤업 모듈은 모든 `Az` `Az.*` 모듈을 포함하므로 롤업 모듈을 가져오지 마십시오. 이 지침은 모든 모듈에 대해 동일합니다.

[Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) 모듈은 다른 `Az.*` 모듈에 대한 종속성입니다. 따라서 다른 모듈을 가져오기 전에 이 모듈을 Automation 계정으로 가져와야 합니다.

자동화 계정에서 **공유 리소스**에서 **모듈을 선택합니다.** **갤러리 찾아보기**를 클릭하여 **갤러리 찾아보기** 페이지를 엽니다.  검색 표시줄에서 모듈 이름(예: `Az.Accounts`)을 입력합니다. PowerShell 모듈 페이지에서 **가져오기를** 클릭하여 모듈을 자동화 계정으로 가져옵니다.

![자동화 계정에서 모듈 가져오기](media/az-modules/import-module.png)

이 가져오기 프로세스는 가져올 모듈을 검색하여 [PowerShell 갤러리를](https://www.powershellgallery.com) 통해 수행할 수도 있습니다. 모듈을 찾은 후에는 선택하고 **Azure Automation** 탭 아래에서 **Azure Automation에 배포**를 클릭합니다.

![갤러리에서 직접 모듈 가져오기](media/az-modules/import-gallery.png)

## <a name="testing-your-runbooks"></a>Runbook 테스트

모듈을 `Az` 자동화 계정으로 가져오면 Runbook 편집을 시작하여 Az 모듈을 사용할 수 있습니다. cmdlet의 대부분은 `AzureRM` `Az`로 변경된 경우를 제외하고 동일한 이름을 갖습니다. 이 명명 규칙을 따르지 않는 모듈 목록은 [예외 목록을](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)참조하십시오.

새 cmdlet을 사용 하 여 Runbook의 수정을 `Enable-AzureRMAlias -Scope Process` 테스트 하는 한 가지 방법은 Runbook의 시작 부분에서 사용 하는 것입니다. Runbook에 이 명령을 추가하면 스크립트를 변경 없이 실행할 수 있습니다.

## <a name="after-migration-details"></a>마이그레이션 후 세부 정보

마이그레이션이 완료된 후에는 자동화 계정의 모듈을 사용하여 `AzureRM` Runbook을 더 이상 시작하지 마십시오. 또한 계정에서 모듈을 가져오거나 `AzureRM` 업데이트하지 않는 것이 좋습니다. 로 마이그레이션된 `Az`계정을 고려하고 모듈로만 `Az` 작동합니다. 

새 자동화 계정을 만들면 기존 `AzureRM` 모듈이 계속 설치됩니다. cmdlet을 통해 `AzureRM` 자습서 Runbook을 업데이트할 수 있습니다. 이러한 Runbook을 실행해서는 안 됩니다.

## <a name="next-steps"></a>다음 단계

Az 모듈을 사용하는 방법에 대한 자세한 내용은 [Az 모듈 시작](/powershell/azure/get-started-azureps?view=azps-1.1.0)을 참조하세요.
