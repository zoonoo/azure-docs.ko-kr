---
title: "Azure CLI를 사용하여 Linux VM 이미지 선택 | Microsoft Docs"
description: "Azure CLI를 사용하여 Marketplace VM 이미지의 게시자, 제품, SKU 및 버전을 확인하는 방법을 알아봅니다."
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7a858e38-4f17-4e8e-a28a-c7f801101721
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/11/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 72c5c2efe2c8a60f13d18b595062448e6a0d1816
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="how-to-find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>Azure CLI를 사용하여 Azure Marketplace에서 Linux VM 이미지를 찾는 방법
이 항목에서는 Azure CLI 2.0을 사용하여 Azure Marketplace에서 VM 이미지를 찾는 방법을 설명합니다. 이 정보를 사용하여 Linux VM을 만들 때 Marketplace 이미지를 지정합니다.

최신 Azure CLI 2.0을 [설치](/cli/azure/install-az-cli2)하고 Azure 계정(`az login`)에 로그인했는지 확인합니다.

## <a name="list-popular-images"></a>인기 있는 이미지 나열

`--all` 옵션을 사용하지 않고 [az vm image list](/cli/azure/vm/image#list) 명령을 실행하여 Azure Marketplace에서 인기있는 VM 이미지의 목록을 확인합니다. 예를 들어, 다음 명령을 실행하여 테이블 형식으로 캐시된 인기있는 이미지 목록을 표시합니다.

```azurecli
az vm image list --output table
```

출력은 URN(*Urn* 열의 값)을 포함합니다. 이 항목의 양식은 *게시자*:*제품*:*Sku*:*버전*과 같습니다. `az vm create`을 사용하여 VM을 만들 때 이미지를 지정하기 위해 이 값을 사용합니다. 또한 인기있는 VM 이미지 중 하나를 사용하여 VM을 만드는 경우 *UbuntuLTS*와 같은 URN 별칭을 지정할 수 있습니다.

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
```

## <a name="list-all-current-images"></a>모든 현재 이미지 나열

Marketplace에서 모든 VM 이미지의 현재 목록을 가져오려면 `az vm image list` 명령과 `--all` 옵션을 사용합니다. 이 버전의 명령을 완료하는 데 다소 시간이 걸립니다.


```azurecli
az vm image list --all
```

`--location` 옵션을 사용하여 특정 위치를 지정하지 않는 경우 기본적으로 `westus`에 대한 값이 반환됩니다. (`az configure --defaults location=<location>`을 실행하여 다른 기본 위치를 설정합니다.)


대화형으로 조사하려는 경우 로컬 파일에 출력을 전송합니다. 예:

```azurecli
az vm image list --all > allImages.json
```




## <a name="find-specific-images"></a>특정 이미지 찾기

추가 옵션과 함께 `az vm image list`을 사용하여 특정 위치, 제품, 게시자 또는 SKU에 대한 검색을 제한합니다. 예를 들어, 다음 명령은 모든 Debian 제품을 표시합니다(`--all` 스위치 없이는 공용 이미지의 로컬 캐시만을 검색함).

```azurecli
az vm image list --offer Debian --all --output table 
```

출력의 목록이 길 수 있으므로 여기에서 자릅니다. 
```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  --------------
Debian   credativ     7                  credativ:Debian:7:7.0.201602010                  7.0.201602010
Debian   credativ     7                  credativ:Debian:7:7.0.201603020                  7.0.201603020
Debian   credativ     7                  credativ:Debian:7:7.0.201604050                  7.0.201604050
Debian   credativ     7                  credativ:Debian:7:7.0.201604200                  7.0.201604200
Debian   credativ     7                  credativ:Debian:7:7.0.201606280                  7.0.201606280
Debian   credativ     7                  credativ:Debian:7:7.0.201609120                  7.0.201609120
Debian   credativ     7                  credativ:Debian:7:7.0.201611020                  7.0.201611020
...
```


`--location`, `--publisher` 및 `--sku` 옵션을 사용하여 비슷한 필터를 적용합니다. 모든 Debian 이미지를 찾는 `--offer Deb`을 검색하는 등 필터에서 부분적 일치도 수행할 수 있습니다.

예를 들어 다음 명령은 `westeurope`에 있는 모든 Debian 8개의 SKU를 나열합니다.

```azurecli
az vm image list --location westeurope --offer Deb --publisher credativ --sku 8 --all --output table
```

출력:

```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  -------------
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
...
```


## <a name="navigate-the-images"></a>이미지 이동 
위치에서 이미지를 찾는 또 다른 방법은 [az vm image list-publishers](/cli/azure/vm/image#list-publishers), [az vm image list-offers](/cli/azure/vm/image#list-offers) 및 [az vm image list-skus](/cli/azure/vm/image#list-skus)의 순서로 명령을 실행하는 것입니다. 이러한 명령을 사용하여 값을 결정합니다.

1. 이미지 게시자를 나열합니다.
2. 지정된 게시자에 제안을 나열합니다.
3. 지정된 제안에 SKU를 나열합니다.


예를 들어 다음 명령은 미국 서부에 있는 이미지 게시자를 나열합니다.

```azurecli
az vm image list-publishers --location westus --output table
```

출력:

```
Location    Name
----------  ----------------------------------------------------
westus      4psa
westus      7isolutions
westus      a10networks
westus      abiquo
westus      accellion
westus      Acronis
westus      Acronis.Backup
westus      actian_matrix
westus      actifio
westus      activeeon
westus      adatao
...
```
이러한 목록이 길 수 있으므로 이 예제의 출력은 코드 조각입니다. 이 정보를 사용하여 특정 게시자의 제안을 찾을 수 있습니다. 예를 들어 Canonical이 미국 서부에 있는 이미지 게시자인 경우 `azure vm image list-offers`을 실행하여 해당 제품을 찾을 수 있습니다. 다음 예제와 같이 위치 및 게시자를 전달합니다.

```azurecli
az vm image list-offers --location westus --publisher Canonical --output table
```

출력:

```
Location    Name
----------  -------------------------
westus      Ubuntu15.04Snappy
westus      Ubuntu15.04SnappyDocker
westus      UbunturollingSnappy
westus      UbuntuServer
westus      Ubuntu_Core
westus      Ubuntu_Snappy_Core
westus      Ubuntu_Snappy_Core_Docker
```
미국 서부 지역에서 Canonical이 Azure에 **UbuntuServer** 제품을 게시한다는 것을 확인할 수 있습니다. 하지만 SKU는 무엇입니까? 이런 값을 가져오려면 `azure vm image list-skus`을 실행하고 검색한 위치, 게시자 및 제품을 설정합니다.

```azurecli
az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
```

출력:

```
Location    Name
----------  -----------------
westus      12.04.3-LTS
westus      12.04.4-LTS
westus      12.04.5-DAILY-LTS
westus      12.04.5-LTS
westus      12.10
westus      14.04.0-LTS
westus      14.04.1-LTS
westus      14.04.2-LTS
westus      14.04.3-LTS
westus      14.04.4-LTS
westus      14.04.5-DAILY-LTS
westus      14.04.5-LTS
westus      16.04-beta
westus      16.04-DAILY-LTS
westus      16.04-LTS
westus      16.04.0-LTS
westus      16.10
westus      16.10-DAILY
westus      17.04
westus      17.04-DAILY
westus      17.10-DAILY
```

마지막으로 `az vm image list` 명령을 사용하여 원하는 SKU의 특정 버전을 찾습니다(예: **14.04-LTS**).

```azurecli
az vm image list --location westus --publisher Canonical --offer UbuntuServer --sku 16.04-LTS --all --output table
```

출력:

```
Offer         Publisher    Sku        Urn                                               Version
------------  -----------  ---------  ------------------------------------------------  ---------------
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201611220  16.04.201611220
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201611300  16.04.201611300
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612050  16.04.201612050
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612140  16.04.201612140
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612210  16.04.201612210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201701130  16.04.201701130
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702020  16.04.201702020
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702200  16.04.201702200
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702210  16.04.201702210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702240  16.04.201702240
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703020  16.04.201703020
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703030  16.04.201703030
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703070  16.04.201703070
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703270  16.04.201703270
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703280  16.04.201703280
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703300  16.04.201703300
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201705080  16.04.201705080
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201705160  16.04.201705160
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201706100  16.04.201706100
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201706191  16.04.201706191
```
## <a name="next-steps"></a>다음 단계
이제 URN 값을 기록하여 사용할 이미지를 정밀하게 선택할 수 있습니다. 이미지를 지정할 경우 필요에 따라 "최신"을 사용하여 URN에서 버전 번호를 바꿀 수 있습니다. 이 버전은 항상 최신 버전의 배포입니다. 방금 찾은 URN 정보를 사용하여 가상 컴퓨터를 빠르게 만들려면 [Azure CLI를 사용하여 Linux VM 만들기](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

