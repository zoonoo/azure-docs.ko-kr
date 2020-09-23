---
title: CLI (azdata 또는 kubectl)를 사용 하 여 Azure Database for PostgreSQL 하이퍼 확장 서버 그룹 확장 및 축소
description: CLI (azdata 또는 kubectl)를 사용 하 여 Azure Database for PostgreSQL 하이퍼 확장 서버 그룹 확장 및 축소
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: b96f38d04fe3e3cb59fa75424ae588fe0e38f510
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938074"
---
# <a name="scale-up-and-down-an-azure-database-for-postgresql-hyperscale-server-group-using-cli-azdata-or-kubectl"></a>CLI (azdata 또는 kubectl)를 사용 하 여 Azure Database for PostgreSQL 하이퍼 확장 서버 그룹 확장 및 축소



서버 그룹의 특성이 나 정의를 변경 해야 하는 경우가 있습니다. 다음은 그 예입니다. 

- 각 코디네이터 또는 작업자 노드에 사용 되는 vCores 수를 늘리거나 줄일 수 있습니다.
- 각 코디네이터 또는 작업자 노드에서 사용 하는 메모리를 확장 하거나 축소 합니다.

이 가이드에서는 vCore 및/또는 메모리 크기를 조정 하는 방법을 설명 합니다.

서버 그룹의 vCore 또는 메모리 설정을 확장 하거나 축소 하면 각 vCore 및 메모리 설정에 대해 최소 및/또는 최대를 설정할 수 있습니다. 특정 개수의 vCore 또는 특정 양의 메모리를 사용 하도록 서버 그룹을 구성 하려면 최소 설정에 해당 하는 최소 설정을 설정 합니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="show-the-current-definition-of-the-server-group"></a>서버 그룹의 현재 정의를 표시 합니다.

현재 서버 그룹 정의를 표시 하 고 현재 vCore 및 메모리 설정을 확인 하려면 다음 명령 중 하나를 실행 합니다.

### <a name="cli-with-azdata"></a>Azdata를 사용 하는 CLI

```console
azdata arc postgres server show -n <server group name>
```
### <a name="cli-with-kubectl"></a>Kubectl를 사용 하는 CLI

```console
kubectl describe postgresql-12/<server group name> [-n <namespace name>]
```
> [!NOTE]
> PostgreSQL 버전 11의 서버 그룹을 만든 경우 대신를 실행 `kubectl describe postgresql-11/<server group name>` 합니다.

서버 그룹의 구성을 반환 합니다. 기본 설정을 사용 하 여 서버 그룹을 만든 경우 다음과 같은 정의가 표시 되어야 합니다.

```console
"scheduling": {
      "default": {
        "resources": {
          "requests": {
            "memory": "256Mi"
          }
        }
      }
    },
```

## <a name="interpret-the-definition-of-the-server-group"></a>서버 그룹의 정의 해석

서버 그룹 정의에서 노드당 최소/최대 vCore 및 노드당 최소/최대 메모리 설정을 전달 하는 섹션은 **"일정"** 섹션입니다. 해당 섹션에서 max 설정은 **"제한"** 이라는 하위 섹션에서 지속 되 고 최소 설정은 **"요청"** 이라는 하위 섹션에서 유지 됩니다.

최대 설정과 다른 최소 설정을 설정 하는 경우 필요한 경우 구성에서 서버 그룹에 요청 된 리소스를 할당 하는 것을 보장 합니다. 설정한 한도를 초과 하지 않습니다.

실제로 서버 그룹에서 사용 되는 리소스 (vCores 및 메모리)는 최대 설정 이며 클러스터에서 사용할 수 있는 워크 로드 및 리소스에 따라 달라 집니다. 설정의 일부를 최대로 설정 하지 않으면 서버 그룹은 Kubernetes 클러스터가 서버 그룹이 예약 된 Kubernetes 노드에 할당 하는 모든 리소스를 사용할 수 있습니다.

이러한 vCore 및 메모리 설정은 각 PostgreSQL Hyperscale 노드 (코디네이터 노드 및 작업자 노드)에 적용 됩니다. 코디네이터 노드 및 작업자 노드의 정의를 별도로 설정 하는 것은 아직 지원 되지 않습니다.

기본 구성에서는 최소 메모리만 PostgreSQL Hyperscale을 실행 하는 데 권장 되는 최소 메모리 크기 이므로 256Mi로 설정 됩니다.

> [!NOTE]
> 최소값을 설정 하는 것은 서버 그룹이 반드시 해당 최소를 사용 하는 것을 의미 하지는 않습니다. 즉, 서버 그룹에 필요한 경우 최소한이 최소의 할당을 보장 합니다. 예를 들어를 설정 하는 것을 가정해 보겠습니다 `--minCpu 2` . 서버 그룹은 항상 2 개 이상의 vCores를 사용 하는 것을 의미 하지는 않습니다. 대신, 서버 그룹은 2 개 미만의 vCores를 사용 하 여 시작할 수 있습니다 .이는 나중에 필요할 경우 2 개 이상의 vcores를 할당 하는 것을 보장 합니다. Kubernetes 클러스터가 다른 작업에 리소스를 할당 하 여 필요한 경우 서버 그룹에 2 개 vCores를 할당할 수 있음을 의미 합니다.

>[!NOTE]
>시스템 구성을 수정 하기 전에 [여기](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities) 에서 Kubernetes 리소스 모델을 숙지 해야 합니다.

## <a name="scale-up-the-server-group"></a>서버 그룹 확장

설정 하려는 설정은 Kubernetes 클러스터에 대해 설정한 구성 내에서 고려해 야 합니다. Kubernetes 클러스터가 충족 하지 못하는 값을 설정 하지 않았는지 확인 합니다. 오류가 발생 하거나 예기치 않은 동작이 발생할 수 있습니다. 예를 들어 구성 변경 후 서버 그룹의 상태가 오랫동안 _업데이트_ 상태로 유지 되는 경우 아래 매개 변수를 Kubernetes 클러스터가 충족 하지 못하는 값으로 설정 하는 것을 나타낼 수 있습니다. 이 경우 변경 내용을 되돌리거나 _troubleshooting_section을 읽습니다.

서버 그룹의 정의를로 확장 하려는 경우를 가정해 보겠습니다.

- 최소 vCore = 2
- 최대 vCore = 4
- 최소 메모리 = 512Mb
- 최대 메모리 = 1Gb

다음 방법 중 하나를 사용 합니다.

### <a name="cli-with-azdata"></a>Azdata를 사용 하는 CLI

```console
azdata arc postgres server edit -n <name of your server group> --cores-request 2  --cores-limit 4  --memory-request 512Mi  --memory-limit 1024Mi
```

명령은 다음과 같이 표시 될 때 성공적으로 실행 됩니다.

```console
<name of your server group> is Ready
```

> [!NOTE]
> 이러한 매개 변수에 대 한 자세한 내용을 보려면를 실행 `azdata arc postgres server edit --help` 하세요.

### <a name="cli-with-kubectl"></a>Kubectl를 사용 하는 CLI

```console
kubectl edit postgresql-12/<server group name> [-n <namespace name>]
```

그러면 vi 편집기에서 구성을 탐색 하 고 변경할 수 있습니다. 다음을 사용 하 여 원하는 설정을 사양의 필드 이름에 매핑합니다.

- 최소 vCore = 2-> scheduling\default\resources\requests\cpu
- 최대 vCore = 4-> scheduling\default\resources\limits\cpu
- 최소 메모리 = 512Mb-> 예약 \ 기본 \ 리소스
- 최대 메모리 = 1Gb > scheduling\default\resources\limits\cpu

Vi 편집기에 익숙하지 않은 경우 [여기](https://www.computerhope.com/unix/uvi.htm)에 필요할 수 있는 명령에 대 한 설명을 참조 하세요.
- 편집 모드: `i`
- 화살표를 사용 하 여 이동
- 편집 _stop: `esc`
- 저장 하지 않고 _exit: `:qa!`
- 저장 후 _exit: `:qw!`


## <a name="show-the-scaled-up-definition-of-the-server-group"></a>서버 그룹의 확장 된 정의 표시

명령을 다시 실행 하 여 서버 그룹의 정의를 표시 하 고 원하는 대로 설정 되었는지 확인 합니다.

### <a name="cli-with-azdata"></a>Azdata를 사용 하는 CLI

```console
azdata arc postgres server show -n <the name of your server group>
```
### <a name="cli-with-kubectl"></a>Kubectl를 사용 하는 CLI

```console
kubectl describe postgresql-12/<server group name>  [-n <namespace name>]
```
> [!NOTE]
> PostgreSQL 버전 11의 서버 그룹을 만든 경우 대신를 실행 `kubectl describe postgresql-11/<server group name>` 합니다.


서버 그룹의 새 정의가 표시 됩니다.

```console
"scheduling": {
      "default": {
        "resources": {
          "limits": {
            "cpu": "4",
            "memory": "1024Mi"
          },
          "requests": {
            "cpu": "2",
            "memory": "512Mi"
          }
        }
      }
    },
```

## <a name="scale-down-the-server-group"></a>서버 그룹 축소

서버 그룹을 축소 하려면 동일한 명령을 실행 하지만 규모를 축소 하려는 설정에 대해 더 낮은 값을 설정 합니다. 요청 및/또는 제한을 제거 하려면 해당 값을 빈 문자열로 지정 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Database for PostgreSQL 하이퍼 확장 서버 그룹 확장](scale-out-postgresql-hyperscale-server-group.md)
- [저장소 구성 및 Kubernetes 저장소 개념](storage-configuration.md)
- [영구적 볼륨 클레임 확장](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Kubernetes 리소스 모델](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
