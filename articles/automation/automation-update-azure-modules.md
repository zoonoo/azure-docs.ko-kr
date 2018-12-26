---
title: Azure Automation에서 Azure 모듈 업데이트
description: 이 문서에서는 Azure Automation에 기본적으로 제공되는 일반 Azure PowerShell 모듈을 즉시 업데이트하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 11/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9fc605ab45241280d9331ad7d515ba007a015daa
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52583656"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법

기본적으로 각 Automation 계정에 가장 일반적인 Azure PowerShell 모듈이 제공됩니다. Azure 팀에서는 Azure 모듈을 정기적으로 업데이트합니다. 포털에서 새 버전을 사용할 수 있으면 Automation 계정에서 모듈을 업데이트하는 방법이 제공됩니다.

제품 그룹에 의해 정기적으로 모듈이 업데이트되므로 포함된 cmdlet이 변경될 수 있습니다. 이 작업은 매개 변수 이름을 바꾸거나 cmdlet을 완전히 중단하는 등 변경 형식에 따라 Runbook에 부정적인 영향을 줄 수 있습니다. 자동화하는 프로세스 및 Runbook에 영향을 주지 않으려면 계속하기 전에 테스트 및 유효성 검사를 수행합니다. 이 용도로 설계된 전용 Automation 계정이 없는 경우 만들어서 Runbook을 개발하는 동안 다양한 시나리오를 테스트할 수 있습니다. 이 테스트에는 PowerShell 모듈을 업데이트하는 등 반복적인 변경 내용도 포함되어야 합니다. 결과의 유효성을 검사하고 필수 변경 내용을 적용한 후에 변경 내용을 프로덕션으로 이동할 수 있습니다.

> [!NOTE]
> 새 자동화 계정에 최신 모듈이 없을 수도 있습니다.

## <a name="updating-azure-modules"></a>Azure 모듈 업데이트

1. Automation 계정의 모듈 계정 페이지에는 **Azure 모듈 업데이트**라는 옵션이 있습니다. 이 옵션은 항상 사용됩니다.<br><br> ![모듈 페이지의 Azure 모듈 업데이트 옵션](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

  > [!NOTE]
  > Azure 모듈을 업데이트하기 전에 테스트 Automation 계정에서 업데이트하여 Azure 모듈을 업데이트하기 전에 기존 스크립트가 예상대로 작동하는지 확인하는 것이 좋습니다.
  >
  > **Azure 모듈 업데이트** 단추는 공용 클라우드에서만 사용할 수 있으며, [소버린 지역](https://azure.microsoft.com/global-infrastructure/)에서는 사용할 수 없습니다. 자세한 내용은 [모듈을 업데이트하는 다른 방법](#alternative-ways-to-update-your-modules) 섹션을 참조하세요.


2. **Azure 모듈 업데이트**를 클릭하면 계속 진행할 것인지 묻는 확인 알림이 나타납니다.<br><br> ![Azure 모듈 업데이트 알림](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. **예**를 클릭하면 모듈 업데이트 프로세스가 시작됩니다. 업데이트 프로세스에서 다음 모듈을 업데이트하는 데 15-20분 정도 걸립니다.

  * Azure
  * Azure.Storage
  * AzureRm.Automation
  * AzureRm.Compute
  * AzureRm.Profile
  * AzureRm.Resources
  * AzureRm.Sql
  * AzureRm.Storage

    모듈이 이미 최신 상태이면 프로세스가 몇 초 이내에 완료됩니다. 업데이트 프로세스가 완료되면 알림이 표시됩니다.<br><br> ![Azure 모듈 업데이트 상태 업데이트](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

    .NET Core AzureRm 모듈(AzureRm.*.Core)은 Azure Automation에서 지원되지 않으므로 가져올 수 없습니다.

> [!NOTE]
> Azure Automation은 예약된 새 작업이 실행될 때 Automation 계정의 최신 모듈을 사용합니다.  

Runbook에서 이러한 Azure PowerShell 모듈의 cmdlet을 사용하는 경우 최신 모듈을 사용하도록 매달 이 업데이트 프로세스를 수행하는 것이 좋습니다. Azure Automation은 `AzureRunAsConnection` 연결을 사용하여 모듈을 업데이트할 때 인증합니다. 서비스 주체가 만료되거나 구독 수준에서 더 이상 존재하지 않는 경우 모듈 업데이트에 실패합니다.

## <a name="alternative-ways-to-update-your-modules"></a>모듈을 업데이트하는 다른 방법

앞에서 언급한 대로 **Azure 모듈 업데이트** 단추는 소버린 클라우드에서 사용할 수 없으며, 글로벌 Azure 클라우드에서만 사용할 수 있습니다. 이는 PowerShell 갤러리에 있는 최신 버전의 Azure PowerShell 모듈이 현재 이러한 클라우드에 배포된 Resource Manager 리소스에서 작동하지 않을 수 있기 때문입니다.

[Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) Runbook을 가져오고 실행하여 Automation 계정에서 Azure 모듈을 업데이트하려고 시도할 수 있습니다. 갤러리에서 가져오려는 버전이 현재 대상 Azure 환경에 배포된 Azure 서비스와 호환되지 않는 경우 이 프로세스에 실패할 수 있습니다. 그러려면 Runbook 매개 변수에서 지정된 모듈의 호환 가능 버전을 확인해야 합니다.

Runbook에 올바른 환경을 전달하려면 `AzureRmEnvironment` 매개 변수를 사용합니다.  허용되는 값은 **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud** 및 **AzureUSGovernment**입니다. `Get-AzureRmEnvironment | select Name`을 사용하여 이러한 값을 가져올 수 있습니다. 이 매개 변수에 값을 전달하지 않으면 Runbook이 기본적으로 **AzureCloud** Azure 공용 클라우드로 설정됩니다.

PowerShell 갤러리에서 사용할 수 있는 최신 버전 대신 특정 Azure PowerShell 모듈 버전을 사용하려면 이러한 버전을 **Update-AzureModule** Runbook의 선택적 `ModuleVersionOverrides` 매개 변수로 전달합니다. 예를 들어 [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) Runbook을 참조하세요. `ModuleVersionOverrides` 매개 변수에 언급되지 않은 Azure PowerShell 모듈은 PowerShell 갤러리의 최신 모듈 버전으로 업데이트됩니다. `ModuleVersionOverrides` 매개 변수에 아무 것도 전달하지 않으면 모든 모듈이 PowerShell 갤러리의 최신 모듈 버전으로 업데이트됩니다. 이 동작은 **Azure 모듈 업데이트** 단추와 동일합니다.

## <a name="next-steps"></a>다음 단계

* 통합 모듈에 대해 알아보고 Automation을 다른 시스템, 서비스 또는 솔루션과 통합하는 사용자 지정 모듈을 만드는 방법을 알아보려면 [통합 모듈](automation-integration-modules.md)을 참조하세요.

