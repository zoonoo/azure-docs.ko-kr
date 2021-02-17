---
title: 포함 파일
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f5ebd05177abf5fb3468b3ce935b7d004b414424
ms.sourcegitcommit: b513b0becf878eb9a1554c26da53aa48d580bb22
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551486"
---
NFS는 프리미엄 파일 저장소를 사용할 수 **있는 30 개** 이상의 지역에서 지원 됩니다.

계속 해 서 지역을 추가 하 고 있습니다. 최신 목록을 보려면 아래 샘플을 사용 하 여 NFS를 지 원하는 지역 목록을 쿼리 하세요. **프리미엄 파일 저장소** 의 [지역에서 사용 가능한 Azure 제품 페이지](https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all) 에서 지역 지원을 확인할 수도 있습니다.

```azurepowershell-interactive
# Log in first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Provide specific subscription id if you want  list for a different subscription
$subscription = $azContext.Subscription.Id

# Invoke the REST API
$restUri = "https://management.azure.com/subscriptions/$subscription/providers/Microsoft.Storage/skus?api-version=2019-06-01"
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader

# List of all regions that has NFS support.
$response.value| Where-Object -FilterScript {$_.capabilities| Where-Object { $_.name -eq 'supportsNfsShare' -and $_.value -eq 'true'}}| Select-Object locations, kind, name

# List of regions that support NFS Zonal redundancy.
$response.value| Where-Object -FilterScript {($_.name -EQ 'Premium_ZRS') -and ($_.capabilities| Where-Object { $_.name -eq 'supportsNfsShare' -and $_.value -eq 'true'})}| Select-Object locations
```

샘플 응답
```
List of regions that support NFS Zonal redundancy
locations
---------
{eastus}
{eastus2}
{westeurope}
{southeastasia}
{japaneast}
{northeurope}
{australiaeast}
{westus2}
{uksouth}
{eastus2euap}
{francecentral}
```
