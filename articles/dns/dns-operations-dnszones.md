---
title: "PowerShell을 사용하여 DNS 영역 관리 | Microsoft 문서"
description: "Azure Powershell을 사용하여 DNS 영역을 관리할 수 있습니다. Azure DNS에서 DNS 영역을 업데이트, 삭제 및 만드는 방법입니다."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: a67992ab-8166-4052-9b28-554c5a39e60c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 1136b0e378cec14a9c899b61ec1dd21ff6720ba5

---
# <a name="how-to-manage-dns-zones-using-powershell"></a>PowerShell을 사용하여 DNS 영역을 관리하는 방법

> [!div class="op_single_selector"]
> * [Azure CLI](dns-operations-dnszones-cli.md)
> * [PowerShell](dns-operations-dnszones.md)

이 문서에서는 PowerShell을 사용하여 DNS 영역을 관리하는 방법을 보여줍니다. 이 단계를 사용하려면, 최신 버전(1.0 이상)의 Azure Resource Manager PowerShell cmdlet을 설치해야 합니다. PowerShell cmdlet 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](/powershell/azureps-cmdlets-docs) 을 참조하세요.

## <a name="create-a-new-dns-zone"></a>새 DNS 영역 만들기

DNS 영역을 만들려면 [PowerShell을 사용하여 DNS 영역 만들기](dns-getstarted-create-dnszone.md)를 참조하세요.

## <a name="get-a-dns-zone"></a>DNS 영역 가져오기

DNS 영역을 가져오려면 `Get-AzureRmDnsZone` cmdlet을 사용합니다. 이 작업은 Azure DNS의 기존 영역에 해당하는 DNS 영역 개체를 반환합니다. 이 개체는 영역에 대한 데이터(예: 레코드 집합 수)를 포함하지만 레코드 집합 자체는 포함하지 않습니다.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup
```

## <a name="list-dns-zones"></a>DNS 영역 나열

`Get-AzureRmDnsZone`에서 영역 이름을 생략하면, 리소스 그룹의 모든 영역을 열거할 수 있습니다. 이 작업은 영역 개체의 배열을 반환합니다.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

## <a name="update-a-dns-zone"></a>DNS 영역 업데이트

`Set-AzureRmDnsZone`를 사용하여 DNS 영역 리소스를 변경할 수 있습니다. 이 작업은 영역 내의 DNS 레코드 집합을 업데이트하지 않습니다( [DNS 레코드를 관리하는 방법](dns-operations-recordsets.md)참조). 영역 리소스 자체의 속성을 업데이트하는 데만 사용됩니다. 이 작업은 현재 영역 리소스에 대한 Azure 리소스 관리자 '태그'로 제한됩니다. 자세한 내용은 [Etag 및 태그](dns-getstarted-create-dnszone.md#tagetag) 를 참조하세요.

다음 두 가지 방법 중 하나를 사용하여 DNS 영역을 업데이트합니다.

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>영역 이름 및 리소스 그룹을 사용하여 영역을 지정합니다.

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Tag $tags]
```

### <a name="specify-the-zone-using-a-zone-object"></a>$zone 개체를 사용하여 영역을 지정합니다.

`Get-AzureRmDnsZone`의 $zone 개체를 사용하여 영역을 지정합니다. $zone 개체와 함께 `Set-AzureRmDnsZone` 을 사용하는 경우 동시 변경 내용을 덮어쓰지 않도록 Etag 검사가 사용됩니다. 선택적 *-Overwrite* 스위치를 사용하여 이러한 검사를 무시할 수 있습니다. 자세한 내용은 [Etag 및 태그](dns-getstarted-create-dnszone.md#tagetag) 를 참조하세요.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
<..modify $zone.Tags here...>
Set-AzureRmDnsZone -Zone $zone [-Overwrite]
```

## <a name="delete-a-dns-zone"></a>DNS 영역 삭제

Remove-AzureRmDnsZone cmdlet을 사용하여 DNS 영역을 삭제할 수 있습니다.

Azure DNS에서 DNS 영역을 삭제하기 전에 영역을 만들 때 자동으로 생성된, 영역 루트에 있는 NS 및 SOA 레코드를 제외한 모든 레코드 집합을 삭제해야 합니다.

다음 두 가지 방법 중 하나를 사용하여 DNS 영역을 제거합니다.

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>영역 이름 및 리소스 그룹 이름을 사용하여 영역을 지정합니다.

이 작업에는 DNS 영역을 제거할 것인지 확인하는 메시지를 표시하지 않는 선택적인 `-Force` 스위치가 있습니다.

```powershell
Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]
```

### <a name="specify-the-zone-using-a-zone-object"></a>$zone 개체를 사용하여 영역을 지정합니다.

`Get-AzureRmDnsZone`의 $zone 개체를 사용하여 영역을 지정합니다. 이 작업에는 DNS 영역을 제거할 것인지 확인하는 메시지를 표시하지 않는 선택적인 `-Force` 스위치가 있습니다. `Set-AzureRmDnsZone`과 마찬가지로, $zone 개체를 사용하여 영역을 지정하면 동시 변경 내용을 삭제하지 않도록 Etag 검사가 사용됩니다.

선택적인 `-Overwrite` 플래그를 사용하면 이러한 검사가 무시됩니다. 자세한 내용은 [Etag 및 태그](dns-getstarted-create-dnszone.md#tagetag) 를 참조하세요.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsZone -Zone $zone [-Force] [-Overwrite]
```

영역 개체를 매개 변수로 전달하는 대신 파이프할 수도 있습니다.

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone [-Force] [-Overwrite]
```

## <a name="next-steps"></a>다음 단계

DNS 영역을 만든 후에는 [레코드 집합 및 레코드](dns-getstarted-create-recordset.md) 를 만들어 인터넷 도메인에 대한 이름 확인을 시작해야 합니다.




<!--HONumber=Dec16_HO2-->


