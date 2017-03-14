---
title: "Azure DNS 시작 | Microsoft 문서"
description: "Azure DNS에 DNS 영역을 만드는 방법을 알아봅니다. 이는 Azure PowerShell을 사용하여 첫 번째 DNS를 만들고 관리하는 방법에 대한 단계별 가이드입니다."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: d78583b7-e669-435c-819b-7605cf791b0e
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 798717eca5180e445b14351bbfc694ed206ee9dc
ms.lasthandoff: 02/27/2017

---

# <a name="create-a-dns-zone-using-powershell"></a>PowerShell을 사용하여 DNS 영역 만들기

> [!div class="op_single_selector"]
> * [Azure Portal](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI 1.0](dns-getstarted-create-dnszone-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-create-dnszone-cli.md)

이 문서에서는 Azure PowerShell을 사용하여 DNS 영역을 만드는 단계를 안내합니다. 플랫폼 간 [Azure CLI](dns-getstarted-create-dnszone-cli.md) 또는 [Azure Portal](dns-getstarted-create-dnszone-portal.md)을 사용하여 DNS 영역을 만들 수도 있습니다.

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>DNS 영역 만들기

DNS 영역은 `New-AzureRmDnsZone` cmdlet을 사용하여 생성됩니다. 다음 예제에서는 *MyResourceGroup*이라는 리소스 그룹에 *contoso.com*이라는 DNS 영역을 만듭니다. 예제를 사용하여 DNS 영역을 만들고 사용자 고유 값으로 대체합니다.

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

## <a name="verify-your-dns-zone"></a>DNS 영역 확인

### <a name="view-records"></a>레코드 보기

DNS 영역을 만들면 다음과 같은 DNS 레코드도 생성됩니다.

* *권한 시작* (SOA) 레코드입니다. 이 레코드는 모든 DNS 영역의 루트에 있습니다.
* 권한이 있는 NS(이름 서버) 레코드. 이러한 레코드는 영역을 호스팅하는 이름 서버를 보여 줍니다. Azure DNS는 이름 서버 풀을 사용하므로 Azure DNS의 각 영역에 다른 이름 서버가 할당될 수 있습니다. 자세한 내용은 [Azure DNS에 도메인 위임](dns-domain-delegation.md)을 참조하세요.

이러한 레코드를 보려면 다음을 사용 `Get-AzureRmDnsRecordSet`:

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

Name              : @
ZoneName          : contoso.com
ResourceGroupName : MyAzureResourceGroup
Ttl               : 172800
Etag              : f573237b-088c-424a-b53c-08567d87d049
RecordType        : NS
Records           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org., ns4-01.azure-dns.info.}
Metadata          : 

Name              : @
ZoneName          : contoso.com
ResourceGroupName : MyAzureResourceGroup
Ttl               : 3600
Etag              : bf88a27d-0eec-4847-ad42-f0c83b9a2c32
RecordType        : SOA
Records           : {[ns1-01.azure-dns.com.,azuredns-hostmaster.microsoft.com,3600,300,2419200,300]}
Metadata          : 
```

> [!NOTE]
> DNS 영역의 루트(또는 *apex*)에 있는 레코드 집합은 레코드 집합 이름으로 **@** 를 사용합니다.

### <a name="test-name-servers"></a>이름 서버 테스트

nslookup, dig, [Resolve-DnsName PowerShell cmdlet](https://technet.microsoft.com/library/jj590781.aspx)과 같은 DNS 도구를 사용하여 DNS 영역이 Azure DNS 이름 서버에 존재하는지 테스트할 수 있습니다.

Azure DNS에서 새 영역을 사용하도록 도메인을 아직 위임하지 않은 경우 DNS 쿼리를 영역에 대한 이름 서버 중 하나로 직접 보내야 합니다. 사용자의 영역에 대한 이름 서버는 `Get-AzureRmDnsRecordSet` 을 통해 나열된 것처럼 NS 레코드에 제공됩니다. 다음 예제를 사용자 영역의 올바른 값으로 대체해야 합니다.

```
nslookup
> set type=SOA
> server ns1-01.azure-dns.com
> contoso.com

Server: ns1-01.azure-dns.com
Address:  40.90.4.1

contoso.com
        primary name server = ns1-01.azure-dns.com
        responsible mail addr = azuredns-hostmaster.microsoft.com
        serial  = 1
        refresh = 3600 (1 hour)
        retry   = 300 (5 mins)
        expire  = 2419200 (28 days)
        default TTL = 300 (5 mins)
```

## <a name="next-steps"></a>다음 단계

DNS 영역을 만든 후에는 [레코드 집합 및 레코드](dns-getstarted-create-recordset.md)를 만들어 인터넷 도메인에 대한 DNS 기록을 만들어야 합니다.

