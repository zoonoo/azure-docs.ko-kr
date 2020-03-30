---
title: 확장 세션 호스트 Azure 자동화 - Azure
description: Azure 자동화를 사용하여 Windows 가상 데스크톱 세션 호스트의 크기를 자동으로 조정하는 방법
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3a853dc32f8716f3f2ba32896a7a4a239efcc5bd
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349875"
---
# <a name="scale-session-hosts-using-azure-automation"></a>Azure 자동화를 사용하여 세션 호스트 확장

가상 컴퓨터(VM)를 확장하여 총 Windows 가상 데스크톱 배포 비용을 줄일 수 있습니다. 즉, 사용량이 많은 사용량이 많은 시간 동안 세션 호스트 VM을 종료하고 할당 해제한 다음 다시 켜고 사용량이 많은 시간에 다시 재할당합니다.

이 문서에서는 Windows 가상 데스크톱 환경에서 세션 호스트 가상 컴퓨터를 자동으로 확장하는 Azure 자동화 및 Azure Logic Apps로 빌드된 확장 도구에 대해 알아봅니다. 크기 조정 도구를 사용하는 방법을 알아보려면 [필수 구성 함으로](#prerequisites)건너뛰십시오.

## <a name="how-the-scaling-tool-works"></a>배율 조정 도구의 작동 방식

확장 도구는 세션 호스트 VM 비용을 최적화하려는 고객을 위해 저렴한 자동화 옵션을 제공합니다.

크기 조정 도구를 사용하여 다음을 수행할 수 있습니다.
 
- 피크 및 사용량이 많은 영업 시간을 기준으로 VM을 시작하고 중지하도록 예약합니다.
- CPU 코어당 세션 수에 따라 VM을 확장합니다.
- 사용량이 적은 시간 동안 VM을 확장하여 최소 세션 호스트 VM이 실행됩니다.

크기 조정 도구는 Azure 자동화 PowerShell 런북, 웹후크 및 Azure 논리 앱의 조합을 사용하여 작동합니다. 도구가 실행되면 Azure Logic Apps는 웹후크를 호출하여 Azure 자동화 실행책을 시작합니다. 그런 다음 Runbook에서 작업을 만듭니다.

사용량이 가장 많은 시간 동안 작업은 각 호스트 풀에 대해 현재 실행 중인 세션 호스트의 현재 세션 수와 VM 용량을 확인합니다. 이 정보를 사용하여 실행 중인 세션 호스트 VM이 **createazurelogicapp.ps1** 파일에 대해 정의된 *SessionThresholdPerCPU* 매개 변수를 기반으로 기존 세션을 지원할 수 있는지 계산합니다. 세션 호스트 VM이 기존 세션을 지원할 수 없는 경우 작업은 호스트 풀에서 추가 세션 호스트 VM을 시작합니다.

>[!NOTE]
>*세션임계값PerCPU는* VM의 세션 수를 제한하지 않습니다. 이 매개 변수는 연결의 부하 분산을 위해 새 VM을 시작해야 하는 경우에만 결정됩니다. 세션 수를 제한하려면 [Set-RdsHostPool](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) 지침에 따라 *MaxSessionLimit* 매개 변수를 적절하게 구성해야 합니다.

사용량이 적은 사용 시간 동안 작업은 *MinimumNumberOfRDSH* 매개 변수를 기반으로 종료해야 하는 세션 호스트 VM을 결정합니다. 이 작업은 세션 호스트 VM을 드레인 모드로 설정하여 호스트에 연결하는 새 세션을 방지합니다. *LimitSecondsToForceLogOffUser* 매개 변수를 0이 아닌 양수 값으로 설정하면 작업이 현재 로그인한 사용자에게 작업을 저장하고 구성된 시간을 기다린 다음 강제로 로그아웃하도록 알립니다. 세션 호스트 VM의 모든 사용자 세션이 로그아웃되면 작업이 VM을 종료합니다.

*LimitSecondsToForceLogUser* 매개 변수를 0으로 설정하면 작업이 지정된 그룹 정책의 세션 구성 설정을 사용하여 사용자 세션 서명을 처리할 수 있도록 합니다. 이러한 그룹 정책을 보려면 **컴퓨터 구성** > **정책** > **관리 템플릿** > **Windows 구성 요소** > **터미널 서비스** > **터미널 서버** > **세션 시간 제한으로**이동합니다. 세션 호스트 VM에 활성 세션이 있는 경우 해당 작업은 세션 호스트 VM을 실행 상태로 둡니다. 활성 세션이 없는 경우 작업이 세션 호스트 VM을 종료합니다.

작업은 설정된 되풀이 간격에 따라 주기적으로 실행됩니다. Windows 가상 데스크톱 환경의 크기에 따라 이 간격을 변경할 수 있지만 가상 컴퓨터를 시작하고 종료하는 데 다소 시간이 걸릴 수 있으므로 지연을 고려해야 합니다. 되풀이 간격을 15분마다 설정하는 것이 좋습니다.

그러나 이 도구에는 다음과 같은 제한 사항이 있습니다.

- 이 솔루션은 풀린 세션 호스트 VM에만 적용됩니다.
- 이 솔루션은 모든 리전에서 VM을 관리하지만 Azure 자동화 계정 및 Azure 논리 앱과 동일한 구독에서만 사용할 수 있습니다.

>[!NOTE]
>크기 조정 도구는 크기 조정하는 호스트 풀의 부하 분산 모드를 제어합니다. 피크 및 사용량이 많은 시간 모두에 대해 폭 우선 부하 밸런싱으로 설정합니다.

## <a name="prerequisites"></a>사전 요구 사항

크기 조정 도구 설정을 시작하기 전에 다음과 같은 작업을 준비해야 합니다.

- [Windows 가상 데스크톱 테넌트 및 호스트 풀](create-host-pools-arm-template.md)
- Windows 가상 데스크톱 서비스로 구성 및 등록된 세션 호스트 풀 VM
- Azure 구독에서 [기여자 액세스 권한이](../role-based-access-control/role-assignments-portal.md) 있는 사용자

도구를 배포하는 데 사용하는 컴퓨터에는 다음이 있어야 합니다. 

- 윈도우 파워쉘 5.1 이상
- 마이크로 소프트 아즈 파워 쉘 모듈

모든 준비가 완료된 경우 시작해 보겠습니다.

## <a name="create-an-azure-automation-account"></a>Azure Automation 계정 만들기

먼저 PowerShell 실행책을 실행하려면 Azure 자동화 계정이 필요합니다. 계정을 설정하는 방법은 다음과 같습니다.

1. 관리자 권한으로 Windows PowerShell을 엽니다.
2. 다음 cmdlet을 실행하여 Azure 계정에 로그인합니다.

     ```powershell
     Login-AzAccount
     ```

     >[!NOTE]
     >계정에 크기 조정 도구를 배포하려는 Azure 구독에 대한 기여자 권한이 있어야 합니다.

3. 다음 cmdlet을 실행하여 Azure 자동화 계정을 만들기 위한 스크립트를 다운로드합니다.

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazureautomationaccount.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazureautomationaccount.ps1"
     ```

4. 다음 cmdlet을 실행하여 스크립트를 실행하고 Azure 자동화 계정을 만듭니다.

     ```powershell
     .\createazureautomationaccount.ps1 -SubscriptionID <azuresubscriptionid> -ResourceGroupName <resourcegroupname> -AutomationAccountName <name of automation account> -Location "Azure region for deployment"
     ```

5. cmdlet의 출력에는 웹후크 URI가 포함됩니다. Azure Logic 앱에 대한 실행 일정을 설정할 때 URI 레코드를 매개 변수로 사용하기 때문에 URI 레코드를 유지해야 합니다.

6. Azure 자동화 계정을 설정한 후 Azure 구독에 로그인하고 다음 이미지와 같이 Azure Automation 계정과 관련 Runbook이 지정된 리소스 그룹에 표시되었는지 확인합니다.

   ![새로 만든 자동화 계정 및 Runbook을 보여 주면 Azure 개요 페이지의 이미지입니다.](media/automation-account.png)

  웹후크가 있어야 할 위치인지 확인하려면 Runbook의 이름을 선택합니다. 그런 다음 Runbook의 리소스 섹션으로 이동하여 **웹후크를 선택합니다.**

## <a name="create-an-azure-automation-run-as-account"></a>Azure 자동화 실행 을 계정으로 만들기

이제 Azure 자동화 계정이 있으므로 Azure 리소스에 액세스하려면 Azure 자동화 실행 계정을 만들어야 합니다.

[Azure 자동화 실행 As 계정은](../automation/manage-runas-account.md) Azure cmdlets를 통해 Azure의 리소스를 관리하기 위한 인증을 제공합니다. Run As 계정을 만들 때 Azure Active Directory에서 새 서비스 주체 사용자를 만들고 구독 수준에서 서비스 주체 사용자에게 기여자 역할을 할당하면 Azure Run As Account는 계정을 안전하게 인증할 수 있는 좋은 방법입니다. 자격 증명 개체에 사용자 이름과 암호를 저장할 필요 없이 인증서 및 서비스 주체 이름입니다. 인증으로 실행에 대해 자세히 알아보려면 [계정으로 실행 권한 제한](../automation/manage-runas-account.md#limiting-run-as-account-permissions)을 참조하십시오.

구독 관리자 역할의 구성원이자 구독의 공동 관리자인 모든 사용자는 다음 섹션의 지침에 따라 Run As 계정을 만들 수 있습니다.

Azure 계정에서 실행 로 계정을 만들려면 다음을 수행합니다.

1. Azure 포털에서 **모든 서비스를**선택합니다. 리소스 목록에서 **자동화 계정을**입력하고 선택합니다.

2. 자동화 **계정** 페이지에서 자동화 계정의 이름을 선택합니다.

3. 창 왼쪽의 창에서 계정 설정 섹션에서 **거래처로 실행을** 선택합니다.

4. **계정으로 Azure 실행을 선택합니다.** Azure **실행 계정으로 추가** 창이 나타나면 개요 정보를 검토한 다음 **만들기를** 선택하여 계정 만들기 프로세스를 시작합니다.

5. Azure에서 실행 로 실행 계정을 만들 때까지 몇 분 동안 기다립니다. 알림 아래의 메뉴에서 생성 진행률을 추적할 수 있습니다.

6. 프로세스가 완료되면 지정된 자동화 계정에서 AzureRunAsConnection라는 자산을 만듭니다. 연결 자산에는 응용 프로그램 ID, 테넌트 ID, 구독 ID 및 인증서 지문이 있습니다. 나중에 사용하므로 응용 프로그램 ID를 기억하십시오.

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Windows Virtual Desktop에서 역할 할당 수행하기

다음으로 AzureRunAsConnection이 Windows 가상 데스크톱과 상호 작용할 수 있도록 역할 할당을 만들어야 합니다. PowerShell을 사용하여 역할 할당을 만들 수 있는 권한이 있는 계정으로 로그인해야 합니다.

먼저 아직 사용하지 않은 경우 PowerShell 세션에서 사용할 [Windows 가상 데스크톱 PowerShell 모듈을](/powershell/windows-virtual-desktop/overview/) 다운로드하여 가져옵니다. 다음 PowerShell cmdlet을 실행하여 Windows Virtual Desktop에 연결하고 테넌트를 표시합니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

Get-RdsTenant
```

확장할 호스트 풀이 있는 테넌트를 찾으면 [Azure Automation 계정 만들기의](#create-an-azure-automation-account) 지침에 따라 다음 cmdlet의 이전 cmdlet에서 얻은 테넌트 이름을 사용하여 역할 할당을 만듭니다.

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId <applicationid> -TenantName <tenantname>
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Azure 논리 앱 및 실행 일정 만들기

마지막으로 Azure Logic 앱을 만들고 새 크기 조정 도구에 대한 실행 일정을 설정해야 합니다.

1.  관리자로 Windows PowerShell 열기

2.  다음 cmdlet을 실행하여 Azure 계정에 로그인합니다.

     ```powershell
     Login-AzAccount
     ```

3. 다음 cmdlet을 실행하여 로컬 컴퓨터에서 createazurelogicapp.ps1 스크립트 파일을 다운로드합니다.

     ```powershell
     Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazurelogicapp.ps1" -OutFile "your local machine path\ createazurelogicapp.ps1"
     ```

4. 다음 cmdlet을 실행하여 RDS 소유자 또는 RDS 기여자 권한이 있는 계정으로 Windows 가상 데스크톱에 로그인합니다.

     ```powershell
     Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
     ```

5. 다음 PowerShell 스크립트를 실행하여 Azure 논리 앱 및 실행 일정을 만듭니다.

     ```powershell
     $resourceGroupName = Read-Host -Prompt "Enter the name of the resource group for the new Azure Logic App"
     
     $aadTenantId = Read-Host -Prompt "Enter your Azure AD tenant ID"

     $subscriptionId = Read-Host -Prompt "Enter your Azure Subscription ID"

     $tenantName = Read-Host -Prompt "Enter the name of your WVD tenant"

     $hostPoolName = Read-Host -Prompt "Enter the name of the host pool you'd like to scale"

     $recurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"

     $beginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"

     $endPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"

     $timeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"

     $sessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"

     $minimumNumberOfRdsh = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"

     $limitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, users will be signed out immediately"

     $logOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"

     $logOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

     $location = Read-Host -Prompt "Enter the name of the Azure region where you will be creating the logic app"

     $connectionAssetName = Read-Host -Prompt "Enter the name of the Azure RunAs connection asset"

     $webHookURI = Read-Host -Prompt "Enter the URI of the WebHook returned by when you created the Azure Automation Account"

     $automationAccountName = Read-Host -Prompt "Enter the name of the Azure Automation Account"

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

     스크립트를 실행한 후 다음 이미지와 같이 논리 앱이 리소스 그룹에 나타납니다.

     ![예제 Azure 논리 앱에 대한 개요 페이지의 이미지입니다.](media/logic-app.png)

되풀이 간격 또는 표준 시간대 를 변경하는 등 실행 일정을 변경하려면 자동 크기 조정 스케줄러로 이동하여 **편집을** 선택하여 논리 앱 디자이너로 이동합니다.

![논리 앱 디자이너의 이미지입니다. 사용자가 되풀이 시간을 편집하고 웹후크 파일을 열 수 있도록 하는 되풀이 및 웹후크 메뉴입니다.](media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>스케일링 도구 관리

크기 조정 도구를 만들었으니 출력에 액세스할 수 있습니다. 이 섹션에서는 도움이 될 수 있는 몇 가지 기능에 대해 설명합니다.

### <a name="view-job-status"></a>작업 상태 보기

모든 Runbook 작업의 요약된 상태를 보거나 Azure Portal에서 특정 Runbook 작업의 자세한 상태를 볼 수 있습니다.

선택한 자동화 계정 오른쪽에서 "작업 통계"에서 모든 Runbook 작업의 요약 목록을 볼 수 있습니다. 창 왼쪽에 **있는 작업** 페이지를 열면 현재 작업 상태, 시작 시간 및 완료 시간이 표시됩니다.

![작업 상태 페이지의 스크린샷입니다.](media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>로그 및 스케일링 도구 출력 보기

Runbook을 열고 작업 이름을 선택하여 확장 및 확장 작업 로그를 볼 수 있습니다.

Azure 자동화 계정을 호스팅하는 리소스 그룹의 Runbook(기본 이름은 WVDAutoScaleRunbook)으로 이동하여 **개요를**선택합니다. 개요 페이지에서 다음 이미지와 같이 최근 작업 에서 작업을 선택하여 배율 조정 도구 출력을 봅니다.

![크기 조정 도구의 출력 창 이미지입니다.](media/tool-output.png)

## <a name="report-issues"></a>문제 보고

크기 조정 도구에 문제가 발생하면 [RDS GitHub 페이지에서](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4a-WVD-scaling-logicapps)보고할 수 있습니다.
