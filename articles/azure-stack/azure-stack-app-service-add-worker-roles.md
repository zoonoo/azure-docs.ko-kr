---
title: App Services-Azure Stack에서에서 작업자 역할 확장 | Microsoft Docs
description: Azure Stack의 App Services를 크기 조정에 대 한 자세한 지침
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2018
ms.author: anwestg
ms.reviewer: sethm
ms.openlocfilehash: f4a3cd7d09c18506a85317db7a360b8de3a9630b
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077025"
---
# <a name="app-service-on-azure-stack-add-more-infrastructure-or-worker-roles"></a>Azure Stack에서 app Service: 인프라 또는 작업자 역할 추가

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*  

이 문서에서는 Azure Stack 인프라 및 작업자 역할에 App Service를 확장 하는 방법에 대 한 지침을 제공 합니다. 모든 규모의 응용 프로그램을 지원 하도록 추가 작업자 역할을 만드는 단계를 포함 합니다.

> [!NOTE]
> Azure Stack 환경에 없는 경우 96-g B RAM 이상, 문제가 추가 용량을 추가 해야 합니다.

기본적으로 Azure Stack에서 app Service는 무료 및 공유 작업자 계층을 지원합니다. 다른 작업자 계층을 추가 하려면 더 많은 작업자 역할을 추가 해야 합니다.

Azure Stack 설치에서 기본 App Service를 사용 하 여 배포 된 내용을 확실 하지 않은 경우의 추가 정보를 검토할 수 있습니다 합니다 [App Service에서 Azure Stack 개요](azure-stack-app-service-overview.md)합니다.

Azure Stack의 azure App Service는 Virtual Machine Scale Sets를 사용 하 여 모든 역할을 배포 하 고이 워크 로드의 크기 조정 기능의 이점을 활용으로. 따라서 앱 서비스 관리자를 통해 수행 됩니다 작업자 계층의 모든 크기 조정

> [!IMPORTANT]
> 현재 Azure Stack 릴리스 정보에서 식별 한 대로 포털에서 가상 머신 확장 집합 확장, 따라서 PowerShell 예제를 사용 하 여 스케일 아웃할 수는 없습니다.
>
>

## <a name="add-additional-workers-with-powershell"></a>PowerShell 사용 하 여 추가 작업자를 추가 합니다.

1. [PowerShell에서 Azure Stack 관리 환경 설정](azure-stack-powershell-configure-admin.md)

2. 확장 집합 규모를 확장 하려면이 예제를 사용 합니다.
   ```powershell
   
    ##### Scale out the AppService Role instances ######
   
    # Set context to AzureStack admin.
    Login-AzureRmAccount -EnvironmentName AzureStackAdmin
                                                 
    ## Name of the Resource group where AppService is deployed.
    $AppServiceResourceGroupName = "AppService.local"

    ## Name of the ScaleSet : e.g. FrontEndsScaleSet, ManagementServersScaleSet, PublishersScaleSet , LargeWorkerTierScaleSet,      MediumWorkerTierScaleSet, SmallWorkerTierScaleSet, SharedWorkerTierScaleSet
    $ScaleSetName = "SharedWorkerTierScaleSet"

    ## TotalCapacity is sum of the instances needed at the end of operation. 
    ## e.g. if your VMSS has 1 instance(s) currently and you need 1 more the TotalCapacity should be set to 2
    $TotalCapacity = 2  

    # Get current scale set
    $vmss = Get-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -VMScaleSetName $ScaleSetName

    # Set and update the capacity
    $vmss.sku.capacity = $TotalCapacity
    Update-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -Name $ScaleSetName -VirtualMachineScaleSet $vmss 
   ```    

   > [!NOTE]
   > 이 단계는 다양 한 역할의 유형 및 인스턴스 수에 따라 완료 하는 데 시간이 걸릴 수 있습니다.
   >
   >

3. 앱 서비스 관리에서 새 역할 인스턴스의 상태를 모니터링, 개별 역할 인스턴스의 상태를 확인 하려면 목록에서 역할 종류를 클릭 합니다.

## <a name="add-additional-workers-directly-within-the-app-service-resource-provider-admin"></a>앱 서비스 리소스 공급자 관리자 내에서 직접 추가 작업자를 추가 합니다.

1. 서비스 관리자로 Azure Stack 관리 포털에 로그인 합니다.

2. 이동할 **App Services**합니다.

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)

3. **역할**을 클릭합니다. 여기에 배포 하는 모든 App Service 역할의 분석 결과 볼 있습니다.

4. 크기를 조정 하 고 클릭 하려는 형식의 행을 마우스 오른쪽 단추로 클릭 **ScaleSet**합니다.

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)

5. 클릭 **크기 조정**를 확장 하 고 클릭 하려는 인스턴스의 수를 선택 **저장**합니다.

    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. Azure Stack에서 app Service는 이제 추가 Vm을 추가, 구성, 모든 필수 소프트웨어를 설치 및이 프로세스가 완료 되 면 준비 된 상태로 표시 합니다. 이 프로세스는 약 80 분 걸릴 수 있습니다.

7. 작업자를 확인 하 여 새 역할의 준비의 진행 상황을 모니터링할 수 있습니다 합니다 **역할** 블레이드입니다.

## <a name="result"></a>결과

완벽 하 게 배포 되 고 준비가 되 면 작업 자가 해당 워크 로드를 배포 하는 사용자에 대해 사용할 수 있습니다. 다음 기본적으로 사용할 수 있는 여러 가격 책정 계층의 예를 보여줍니다. 특정 작업자 계층에 대 한 사용 가능한 작업 자가 없는 경우 해당 가격 책정 계층을 선택 하는 옵션이 제공 되지 않습니다.

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> 프런트 엔드 또는 게시자 역할 관리를 확장 하는 해당 역할 유형의 규모 확장 해야 합니다를 추가 합니다. 
>
>

관리, 프런트 엔드 또는 게시자 역할을 확장 하려면 적절 한 역할 유형을 선택한 후 동일한 단계를 수행 합니다. 컨트롤러는 확장 집합으로 배포 되지 않습니다 및 따라서 두 개의 배포 해야 모든 프로덕션 배포에 대 한 설치 시.

### <a name="next-steps"></a>다음 단계

[배포 소스 구성](azure-stack-app-service-configure-deployment-sources.md)
