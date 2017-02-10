---
title: "Azure CLI를 사용하여 DNS 레코드 만들기 | Microsoft Docs"
description: "Azure DNS에 대한 호스트 레코드를 만드는 방법입니다. CLI를 사용하여 레코드 집합 및 레코드를 설정합니다."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 02b897d3-e83b-4257-b96d-5c29aa59e843
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 18a21cdc0f9641356dfaf6f6d93edfcac11af210
ms.openlocfilehash: 790af1544ed86155f5f864f3914b5fd1c4f42f4b

---

# <a name="create-dns-records-using-the-azure-cli"></a>Azure CLI를 사용하여 DNS 레코드 만들기

> [!div class="op_single_selector"]
> * [문제 해결](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI](dns-getstarted-create-recordset-cli.md)

이 문서에서는 Azure CLI를 사용하여 레코드 및 레코드 집합을 만드는 과정을 안내합니다.

## <a name="introduction"></a>소개

Azure DNS에 DNS 레코드를 만들기 전에 먼저 Azure DNS에서 DNS 레코드를 DNS 레코드 집합으로 구성하는 방법을 이해해야 합니다.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Azure DNS의 DNS 레코드에 대한 자세한 내용은 [DNS 영역 및 레코드](dns-zones-records.md)를 참조하세요.

## <a name="create-a-record-set-and-record"></a>레코드 집합 및 레코드 만들기

이 섹션에서는 Azure DNS에서 DNS 레코드를 만드는 방법을 설명합니다. 예제에서는 이미 [Azure CLI를 설치했고, 로그인했고, DNS 영역을 만들었다](dns-getstarted-create-dnszone-cli.md)고 가정합니다.

이 페이지에 나오는 모든 예제에서는 'A' DNS 레코드 유형을 사용합니다. 다른 레코드 유형과 DNS 레코드 및 레코드 집합을 관리하는 방법에 관한 자세한 내용은 [Azure CLI를 사용하여 DNS 레코드 및 레코드 집합을 관리하기](dns-operations-recordsets-cli.md)를 참조하세요.

## <a name="create-a-dns-record"></a>DNS 레코드 만들기

DNS 레코드를 만들려면 `azure network dns record-set add-record` 명령을 사용합니다. 도움말을 보려면 `azure network dns record-set add-record -h`을 참조하세요.

레코드를 만드는 경우 리소스 그룹 이름, 영역 이름, 레코드 집합 이름, 레코드 유형 및 만드는 레코드의 세부 정보를 지정해야 합니다.

레코드 집합이 아직 없는 경우 이 명령은 자동으로 레코드 집합을 만듭니다. 레코드 집합이 이미 있는 경우 이 명령은 지정한 레코드를 기존 레코드 집합에 추가합니다. 

새 레코드 집합이 만들어지면 3600의 기본 TTL(Time to Live)이 사용됩니다. 다른 TTL을 사용하는 방법에 대한 지침은 [Azure CLI를 사용하여 Azure DNS에서 DNS 레코드 관리](dns-operations-recordsets-cli.md)를 참조하세요.

다음 예제에서는 *MyResourceGroup* 리소스 그룹의 *contoso.com* 영역에 *www*라는 A 레코드를 만듭니다. A 레코드의 IP 주소는 *1.2.3.4*입니다.

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

영역(이 경우 "contoso.com")의 apex에 레코드 집합을 만들려면 따옴표를 포함한 "@", 레코드 이름을 사용합니다.

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com "@" A -a 1.2.3.4
```

레코드 데이터를 지정하는 데 사용되는 매개 변수는 레코드 유형에 따라 다릅니다. 예를 들어 "A" 유형의 레코드의 경우 `-a <IPv4 address>` 매개 변수로 IPv4 주소를 지정합니다. 다른 레코드 종류에 대한 매개 변수를 나열하려면 `azure network dns record-set add-record -h`을 참조하세요. 각 레코드 유형에 대한 예제는 [Azure CLI를 사용하여 DNS 레코드 및 레코드 집합 관리](dns-operations-recordsets-cli.md)를 참조하세요.


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

[Azure CLI를 사용하여 DNS 영역을 관리하는](dns-operations-dnszones-cli.md) 방법에 대해 알아봅니다.

[Azure CLI를 사용하여 DNS 레코드 및 레코드 집합을 관리하는](dns-operations-recordsets-cli.md) 방법에 대해 알아봅니다.




<!--HONumber=Feb17_HO1-->


