---
title: "Azure CLI를 사용하여 Linux VM 이미지 선택 | Microsoft Docs"
description: "리소스 관리자 배포 모델로 Linux 가상 컴퓨터를 만들 경우 이미지의 게시자, 제품 및 SKU를 확인하는 방법에 대해 알아보세요."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7a858e38-4f17-4e8e-a28a-c7f801101721
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/15/2017
ms.author: rasquill
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: fa842efd99718be7fa9eaf8aac8030c32cbceeec
ms.openlocfilehash: e9be064425ae6b9048098333cc664310e7128314
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-find-linux-vm-images-with-the-azure-cli"></a>Azure CLI를 사용하여 Linux VM 이미지를 찾는 방법
이 항목은 배포할 수 있는 각 위치에 대한 게시자, 제안, SKU 및 버전을 찾는 방법을 설명합니다. 


## <a name="use-azure-cli-20"></a>Azure CLI 2.0 사용

[Azure CLI 2.0을 설치](https://docs.microsoft.com/cli/azure/install-az-cli2)한 후 `az vm image list` 명령을 사용하여 인기 있는 VM 이미지의 캐시된 목록을 확인합니다. 예를 들어, 명령 `az vm image list -o table`의 다음 예제가 표시됩니다.

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               14.04.4-LTS         Canonical:UbuntuServer:14.04.4-LTS:latest                       UbuntuLTS            latest
CentOS         OpenLogic               7.2                 OpenLogic:CentOS:7.2:latest                                     CentOS               latest
openSUSE       SUSE                    13.2                SUSE:openSUSE:13.2:latest                                       openSUSE             latest
RHEL           RedHat                  7.2                 RedHat:RHEL:7.2:latest                                          RHEL                 latest
SLES           SUSE                    12-SP1              SUSE:SLES:12-SP1:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

### <a name="finding-all-current-images"></a>모든 현재 이미지 찾기

모든 이미지의 현재 목록을 가져오려면 `az vm image list` 명령과 `--all` 옵션을 사용합니다. Azure CLI 1.0 명령과 달리 `az vm image list --all` 명령은 기본적으로(특정 `--location` 인수를 지정하지 않으면) **westus**의 모든 이미지를 반환하기 때문에 `--all` 명령을 완료하려면 시간이 오래 걸립니다. 대화형으로 조사하려는 경우 `az vm image list --all > allImages.json`을 사용하면, Azure에서 현재 사용 가능한 모든 이미지 목록을 반환하고 로컬 사용을 위해 파일로 저장합니다. 

이미 옵션을 한 가지 이상 생각하고 있는 경우 몇 가지 옵션 중 하나를 지정하여 특정 위치, 제품, 게시자 또는 sku로 검색을 제한할 수 있습니다. 위치를 지정하지 않으면 **westus** 값은 반환됩니다.

### <a name="find-specific-images"></a>특정 이미지 찾기

특정 정보를 찾는 필터로 `az vm image list`을 사용합니다. 예를 들어, 다음은 **Debian**(`--all`스위치 없이 공용 이미지의 로컬 캐시를 검색만 한다는 것을 기억하세요)에 대해 사용할 수 있는 **제품**을 표시합니다.

```azurecli
az vm image list --offer Debian -o table --all
```

출력은 다음과 같습니다. 
```
Offer   Publisher   Sku   Urn                              Version
------  ---------   ---   -------------------------------  -------------
Debian  credativ    8     credativ:Debian:8:8.0.201701180  8.0.201701180

<list shortened for the example>
```

**-게시자** 및 **-sku** 옵션에 유사한 필터를 수행할 수 있습니다. 모든 Debian 이미지를 찾으려면 **-Deb 제품** 또는 모든 Microsoft 게시 이미지를 찾으려면 **-게시자 Micr**를 검색하는 등 필터에서 부분적으로 매치를 수행할 수도 있습니다.

배포하고 있는 위치를 알고 있는 경우 `az vm image list-skus`, `az vm image list-offers` 및 `az vm image list-publishers` 명령과 함께 일반 이미지 검색 결과를 사용하여 원하는 것이 무엇이고 배포할 수 있는 위치가 어디인지 정확히 찾을 수 있습니다. 예를 들어 앞의 예제에서 `credativ`에서 Debian 제품을 가지고 있는지 알고 있다면 `--location` 및 기타 옵션을 사용하여 원하는 것을 정확하게 찾을 수 있습니다. 다음 예제에서는 **westeurope**의 Debian 8 이미지를 찾습니다.

```azurecli 
az vm image show -l westeurope -f debian -p credativ --skus 8 --version 8.0.201701180
```

출력은 다음과 같습니다.

```json
{
  "dataDiskImages": [],
  "id": "/Subscriptions/<guid>/Providers/Microsoft.Compute/Locations/westeurope/Publishers/credativ/ArtifactTypes/VMImage/Offers/debian/Skus/8/Versions/8.0.201701180",
  "location": "westeurope",
  "name": "8.0.201701180",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

## <a name="use-azure-cli-10"></a>Azure CLI 1.0 사용 

> [!NOTE]
> 이 문서에서는 Azure Resource Manager 배포 모델을 지원하는 Azure CLI 1.0 또는 Azure PowerShell을 사용하여 가상 컴퓨터 이미지를 탐색 및 선택하는 방법을 설명합니다. 전제 조건으로 리소스 관리자 모드를 변경합니다. Azure CLI로 `azure config mode arm`을(를) 입력하여 해당 모드를 입력합니다. 
> 

이미지를 찾는 가장 빠른 방법은 `azure vm image list` 명령을 호출하고 위치, 게시자 이름(대/소문자 구분 없음) 및 제품(제품을 알고 있는 경우)을 전달하는 것입니다. 예를 들어 다음 목록은 "UbuntuServer" 제안에 대한 게시자가 “Canonical"임을 알고 있는 경우의 간단한 예제일 뿐이며, 대부분의 목록은 매우 깁니다.

```azurecli
azure vm image list westus canonical ubuntuserver
info:    Executing command vm image list
warn:    The parameter --sku if specified will be ignored
+ Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
data:    Publisher  Offer         Sku                OS     Version          Location  Urn
data:    ---------  ------------  -----------------  -----  ---------------  --------  --------------------------------------------------------
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201604203  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201604203
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201605161  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201605161
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201606100  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606100
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201606270  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606270
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201607210  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201607210
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201608150  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201608150
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607220  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607220
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607230  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607230
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607240  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607240
```

**Urn** 열은 `azure vm quick-create`에 전달되는 양식입니다.

하지만 사용 가능한 항목은 아직 알 수 없습니다. 이 경우에 `azure vm image list-publishers` 명령을 사용하고 프롬프트에서 데이터 센터 위치를 지정하여 이미지를 탐색할 수 있습니다. 예를 들어 다음은 미국 서부 위치의 모든 이미지 게시자를 나열합니다(소문자로 표시하고 표준 위치에서 공백을 제거하여 위치 인수 전달).

```azurecli
azure vm image list-publishers
info:    Executing command vm image list-publishers
Location: westus
+ Getting virtual machine and/or extension image publishers (Location: "westus")
data:    Publisher                                       Location
data:    ----------------------------------------------  --------
data:    a10networks                                     westus  
data:    aiscaler-cache-control-ddos-and-url-rewriting-  westus  
data:    alertlogic                                      westus  
data:    AlertLogic.Extension                            westus  
```

이러한 목록은 매우 길 수 있으며 위의 예제 목록은 일부에 불과합니다. 미국 서부 위치의 이미지 게시자가 Canonical임을 알고 있다고 가정합니다. 이제 다음 예제와 같이 `azure vm image list-offers` 를 호출하여 제안을 찾은 다음 프롬프트에 위치와 게시자를 전달할 수 있습니다.

```azurecli
azure vm image list-offers
info:    Executing command vm image list-offers
Location: westus
Publisher: canonical
+ Getting virtual machine image offers (Publisher: "canonical" Location:"westus")
data:    Publisher  Offer                      Location
data:    ---------  -------------------------  --------
data:    canonical  Ubuntu15.04Snappy          westus
data:    canonical  Ubuntu15.04SnappyDocker    westus
data:    canonical  UbunturollingSnappy        westus
data:    canonical  UbuntuServer               westus
data:    canonical  Ubuntu_Snappy_Core         westus
data:    canonical  Ubuntu_Snappy_Core_Docker  westus
info:    vm image list-offers command OK
```

이제 미국 서부 지역에서 Canonical이 Azure에 **UbuntuServer** 제안을 게시함을 알고 있습니다. 하지만 SKU는 무엇입니까? 이런 값을 가져오려면 `azure vm image list-skus`을 호출하고 검색한 위치, 게시자 및 제품으로 프롬프트에 응답합니다.

```azurecli
azure vm image list-skus
info:    Executing command vm image list-skus
Location: westus
Publisher: canonical
Offer: ubuntuserver
+ Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
data:    Publisher  Offer         sku                Location
data:    ---------  ------------  -----------------  --------
data:    canonical  ubuntuserver  12.04.2-LTS        westus
data:    canonical  ubuntuserver  12.04.3-LTS        westus
data:    canonical  ubuntuserver  12.04.4-LTS        westus
data:    canonical  ubuntuserver  12.04.5-DAILY-LTS  westus
data:    canonical  ubuntuserver  12.04.5-LTS        westus
data:    canonical  ubuntuserver  12.10              westus
data:    canonical  ubuntuserver  14.04-beta         westus
data:    canonical  ubuntuserver  14.04.0-LTS        westus
data:    canonical  ubuntuserver  14.04.1-LTS        westus
data:    canonical  ubuntuserver  14.04.2-LTS        westus
data:    canonical  ubuntuserver  14.04.3-LTS        westus
data:    canonical  ubuntuserver  14.04.4-DAILY-LTS  westus
data:    canonical  ubuntuserver  14.04.4-LTS        westus
data:    canonical  ubuntuserver  14.04.5-DAILY-LTS  westus
data:    canonical  ubuntuserver  14.04.5-LTS        westus
data:    canonical  ubuntuserver  14.10              westus
data:    canonical  ubuntuserver  14.10-beta         westus
data:    canonical  ubuntuserver  14.10-DAILY        westus
data:    canonical  ubuntuserver  15.04              westus
data:    canonical  ubuntuserver  15.04-beta         westus
data:    canonical  ubuntuserver  15.04-DAILY        westus
data:    canonical  ubuntuserver  15.10              westus
data:    canonical  ubuntuserver  15.10-alpha        westus
data:    canonical  ubuntuserver  15.10-beta         westus
data:    canonical  ubuntuserver  15.10-DAILY        westus
data:    canonical  ubuntuserver  16.04-alpha        westus
data:    canonical  ubuntuserver  16.04-beta         westus
data:    canonical  ubuntuserver  16.04.0-DAILY-LTS  westus
data:    canonical  ubuntuserver  16.04.0-LTS        westus
data:    canonical  ubuntuserver  16.10-DAILY        westus
info:    vm image list-skus command OK
```

이제 이 정보로 맨 위쪽에 원래 호출을 호출하여 원하는 이미지를 정확하게 찾을 수 있습니다.

```azurecli
azure vm image list westus canonical ubuntuserver 16.04.0-LTS
info:    Executing command vm image list
+ Getting virtual machine images (Publisher:"canonical" Offer:"ubuntuserver" Sku: "16.04.0-LTS" Location:"westus")
data:    Publisher  Offer         Sku          OS     Version          Location  Urn
data:    ---------  ------------  -----------  -----  ---------------  --------  --------------------------------------------------
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201604203  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201604203
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201605161  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201605161
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201606100  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606100
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201606270  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606270
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201607210  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201607210
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201608150  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201608150
info:    vm image list command OK
```

## <a name="next-steps"></a>다음 단계
이제 사용할 이미지를 정밀하게 선택할 수 있습니다. 방금 찾은 URN 정보를 사용하여 가상 컴퓨터를 빠르게 만들거나 해당 URN 정보로 템플릿을 사용하려면 [Azure CLI를 사용하여 Linux VM 만들기](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

