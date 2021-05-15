---
title: Site Recovery 복구 계획에 Azure Automation Runbook 추가
description: Azure Site Recovery를 통한 재해 복구를 위해 Azure Automation을 사용하여 복구 계획을 확장하는 방법에 대해 알아봅니다.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: ramamill
ms.openlocfilehash: a141280338632fdad7053cbbe76c8bdf2797443d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89424874"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>복구 계획에 Azure Automation Runbook 추가

이 문서에서는 Azure Automation runbook을 통합하여 [Azure Site Recovery](site-recovery-overview.md) 복구 계획을 확장하는 방법을 설명합니다. 수동 작업이 필요했을 기본 작업을 자동화하는 방법과 다단계 복구를 단일 클릭 작업으로 변환하는 방법을 보여 줍니다.

## <a name="recovery-plans"></a>복구 계획 

온-프레미스 컴퓨터 또는 Azure VM을 장애 조치(failover)할 때 복구 계획을 사용할 수 있습니다. 복구 계획은 컴퓨터 장애 조치(failover) 방법과 장애 조치(failover) 후의 시작 및 복구 방법을 정의하는 체계적인 복구 프로세스를 정의하는 데 도움이 됩니다. 

규모가 큰 앱의 복구는 복잡할 수 있습니다. 복구 계획을 통해 순서가 적용되면 복구가 일관적으로 정확하고 반복 가능하며 자동화될 수 있습니다. 스크립트 및 Azure Automation Runbook을 사용하여 복구 계획 내에서 작업을 자동화할 수 있습니다. 장애 조치(failover) 후 Azure VM에서 설정을 구성하거나 VM에서 실행되는 앱을 다시 구성하는 것이 일반적인 예가 될 수 있습니다.

- [자세히 알아봅니다](recovery-plan-overview.md) .
- Azure Automation Runbook에 대해 [자세히 알아봅니다](../automation/automation-runbook-types.md).



## <a name="runbooks-in-recovery-plans"></a>복구 계획의 Runbook

복구 계획에 Azure Automation 계정과 Runbook을 추가합니다. Runbook은 복구 계획이 실행될 때 호출됩니다.

- Automation 계정은 모든 Azure 지역에 존재할 수 있지만 Site Recovery 자격 증명 모음과 동일한 구독에 있어야 합니다. 
- Runbook은 기본 위치에서 보조 위치로 장애 조치(failover)를 하는 동안 또는 보조 위치에서 기본 위치로 장애 복구(failback)를 하는 동안 복구 계획에서 실행할 수 있습니다.
- 복구 계획의 Runbook은 설정된 순서대로 순차적으로 실행됩니다.
- 복구 계획의 Runbook이 VM을 다른 그룹에서 시작하도록 구성하는 경우 Azure가 모든 VM을 실행 중으로 보고하는 경우에만 복구 계획이 계속됩니다.
- 복구 계획은 스크립트가 실패할 경우에도 계속 실행됩니다.

### <a name="recovery-plan-context"></a>복구 계획 컨텍스트

스크립트가 실행되면 복구 계획 컨텍스트가 Runbook으로 삽입됩니다. 컨텍스트에는 테이블에 요약되어 있는 변수가 포함되어 있습니다.

| **변수 이름** | **설명** |
| --- | --- |
| RecoveryPlanName |복구 계획 이름입니다. 이름에 따라 동작에 사용됩니다. |
| FailoverType |테스트인지 또는 프로덕션 장애 조치(failover)인지 여부를 지정합니다. 
| FailoverDirection | 복구가 기본 위치 쪽으로 이루어지는지 보조 사이트 쪽으로 이루어지는지 지정합니다. |
| GroupID |계획이 실행 중일 때 복구 계획에서 그룹 번호를 식별합니다. |
| VmMap |그룹의 모든 VM 배열입니다. |
| VMMap key |각 VM에 대한 고유 키(GUID)입니다. |
| SubscriptionId |VM이 만들어진 Azure 구독 ID입니다. |
| ResourceGroupName | VM이 있는 리소스 그룹의 이름입니다.
| CloudServiceName |VM이 만들어진 Azure 클라우드 서비스 이름입니다. |
| RoleName |Azure VM의 이름입니다. |
| RecoveryPointId|VM 복구의 타임스탬프입니다. |

>[!Note]
>변수 ' FailoverDirection '의 값은 장애 조치(failover)의 경우 'PrimaryToSecondary '이고 장애 복구(failback)의 경우 'SecondaryToPrimary'입니다.

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

루프에서 VMMap의 모든 VM에 액세스하려는 경우 다음 코드를 사용할 수 있습니다.

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


[Harvesting Clouds](http://harvestingclouds.com)에 있는 Aman Sharma의 블로그에는 [복구 계획 컨텍스트 스크립트](http://harvestingclouds.com/post/script-sample-azure-automation-runbook-for-asr-recovery-plan/)의 유용한 예가 있습니다.



## <a name="before-you-start"></a>시작하기 전에

- Azure Automation을 처음 접하는 경우 [등록](https://azure.microsoft.com/services/automation/)하여 [샘플 스크립트를 다운로드](https://azure.microsoft.com/documentation/scripts/)할 수 있습니다.
- Automation 계정에 다음 모듈이 있는지 확인합니다.
    - AzureRM.profile
    - AzureRM.Resources
    - AzureRM.Automation
    - AzureRM.Network
    - AzureRM.Compute

    모든 모듈은 호환되는 버전이어야 합니다. 가장 간단한 방법은 항상 모든 모듈의 최신 버전을 사용하는 것입니다.



## <a name="customize-the-recovery-plan"></a>복구 계획 사용자 지정

1. 자격 증명 모음에서 **복구 계획(Site Recovery)** 을 선택합니다.
2. 복구 계획을 만들려면 **+ 복구 계획** 을 클릭합니다. [자세히 알아봅니다](site-recovery-create-recovery-plans.md). 복구 계획이 이미 있는 경우 열기를 선택합니다.
3. 복구 계획 페이지에서 **사용자 지정** 을 클릭합니다.

    ![[사용자 지정] 단추 클릭](media/site-recovery-runbook-automation-new/custom-rp.png)

2. **그룹 1: 시작** > **후 작업 추가** 옆에 있는 줄임표(...)를 클릭합니다.
3. **삽입 동작** 에서 **스크립트** 가 선택되어 있는지 확인하고 스크립트 이름(**Hello World**)을 지정합니다.
4. 자동화 계정을 지정하고 Runbook을 선택합니다. 스크립트를 저장하려면 **확인** 을 클릭합니다. 스크립트가 **그룹 1: 사후 단계** 에 추가됩니다.


## <a name="reuse-a-runbook-script"></a>Runbook 스크립트 다시 사용

단일 Runbook 스크립트는 외부 변수를 사용하여 여러 복구 계획에서 사용할 수 있습니다. 

- [Azure Automation 변수](../automation/shared-resources/variables.md)를 사용하여 복구 계획 실행을 위한 매개 변수를 저장합니다.
- 복구 계획 이름을 변수에 대한 접두사로 추가하여 각 복구 계획에 대한 개별 변수를 만들 수 있습니다. 그런 다음 매개 변수로 변수를 사용합니다.
- 스크립트를 변경하지 않고 매개 변수를 변경할 수 있으며 스크립트가 작동하는 방식도 변경할 수 있습니다.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Runbook 스크립트에서 단순 문자열 변수 사용

이 예제에서 스크립트는 NSG(네트워크 보안 그룹)의 입력을 받아서 복구 계획의 VM에 적용합니다. 

1. 스크립트가 실행 중인 복구 계획을 검색할 수 있도록 다음 복구 계획 컨텍스트를 사용합니다.

    ```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
    ```

2. NSG 이름과 리소스 그룹을 주목해서 봅니다. 복구 계획 스크립트에 대한 입력으로 이러한 변수를 사용합니다. 
1. Automation 계정 자산에서 NSG 이름을 저장할 변수를 만듭니다. 복구 계획의 이름을 사용하여 변수 이름에 접두사를 추가합니다.

    ![NSG 이름 변수 만들기](media/site-recovery-runbook-automation-new/var1.png)

2. NSG 리소스의 리소스 그룹 이름을 저장할 변수를 만듭니다. 복구 계획의 이름을 사용하여 변수 이름에 접두사를 추가합니다.

    ![NSG 리소스 그룹 이름 만들기](media/site-recovery-runbook-automation-new/var2.png)


3.  스크립트에서 다음 참조 코드를 사용하여 변수 값을 가져옵니다.

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

이 시나리오의 전체적인 엔드투엔드 스크립트의 경우 [이 스크립트](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee)를 검토합니다.


### <a name="use-a-complex-variable-to-store-more-information"></a>복합 변수를 사용하여 자세한 정보 저장

일부 시나리오에서는 각 복구 계획에 대한 별도의 변수를 만들지 못할 수 있습니다. 단일 스크립트에서 특정 VM의 공용 IP 주소를 할당하는 시나리오를 고려해 보세요. 다른 시나리오에서는 서로 다른 VM(모든 VM 아님)에는 다른 NSG를 적용하려고 합니다. 다음 사항에 유의합니다.

- 모든 복구 계획에 다시 사용할 수 있는 스크립트를 만들 수 있습니다.
- 각 복구 계획에는 다양한 수의 VM이 포함될 수 있습니다.
- 예를 들어 SharePoint 복구에는 두 개의 프런트 엔드가 있습니다. 기본 LOB(기간 업무) 애플리케이션에는 하나의 프런트 엔드만 있습니다.
- 이 시나리오에서는 각 복구 계획에 별도의 변수를 만들 수 없습니다.

다음 예제에서는 Azure Automation에 [복합 변수](/powershell/module/servicemanagement/azure.service/set-azureautomationvariable)를 만듭니다.

Azure PowerShell를 사용하여 여러 값을 지정하여 이 작업을 수행합니다.

1. PowerShell에서 Azure 구독에 로그인합니다.

    ```
    Connect-AzureRmAccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. 매개 변수를 저장하려면 복구 계획의 이름을 사용하여 복합 변수를 만듭니다.

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. 이 복합 변수에서 **VMDetails** 는 보호된 VM의 VM ID입니다. Azure Portal에서 VM ID를 가져오려면 VM 속성을 봅니다. 다음 스크린샷은 두 VM의 세부 정보를 저장하는 변수를 보여 줍니다.

    ![VM ID를 GUID로 사용](media/site-recovery-runbook-automation-new/vmguid.png)

4. Runbook에서 이 변수를 사용합니다. 표시된 VM GUID가 복구 계획 컨텍스트에 있으면 VM에서 NSG를 적용합니다.

    ```
    $VMDetailsObj = (Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName).ToObject([hashtable])
    ```

4. Runbook에서 복구 계획 컨텍스트 VM을 반복합니다. **$VMDetailsObj** 에서 VM의 존재 여부를 확인합니다. 존재하는 경우 변수 속성에 액세스하여 NSG를 적용합니다.

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

이 영상에서 또 다른 예를 제공합니다. Azure로 2계층 WordPress 애플리케이션을 복구하는 방법을 보여줍니다.


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="next-steps"></a>다음 단계

- [Azure Automation 실행 계정](../automation/manage-runas-account.md)에 대해 알아봅니다.
- [Azure Automation 샘플 스크립트](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=User&f%5B0%5D.Value=SC%20Automation%20Product%20Team&f%5B0%5D.Text=SC%20Automation%20Product%20Team)를 검토합니다.
- 장애 조치를 실행하는 방법에 대해 [자세히 알아보세요](site-recovery-failover.md).
