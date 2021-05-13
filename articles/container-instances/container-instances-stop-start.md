---
title: 수동으로 컨테이너 그룹 중지 또는 시작
description: Azure Container Instances에서 컨테이너 그룹을 수동으로 중지하거나 시작하는 방법을 알아봅니다.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 43498a7d31f0cb78751d2f318aede8523b7b9345
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787002"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Azure Container Instances에서 수동으로 컨테이너 중지 또는 시작

컨테이너 그룹의 [다시 시작 정책](container-instances-restart-policy.md) 설정은 기본적으로 컨테이너 인스턴스를 시작하거나 중지하는 방법을 결정합니다. 컨테이너 그룹을 수동으로 중지하거나 시작하여 기본 설정을 재정의할 수 있습니다.

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="stop"></a>Stop

예를 들어 [az container stop][az-container-stop] 명령 또는 Azure Portal을 사용하여 실행 중인 컨테이너 그룹을 수동으로 중지합니다. 특정 컨테이너 워크로드의 경우 정의된 기간 후 장기 컨테이너 그룹을 중지하여 비용을 절감하는 것이 좋습니다. 

컨테이너 그룹이 중지됨 상태가 되면 그룹의 모든 컨테이너를 종료하고 재순환합니다. 컨테이너 상태는 유지되지 않습니다.

컨테이너가 재순환되면 [리소스](container-instances-container-groups.md#resource-allocation) 할당이 취소되고 컨테이너 그룹 관련 청구가 중지됩니다.

컨테이너 그룹이 이미 종료된 경우(성공 또는 실패 상태임)에는 중지 작업이 적용되지 않습니다. 예를 들어 성공적으로 실행된 한 번 실행 컨테이너 작업이 포함된 컨테이너 그룹은 성공 상태로 종료됩니다. 해당 상태에서 그룹을 중지하려고 시도해도 상태는 변경되지 않습니다. 

## <a name="start"></a>시작

컨테이너 그룹이 자체적으로 종료되거나 사용자가 수동으로 그룹을 중지했기 때문에 컨테이너 그룹이 중지되면 컨테이너를 시작할 수 있습니다. 예를 들어 [az container start][az-container-start] 명령 또는 Azure Portal을 사용하여 그룹의 컨테이너를 수동으로 시작합니다. 컨테이너의 컨테이너 이미지가 업데이트되면 새 이미지가 풀됩니다. 

컨테이너 그룹을 시작하면 동일한 컨테이너 구성을 가진 새 배포가 시작됩니다. 이 작업은 예상대로 작동하는 알려진 컨테이너 그룹 구성을 빠르게 다시 사용하는 데 도움이 될 수 있습니다. 동일한 워크로드를 실행하기 위해 새 컨테이너 그룹을 만들 필요가 없습니다.

컨테이너 그룹의 모든 컨테이너는 이 작업으로 시작됩니다. 그룹의 특정 컨테이너를 시작할 수 없습니다.

컨테이너 그룹을 수동으로 시작 또는 다시 시작한 후에 컨테이너 그룹은 구성된 다시 시작 정책에 따라 실행됩니다.
  
## <a name="restart"></a>다시 시작

예를 들어 [az container restart][az-container-restart] 명령을 사용하여 실행되는 동안 컨테이너 그룹을 다시 시작할 수 있습니다. 이 작업은 컨테이너 그룹의 모든 컨테이너를 다시 시작합니다. 컨테이너의 컨테이너 이미지가 업데이트되면 새 이미지가 풀됩니다. 

컨테이너 그룹을 다시 시작하면 배포 문제를 해결하려는 경우 도움이 됩니다. 예를 들어 임시 리소스 제한 사항으로 인해 컨테이너가 성공적으로 실행되지 않는 경우 그룹을 다시 시작하면 문제가 해결될 수 있습니다.

컨테이너 그룹의 모든 컨테이너는 이 작업으로 다시 시작됩니다. 그룹의 특정 컨테이너를 다시 시작할 수 없습니다.

컨테이너 그룹을 수동으로 다시 시작하면 컨테이너 그룹이 구성된 다시 시작 정책에 따라 실행됩니다.

## <a name="next-steps"></a>다음 단계

Azure Container Instances의 [다시 시작 정책 설정](container-instances-restart-policy.md)에 대해 자세히 알아봅니다.

기존 구성을 사용하여 컨테이너 그룹을 수동으로 중지하고 시작하는 것 외에도 실행 중인 컨테이너 그룹의 [설정을 업데이트](container-instances-update.md)할 수 있습니다.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container#az_container_restart
[az-container-start]: /cli/azure/container#az_container_start
[az-container-stop]: /cli/azure/container#az_container_stop
