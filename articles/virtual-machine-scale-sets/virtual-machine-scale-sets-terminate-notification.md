---
title: Azure 가상 시스템 규모 집합 인스턴스에 대한 알림 종료
description: Azure 가상 시스템 규모 집합 인스턴스에 대한 종료 알림을 사용하도록 설정하는 방법 알아보기
author: avirishuv
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: 6023e9bf7539b79446d0135ba731b61be166dd6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250752"
---
# <a name="terminate-notification-for-azure-virtual-machine-scale-set-instances"></a>Azure 가상 시스템 규모 집합 인스턴스에 대한 알림 종료
스케일 세트 인스턴스는 인스턴스 종료 알림을 수신하도록 선택하고 미리 정의된 지연 시간 시간을 종료 작업으로 설정할 수 있습니다. 종료 알림은 Azure 메타데이터 서비스 – [예약된 이벤트를](../virtual-machines/windows/scheduled-events.md)통해 전송되며, 이 이벤트는 재부팅 및 재배포와 같은 영향 있는 작업에 대한 알림 및 지연을 제공합니다. 솔루션은 예약된 이벤트 목록에 다른 이벤트인 종료를 추가하고, 종료 이벤트의 관련 지연은 스케일 세트 모델 구성의 사용자가 지정한 지연 제한에 따라 달라집니다.

피처에 등록되면 인스턴스가 삭제되기 전에 지정된 시간 시간이 만료될 때까지 기다릴 필요가 없습니다. 종료 알림을 받은 후 종료 시간 표시가 만료되기 전에 언제든지 인스턴스를 삭제하도록 선택할 수 있습니다.

## <a name="enable-terminate-notifications"></a>알림 종료 사용
아래 예제에서 자세히 설명한 것처럼 스케일 집합 인스턴스에서 종료 알림을 사용하도록 설정하는 방법에는 여러 가지가 있습니다.

### <a name="azure-portal"></a>Azure portal

다음 단계는 새 축척 집합을 만들 때 종료 알림을 사용할 수 있습니다. 

1. 가상 **컴퓨터 크기 집합으로**이동합니다.
1. **+ 추가를** 선택하여 새 축척 세트를 만듭니다.
1. **관리** 탭으로 이동합니다. 
1. 인스턴스 **종료** 섹션을 찾습니다.
1. **예를 들어 종료 알림은** **에서**를 선택합니다.
1. **종료 지연(분)의**경우 원하는 기본 시간 초과를 설정합니다.
1. 새 축척 세트 작성이 완료되면 **검토 + 만들기** 단추를 선택합니다. 

> [!NOTE]
> Azure Portal의 기존 축척 집합에서 종료 알림을 설정할 수 없습니다.

### <a name="rest-api"></a>REST API

다음 예제는 축척 집합 모델에서 종료 알림을 활성화합니다.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "virtualMachineProfile": {
            "scheduledEventsProfile": {
                "terminateNotificationProfile": {
                    "notBeforeTimeout":"PT5M",
                    "enable":true
                }
            }
        }
    }        
}

```

위의 블록은 스케일 세트의 모든 인스턴스에서 종료 작업에 대해 *5분(PT5M으로*표시)의 시간 초과 지연을 지정합니다. 필드 *notBeforeTimeout* ISO 8601 형식에서 5~15분 사이의 값을 취할 수 있습니다. 위에서 설명한 *terminateNotificationProfile에서* *notBeforeTimeout* 속성을 수정하여 종료 작업의 기본 시간 초과를 변경할 수 있습니다.

스케일 세트 모델에서 *예약된이벤트프로필을* 활성화하고 *notBeforeTimeout을*설정한 후 변경 사항을 반영하도록 개별 인스턴스를 [최신 모델로](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) 업데이트합니다.

> [!NOTE]
>규모 집합 인스턴스에서 종료 알림은 API 버전 2019-03-01 이상에서만 사용할 수 있습니다.

### <a name="azure-powershell"></a>Azure PowerShell
새 축척 집합을 만들 때 [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) cmdlet을 사용하여 축척 집합에서 종료 알림을 활성화할 수 있습니다.

이 샘플 스크립트는 구성 파일: [전체 가상 시스템 축척 집합 만들기를](./scripts/powershell-sample-create-complete-scale-set.md)사용하여 축척 집합 및 관련 리소스를 만드는 데 대해 살펴봅니다. 확장 집합을 만들기 위한 구성 개체에 *[종료일정이벤트* 및 *TerminateScheduledEventNotBeforeTimeoutInMinutes]을* 추가하여 구성 종료 알림을 제공할 수 있습니다. 다음 예제에서는 지연 시간 초과가 10분인 기능을 사용할 수 있습니다.

```azurepowershell-interactive
New-AzVmssConfig `
  -Location "VMSSLocation" `
  -SkuCapacity 2 `
  -SkuName "Standard_DS2" `
  -UpgradePolicyMode "Automatic" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 10
```

[업데이트-AzVmss](/powershell/module/az.compute/update-azvmss) cmdlet을 사용하여 기존 축척 집합에서 종료 알림을 활성화합니다.

```azurepowershell-interactive
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -TerminateScheduledEvents $true
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 15
```
위의 예제는 기존 축척 집합에서 알림을 종료하고 종료 이벤트에 대해 15분 의 시간 초과를 설정합니다.

축척 집합 모델에서 예약된 이벤트를 사용하도록 설정하고 시간 시간을 설정한 후 변경 내용을 반영하도록 개별 인스턴스를 [최신 모델로](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) 업데이트합니다.

### <a name="azure-cli-20"></a>Azure CLI 2.0

다음 예제는 새 축척 집합을 만드는 동안 종료 알림을 사용하도록 설정하기 위한 예제입니다.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --terminate-notification-time 10
```

위의 예제는 먼저 리소스 그룹을 만들고 10분 기본 시간 초과에 대해 종료 알림이 활성화된 새 축척 집합을 만듭니다.

다음 예제는 기존 축척 집합에서 종료 알림을 사용하도록 설정하기 위한 예제입니다.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-terminate-notification true \
  --terminate-notification-time 10
```

## <a name="get-terminate-notifications"></a>알림 종료 받기

종료 알림은 Azure 메타데이터 서비스인 [예약된 이벤트를](../virtual-machines/windows/scheduled-events.md)통해 전달됩니다. Azure 메타데이터 서비스는 VM 내에서 액세스할 수 있는 REST 엔드포인트를 사용하여 Virtual Machines 실행에 대한 정보를 공개합니다. 이 정보는 라우팅이 불가능한 IP를 통해 제공되므로 VM 외부에 노출되지 않습니다.

예약된 이벤트는 이벤트를 처음 요청할 때 스케일 세트에 대해 활성화됩니다. 최대 2분의 첫 번째 통화에서 지연된 응답을 기대할 수 있습니다. 끝점을 주기적으로 쿼리하여 예정된 유지 관리 이벤트 및 진행 중인 유지 관리 활동의 상태를 검색합니다.

스케일 세트 인스턴스가 24시간 동안 요청을 하지 않으면 예약된 이벤트가 스케일 집합에 대해 비활성화됩니다.

### <a name="endpoint-discovery"></a>엔드포인트 검색
VNET 지원 VM의 경우 메타데이터 서비스는 정적 비라우팅 IP, 169.254.169.254에서 사용할 수 있습니다.

예약된 이벤트의 최신 버전에 대한 전체 엔드포인트는 다음과 같습니다.
> 'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="query-response"></a>쿼리 응답
응답에는 예약된 이벤트의 배열이 포함됩니다. 빈 배열은 현재 예약된 이벤트가 없음을 의미합니다.

예약된 이벤트가 있는 경우 응답에 다음과 같은 이벤트의 배열이 포함됩니다. "종료" 이벤트의 경우 응답은 다음과 같습니다.
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
*문서화는* ETag이며 마지막 쿼리 이후 이벤트 페이로드가 변경되었는지 쉽게 검사할 수 있는 방법을 제공합니다.

위의 각 필드에 대한 자세한 내용은 [Windows](../virtual-machines/windows/scheduled-events.md#event-properties) 및 [Linux의](../virtual-machines/linux/scheduled-events.md#event-properties)예약된 이벤트 설명서를 참조하십시오.

### <a name="respond-to-events"></a>이벤트에 응답
예정된 이벤트를 학습하고 정상적으로 종료하기 위한 논리를 완료한 후에는 EventId를 사용하여 메타데이터 서비스에 POST 호출을 수행하여 미해결 이벤트를 승인할 수 있습니다. POST 호출은 Azure에 VM 삭제를 계속할 수 있음을 나타냅니다.

다음은 POST 요청 본문에 예상되는 json입니다. 요청에는 StartRequests 목록이 포함되어야 합니다. 각 StartRequest에는 신속히 수행하려는 이벤트에 대한 EventId가 포함되어 있습니다.
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

규모 집합의 모든 VM이 해당 VM에만 적합한 EventID만 승인하도록 합니다. VM은 [인스턴스 메타데이터를 통해](virtual-machine-scale-sets-instance-ids.md#instance-metadata-vm-name)자체 VM 이름을 얻을 수 있습니다. 이 이름은 "{scale-set-name}"이라는 형식을 취하며 위에서 설명한 쿼리 응답의 '리소스' 섹션에 표시됩니다.

[PowerShell](../virtual-machines/windows/scheduled-events.md#powershell-sample) 및 [Python을](../virtual-machines/linux/scheduled-events.md#python-sample)사용하여 이벤트를 쿼리하고 응답하기 위한 샘플 스크립트를 참조할 수도 있습니다.

## <a name="tips-and-best-practices"></a>팁과 모범 사례
-   '삭제' 작업에서만 알림 종료 - 모든 삭제 작업(수동 삭제 또는 자동 크기 조정 시작) 확장 집합이 *예약된EventsProfile을* 사용하도록 설정한 경우 종료 이벤트가 생성됩니다. 재부팅, 이미지 다시 이미지, 다시 배포 및 중지/할당 할당과 같은 다른 작업은 종료 이벤트를 생성하지 않습니다. 우선 순위가 낮은 VM의 경우 종료 알림을 사용할 수 없습니다.
-   시간 시간에 대한 필수 대기 없음 - 이벤트가 수신된 후 이벤트의 *NotBefore* 시간이 만료되기 전에 언제든지 종료 작업을 시작할 수 있습니다.
-   시간 시간에 필수 삭제 – 이벤트가 생성된 후 시간 시간 값을 확장하는 기능은 없습니다. 시간 시간이 만료되면 보류 중인 종료 이벤트가 처리되고 VM이 삭제됩니다.
-   수정 가능한 시간 제한 값 - 스케일 세트 모델의 *notBeforeTimeout* 속성을 수정하고 VM 인스턴스를 최신 모델로 업데이트하여 인스턴스가 삭제되기 전에 언제든지 시간 제한 값을 수정할 수 있습니다.
-   보류 중인 모든 삭제 승인 – 승인되지 않은 VM_1 삭제 보류 중이고 VM_2 다른 종료 이벤트를 승인한 경우 VM_1 대한 종료 이벤트가 승인되거나 시간 시간이 경과할 때까지 VM_2 삭제되지 않습니다. VM_1 대한 종료 이벤트를 승인하면 VM_1 및 VM_2 모두 삭제됩니다.
-   모든 동시 삭제 승인 - 위의 예제를 확장하여 VM_1 VM_2 *NotBefore* 시간이 동일한 경우 두 종료 이벤트가 모두 승인되거나 시간 초과가 만료되기 전에 VM이 모두 삭제되지 않습니다.

## <a name="troubleshoot"></a>문제 해결
### <a name="failure-to-enable-scheduledeventsprofile"></a>예약된이벤트프로필을 사용하도록 설정하지 못합니다.
'VirtualMachineProfile' 형식의 개체에서 '예약된EventsProfile'이라는 오류 메시지가 있는 'BadRequest' 오류가 발생하면 스케일 집합 작업에 사용되는 API 버전을 확인합니다. 계산 API 버전 **2019-03-01** 이상이 필요합니다. 

### <a name="failure-to-get-terminate-events"></a>이벤트 종료 실패
예약된 이벤트를 통해 **종료** 이벤트를 얻지 못하는 경우 이벤트를 가져오는 데 사용되는 API 버전을 확인합니다. 메타데이터 서비스 API 버전 **2019-01-01** 이상은 이벤트 종료에 필요합니다.
>'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="getting-terminate-event-with-incorrect-notbefore-time"></a>잘못된 하지와 종료 이벤트를 얻기 시간 전에  
스케일 세트 모델에서 *예약된이벤트프로필을* 활성화하고 *notBeforeTimeout을*설정한 후 변경 사항을 반영하도록 개별 인스턴스를 [최신 모델로](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) 업데이트합니다.

## <a name="next-steps"></a>다음 단계
가상 머신 확장 집합에 [애플리케이션을 배포하는 방법](virtual-machine-scale-sets-deploy-app.md)에 대해 알아봅니다.
