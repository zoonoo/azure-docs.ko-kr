---
title: AKS (Azure Kubernetes Service) 클러스터에 대해 계획 된 유지 관리 사용 (미리 보기)
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes 서비스 (AKS)에서 계획 된 유지 관리를 사용 하는 방법에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.author: qpetraroia
author: qpetraroia
ms.openlocfilehash: 8526d7c1c436074fbf6f838caf232e1abee06339
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670378"
---
# <a name="use-planned-maintenance-to-schedule-maintenance-windows-for-your-azure-kubernetes-service-aks-cluster-preview"></a>계획 된 유지 관리를 사용 하 여 AKS (Azure Kubernetes Service) 클러스터 (미리 보기)에 대 한 유지 관리 기간 예약

AKS 클러스터는 자동으로 정기적으로 유지 관리를 수행 합니다. 기본적으로이 작업은 언제 든 지 발생할 수 있습니다. 계획 된 유지 관리를 사용 하면 VMSS 인스턴스에서 kube 시스템 Pod 뿐만 아니라 제어 평면을 업데이트 하 고 워크 로드의 영향을 최소화 하는 주간 유지 관리 기간을 예약할 수 있습니다. 예약 되 면 선택한 기간 동안 모든 유지 관리가 수행 됩니다. 특정 날짜에 날짜 또는 시간 범위를 지정 하 여 클러스터에서 하나 이상의 주 창을 예약할 수 있습니다. 유지 관리 기간은 Azure CLI를 사용 하 여 구성 됩니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="limitations"></a>제한 사항

계획 된 유지 관리를 사용 하는 경우 다음 제한 사항이 적용 됩니다.

- AKS은 긴급 또는 중요 한 계획 되지 않은/사후 유지 관리 작업에 대해 이러한 창을 중단할 수 있는 권한을 보유 합니다.
- 현재 유지 관리 작업을 수행 하는 작업은 *가장 적합* 한 것으로 간주 되며 지정 된 기간 내에 발생 하는 것이 보장 되지 않습니다.
- 7 일 넘게 업데이트를 차단할 수 없습니다.

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 확장 설치

*Aks-preview* Azure CLI 확장 버전 0.5.4 이상이 필요 합니다. [Az extension add][az-extension-add] 명령을 사용하여 *aks-preview* Azure CLI 확장을 설치 합니다. 또는 [az extension update][az-extension-update] 명령을 사용하여 사용 가능한 업데이트를 설치 합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="allow-maintenance-on-every-monday-at-100am-to-200am"></a>매주 월요일 오전 1 시에 유지 관리를 허용 합니다. 2:00am

유지 관리 기간을 추가 하려면 명령을 사용 하면 `az aks maintenanceconfiguration add` 됩니다.

> [!IMPORTANT]
> 계획 된 유지 관리 기간은 UTC (협정 세계시)로 지정 됩니다.

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

다음 예제 출력에서는 1:00am에서 2:00am 마다 월요일에 유지 관리 기간을 보여 줍니다.

```json
{- Finished ..
  "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
  "name": "default",
  "notAllowedTime": null,
  "resourceGroup": "MyResourceGroup",
  "systemData": null,
  "timeInWeek": [
    {
      "day": "Monday",
      "hourSlots": [
        1
      ]
    }
  ],
  "type": null
}
```

하루 중에 언제 든 지 유지 관리를 허용 하려면 *시작 시간* 매개 변수를 생략 합니다. 예를 들어 다음 명령은 월요일 마다 전체 요일에 대 한 유지 관리 기간을 설정 합니다.

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday
```

## <a name="add-a-maintenance-configuration-with-a-json-file"></a>JSON 파일을 사용 하 여 유지 관리 구성 추가

또한 매개 변수를 사용 하는 대신 JSON 파일을 사용 하 여 유지 관리 기간을 만들 수 있습니다. `test.json`다음 내용으로 파일을 만듭니다.

```json
  {
        "timeInWeek": [
          {
            "day": "Tuesday",
            "hour_slots": [
              1,
              2
            ]
          },
          {
            "day": "Wednesday",
            "hour_slots": [
              1,
              6
            ]
          }
        ],
        "notAllowedTime": [
          {
            "start": "2021-05-26T03:00:00Z",
            "end": "2021-05-30T12:00:00Z"
          }
        ]
}
```

위의 JSON 파일은 매주 화요일, 오전 1 시-3:00am 및 모든 수요일에 오전 1 시-2:00am 및 6:00am-7:00am에 유지 관리 기간을 지정 합니다. *2021-05-26T03:00:00z* 에서 *2021-30t12:00:00z* 로의 예외도 있습니다. 유지 관리 기간이 겹치면 유지 관리가 허용 되지 않습니다. 다음 명령은에서 유지 관리 기간을 추가 합니다 `test.json` .

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --config-file ./test.json
```

## <a name="update-an-existing-maintenance-window"></a>기존 유지 관리 기간 업데이트

기존 유지 관리 구성을 업데이트 하려면 `az aks maintenanceconfiguration update` 명령을 사용 합니다.

```azurecli-interactive
az aks maintenanceconfiguration update -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

## <a name="list-all-maintenance-windows-in-an-existing-cluster"></a>기존 클러스터의 모든 유지 관리 기간을 나열 합니다.

AKS 클러스터에서 현재 유지 관리 구성 창을 모두 보려면 `az aks maintenanceconfiguration list` 명령을 사용 합니다.

```azurecli-interactive
az aks maintenanceconfiguration list -g MyResourceGroup --cluster-name myAKSCluster
```

아래 출력에서는 myAKSCluster에 대해 구성 된 유지 관리 기간이 두 개 있음을 볼 수 있습니다. 한 창은 월요일 오전 1 시에, 다른 하나는 금요일 오전 4 시 (오전 4 시)에 있습니다.

```json
[
  {
    "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
    "name": "default",
    "notAllowedTime": null,
    "resourceGroup": "MyResourceGroup",
    "systemData": null,
    "timeInWeek": [
      {
        "day": "Monday",
        "hourSlots": [
          1
        ]
      }
    ],
    "type": null
  },
  {
    "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/testConfiguration",
    "name": "testConfiguration",
    "notAllowedTime": null,
    "resourceGroup": "MyResourceGroup",
    "systemData": null,
    "timeInWeek": [
      {
        "day": "Friday",
        "hourSlots": [
          4
        ]
      }
    ],
    "type": null
  }
]
```

## <a name="show-a-specific-maintenance-configuration-window-in-an-aks-cluster"></a>AKS 클러스터에 특정 유지 관리 구성 창 표시

AKS 클러스터에서 특정 유지 관리 구성 창을 보려면 `az aks maintenanceconfiguration show` 명령을 사용 합니다.

```azurecli-interactive
az aks maintenanceconfiguration show -g MyResourceGroup --cluster-name myAKSCluster --name default
```

다음 예제 출력에서는 *기본값* 에 대 한 유지 관리 기간을 보여 줍니다.

```json
{
  "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
  "name": "default",
  "notAllowedTime": null,
  "resourceGroup": "MyResourceGroup",
  "systemData": null,
  "timeInWeek": [
    {
      "day": "Monday",
      "hourSlots": [
        1
      ]
    }
  ],
  "type": null
}
```

## <a name="delete-a-certain-maintenance-configuration-window-in-an-existing-aks-cluster"></a>기존 AKS 클러스터에서 특정 유지 관리 구성 창 삭제

AKS 클러스터에서 특정 유지 관리 구성 창을 삭제 하려면 `az aks maintenanceconfiguration delete` 명령을 사용 합니다.

```azurecli-interactive
az aks maintenanceconfiguration delete -g MyResourceGroup --cluster-name myAKSCluster --name default
```

## <a name="next-steps"></a>다음 단계

- AKS 클러스터 업그레이드를 시작 하려면 [AKS 클러스터 업그레이드][aks-upgrade] 를 참조 하세요.


<!-- LINKS - Internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli&preserve-view=true
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[aks-upgrade]: upgrade-cluster.md
