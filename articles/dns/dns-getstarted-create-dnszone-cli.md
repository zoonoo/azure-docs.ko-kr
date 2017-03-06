---
title: "Azure CLI 2.0을 사용하여 DNS 영역 만들기| Microsoft Docs"
description: "Azure DNS에 DNS 영역을 만드는 방법을 알아봅니다. Azure CLI 2.0을 사용하여 첫 번째 DNS 영역을 만들고 관리하는 단계별 가이드입니다."
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
ms.date: 02/27/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1481fcb070f383d158c5a6ae32504e498de4a66b
ms.openlocfilehash: 00a4dfbe070129ce6dc84f006d793007d55dba3f
ms.lasthandoff: 03/01/2017

---

# <a name="create-an-azure-dns-zone-using-azure-cli-20"></a>Azure CLI 2.0을 사용하여 Azure DNS 영역 만들기

> [!div class="op_single_selector"]
> * [Azure Portal](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI 1.0](dns-getstarted-create-dnszone-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-create-dnszone-cli.md)

이 문서에서는 Windows, Mac 및 Linux용 플랫폼 간 Azure CLI를 사용하여 DNS 영역을 만드는 단계를 안내합니다. [Azure PowerShell](dns-getstarted-create-dnszone.md) 또는 [Azure Portal](dns-getstarted-create-dnszone-portal.md)을 사용하여 DNS 영역을 만들 수도 있습니다.

## <a name="cli-versions-to-complete-the-task"></a>태스크를 완료하기 위한 CLI 버전

다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

* [Azure CLI 1.0](dns-getstarted-create-dnszone-cli-nodejs.md) - 클래식 및 리소스 관리 배포 모델용 CLI.
* [Azure CLI 2.0](dns-getstarted-create-dnszone-cli.md) - 리소스 관리 배포 모델용 차세대 CLI.

## <a name="introduction"></a>소개

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-20-for-azure-dns"></a>Azure DNS용 Azure CLI 2.0 설정

### <a name="before-you-begin"></a>시작하기 전에

구성을 시작하기 전에 다음 항목이 있는지 확인합니다.

* Azure 구독. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.

* Windows, Linux 또는 MAC용 최신 버전의 Azure CLI를 설치합니다. 자세한 내용은 [Azure CLI 2.0 설치](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2)를 참조하세요.

### <a name="sign-in-to-your-azure-account"></a>Azure 계정에 로그인

콘솔 창을 열고 자격 증명을 사용하여 인증합니다. 자세한 내용은 Azure CLI에서 Azure에 로그인을 참조하세요.

```azurecli
az login
```

### <a name="select-the-subscription"></a>구독 선택

계정에 대한 구독을 확인합니다.

```azurecli
az account list
```

### <a name="choose-which-of-your-azure-subscriptions-to-use"></a>사용할 Azure 구독을 선택합니다.

```azurecli
az account set --subscription "subscription name"
```

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 관리자를 사용하려면 모든 리소스 그룹이 위치를 지정해야 합니다. 이 위치는 해당 리소스 그룹에서 리소스의 기본 위치로 사용됩니다. 그러나 모든 DNS 리소스는 국가별이 아니라 전역이므로 리소스 그룹의 위치 선택이 Azure DNS에 영향을 주지 않습니다.

기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뛸 수 있습니다.

```azurecli
az group create --name myresourcegroup --location "West US"
```

## <a name="create-a-dns-zone"></a>DNS 영역 만들기

`az network dns zone create` 명령을 사용하여 DNS 영역을 만듭니다. 이 명령에 대한 도움말을 보려면 `az network dns zone create --help`을 입력합니다.

다음 예제에서는 *MyResourceGroup*이라는 리소스 그룹에 *contoso.com*이라는 DNS 영역을 만듭니다. 예제를 사용하여 DNS 영역을 만들고 사용자 고유 값으로 대체합니다.

```azurecli
az network dns zone create --resource-group myresourcegroup --name contoso.com
```

## <a name="verify-your-dns-zone"></a>DNS 영역 확인

### <a name="view-records"></a>레코드 보기

DNS 영역을 만들면 다음과 같은 DNS 레코드도 생성됩니다.

* *권한 시작* (SOA) 레코드입니다. 이 레코드는 모든 DNS 영역의 루트에 있습니다.
* 권한이 있는 NS(이름 서버) 레코드. 이러한 레코드는 영역을 호스팅하는 이름 서버를 보여 줍니다. Azure DNS는 이름 서버 풀을 사용하므로 Azure DNS의 각 영역에 다른 이름 서버가 할당될 수 있습니다. 자세한 내용은 [Azure DNS에 도메인 위임](dns-domain-delegation.md)을 참조하세요.

이러한 레코드를 보려면 다음을 사용 `az network dns record-set list`:

```azurecli
az network dns record-set list --resource-group MyResourceGroup --zone-name contoso.com
```

`az network dns record-set list`에 대한 응답은 다음과 같습니다.

```json
[
  {
    "etag": "510f4711-8b7f-414e-b8d5-c0383ea3d62e",
    "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@",
    "metadata": null,
    "name": "@",
    "nsRecords": [
      {
        "nsdname": "ns1-04.azure-dns.com."
      },
      {
        "nsdname": "ns2-04.azure-dns.net."
      },
      {
        "nsdname": "ns3-04.azure-dns.org."
      },
      {
        "nsdname": "ns4-04.azure-dns.info."
      }
    ],
    "resourceGroup": "myresourcegroup",
    "ttl": 172800,
    "type": "Microsoft.Network/dnszones/NS"
  },
  {
    "etag": "0e48e82f-f194-4d3e-a864-7e109a95c73a",
    "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@",
    "metadata": null,
    "name": "@",
    "resourceGroup": "myresourcegroup",
    "soaRecord": {
      "email": "azuredns-hostmaster.microsoft.com",
      "expireTime": 2419200,
      "host": "ns1-04.azure-dns.com.",
      "minimumTtl": 300,
      "refreshTime": 3600,
      "retryTime": 300,
      "serialNumber": 1
    },
    "ttl": 3600,
    "type": "Microsoft.Network/dnszones/SOA"
  }
]
```

> [!NOTE]
> DNS 영역의 루트(또는 *apex*)에 있는 레코드 집합은 레코드 집합 이름으로 **@** 를 사용합니다.

### <a name="test-name-servers"></a>이름 서버 테스트

nslookup, dig, `Resolve-DnsName` PowerShell cmdlet과 같은 DNS 도구를 사용하여 DNS 영역이 Azure DNS 이름 서버에 존재하는지 테스트할 수 있습니다.

Azure DNS에서 새 영역을 사용하도록 도메인을 아직 위임하지 않은 경우 DNS 쿼리를 영역에 대한 이름 서버 중 하나로 직접 보내야 합니다. 영역에 대한 이름 서버는 `az network dns record-set list`에서 제공하는 NS 레코드에 제공됩니다.

다음 예제에서는 'dig'을 사용하여 DNS 영역에 할당된 이름 서버를 사용하는 contoso.com 도메인을 쿼리합니다. 영역의 올바른 값으로 대체해야 합니다.

```
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
```

## <a name="next-steps"></a>다음 단계

DNS 영역을 만든 후에는 영역에 [DNS 레코드 집합 및 레코드를 만듭니다](dns-getstarted-create-recordset-cli.md).


