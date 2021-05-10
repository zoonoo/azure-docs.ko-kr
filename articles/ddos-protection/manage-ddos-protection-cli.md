---
title: Azure CLI를 사용하여 Azure DDoS Protection 계획 만들기 및 구성
description: Azure CLI를 사용하여 DDoS Protection 계획을 만드는 방법을 알아봅니다.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.openlocfilehash: 8a8da50dc703d59dc16b5cb6253d39aeb33fd76d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777638"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Azure DDoS Protection Standard 만들기 및 구성

Azure CLI를 사용하여 Azure DDoS Protection Standard를 시작합니다. 

DDoS 보호 계획은 구독 전반에 걸쳐 DDoS 보호 표준을 사용하도록 설정된 일단의 가상 네트워크를 정의합니다. 조직에 대해 하나의 DDoS 보호 계획을 구성하고 여러 구독의 가상 네트워크를 동일한 계획에 연결할 수 있습니다. 

이 빠른 시작에서는 DDoS 보호 계획을 만들고 가상 네트워크에 연결 합니다. 

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 로컬로 설치된 Azure CLI 또는 Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

로컬로 CLI를 설치하여 사용하려는 경우 이 빠른 시작을 진행하려면 Azure CLI 버전 2.0.28 이상이 필요합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-ddos-protection-plan"></a>DDoS 보호 계획 만들기

Azure에서 관련 리소스를 리소스 그룹에 할당합니다. 기존 리소스 그룹을 사용하거나 리소스 그룹을 새로 만들 수 있습니다.

리소스 그룹을 만들려면 [az group create](/cli/azure/group#az_group_create)를 사용합니다. 이 예에서는 리소스 그룹 이름을 _MyResourceGroup_ 으로 지정하고 _미국 동부_ 위치를 사용합니다.

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus
```

이제 _MyDdosProtectionPlan_ 이라는 DDoS 보호 계획을 만듭니다.

```azurecli-interactive
az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

## <a name="enable-ddos-protection-for-a-virtual-network"></a>가상 네트워크에 DDoS 보호 사용

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>새 가상 네트워크에 DDoS 보호 사용

가상 네트워크를 만들 때 DDoS 보호를 사용하도록 설정할 수 있습니다. 이 예에서는 가상 네트워크 이름을 _MyVnet_ 으로 지정합니다. 

```azurecli-interactive
az network vnet create \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --location eastus \
    --ddos-protection true
    --ddos-protection-plan MyDdosProtectionPlan
```

가상 네트워크에 대해 DDoS 표준을 사용하도록 설정하면 가상 네트워크를 다른 리소스 그룹 또는 구독으로 이동할 수 없습니다. DDoS 표준을 사용하도록 설정된 가상 네트워크를 이동해야 하는 경우 먼저 DDoS 표준을 사용하지 않도록 설정하고, 가상 네트워크를 이동한 다음, DDoS 표준을 사용하도록 설정합니다. 이동 후에는 가상 네트워크의 모든 보호된 공용 IP 주소에 대한 자동 조정된 정책 임계값이 다시 설정됩니다.

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>기존 가상 네트워크에 DDoS 보호 사용

[DDoS 보호 계획을 만들](#create-a-ddos-protection-plan) 때 하나 이상의 가상 네트워크를 계획에 연결할 수 있습니다. 둘 이상의 가상 네트워크를 추가하려면 이름 또는 ID를 공백으로 구분하여 나열하면 됩니다. 이 예에서는 _MyVnet_ 을 추가합니다.

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
    --vnets MyVnet
```

또는 지정된 가상 네트워크에 DDoS 보호를 사용하도록 설정할 수 있습니다.

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --ddos-protection true
    --ddos-protection-plan MyDdosProtectionPlan
```

## <a name="validate-and-test"></a>유효성 검사 및 테스트

먼저 DDoS 보호 계획의 세부 정보를 확인합니다.

```azurecli-interactive
az network ddos-protection show \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

명령이 DDoS 보호 계획의 올바른 세부 정보를 반환하는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서를 위해 리소스를 보관할 수 있습니다. 더 이상 필요 없는 경우 _MyResourceGroup_ 리소스 그룹을 삭제합니다. 리소스 그룹을 삭제하면 DDoS 보호 계획과 모든 관련 리소스도 함께 삭제됩니다. 

리소스 그룹을 삭제하려면 [az group delete](/cli/azure/group#az_group_delete)를 사용합니다.

```azurecli-interactive
az group delete \
--name MyResourceGroup 
```

지정된 가상 네트워크를 업데이트하여 DDoS 보호를 사용하지 않도록 설정

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --ddos-protection false
    --ddos-protection-plan ""
```

DDoS 보호 계획을 삭제하려면 먼저 모든 가상 네트워크를 해당 계획에서 분리해야 합니다. 

## <a name="next-steps"></a>다음 단계

DDoS 보호 계획에 대한 원격 분석을 보고 구성하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [DDoS 보호 원격 분석 보기 및 구성](telemetry.md)
