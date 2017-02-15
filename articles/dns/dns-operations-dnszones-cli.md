---
title: "CLI를 사용하여 DNS 영역 관리 | Microsoft 문서"
description: "Azure CLI를 사용하여 DNS 영역을 관리할 수 있습니다. Azure DNS에서 DNS 영역을 업데이트, 삭제 및 만드는 방법입니다."
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
ms.date: 12/07/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: bfbffe7843bc178cdf289c999925c690ab82e922
ms.openlocfilehash: 927503bb18a63db1da2c92e034dbccb7707afab4

---

# <a name="how-to-manage-dns-zones-using-cli"></a>CLI를 사용하여 DNS 영역을 관리하는 방법

> [!div class="op_single_selector"]
> * [Azure CLI](dns-operations-dnszones-cli.md)
> * [PowerShell](dns-operations-dnszones.md)

이 가이드에서는 플랫폼 간 Azure CLI를 사용하여 DNS 영역 리소스를 관리하는 방법을 보여 줍니다.

이 지침은 Microsoft Azure CLI를 사용합니다. Azure DNS 명령을 사용하려면 최신 Azure CLI로 업데이트해야 합니다. Windows, Linux 또는 MAC용 Azure CLI를 설치할 수 있습니다. 자세한 내용은 [Azure CLI 설치](../xplat-cli-install.md)를 참조하세요.

Azure DNS는 Azure 리소스 관리자 전용 서비스입니다. '클래식' 배포 모델이 없습니다. Azure CLI가 Resource Manager 모드를 사용하도록 구성되었는지 확인해야 합니다. `azure config mode arm` 명령을 사용하여 이렇게 수행할 수 있습니다.

"*오류: 'dns'가 azure 명령이 아닙니다.*" 메시지가 표시되면 Azure CLI를 Resource Manager 모드가 아닌 Azure 서비스 관리 모드에서 사용하고 있기 때문입니다.

Azure DNS에 관련된 모든 CLI 명령은 `azure network dns`로 시작합니다. `--help` 옵션(약식 `-h`)을 사용하여 각 명령에 대한 도움말을 볼 수 있습니다.  예:

```azurecli
azure network dns -h
azure network dns zone -h
azure network dns zone create -h
```

## <a name="create-a-dns-zone"></a>DNS 영역 만들기

`azure network dns zone create` 명령을 사용하여 DNS 영역을 만듭니다. 도움말을 보려면 `azure network dns zone create -h`을 참조하세요.

아래 예제는 [Azure Resource Manager 태그](dns-zones-records.md#tags)를 사용하거나 사용하지 않고 DNS 영역을 만드는 방법을 보여 줍니다.

### <a name="to-create-a-dns-zone"></a>DNS 영역을 만들려면

아래 예제에서는 *MyResourceGroup*이라는 리소스 그룹에 *contoso.com*이라는 DNS 영역을 만듭니다. 예제를 사용하여 DNS 영역을 만들고 사용자 고유 값으로 대체합니다.

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>태그를 사용하여 DNS 영역을 만들려면

다음 예제에서는 두 태그 *project = demo* 및 *env = test*와 함께 `--tags` 매개 변수(짧은 양식 `-t`)를 사용하여 DNS 영역을 만드는 방법을 보여 줍니다.

```azurecli
azure network dns zone create MyResourceGroup contoso.com -t "project=demo";"env=test"
```

## <a name="get-a-dns-zone"></a>DNS 영역 가져오기

DNS 영역을 가져오려면 `azure network dns zone show`를 사용합니다. 도움말을 보려면 `azure network dns zone show -h`을 참조하세요.

다음 예제에서는 DNS 영역 *contoso.com* 및 해당 관련 데이터를 리소스 그룹 *MyResourceGroup*에서 반환합니다. 

```azurecli
azure network dns zone show MyResourceGroup contoso.com

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

이 명령은 영역 내의 DNS 레코드 집합을 업데이트하지 않습니다([DNS 레코드를 관리하는 방법](dns-operations-recordsets.md) 참조). 영역 리소스 자체의 속성을 업데이트하는 데만 사용됩니다. 이러한 속성은 현재 영역 리소스에 대한 [Azure Resource Manager '태그'](dns-zones-records.md#tags)로 제한됩니다.

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

DNS 영역에서 [레코드 집합 및 레코드 관리](dns-getstarted-create-recordset-cli.md) 방법을 알아봅니다.
<br>
[Azure DNS에 도메인을 위임](dns-domain-delegation.md)하는 방법을 알아봅니다.




<!--HONumber=Dec16_HO2-->


