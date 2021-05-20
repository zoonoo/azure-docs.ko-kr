---
title: CLI(azdata 또는 kubectl)를 사용하여 Azure Database for PostgreSQL 하이퍼스케일 서버 그룹 스케일 업 및 스케일 다운
description: CLI(azdata 또는 kubectl)를 사용하여 Azure Database for PostgreSQL 하이퍼스케일 서버 그룹 스케일 업 및 스케일 다운
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 4461fb6904d51ee8d740b633a2d0028658ac2ced
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101687552"
---
# <a name="scale-up-and-down-an-azure-database-for-postgresql-hyperscale-server-group-using-cli-azdata-or-kubectl"></a>CLI(azdata 또는 kubectl)를 사용하여 Azure Database for PostgreSQL 하이퍼스케일 서버 그룹 스케일 업 및 스케일 다운



서버 그룹의 특성 또는 정의를 변경해야 하는 경우가 있습니다. 예를 들어 다음과 같은 가치를 제공해야 합니다.

- 각 코디네이터 또는 작업자 노드가 사용하는 vCore의 수 스케일 업 또는 스케일 다운
- 각 코디네이터 또는 작업자 노드가 사용하는 메모리 스케일 업 또는 스케일 다운

이 가이드에서는 vCore 및/또는 메모리의 크기를 조정하는 방법을 설명합니다.

서버 그룹의 vCore 또는 메모리 설정을 스케일 업하거나 스케일 다운하면 각 vCore 및 메모리 설정에 대해 최소 및/또는 최대를 설정할 수 있습니다. 특정 수의 vCore 또는 특정 양의 메모리를 사용하도록 서버 그룹을 구성하려면 최대 설정에 해당하는 최소 설정을 설정합니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="show-the-current-definition-of-the-server-group"></a>서버 그룹의 현재 정의 표시

서버 그룹 현재 정의를 표시하고 현재 vCore 및 메모리 설정을 확인하려면 다음 명령 중 하나를 실행합니다.

### <a name="cli-with-azdata"></a>azdata를 사용하는 CLI

```console
azdata arc postgres server show -n <server group name>
```
### <a name="cli-with-kubectl"></a>kubectl을 사용하는 CLI

```console
kubectl describe postgresql-12/<server group name> [-n <namespace name>]
```
> [!NOTE]
> PostgreSQL 버전 11의 서버 그룹을 만든 경우 대신 `kubectl describe postgresql-11/<server group name>`을 실행합니다.

서버 그룹의 구성을 반환합니다. 기본 설정을 사용하여 서버 그룹을 만든 경우 다음과 같은 정의가 표시되어야 합니다.

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

서버 그룹 정의에서 노드당 최소/최대 vCore 및 노드당 최소/최대 메모리 설정을 전달하는 섹션은 **"일정"** 섹션입니다. 해당 섹션에서 최대 설정은 **"제한"** 이라는 하위 섹션에서 유지되고 최소 설정은 **"요청"** 이라는 하위 섹션에서 유지됩니다.

최대 설정과 다른 최소 설정을 설정하는 경우 필요한 경우 구성에서 서버 그룹에 요청된 리소스를 할당하는 것을 보장합니다. 설정한 한도를 초과하지 않습니다.

실제로 서버 그룹에서 사용되는 리소스(vCore 및 메모리)는 최대 설정에 따르며 클러스터에서 사용할 수 있는 워크로드 및 리소스에 따라 달라집니다. 설정을 최댓값으로 제한하지 않으면 서버 그룹은 Kubernetes 클러스터가 서버 그룹이 예약된 Kubernetes 노드에 할당하는 모든 리소스를 사용할 수 있습니다.

이러한 vCore 및 메모리 설정은 각 PostgreSQL 하이퍼스케일 노드(코디네이터 노드 및 작업자 노드)에 적용됩니다. 코디네이터 노드 및 작업자 노드의 정의를 별도로 설정하는 것은 아직 지원되지 않습니다.

기본 구성에서는 최소 메모리만 PostgreSQL 하이퍼스케일을 실행하는 데 권장되는 최소 메모리 크기이므로 256Mi로 설정됩니다.

> [!NOTE]
> 최솟값을 설정하는 것은 서버 그룹이 반드시 해당 최소를 사용하는 것을 의미하지는 않습니다. 즉, 서버 그룹에 필요한 경우 최소한 이 최소로 할당되도록 보장합니다. 예를 들어 `--minCpu 2`를 설정하는 것을 가정해 보겠습니다. 서버 그룹은 항상 최소 2개의 vCore를 사용하는 것을 의미하지는 않습니다. 대신 서버 그룹이 필요한 양이 많지 않은 경우 2개 미만의 vCore를 사용하여 시작할 수 있으며 이는 나중에 필요할 경우 최소 2개의 vCore를 할당하는 것을 보장합니다. 이는 Kubernetes 클러스터가 필요한 경우 서버 그룹에 2개의 vCore를 할당할 수 있도록 이러한 방법으로 다른 워크로드에 리소스를 할당하는 것을 의미합니다.

>[!NOTE]
>시스템 구성을 수정하기 전에 [여기](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)에서 Kubernetes 리소스 모델을 숙지해야 합니다.

## <a name="scale-up-the-server-group"></a>서버 그룹 스케일 업

설정하려는 설정은 Kubernetes 클러스터에 대해 설정한 구성 내에서 고려해야 합니다. Kubernetes 클러스터가 충족시킬 수 없는 값을 설정하지 않았는지 확인합니다. 오류가 발생하거나 예기치 않은 동작이 발생할 수 있습니다. 예를 들어 구성 변경 후 서버 그룹의 상태가 오랫동안 _업데이트_ 상태로 유지되는 경우 아래 매개 변수를 Kubernetes 클러스터가 충족하지 못하는 값으로 설정하는 것을 나타낼 수 있습니다. 이 경우 변경 내용을 되돌리거나 _troubleshooting_section을 읽습니다.

예를 들어 서버 그룹의 정의를 스케일 업하려는 경우를 가정해 보겠습니다.

- 최소 vCore = 2
- 최대 vCore = 4
- 최소 메모리 = 512Mb
- 최대 메모리 = 1Gb

다음 방법 중 하나를 사용합니다.

### <a name="cli-with-azdata"></a>azdata를 사용하는 CLI

```console
azdata arc postgres server edit -n <name of your server group> --cores-request <# core-request>  --cores-limit <# core-limit>  --memory-request <# memory-request>Mi  --memory-limit <# memory-limit>Mi
```

> [!CAUTION]
> 다음은 명령을 사용하는 방법을 보여 주도록 제공된 예제입니다. 편집 명령을 실행하기 전에 매개 변수를 Kubernetes 클러스터에서 사용할 수 있는 값으로 설정해야 합니다.

```console
azdata arc postgres server edit -n <name of your server group> --cores-request 2  --cores-limit 4  --memory-request 512Mi  --memory-limit 1024Mi
```

표시되면 명령이 성공적으로 실행됩니다.

```console
<name of your server group> is Ready
```

> [!NOTE]
> 이러한 매개 변수에 대한 자세한 내용은 `azdata arc postgres server edit --help`를 실행합니다.

### <a name="cli-with-kubectl"></a>kubectl을 사용하는 CLI

```console
kubectl edit postgresql-12/<server group name> [-n <namespace name>]
```

그러면 vi 편집기에서 구성을 탐색하고 변경할 수 있습니다. 다음을 사용하여 원하는 설정을 사양의 필드 이름에 매핑합니다.

> [!CAUTION]
> 다음은 구성을 편집하는 방법을 보여 주도록 제공된 예제입니다. 구성을 업데이트하기 전에 매개 변수를 Kubernetes 클러스터에서 사용할 수 있는 값으로 설정해야 합니다.

예를 들어 다음과 같은 가치를 제공해야 합니다.
- 최소 vCore = 2 -> scheduling\default\resources\requests\cpu
- 최대 vCore = 4 -> scheduling\default\resources\limits\cpu
- 최소 메모리 = 512Mb -> scheduling\default\resources\requests\cpu
- 최대 메모리 = 1Gb -> scheduling\default\resources\limits\cpu

vi 편집기에 익숙하지 않은 경우 [여기](https://www.computerhope.com/unix/uvi.htm)에서 필요할 수 있는 명령에 대한 설명을 참조하세요.
- 편집 모드: `i`
- 화살표를 사용하여 이동
- 편집 중지(_s): `esc`
- 저장하지 않고 종료(_e): `:qa!`
- 저장 후 종료(_e): `:qw!`


## <a name="show-the-scaled-up-definition-of-the-server-group"></a>서버 그룹의 스케일 업된 정의 표시

명령을 다시 실행하여 서버 그룹의 정의를 표시하고 원하는 대로 설정되었는지 확인합니다.

### <a name="cli-with-azdata"></a>azdata를 사용하는 CLI

```console
azdata arc postgres server show -n <the name of your server group>
```
### <a name="cli-with-kubectl"></a>kubectl을 사용하는 CLI

```console
kubectl describe postgresql-12/<server group name>  [-n <namespace name>]
```
> [!NOTE]
> PostgreSQL 버전 11의 서버 그룹을 만든 경우 대신 `kubectl describe postgresql-11/<server group name>`을 실행합니다.


서버 그룹의 새 정의가 표시됩니다.

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

## <a name="scale-down-the-server-group"></a>서버 그룹 스케일 다운

서버 그룹을 스케일 다운하려면 동일한 명령을 실행하지만 스케일 다운하려는 설정에 대해 더 낮은 값을 설정합니다. 요청 및/또는 제한을 제거하려면 해당 값을 빈 문자열로 지정합니다.

## <a name="reset-to-default-values"></a>기본 값으로 초기화
코어/메모리 제한/요청 매개 변수를 기본값으로 다시 설정하려면 해당 매개 변수를 편집하고 실제 값 대신 빈 문자열을 전달합니다. 예를 들어 코어 제한(cl) 매개 변수를 다시 설정하려는 경우 다음 명령을 실행합니다.
- Linux 클라이언트에서:

```console
    azdata arc postgres server edit -n <servergroup name> -cl ""
```

- Windows 클라이언트에서: 
 
```console
    azdata arc postgres server edit -n <servergroup name> -cl '""'
```


## <a name="next-steps"></a>다음 단계

- [Azure Database for PostgreSQL 하이퍼스케일 서버 그룹 스케일 아웃](scale-out-postgresql-hyperscale-server-group.md)
- [스토리지 구성 및 Kubernetes 스토리지 개념](storage-configuration.md)
- [Kubernetes 리소스 모델](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
