---
title: 'Azure Stack의 app Service: 도메인 업데이트 오류 | Microsoft Docs'
description: 장애 도메인과 Azure App Service on Azure Stack을 재배포 하는 방법
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: jeffgilb
ms.reviewer: anwestg
ms.lastreviewed: 09/05/2018
ms.openlocfilehash: c8c81d95a1179efff3bfdab49c2bc63cb074f73b
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447891"
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>장애 도메인과 Azure App Service on Azure Stack을 재배포 하는 방법

*적용 대상: Azure Stack 통합 시스템*

1802 업데이트를 사용 하 여 Azure Stack 이제는 배포를 지원 워크 로드의 오류 도메인에 걸쳐 고가용성에 대 한 중요 한 기능입니다.

>[!IMPORTANT]
>장애 도메인 지원을 이용 하려면 Azure Stack 통합 시스템 1802로 업데이트 해야 합니다. 이 문서는 1802 업데이트 하기 전에 완료 된 App Service 리소스 공급자 배포에만 적용 됩니다. 1802 업데이트는 Azure Stack에 적용 된 후에 Azure Stack에서 App Service를 배포한 경우 리소스 공급자가 이미 오류 도메인 간에 분산 됩니다.

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>App Service 리소스 공급자는 장애 도메인 간에 균형 다시 맞추기

App Service 리소스 공급자에 대해 배포 된 확장 집합을 재배포 하려면 각 확장 집합에 대 한이 문서의 단계를 수행 해야 합니다. 기본적으로 확장 집합 이름은 다음과 같습니다.

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

>[!NOTE]
> 작업자 계층 확장 집합의 일부에 배포 된 인스턴스가 없으면 해당 확장 집합을 균형 다시 맞추기 필요가 없습니다. 올바르게를 나중에 확장할 때 확장 집합에 분산 된 됩니다.

확장 집합을 확장 하려면 다음이 단계를 수행 합니다.

1. Azure Stack 관리자 포털에 로그인 합니다.
1. **모든 서비스**를 선택합니다.
2. 에 **계산** 범주를 선택한 **가상 머신 확장 집합**합니다. App Service 배포의 일부분으로 배포 하는 기존 확장 집합 인스턴스 수 정보를 사용 하 여 나열 됩니다. 다음 화면 캡처에는 확장 집합의 예가 나와 있습니다.

      ![가상 머신 확장 집합 UX에 나열 된 azure App Service 확장 집합][1]

1. 각 집합을 확장 합니다. 예를 들어, 확장 집합의 세 가지 기존 인스턴스가 있는 경우 확장 해야 아웃 6 하므로 세 새 인스턴스가 오류 도메인 간에 배포 됩니다. 확장 집합 규모를 확장 하려면 아웃 다음 PowerShell 예제를 보여 줍니다.

   ```powershell
   Add-AzureRmAccount -EnvironmentName AzureStackAdmin 

   # Get current scale set
   $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

   # Set and update the capacity of your scale set
   $vmss.sku.capacity = 6
   Update-AzureRmVmss -ResourceGroupName AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
   ```

   >[!NOTE]
   >이 단계는 다양 한 역할의 유형 및 인스턴스 수에 따라 완료 하는 데 시간이 걸릴 수 있습니다.

1. **앱 서비스 관리 역할**, 새 역할 인스턴스의 상태를 모니터링 합니다. 역할 인스턴스의 상태를 확인 하려면 목록에서 역할 유형 선택

    ![Azure Stack 역할의 azure App Service][2]

1. 새 역할 인스턴스 상태가 **준비**돌아가서 **Virtual Machine Scale Set** 하 고 **삭제** 이전 역할 인스턴스.

1. 이러한 단계를 반복 **각** 가상 머신 확장 집합입니다.

## <a name="next-steps"></a>다음 단계

또한에서 사용할 수 있는 다른 [서비스 (PaaS) 플랫폼](azure-stack-tools-paas-services.md)합니다.

* [SQL Server 리소스 공급자](azure-stack-sql-resource-provider-deploy.md)
* [MySQL 리소스 공급자](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
