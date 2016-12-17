---
title: "복구 계획에 Azure 자동화 Runbook 추가 | Microsoft Docs"
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
ms.date: 10/23/2016
ms.author: ruturajd@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 5614c39d914d5ae6fde2de9c0d9941e7b93fc10f
ms.openlocfilehash: 9f9a71cd7c6688dfd42dcb7ce52847f2016daf58


---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>복구 계획에 Azure 자동화 runbook 추가
이 자습서에서는 Azure Site Recovery가 복구 계획에 확장성을 제공하는 Azure 자동화와 통합하는 방법을 설명합니다. 복구 계획은 보조 클라우드에 복제 및 Azure 시나리오에 복제 둘 다에 Azure Site Recovery를 사용하여 보호되는 가상 컴퓨터의 복구를 오케스트레이션할 수 있습니다. 복구를 **일관적으로 정확**하고, **반복 가능**하며, **자동화**되도록 할 수도 있습니다. Azure에 가상 컴퓨터의 장애를 복구하는 경우, Azure 자동화와의 통합은 복구 계획을 확대하고 runbook을 실행하는 기능을 제공하므로 강력한 자동화 작업이 가능합니다.

Azure Automation에 대해 아직 들어보지 못한 경우 [여기](https://azure.microsoft.com/services/automation/)에 등록하여 [여기](https://azure.microsoft.com/documentation/scripts/)에서 샘플 스크립트를 다운로드합니다. [여기](https://azure.microsoft.com/blog/?p=166264)에서 [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) 및 복구 계획을 사용하여 Azure에 복구를 오케스트레이션하는 방법에 대해 더 알아봅니다.

이 자습서에서는 Azure 자동화 runbook을 복구 계획에 통합하는 방법에 대해 살펴봅니다. 앞서 수동 개입이 필요한 단순한 작업을 자동화하고 여러 단계의 복구를 클릭 한 번의 복구 동작으로 변환하는 방법에 대해 알아봅니다. 문제가 발생하는 경우 간단한 스크립트 문제를 해결하는 방법에 대해서도 살펴보겠습니다.

## <a name="customize-the-recovery-plan"></a>복구 계획 사용자 지정
1. 먼저 복구 계획의 리소스 블레이드를 열어 보겠습니다. 복구 계획에 두 개의 가상 컴퓨터가 복구용으로 추가되어 있는 것을 볼 수 있습니다.

    ![](media/site-recovery-runbook-automation-new/essentials-rp.PNG)
- - -
1. 사용자 지정 단추를 클릭하여 Runbook을 추가하기 시작합니다. 복구 계획 사용자 지정 블레이드가 열릴 것입니다.

    ![](media/site-recovery-runbook-automation-new/customize-rp.PNG)


1. 시작 그룹 1을 마우스 오른쪽 단추로 클릭하고 '사후 작업 추가'를 선택하여 추가합니다.
2. 새 블레이드에서 스크립트를 선택합니다.
3. 스크립트 이름을 'Hello World'로 지정합니다.
4. Automation 계정 이름을 선택합니다. 이는 Azure Automation 계정입니다. 이 계정은 모든 Azure 지리적 위치에 있을 수 있지만 Site Recovery 자격 증명 모음과 동일한 구독에 있어야 합니다.
5. Automation 계정에서 runbook을 선택합니다. 이는 첫 번째 그룹의 복구 후 복구 계획을 실행하는 동안 실행할 스크립트입니다.

    ![](media/site-recovery-runbook-automation-new/update-rp.PNG)
6. 확인을 선택하여 스크립트를 저장합니다. 그룹 1: 시작의 사후 작업 그룹에 스크립트가 추가됩니다.

    ![](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="salient-points-of-adding-a-script"></a>스크립트 추가의 중요한 요점
1. 스크립트를 마우스 오른쪽 단추로 클릭하고 '단계 삭제' 또는 '스크립트 업데이트'를 선택합니다.
2. 온-프레미스에서 Azure로 장애 조치하는 동안 Azure에서 스크립트를 실행할 수 있으며, 종료하기 전에 Azure에서 온-프레미스로 장애 복구하는 동안 Azure에서 기본 스크립트로 스크립트를 실행할 수 있습니다.
3. 스크립트를 실행할 때 복구 계획 컨텍스트가 삽입됩니다.

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
| CloudServiceName(Resource Manager 배포 모델) |가상 컴퓨터가 만들어지는 Azure 리소스 그룹 이름입니다. |

## <a name="using-complex-variables-per-recovery-plan"></a>복구 계획별 복잡한 변수 사용
경우에 따라 runbook에 RecoveryPlanContext보다 더 많은 정보가 필요합니다. Runbook에 매개 변수를 전달하는 첫 번째 클래스 메커니즘은 없습니다. 그러나 여러 복구 계획을 통해 동일한 스크립트를 사용하려는 경우 'RecoveryPlanName' 복구 계획 컨텍스트 변수를 사용하고 아래의 실험적 기술을 사용하여 runbook에는 Azure Automation 복잡한 변수를 사용할 수 있습니다. 아래 예제에서는 세 가지 복잡한 변수 자산을 만들어 복구 계획의 이름에 따라 runbook에서 사용하는 방법을 보여 줍니다.

Runbook에서 3개의 추가 매개 변수를 사용하려는 것으로 가정합니다. 이를 JSON 형식 {"Var1":"testautomation","Var2":"Unplanned","Var3":"PrimaryToSecondary"}로 인코딩해 보겠습니다.

[AA 복잡한 변수](../automation/automation-variables.md#variable-types)를 사용하여 새 Automation 자산을 만듭니다.
변수 이름을 <RecoveryPlanName>-params로 지정합니다.
여기서 참조를 사용하여 [복잡한 변수](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396)를 만들 수 있습니다.

여러 복구 계획에 대해 변수 이름을 다음으로 지정합니다.

1. recoveryPlanName1>-params
2. recoveryPlanName2>-params
3. recoveryPlanName3>-params

이제 스크립트에서 다음과 같이 매개 변수를 참조합니다.

1. $rpname = $Recoveryplancontext 변수에서 RP 이름을 가져옵니다.
2. $paramValue = "$($rpname)-params"의 자산을 가져옵니다.
3. Get-AzureAutomationVariable [-AutomationAccountName] <String> -Name $paramValue를 호출하여 복구 계획의 복잡한 변수로 사용합니다.

예를 들어 SharepointApp 복구 계획에 대한 복잡한 변수/매개 변수를 가져오려면 'SharepointApp-params'라는 Azure Automation 복잡한 변수를 만듭니다.

Get-AzureAutomationVariable [-AutomationAccountName] <String> [-Name] $paramValue 문을 사용해 자산에서 변수를 추출하여 복구 계획에서 사용합니다. [자세한 내용은 이 항목을 참조하세요](https://msdn.microsoft.com/library/dn913772.aspx).

이러한 방식으로 계획별 복잡한 변수를 자산에 저장하여 동일한 스크립트를 여러 복구 계획에 사용할 수 있습니다.

## <a name="sample-scripts"></a>샘플 스크립트
Automation 계정으로 직접 가져올 수 있는 스크립트 리포지토리는 [Kristian Nese의 스크립트용 OMS 리포지토리](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/Solutions/asrautomation)를 참조하세요.

여기서 스크립트는 아래의 모든 스크립트를 배포하는 Azure Resource Manager 템플릿입니다.

* NSG

NSG runbook은 복구 계획 내의 모든 VM에 공용 IP 주소를 할당하고, 해당 가상 네트워크 어댑터를 기본 통신을 허용하는 네트워크 보안 그룹에 연결합니다.

* PublicIP

Public IP runbook은 복구 계획 내의 모든 VM에 공용 IP 주소를 할당합니다. 컴퓨터 및 응용 프로그램에 대한 액세스는 각 게스트 내의 방화벽 설정에 따라 달라집니다.

* CustomScript

CustomScript runbook은 복구 계획 내의 모든 VM에 공용 IP 주소를 할당하고, 템플릿을 배포하는 동안 참조하는 스크립트를 가져올 사용자 지정 스크립트 확장을 설치합니다.

* NSGwithCustomScript

NSGwithCustomScript runbook은 복구 계획 내의 모든 VM에 공용 IP 주소를 할당하고, 확장을 사용하는 사용자 지정 스크립트를 설치하며, 원격 액세스를 위한 기본 인바운드 및 아웃바운드 통신을 허용하는 NSG에 가상 네트워크 어댑터를 연결합니다.

## <a name="additional-resources"></a>추가 리소스
[Azure Automation 서비스 실행 계정](../automation/automation-sec-configure-azure-runas-account.md)

[Azure 자동화 개요](http://msdn.microsoft.com/library/azure/dn643629.aspx "Azure 자동화 개요")

[Azure 자동화 스크립트 샘플](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Azure 자동화 스크립트 샘플")



<!--HONumber=Nov16_HO3-->


