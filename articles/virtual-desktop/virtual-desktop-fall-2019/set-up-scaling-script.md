---
title: 세션 호스트 Azure Automation 크기 조정 - Azure
description: Azure Automation을 사용하여 Windows Virtual Desktop 세션 호스트 크기를 자동으로 조정하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f94852a99f0bc430ac193b9951de607cdd7fa933
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85362546"
---
# <a name="scale-session-hosts-using-azure-automation"></a>Azure Automation을 사용하여 세션 호스트 크기 조정

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 지원하지 않는 2019년 가을 릴리스에 적용됩니다.

VM(가상 머신)을 크기 조정하여 총 Windows Virtual Desktop 배포 비용을 줄일 수 있습니다. VM을 크기 조정한다는 것은 사용량이 적은 시간에는 세션 호스트 VM을 종료 및 할당 취소하고 사용량이 많은 시간에는 다시 켜서 할당하는 것을 의미합니다.

이 문서에서는 Azure Automation을 사용하여 빌드된 크기 조정 도구와 Windows Virtual Desktop 환경에서 세션 호스트 가상 머신을 자동으로 크기 조정하는 Azure Logic Apps에 대해 알아봅니다. 크기 조정 도구를 사용하는 방법을 알아보려면 [사전 요구 사항](#prerequisites)으로 건너뛰세요.

## <a name="report-issues"></a>문제 보고

크기 조정 도구에 대한 문제 보고서는 현재 Microsoft 지원이 아닌 GitHub에서 처리되고 있습니다. 크기 조정 도구에 문제가 발생할 경우 [RDS GitHub 페이지](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4a-WVD-scaling-logicapps)에서 “4a-WVD-scaling-logicapps” 레이블이 지정된 GitHub 문제를 열어서 보고할 수 있습니다.

## <a name="how-the-scaling-tool-works"></a>크기 조정 도구의 작동 방식

크기 조정 도구는 세션 호스트 VM 비용을 최적화하려는 고객을 위해 저비용 자동화 옵션을 제공합니다.

크기 조정 도구를 사용하여 다음을 수행할 수 있습니다.

- 사용량이 많은 시간과 적은 시간을 기준으로 VM이 시작 및 중지되도록 예약합니다.
- CPU 코어당 세션 수를 기준으로 VM을 스케일 아웃합니다.
- 사용량이 적은 시간에는 VM을 스케일 인하여 실행 중인 세션 호스트 VM의 수를 최소로 유지합니다.

크기 조정 도구는 Azure Automation PowerShell Runbook, 웹후크 및 Azure Logic Apps를 사용하여 작동합니다. 도구가 실행되면 Azure Logic Apps가 웹후크를 호출하여 Azure Automation Runbook을 시작합니다. 그런 다음 Runbook이 작업을 만듭니다.

사용량이 많은 시간에는 이 작업이 각 호스트 풀에 대해 현재 세션 수와 현재 실행 중인 세션 호스트의 VM 용량을 확인합니다. 작업은 이 정보를 사용하여 실행 중인 세션 호스트 VM이 **createazurelogicapp.ps1** 파일에 대해 정의된 *SessionThresholdPerCPU* 매개 변수를 기준으로 기존 세션을 지원할 수 있는지 여부를 계산합니다. 세션 호스트 VM이 기존 세션을 지원할 수 없는 경우 작업은 호스트 풀에서 추가 세션 호스트 VM을 시작합니다.

>[!NOTE]
>*SessionThresholdPerCPU*는 VM의 세션 수를 제한하지 않습니다. 이 매개 변수는 연결의 부하를 분산하기 위해 새 VM을 시작해야 하는 시점만 결정합니다. 세션 수를 제한하려면 [Set-RdsHostPool](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) 지침에 따라 *MaxSessionLimit* 매개 변수를 적절하게 구성해야 합니다.

사용량이 적은 시간에는 이 작업이 *MinimumNumberOfRDSH* 매개 변수를 기준으로 어느 세션 호스트 VM을 종료해야 하는지 결정합니다. 작업은 새 세션이 호스트에 연결하지 못하도록 세션 호스트 VM을 드레이닝 모드로 설정합니다. *LimitSecondsToForceLogOffUser* 매개 변수를 0이 아닌 값으로 설정할 경우 작업은 현재 로그인한 사용자에게 작업을 저장하라고 알리고 구성된 시간 동안 기다린 후 사용자를 강제로 로그아웃합니다. 세션 호스트 VM의 모든 사용자 세션이 로그아웃되면 작업이 VM을 종료합니다.

*LimitSecondsToForceLogOffUser* 매개 변수를 0으로 설정할 경우 작업은 지정된 그룹 정책의 세션 구성 설정이 사용자 세션 로그아웃을 처리하도록 허용합니다. 이러한 그룹 정책을 보려면 **컴퓨터 구성** > **정책** > **관리 템플릿** > **Windows 구성 요소** > **터미널 서비스** > **터미널 서버** > **세션 시간 제한**으로 이동합니다. 세션 호스트 VM에 활성 세션이 있으면 작업이 세션 호스트 VM을 실행 상태로 유지합니다. 활성 세션이 없으면 작업이 세션 호스트 VM을 종료합니다.

작업은 설정된 되풀이 간격에 따라 주기적으로 실행됩니다. Windows Virtual Desktop 환경의 크기에 따라 이 간격을 변경할 수 있지만, 가상 머신을 시작하고 종료하는 데 다소 시간이 걸릴 수 있으므로 지연 시간을 고려해야 합니다. 되풀이 간격은 15분으로 설정하는 것이 좋습니다.

단, 이 도구에는 다음과 같은 제한 사항도 있습니다.

- 이 솔루션은 풀링된 세션 호스트 VM에만 적용됩니다.
- 이 솔루션은 모든 지역의 VM을 관리하지만 Azure Automation 계정 및 Azure Logic Apps와 동일한 구독에서만 사용할 수 있습니다.

>[!NOTE]
>크기 조정 도구는 도구가 크기를 조정하는 대상 호스트 풀의 부하 분산 모드를 제어합니다. 도구는 사용량이 많은 시간과 적은 시간 모두에 대해 호스트 풀을 폭 우선 부하 분산으로 설정합니다.

## <a name="prerequisites"></a>사전 요구 사항

크기 조정 도구를 설정하기 전에 먼저 다음을 준비해야 합니다.

- [Windows Virtual Desktop 테넌트 및 호스트 풀](create-host-pools-arm-template.md)
- Windows Virtual Desktop 서비스를 사용하여 구성 및 등록된 세션 호스트 풀 VM
- Azure 구독에 대한 [기여자 액세스](../../role-based-access-control/role-assignments-portal.md)를 갖는 사용자

도구를 배포하는 데 사용할 머신에는 다음이 필요합니다.

- Windows PowerShell 5.1 이상
- Microsoft Az PowerShell 모듈

모두 준비됐으면 이제 시작하겠습니다.

## <a name="create-an-azure-automation-account"></a>Azure Automation 계정 만들기

먼저 PowerShell Runbook을 실행할 Azure Automation 계정이 필요합니다. 계정을 설정하는 방법은 다음과 같습니다.

1. 관리자 권한으로 Windows PowerShell을 엽니다.
2. 다음 cmdlet을 실행하여 Azure 계정에 로그인합니다.

     ```powershell
     Login-AzAccount
     ```

     >[!NOTE]
     >계정에는 크기 조정 도구를 배포할 Azure 구독에 대한 기여자 권한이 있어야 합니다.

3. 다음 cmdlet을 실행하여 Azure Automation 계정을 만드는 스크립트를 다운로드합니다.

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazureautomationaccount.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazureautomationaccount.ps1"
     ```

4. 다음 cmdlet을 실행하여 스크립트를 실행하고 Azure Automation 계정을 만듭니다.

     ```powershell
     .\createazureautomationaccount.ps1 -SubscriptionID <azuresubscriptionid> -ResourceGroupName <resourcegroupname> -AutomationAccountName <name of automation account> -Location "Azure region for deployment"
     ```

5. cmdlet의 출력에 웹후크 URI가 포함됩니다. URI를 기록해 두세요. 이 값은 Azure Logic Apps에 대해 실행 일정을 설정할 때 매개 변수로 사용합니다.

6. Azure Automation 계정을 설정한 후에는 Azure 구독에 로그인하고 다음 그림과 같이 Azure Automation 계정 및 관련 Runbook이 지정된 리소스 그룹에 표시되는지 확인합니다.

> [!div class="mx-imgBorder"]
> ![새로 만든 Automation 계정 및 Runbook을 보여 주는 Azure 개요 페이지 이미지.](../media/automation-account.png)

  웹후크가 제자리에 있는지 확인하려면 Runbook의 이름을 선택합니다. 그런 다음 Runbook의 리소스 섹션으로 이동하여 **웹후크**를 선택합니다.

## <a name="create-an-azure-automation-run-as-account"></a>Azure Automation 실행 계정 만들기

Azure Automation 계정을 만들었으니 이번에는 Azure 리소스에 액세스하는 데 사용할 Azure Automation 실행 계정을 만들어야 합니다.

[Azure Automation의 실행 계정](../../automation/manage-runas-account.md)은 Azure cmdlet을 사용하여 Azure의 리소스를 관리하는 데 필요한 인증을 제공합니다. 실행 계정을 만들면 Azure Active Directory에 새로운 서비스 주체 사용자가 생성되며 구독 수준에서 이 사용자에게 기여자 역할을 할당합니다. Azure 실행 계정은 자격 증명 개체에 사용자 이름과 암호를 저장하지 않고도 인증서와 서비스 사용자 이름을 사용하여 안전하게 인증하는 좋은 방법입니다. 실행 인증에 대한 자세한 내용은 [실행 계정 권한 제한](../../automation/manage-runas-account.md#limit-run-as-account-permissions)을 참조하세요.

구독 관리자 역할의 구성원이고 구독의 공동 관리자인 사용자는 다음 섹션의 지침에 따라 실행 계정을 만들 수 있습니다.

Azure 계정에서 실행 계정을 만들려면 다음을 수행합니다.

1. Azure Portal에서 **모든 서비스**를 선택합니다. 리소스 목록에서 **Automation 계정**을 입력하고 선택합니다.

2. **Automation 계정** 페이지에서 Automation 계정의 이름을 선택합니다.

3. 창의 왼쪽 창에서 계정 설정 섹션 아래에 있는 **실행 계정**을 선택합니다.

4. **Azure 실행 계정**을 선택합니다. **Azure 실행 계정 추가** 창이 나타나면 개요 정보를 검토한 다음 **만들기**를 선택하여 계정 만들기 프로세스를 시작합니다.

5. Azure가 실행 계정을 만드는 동안 몇 분 정도 기다립니다. 알림 아래에 있는 메뉴에서 만들기 진행률을 추적할 수 있습니다.

6. 프로세스가 완료되면 지정된 Automation 계정에 AzureRunAsConnection이라는 자산이 생성됩니다. 이 연결 자산은 애플리케이션 ID, 테넌트 ID, 구독 ID 및 인증서 지문을 보유합니다. 나중에 사용할 수 있도록 애플리케이션 ID를 기록해 두세요.

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Windows Virtual Desktop에서 역할 할당 수행하기

다음으로, AzureRunAsConnection이 Windows Virtual Desktop과 상호 작용할 수 있도록 역할 할당을 만들어야 합니다. PowerShell을 사용하여, 역할 할당을 만들 수 있는 권한이 있는 계정에 로그인합니다.

먼저 PowerShell 세션에서 사용할 [Windows Virtual Desktop PowerShell 모듈](/powershell/windows-virtual-desktop/overview/)을 다운로드하고 가져옵니다. 다음 PowerShell cmdlet을 실행하여 Windows Virtual Desktop에 연결하고 테넌트를 표시합니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

Get-RdsTenant
```

크기를 조정하려는 호스트 풀이 있는 테넌트를 찾았으면 [Azure Automation 계정 만들기](#create-an-azure-automation-account)의 지침에 따라 이전 cmdlet에서 확인한 테넌트 이름을 다음 cmdlet에 사용하여 역할 할당을 만듭니다.

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId <applicationid> -TenantName <tenantname>
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Azure Logic App 및 실행 일정 만들기

마지막으로, Azure Logic App을 만들고 새 크기 조정 도구의 실행 일정을 설정해야 합니다.

1.  관리자 권한으로 Windows PowerShell을 엽니다.

2.  다음 cmdlet을 실행하여 Azure 계정에 로그인합니다.

     ```powershell
     Login-AzAccount
     ```

3. 다음 cmdlet을 실행하여 로컬 머신에서 ccreateazurelogicapp.ps1 스크립트 파일을 다운로드합니다.

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazurelogicapp.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazurelogicapp.ps1"
     ```

4. 다음 cmdlet을 실행하여 RDS 소유자 또는 RDS 기여자 권한이 있는 계정으로 Windows Virtual Desktop에 로그인합니다.

     ```powershell
     Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
     ```

5. 다음 PowerShell 스크립트를 실행하여 Azure Logic App 및 실행 일정을 만듭니다.

     ```powershell
     $aadTenantId = (Get-AzContext).Tenant.Id

     $azureSubscription = Get-AzSubscription | Out-GridView -PassThru -Title "Select your Azure Subscription"
     Select-AzSubscription -Subscription $azureSubscription.Id
     $subscriptionId = $azureSubscription.Id

     $resourceGroup = Get-AzResourceGroup | Out-GridView -PassThru -Title "Select the resource group for the new Azure Logic App"
     $resourceGroupName = $resourceGroup.ResourceGroupName
     $location = $resourceGroup.Location

     $wvdTenant = Get-RdsTenant | Out-GridView -PassThru -Title "Select your WVD tenant"
     $tenantName = $wvdTenant.TenantName

     $wvdHostpool = Get-RdsHostPool -TenantName $wvdTenant.TenantName | Out-GridView -PassThru -Title "Select the host pool you'd like to scale"
     $hostPoolName = $wvdHostpool.HostPoolName

     $recurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"
     $beginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"
     $endPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"
     $timeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"
     $sessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"
     $minimumNumberOfRdsh = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"
     $limitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, users will be signed out immediately"
     $logOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"
     $logOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

     $automationAccount = Get-AzAutomationAccount -ResourceGroupName $resourceGroup.ResourceGroupName | Out-GridView -PassThru
     $automationAccountName = $automationAccount.AutomationAccountName
     $automationAccountConnection = Get-AzAutomationConnection -ResourceGroupName $resourceGroup.ResourceGroupName -AutomationAccountName $automationAccount.AutomationAccountName | Out-GridView -PassThru -Title "Select the Azure RunAs connection asset"
     $connectionAssetName = $automationAccountConnection.Name

     $webHookURI = Read-Host -Prompt "Enter the URI of the WebHook returned by when you created the Azure Automation Account"
     $maintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don't want to be managed by this scaling tool"

     .\createazurelogicapp.ps1 -ResourceGroupName $resourceGroupName `
       -AADTenantID $aadTenantId `
       -SubscriptionID $subscriptionId `
       -TenantName $tenantName `
       -HostPoolName $hostPoolName `
       -RecurrenceInterval $recurrenceInterval `
       -BeginPeakTime $beginPeakTime `
       -EndPeakTime $endPeakTime `
       -TimeDifference $timeDifference `
       -SessionThresholdPerCPU $sessionThresholdPerCPU `
       -MinimumNumberOfRDSH $minimumNumberOfRdsh `
       -LimitSecondsToForceLogOffUser $limitSecondsToForceLogOffUser `
       -LogOffMessageTitle $logOffMessageTitle `
       -LogOffMessageBody $logOffMessageBody `
       -Location $location `
       -ConnectionAssetName $connectionAssetName `
       -WebHookURI $webHookURI `
       -AutomationAccountName $automationAccountName `
       -MaintenanceTagName $maintenanceTagName
     ```

     스크립트를 실행하면 다음 그림에서처럼 Logic App이 리소스 그룹에 표시됩니다.

     > [!div class="mx-imgBorder"]
     > ![예제 Azure Logic App의 개요 페이지 이미지.](../media/logic-app.png)

실행 일정을 변경하려면(예: 되풀이 간격 또는 표준 시간대 변경) 자동 크기 조정 스케줄러로 이동하고 **편집**을 선택하여 Logic Apps 디자이너로 이동합니다.

> [!div class="mx-imgBorder"]
> ![Logic Apps 디자이너 이미지. 사용자가 되풀이 시간 및 웹후크 파일을 편집할 수 있는 되풀이 및 웹후크 메뉴가 열립니다.](../media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>크기 조정 도구 관리

크기 조정 도구를 만들었으니 이제 출력에 액세스할 수 있습니다. 이 섹션에서는 몇 가지 유용한 기능에 대해 설명합니다.

### <a name="view-job-status"></a>작업 상태 보기

모든 Runbook 작업의 요약 상태를 보거나 Azure Portal에서 특정 Runbook 작업의 세부 상태를 볼 수 있습니다.

선택한 Automation 계정 오른쪽의 “작업 통계” 아래에서 모든 Runbook 작업의 요약을 볼 수 있습니다. 창 왼쪽에서 **작업** 페이지를 열면 현재 작업 상태, 시작 시간 및 완료 시간이 표시됩니다.

> [!div class="mx-imgBorder"]
> ![작업 상태 페이지 스크린샷.](../media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>로그 및 크기 조정 도구 출력 보기

Runbook을 열고 작업 이름을 선택하여 스케일 아웃 및 스케일 인 작업의 로그를 볼 수 있습니다.

Azure Automation 계정을 호스트하는 리소스 그룹의 Runbook(기본 이름은 WVDAutoScaleRunbook)으로 이동하여 **개요**를 선택합니다. 다음 그림에서처럼 개요 페이지의 최근 작업 아래에서 작업을 선택하면 크기 조정 도구 출력을 볼 수 있습니다.

> [!div class="mx-imgBorder"]
> ![크기 조정 도구의 출력 창 이미지.](../media/tool-output.png)

