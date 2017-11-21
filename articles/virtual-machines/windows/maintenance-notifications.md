---
title: "Azure에서 Windows VM에 대한 유지 관리 알림 처리 | Microsoft Docs"
description: "Azure에서 실행 중인 Windows 가상 컴퓨터에 대한 유지 관리 알림을 확인하고 셀프 서비스 유지 관리를 시작합니다."
services: virtual-machines-windows
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: zivr
ms.openlocfilehash: 80c029866f3d28712be823692f3bf4ce6e210405
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2017
---
# <a name="handling-planned-maintenance-notifications-for-windows-virtual-machines"></a>Windows 가상 컴퓨터에 대한 계획된 유지 관리 알림 처리

Azure에서는 가상 컴퓨터에 대한 호스트 인프라의 안정성, 성능 및 보안을 향상시키기 위해 주기적으로 업데이트를 수행합니다. 업데이트란 호스팅 환경의 패치 적용 또는 하드웨어의 업그레이드 및 서비스 해제와 같은 변경 내용을 말합니다. 이러한 업데이트 중 대다수는 호스트된 가상 컴퓨터에 영향을 미치지 않고 수행됩니다. 그러나 업데이트가 다음 항목에 영향을 미치는 경우가 있습니다.

- 유지 관리를 다시 부팅하지 않아도 되는 경우 Azure에서는 호스트를 업데이트하는 동안 바로 마이그레이션을 사용하여 VM을 일시 중지합니다.

- 유지 관리를 다시 부팅해야 하는 경우 유지 관리가 계획된 시기에 대해 알림을 받을 수 있습니다. 이러한 경우에 사용자가 원하는 시점에 스스로 유지 관리를 시작할 수 있는 기간도 지정됩니다.


다시 부팅해야 하는 계획된 유지 관리는 웨이브에서 예약됩니다. 각 웨이브는 범위(지역)이 다릅니다.

- 웨이브는 고객에게 알림을 보내면서 시작합니다. 기본적으로 알림은 구독 소유자 및 공동 소유자에게 전송됩니다. 추가 수신자나 전자 메일, SMS 및 웹후크와 같은 메시징 옵션을 추가할 수 있습니다.  
- 알림 후 바로 셀프 서비스 기간이 설정됩니다. 이 기간 동안 이 웨이브에 포함되어 있는 가상 컴퓨터를 찾고, 사전 재배포를 사용하여 유지 관리를 시작할 수 있습니다. 
- 셀프 서비스 기간에 이어 예약된 유지 관리 기간이 시작됩니다. 이때 Azure는 가상 컴퓨터에 필요한 유지 관리를 예약하고 적용합니다. 

두 기간이 존재하는 이유는 Azure에서 유지 관리를 자동으로 시작하는 시기를 파악하면서 유지 관리를 시작하고 가상 컴퓨터를 다시 부팅하는 데 충분한 시간을 제공하기 위한 것입니다.


Azure Portal, PowerShell, REST API 및 CLI를 사용하여 사용자 VM에 대한 유지 관리 기간을 쿼리하고, 셀프 서비스 유지 관리를 시작할 수 있습니다.

 > [!NOTE]
 > 유지 관리를 시작하려고 시도하다가 실패한 경우 Azure는 사용자 VM을 **건너뜀**으로 표시하고 예약된 유지 관리 기간 동안 재부팅하지 않습니다. 대신 새 일정으로 나중에 연결됩니다. 


[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="check-maintenance-status-using-powershell"></a>PowerShell을 사용하여 유지 관리 상태 확인

또한 Azure Powershell을 사용하여 VM이 유지 관리에 대해 예약된 시기를 볼 수 있습니다. 계획된 유지 관리 정보는 `-status` 매개 변수를 사용하는 경우 [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) cmdlet에서 확인할 수 있습니다.
 
유지 관리 정보는 계획된 유지 관리가 있는 경우에만 반환됩니다. VM에 영향을 미치는 예약된 유지 관리가 없는 경우 cmdlet은 유지 관리 정보를 반환하지 않습니다. 

```powershell
Get-AzureRmVM -ResourceGroupName rgName -Name vmName -Status
```

MaintenanceRedeployStatus의 다음과 같은 속성이 반환됩니다. 
| 값 | 설명   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | 이번에 VM에서 유지 관리를 시작할 수 있는지 여부를 나타냅니다. ||
| PreMaintenanceWindowStartTime         | VM에서 유지 관리를 시작할 수 있을 때 유지 관리 셀프 서비스 기간의 시작 시간입니다. ||
| PreMaintenanceWindowEndTime           | VM에서 유지 관리를 시작할 수 있을 때 유지 관리 셀프 서비스 기간의 종료 시간입니다. ||
| MaintenanceWindowStartTime            | VM에서 유지 관리를 시작할 수 있을 때 유지 관리 예약 기간의 시작 시간입니다. ||
| MaintenanceWindowEndTime              | VM에서 유지 관리를 시작할 수 있을 때 유지 관리 예약 기간의 종료 시간입니다. ||
| LastOperationResultCode               | VM에서 유지 관리를 시작하는 마지막 시도의 결과입니다. ||



또한 [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm)을 사용하고 VM을 지정하지 않고 리소스 그룹의 모든 VM에 대한 유지 관리 상태를 가져올 수 있습니다.
 
```powershell
Get-AzureRmVM -ResourceGroupName rgName -Status
```

다음 PowerShell 함수는 사용자의 구독 ID를 사용하고 유지 관리에 예약된 VM의 목록을 출력합니다.

```powershell

function MaintenanceIterator
{
    Select-AzureRmSubscription -SubscriptionId $args[0]

    $rgList= Get-AzureRmResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        $vmList = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>PowerShell을 사용하여 VM에서 유지 관리 시작

이전 섹션의 함수 정보를 사용하여 **IsCustomerInitiatedMaintenanceAllowed**가 true로 설정되어 있는 경우 VM에서 유지 관리를 시작합니다.

```powershell
Restart-AzureRmVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>클래식 배포

클래식 배포 모델을 사용하여 배포된 레거시 VM이 아직 있는 경우 PowerShell을 사용하여 VM을 쿼리하고 유지 관리를 시작할 수 있습니다.

VM에 대한 유지 관리 상태를 가져오려면 다음을 입력합니다.

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

클래식 VM에 대한 유지 관리를 시작하려면 다음을 입력합니다.

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```


## <a name="faq"></a>FAQ


**Q: 왜 내 가상 컴퓨터를 지금 다시 부팅해야 하나요?**

**A:** Azure 플랫폼에 대한 대부분의 업데이트와 업그레이드는 가상 컴퓨터의 가용성에 영향을 주지 않으나, Azure에서 호스팅되는 가상 컴퓨터를 불가피하게 다시 부팅해야 하는 경우가 있습니다. 서버 재시작이 필요한 여러 변경 사항이 누적되면 가상 컴퓨터 다시 부팅이 발생하게 됩니다.

**Q: 가용성 집합을 사용한 고가용성 제안을 따르는 것이 안전한가요?**

**A:** 가용성 집합 또는 가상 컴퓨터 확장 집합에 배포된 가상 컴퓨터에는 UD(업데이트 도메인) 개념이 있습니다. 유지 관리를 수행할 때 Azure는 UD 제약 조건을 적용하고 다른 UD(동일한 가용성 집합 내)의 가상 컴퓨터를 다시 부팅하지 않습니다.  또 Azure는 다음 가상 컴퓨터 그룹으로 이동하기 전에 30분 이상 대기합니다. 

고가용성에 대한 자세한 내용은 Azure에서 Windows 가상 컴퓨터의 가용성 관리 또는 Azure에서 Linux 가상 컴퓨터의 가용성 관리를 참조하세요.

**Q: 다른 지역에 재해 복구 설정이 있습니다. 안전한가요?**

**A:** 각 Azure 지역은 동일한 지리적 위치 내의 다른 지역(예: 미국, 유럽 또는 아시아)과 쌍을 이룹니다. 계획된 Azure 업데이트는 가동 중지 및 응용 프로그램 중단 위험을 최소화하기 위해 한 번에 한 쌍의 지역으로 롤아웃됩니다. 계획된 유지 관리 중에는 Azure가 사용자의 유지 관리 시작을 위해 유사한 창을 예약할 수 있으나, 예약된 유지 관리 창은 쌍을 이룬 지역 간에 차이가 있습니다.  

Azure 지역에 관한 자세한 내용은 Azure에서 가상 컴퓨터의 지역 및 가용성을 참조하세요.  여기에서 전체 지역 쌍 목록을 확인할 수 있습니다.

**Q: 계획된 유지 관리에 관한 알림은 어떻게 받나요?**

**A:** 계획된 유지 관리 주기는 하나 이상의 Azure 지역에 예약을 설정하는 것에서 출발합니다. 곧 이메일 알림이 구독 소유자에게 전달됩니다(구독당 1개 이메일). 이 알림에 대한 추가 채널과 받는 사람은 활동 로그 경고를 통해 구성할 수 있습니다.  계획된 유지 관리가 이미 예약된 지역에 가상 컴퓨터를 배포하는 경우 알림이 전달되지 않으므로 VM의 유지 관리 상태를 확인해야 합니다.

**Q: 포털, Powershell 또는 CLI에서 계획된 유지 관리의 표시가 전혀 나타나지 않습니다. 무엇이 문제인가요?**

**A:** 계획된 유지 관리 관련 정보는 영향을 받게 되는 VM에 대해서만 계획된 유지 관리 주기 중에 제공됩니다. 즉 데이터가 표시되지 않는다면 유지 관리 주기가 이미 완료되었거나(또는 시작되지 않음) 가상 컴퓨터가 이미 업데이트된 서버에서 호스팅되는 것일 수 있습니다.

**Q: 내 가상 컴퓨터에에서 유지 관리를 시작해야 하나요?**

**A:** 일반적으로 클라우드 서비스, 가용성 집합 또는 가상 컴퓨터 확장 집합에 배포된 워크로드는 계획된 유지 관리에 탄력적입니다.  계획된 유지 관리 동안에는 단일 업데이트 도메인만 지정된 시간에 영향을 받습니다. 영향을 받는 업데이트 도메인의 순서가 반드시 순차적으로 나열되지는 않습니다.

다음과 같은 경우 직접 유지 관리를 시작하려 할 수 있습니다.
- 응용 프로그램이 단일 가상 컴퓨터에서 실행 중이며 일과 외 시간에 모든 유지 관리를 적용해야 함
- SLA의 일부로 유지 관리 시간을 조정해야 함
- 한 가용성 집합 안에서도 각 VM 다시 시작 사이에 30분 이상이 필요함
- 더 신속한 유지 관리 완료를 위해 전체 응용 프로그램(다중 계층, 다중 업데이트 도메인)을 작동 중지하려 함

**Q: 내 가상 컴퓨터가 정확히 언제 영향을 받는지 확인할 수 있나요?**

**A:** 예약을 설정할 때 며칠의 시간 창을 정의합니다. 그러나 이 창 내에서의 정확한 서버(및 VM) 순서는 알 수 없습니다. VM에 해당하는 정확한 시간을 알고자 하는 고객은 예약된 이벤트를 사용하고 가상 컴퓨터 안에서 쿼리하여 VM 다시 부팅에 대한 10분 전 알림을 수신할 수 있습니다.
  
**Q: 가상 컴퓨터를 다시 부팅하는 데 얼마나 걸리나요?**

**A:** VM의 크기에 따라 다시 부팅은 최대 몇 분이 소요될 수 있습니다. 클라우드 서비스, 확장 집합 또는 가용성 집합을 사용하는 경우 각 VM 그룹(UD) 사이에 30분이 주어집니다. 

**Q: 클라우드 서비스, 확장 집합 또는 Service Fabric의 경우 어떻게 되나요?**

**A:** 이러한 플랫폼은 계획된 유지 관리의 영향을 받지만, 이런 플랫폼을 사용하는 고객은 특정 시간에 단일 UD(업그레이드 도메인)의 VM만 영향을 받는다면 안전하다고 할 수 있습니다.  

**Q: 하드웨어 서비스 해제에 관한 이메일을 받았습니다. 계획된 유지 관리와 같은가요?**

**A:** 하드웨어 서비스 해제는 계획된 유지 관리 이벤트이나 아직 이 사용 사례를 새로운 환경에 적용하지 않았습니다.  두 가지 계획된 유지 관리 주기에 대해 유사한 이메일 두 개를 수신하면 고객에게 혼동을 초래할 수 있습니다.

**Q: 내 VM에 관한 유지 관리 정보가 전혀 표시되지 않았습니다. 무엇이 문제인가요?**

**A:** VM에 대한 유지 관리 정보가 전혀 표시되지 않는 데는 몇 가지 이유가 있습니다.
1.  Microsoft 내부로 표시된 구독을 사용하고 있습니다.
2.  VM에 유지 관리가 예약되어 있지 않습니다. 유지 관리 주기가 종료, 취소 또는 수정되면 VM이 더 이상 해당 주기의 영향을 받지 않습니다.
3.  VM 목록 보기에 '유지 관리' 열을 추가할 필요는 없습니다. 기본 보기에 이 열을 추가했지만, 기본이 아는 열을 보도록 구성한 고객은 수동으로 **유지 관리** 열을 VM 목록 보기에 추가해야 합니다.

**Q: 내 VM에 두 번째 유지 관리가 예약되었습니다. 그 이유는 무엇일까요?**

**A:** 유지 관리 재배포를 이미 완료한 후에도 VM에 유지 관리가 예약되는 몇 가지 사용 사례가 있습니다.
1.  유지 관리 주기를 취소하고 다른 페이로드에서 다시 시작합니다. 오류가 발생한 페이로드를 탐지하여 단순히 추가 페이로드를 배포해야 합니다.
2.  하드웨어 오류로 인해 VM이 다른 노드에 대해 *조정된 서비스*입니다.
3.  VM을 중지(할당 취소)하고 다시 시작하도록 선택했습니다.
4.  VM에 대해 **자동 종료**를 실행했습니다.


## <a name="next-steps"></a>다음 단계

[예약된 이벤트](scheduled-events.md)를 사용하여 VM 내에서 유지 관리 이벤트에 등록하는 방법을 알아보세요.
