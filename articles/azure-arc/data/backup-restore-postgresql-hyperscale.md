---
title: Azure Database for PostgreSQL 하이퍼스케일 서버 그룹에 대한 백업 및 복원
description: Azure Database for PostgreSQL 하이퍼스케일 서버 그룹에 대한 백업 및 복원
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: dde4db7f3eb476b7645e910504e48fea8bb6df0c
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569707"
---
# <a name="backup-and-restore-for-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Azure Arc 사용 PostgreSQL Hyperscale 서버 그룹에 대 한 백업 및 복원

Azure Arc enabled PostgreSQL Hyperscale 서버 그룹의 전체 백업/복원을 수행할 수 있습니다. 이렇게 하면 Azure Arc enabled PostgreSQL Hyperscale 서버 그룹의 모든 노드에 있는 전체 데이터베이스 집합이 백업 되 고/또는 복원 됩니다.
백업을 수행 하 고 복원 하려면 서버 그룹에 대해 백업 저장소 클래스가 구성 되어 있는지 확인 해야 합니다. 지금은 서버 그룹을 만들 때 백업 저장소 클래스를 지정 해야 합니다. 아직 서버 그룹을 만든 후에는 백업 저장소 클래스를 사용 하도록 서버 그룹을 구성할 수 없습니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

> [!CAUTION]
> Preview는 Postgres 엔진의 버전 11에 대 한 백업/복원을 지원 하지 않습니다. Postgres 버전 12에 대 한 백업/복원만 지원 합니다.

## <a name="verify-configuration"></a>구성 확인

먼저 기존 서버 그룹이 backup 저장소 클래스를 사용 하도록 구성 되어 있는지 확인 합니다.

서버 그룹의 이름을 설정한 후 다음 명령을 실행 합니다.
```console
 azdata arc postgres server show -n postgres01
```
출력의 저장소 섹션을 살펴봅니다.
```console
...
"storage": {
      "backups": {
        "className": "local-storage"
      },
      "data": {
        "className": "local-storage",
        "size": "5Gi"
      },
      "logs": {
        "className": "local-storage",
        "size": "5Gi"
      }
    }
...
```
해당 명령의 출력에서 "백업" 섹션에 표시 된 저장소 클래스의 이름이 표시 되는 경우이는 서버 그룹이 백업 저장소 클래스를 사용 하도록 구성 되었으며 백업을 수행 하 고 복원을 수행할 준비가 되었음을 의미 합니다. "백업" 섹션이 표시 되지 않으면 서버 그룹을 삭제 하 고 다시 만들어 백업 저장소 클래스를 구성 해야 합니다. 이 시점에서 서버 그룹을 만든 후에는 아직 백업 저장소 클래스를 구성할 수 없습니다.

>[!IMPORTANT]
>서버 그룹이 이미 백업 저장소 클래스를 사용 하도록 구성 된 경우 다음 단계를 건너뛰고 "수동 전체 백업 수행" 단계로 바로 이동 합니다.

## <a name="create-a-server-group"></a>서버 그룹 만들기 

다음으로, 백업/복원을 위해 구성 된 서버 그룹을 만듭니다.

백업을 가져와서 복원 하려면 저장소 클래스를 사용 하 여 구성 된 서버를 만들어야 합니다.

Kubernetes 클러스터에서 사용할 수 있는 저장소 클래스 목록을 가져오려면 다음 명령을 실행 합니다.

```console
kubectl get sc
```

<!--The general format of create server group command is documented [here](create-postgresql-instances.md)-->

```console
azdata arc postgres server create -n <name> --workers 2 --storage-class-backups <storage class name> [--storage-class-data <storage class name>] [--storage-class-logs <storage class name>]
```

예를 들어 kubeadm을 기반으로 하는 간단한 환경을 만든 경우 다음을 수행 합니다.
```console
azdata arc postgres server create -n postgres01 --workers 2 --storage-class-backups local-storage
```

## <a name="take-manual-full-backup"></a>수동 전체 백업 수행

다음으로, 수동 전체 백업을 수행 합니다.

서버 그룹의 전체 데이터 및 로그 폴더에 대 한 전체 백업을 수행 하려면 다음 명령을 실행 합니다.

```console
azdata arc postgres backup create [--name <backup name>] --server-name <server group name> [--no-wait] 
```
위치:
- __이름__ 백업 이름을 나타냅니다.
- __서버 이름__ 은 서버 그룹을 나타냅니다.
- __대기 안 함__ 은 명령줄에서이 명령줄 창을 계속 사용할 수 있도록 백업이 완료 될 때까지 기다리지 않음을 나타냅니다.

>**참고**: 복원에 사용할 수 있는 백업을 나열 하는 데 사용할 수 있는 명령은 아직 백업이 수행 된 날짜/시간을 표시 하지 않습니다. 즉,--name 매개 변수를 사용 하 여 날짜/시간 정보를 포함 하는 백업에 이름을 지정 하는 것이 좋습니다.

이 명령은 Azure Arc enabled PostgreSQL Hyperscale 서버 그룹을 구성 하는 모든 노드에서 분산 된 전체 백업을 조정 합니다. 즉, 코디네이터 및 작업자 노드의 모든 데이터를 백업 합니다.

다음은 그 예입니다.
```console
azdata arc postgres backup create --name MyBackup_Aug31_0730amPST --server-name postgres01
```

백업이 완료 되 면 백업 ID, 이름 및 상태가 반환 됩니다. 다음은 그 예입니다.
```console
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "name": "MyBackup_Aug31_0730amPS",
  "state": "Done"
}
```

> [!NOTE]
> 아직 다음 작업을 수행할 수 없습니다.
> - 자동 백업 예약
> - 수행 중인 백업 진행률 표시

## <a name="list-backups"></a>백업 나열

복원에 사용할 수 있는 백업을 나열 합니다.

복원에 사용할 수 있는 백업을 나열 하려면 다음 명령을 실행 합니다.

```console
azdata arc postgres backup list --server-name <servergroup name>
```

다음은 그 예입니다.
```console
azdata arc postgres backup list --server-name postgres01
```

다음과 같은 출력을 반환 합니다.
```console
ID                                Name                      State    Timestamp
--------------------------------  ------------------------  -------  ------------------------------
d134f51aa87f4044b5fb07cf95cf797f  MyBackup_Aug31_0730amPST  Done     2020-08-31 14:30:00:00+00:00
```

타임 스탬프는 백업이 수행 된 시점을 UTC로 나타냅니다.

## <a name="restore-a-backup"></a>백업 복원

전체 서버 그룹의 백업을 복원 하려면 다음 명령을 실행 합니다.

```console
azdata arc postgres backup restore --server-name <server group name> --backup-id <backup id>
```

위치:
- __백업 id__ 는 백업 나열 명령에 표시 된 백업의 id입니다 (3 단계 참조).
Azure Arc enabled PostgreSQL Hyperscale 서버 그룹을 구성 하는 모든 노드에서 분산 된 전체 복원을 조정 합니다. 즉, 코디네이터와 작업자 노드의 모든 데이터를 복원 합니다.

다음은 그 예입니다.
```console
azdata arc postgres backup restore --server-name postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```

복원 작업이 완료 되 면 명령줄에 다음과 같은 출력이 반환 됩니다.
```console
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "state": "Done"
}
```
> [!NOTE]
> 아직 다음 작업을 수행할 수 없습니다.
> - 이름을 표시 하 여 백업을 복원 합니다.
> - 다른 이름이 나 다른 서버 그룹에 있는 서버 그룹 복원
> - 복원 작업의 진행 상태를 표시 합니다.

## <a name="delete-backups"></a>백업 삭제
미리 보기에서는 백업 보존 기간을 설정할 수 없습니다. 그러나 필요 하지 않은 백업은 수동으로 삭제할 수 있습니다.
백업을 삭제 하는 일반 명령은 다음과 같습니다.
```console
azdata arc postgres backup delete  [--server-name, -sn] {[--name, -n], -id}
```
여기서
- `--server-name` 사용자가 백업을 삭제 하려는 서버 그룹의 이름입니다.
- `--name` 삭제할 백업의 이름입니다.
- `-id`삭제할 백업의 ID입니다.

> [!NOTE]
> `--name` 및 `-id` 는 함께 사용할 수 없습니다.

이전 단락에서 설명한 대로 백업 나열 명령을 실행 하 여 백업의 이름 및 ID를 검색할 수 있습니다.

예를 들어 다음과 같은 백업이 나열 되어 있다고 가정 합니다.
```console
azdata arc postgres backup list -sn postgres01
ID                                Name                    State
--------------------------------  ----------------------  -------
5b0481dfc1c94b4cac79dd56a1bb21f4  MyBackup091720200110am  Done
0cf39f1e92344e6db4cfa285d36c7b14  MyBackup091720200111am  Done
```
그 중 첫 번째를 삭제 하려는 경우 다음 명령을 실행 합니다.
```console
azdata arc postgres backup delete -sn postgres01 -n MyBackup091720200110am
{
  "ID": "5b0481dfc1c94b4cac79dd56a1bb21f4",
  "name": "MyBackup091720200110am",
  "state": "Done"
}
```
해당 시점에 백업을 나열 하는 경우 다음과 같은 출력을 얻게 됩니다.
```console
azdata arc postgres backup list -sn postgres01
ID                                Name                    State
--------------------------------  ----------------------  -------
0cf39f1e92344e6db4cfa285d36c7b14  MyBackup091720200111am  Done
```

Delete 명령에 대 한 자세한 내용을 보려면 다음을 실행 하십시오.
```console
azdata arc postgres backup delete --help
```

## <a name="next-steps"></a>다음 단계
- 서버 그룹에 대 한 [규모 확장 (작업자 노드 추가)을](scale-out-postgresql-hyperscale-server-group.md) 참조 하세요.
- 서버 그룹 [확장 또는 축소 (메모리/vcores 늘리기/줄이기)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) 를 참조 하세요.
