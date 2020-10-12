---
title: VM에 대한 라우팅 기본 설정 구성 - Azure CLI
description: Azure CLI(명령줄 인터페이스)를 통해 라우팅 기본 설정을 선택하여 공용 IP 주소가 있는 VM을 만드는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.custom: devx-track-azurecli
ms.openlocfilehash: 49b0df3e750d4d23cb6a64f3f7266613fd2f2981
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87501834"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-cli"></a>Azure CLI를 사용하여 VM에 대한 라우팅 기본 설정 구성

이 문서에서는 가상 머신의 라우팅 기본 설정을 구성하는 방법을 보여줍니다. 라우팅 기본 설정 옵션으로 **인터넷**을 선택하면 VM의 인터넷 바인딩 트래픽이 ISP 네트워크를 통해 라우팅됩니다. 기본 라우팅은 Microsoft 글로벌 네트워크를 통해 전달됩니다.

이 문서에서는 Azure CLI를 사용하여 공용 인터넷을 통해 트래픽을 라우팅하도록 설정된 공용 IP로 가상 머신을 만드는 방법을 보여줍니다.

> [!IMPORTANT]
> 라우팅 기본 설정은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="register-the-feature-for-your-subscription"></a>구독에 대한 기능 등록
라우팅 기본 설정 기능은 현재 미리 보기로 제공됩니다. 다음과 같이 구독에 대한 기능을 등록합니다.
```azurecli
az feature register --namespace Microsoft.Network --name AllowRoutingPreferenceFeature
```
## <a name="create-a-resource-group"></a>리소스 그룹 만들기
1. Cloud Shell을 사용하는 경우 2단계로 건너뜁니다. 명령 세션을 열고 `az login`를 사용하여 Azure에 로그인합니다.
2. [az group create](/cli/azure/group#az-group-create) 명령을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 미국 동부 Azure 지역의 리소스 그룹을 만듭니다.

    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```

## <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기
인터넷에서 가상 머신에 액세스하려면 공용 IP 주소를 만들어야 합니다. [az network public-ip create](/cli/azure/network/public-ip)를 사용하여 공용 IP 주소를 만듭니다. 다음 예제에서는 *미국 동부* 지역에 라우팅 기본 설정 형식 *인터넷*의 공용 IP를 만듭니다.

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```

## <a name="create-network-resources"></a>네트워크 리소스 만들기

VM을 배포하기 전에 지원되는 네트워크 리소스(네트워크 보안 그룹, 가상 네트워크 및 가상 NIC)를 만들어야 합니다.

### <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

[az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)를 사용하여 VNet에서 인바운드 및 아웃바운드 통신을 제어하는 규칙에 대한 네트워크 보안 그룹을 만듭니다.

```azurecli
az network nsg create \
--name myNSG  \
--resource-group MyResourceGroup \
--location eastus
```

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

[az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 서브넷 *mySubNet*으로 *myVNET*이라는 가상 네트워크를 만듭니다.

```azurecli
# Create a virtual network
az network vnet create \
--name myVNET \
--resource-group MyResourceGroup \
--location eastus

# Create a subnet
az network vnet subnet create \
--name mySubNet \
--resource-group MyResourceGroup \
--vnet-name myVNET \
--address-prefixes "10.0.0.0/24" \
--network-security-group myNSG
```

### <a name="create-a-nic"></a>NIC 만들기

[az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)를 사용하여 VM에 대한 가상 NIC를 만듭니다. 다음 예제에서는 VM에 연결할 가상 NIC를 만듭니다.

```azurecli-interactive
# Create a NIC
az network nic create \
--name mynic  \
--resource-group MyResourceGroup \
--network-security-group myNSG  \
--vnet-name myVNET \
--subnet mySubNet  \
--private-ip-address-version IPv4 \
--public-ip-address MyRoutingPrefIP
```

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

[az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create)로 VM을 만듭니다. 다음 예제에서는 Windows Server 2019 VM 및 아직 없는 경우 필요한 가상 네트워크 구성 요소를 만듭니다.

```azurecli
az vm create \
--name myVM \
--resource-group MyResourceGroup \
--nics mynic \
--size Standard_A2 \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
--admin-username myUserName
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az-group-delete)를 사용하여 리소스 그룹 및 해당 그룹에 포함된 모든 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>다음 단계

- [공용 IP 주소의 라우팅 기본 설정](routing-preference-overview.md)에 대해 자세히 알아봅니다.
- Azure에서 [공용 IP 주소](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)에 대해 자세히 알아봅니다.
- [공용 IP 주소 설정](virtual-network-public-ip-address.md#create-a-public-ip-address)에 대해 자세히 알아봅니다.
