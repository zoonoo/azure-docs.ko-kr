---
title: Azure Network Watcher에서 네트워크 보안 그룹 흐름 로그에 대한 Storage Blob 삭제 | Microsoft Docs
description: 이 문서에서는 Azure Network Watcher의 보존 정책 기간을 벗어난 네트워크 보안 그룹 흐름 로그 Storage Blob을 삭제하는 방법을 설명합니다.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9ef7160c667ed9fa5f932b49c295a1247ff94c30
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110665352"
---
# <a name="delete-network-security-group-flow-log-storage-blobs-in-network-watcher"></a>Network Watcher에서 네트워크 보안 그룹 흐름 로그 Storage Blob 삭제

스토리지 계정에서 흐름 로그를 수동으로 삭제해야 하는 경우 아래 PowerShell 스크립트를 사용할 수 있습니다.
이 스크립트는 사용자가 지정한 기존 보존 정책보다 오래된 Storage Blob만 삭제합니다.

## <a name="run-powershell-script-to-delete-nsg-flow-logs"></a>PowerShell 스크립트를 실행하여 NSG 흐름 로그 삭제
 
다음 스크립트를 복사하여 현재 작업 디렉터리와 같은 위치에 저장합니다. 

```powershell
# This powershell script deletes all NSG flow log blobs that should not be retained anymore as per configured retention policy.
# While configuring NSG flow logs on Azure portal, the user configures the retention period of NSG flow log blobs in
# their storage account (in days).
# This script reads all blobs and deletes blobs that are not to be retained (outside retention window)
# if the retention days are zero; all blobs are retained forever and hence no blobs are deleted.
#
#

param (
        [string] [Parameter(Mandatory=$true)]  $SubscriptionId,
        [string] [Parameter(Mandatory=$true)]  $Location,
        [switch] [Parameter(Mandatory=$false)] $Confirm
    )

Login-AzAccount

$SubId = Get-AzSubscription| Where-Object {$_.Id.contains($SubscriptionId.ToLower())}

if ($SubId.Count -eq 0)
{
    Write-Error 'The SubscriptionId does not exist' -ErrorAction Stop
}

Set-AzContext -SubscriptionId $SubscriptionId

$NsgList = Get-AzNetworkSecurityGroup | Where-Object {$_.Location -eq $Location}
$NW = Get-AzNetworkWatcher | Where-Object {$_.Location -eq $Location}

$FlowLogsList = @()
foreach ($Nsg in $NsgList)
{
    # Query Flow Log Status which are enabled
    $NsgFlowLog = Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $Nsg.Id | Where-Object {$_.Enabled -eq "True"}
    if ($NsgFlowLog.Count -gt 0)
    {
        $FlowLogsList +=  $NsgFlowLog
        Write-Output ('Enabled NSG found: ' +  $NsgFlowLog.TargetResourceId)
    }
}

foreach ($Psflowlog in $FlowLogsList)
{
    $RetentionDays = $Psflowlog.RetentionPolicy.Days
    if ($RetentionDays -le 0)
    {
        continue
    }

    $Strings = $Psflowlog.StorageId -split '/'
    $RGName = $Strings[4]
    $StorageAccountName = $Strings[-1]

    $Key = (Get-AzStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName).Value[1]
    $StorageAccount = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $Key

    $ContainerName = 'insights-logs-networksecuritygroupflowevent'  
    $BLobsList = Get-AzStorageBlob -Container $ContainerName -Context $StorageAccount.Context

    $TargetBLobsList = $BLobsList | Where-Object {$_.Name.contains($Psflowlog.TargetResourceId.ToUpper())}

    $RetentionDate = Get-Date
    $RetentionDate = $RetentionDate.AddDays(-1*$RetentionDays)
    $RetentionDateInUTC = $RetentionDate.ToUniversalTime()

    foreach ($Blob in $TargetBLobsList)
    {
        $BlobLastModifietedDTinUTC = [datetime]$Blob.LastModified.UtcDateTime

        if ($BlobLastModifietedDTinUTC -ge  $RetentionDateInUTC)
        {
            Write-Output ($Blob.Name + '===>' + $BlobLastModifietedDTinUTC  + ' ===> RETAINED')
            continue
        }

        if ($Confirm)
        {
            Write-Output (Blob to be deleted: $Blob.Name)
            $Confirmation = Read-Host "Are you sure you want to remove this blob (Y/N)?"
        }

        if ((-not $Confirm) -or ($Confirmation -eq 'Y'))
        {
            Write-Output ($Blob.Name + '===>' + $BlobLastModifietedDTinUTC  + ' ===> DELETED')
            Remove-AzStorageBlob -Container $ContainerName -Context $StorageAccount.Context -Blob $Blob.Name
        }
        else
        {
            Write-Output ($Blob.Name + '===>' + $BlobLastModifietedDTinUTC  + ' ===> RETAINED')
        }
    }
}

Write-Output ('Retention policy for all NSGs evaluated and completed successfully')
```

1. 필요에 따라 스크립트에서 다음 매개 변수를 입력합니다.
   - **SubscriptionId** [필수]: NSG 흐름 로그 blob을 삭제할 구독 ID입니다.
   - **Location** [필수]: NSG 흐름 로그 blob을 삭제하려는 NSG 영역의 _위치 문자열_ 입니다. 이 정보는 Azure Portal 또는 [GitHub](https://github.com/Azure/azure-extensions-cli/blob/beb3d3fe984cfa9c7798cb11a274c5337968cbc5/regions.go#L23)에서 볼 수 있습니다.
   - **확인** [선택 사항]: 각 Storage Blob의 삭제를 수동으로 확인하려면 확인 플래그를 전달합니다.

1. 스크립트 파일이 **Delete-NsgFlowLogsBlobs.ps1** 로 저장된 다음 예제와 같이 저장된 스크립트를 실행합니다.
   ```
   .\Delete-NsgFlowLogsBlobs.ps1 -SubscriptionId <subscriptionId> -Location  <location> -Confirm
   ```
    
## <a name="next-steps"></a>다음 단계
- 고객은 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 또는 [Azure Automation](https://azure.microsoft.com/services/automation/)을 사용하여 스크립트 실행을 자동화할 수 있습니다.
- NSG 로깅에 대한 자세한 내용은 [NSG(네트워크 보안 그룹)에 대한 Azure Monitor 로그](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)를 참조하세요.

