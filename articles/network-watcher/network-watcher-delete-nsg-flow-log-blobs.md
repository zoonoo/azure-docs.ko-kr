---
title: Azure 네트워크 감시자에서 네트워크 보안 그룹 흐름 로그에 대한 저장소 Blob 삭제 | 마이크로 소프트 문서
description: 이 문서에서는 Azure Network Watcher에서 보존 정책 기간을 벗어난 네트워크 보안 그룹 흐름 로그 저장소 Blob을 삭제하는 방법을 설명합니다.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2019
ms.author: damendo
ms.openlocfilehash: 6d535bcc2e0831baae658796f76c8087d74c6a85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587212"
---
# <a name="delete-network-security-group-flow-log-storage-blobs-in-network-watcher"></a>네트워크 감시자에서 네트워크 보안 그룹 흐름 로그 저장소 Blob 삭제

현재는 Network Watcher에 대한 [NSG(네트워크 보안 그룹) 흐름 로그](network-watcher-nsg-flow-logging-overview.md)가 보존 정책 설정에 따라 Blob 스토리지에서 자동으로 삭제되지 않는 문제가 있습니다. 이제 PowerShell 스크립트를 실행하여 이 문서에 설명된 대로 저장소 계정에서 흐름 로그를 수동으로 삭제해야 합니다.

## <a name="run-powershell-script-to-delete-nsg-flow-logs"></a>PowerShell 스크립트를 실행하여 NSG 흐름 로그를 삭제합니다.
 
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

1. 필요에 따라 스크립트에 다음 매개 변수를 입력합니다.
   - **SubscriptionId** [필수]: NSG 흐름 로그 Blob을 삭제하려는 구독 ID입니다.
   - **위치** [필수]: NSG 흐름 로그 Blob을 삭제하려는 NSG 영역의 _위치 문자열입니다._ 이 정보는 Azure 포털 또는 [GitHub](https://github.com/Azure/azure-extensions-cli/blob/beb3d3fe984cfa9c7798cb11a274c5337968cbc5/regions.go#L23)에서 볼 수 있습니다.
   - **[선택** 사항] 확인: 각 저장소 Blob의 삭제를 수동으로 확인하려면 확인 플래그를 전달합니다.

1. 다음 예제와 같이 저장된 스크립트를 실행하여 스크립트 파일이 **삭제-NsgFlowLogsBlobs.ps1로 저장되었습니다.**
   ```
   .\Delete-NsgFlowLogsBlobs.ps1 -SubscriptionId <subscriptionId> -Location  <location> -Confirm
   ```
    
## <a name="next-steps"></a>다음 단계
- 고객은 [Azure 논리 앱](../logic-apps/logic-apps-overview.md) 또는 Azure 자동화를 사용하여 스크립트 실행을 자동화할 수 [있습니다.](https://azure.microsoft.com/services/automation/)
- NSG 로깅에 대한 자세한 내용은 [NSG(네트워크 보안 그룹)에 대한 Azure 모니터 로그를](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)참조하십시오.

