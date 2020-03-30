---
title: 사이트 복구 복구 계획에 Azure 자동화 실행책 추가
description: Azure 사이트 복구를 사용하여 재해 복구를 위해 Azure 자동화를 사용하여 복구 계획을 확장하는 방법을 알아봅니다.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: rajanaki
ms.openlocfilehash: ecfe993a137ca63c84438870ec54ac1e6d6707da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257486"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>복구 계획에 Azure Automation Runbook 추가

이 문서에서는 Azure [사이트 복구](site-recovery-overview.md) 계획을 확장하기 위해 Azure 자동화 실행 책을 통합하는 방법을 설명합니다. 수동 작업이 필요한 기본 작업을 자동화하는 방법과 다중 단계 복구를 한 번의 클릭으로 변환하는 방법을 보여 드리겠습니다.

## <a name="recovery-plans"></a>복구 계획 

온-프레미스 시스템 또는 Azure VM을 장애 조치할 때 복구 계획을 사용할 수 있습니다. 복구 계획을 사용하면 컴퓨터가 장애 조치(failover) 후 시작하고 복구하는 방법과 장애 조치(failover)를 통해 시스템이 어떻게 시작되고 복구되는지를 정의하는 체계적인 복구 프로세스를 정의하는 데 도움이 됩니다. 

큰 앱을 복구하는 것은 복잡할 수 있습니다. 복구 계획은 복구가 일관되게 정확하고 반복 가능하며 자동화되도록 순서를 부과하는 데 도움이 됩니다. Azure 자동화 실행북뿐만 아니라 스크립트를 사용하여 복구 계획 내에서 작업을 자동화할 수 있습니다. 일반적인 예로는 장애 조치 후 Azure VM에서 설정을 구성하거나 VM에서 실행 중인 앱을 다시 구성하는 경우를 들 수 있습니다.

- [자세히 알아봅니다](recovery-plan-overview.md) .
- Azure 자동화 실행책에 대해 [자세히 알아보세요.](../automation/automation-runbook-types.md)



## <a name="runbooks-in-recovery-plans"></a>복구 계획의 런북

Azure 자동화 계정 및 Runbook을 복구 계획에 추가합니다. 복구 계획이 실행될 때 Runbook이 호출됩니다.

- 자동화 계정은 모든 Azure 지역에 있을 수 있으며 사이트 복구 자격 증명 모음과 동일한 구독에 있어야 합니다. 
- Runbook은 기본 위치에서 보조 위치로 장애 조치 하는 동안 또는 보조 위치에서 기본 으로 장애 복구 하는 동안 복구 계획에서 실행할 수 있습니다.
- 복구 계획의 Runbook은 차례로 설정된 순서로 연속적으로 실행됩니다.
- 복구 계획의 Runbook이 VM을 다른 그룹에서 시작하도록 구성하면 Azure에서 모든 VM을 실행 중으로 보고하는 경우에만 복구 계획이 계속됩니다.
- 스크립트가 실패하더라도 복구 계획은 계속 실행됩니다.

### <a name="recovery-plan-context"></a>복구 계획 컨텍스트

스크립트가 실행되면 Runbook에 복구 계획 컨텍스트를 삽입합니다. 컨텍스트에는 테이블에 요약된 변수가 포함되어 있습니다.

| **변수 이름** | **설명** |
| --- | --- |
| RecoveryPlanName |복구 계획 이름입니다. 이름에 기반한 작업에 사용됩니다. |
| FailoverType |테스트 또는 프로덕션 장애 조치인지 여부를 지정합니다. 
| FailoverDirection | 복구가 기본 또는 보조 위치에 있는지 여부를 지정합니다. |
| GroupID |계획이 실행 중일 때 복구 계획에서 그룹 번호를 식별합니다. |
| VmMap |그룹의 모든 VM 배열입니다. |
| VMMap key |각 VM에 대한 고유 키(GUID)입니다. |
| SubscriptionId |VM이 만들어진 Azure 구독 ID입니다. |
| ResourceGroupName | VM이 있는 리소스 그룹의 이름입니다.
| CloudServiceName |VM이 만들어진 Azure 클라우드 서비스 이름입니다. |
| RoleName |Azure VM의 이름입니다. |
| RecoveryPointId|VM 복구를 위한 타임스탬프입니다. |

다음 예제는 컨텍스트 변수를 보여줍니다.

```
{"RecoveryPlanName":"hrweb-recovery",
"FailoverType":"Test",
"FailoverDirection":"PrimaryToSecondary",
"GroupId":"1",
"VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":
    { "SubscriptionId":"7a1111111-c1d6-49c5-8c5d-111ce8dd183",
    "ResourceGroupName":"ContosoRG",
    "CloudServiceName":"pod02hrweb-Chicago-test",
    "RoleName":"Fabrikam-Hrweb-frontend-test",
    "RecoveryPointId":"TimeStamp"}
    }
}
```

루프에서 VMMap의 모든 VM에 액세스하려면 다음 코드를 사용할 수 있습니다.

```
$VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
$vmMap = $RecoveryPlanContext.VmMap
    foreach($VMID in $VMinfo)
    {
        $VM = $vmMap.$VMID                
            if( !(($VM -eq $Null) -Or ($VM.ResourceGroupName -eq $Null) -Or ($VM.RoleName -eq $Null))) {
            #this check is to ensure that we skip when some data is not available else it will fail
    Write-output "Resource group name ", $VM.ResourceGroupName
    Write-output "Rolename " = $VM.RoleName
            }
        }
```


[수확 구름에서](http://harvestingclouds.com) 아만 샤르마의 블로그는 복구 계획 [컨텍스트 스크립트의](http://harvestingclouds.com/post/script-sample-azure-automation-runbook-for-asr-recovery-plan/)유용한 예가 있습니다.



## <a name="before-you-start"></a>시작하기 전에

- Azure 자동화를 새로 접하는 경우 샘플 [스크립트를](https://azure.microsoft.com/documentation/scripts/) [등록하고](https://azure.microsoft.com/services/automation/) 다운로드할 수 있습니다.
- Automation 계정에 다음 모듈이 있는지 확인합니다.
    - AzureRM.profile
    - AzureRM.Resources
    - AzureRM.Automation
    - AzureRM.Network
    - AzureRM.Compute

    모든 모듈은 호환되는 버전이어야 합니다. 가장 간단한 방법은 항상 모든 모듈의 최신 버전을 사용하는 것입니다.



## <a name="customize-the-recovery-plan"></a>복구 계획 사용자 지정

1. 볼트에서 복구 **계획(사이트 복구)을 선택합니다.**
2. 복구 계획을 만들려면 **+복구 계획을**클릭합니다. [자세히 알아봅니다](site-recovery-create-recovery-plans.md). 이미 복구 계획이 있는 경우 클릭하여 엽니다.
3. 복구 계획 페이지에서 **사용자 지정을**클릭합니다.

    ![[사용자 지정] 단추 클릭](media/site-recovery-runbook-automation-new/custom-rp.png)

2. 그룹 1 옆의 타원(...) :**사후 작업 추가** **시작** > 을 클릭합니다.
3. **삽입 작업에서** **스크립트가** 선택되었는지 확인하고 스크립트의 이름을 지정합니다(Hello**World).**
4. 자동화 계정을 지정하고 Runbook을 선택합니다. 스크립트를 저장하려면 **확인**을 클릭합니다. 스크립트가 **그룹 1: 사후 단계**에 추가됩니다.


## <a name="reuse-a-runbook-script"></a>Runbook 스크립트 재사용

외부 변수를 사용하여 여러 복구 계획에서 단일 Runbook 스크립트를 사용할 수 있습니다. 

- Azure [자동화 변수를](../automation/automation-variables.md) 사용하여 복구 계획을 실행하기 위한 매개 변수를 저장합니다.
- 복구 계획 이름을 변수에 대한 접두사로 추가하여 각 복구 계획에 대한 개별 변수를 만들 수 있습니다. 그런 다음 매개 변수로 변수를 사용합니다.
- 스크립트를 변경하지 않고 매개 변수를 변경할 수 있으며 스크립트가 작동하는 방식도 변경할 수 있습니다.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Runbook 스크립트에서 단순 문자열 변수 사용

이 예제에서 스크립트는 NSG(네트워크 보안 그룹)의 입력을 가져와 복구 계획의 VM에 적용합니다. 

1. 스크립트에서 실행 중인 복구 계획을 검색할 수 있도록 이 복구 계획 컨텍스트를 사용합니다.

    ```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
    ```

2. NSG 이름 및 리소스 그룹을 기록합니다. 이러한 변수를 복구 계획 스크립트의 입력으로 사용합니다. 
1. 자동화 계정 자산에서. NSG 이름을 저장할 변수를 만듭니다. 복구 계획의 이름으로 변수 이름에 접두사를 추가합니다.

    ![NSG 이름 변수 만들기](media/site-recovery-runbook-automation-new/var1.png)

2. NSG 리소스에 대한 리소스 그룹 이름을 저장할 변수를 만듭니다. 복구 계획의 이름으로 변수 이름에 접두사를 추가합니다.

    ![NSG 리소스 그룹 이름 만들기](media/site-recovery-runbook-automation-new/var2.png)


3.  스크립트에서 이 참조 코드를 사용하여 변수 값을 가져옵니다.

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  Runbook에서 변수를 사용하여 장애 조치된 VM의 네트워크 인터페이스에 NSG를 적용합니다.

    ```
    InlineScript {
    if (($Using:NSGname -ne $Null) -And ($Using:NSGRGname -ne $Null)) {
            $NSG = Get-AzureRmNetworkSecurityGroup -Name $Using:NSGname -ResourceGroupName $Using:NSGRGname
            Write-output $NSG.Id
            #Apply the NSG to a network interface
            #$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
            #Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            #  -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $NSG
        }
    }
    ```


각 복구 계획마다 개별 변수를 만들어 스크립트를 다시 사용할 수 있도록 합니다. 복구 계획 이름을 사용하여 접두사를 추가합니다. 

이 시나리오에 대한 전체 종단 간 스크립트를 보려면 [이 스크립트를](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee)검토하십시오.


### <a name="use-a-complex-variable-to-store-more-information"></a>복합 변수를 사용하여 자세한 정보 저장

일부 시나리오에서는 각 복구 계획에 대해 별도의 변수를 만들지 못할 수 있습니다. 단일 스크립트가 특정 VM에 공용 IP 주소를 할당하려는 시나리오를 고려합니다. 다른 시나리오에서는 서로 다른 VM(모든 VM 아님)에는 다른 NSG를 적용하려고 합니다. 다음 사항에 유의하세요.

- 모든 복구 계획에 대해 재사용할 수 있는 스크립트를 만들 수 있습니다.
- 각 복구 계획에는 다양한 수의 VM이 포함될 수 있습니다.
- 예를 들어 SharePoint 복구에는 두 개의 프런트 엔드가 있습니다. 기본 LOB(기간 업무) 애플리케이션에는 하나의 프런트 엔드만 있습니다.
- 이 시나리오에서는 각 복구 계획에 대 한 별도 변수를 만들 수 없습니다.

다음 예제에서는 Azure 자동화 계정에서 [복잡한 변수를](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureautomationvariable) 만듭니다.

Azure PowerShell을 사용하여 여러 값을 지정하여 이 작업을 수행합니다.

1. PowerShell에서 Azure 구독에 로그인합니다.

    ```
    Connect-AzureRmAccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. 매개 변수를 저장하려면 복구 계획의 이름을 사용하여 복잡한 변수를 만듭니다.

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. 이 복합 변수에서 **VMDetails**는 보호된 VM의 VM ID입니다. Azure Portal에서 VM ID를 가져오려면 VM 속성을 봅니다. 다음 스크린샷은 두 VM의 세부 정보를 저장하는 변수를 보여 줍니다.

    ![VM ID를 GUID로 사용](media/site-recovery-runbook-automation-new/vmguid.png)

4. Runbook에서 이 변수를 사용합니다. 표시된 VM GUID가 복구 계획 컨텍스트에 있으면 VM에서 NSG를 적용합니다.

    ```
    $VMDetailsObj = (Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName).ToObject([hashtable])
    ```

4. Runbook에서 복구 계획 컨텍스트 VM을 반복합니다. **$VMDetailsObj**에서 VM의 존재 여부를 확인합니다. 존재하는 경우 변수 속성에 액세스하여 NSG를 적용합니다.

    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap

        foreach($VMID in $VMinfo) {
            $VMDetails = $VMDetailsObj[$VMID].ToObject([hashtable]);
            Write-output $VMDetails
            if ($VMDetails -ne $Null) { #If the VM exists in the context, this will not be Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetails.NSGName
                $NSGRGname = $VMDetails.NSGResourceGroupName

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

다른 복구 계획에 대해 동일한 스크립트를 사용할 수 있습니다. 다른 변수에서 복구 계획에 해당하는 값을 저장하여 서로 다른 매개 변수를 입력합니다.

## <a name="sample-scripts"></a>샘플 스크립트

Automation 계정에 샘플 스크립트를 배포하려면 **Azure에 배포** 단추를 클릭합니다.

[![Azure에 배포](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

이 비디오에서는 또 다른 예제를 제공합니다. Azure로 2계층 WordPress 애플리케이션을 복구하는 방법을 보여줍니다.


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="next-steps"></a>다음 단계

- [계정으로 실행되는 Azure 자동화에](../automation/automation-create-runas-account.md) 대해 자세히 알아보기
- [Azure 자동화 샘플 스크립트 검토](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=User&f%5B0%5D.Value=SC%20Automation%20Product%20Team&f%5B0%5D.Text=SC%20Automation%20Product%20Team).
- 장애 조치(failover) 실행에 대해 [자세히 알아보세요.](site-recovery-failover.md)



