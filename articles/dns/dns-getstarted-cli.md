---
title: 빠른 시작 - Azure CLI를 사용하여 Azure DNS 영역 및 레코드 만들기
description: 빠른 시작 - Azure DNS에 DNS 영역 및 레코드를 만드는 방법을 알아봅니다. Azure CLI를 사용하여 첫 번째 DNS 영역 및 레코드를 만들고 관리하는 단계별 가이드입니다.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 7/16/2018
ms.author: victorh
ms.openlocfilehash: 3fb39558ff99c35786dedc133a9d1d1a450b5928
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090125"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Azure DNS 영역 및 레코드 만들기

이 문서에서는 Windows, Mac 및 Linux에서 사용할 수 있는 Azure CLI를 사용하여 첫 번째 DNS 영역 및 레코드를 만드는 단계를 안내합니다. [Azure Portal](dns-getstarted-portal.md)이나 [Azure PowerShell](dns-getstarted-powershell.md)을 사용하여 이 단계를 수행할 수도 있습니다.

DNS 영역은 특정 도메인에 대한 DNS 레코드를 호스트하는 데 사용됩니다. Azure DNS에서 도메인 호스팅을 시작하려면 해당 도메인 이름의 DNS 영역을 만들어야 합니다. 그러면 이 DNS 영역 안에 도메인의 각 DNS 레코드가 생성됩니다. 마지막으로 DNS 영역을 인터넷에 게시하려면 도메인에 대한 이름 서버를 구성해야 합니다. 아래에서는 이러한 각 단계에 대해 설명합니다.

Azure DNS는 이제 사설 DNS 영역(현재는 공개 미리 보기)도 지원합니다. 사설 DNS 영역에 대해 자세히 알아보려면 [사설 도메인에 Azure DNS 사용](private-dns-overview.md)을 참조하세요. 사설 DNS 영역을 만드는 방법은 [CLI를 사용하여 Azure DNS 사설 영역 시작](./private-dns-getstarted-cli.md)을 참조하세요.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-the-resource-group"></a>리소스 그룹 만들기

DNS 영역을 만들기 전에 DNS 영역을 포함할 리소스 그룹을 만듭니다.

```azurecli
az group create --name MyResourceGroup --location "East US"
```

## <a name="create-a-dns-zone"></a>DNS 영역 만들기

`az network dns zone create` 명령을 사용하여 DNS 영역을 만듭니다. 이 명령에 대한 도움말을 보려면 `az network dns zone create -h`을 입력합니다.

다음 예제에서는 *MyResourceGroup*이라는 리소스 그룹에 *contoso.com*이라는 DNS 영역을 만듭니다. 예제를 사용하여 DNS 영역을 만들고 사용자 고유 값으로 대체합니다.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.com
```

## <a name="create-a-dns-record"></a>DNS 레코드 만들기

DNS 레코드를 만들려면 `az network dns record-set [record type] add-record` 명령을 사용합니다. 도움말과 A 레코드에 대한 예제는 `azure network dns record-set A add-record -h`을 참조하세요.

다음 예제에서는 리소스 그룹 "MyResourceGroup"에서 DNS 영역 "contoso.com"에 상대적 이름 "www"가 포함된 레코드를 만듭니다. 레코드의 정규화된 이름은 “www.contoso.com”입니다. 레코드 유형은 "A"이고, IP 주소는 "1.2.3.4", 기본 TTL은 3600초(1시간)가 사용됩니다.

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.com -n www -a 1.2.3.4
```

## <a name="view-records"></a>레코드 보기

사용자 영역에 DNS 레코드를 나열하려면 다음을 사용하세요.

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```

## <a name="update-name-servers"></a>이름 서버 업데이트

DNS 영역 및 레코드가 적절히 설정되었다면 Azure DNS 이름 서버를 사용하도록 도메인 이름을 구성해야 합니다. 이렇게 하면 인터넷에 있는 다른 사용자가 DNS 레코드를 찾을 수 있습니다.

영역에 대한 이름 서버는 `az network dns zone show` 명령으로 지정됩니다. 이름 서버 이름을 보려면 다음 예에 표시된 것처럼 JSON 출력을 사용하세요.

```azurecli
az network dns zone show -g MyResourceGroup -n contoso.com -o json

{
  "etag": "00000003-0000-0000-b40d-0996b97ed101",
  "id": "/subscriptions/a385a691-bd93-41b0-8084-8213ebc5bff7/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-01.azure-dns.com.",
    "ns2-01.azure-dns.net.",
    "ns3-01.azure-dns.org.",
    "ns4-01.azure-dns.info."
  ],
  "numberOfRecordSets": 3,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

이러한 이름 서버는 사용자가 도메인 이름을 구입한 도메인 이름 등록 기관에서 구성해야 합니다. 등록 기관에서 도메인에 대한 이름 서버를 설정하는 옵션을 제공합니다. 자세한 내용은 [Azure DNS에 도메인 위임](dns-domain-delegation.md)을 참조하세요.

## <a name="delete-all-resources"></a>모든 리소스 삭제
 
더 이상 필요하지 않은 경우 리소스 그룹을 삭제하면 이 빠른 시작에서 만든 모든 리소스를 삭제할 수 있습니다.

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>다음 단계

Azure CLI를 사용하여 첫 번째 DNS 영역과 레코드를 만들었으면 사용자 지정 도메인에서 웹앱의 레코드를 만들 수 있습니다.

> [!div class="nextstepaction"]
> [사용자 지정 도메인에서 웹앱에 대한 DNS 레코드 만들기](./dns-web-sites-custom-domain.md)
