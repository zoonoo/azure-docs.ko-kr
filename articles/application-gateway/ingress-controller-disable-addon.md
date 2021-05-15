---
title: Azure Kubernetes Service 클러스터에 대한 Application Gateway 수신 컨트롤러 추가 기능을 사용하지 않도록 설정하고 다시 사용하도록 설정합니다.
description: 이 문서에서는 AKS 클러스터를 위한 AGIC 추가 기능을 사용하지 않도록 설정하고 다시 사용하도록 설정하는 방법을 설명합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: fe4da0435731c536a723cb2cb43428166456360b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "84807947"
---
# <a name="disable-and-re-enable-agic-add-on-for-your-aks-cluster"></a>AKS 클러스터에 대한 AGIC 추가 기능을 사용하지 않도록 설정하고 다시 사용하도록 설정
AKS 추가 기능으로 배포된 AGIC(Application Gateway 수신 컨트롤러)를 사용하면 Azure CLI에서 한 줄로 추가 기능을 사용하거나 사용하지 않도록 설정할 수 있습니다. AGIC 추가 기능을 사용하지 않도록 설정하는 경우에는 AGIC 추가 기능을 사용하여 Application Gateway를 만들었는지 또는 AGIC 추가 기능에서 별도로 배포했는지에 따라 Application Gateway의 수명 주기가 달라집니다. AGIC 추가 기능을 사용하지 않도록 설정했거나 기존 AKS 클러스터 및 Application Gateway를 사용하여 AGIC 추가 기능을 사용하도록 설정한 경우 동일한 명령을 실행하여 AGIC 추가 기능을 다시 사용하도록 설정할 수 있습니다.

## <a name="disabling-agic-add-on-with-associated-application-gateway"></a>연결된 Application Gateway에서 AGIC 추가 기능을 사용하지 않도록 설정 
처음에 모든 항목을 설정할 때 AGIC 추가 기능에서 자동으로 Application Gateway를 배포한 경우 AGIC 추가 기능을 사용하지 않도록 설정하면 두 가지 기준에 따라 기본적으로 Application Gateway가 삭제됩니다. AGIC 추가 기능을 사용하지 않도록 설정하면 연결된 Application Gateway를 삭제해야 하는지 확인하기 위해 AGIC 추가 기능에서는 다음과 같은 두 가지 조건을 검색합니다.
- AGIC 추가 기능이 MC_* 노드 리소스 그룹에 배포된 Application Gateway인가요? 
- AGIC 추가 기능에 연결된 Application Gateway에 “created-by: ingress-appgw” 태그가 있나요? 해당 태그는 AGIC에서 Application Gateway이 추가 기능을 통해 배포되었는지 여부를 확인하는 데 사용됩니다. 

두 조건을 모두 충족하는 경우 AGIC 추가 기능을 사용하지 않도록 설정하면 AGIC 추가 기능에서 만든 Application Gateway를 삭제합니다. 그러나 Application Gateway를 사용하거나 Application Gateway 내에서 배포된 공용 IP 또는 서브넷은 삭제되지 않습니다. 첫 번째 조건을 충족하지 않는 경우 Application Gateway에 “created-by: ingress-appgw” 태그가 있는지 여부는 중요하지 않습니다. 해당 추가 기능을 사용하지 않도록 설정해도 Application Gateway는 삭제되지 않습니다. 마찬가지로 두 번째 조건을 충족하지 않는 경우, 즉 Application Gateway에 해당 태그가 없는 경우에는 추가 기능을 사용하지 않도록 설정하면 MC_* 노드 리소스 그룹의 Application Gateway는 삭제되지 않습니다. 

> [!TIP] 
> 추가 기능을 사용하지 않도록 설정할 때 Application Gateway를 삭제하고 싶지 않지만 두 기준을 모두 충족하는 경우 “created-by: ingress-appgw” 태그를 제거하여 추가 기능에서 Application Gateway를 삭제하지 않도록 합니다. 

AGIC 추가 기능을 사용하지 않도록 설정하려면 다음 명령을 실행합니다. 
```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a ingress-appgw 
```

## <a name="enable-agic-add-on-on-existing-application-gateway-and-aks-cluster"></a>기존 Application Gateway 및 AKS 클러스터에서 AGIC 추가 기능 사용
AGIC 추가 기능을 사용하지 않도록 설정했지만 이를 다시 사용하도록 설정하거나 기존 Application Gateway 및 AKS 클러스터를 사용하여 추가 기능을 사용하도록 설정하려는 경우 다음 명령을 실행합니다.

```azurecli-interactive
appgwId=$(az network application-gateway show -n <application-gateway-name> -g <resource-group-name> -o tsv --query "id") 
az aks enable-addons -n <AKS-cluster-name> -g <AKS-cluster-resource-group> -a ingress-appgw --appgw-id $appgwId
```

## <a name="next-steps"></a>다음 단계
기존 Application Gateway 및 AKS 클러스터를 사용하여 AGIC 추가 기능을 사용하도록 설정하는 방법에 대한 자세한 내용은 [AGIC 추가 기능 브라운필드 배포](tutorial-ingress-controller-add-on-existing.md)를 참조하세요.