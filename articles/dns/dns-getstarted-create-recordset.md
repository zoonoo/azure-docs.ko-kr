---
title: "PowerShell을 사용하여 DNS 영역에 대한 레코드 집합 및 레코드 만들기 | Microsoft 문서"
description: "Azure DNS에 대한 호스트 레코드를 만드는 방법입니다. PowerShell을 사용하여 레코드 집합 및 레코드 설정"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: a8068c5a-f248-4e97-be97-8bd0d79eeffd
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 67b780d66eac4199b0a2367f575477191542cfa7
ms.lasthandoff: 02/27/2017

---

# <a name="create-dns-record-sets-and-records-by-using-powershell"></a>PowerShell을 사용하여 DNS 레코드 집합 및 레코드 만들기

> [!div class="op_single_selector"]
> * [Azure 포털](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI 1.0](dns-getstarted-create-recordset-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-create-recordset-cli.md)

이 문서는 Azure PowerShell을 사용하여 레코드 및 레코드 집합을 만드는 과정을 안내합니다.

## <a name="introduction"></a>소개

Azure DNS에 DNS 레코드를 만들기 전에 먼저 Azure DNS에서 DNS 레코드를 DNS 레코드 집합으로 구성하는 방법을 이해해야 합니다.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Azure DNS의 DNS 레코드에 대한 자세한 내용은 [DNS 영역 및 레코드](dns-zones-records.md)를 참조하세요.

## <a name="create-a-record-set-and-record"></a>레코드 집합 및 레코드 만들기

이 섹션에서는 Azure DNS에서 DNS 레코드를 만드는 방법을 설명합니다. 예제에서는 이미 [Azure PowerShell을 설치했고, 로그인했고, DNS 영역을 만들었다](dns-getstarted-create-dnszone.md)고 가정합니다.

이 페이지에 나오는 모든 예제에서는 'A' DNS 레코드 유형을 사용합니다. 다른 레코드 형식과 DNS 레코드 및 레코드 집합을 관리하는 방법에 관한 자세한 내용은 [PowerShell을 사용하여 DNS 레코드 및 레코드 집합 관리](dns-operations-recordsets.md)를 참조하세요.

새 레코드가 기존 레코드와 이름 및 형식이 똑같은 경우 [기존 레코드 집합에 추가](#add-a-record-to-an-existing-record-set)해야 합니다. 새 레코드가 기존 레코드와 이름 및 형식이 다른 경우 [새 레코드 집합을 만들](#create-records-in-a-new-record-set)어야 합니다. 

### <a name="create-records-in-a-new-record-set"></a>새 레코드 집합에서 레코드 만들기

`New-AzureRmDnsRecordSet` cmdlet을 사용하여 레코드 집합을 만듭니다. 레코드 집합을 만들 때, 레코드 집합 이름, 영역, TTL(Time-to-Live), 레코드 형식 및 만들 레코드를 지정해야 합니다.

영역의 구로에서 레코드 집합을 만들려면(이 경우 "contoso.com"), 따옴표를 포함한 레코드 이름 "@"를 사용합니다. 이것이 일반적인 DNS 규칙입니다.

다음 예제에서는 DNS 영역 "contoso.com"에 상대적 이름 "www"가 포함된 새 레코드 집합을 만듭니다. 레코드의 정규화된 이름은 “www.contoso.com”입니다. 레코드 형식은 "A"이고 TTL은 3600초입니다. 레코드 집합은 "1.2.3.4" IP 주소를 가진 단일 레코드를 포함합니다.

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4")
```

둘 이상의 레코드를 포함하는 새 레코드 집합을 만들어야 하는 경우 먼저 추가할 레코드를 포함하는 로컬 배열을 만들어야 합니다.  다음과 같이 `New-AzureRmDnsRecordSet`에 전달됩니다.

```powershell
$aRecords = @()
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzureRmDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

### <a name="add-a-record-to-an-existing-record-set"></a>기존 레코드 집합에 레코드 추가

기존 레코드 집합에 레코드를 추가하려면 다음 세 단계를 수행합니다.

1. 기존 레코드 집합 가져오기

    ```powershell
    $rs = Get-AzureRmDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. 로컬 레코드 집합에 새 레코드 추가 이 작업은 오프라인 작업입니다.

    ```powershell
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Azure DNS 서비스에 변경 내용 커밋 

    ```powershell
    Set-AzureRmDnsRecordSet -RecordSet $rs
    ```

### <a name="verify-name-resolution"></a>이름 확인을 확인하기

nslookup, dig, [Resolve-DnsName PowerShell cmdlet](https://technet.microsoft.com/library/jj590781.aspx)과 같은 DNS 도구를 사용하여 DNS 영역이 Azure DNS 이름 서버에 존재하는지 테스트할 수 있습니다.

Azure DNS에서 새 영역을 사용하도록 도메인을 아직 위임하지 않은 경우 [DNS 쿼리를 영역에 대한 이름 서버 중 하나로 직접 보내](dns-getstarted-create-dnszone.md#test-name-servers)야 합니다. 다음 예제를 사용자 레코드 영역의 올바른 값으로 대체해야 합니다.

```
nslookup
> set type=A
> server ns1-01.azure-dns.com
> www.contoso.com

Server:  ns1-01.azure-dns.com
Address:  40.90.4.1

Name:    www.contoso.com
Address:  1.2.3.4
```

## <a name="next-steps"></a>다음 단계

[Azure DNS 이름 서버에 도메인 이름을 위임](dns-domain-delegation.md)하는 방법을 알아봅니다.

[PowerShell을 사용하여 DNS 영역을 관리하는](dns-operations-dnszones.md) 방법에 대해 알아봅니다.

[PowerShell을 사용하여 DNS 레코드 및 레코드 집합을 관리하는](dns-operations-recordsets.md) 방법에 대해 알아봅니다.



