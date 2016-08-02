<properties
   pageTitle="JSON 형식 태그를 사용하여 Azure VM 시작 및 종료 일정 만들기 | Microsoft Azure"
   description="이 문서에서는 태그에 JSON 문자열을 사용하여 VM 시작 및 종료 예약을 자동화하는 방법을 보여 줍니다."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/18/2016"
   ms.author="magoedte;paulomarquesc" />

# Azure 자동화 시나리오 - JSON 형식 태그를 사용하여 Azure VM 시작 및 종료 일정 만들기

일반적으로 고객은 가상 컴퓨터의 시작 및 종료를 예약하여 구독 비용을 절감하거나 비즈니스 및 기술 요구 사항을 지원하는 데 도움을 주려고 합니다.

다음 시나리오에서는 Azure의 리소스 그룹 수준 또는 가상 컴퓨터 수준에서 일정이라는 태그를 사용하여 VM의 자동화된 시작 및 종료를 설정할 수 있습니다. 이 일정은 시작 시간 및 종료 시간을 사용하여 일요일부터 토요일까지 구성할 수 있습니다. 기본적으로 규모를 축소 또는 확장할 수 있도록 하는 자동 크기 조정 설정의 [가상 컴퓨터 크기 집합](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)을 사용하거나 시작 및 종료 작업을 예약하는 기본 제공 기능이 있는 [DevTest Labs](../devtest-lab/devtest-lab-overview.md) 서비스를 사용하는 것과 같은 몇 가지 옵션이 제공되고 있지만 이러한 옵션은 특정 시나리오만 지원하며 IaaS VM에 적용할 수 없습니다.

리소스 그룹에는 Schedule 태그를 적용하는 경우 해당 리소스 그룹 내의 모든 가상 컴퓨터에 해당 태그가 적용됩니다. 일정이 VM에 직접 적용될 경우 마지막 일정이 다음과 같은 순서로 먼저 적용됩니다.

1.  리소스 그룹에 적용된 일정
2.  리소스 그룹 및 리소스 그룹의 가상 컴퓨터에 적용된 일정
3.  가상 컴퓨터에 적용된 일정

이 시나리오에서는 기본적으로 지정된 형식의 JSON 문자열을 가져온 후 Schedule이라는 태그의 값으로 추가합니다. 그러면 Runbook은 모든 리소스 그룹 및 가상 컴퓨터를 나열하고, 위에 나열된 시나리오에 따라 각 VM에 대한 일정을 식별하고, 일정이 연결된 이러한 VM을 반복하면서 수행해야 하는 작업(중지, 종료 또는 무시)을 평가합니다.

이러한 Runbook은 [Azure 실행 계정](../automation/automation-sec-configure-azure-runas-account.md)을 사용하여 인증합니다.

## 시나리오 가져오기

이 시나리오는 이 프로젝트에 대한 [TechNet 갤러리](https://gallery.technet.microsoft.com/Azure-Automation-Runbooks-84f0efc7) 또는 [GitHub](https://github.com/paulomarquesdacosta/azure-automation-scheduled-shutdown-and-startup) 저장소에서 다운로드할 수 있는 4개의 PowerShell 워크플로 Runbook으로 구성되어 있습니다.

Runbook | 설명 
----------|----------
Test-ResourceSchedule | 각 가상 컴퓨터의 일정을 확인합니다(일정에 따라 가상 컴퓨터의 종료 또는 시작 수행).
Add-ResourceSchedule | Schedule 태그를 VM 또는 리소스 그룹에 추가
Update-ResourceSchedule | 기존 Schedule 태그를 새 태그로 바꾸어 수정
Remove-ResourceSchedule | VM 또는 리소스 그룹에서 Schedule 태그 제거 


## 이 시나리오 설치 및 구성

### Runbook 설치 및 게시

Runbook을 다운로드한 후에 [Runbook 가져오기 절차](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-Azure-Automation)의 절차를 사용하여 Runbook을 가져올 수 있습니다. 각 Runbook을 자동화 계정으로 가져온 후에는 Runbook을 게시합니다.


### Test-ResourceSchedule에 일정 추가

다음 단계에 따라 Test-ResourceSchedule Runbook에 대한 일정을 사용하도록 설정합니다. 가상 컴퓨터를 시작 또는 종료할지 아니면 그대로 둘지를 테스트하는 Runbook입니다.

1. Azure 포털에서 자동화 계정을 열고 **Runbook** 타일을 클릭합니다.
2. **Test-ResourceSchedule** 블레이드에서 **일정** 타일을 클릭합니다.
3. **일정** 블레이드에서 **일정 추가**를 클릭합니다.
4. **일정** 블레이드에서 **Runbook에 일정 연결**을 선택하고 **일정** 블레이드에서 **새 일정 만들기**를 선택합니다.
5.  **새 일정** 블레이드에서 이 일정의 이름을 입력합니다. 예: HourlyExecution
6. 일정 **시작** 일정에서 반올림한 시간 증분으로 설정합니다.
7. **되풀이**를 선택하고 **되풀이 간격**에서 **1시간**을 선택합니다.
8. **만료 설정**이 **아니요**로 설정되어 있는지 확인한 후 **만들기**를 클릭하여 새 일정을 저장합니다.
9. **일정 Runbook** 옵션 블레이드에서 **매개 변수 및 실행 설정** 옵션을 선택하고 Test-ResourceSchedule **매개 변수** 블레이드의 **SubscriptionName** 필드에 구독의 이름을 입력합니다. Runbook에는 이 매개 변수만 있으면 됩니다. 완료되면 **확인**을 클릭합니다.
   

완료된 Runbook 일정은 다음과 같이 표시됩니다.<br> ![구성된 Test-ResourceSchedule Runbook](./media/automation-scenario-start-stop-vm-wjson-tags/automation-schedule-config.png)<br>

## 일정 JSON 형식

이 솔루션은 기본적으로 지정된 형식의 JSON 문자열을 가져온 후 Schedule이라는 태그의 값으로 추가합니다. 그러면 Runbook은 모든 리소스 그룹 및 가상 컴퓨터를 나열하고, 각 가상 컴퓨터에 대한 일정을 식별하고, 일정이 연결된 해당 가상 컴퓨터를 반복하면서 수행할 작업을 확인합니다. 다음은 형식 지정 방법의 예입니다.

    {
       "TzId": "Eastern Standard Time", 
        "0": {  
           "S": "11",
           "E": "17" 
        },
        "1": {
           "S": "9",
           "E": "19"
        },
        "2": {
           "S": "9",
           "E": "19"
        },
    }

이 구조에 대한 자세한 정보:

1. 이 JSON 구조의 형식은 Azure의 단일 태그 값에 대한 256자 제한을 해결하도록 최적화되어 있습니다.

2. *TzId*는 가상 컴퓨터의 표준 시간대를 나타냅니다. 이 ID는 PowerShell 세션에서 TimeZoneInfo .NET 클래스를 사용하여 구할 수 있습니다. **[System.TimeZoneInfo]::GetSystemTimeZones()**.<br>

    ![PowerShell의 GetSystemTimeZones](./media/automation-scenario-start-stop-vm-wjson-tags/automation-get-timzone-powershell.png)

    - 요일 0에서 6의 숫자 값으로 표시됩니다. 값 0은 일요일을 나타냅니다.
    - 시작 시간은 **S** 특성으로 표시되고 해당 값은 24시간 형식입니다.
    - 끝 또는 종료 시간은 **E** 특성으로 표시되고 해당 값은 24시간 형식입니다.

    S 및 E 특성 값이 0인 경우 가상 시스템은 평가 시의 현재 상태를 유지합니다.

3. 특정 요일에 대한 평가를 생략하려면 관련 요일의 섹션을 추가하지 마세요. 다음 예제에서는 월요일만 평가되고 다른 요일은 무시됩니다.
   
        {
          "TzId": "Eastern Standard Time",
           "1": {
             "S": "11",
             "E": "17"
           }
        }

## 리소스 그룹 또는 VM 태그 지정

VM을 종료하려면 해당 VM 또는 해당 VM이 있는 리소스 그룹에 태그를 지정해야 합니다. Schedule 태그가 없는 가상 컴퓨터는 평가되지 않으므로 시작되지도, 종료되지도 않습니다. 이 솔루션에서는 포털에서 직접 수행하거나 **Add-ResourceSchedule**, **Add-ResourceSchedule** 및 **Remove-ResourceSchedule** Runbook을 사용하는 두 가지 방식으로 리소스 그룹 또는 VM에 태그를 지정할 수 있습니다.

### 포털을 통해 태그 지정

다음은 포털에서 가상 컴퓨터 또는 리소스 그룹에 태그를 지정하는 작업 단계입니다.

1. JSON 문자열을 평면화하고 공백이 없는지 확인합니다. JSON 문자열은 다음과 같아야 합니다.

        {"TzId":"Eastern Standard Time","0":{"S":"11","E":"17"},"1":{"S":"9","E":"19"},"2": {"S":"9","E":"19"},"3":{"S":"9","E":"19"},"4":{"S":"9","E":"19"},"5":{"S":"9","E":"19"},"6":{"S":"11","E":"17"}}
   

2. 태그 아이콘을 선택하여 이 일정을 적용할 VM 또는 리소스 그룹을 선택합니다.<br>![VM 태그 옵션](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-tag-option.png)
3. 태그는 다음 키/값 쌍으로 정의됩니다. **키** 필드에 **일정**을 입력하고 JSON 문자열을 **값** 필드에 붙여넣은 후 **저장**을 클릭합니다. 이제 리소스에 대한 태그 목록에 새 태그가 나타납니다.<br>![VM Schedule 태그](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-schedule-tag.png)


### PowerShell에서 태그 지정

가져온 모든 Runbook의 스크립트 맨 처음에는 PowerShell에서 직접 Runbook을 실행하는 방법을 설명하기 위한 도움말 정보가 포함되어 있습니다. Add-ScheduleResource 및 Update-ScheduleResource Runbook은 포털 외부에서 VM 또는 리소스 그룹에 대해 Schedule 태그를 만들거나 업데이트할 수 있도록 하는 필수 매개 변수를 제공하여 PowerShell에서 호출할 수 있습니다.

PowerShell을 통해 태그를 만들고 추가 및 삭제하려면 먼저 [Azure에 맞게 PowerShell 환경](../powershell-install-configure.md)을 설정해야 합니다. 설정이 끝나면 다음 단계를 진행할 수 있습니다.

### PowerShell을 사용하여 Schedule 태그 만들기

1. PowerShell 세션을 열고 다음을 실행하여 실행 계정으로 인증을 받은 후 구독을 지정합니다.
    
        Conn = Get-AutomationConnection -Name AzureRunAsConnection
        Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
        -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
        Select-AzureRmSubscription -SubscriptionName "MySubscription"
   
2. 일정 해시 테이블을 정의합니다. 다음은 생성 방법의 예입니다.
    
        $schedule= @{ "TzId"="Eastern Standard Time"; "0"= @{"S"="11";"E"="17"};"1"= @{"S"="9";"E"="19"};"2"= @{"S"="9";"E"="19"};"3"= @{"S"="9";"E"="19"};"4"= @{"S"="9";"E"="19"};"5"= @{"S"="9";"E"="19"};"6"= @{"S"="11";"E"="17"}}

3. Runbook에 필요한 매개 변수를 정의합니다. 다음 예제에서는 VM을 대상으로 합니다.

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; `
        "VmName"="VM01";"Schedule"=$schedule}

    리소스 그룹에 태그를 지정하려는 경우 다음과 같이 $params 해시 테이블에서 *VMName* 매개 변수를 제거하면 됩니다.

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; `
        "Schedule"=$schedule}

4. 다음 매개 변수로 **Add-ResourceSchedule** Runbook을 실행하여 Schedule 태그를 만듭니다.

        Start-AzureRmAutomationRunbook -Name "Add-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"

5. 리소스 그룹 또는 가상 컴퓨터 태그를 업데이트하려는 경우 다음 매개 변수로 **Update-ResourceSchedule** Runbook을 실행합니다.

        Start-AzureRmAutomationRunbook -Name "Update-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"

### PowerShell을 사용하여 Schedule 태그 제거

1. PowerShell 세션을 열고 다음을 실행하여 실행 계정으로 인증을 받은 후 구독을 선택하고 지정합니다.
    
        Conn = Get-AutomationConnection -Name AzureRunAsConnection
        Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
        -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
        Select-AzureRmSubscription -SubscriptionName "MySubscription"

2. Runbook에 필요한 매개 변수를 정의합니다. 다음 예제에서는 VM을 대상으로 합니다.

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01" ` 
        ;"VmName"="VM01"}

    리소스 그룹에서 태그를 제거하려는 경우 다음과 같이 $params 해시 테이블에서 *VMName* 매개 변수를 제거하면 됩니다.

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"}

3. **Remove-ResourceSchedule** Runbook을 실행하여 Schedule 태그를 제거합니다.

        Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"

4. 리소스 그룹 또는 가상 컴퓨터 태그를 업데이트하려는 경우 다음 매개 변수로 **Remove-ResourceSchedule** Runbook을 실행합니다.

        Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"


>[AZURE.NOTE] 이러한 Runbook(및 가상 컴퓨터 상태)을 사전에 모니터링하여 가상 컴퓨터가 적절히 종료 및 시작되고 있는지 확인하는 것이 좋습니다.

Runbook의 작업 타일을 선택하여 Azure 포털에서 **Test-ResourceSchedule** Runbook 작업의 세부 정보를 볼 수 있습니다. 작업 요약은 작업 및 발생하는 모든 예외에 대한 일반 정보 외에도 입력 매개 변수 및 출력 스트림을 표시합니다. 기록에는 출력 스트림과 경고 및 오류 스트림의 메시지가 포함됩니다. Runbook 실행의 자세한 결과를 보려면 출력 타일을 선택합니다.<br> ![Test-ResourceSchedule 출력](./media/automation-scenario-start-stop-vm-wjson-tags/automation-job-output.png)

## 다음 단계

-  PowerShell 워크플로 Runbook을 시작하려면 [내 첫 번째 PowerShell 워크플로 Runbook](automation-first-runbook-textual.md)을 참조하세요.
-  Runbook의 형식, 장점 및 제한 사항에 대해 자세히 알아보려면 [Azure 자동화 Runbook 형식](automation-runbook-types.md)을 참조하세요.
-  PowerShell 스크립트 지원 기능에 대한 자세한 내용은 [Azure 자동화에서 네이티브 PowerShell 스크립트 지원](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)을 참조하세요.
-  Runbook 로깅 및 출력에 대한 자세한 내용은 [Azure 자동화에서 Runbook 출력 및 메시지](automation-runbook-output-and-messages.md)를 참조하세요.
-  Azure 실행 계정 및 이 계정을 사용하여 Runbook을 인증하는 방법에 대한 자세한 내용은 [Azure 실행 계정으로 Runbook 인증](../automation/automation-sec-configure-azure-runas-account.md)을 참조하세요.

<!---HONumber=AcomDC_0720_2016-->