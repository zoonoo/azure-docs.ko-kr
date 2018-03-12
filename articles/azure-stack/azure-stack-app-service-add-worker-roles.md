---
title: "Azure 스택 앱 서비스-에 작업자 역할을 수평적 | Microsoft Docs"
description: "Azure 스택 앱 서비스를 크기 조정 하기 위한 세부 지침"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: d6471796863a80e69fdaf740b68fb27d59503453
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="app-service-on-azure-stack-add-more-infrastructure-or-worker-roles"></a>Azure 스택 앱 서비스: 더 많은 인프라 또는 작업자 역할 추가

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*  

이 문서에서는 Azure 스택 인프라 및 작업자 역할에서 응용 프로그램 서비스를 확장 하는 방법에 대 한 지침을 제공 합니다. 모든 규모의 응용 프로그램을 지원 하도록 추가 작업자 역할을 만들기 위한 단계를 포함 합니다.

> [!NOTE]
> Azure 스택 환경에 96 g B RAM 이상 문제가 용량 추가 할 수 있습니다.

기본적으로 Azure 스택 앱 서비스는 무료 및 공유 작업자 계층을 지원합니다. 다른 작업자 계층을 추가 하려면 다른 작업자 역할을 추가 해야 합니다.

Azure 스택 설치에서 기본 응용 프로그램 서비스와 함께 배포 된 내용을 확실 하지 않은 경우의 추가 정보를 검토할 수 있습니다는 [개요 Azure 스택 앱 서비스](azure-stack-app-service-overview.md)합니다.

Azure 스택 앱 서비스를 azure 가상 컴퓨터 크기 집합을 사용 하 여 모든 역할 하 고 따라서는이 작업의 크기 조정 기능 이용 합니다. 따라서 응용 프로그램 서비스 관리자를 통해 수행 됩니다 작업자 계층의 모든 크기 조정

> [!IMPORTANT]
> 현재 Azure 스택 릴리스 정보에서 식별 한 대로 포털에서 가상 컴퓨터 크기 집합의 크기를 조정, 따라서 PowerShell 예제를 사용 하 여 확장할 수는 없습니다.
>
>

## <a name="add-additional-workers-with-powershell"></a>PowerShell과 함께 추가 작업자 추가

1. [PowerShell에서 Azure 스택 관리자 환경을 설정합니다](azure-stack-powershell-configure-admin.md)
2. 이 예제를 사용 하 여 크기 집합을 확장 하려면:
   ```powershell
   
    ##### Scale out the AppService Role instances ######
   
    # Set context to AzureStack admin.
    Login-AzureRMAccount -EnvironmentName AzureStackAdmin
                                                 
    ## Name of the Resource group where AppService is deployed.
    $AppServiceResourceGroupName = "AppService.local"

    ## Name of the ScaleSet : e.g. FrontEndsScaleSet, ManagementServersScaleSet, PublishersScaleSet , LargeWorkerTierScaleSet,      MediumWorkerTierScaleSet, SmallWorkerTierScaleSet, SharedWorkerTierScaleSet
    $ScaleSetName = "SharedWorkerTierScaleSet"

    ## TotalCapacity is sum of the instances needed at the end of operation. 
    ## e.g. if you VMSS has 1 instance(s) currently and you need 1 more the TotalCapacity should be set to 2
    $TotalCapacity = 2  

    # Get current scale set
    $vmss = Get-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -VMScaleSetName $ScaleSetName

    # Set and update the capacity
    $vmss.sku.capacity = $TotalCapacity
    Update-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -Name $ScaleSetName -VirtualMachineScaleSet $vmss 
  
    '''

> [!NOTE]
> This step can take a number of hours to complete depending on the type of role and the number of instances.
>
>

3. Monitor the status of the new role instances in the App Service Administration, to check the status of an individual role instance click the role type in the list.

## Add additional workers directly within the App Service Resource Provider Admin.

1. Log in to the Azure Stack administration portal as the service administrator.

2. Browse to **App Services**.

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Click **Roles**. Here you see the breakdown of all App Service roles deployed.

4. Right click on the row of the type you want to scale and then click **ScaleSet**.

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Click **Scaling**, select the number of instances you want to scale to, and then click **Save**.

    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. App Service on Azure Stack will now add the additional VMs, configure them, install all the required software, and mark them as ready when this process is complete. This process can take approximately 80 minutes.

7. You can monitor the progress of the readiness of the new roles by viewing the workers in the **Roles** blade.

## Result

After they are fully deployed and ready, the workers become available for users to deploy their workload onto them. The following shows an example of the multiple pricing tiers available by default. If there are no available workers for a particular worker tier, the option to choose the corresponding pricing tier is unavailable.

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> To scale out Management, Front End or Publisher roles add you must scale out the corresponding role type. 
>
>

To scale out Management, Front End, or Publisher roles, follow the same steps selecting the appropriate role type. Controllers are not deployed as Scale Sets and therefore two should be deployed at Installation time for all production deployments.

### Next steps

[Configure deployment sources](azure-stack-app-service-configure-deployment-sources.md)
