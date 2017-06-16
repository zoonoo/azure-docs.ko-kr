---
title: "클래식 포털에서 복구 계획에 Azure 자동화 Runbook 추가 | Microsoft Docs"
description: "이 문서에서는 Azure Site Recovery에서 Azure 자동화를 사용하여 복구 계획을 확장하여 Azure로 복구하는 동안 복잡한 작업을 완료할 수 있도록 하는 방법을 설명합니다."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: f24eaa62-9dea-4fce-92e1-a72513ca0289
ms.service: site-recovery
ms.devlang: powershell
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 02/06/2017
ms.author: ruturajd@microsoft.com
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: c91a1210e84d215449b98aae14bda490aa2cb156
ms.contentlocale: ko-kr
ms.lasthandoff: 04/27/2017


---
# <a name="add-azure-automation-runbooks-to-recovery-plans-in-the-classic-portal"></a>클래식 포털에서 복구 계획에 Azure 자동화 Runbook 추가
이 자습서에서는 Azure Site Recovery가 복구 계획에 확장성을 제공하는 Azure 자동화와 통합하는 방법을 설명합니다. 복구 계획은 보조 클라우드에 복제 및 Azure 시나리오에 복제 둘 다에 Azure Site Recovery를 사용하여 보호되는 가상 컴퓨터의 복구를 오케스트레이션할 수 있습니다. 복구를 **일관적으로 정확**하고, **반복 가능**하며, **자동화**되도록 할 수도 있습니다. Azure에 가상 컴퓨터의 장애를 복구하는 경우, Azure 자동화와의 통합은 복구 계획을 확대하고 runbook을 실행하는 기능을 제공하므로 강력한 자동화 작업이 가능합니다.

Azure Automation에 대해 아직 들어보지 못한 경우 [여기](https://azure.microsoft.com/services/automation/)에 등록하여 [여기](https://azure.microsoft.com/documentation/scripts/)에서 샘플 스크립트를 다운로드합니다. [여기](https://azure.microsoft.com/blog/?p=166264)에서 [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) 및 복구 계획을 사용하여 Azure에 복구를 오케스트레이션하는 방법에 대해 더 알아봅니다.

이 짧은 자습서에서는 Azure Automation runbook을 복구 계획에 통합하는 방법에 대해 살펴봅니다. 앞서 수동 개입이 필요한 단순한 작업을 자동화하고 여러 단계의 복구를 클릭 한 번의 복구 동작으로 변환하는 방법에 대해 알아봅니다. 문제가 발생하는 경우 간단한 스크립트 문제를 해결하는 방법에 대해서도 살펴보겠습니다.

## <a name="protect-the-application-to-azure"></a>Azure로 응용프로그램 보호
두 가상 컴퓨터로 구성되는 간단한 응용프로그램으로 시작해 보겠습니다. 여기서는 Fabrikam의 HRweb 응용프로그램을 사용합니다. Fabrikam-HRweb-프런트엔드 및 Fabrikam-Hrweb-백엔드는 Azure Site Recovery를 사용하여 Azure로 보호되는 두 가상 컴퓨터입니다. Azure Site Recovery를 사용하여 가상 컴퓨터를 보호하려면 다음 단계를 수행합니다.

1. 가상 컴퓨터의 보호를 활성화합니다.
2. 가상 컴퓨터가 초기 복제를 완료했고 복제 중인지 확인합니다.
3. 초기 복제를 완료할 때까지 기다리면 복제 상태가 보호됨이 됩니다.

## ![](media/site-recovery-runbook-automation/01.png)
이 자습서에서는 Azure로 응용프로그램 장애 조치를 하도록 Fabrikam HRweb 응용프로그램에 대한 복구 계획을 만듭니다. 그런 다음 포트 80에서 웹 페이지를 제공하여 장애를 복구한 Azure 가상 컴퓨터에 끝점을 만듭니다.

먼저, 응용프로그램에 대한 복구 계획을 만들어 보겠습니다.

## <a name="create-the-recovery-plan"></a>복구 계획 만들기
Azure에 응용프로그램을 복구하려면 복구 계획을 만들어야 합니다.
복구 계획을 사용하여 가상 컴퓨터 복구 순서를 지정할 수 있습니다. 그룹 1에 배치된 가상 컴퓨터는 먼저 복구를 시작하며 그룹 2의 가상 컴퓨터가 이어서 복구됩니다.

아래와 같이 복구 계획을 만듭니다.

![](media/site-recovery-runbook-automation/12.png)

복구 계획에 대한 자세한 내용은 [여기](https://msdn.microsoft.com/library/azure/dn788799.aspx "여기")되도록 할 수도 있습니다.

다음으로, Azure 자동화에서 필요한 아티팩트를 만들어 보겠습니다.

## <a name="create-the-automation-account-and-its-assets"></a>자동화 계정 및 해당 자산 만들기
runbook을 만들려면 Azure 자동화 계정이 필요합니다. 계정이 아직 없는 경우 ![](media/site-recovery-runbook-automation/02.png)(으)로 표시된 Azure 자동화 탭으로 이동하여 새 계정을 만듭니다.

1. 식별하는 이름으로 계정을 제공합니다.
2. 해당 계정을 배치하려는 지리적 영역을 지정합니다.

ASR 자격 증명 모음과 동일한 하위 지역에 계정을 배치하는 것이 좋습니다.

![](media/site-recovery-runbook-automation/03.png)

다음으로, 계정에서 다음 자산을 만듭니다.

### <a name="add-a-subscription-name-as-asset"></a>자산으로 구독 이름 추가
1. Azure 자동화 자산에 새 설정 ![](media/site-recovery-runbook-automation/04.png)을(를) 추가하고 ![](media/site-recovery-runbook-automation/05.png) 선택
2. 변수 유형을 **문자열**
3. 변수 이름으로 **AzureSubscriptionName**

   ![](media/site-recovery-runbook-automation/06.png)
4. 실제 Azure 구독 이름을 변수 값으로 지정합니다.

   ![](media/site-recovery-runbook-automation/07_1.png)

Azure 포털의 계정 설정 페이지에서 구독 이름을 확인할 수 있습니다.

### <a name="add-an-azure-login-credential-as-asset"></a>Azure 로그인 자격 증명을 자산으로 추가
Azure 자동화는 Azure PowerShell을 사용하여 구독에 연결하고 해당 아티팩트에서 작업합니다. 이를 위해 Microsoft 계정 또는 회사나 학교 계정을 사용하여 인증을 받아야 합니다.
해당 runbook을 통해 안전하게 사용되는 자산에 계정 자격 증명을 저장할 수 있습니다.

1. Azure 자동화 자산에 새 설정 ![](media/site-recovery-runbook-automation/04.png)을(를) 추가하고 ![](media/site-recovery-runbook-automation/09.png) 선택
2. 자격 증명 형식을 **Windows PowerShell 자격 증명**
3. 이름을 **AzureCredential**

   ![](media/site-recovery-runbook-automation/10.png)
4. 로그인할 사용자 이름 및 암호를 지정합니다.

이제 이러한 두 설정을 자산에서 사용할 수 있습니다.

![](media/site-recovery-runbook-automation/11.png)

PowerShell을 통해 구독에 연결하는 방법에 대한 자세한 내용은 [여기](/powershell/azure/overview)를 참조하세요.

다음으로, 장애 조치 후 프런트엔드 가상 컴퓨터에 끝점을 추가할 수 있는 Azure 자동화에서 runbook을 만듭니다.

## <a name="azure-automation-context"></a>Azure 자동화 컨텍스트
ASR은 결정적 스크립트를 작성할 수 있도록 runbook에 컨텍스트 변수를 전달합니다. 클라우드 서비스 및 가상 컴퓨터의 이름을 보호할 수 있음을 입증하지만, Azure에서 지원되지 않는 문자로 인해 변경될 수 있는 가상 컴퓨터의 이름이 있는 특정 시나리오 때문에 항상 그렇지는 않습니다. 따라서 이 정보는 *컨텍스트*의 일부로 ASR 복구 계획에 전달됩니다.

다음은 컨텍스트 변수를 표시하는 방법의 예입니다.

        {"RecoveryPlanName":"hrweb-recovery",

        "FailoverType":"Test",

        "FailoverDirection":"PrimaryToSecondary",

        "GroupId":"1",

        "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                {"CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test"}

                }

        }


아래 표에는 컨텍스트의 각 변수에 대한 이름과 설명이 있습니다.

| **변수 이름** | **설명** |
| --- | --- |
| RecoveryPlanName |실행되는 계획의 이름입니다. 동일한 스크립트를 사용하는 이름에 따라 필요한 조치를 취할 수 있습니다. |
| FailoverType |장애 조치(failover)가 테스트, 계획됨 또는 계획되지 않음인지 지정합니다. |
| FailoverDirection |복구가 주 사이트 쪽으로 이루어지는지 보조 사이트 쪽으로 이루어지는지 지정합니다. |
| GroupID |계획이 실행 중일 때 복구 계획 내의 그룹 번호를 식별합니다. |
| VmMap |그룹에 있는 모든 가상 컴퓨터의 배열입니다. |
| VMMap key |각 VM에 대한 고유 키(GUID)입니다. 해당되는 경우 가상 컴퓨터의 VMM ID와 동일합니다. |
| RoleName |복구 중인 Azure VM의 이름입니다. |
| CloudServiceName |가상 컴퓨터가 만들어지는 Azure 클라우드 서비스 이름입니다. |

컨텍스트에서 VmMap 키를 식별하려면 ASR에서 VM 속성 페이지로 이동하여 VM GUID 속성을 확인할 수도 있습니다.

![](media/site-recovery-runbook-automation/13.png)

## <a name="author-an-automation-runbook"></a>자동화 Runbook 작성
이제 프런트엔드 가상 컴퓨터에서 포트 80을 열기 위해 runbook을 만듭니다.

1. **OpenPort80**

   ![](media/site-recovery-runbook-automation/14.png)
2. runbook의 작성자 보기로 이동하여 초안 모드를 입력합니다.
3. 먼저, 복구 계획 컨텍스트로 사용할 변수를 지정합니다.

   ```
       param (
           [Object]$RecoveryPlanContext
       )

   ```
4. 그런 다음, 자격 증명 및 구독 이름을 사용하여 구독에 연결합니다.

   ```
       $Cred = Get-AutomationPSCredential -Name 'AzureCredential'

       # Connect to Azure
       $AzureAccount = Add-AzureAccount -Credential $Cred
       $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
       Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
   ```

   여기에서 Azure 자산, **AzureCredential** 및 **AzureSubscriptionName**을 사용해야 합니다.
5. 이제 끝점을 노출하려는 가상 컴퓨터의 GUID 및 끝점 세부 정보를 지정합니다. 이 경우 프런트엔드 가상 컴퓨터입니다.

   ```
       # Specify the parameters to be used by the script
       $AEProtocol = "TCP"
       $AELocalPort = 80
       $AEPublicPort = 80
       $AEName = "Port 80 for HTTP"
       $VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"
   ```

   Azure 끝점 프로토콜, VM의 로컬 포트 및 매핑된 해당 공용 포트를 지정합니다. 이러한 변수는 VM에 끝점을 추가하는 Azure 명령에 필요한 매개 변수입니다. VMGUID는 작동에 필요한 가상 컴퓨터의 GUID를 보유합니다.
6. 스크립트는 이제 지정한 VM GUID에 대한 컨텍스트를 추출하여 이를 통해 참조되는 가상 컴퓨터에 끝점을 만듭니다.

   ```
       #Read the VM GUID from the context
       $VM = $RecoveryPlanContext.VmMap.$VMGUID

       if ($VM -ne $null)
       {
           # Invoke pipeline commands within an InlineScript

           $EndpointStatus = InlineScript {
               # Invoke the necessary pipeline commands to add a Azure Endpoint to a specified Virtual Machine
               # Commands include: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including parameters)

               $Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
                   Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
                   Update-AzureVM
               Write-Output $Status
           }
       }
   ```
7. 이 작업이 완료되면 게시 ![](media/site-recovery-runbook-automation/20.png) 를 눌러 스크립트를 실행에 사용할 수 있도록 합니다.

전체 스크립트는 참조를 위해 아래에 제공됩니다.

```
  workflow OpenPort80
  {
    param (
        [Object]$RecoveryPlanContext
    )

    $Cred = Get-AutomationPSCredential -Name 'AzureCredential'

    # Connect to Azure
    $AzureAccount = Add-AzureAccount -Credential $Cred
    $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
    Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

    # Specify the parameters to be used by the script
    $AEProtocol = "TCP"
    $AELocalPort = 80
    $AEPublicPort = 80
    $AEName = "Port 80 for HTTP"
    $VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"

    #Read the VM GUID from the context
    $VM = $RecoveryPlanContext.VmMap.$VMGUID

    if ($VM -ne $null)
    {
        # Invoke pipeline commands within an InlineScript

        $EndpointStatus = InlineScript {
            # Invoke the necessary pipeline commands to add an Azure Endpoint to a specified Virtual Machine
            # This set of commands includes: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including necessary parameters)

            $Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
                Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
                Update-AzureVM
            Write-Output $Status
        }
    }
  }
```

## <a name="add-the-script-to-the-recovery-plan"></a>복구 계획에 스크립트 추가
스크립트가 준비되면 앞서 만든 복구 계획에 이를 추가할 수 있습니다.

1. 만든 복구 계획에서 그룹 2 다음에 스크립트를 추가하도록 선택합니다. ![](media/site-recovery-runbook-automation/15.png)
2. 스크립트 이름을 지정합니다. 복구 계획 내에 표시하기 위한 이 스크립트의 이름입니다.
3. Azure 스크립트에 대한 장애 조치에서 Azure 자동화 계정 이름을 선택합니다.
4. Azure Runbook에서 작성한 runbook을 선택합니다.

![](media/site-recovery-runbook-automation/16.png)

## <a name="primary-side-scripts"></a>기본 측 스크립트
Azure에 장애 조치(Failover)를 실행할 때 기본 측 스크립트를 실행할 수 있습니다. 이러한 스크립트는 장애 조치(Failover) 중 VMM 서버에서 실행 됩니다.
기본 측 스크립트는 종료 전 및 종료 후 단계에만 사용할 수 있습니다. 그 이유는 재해가 발생할 경우 일반적으로 기본 사이트를 사용할 수 없다고 예상하기 때문입니다.
계획되지 않은 장애 조치(Failover) 중에는 기본 사이트 작업을 선택할 경우에만 기본 측 스크립트 실행을 시도합니다. 연결할 수 없거나 시간이 초과되는 경우 장애 조치(Failover)에서 계속 가상 컴퓨터를 복구합니다.
Azure로 장애 조치(Failover)를 실행하는 동안 Azure에 대해 보호된 VMM이 없으면 VMware/물리/Hyper-v 사이트에서 기본 측 스크립트를 사용할 수 없습니다.
하지만 Azure에서 온-프레미스로 장애 조치(Failover)를 실행할 경우에는 VMware를 제외한 모든 대상에 기본 측 스크립트(Runbook)를 사용할 수 있습니다.

## <a name="test-the-recovery-plan"></a>복구 계획 테스트
계획에 runbook을 추가하면 테스트 장애 조치를 초기화하고 동작에서 이를 볼 수 있습니다. 오류가 없도록 복구 계획 및 응용프로그램을 테스트하도록 테스트 장애 조치를 실행하는 것이 좋습니다.

1. 복구 계획을 선택하고 테스트 장애 조치를 시작합니다.
2. 계획을 실행하는 동안 해당 상태를 통해 runbook의 실행 여부를 확인할 수 있습니다.

   ![](media/site-recovery-runbook-automation/17.png)
3. runbook에 대해 Azure 자동화 작업 페이지에서 자세한 runbook 실행 상태를 확인할 수도 있습니다.

   ![](media/site-recovery-runbook-automation/18.png)
4. runbook 실행 결과 외에도 장애 조치를 완료한 후에 Azure 가상 컴퓨터 페이지를 방문하여 끝점을 살펴봄으로써 실행 성공 여부를 확인할 수 있습니다.

![](media/site-recovery-runbook-automation/19.png)

## <a name="sample-scripts"></a>샘플 스크립트
이 자습서에서 Azure 가상 컴퓨터에 끝점을 추가하는 일반적인 작업을 자동화하는 과정을 통해 Azure 자동화를 사용하여 그 밖의 강력한 자동화 작업을 수행할 수 있습니다. Microsoft 및 Azure 자동화 커뮤니티에서는 사용자 고유의 솔루션 만들기를 시작하는 데 도움이 되는 샘플 Runbook 및 대규모 자동화 작업을 위한 구성 요소로 사용할 수 있는 유틸리티 Runbook을 제공합니다. 갤러리에서 사용을 시작하고 Azure Site Recovery를 사용하여 응용프로그램에 대한 강력한 원클릭 복구 계획을 구성합니다.

## <a name="additional-resources"></a>추가 리소스
[Azure 자동화 개요](http://msdn.microsoft.com/library/azure/dn643629.aspx "Azure 자동화 개요")

[Azure 자동화 스크립트 샘플](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Azure 자동화 스크립트 샘플")

