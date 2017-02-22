---
title: "PowerShell을 사용하여 Azure DNS의 역방향 DNS 레코드 관리 | Microsoft Docs"
description: "Azure DNS를 통해 Resource Manager에서 PowerShell을 사용하여 Azure 서비스에 대한 역방향 DNS 레코드 또는 PTR 레코드를 관리"
services: DNS
documentationcenter: na
author: s-malone
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: b95703c5-e94e-4009-ab37-0c3f7908783c
ms.service: DNS
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2016
ms.author: smalone
translationtype: Human Translation
ms.sourcegitcommit: dd020bf625510eb90af2e1ad19c155831abd7e75
ms.openlocfilehash: 78de3b8dd2d8bd0992bfbacb1079825dca486442


---
# <a name="manage-reverse-dns-records-for-your-azure-services-using-powershell"></a>PowerShell을 사용하여 Azure 서비스에 대한 역방향 DNS 레코드를 관리

[!INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]


[!INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]


[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

클래식 배포 모델에 대한 자세한 내용은 [Azure PowerShell을 사용하여 Azure 서비스(클래식)에 대한 역방향 DNS 레코드 관리 방법](dns-reverse-dns-record-operations-classic-ps.md)을 참조하세요.

## <a name="validation-of-reverse-dns-records"></a>역방향 DNS 레코드의 유효성 검사

타사에서 DNS 도메인에 매핑하는 역방향 DNS 레코드를 만들 수 없도록 하기 위해 Azure에서는 다음 중 하나에 해당하는 역방향 DNS 레코드만 만들도록 허용합니다.

* "ReverseFqdn"이 지정되어 있는 공용 IP 주소 리소스에 대한 "Fqdn"이거나, 동일한 구독 내의 공용 IP 주소에 대한 "Fqdn"과 같습니다. 예를 들어 "ReverseFqdn"이 "contosoapp1.northus.cloudapp.azure.com"입니다.
* "ReverseFqdn" 정방향이 지정되어 있는 공용 IP 주소의 이름 또는 IP 주소로 확인되거나, 동일한 구독 내의 공용 IP 주소 "Fqdn" 또는 IP로 확인됩니다. 예를 들어 "ReverseFqdn"이 "contosoapp1.northus.cloudapp.azure.com"에 대한 CName 별칭인 "app1.contoso.com"입니다.

유효성 검사는 공용 IP 주소의 역방향 DNS 속성이 설정되거나 수정되는 경우에만 수행됩니다. 유효성 재검사는 정기적으로 수행되지 않습니다.

## <a name="add-reverse-dns-to-existing-public-ip-addresses"></a>기존 공용 IP 주소에 역방향 DNS 추가

`Set-AzureRmPublicIpAddress` cmdlet을 사용하여 기존 공용 IP 주소에 역방향 DNS를 추가할 수 있습니다.

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIP" -ResourceGroupName "NRP-DemoRG-PS"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

DNS 이름이 없는 기존 공용 IP 주소에 역방향 DNS를 추가하려면 DNS 이름도 지정해야 합니다. "Set-AzureRmPublicIpAddress" cmdlet을 사용하여 추가할 수 있습니다.

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIP" -ResourceGroupName "NRP-DemoRG-PS"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

## <a name="create-a-public-ip-address-with-reverse-dns"></a>역방향 DNS를 사용하여 공용 IP 주소 만들기

`New-AzureRmPublicIpAddress` cmdlet를 사용하여 지정된 역방향 DNS 속성을 사용하여 새 공용 IP 주소를 추가할 수 있습니다.

```powershell
New-AzureRmPublicIpAddress -Name "PublicIP2" -ResourceGroupName "NRP-DemoRG-PS" -Location "WestUS" -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
```

## <a name="view-reverse-dns-for-existing-public-ip-addresses"></a>기존 공용 IP 주소에 대한 역방향 DNS 보기

`Get-AzureRmPublicIpAddress` cmdlet를 사용하여 기존 공용 IP 주소에 구성된 값을 볼 수 있습니다.

```powershell
Get-AzureRmPublicIpAddress -Name "PublicIP2" -ResourceGroupName "NRP-DemoRG-PS"
```

## <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>기존 공용 IP 주소에서 역방향 DNS 제거

`Set-AzureRmPublicIpAddress` cmdlet를 사용하여 기존 공용 IP 주소에서 역방향 DNS 속성을 제거할 수 있습니다. ReverseFqdn 속성 값을 공백으로 설정하면 됩니다.

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIP" -ResourceGroupName "NRP-DemoRG-PS"
$pip.DnsSettings.ReverseFqdn = ""
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

[!INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[!INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]




<!--HONumber=Feb17_HO2-->


