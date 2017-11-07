---
title: "PowerShell을 사용하여 Azure DNS 사설 영역 시작 | Microsoft Docs"
description: "Azure DNS에 사설 DNS 영역 및 레코드를 만드는 방법을 알아봅니다. PowerShell을 사용하여 첫 번째 사설 DNS 영역 및 레코드를 만들고 관리하는 단계별 가이드입니다."
services: dns
documentationcenter: na
author: garbrad
manager: 
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/04/2017
ms.author: garbrad
ms.openlocfilehash: c5d4b33682f9b5d33e74d45973d5fbd76cf16465
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-dns-private-zones-using-powershell"></a>PowerShell을 사용하여 Azure DNS 사설 영역 시작

이 문서에서는 Azure PowerShell을 사용하여 첫 번째 사설 DNS 영역 및 레코드를 만드는 단계를 안내합니다.

DNS 영역은 특정 도메인에 대한 DNS 레코드를 호스트하는 데 사용됩니다. Azure DNS에서 도메인 호스팅을 시작하려면 해당 도메인 이름의 DNS 영역을 만들어야 합니다. 그러면 이 DNS 영역 안에 도메인의 각 DNS 레코드가 생성됩니다. 가상 네트워크에 사설 DNS 영역에 게시하려면 영역 내에서 레코드를 확인하도록 허용된 가상 네트워크 목록을 지정합니다.  이것을 ‘해상도 네트워크’라고 합니다.  또한 VM이 생성되고, IP가 변경되고, 제거될 때마다 Azure DNS가 호스트 이름 레코드를 유지 관리하게 되는 가상 네트워크 집합을 지정할 수 있습니다.  이것을 ‘등록 네트워크’라고 합니다.

현재 이 기능은 관리 미리 보기이므로 미리 보기 PowerShell 모듈이 제공됩니다.

[!INCLUDE [private-dns-preview-notice](../../includes/private-dns-preview-notice.md)]

## <a name="create-the-resource-group"></a>리소스 그룹 만들기

DNS 영역을 만들기 전에 DNS 영역을 포함할 리소스 그룹이 생성됩니다. 다음은 명령을 표시합니다.

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-zone"></a>DNS 영역 만들기

DNS 영역은 `New-AzureRmDnsZone` cmdlet을 사용하여 생성됩니다. 다음 예제에서는 *contoso.local*이라는 DNS 영역을 *MyResourceGroup*이라는 리소스 그룹에 만들고, *MyAzureVnet*이라는 가상 네트워크에서 DNS 영역을 사용할 수 있도록 합니다. 예제를 사용하여 DNS 영역을 만들고 사용자 고유 값으로 대체합니다.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -ResolutionVirtualNetworkId @($vnet.Id)
```

Azure가 자동으로 영역에 호스트 이름 레코드를 만들어야 하는 경우 *ResolutionVirtualNetworkId* 대신 *RegistrationVirtualNetworkId* 매개 변수를 사용합니다.  가상 네트워크 등록은 자동으로 해상도에 대해 사용이 설정됩니다.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -RegistrationVirtualNetworkId @($vnet.Id)
```


## <a name="create-a-dns-record"></a>DNS 레코드 만들기

`New-AzureRmDnsRecordSet` cmdlet을 사용하여 레코드 집합을 만듭니다. 다음 예제에서는 리소스 그룹 “MyResourceGroup”의 DNS 영역 “contoso.com”에 상대적 이름 “db”가 포함된 레코드를 만듭니다. 레코드 집합의 정규화된 이름은 “db.contoso.local”입니다. 레코드 형식은 “A”이고, IP 주소는 “10.0.0.4”이며, TTL은 3600초입니다.

```powershell
New-AzureRmDnsRecordSet -Name db -RecordType A -ZoneName contoso.local -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "10.0.0.4")
```

두 개 이상의 레코드가 있는 레코드 집합 등 다른 레코드 유형을 알아보거나 기존 레코드를 수정하려면 [Azure PowerShell을 사용하여 DNS 레코드 및 레코드 집합 관리](dns-operations-recordsets.md)를 참조하세요. 


## <a name="view-records"></a>레코드 보기

사용자 영역에 DNS 레코드를 나열하려면 다음을 사용하세요.

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.local -ResourceGroupName MyResourceGroup
```

## <a name="delete-all-resources"></a>모든 리소스 삭제

이 문서에서 만든 모든 리소스를 삭제하려면 다음 단계를 수행합니다.

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>다음 단계

사설 DNS 영역에 대해 자세히 알아보려면 [사설 도메인에 Azure DNS 사용](private-dns-overview.md)을 참조하세요.

Azure DNS에서 DNS 레코드 관리에 대한 자세한 내용은 [PowerShell을 사용하여 Azure DNS에서 DNS 레코드 및 레코드 집합 관리](dns-operations-recordsets.md)를 참조하세요.

