---
title: "Azure CLI를 사용하여 Azure 서비스에 대한 역방향 DNS 레코드 관리 | Microsoft 문서"
description: "Resource Manager에서 Azure CLI를 사용하여 Azure 서비스에 대한 역방향 DNS 레코드 또는 PTR 레코드를 관리하는 방법"
services: DNS
documentationcenter: na
author: s-malone
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: c655707e-1156-4893-b163-0b228ffd25d2
ms.service: DNS
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: smalone
translationtype: Human Translation
ms.sourcegitcommit: 1481fcb070f383d158c5a6ae32504e498de4a66b
ms.openlocfilehash: 1ac7bc232082100cd0f0fe3e337930a6c5e9ede9
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure 서비스에 대한 역방향 DNS 레코드를 관리하는 방법

[!INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]


[!INCLUDE [dns-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]

## <a name="cli-versions-to-complete-the-task"></a>태스크를 완료하기 위한 CLI 버전

다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

* [Azure CLI 1.0](dns-reverse-dns-record-operations-cli-nodejs.md) - 클래식 및 리소스 관리 배포 모델용 CLI
* [Azure CLI 2.0](dns-reverse-dns-record-operations-cli.md) - 리소스 관리 배포 모델용 차세대 CLI

## <a name="introduction"></a>소개

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

클래식 배포 모델에 대한 자세한 내용은 [Azure PowerShell을 사용하여 Azure 서비스(클래식)에 대한 역방향 DNS 레코드 관리 방법](dns-reverse-dns-record-operations-classic-ps.md)을 참조하세요.


## <a name="validation-of-reverse-dns-records"></a>역방향 DNS 레코드의 유효성 검사
타사에서 DNS 도메인에 매핑하는 역방향 DNS 레코드를 만들 수 없도록 하기 위해 Azure에서는 다음 중 하나에 해당하는 역방향 DNS 레코드만 만들도록 허용합니다.

* "ReverseFqdn"이 지정되어 있는 공용 IP 주소 리소스에 대한 "Fqdn"이거나, 동일한 구독 내의 공용 IP 주소에 대한 "Fqdn"과 같습니다. 예를 들어 "ReverseFqdn"이 "contosoapp1.northus.cloudapp.azure.com."입니다.
* "ReverseFqdn" 정방향이 지정되어 있는 공용 IP 주소의 이름 또는 IP 주소로 확인되거나, 동일한 구독 내의 공용 IP 주소 "Fqdn" 또는 IP로 확인됩니다. 예를 들어 "ReverseFqdn"이 "contosoapp1.northus.cloudapp.azure.com."에 대한 CName 별칭인 "app1.contoso.com."입니다.

유효성 검사는 공용 IP 주소의 역방향 DNS 속성이 설정되거나 수정되는 경우에만 수행됩니다. 유효성 재검사는 정기적으로 수행되지 않습니다.

## <a name="add-reverse-dns-to-existing-public-ip-addresses"></a>기존 공용 IP 주소에 역방향 DNS 추가
azure network public-ip set을 사용하여 기존 공용 IP 주소에 역방향 DNS를 추가할 수 있습니다.

```azurecli
az network public-ip update --resource-group NRP-DemoRG-PS --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

DNS 이름이 없는 기존 공용 IP 주소에 역방향 DNS를 추가하려면 DNS 이름도 지정해야 합니다. azure network public-ip set을 사용하여 추가할 수 있습니다.

```azurecli
az network public-ip update --resource-group NRP-DemoRG-PS --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

## <a name="create-a-public-ip-address-with-reverse-dns"></a>역방향 DNS를 사용하여 공용 IP 주소 만들기
azure network public-ip create를 사용하여 지정된 역방향 DNS 속성을 사용하여 새 공용 IP 주소를 추가할 수 있습니다.

```azurecli
az network public-ip create --name PublicIp --resource-group NRP-DemoRG-PS --location westcentralus --dns-name contosoapp1 --reverse-fqdn contosoapp1.westcentralus.cloudapp.azure.com
```

## <a name="view-reverse-dns-for-existing-public-ip-addresses"></a>기존 공용 IP 주소에 대한 역방향 DNS 보기
azure network public-ip show를 사용하여 기존 공용 IP 주소에 구성된 값을 볼 수 있습니다.

```azurecli
 az network public-ip show --name PublicIp --resource-group NRP-DemoRG-PS
```

## <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>기존 공용 IP 주소에서 역방향 DNS 제거
azure network public-ip set을 사용하여 기존 공용 IP 주소에서 역방향 DNS 속성을 제거할 수 있습니다. ReverseFqdn 속성 값을 공백으로 설정하면 됩니다.

```azurecli
az network public-ip update --resource-group NRP-DemoRG-PS --name PublicIp --reverse-fqdn ""
```

[!INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[!INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]


