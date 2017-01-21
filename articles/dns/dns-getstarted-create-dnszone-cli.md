---
title: "CLI를 사용하여 DNS 영역 만들기 | Microsoft Docs"
description: "Azure DNS에 DNS 영역을 만드는 방법을 알아봅니다. 이는 Azure CLI를 사용하여 첫 번째 DNS를 만들고 관리하는 방법에 대한 단계별 가이드입니다."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 1514426a-133c-491a-aa27-ee0962cea9dc
ms.service: dns
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: f156e4d4c5ffddb7e93ebf21baa75864e0e260e9
ms.openlocfilehash: d00792a4bb19e194dbbcee8b9c11e4a744891388

---

# <a name="create-an-azure-dns-zone-using-cli"></a>CLI를 사용하여 Azure DNS 영역 만들기

> [!div class="op_single_selector"]
> * [Azure 포털](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI](dns-getstarted-create-dnszone-cli.md)

이 문서에서는 Windows, Mac 및 Linux용 플랫폼 간 Azure CLI를 사용하여 DNS 영역을 만드는 단계를 안내합니다. [Azure PowerShell](dns-getstarted-create-dnszone.md) 또는 [Azure Portal](dns-getstarted-create-dnszone-portal.md)을 사용하여 DNS 영역을 만들 수도 있습니다.

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-cli-setup](../../includes/dns-cli-setup-include.md)]


## <a name="create-a-dns-zone"></a>DNS 영역 만들기

`azure network dns zone create` 명령을 사용하여 DNS 영역을 만듭니다. 이 명령에 대한 도움말을 보려면 `azure network dns zone create -h`을 입력합니다.

다음 예제에서는 *MyResourceGroup*이라는 리소스 그룹에 *contoso.com*이라는 DNS 영역을 만듭니다. 예제를 사용하여 DNS 영역을 만들고 사용자 고유 값으로 대체합니다.

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

## <a name="verify-your-dns-zone"></a>DNS 영역 확인

### <a name="view-records"></a>레코드 보기

DNS 영역을 만들면 다음과 같은 DNS 레코드도 생성됩니다.

* *권한 시작* (SOA) 레코드입니다. 이 레코드는 모든 DNS 영역의 루트에 있습니다.
* 권한이 있는 NS(이름 서버) 레코드. 이러한 레코드는 영역을 호스팅하는 이름 서버를 보여 줍니다. Azure DNS는 이름 서버 풀을 사용하므로 Azure DNS의 각 영역에 다른 이름 서버가 할당될 수 있습니다. 자세한 내용은 [Azure DNS에 도메인 위임](dns-domain-delegation.md)을 참조하세요.

이러한 레코드를 보려면 다음을 사용 `azure network dns-record-set list`:

```azurecli
azure network dns record-set list MyResourceGroup contoso.com

info:    Executing command network dns record-set list
+ Looking up the DNS Record Sets
data:    Name                            : @
data:    Type                            : NS
data:    TTL                             : 172800
data:    Records:
data:      ns1-01.azure-dns.com.
data:      ns2-01.azure-dns.net.
data:      ns3-01.azure-dns.org.
data:      ns4-01.azure-dns.info.
data:
data:    Name                            : @
data:    Type                            : SOA
data:    TTL                             : 3600
data:    Email                           : azuredns-hostmaster.microsoft.com
data:    Host                            : ns1-01.azure-dns.com.
data:    Serial Number                   : 2
data:    Refresh Time                    : 3600
data:    Retry Time                      : 300
data:    Expire Time                     : 2419200
data:    Minimum TTL                     : 300
data:
info:    network dns record-set list command OK
```

> [!NOTE]
> DNS 영역의 루트(또는 *apex*)에 있는 레코드 집합은 레코드 집합 이름으로 **@** 를 사용합니다.

### <a name="test-name-servers"></a>이름 서버 테스트

nslookup, dig, `Resolve-DnsName` PowerShell cmdlet과 같은 DNS 도구를 사용하여 DNS 영역이 Azure DNS 이름 서버에 존재하는지 테스트할 수 있습니다.

Azure DNS에서 새 영역을 사용하도록 도메인을 아직 위임하지 않은 경우 DNS 쿼리를 영역에 대한 이름 서버 중 하나로 직접 보내야 합니다. 영역에 대한 이름 서버는 `azure network dns record-set list`에서 제공하는 NS 레코드에 제공됩니다.

다음 예제에서는 'dig'을 사용하여 DNS 영역에 할당된 이름 서버를 사용하는 contoso.com 도메인을 쿼리합니다. 영역의 올바른 값으로 대체해야 합니다.

     > dig @ns1-01.azure-dns.com contoso.com
     
     <<>> DiG 9.10.2-P2 <<>> @ns1-01.azure-dns.com contoso.com
    (1 server found)
    global options: +cmd
     Got answer:
    ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
     flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
     WARNING: recursion requested but not available

     OPT PSEUDOSECTION:
     EDNS: version: 0, flags:; udp: 4000
      QUESTION SECTION:
    contoso.com.                        IN      A

     AUTHORITY SECTION:
    contoso.com.         3600     IN      SOA     ns1-01.azure-dns.com. azuredns-hostmaster.microsoft.com. 1 3600 300 2419200 300

    Query time: 93 msec
    SERVER: 208.76.47.5#53(208.76.47.5)
    WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
    MSG SIZE  rcvd: 120

## <a name="next-steps"></a>다음 단계

DNS 영역을 만든 후에는 영역에 [DNS 레코드 집합 및 레코드를 만듭니다](dns-getstarted-create-recordset-cli.md).




<!--HONumber=Dec16_HO3-->


