---
title: Azure 자동화에서 Azure PowerShell 모듈 업데이트
description: 이 문서에서는 Azure Automation에 기본적으로 제공되는 일반 Azure PowerShell 모듈을 즉시 업데이트하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: bb73b3d644e96f9596f887faaf62eb15f01956ab
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769664"
---
# <a name="update-azure-powershell-modules-in-azure-automation"></a>Azure 자동화에서 Azure PowerShell 모듈 업데이트

자동화 계정의 Azure 모듈을 업데이트하려면 오픈 소스로 사용할 수 있는 [Azure 모듈 Runbook 업데이트를](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)사용해야 합니다. **Update-AutomationAzureModulesForAccount** Runbook을 사용하여 Azure 모듈을 업데이트하려면 GitHub의 [Azure 모듈 Runbook 리포지토리 업데이트](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)에서 다운로드합니다. 그런 다음 자동화 계정으로 가져오거나 스크립트로 실행할 수 있습니다. 자동화 계정에서 Runbook을 가져오는 방법을 알아보려면 [runbook 가져오기](manage-runbooks.md#importing-a-runbook)를 참조하십시오.

가장 일반적인 PowerShell 모듈은 기본적으로 각 자동화 계정에서 제공됩니다. Azure 팀에서는 Azure 모듈을 정기적으로 업데이트합니다. 따라서 자동화 계정의 모듈을 최신 상태로 유지하려면 [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) 실행책을 사용해야 합니다.

제품 그룹에 의해 정기적으로 모듈이 업데이트되므로 포함된 cmdlet이 변경될 수 있습니다. 예를 들어 매개 변수의 이름을 바꾸거나 cmdlet을 완전히 사용 하지 않도록 하는 경우 이러한 변경 사항은 Runbook에 부정적인 영향을 줄 수 있습니다.

자동화하는 프로세스 및 Runbook에 영향을 주지 않으려면 계속하기 전에 테스트 및 유효성 검사를 수행합니다. 이 용도로 설계된 전용 Automation 계정이 없는 경우 만들어서 Runbook을 개발하는 동안 다양한 시나리오를 테스트할 수 있습니다. 이 테스트에는 PowerShell 모듈 업데이트와 같은 반복적인 변경 사항이 포함되어야 합니다.

스크립트를 로컬로 개발하는 경우 동일한 결과를 받을 수 있도록 테스트할 때 자동화 계정에 있는 것과 동일한 모듈 버전을 로컬로 두는 것이 좋습니다. 결과의 유효성을 검사하고 필수 변경 내용을 적용한 후에 변경 내용을 프로덕션으로 이동할 수 있습니다.

> [!NOTE]
> 새 자동화 계정에 최신 모듈이 없을 수도 있습니다.

> [!NOTE]
> 자동화가 즉시 제공하는 모듈인 전역 모듈은 삭제할 수 없습니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자의 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치를](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 

## <a name="considerations"></a>고려 사항

다음은 이 문서를 사용하여 Azure 모듈을 업데이트할 때 고려해야 할 몇 가지 고려 사항입니다.

* 이 문서에서 설명하는 Runbook은 기본적으로 Azure, AzureRM 및 Az 모듈 업데이트를 지원합니다. 이 [Runbook을](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) 통해 Az.Automation 모듈 업데이트에 대한 자세한 내용은 Azure 모듈 런북 README 업데이트를 검토하십시오. 자동화 계정에서 Az 모듈을 사용할 때 고려해야 할 중요한 요소가 추가로 있습니다. 자세한 내용은 [Azure 자동화의 모듈 관리를](shared-resources/modules.md)참조하십시오.

* 이 Runbook을 시작하기 전에 Automation 계정에 [Azure 실행 계정 자격 증명](manage-runas-account.md)이 만들어져 있는지 확인합니다.

* 이 코드를 Runbook 대신 일반 PowerShell 스크립트로 사용할 수 있습니다. [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) `-Login $false`

* 소버린 클라우드에서 이 Runbook을 사용하려면 `AzEnvironment` 매개 변수를 사용하여 올바른 환경을 Runbook에 전달합니다.  허용 가능한 값은 AzureCloud(Azure 퍼블릭 클라우드), AzureChinaCloud, AzureGermanCloud 및 AzureUSGovernment입니다. 이러한 값은 을 `Get-AzEnvironment | select Name`사용하여 검색할 수 있습니다. 이 cmdlet에 값을 전달하지 않으면 Runbook이 기본적으로 AzureCloud로 설정됩니다.

* PowerShell 갤러리에서 사용할 수 있는 최신 모듈 대신 특정 Azure PowerShell 모듈 버전을 사용하려는 `ModuleVersionOverrides` 경우 이러한 버전을 **업데이트-AutomationAzureModulesFor계정** 런북의 선택적 매개 변수로 전달합니다. 예제는 [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) Runbook을 참조하세요. `ModuleVersionOverrides` 매개 변수에 언급되지 않은 Azure PowerShell 모듈은 PowerShell 갤러리의 최신 모듈 버전으로 업데이트됩니다. `ModuleVersionOverrides` 매개 변수에 아무 것도 전달하지 않으면 모든 모듈이 PowerShell 갤러리의 최신 모듈 버전으로 업데이트됩니다. 이 동작은 **Azure 모듈 업데이트** 단추와 동일합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 오픈 소스 [Azure 모듈 Runbook 업데이트](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)를 참조하세요.
