---
title: 'Azure 스택 앱 서비스: 도메인 업데이트 오류 | Microsoft Docs'
description: 오류 도메인 간에 Azure 스택에 Azure 앱 서비스를 재배포 하는 방법
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2018
ms.author: anwestg
ms.openlocfilehash: ce57e153dcab6a386150ebefe1ecb4a018514247
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130373"
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>오류 도메인 간에 Azure 스택에 Azure 앱 서비스를 재배포 하는 방법

*적용 대상: Azure 스택 시스템 통합*

1802 업데이트를 Azure 스택 이제 배포 작업의 오류 도메인 있는 기능을 지원 고가용성을 위해 중요 합니다.

>[!IMPORTANT]
>오류 도메인 지원을 활용 하려면 통합 하는 Azure 스택 시스템 1802 업데이트 해야 합니다. 이 문서는 1802 업데이트 하기 전에 완료 된 앱 서비스 리소스 공급자 배포에만 적용 됩니다. Azure 스택 1802 업데이트가 적용 된 후 Azure 스택 앱 서비스를 배포한 경우 리소스 공급자 장애 도메인에서 이미 배포 됩니다.

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>앱 서비스 리소스 공급자 오류 도메인 간에 균형을 다시 조정

크기 집합 앱 서비스 리소스 공급자에 대 한 배포를 재배포 하려면 각 크기 집합에 대 한이 문서의 단계를 수행 해야 합니다. 기본적으로 scaleset 이름은 다음과 같습니다.

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

>[!NOTE]
> 인스턴스를 일부 작업자 계층 크기 집합의 배포를 설정 하지 않은 경우 이러한 크기 집합 균형을 다시 조정할 필요가 없습니다. 나중에 확장할 크기 집합이 올바르게 균형 됩니다.

크기 집합을 확장 하려면 다음이 단계를 따르십시오.

1. Azure 스택 관리자 포털에 로그인 합니다.
2. **추가 서비스**를 선택합니다.
3. 계산의 경우, 아래에서 선택 **가상 컴퓨터 크기 집합**합니다. 앱 서비스 배포의 일부로 배포 된 기존 크기 집합 인스턴스 개수 정보와 함께 나열 됩니다. 다음 화면 캡처 크기 집합의 예를 보여 줍니다.

      ![가상 컴퓨터 크기 조정 설정 UX에 나열 된 azure 앱 서비스 크기 집합][1]

4. 각 집합을 확장 합니다. 예를 들어 세 개의 기존 인스턴스 크기 집합에 있는 경우 수평 확장 해야 합니다 6으로 오류 도메인 세 개의 새 인스턴스를 배포 하도록 합니다. 다음 PowerShell 예에서는 크기 집합을 확장 하려면 아웃 보여 줍니다.

   ```powershell
   Add-AzureRmAccount -EnvironmentName AzureStackAdmin 

   # Get current scale set
   $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

   # Set and update the capacity of your scale set
   $vmss.sku.capacity = 6
   Update-AzureRmVmss -ResourceGroupName AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
   ```

   >[!NOTE]
   >이 단계는 다양 한 유형의 역할 및 인스턴스 수에 따라 완료 하는 데 시간이 걸릴 수 있습니다.

5. **앱 서비스 관리 역할**, 새 역할 인스턴스 상태를 모니터링 합니다. 역할 인스턴스 상태를 확인 하려면 역할 유형 목록에서 선택

    ![Azure 스택 역할에서 azure 앱 서비스][2]

6. 새 역할 인스턴스 상태가 **준비**로 돌아가서, **가상 컴퓨터 크기 집합** 및 **삭제** 이전 역할 인스턴스.

7. 에 대 한 이러한 단계를 반복 **각** 가상 컴퓨터 크기 집합입니다.

## <a name="next-steps"></a>다음 단계

또한을 수행 하려면 다른 [플랫폼 서비스 (PaaS) 서비스로](azure-stack-tools-paas-services.md)합니다.

* [SQL Server 리소스 공급자](azure-stack-sql-resource-provider-deploy.md)
* [MySQL 리소스 공급자](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
