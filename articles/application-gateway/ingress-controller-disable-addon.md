---
title: Azure Kubernetes Service 클러스터에 대 한 Application Gateway 수신 컨트롤러 추가 기능을 사용 하지 않도록 설정 하 고 다시 사용 하도록 설정 합니다.
description: 이 문서에서는 AKS 클러스터용 AGIC 추가 기능을 사용 하지 않도록 설정 하 고 다시 사용 하도록 설정 하는 방법에 대 한 정보를 제공 합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: fe4da0435731c536a723cb2cb43428166456360b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84807947"
---
# <a name="disable-and-re-enable-agic-add-on-for-your-aks-cluster"></a>AKS 클러스터에 대 한 AGIC 추가 기능을 사용 하지 않도록 설정 하 고 다시 사용 하도록 설정 합니다.
AKS 추가 기능으로 배포 된 AGIC (Application Gateway 수신 컨트롤러)를 사용 하면 Azure CLI에서 한 줄로 추가 기능을 사용 하거나 사용 하지 않도록 설정할 수 있습니다. AGIC 추가 기능을 사용 하지 않도록 설정 하는 경우에는 AGIC 추가 기능을 사용 하 여 Application Gateway를 만들었는지 또는 AGIC 추가 기능에서 별도로 배포 했는지에 따라 Application Gateway 수명 주기가 달라 집니다. 동일한 명령을 실행 하 여 AGIC 추가 기능을 사용 하지 않도록 설정 하거나 기존 AKS 클러스터를 사용 하 여 AGIC 추가 기능을 사용 하도록 설정 하 고 Application Gateway 수 있습니다.

## <a name="disabling-agic-add-on-with-associated-application-gateway"></a>연결 된 Application Gateway에서 AGIC 추가 기능을 사용 하지 않도록 설정 
AGIC 추가 기능에서 먼저 모든 항목을 설정 하는 경우 Application Gateway 자동으로 배포 된 경우 AGIC 추가 기능을 사용 하지 않도록 설정 하면 기본적으로 두 가지 기준에 따라 Application Gateway 삭제 됩니다. AGIC 추가 기능을 사용 하지 않도록 설정 하면 연결 된 Application Gateway를 삭제 해야 하는지 확인 하기 위해 다음과 같은 두 가지 조건이 있습니다.
- AGIC 추가 기능이 MC_ * 노드 리소스 그룹에 배포 된에 연결 된 Application Gateway 입니까? 
- AGIC 추가 기능에 연결 된 Application Gateway에 "만든 사람: 수신-apim-appgw-rg" 태그가 있나요? 태그는 AGIC에서 추가 기능을 통해 Application Gateway 배포 되었는지 여부를 확인 하는 데 사용 됩니다. 

두 조건이 모두 충족 되는 경우 추가 기능을 사용 하지 않도록 설정 하면 AGIC 추가 기능에서 만든 Application Gateway 삭제 됩니다. 그러나 Application Gateway를 사용 하 여 배포 된 공용 IP 또는 서브넷은 삭제 되지 않습니다. 첫 번째 조건이 충족 되지 않는 경우 Application Gateway에 "만든 사람: 수신-apim-appgw-rg" 태그를 사용 하지 않도록 설정 하면 해당 추가 기능을 사용 하지 않도록 설정 해도 Application Gateway 삭제 되지 않습니다. 마찬가지로 두 번째 조건이 충족 되지 않는 경우, 예를 들어 Application Gateway에 해당 태그가 없는 경우 추가 기능을 사용 하지 않도록 설정 하면 MC_ * 노드 리소스 그룹의 Application Gateway 삭제 되지 않습니다. 

> [!TIP] 
> 추가 기능을 사용 하지 않도록 설정할 때 Application Gateway 삭제 하지 않고 두 기준을 모두 충족 하는 경우 "만든 사람: 수신-apim-appgw-rg" 태그를 제거 하 여 추가 기능에서 Application Gateway를 삭제 하지 않도록 합니다. 

AGIC 추가 기능을 사용 하지 않도록 설정 하려면 다음 명령을 실행 합니다. 
```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a ingress-appgw 
```

## <a name="enable-agic-add-on-on-existing-application-gateway-and-aks-cluster"></a>기존 Application Gateway 및 AKS 클러스터에서 AGIC 추가 기능 사용
AGIC 추가 기능을 사용 하지 않도록 설정 하 고 추가 기능을 다시 사용 하도록 설정 하거나 기존 Application Gateway 및 AKS 클러스터를 사용 하 여 추가 기능을 사용 하도록 설정 하려는 경우 다음 명령을 실행 합니다.

```azurecli-interactive
appgwId=$(az network application-gateway show -n <application-gateway-name> -g <resource-group-name> -o tsv --query "id") 
az aks enable-addons -n <AKS-cluster-name> -g <AKS-cluster-resource-group> -a ingress-appgw --appgw-id $appgwId
```

## <a name="next-steps"></a>다음 단계
기존 Application Gateway 및 AKS 클러스터를 사용 하 여 AGIC 추가 기능을 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [AGIC brownfield 배포](tutorial-ingress-controller-add-on-existing.md)를 참조 하세요.