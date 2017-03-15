---
title: "Site Recovery에서 복구 계획에 Azure 자동화 Runbook 추가 | Microsoft Docs"
description: "이 문서에서는 Azure Site Recovery에서 Azure 자동화를 사용하여 복구 계획을 확장하여 Azure로 복구하는 동안 복잡한 작업을 완료할 수 있도록 하는 방법을 설명합니다."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: ecece14d-5f92-4596-bbaf-5204addb95c2
ms.service: site-recovery
ms.devlang: powershell
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: required
ms.date: 02/22/2017
ms.author: ruturajd@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: ac56273cf85aff550febecf0d75ec87d5c6dbbca
ms.openlocfilehash: 26547135548dde96e9da601f2e0ccfe96c626880
ms.lasthandoff: 02/23/2017


---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>복구 계획에 Azure 자동화 runbook 추가
이 자습서에서는 Azure Site Recovery가 복구 계획에 확장성을 제공하는 Azure 자동화와 통합하는 방법을 설명합니다. 복구 계획은 보조 클라우드에 복제 및 Azure 시나리오에 복제 둘 다에 Azure Site Recovery를 사용하여 보호되는 가상 컴퓨터의 복구를 오케스트레이션할 수 있습니다. 복구를 **일관적으로 정확**하고, **반복 가능**하며, **자동화**되도록 할 수도 있습니다. Azure에 가상 컴퓨터의 장애를 복구하는 경우, Azure 자동화와의 통합은 복구 계획을 확대하고 runbook을 실행하는 기능을 제공하므로 강력한 자동화 작업이 가능합니다.

Azure Automation에 대해 아직 들어보지 못한 경우 [여기](https://azure.microsoft.com/services/automation/)에 등록하여 [여기](https://azure.microsoft.com/documentation/scripts/)에서 샘플 스크립트를 다운로드합니다. [여기](https://azure.microsoft.com/blog/?p=166264)에서 [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) 및 복구 계획을 사용하여 Azure에 복구를 오케스트레이션하는 방법에 대해 더 알아봅니다.

이 자습서에서는 Azure Automation runbook을 복구 계획에 통합하는 방법을 살펴봅니다. 이전에 수동 개입이 필요했던 단순 작업을 자동화하고, 다단계 복구를 클릭 한 번의 복구 동작으로 변환하는 방법에 대해 알아봅니다.

## <a name="customize-the-recovery-plan"></a>복구 계획 사용자 지정
1. 먼저 복구 계획의 리소스 블레이드를 열어 보겠습니다. 복구 계획에 두 개의 가상 컴퓨터가 복구용으로 추가되어 있는 것을 볼 수 있습니다.

    ![](media/site-recovery-runbook-automation-new/essentials-rp.PNG)
- - -
1. 사용자 지정 단추를 클릭하여 runbook을 추가합니다. 

    ![](media/site-recovery-runbook-automation-new/customize-rp.PNG)


1. 시작 그룹 1을 마우스 오른쪽 단추로 클릭하고 [사후 작업 추가]를 선택하여 추가합니다.
2. 새 블레이드에서 스크립트를 선택합니다.
3. 스크립트 이름을 'Hello World'로 지정합니다.
4. Automation 계정 이름을 선택합니다. 
    >[!NOTE]
    > Automation 계정은 모든 Azure 지리적 위치에 있을 수 있지만 Site Recovery 자격 증명 모음과 동일한 구독에 있어야 합니다.
    
5. Automation 계정에서 runbook을 선택합니다. 이 runbook은 첫 번째 그룹을 복구한 후에 복구 계획을 실행하는 동안 실행할 스크립트입니다.

    ![](media/site-recovery-runbook-automation-new/update-rp.PNG)
6. 확인을 선택하여 스크립트를 저장합니다. 스크립트가 [그룹 1: 시작]의 사후 작업 그룹에 추가됩니다.

    ![](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="salient-points-of-adding-a-script"></a>스크립트 추가의 중요한 요점
1. 스크립트를 마우스 오른쪽 단추로 클릭하고 [단계 삭제] 또는 [스크립트 업데이트]를 선택합니다.
2. 온-프레미스에서 Azure로 장애 조치하는 동안 Azure에서 스크립트를 실행할 수 있으며, 종료하기 전에 Azure에서 온-프레미스로 장애 복구하는 동안 Azure에서 기본 스크립트로 스크립트를 실행할 수 있습니다.
3. 스크립트가 실행되면 복구 계획 컨텍스트가 삽입됩니다.

다음은 컨텍스트 변수를 표시하는 방법의 예제입니다.

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


아래 표에는 컨텍스트의 각 변수에 대한 이름과 설명이 있습니다.

| **변수 이름** | **설명** |
| --- | --- |
| RecoveryPlanName |실행되는 계획의 이름입니다. 이 변수를 사용하면 스크립트를 다시 사용하여 이름에 따라 서로 다른 작업을 수행할 수 있습니다. |
| FailoverType |장애 조치(failover)가 테스트, 계획됨 또는 계획되지 않음인지 지정합니다. |
| FailoverDirection |복구가 주 사이트 쪽으로 이루어지는지 보조 사이트 쪽으로 이루어지는지 지정합니다. |
| GroupID |계획이 실행 중일 때 복구 계획 내의 그룹 번호를 식별합니다. |
| VmMap |그룹에 있는 모든 가상 컴퓨터의 배열입니다. |
| VMMap key |각 VM에 대한 고유 키(GUID)입니다. 해당되는 경우 가상 컴퓨터의 VMM ID와 동일합니다. |
| SubscriptionId |VM이 만들어진 Azure 구독 ID입니다. |
| RoleName |복구 중인 Azure VM의 이름입니다. |
| CloudServiceName |가상 컴퓨터가 만들어지는 Azure 클라우드 서비스 이름입니다. |
| ResourceGroupName|가상 컴퓨터가 만들어지는 Azure 리소스 그룹 이름입니다. |
| RecoveryPointId|VM이 복구되는 타임스탬프입니다. |

또한 Automation 계정에 다음 모듈이 추가되었는지 확인해야 합니다. 모든 모듈은 호환되는 버전이어야 합니다. 모든 모듈이 최신 버전인지 확인하는 것이 손쉬운 방법입니다.
* AzureRM.profile
* AzureRM.Resources
* AzureRM.Automation
* AzureRM.Network
* AzureRM.Compute


### <a name="accessing-all-vms-of-the-vmmap-in-a-loop"></a>루프에서 VmMap의 모든 VM 액세스
다음 코드 조각을 사용하여 VmMap의 모든 VM에서 반복합니다.

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

> [!NOTE]
> 스크립트가 부팅 그룹에 대한 사전 작업인 경우 리소스 그룹 이름과 역할 이름 값이 비어 있습니다. 해당 그룹의 VM이 장애 조치에서 성공하고 스크립트가 부팅 그룹의 사후 작업인 경우에만 값이 채워집니다.

## <a name="using-the-same-automation-runbook-with-multiple-recovery-plans"></a>여러 복구 계획에 동일한 Automation runbook 사용

단일 스크립트는 외부 변수를 사용하여 여러 복구 계획에서 사용할 수 있습니다. [Azure Automation 변수](../automation/automation-variables.md)를 사용하여 복구 계획 실행을 위해 전달할 수 있는 매개 변수를 저장할 수 있습니다. 변수를 복구 계획의 이름으로 미리 수정하여 복구 계획마다 개별 변수를 만들고 이를 매개 변수로 사용할 수 있습니다. 스크립트를 변경하지 않고 매개 변수를 변경할 수 있고, 스크립트가 다르게 작동하도록 만들 수 있습니다.

### <a name="using-simple-string-variables-in-runbook-script"></a>runbook 스크립트에서 단순 문자열 변수 사용

NSG의 입력을 받아들여 복구 계획의 VM에 적용하는 스크립트를 사용하는 것이 좋습니다.

스크립트에서 실행 중인 복구 계획을 해석할 수 있도록 복구 계획 컨텍스트를 사용할 수 있습니다.

```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
```

기존 NSG를 적용하려면 NSG 이름과 NSG 리소스 그룹이 필요합니다. 이러한 변수를 복구 계획 스크립트의 입력으로 제공합니다. 이렇게 하려면 Automation 계정 자산에 두 개의 변수를 만들고, 매개 변수를 만드는 복구 계획의 이름 앞에 접두사를 붙입니다.

1. NSG 이름을 저장할 변수를 만듭니다. 복구 계획의 이름 앞에 접두사를 붙입니다.
    ![NSG 이름 변수 만들기](media/site-recovery-runbook-automation-new/var1.png)

2. NSG의 RG 이름을 저장할 변수를 만듭니다. 복구 계획의 이름 앞에 접두사를 붙입니다.
    ![NSG RG 이름 만들기](media/site-recovery-runbook-automation-new/var2.png)


스크립트에서 다음 참조 코드를 사용하여 변수의 값을 얻습니다.

```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue 
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
```

다음으로 runbook에서 변수를 사용하고 장애 조치된 가상 컴퓨터의 네트워크 인터페이스에 NSG를 적용할 수 있습니다.

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

각 복구 계획마다 개별 변수를 만들어 스크립트를 다시 사용하고, 복구 계획 이름 앞에 접두사를 붙일 수 있습니다. 이 예제의 전체 종단 간 스크립트는 [여기서 제공합니다](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee).


### <a name="using-complex-variable-to-store-more-information"></a>복합 변수를 사용하여 자세한 정보 저장

하나의 스크립트로 특정 VM들에 공용 IP를 설정하는 시나리오를 생각해 보세요. 또 다른 예는 전체가 아니라 가상 컴퓨터마다 별도의 NSG를 적용하는 것입니다. 이 스크립트는 다른 복구 계획에 다시 사용할 수 있어야 합니다. 각 복구 계획에는 다양한 가상 컴퓨터가 있을 수 있습니다(예: 공유 지점 복구에는 프런트 엔드가 둘, 간단한 LOB 응용 프로그램에는 프런트 엔드가 하나만 있음). 이 결과를 얻기 위해 복구 계획별로 별도의 변수를 만들 수가 없습니다. 여기서는 여러 값을 지정하여 Azure Automation 계정 자산에서 새 기술을 사용하고 [복합 변수](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396)를 만듭니다. 다음 단계를 수행하려면 Azure PowerShell이 필요합니다.

1. Azure PowerShell에서 구독에 로그인합니다.

    ```
        login-azurermaccount
        $sub = Get-AzureRmSubscription -Name <SubscriptionName>
        $sub | Select-AzureRmSubscription
    ```

2. 매개 변수를 저장하려면 복구 계획과 동일한 이름의 복합 변수를 만듭니다.

    ```
        $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

    이 복합 변수에서 **VMDetails*는 보호된 가상 컴퓨터의 VM ID입니다. 포털의 가상 컴퓨터 속성에서 이 ID를 찾을 수 있습니다. 여기서는 두 가상 컴퓨터의 세부 정보를 저장하는 변수를 만들었습니다.

    ![GUID로 사용되는 VM ID](media/site-recovery-runbook-automation-new/vmguid.png)

3. 지정된 VMGUID 중 하나가 복구 계획 컨텍스트에 있으면 runbook에서 이 변수를 사용하고 가상 컴퓨터에서 해당 NSG를 적용합니다.

    ```
        $VMDetailsObj = Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName 
    ```

4. runbook에서 복구 계획 컨텍스트의 VM을 반복하고 VM이 **$VMDetailsObj**에도 있는지 확인합니다. 이 변수가 있는 경우 변수 속성에 액세스하여 NSG를 적용합니다.
    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap
           
        foreach($VMID in $VMinfo) {
            Write-output $VMDetailsObj.value.$VMID
            
            if ($VMDetailsObj.value.$VMID -ne $Null) { #If the VM exists in the context, this will not b Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetailsObj.value.$VMID.'NSGName'
                $NSGRGname = $VMDetailsObj.value.$VMID.'NSGResourceGroupName'

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

다른 복구 계획에 동일한 스크립트를 사용하고, 다른 복구 계획에 해당하는 값을 다른 변수에 저장하여 별도의 매개 변수를 제공할 수 있습니다.

## <a name="sample-scripts"></a>샘플 스크립트
Automation 계정으로 직접 가져올 수 있는 스크립트 리포지토리는 [Kristian Nese의 스크립트용 OMS 리포지토리](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/Solutions/asrautomation)(영문)를 참조하세요.

여기에 있는 스크립트는 다음의 모든 스크립트를 배포하는 Azure Resource Manager 템플릿입니다.

* NSG

NSG runbook은 복구 계획 내의 모든 VM에 공용 IP 주소를 할당하고, 해당 가상 네트워크 어댑터를 기본 통신을 허용하는 네트워크 보안 그룹에 연결합니다.

* PublicIP

PublicIP runbook은 복구 계획 내의 모든 VM에 공용 IP 주소를 할당합니다. 컴퓨터 및 응용 프로그램에 대한 액세스는 각 게스트 내의 방화벽 설정에 따라 달라집니다.

* CustomScript

CustomScript runbook은 복구 계획 내의 모든 VM에 공용 IP 주소를 할당하고, 템플릿을 배포하는 동안 참조하는 스크립트를 가져올 사용자 지정 스크립트 확장을 설치합니다.

* NSGwithCustomScript

NSGwithCustomScript runbook은 복구 계획 내의 모든 VM에 공용 IP 주소를 할당하고, 확장을 사용하는 사용자 지정 스크립트를 설치하며, 원격 액세스를 위한 기본 인바운드 및 아웃바운드 통신을 허용하는 NSG에 가상 네트워크 어댑터를 연결합니다.

## <a name="additional-resources"></a>추가 리소스
[Azure Automation 서비스 실행 계정](../automation/automation-sec-configure-azure-runas-account.md)

[Azure 자동화 개요](http://msdn.microsoft.com/library/azure/dn643629.aspx "Azure 자동화 개요")

[Azure 자동화 스크립트 샘플](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Azure 자동화 스크립트 샘플")

