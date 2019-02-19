---
title: 빠른 시작 - Azure PowerShell을 사용하여 Azure DNS 영역 및 레코드 만들기
description: Azure DNS에 DNS 영역 및 레코드를 만드는 방법을 알아봅니다. Azure PowerShell을 사용하여 첫 번째 DNS 영역 및 레코드를 만들고 관리하는 단계별 빠른 시작입니다.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 12/4/2018
ms.author: victorh
ms.openlocfilehash: 839c97ccccbc1ce2cf646afcd27894a190eda1b0
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000894"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 Azure DNS 영역 및 레코드 만들기

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 빠른 시작에서는 Azure PowerShell을 사용하여 첫 번째 DNS 영역과 레코드를 만듭니다. [Azure Portal](dns-getstarted-portal.md) 또는 [Azure CLI](dns-getstarted-cli.md)를 사용하여 이 단계를 수행할 수도 있습니다. 

DNS 영역은 특정 도메인에 대한 DNS 레코드를 호스트하는 데 사용됩니다. Azure DNS에서 도메인 호스팅을 시작하려면 해당 도메인 이름의 DNS 영역을 만들어야 합니다. 그러면 이 DNS 영역 안에 도메인의 각 DNS 레코드가 생성됩니다. 마지막으로 DNS 영역을 인터넷에 게시하려면 도메인에 대한 이름 서버를 구성해야 합니다. 아래에서는 이러한 각 단계에 대해 설명합니다.

Azure DNS는 사설 도메인 만들기도 지원합니다. 첫 번째 사설 DNS 영역 및 레코드를 만드는 방법에 대한 단계별 지침은 [PowerShell을 사용하여 Azure 사설 DNS 영역 시작](private-dns-getstarted-powershell.md)을 참조하세요.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-the-resource-group"></a>리소스 그룹 만들기

DNS 영역을 만들기 전에 DNS 영역을 포함할 리소스 그룹을 만듭니다.

```powershell
New-AzResourceGroup -name MyResourceGroup -location "eastus"
```

## <a name="create-a-dns-zone"></a>DNS 영역 만들기

DNS 영역은 `New-AzDnsZone` cmdlet을 사용하여 생성됩니다. 다음 예제에서는 *MyResourceGroup*이라는 리소스 그룹에 *contoso.com*이라는 DNS 영역을 만듭니다. 예제를 사용하여 DNS 영역을 만들고 사용자 고유 값으로 대체합니다.

```powershell
New-AzDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup
```

## <a name="create-a-dns-record"></a>DNS 레코드 만들기

`New-AzDnsRecordSet` cmdlet을 사용하여 레코드 집합을 만듭니다. 다음 예제에서는 리소스 그룹 "MyResourceGroup"에서 DNS 영역 "contoso.com"에 상대적 이름 "www"가 포함된 레코드를 만듭니다. 레코드 집합의 정규화된 이름은 "www.contoso.com"입니다. 레코드 형식은 "A"이고, IP 주소는 "1.2.3.4"이며, TTL은 3600초입니다.

```powershell
New-AzDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4")
```

## <a name="view-records"></a>레코드 보기

사용자 영역에 DNS 레코드를 나열하려면 다음을 사용하세요.

```powershell
Get-AzDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyResourceGroup
```

## <a name="update-name-servers"></a>이름 서버 업데이트

DNS 영역 및 레코드가 적절히 설정되었다면 Azure DNS 이름 서버를 사용하도록 도메인 이름을 구성해야 합니다. 이렇게 하면 인터넷에 있는 다른 사용자가 DNS 레코드를 찾을 수 있습니다.

영역에 대한 이름 서버는 `Get-AzDnsZone` cmdlet으로 지정됩니다.

```powershell
Get-AzDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-b40d-0996b97ed101
Tags                  : {}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org., ns4-01.azure-dns.info.}
NumberOfRecordSets    : 3
MaxNumberOfRecordSets : 5000
```

이러한 이름 서버는 사용자가 도메인 이름을 구입한 도메인 이름 등록 기관에서 구성해야 합니다. 등록 기관에서 도메인에 대한 이름 서버를 설정하는 옵션을 제공합니다. 자세한 내용은 [자습서: Azure DNS에서 도메인 호스트](dns-delegate-domain-azure-dns.md#delegate-the-domain)를 참조하세요.

## <a name="delete-all-resources"></a>모든 리소스 삭제

더 이상 필요하지 않은 경우 리소스 그룹을 삭제하면 이 빠른 시작에서 만든 모든 리소스를 삭제할 수 있습니다.

```powershell
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>다음 단계

Azure PowerShell을 사용하여 첫 번째 DNS 영역과 레코드를 만들었으면 사용자 지정 도메인에서 웹앱의 레코드를 만들 수 있습니다.

> [!div class="nextstepaction"]
> [사용자 지정 도메인에서 웹앱에 대한 DNS 레코드 만들기](./dns-web-sites-custom-domain.md)

