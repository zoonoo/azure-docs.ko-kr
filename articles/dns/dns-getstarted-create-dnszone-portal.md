---
title: "Azure Portal에서 DNS 영역을 만들고 관리하는 방법 | Microsoft 문서"
description: "Azure DNS에 DNS 영역을 만드는 방법을 알아봅니다. 이는 Azure 포털을 사용하여 첫 번째 DNS를 만들고 관리하는 방법에 대한 단계별 가이드입니다."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f44c5ea1-4c85-469e-888e-5f5b34451664
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 7119ce18e04b427c268d4d8636b1a5fa02ed0cc3
ms.lasthandoff: 02/27/2017

---

# <a name="create-a-dns-zone-in-the-azure-portal"></a>Azure 포털에서 DNS 영역 만들기

> [!div class="op_single_selector"]
> * [Azure Portal](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI 1.0](dns-getstarted-create-dnszone-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-create-dnszone-cli.md)

이 문서에서는 Azure 포털을 사용하여 DNS 영역을 만드는 단계를 안내합니다. PowerShell 또는 CLI를 사용하여 DNS 영역을 만들 수도 있습니다.

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="create-a-dns-zone"></a>DNS 영역 만들기

1. Azure 포털에 로그인합니다.
2. 허브 메뉴에서 **새로 만들기 > 네트워킹 >**을 클릭한 다음 **DNS 영역**을 클릭하여 DNS 영역 블레이드 만들기를 엽니다.

    ![DNS 영역](./media/dns-getstarted-create-dnszone-portal/openzone650.png)

4. **DNS 영역 만들기** 블레이드에서 DNS 영역의 이름을 지정합니다. 예를 들어 *contoso.com*입니다.
 
    ![영역 만들기](./media/dns-getstarted-create-dnszone-portal/newzone250.png)

5. 다음으로, 사용할 리소스 그룹을 지정합니다. 새 리소스 그룹을 만들거나 이미 있는 리소스 그룹을 선택할 수 있습니다. 새 리소스 그룹을 만드는 경우 **위치** 드롭다운을 사용하여 리소스 그룹의 위치를 지정합니다. 이 설정은 리소스 그룹의 위치를 나타내며 DNS 영역에는 영향이 없습니다. DNS 영역 위치는 항상 "전역"이며 표시되지 않습니다.

6. 대시보드에서 새 영역을 쉽게 찾으려는 경우 **대시보드에 고정** 확인란을 선택된 상태로 그대로 둘 수 있습니다. 그런 다음 **Create**를 클릭합니다.

    ![대시보드에 고정](./media/dns-getstarted-create-dnszone-portal/pindashboard150.png)

7. 만들기를 클릭하면 대시보드에 새 영역이 구성되고 있는 것을 볼 수 있습니다.

    ![만드는 중](./media/dns-getstarted-create-dnszone-portal/creating150.png)

8. 새 영역이 만들어지면 대시보드에서 새 영역에 대한 블레이드가 열립니다.

## <a name="view-records"></a>레코드 보기

DNS 영역을 만들면 다음 레코드도 생성됩니다.

* “SOA(권한 시작)” 레코드. SOA는 각 DNS 영역의 루트에 있습니다.
* 권한이 있는 NS(이름 서버) 레코드. 영역을 호스트하는 이름 서버를 보여 줍니다. Azure DNS는 이름 서버 풀을 사용하므로 Azure DNS의 각 영역에 다른 이름 서버가 할당될 수 있습니다. 자세한 내용은 [Azure DNS에 도메인 위임](dns-domain-delegation.md) 을 참조하세요.

DNS 영역 블레이드의 아래쪽에서 DNS 영역에 대한 레코드 집합을 볼 수 있습니다.

![영역](./media/dns-getstarted-create-dnszone-portal/viewzone500.png)

## <a name="test-name-servers"></a>이름 서버 테스트

nslookup, dig, [Resolve-DnsName PowerShell cmdlet](https://technet.microsoft.com/library/jj590781.aspx)과 같은 DNS 도구를 사용하여 DNS 영역이 Azure DNS 이름 서버에 존재하는지 테스트할 수 있습니다.

Azure DNS에서 새 영역을 사용하도록 도메인을 아직 위임하지 않은 경우 DNS 쿼리를 영역에 대한 이름 서버 중 하나로 직접 보내야 합니다. 영역에 대한 이름 서버는 Azure 포털에 제공됩니다.
    
![영역](./media/dns-getstarted-create-dnszone-portal/viewzonens500.png)

아래 명령을 사용자 영역의 올바른 이름 서버로 대체해야 합니다.

    nslookup
    > set type=SOA
    > server ns1-01.azure-dns.com
    > contoso.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    contoso.com
            primary name server = ns1-01.azure-dns.com
            responsible mail addr = azuredns-hostmaster.microsoft.com
            serial  = 1
            refresh = 3600 (1 hour)
            retry   = 300 (5 mins)
            expire  = 2419200 (28 days)
            default TTL = 300 (5 mins)

## <a name="next-steps"></a>다음 단계

DNS 영역을 만든 후에는 [레코드 집합 및 레코드](dns-getstarted-create-recordset-portal.md)를 만들어 인터넷 도메인에 대한 DNS 기록을 만들어야 합니다.


