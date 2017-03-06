---
title: "Azure CLI 2.0을 사용하여 DNS 레코드 만들기 | Microsoft Docs"
description: "Azure DNS의 호스트 레코드를 만들고 Azure CLI 2.0을 사용하여 레코드 집합 및 레코드를 설정하는 방법"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 02b897d3-e83b-4257-b96d-5c29aa59e843
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1481fcb070f383d158c5a6ae32504e498de4a66b
ms.openlocfilehash: ca713647a9f795d7803c32eac7fba9b5b6ac95cf
ms.lasthandoff: 03/01/2017

---

# <a name="how-to-create-dns-records-using-the-azure-cli-20"></a>Azure CLI 2.0을 사용하여 DNS 레코드를 만드는 방법

> [!div class="op_single_selector"]
> * [Azure 포털](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI 1.0](dns-getstarted-create-recordset-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-create-recordset-cli.md)

이 문서에서는 Azure CLI를 사용하여 레코드 및 레코드 집합을 만드는 과정을 안내합니다.

## <a name="introduction"></a>소개

Azure DNS에 DNS 레코드를 만들기 전에 먼저 Azure DNS에서 DNS 레코드를 DNS 레코드 집합으로 구성하는 방법을 이해해야 합니다.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Azure DNS의 DNS 레코드에 대한 자세한 내용은 [DNS 영역 및 레코드](dns-zones-records.md)를 참조하세요.

## <a name="cli-versions-to-complete-the-task"></a>태스크를 완료하기 위한 CLI 버전

다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

* [Azure CLI 1.0](dns-getstarted-create-recordset-cli-nodejs.md) - 클래식 및 리소스 관리 배포 모델용 CLI.
* [Azure CLI 2.0](dns-getstarted-create-recordset-cli.md) - 리소스 관리 배포 모델용 차세대 CLI.

## <a name="create-a-record-set-and-record"></a>레코드 집합 및 레코드 만들기

이 섹션에서는 Azure DNS에서 DNS 레코드를 만드는 방법을 설명합니다. 예제에서는 이미 [Azure CLI 2.0을 설치했고, 로그인했고, DNS 영역을 만들었다](dns-getstarted-create-dnszone-cli.md)고 가정합니다.

이 페이지에 나오는 모든 예제에서는 'A' DNS 레코드 유형을 사용합니다. 다른 레코드 유형과 DNS 레코드 및 레코드 집합을 관리하는 방법에 관한 자세한 내용은 [Azure CLI 2.0을 사용하여 DNS 레코드 및 레코드 집합 관리](dns-operations-recordsets-cli.md)를 참조하세요.

## <a name="create-a-dns-record"></a>DNS 레코드 만들기

DNS 레코드를 만들려면 `az network dns record-set ? add` 명령(? 는 레코드 유형)을 사용합니다. 도움말을 보려면 `az network dns record-set --help`을 참조하세요.

레코드를 만드는 경우 리소스 그룹 이름, 영역 이름, 레코드 집합 이름, 레코드 유형 및 만드는 레코드의 세부 정보를 지정해야 합니다.

레코드 집합이 아직 없는 경우 이 명령은 자동으로 레코드 집합을 만듭니다. 레코드 집합이 이미 있는 경우 이 명령은 지정한 레코드를 기존 레코드 집합에 추가합니다. 

새 레코드 집합이 만들어지면 3600의 기본 TTL(Time to Live)이 사용됩니다. 다른 TTL을 사용하는 방법에 대한 지침은 [Azure CLI 2.0을 사용하여 Azure DNS에서 DNS 레코드 관리](dns-operations-recordsets-cli.md)를 참조하세요.

다음 예제에서는 *MyResourceGroup* 리소스 그룹의 *contoso.com* 영역에 *www*라는 A 레코드를 만듭니다. A 레코드의 IP 주소는 *1.2.3.4*입니다.

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

영역의 루트(이 경우 "contoso.com")에 레코드 집합을 만들기 위해 따옴표를 포함한 "@"을 레코드 이름으로 사용합니다.

```azurecli
az network dns record-set a --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

레코드 데이터를 지정하는 데 사용되는 매개 변수는 레코드 유형에 따라 다릅니다. 예를 들어 "A" 유형의 레코드의 경우 `--ipv4-address <IPv4 address>` 매개 변수로 IPv4 주소를 지정합니다. 다른 레코드 종류에 대한 매개 변수를 나열하려면 `az network dns record --help`을 참조하세요. 각 레코드 유형에 대한 예제는 [Azure CLI 2.0을 사용하여 DNS 레코드 및 레코드 집합 관리](dns-operations-recordsets-cli.md)를 참조하세요.


## <a name="verify-name-resolution"></a>이름 확인을 확인하기

nslookup, dig, [Resolve-DnsName PowerShell cmdlet](https://technet.microsoft.com/library/jj590781.aspx)과 같은 DNS 도구를 사용하여 DNS 영역이 Azure DNS 이름 서버에 존재하는지 테스트할 수 있습니다.

Azure DNS에서 새 영역을 사용하도록 도메인을 아직 위임하지 않은 경우 [DNS 쿼리를 영역에 대한 이름 서버 중 하나로 직접 보내](dns-getstarted-create-dnszone.md#test-name-servers)야 합니다. 아래 명령을 사용자 레코드 영역의 올바른 값으로 대체해야 합니다.

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

[Azure CLI 2.0을 사용하여 DNS 영역을 관리](dns-operations-dnszones-cli.md)하는 방법을 알아봅니다.

[Azure CLI 2.0을 사용하여 DNS 레코드 및 레코드 집합을 관리](dns-operations-recordsets-cli.md)하는 방법을 알아봅니다.


