---
title: "CLI를 사용하여 DNS 영역 만들기 | Microsoft Docs"
description: "Azure DNS에 대한 DNS 영역을 만들고 CLI를 사용하여 DNS 도메인 호스팅을 시작하는 방법을 단계별로 알아봅니다."
services: dns
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: 
ms.assetid: 1514426a-133c-491a-aa27-ee0962cea9dc
ms.service: dns
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e2b93d8788f8f36ff6bc04a33d1f35a86cd49973


---
# <a name="create-an-azure-dns-zone-using-cli"></a>CLI를 사용하여 Azure DNS 영역 만들기
> [!div class="op_single_selector"]
> * [Azure 포털](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI](dns-getstarted-create-dnszone-cli.md)
> 
> 

이 문서에서는 CLI를 사용하여 DNS 영역을 만드는 단계를 안내합니다. PowerShell 또는 Azure 포털을 사용하여 DNS 영역을 만들 수도 있습니다.

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="before-you-begin"></a>시작하기 전에
이 지침은 Microsoft Azure CLI를 사용합니다. Azure DNS 명령을 사용하려면 최신 Azure CLI(0.9.8 이상)로 업데이트해야 합니다. `azure -v` 를 입력하여 현재 컴퓨터에 설치되어 있는 Azure CLI 버전을 확인합니다.

## <a name="step-1-set-up-azure-cli"></a>1단계 - Azure CLI 설치
### <a name="1-install-azure-cli"></a>1. Azure CLI 설치
Windows, Linux 또는 MAC용 Azure CLI를 설치할 수 있습니다. Azure CLI를 사용하여 Azure DNS를 관리하려면 먼저 다음 단계를 완료해야 합니다. 자세한 내용은 [Azure CLI 설치](../xplat-cli-install.md)를 참조하세요. DNS 명령을 사용하려면 Azure CLI 버전 0.9.8 이상이 필요합니다.

CLI에 대한 모든 네트워크 공급자 명령은 다음 명령을 사용하여 찾을 수 있습니다.

    azure network

### <a name="2-switch-cli-mode"></a>2. CLI 모드 전환
Azure DNS는 Azure 리소스 관리자를 사용합니다. ARM 명령을 사용하려면 CLI 모드를 전환해야 합니다.

    azure config mode arm

### <a name="3-sign-in-to-your-azure-account"></a>3. Azure 계정에 로그인
자격 증명을 사용하여 인증하라는 메시지가 표시됩니다. ORGID 계정만을 사용할 수 있습니다.

    azure login -u "username"

### <a name="4-select-the-subscription"></a>4. 구독 선택
사용할 Azure 구독을 선택합니다.

    azure account set "subscription name"

### <a name="5-create-a-resource-group"></a>5. 리소스 그룹 만들기
Azure 리소스 관리자를 사용하려면 모든 리소스 그룹이 위치를 지정해야 합니다. 이 위치는 해당 리소스 그룹에서 리소스의 기본 위치로 사용됩니다. 그러나 모든 DNS 리소스는 국가별이 아니라 전역이므로 리소스 그룹의 위치 선택이 Azure DNS에 영향을 주지 않습니다.

기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뛸 수 있습니다.

    azure group create -n myresourcegroup --location "West US"


### <a name="6-register"></a>6. 등록
Azure DNS 서비스는 Microsoft.Network 리소스 공급자에 의해 관리됩니다. Azure DNS를 사용하려면 먼저 이 리소스 공급자를 사용하도록 Azure 구독을 등록해야 합니다. 이 작업은 각 구독에 대해 한 번만 수행하면 됩니다.

    azure provider register --namespace Microsoft.Network


## <a name="step-2-create-a-dns-zone"></a>2단계 - DNS 영역 만들기
`azure network dns zone create` 명령을 사용하여 DNS 영역을 만듭니다. 태그와 함께 DNS 영역을 만들 수 있습니다(선택 사항). 태그는 이름-값 쌍의 목록으로, Azure Resource Manager에서 대금 청구 또는 그룹화를 위해 리소스에 레이블을 지정하는 데 사용됩니다. 태그에 대한 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](../resource-group-using-tags.md)을 참조하십시오.

Azure DNS에서는 종료하는 **'.'**없이 영역 이름을 지정해야 합니다. 예를 들어 '**contoso.com.**' 대신 '**contoso.com**'으로 지정합니다.

### <a name="to-create-a-dns-zone"></a>DNS 영역을 만들려면
아래 예제에서는 *MyResourceGroup*이라는 리소스 그룹에 *contoso.com*이라는 DNS 영역을 만듭니다.

예제를 사용하여 DNS 영역을 만들고 사용자 고유 값으로 대체합니다.

    azure network dns zone create myresourcegroup contoso.com

### <a name="to-create-a-dns-zone-and-tags"></a>DNS 영역 및 태그를 만들려면
Azure DNS CLI에서는 옵션 *-Tag* 매개 변수를 사용하여 DNS 영역의 태그를 지정할 수 있습니다. 다음 예제에서는 두 태그 project = demo 및 env = test를 사용하여 DNS 영역을 만드는 방법을 보여 줍니다.

아래 예제를 사용하여 DNS 영역과 태그를 만들고 사용자 고유 값으로 대체합니다.

    azure network dns zone create myresourcegroup contoso.com -t "project=demo";"env=test"

## <a name="view-records"></a>레코드 보기
DNS 영역을 만들면 다음과 같은 DNS 레코드도 생성됩니다.

* 'SOA(권한 시작)' 레코드. 각 DNS 영역의 루트에 있습니다.
* 권한이 있는 NS(이름 서버) 레코드. 영역을 호스트하는 이름 서버를 보여 줍니다. Azure DNS는 이름 서버 풀을 사용하므로 Azure DNS의 각 영역에 다른 이름 서버가 할당될 수 있습니다. 자세한 내용은 [Azure DNS에 도메인 위임](dns-domain-delegation.md) 을 참조하세요.

이러한 레코드를 보려면 `azure network dns-record-set show`를 참조하세요.<BR>
*사용법: network dns record-set show <resource-group> <dns-zone-name> <name> <type>*

아래 예제에서 *myresourcegroup* 리소스 그룹, *"@"* 레코드 집합 이름(루트 레코드인 경우)으로 명령을 실행하고 *SOA*를 입력하면 다음과 같은 출력이 생성됩니다.

    azure network dns record-set show myresourcegroup "contoso.com" "@" SOA
    info:    Executing command network dns-record-set show
    + Looking up the DNS record set "@"
    data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/SOA
    data:    Location                        : global
    data:    TTL                             : 3600
    data:    SOA record:
    data:      Email                         : msnhst.microsoft.com
    data:      Expire time                   : 604800
    data:      Host                          : edge1.azuredns-cloud.net
    data:      Minimum TTL                   : 300
    data:      Refresh time                  : 900
    data:      Retry time                    : 300
    data:                                    :
<BR>
 영역에 생성된 NS 레코드를 보려면 다음 명령을 사용합니다.

    azure network dns record-set show myresourcegroup "contoso.com" "@" NS
    info:    Executing command network dns-record-set show
    + Looking up the DNS record set "@"
    data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/NS
    data:    Location                        : global
    data:    TTL                             : 3600
    data:    NS records
    data:        Name server domain name     : ns1-05.azure-dns.com
    data:        Name server domain name     : ns2-05.azure-dns.net
    data:        Name server domain name     : ns3-05.azure-dns.org
    data:        Name server domain name     : ns4-05.azure-dns.info
    data:
    info:    network dns-record-set show command OK

> [!NOTE]
> DNS 영역의 루트(또는 *apex*)에 있는 레코드 집합은 레코드 집합 이름으로 **@** 를 사용합니다.
> 
> 

## <a name="test"></a>테스트
nslookup, DIG, `Resolve-DnsName` PowerShell cmdlet과 같은 DNS 도구를 사용하여 DNS 영역을 테스트할 수 있습니다.

Azure DNS에서 새 영역을 사용하도록 도메인을 아직 위임하지 않은 경우 DNS 쿼리를 영역에 대한 이름 서버 중 하나로 직접 보내야 합니다. 사용자의 영역에 대한 이름 서버는 "azure network dns record-set show" 명령에 의해 나열된 것처럼 NS 레코드에 제공됩니다. 아래 명령을 사용자 영역의 올바른 값으로 대체해야 합니다.

다음 예에서는 DIG를 사용하여 DNS 영역에 할당된 이름 서버를 사용하는 도메인 contoso.com을 쿼리합니다. 쿼리는 *@<name server for the zone>* 을 사용한 이름 서버와 DIG를 사용하는 영역 이름을 가리켜야 합니다.

     <<>> DiG 9.10.2-P2 <<>> @ns1-05.azure-dns.com contoso.com
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
    contoso.com.         300     IN      SOA     edge1.azuredns-cloud.net.
    msnhst.microsoft.com. 6 900 300 604800 300

    Query time: 93 msec
    SERVER: 208.76.47.5#53(208.76.47.5)
    WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
    MSG SIZE  rcvd: 120

## <a name="next-steps"></a>다음 단계
DNS 영역을 만든 후에는 [레코드 집합 및 레코드](dns-getstarted-create-recordset-cli.md) 를 만들어 인터넷 도메인에 대한 이름 확인을 시작해야 합니다.




<!--HONumber=Nov16_HO2-->


