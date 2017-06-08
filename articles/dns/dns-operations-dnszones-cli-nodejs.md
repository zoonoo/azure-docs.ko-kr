---
title: "Azure DNS에서 DNS 영역 관리 - Azure CLI 1.0 | Microsoft Docs"
description: "Azure CLI 1.0을 사용하여 DNS 영역을 관리할 수 있습니다. 이 문서에서는 Azure DNS에서 DNS 영역을 업데이트, 삭제 및 만드는 방법을 설명합니다."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 588c87749f049eff5b9e0729f6769c8367ba41e4
ms.contentlocale: ko-kr
ms.lasthandoff: 06/01/2017

---

# <a name="how-to-manage-dns-zones-in-azure-dns-using-the-azure-cli-10"></a>Azure CLI 1.0을 사용하여 Azure DNS에서 DNS 영역을 관리하는 방법

> [!div class="op_single_selector"]
> * [포털](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure CLI 1.0](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI 2.0](dns-operations-dnszones-cli.md)

이 가이드는 Windows, Mac 및 Linux에서 사용할 수 있는 플랫폼 간 Azure CLI 1.0을 사용하여 DNS 영역을 관리하는 방법을 보여 줍니다. [Azure PowerShell](dns-operations-dnszones.md) 또는 Azure Portal을 사용하여 DNS 영역을 관리할 수도 있습니다.

## <a name="cli-versions-to-complete-the-task"></a>태스크를 완료하기 위한 CLI 버전

다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

* [Azure CLI 1.0](dns-operations-dnszones-cli-nodejs.md) - 클래식 및 리소스 관리 배포 모델용 CLI.
* [Azure CLI 2.0](dns-operations-dnszones-cli.md) - 리소스 관리 배포 모델용 차세대 CLI.

## <a name="introduction"></a>소개

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-cli-setup](../../includes/dns-cli-setup-include.md)]

## <a name="getting-help"></a>도움말 보기

Azure DNS에 관련된 모든 CLI 1.0 명령은 `azure network dns`로 시작합니다. `--help` 옵션(약식 `-h`)을 사용하여 각 명령에 대한 도움말을 볼 수 있습니다.  예:

```azurecli
azure network dns -h
azure network dns zone -h
azure network dns zone create -h
```

## <a name="create-a-dns-zone"></a>DNS 영역 만들기

`azure network dns zone create` 명령을 사용하여 DNS 영역을 만듭니다. 도움말을 보려면 `azure network dns zone create -h`을 참조하세요.

다음 예제에서는 *MyResourceGroup*이라는 리소스 그룹에 *contoso.com*이라는 DNS 영역을 만듭니다.

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>태그를 사용하여 DNS 영역을 만들려면

다음 예제에서는 두 [Azure Resource Manager 태그](dns-zones-records.md#tags), *project = demo* 및 *env = test*와 함께 `--tags` 매개 변수(짧은 양식 `-t`)를 사용하여 DNS 영역을 만드는 방법을 보여 줍니다.

```azurecli
azure network dns zone create MyResourceGroup contoso.com -t "project=demo";"env=test"
```

## <a name="get-a-dns-zone"></a>DNS 영역 가져오기

DNS 영역을 가져오려면 `azure network dns zone show`를 사용합니다. 도움말을 보려면 `azure network dns zone show -h`을 참조하세요.

다음 예제에서는 DNS 영역 *contoso.com* 및 해당 관련 데이터를 리소스 그룹 *MyResourceGroup*에서 반환합니다. 

```azurecli
azure network dns zone show MyResourceGroup contoso.com
```

다음 예제는 응답입니다.

```
info:    Executing command network dns zone show
+ Looking up the dns zone "contoso.com"
data:    Id                              : /subscriptions/.../contoso.com
data:    Name                            : contoso.com
data:    Type                            : Microsoft.Network/dnszones
data:    Location                        : global
data:    Number of record sets           : 2
data:    Max number of record sets       : 5000
data:    Name servers:
data:        ns1-01.azure-dns.com.
data:        ns2-01.azure-dns.net.
data:        ns3-01.azure-dns.org.
data:        ns4-01.azure-dns.info.
data:    Tags                            : project=demo;env=test
info:    network dns zone show command OK
```

DNS 레코드는 `azure network dns zone show`에서 반환되지 않습니다. DNS 레코드를 나열하려면 `azure network dns record-set list`를 사용합니다.


## <a name="list-dns-zones"></a>DNS 영역 나열

DNS 영역을 열거하려면 `azure network dns zone list`를 사용합니다. 도움말을 보려면 `azure network dns zone list -h`을 참조하세요.

리소스 그룹을 지정하면 리소스 그룹 내의 해당 영역만 나열합니다.

```azurecli
azure network dns zone list MyResourceGroup
```

리소스 그룹을 생략하면 구독의 모든 영역이 나열됩니다.

```azurecli
azure network dns zone list 
```

## <a name="update-a-dns-zone"></a>DNS 영역 업데이트

`azure network dns zone set`를 사용하여 DNS 영역 리소스를 변경할 수 있습니다. 도움말을 보려면 `azure network dns zone set -h`을 참조하세요.

이 명령은 영역 내의 DNS 레코드 집합을 업데이트하지 않습니다([DNS 레코드를 관리하는 방법](dns-operations-recordsets-cli-nodejs.md) 참조). 영역 리소스 자체의 속성을 업데이트하는 데만 사용됩니다. 이러한 속성은 현재 영역 리소스에 대한 [Azure Resource Manager '태그'](dns-zones-records.md#tags)로 제한됩니다.

다음 예제에서는 DNS 영역에서 태그를 업데이트하는 방법을 보여 줍니다. 기존 태그는 지정된 값으로 대체됩니다.

```azurecli
azure network dns zone set MyResourceGroup contoso.com -t "team=support"
```

## <a name="delete-a-dns-zone"></a>DNS 영역 삭제

`azure network dns zone delete`를 사용하여 DNS 영역을 삭제할 수 있습니다. 도움말을 보려면 `azure network dns zone delete -h`을 참조하세요.

> [!NOTE]
> DNS 영역을 삭제하면 영역 내의 모든 DNS 레코드도 삭제됩니다. 이 작업은 취소할 수 없습니다. DNS 영역을 사용 중인 경우 영역이 삭제되면 영역을 사용하는 서비스가 실패합니다.
>
>실수로 영역이 삭제되는 것을 방지하려면 [DNS 영역 및 레코드를 보호하는 방법](dns-protect-zones-recordsets.md)을 참조하세요.

이 명령은 확인 메시지를 표시합니다. 선택적 `--quiet` 스위치(약식 `-q`)는 이 프롬프트를 표시하지 않습니다.

다음 예제는 리소스 그룹 *MyResourceGroup*에서 *contoso.com* 영역을 삭제하는 방법을 보여 줍니다.

```azurecli
azure network dns zone delete MyResourceGroup contoso.com
```

## <a name="next-steps"></a>다음 단계

DNS 영역에서 [레코드 집합 및 레코드 관리](dns-getstarted-create-recordset-cli-nodejs.md) 방법을 알아봅니다.

[Azure DNS에 도메인을 위임](dns-domain-delegation.md)하는 방법을 알아봅니다.


