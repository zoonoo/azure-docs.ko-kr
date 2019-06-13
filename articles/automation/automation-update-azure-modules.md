---
title: Azure Automation에서 Azure 모듈 업데이트
description: 이 문서에서는 Azure Automation에 기본적으로 제공되는 일반 Azure PowerShell 모듈을 즉시 업데이트하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 05/30/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c09c9df1b8af7adca5c0169cf31881121ba6cc99
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427493"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법

사용 해야 합니다. Automation 계정에서 Azure 모듈을 업데이트 하는 [업데이트 Azure 모듈 runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), 오픈 소스로 사용할 수 있는 합니다. **Update-AutomationAzureModulesForAccount** Runbook을 사용하여 Azure 모듈을 업데이트하려면 GitHub의 [Azure 모듈 Runbook 리포지토리 업데이트](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)에서 다운로드합니다. 그런 다음, Automation 계정으로 가져오거나 스크립트로 실행할 수 있습니다. Automation 계정에서 runbook을 가져오는 방법에 알아보려면 참조 [runbook을 가져오려면](manage-runbooks.md#import-a-runbook)합니다.

가장 일반적인 AzureRM PowerShell 모듈은 각 Automation 계정에서 기본적으로 제공 됩니다. Azure 팀은 Azure 모듈을 정기적으로 업데이트, 따라서를 최신 상태로 유지 하는 사용 하려는 합니다 [업데이트 AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) runbook을 Automation 계정에서 모듈을 업데이트 합니다.

제품 그룹에 의해 정기적으로 모듈이 업데이트되므로 포함된 cmdlet이 변경될 수 있습니다. 이 작업은 매개 변수 이름을 바꾸거나 cmdlet을 완전히 중단하는 등 변경 형식에 따라 Runbook에 부정적인 영향을 줄 수 있습니다.

자동화하는 프로세스 및 Runbook에 영향을 주지 않으려면 계속하기 전에 테스트 및 유효성 검사를 수행합니다. 이 용도로 설계된 전용 Automation 계정이 없는 경우 만들어서 Runbook을 개발하는 동안 다양한 시나리오를 테스트할 수 있습니다. 이 테스트에는 PowerShell 모듈을 업데이트하는 등 반복적인 변경 내용도 포함되어야 합니다.

로컬로 스크립트를 개발하는 경우 동일한 결과를 받기 위해 테스트 시 Automation 계정에서 사용하는 것과 동일한 모듈 버전을 로컬에서 사용하는 것이 좋습니다. 결과의 유효성을 검사하고 필수 변경 내용을 적용한 후에 변경 내용을 프로덕션으로 이동할 수 있습니다.

> [!NOTE]
> 새 자동화 계정에 최신 모듈이 없을 수도 있습니다.

## <a name="considerations"></a>고려 사항

다음은 이 프로세스를 사용하여 Azure 모듈을 업데이트할 때 고려해야 하는 몇 가지 사항입니다.

* 이 runbook만 업데이트를 지원 합니다 **Azure** 하 고 **AzureRm** 현재 모듈입니다. [Azure PowerShell Az 모듈](/powershell/azure/new-azureps-module-az) Automation 계정에서 지원 되지만이 runbook을 사용 하 여 업데이트할 수 없습니다. 사용 하는 경우 고려해 야 할 중요 한 요인이 있습니다 합니다 `Az` 자세한 내용은 Automation 계정의 모듈 참조 [Automation 계정에서 사용 하 여 Az 모듈](az-modules.md)합니다.

* Az 모듈이 포함된 Automation 계정에서 이 Runbook을 시작하지 마세요.

* 이 Runbook을 시작하기 전에 Automation 계정에 [Azure 실행 계정 자격 증명](manage-runas-account.md)이 만들어져 있는지 확인합니다.

* Runbook 대신 PowerShell 스크립트를 정기적으로이 코드를 사용할 수 있습니다: 사용 하 여 Azure에 로그인 합니다 [Connect-azurermaccount](/powershell/module/azurerm.profile/connect-azurermaccount) 먼저 명령을 사용한 다음 전달 `-Login $false` 스크립트입니다.

* 소버린 클라우드에서 이 Runbook을 사용하려면 `AzureRmEnvironment` 매개 변수를 사용하여 올바른 환경을 Runbook에 전달합니다.  허용되는 값은 **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud** 및 **AzureUSGovernment**입니다. `Get-AzureRmEnvironment | select Name`을 사용하여 이러한 값을 가져올 수 있습니다. 이 매개 변수에 값을 전달하지 않으면 Runbook이 기본적으로 **AzureCloud** Azure 공용 클라우드로 설정됩니다.

* PowerShell 갤러리에서 사용할 수 있는 최신 버전 대신 특정 Azure PowerShell 모듈 버전을 사용하려면 이러한 버전을 **Update-AutomationAzureModulesForAccount** Runbook의 선택적 `ModuleVersionOverrides` 매개 변수에 전달합니다. 예제는 [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) Runbook을 참조하세요. `ModuleVersionOverrides` 매개 변수에 언급되지 않은 Azure PowerShell 모듈은 PowerShell 갤러리의 최신 모듈 버전으로 업데이트됩니다. `ModuleVersionOverrides` 매개 변수에 아무 것도 전달하지 않으면 모든 모듈이 PowerShell 갤러리의 최신 모듈 버전으로 업데이트됩니다. 이 동작은 **Azure 모듈 업데이트** 단추와 동일합니다.

## <a name="known-issues"></a>알려진 문제

0부터 시작 하는 숫자 이름의 리소스 그룹에 있는 Automation 계정에는 AzureRM 모듈을 업데이트 하는 알려진된 문제가 있습니다. Automation 계정에서 Azure 모듈 업데이트에 영숫자 이름을 가진 리소스 그룹에 이어야 합니다. 0부터 시작 하는 숫자 이름의 리소스 그룹에 AzureRM 모듈을 업데이트할 수 없는 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 오픈 소스 [Azure 모듈 Runbook 업데이트](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)를 참조하세요.
