---
title: 컨테이너 그룹 업데이트
description: Azure Containers Instances 컨테이너 그룹에서 실행되는 컨테이너를 업데이트하는 방법을 알아봅니다.
ms.topic: article
ms.date: 04/17/2020
ms.openlocfilehash: dc10e38aa6be9473f89df6f7fa10a72f63d9ada8
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82854440"
---
# <a name="update-containers-in-azure-container-instances"></a>Azure Container Instances에서 컨테이너 업데이트

컨테이너 인스턴스를 정상적으로 작동 하는 동안 [컨테이너 그룹](container-instances-container-groups.md)에서 실행 중인 컨테이너를 업데이트 해야 하는 경우가 있을 수 있습니다. 예를 들어 이미지 버전, DNS 이름 또는 환경 변수와 같은 속성을 업데이트 하거나 응용 프로그램이 충돌 하는 컨테이너에서 속성을 새로 고칠 수 있습니다.

하나 이상의 수정 된 속성을 사용 하 여 기존 그룹을 다시 배포 하 여 실행 중인 컨테이너 그룹의 컨테이너를 업데이트 합니다. 컨테이너 그룹을 업데이트 하면 일반적으로 동일한 기본 컨테이너 호스트에서 그룹의 실행 중인 모든 컨테이너가 내부에서 다시 시작 됩니다.

> [!NOTE]
> 종료되거나 삭제된 컨테이너 그룹은 업데이트할 수 없습니다. 컨테이너 그룹이 종료 되었거나 (성공 또는 실패 상태) 삭제 된 경우 그룹을 새로 배포 해야 합니다. 기타 [제한 사항](#limitations)을 참조 하세요.

## <a name="update-a-container-group"></a>컨테이너 그룹 업데이트

기존 컨테이너 그룹을 업데이트 하려면:

* Create 명령을 실행 하거나 Azure Portal를 사용 하 고 기존 그룹의 이름을 지정 합니다. 
* 다시 배포할 때 업데이트를 지 원하는 그룹의 속성을 하나 이상 수정 하거나 추가 합니다. 특정 속성은 [업데이트를 지원 하지 않습니다](#properties-that-require-container-delete).
* 이전에 제공한 값을 사용 하 여 다른 속성을 설정 합니다. 속성의 값을 설정 하지 않으면 해당 값이 기본값으로 되돌아갑니다.

> [!TIP]
> [Yaml 파일](container-instances-container-groups.md#deployment) 은 컨테이너 그룹의 배포 구성을 유지 하는 데 도움이 되며 업데이트 된 그룹을 배포 하기 위한 시작점을 제공 합니다. 다른 방법을 사용 하 여 그룹을 만든 경우 [az container export][az-container-export]를 사용 하 여 구성을 yaml로 내보낼 수 있습니다. 

### <a name="example"></a>예

다음 Azure CLI 예제에서는 컨테이너 그룹을 새 DNS 이름 레이블로 업데이트합니다. 그룹의 DNS 이름 레이블 속성은 업데이트할 수 있는 속성 이므로 컨테이너 그룹을 다시 배포 하 고 컨테이너를 다시 시작 합니다.

*myapplication-staging* DNS 이름 레이블을 사용한 초기 배포는 다음과 같습니다.

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

새 DNS 이름 레이블 *myapplication*으로 컨테이너 그룹을 업데이트 하 고 나머지 속성을 이전에 사용한 값으로 설정 합니다.

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>업데이트 혜택

기존 컨테이너 그룹을 업데이트하는 주요 이점은 배포 속도가 더 빠르다는 것입니다. 기존 컨테이너 그룹을 다시 배포하면 이전 배포에서 캐시된 계층에서 해당 컨테이너 이미지 계층을 가져옵니다. 새 배포를 통해 수행되는 것처럼 레지스트리에서 모든 이미지 계층을 새로 가져오는 대신 수정된 계층(있는 경우)만 가져옵니다.

Windows Server Core와 같이 더 큰 컨테이너 이미지를 기반으로 하는 애플리케이션에서 삭제하여 새로 배포하는 대신 업데이트하는 경우 배포 속도가 크게 향상될 수 있습니다.

## <a name="limitations"></a>제한 사항

* 컨테이너 그룹의 일부 속성만 업데이트를 지원합니다. 컨테이너 그룹의 일부 속성을 변경하려면 먼저 해당 그룹을 삭제한 다음, 다시 배포해야 합니다. [컨테이너 삭제를 필요로 하는 속성을](#properties-that-require-container-delete)참조 하세요.
* 컨테이너 그룹을 업데이트하면 컨테이너 그룹의 모든 컨테이너가 다시 시작됩니다. 다중 컨테이너 그룹에 속한 특정 컨테이너에 대한 업데이트 또는 적절한 재시작은 수행할 수 없습니다.
* 컨테이너 그룹의 IP 주소는 일반적으로 업데이트 간에 유지 되지만 동일 하 게 유지 되는 것은 아닙니다. 컨테이너 그룹이 동일한 기본 호스트에 배포되는 한 컨테이너 그룹에서 해당 IP 주소를 유지합니다. 드문 경우 지만 다른 호스트로 재배포를 유발할 수 있는 몇 가지 Azure 내부 이벤트가 있습니다. 이 문제를 완화 하려면 컨테이너 인스턴스에 DNS 이름 레이블을 사용 하는 것이 좋습니다.
* 종료되거나 삭제된 컨테이너 그룹은 업데이트할 수 없습니다. 컨테이너 그룹을 중지 하거나 ( *종료* 됨 상태에 있는 경우) 그룹을 삭제 하면 새 그룹으로 배포 됩니다.

## <a name="properties-that-require-container-delete"></a>컨테이너를 삭제해야 하는 속성

모든 컨테이너 그룹 속성을 업데이트할 수 있는 것은 아닙니다. 예를 들어 컨테이너의 다시 시작 정책을 변경 하려면 먼저 컨테이너 그룹을 삭제 한 다음 다시 만들어야 합니다.

이러한 속성을 변경 하려면 다시 배포 하기 전에 컨테이너 그룹을 삭제 해야 합니다.

* OS 유형
* CPU, 메모리 또는 GPU 리소스
* 다시 시작 정책
* 네트워크 프로필

컨테이너 그룹을 삭제하고 다시 만들면 해당 그룹은 "다시 배포"되지 않고 새로 만들어집니다. 모든 이미지 계층은 이전 배포에서 캐시된 계층이 아니라 레지스트리에서 새로 가져옵니다. 컨테이너의 IP 주소도 다른 기본 호스트에 배포되어 변경될 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 **컨테이너 그룹**이 여러 번 언급되었습니다. Azure Container Instances의 모든 컨테이너는 컨테이너 그룹에 배포되고, 이 컨테이너 그룹에는 둘 이상의 컨테이너가 포함될 수 있습니다.

[Azure Container Instances의 컨테이너 그룹](container-instances-container-groups.md)

[다중 컨테이너 그룹 배포](container-instances-multi-container-group.md)

[Azure Container Instances에서 수동으로 컨테이너 중지 또는 시작](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[azure-cli-install]: /cli/azure/install-azure-cli
[az-container-export]: /cli/azure/container#az-container-export
