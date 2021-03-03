---
title: Azure Database for PostgreSQL 하이퍼스케일 서버 그룹에 대한 백업 및 복원
description: Azure Database for PostgreSQL 하이퍼스케일 서버 그룹에 대한 백업 및 복원
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: 8b3304c673e8606667246a7d0df9ad8f3be11d9b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101686702"
---
# <a name="back-up-and-restore-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Azure Arc 사용 PostgreSQL Hyperscale 서버 그룹 백업 및 복원

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Azure Arc enabled PostgreSQL Hyperscale 서버 그룹을 백업 하거나 복원할 때 서버 그룹의 모든 PostgreSQL 노드에 있는 전체 데이터베이스 집합이 백업 및/또는 복원 됩니다.

## <a name="take-a-manual-full-backup"></a>수동 전체 백업 수행

서버 그룹의 전체 데이터 및 로그 폴더에 대 한 전체 백업을 수행 하려면 다음 명령을 실행 합니다.
```console
azdata arc postgres backup create [--name <backup name>] --server-name <server group name> [--no-wait] 
```
위치:
- __이름__ 백업 이름을 나타냅니다.
- __서버 이름__ 은 서버 그룹을 나타냅니다.
- __대기 안 함__ 은 명령줄에서이 명령줄 창을 계속 사용할 수 있도록 백업이 완료 될 때까지 기다리지 않음을 나타냅니다.

이 명령은 Azure Arc enabled PostgreSQL Hyperscale 서버 그룹을 구성 하는 모든 노드에서 분산 된 전체 백업을 조정 합니다. 즉, 코디네이터 및 작업자 노드의 모든 데이터를 백업 합니다.

다음은 그 예입니다. 

```console
azdata arc postgres backup create --name backup12082020-0250pm --server-name postgres01
```

백업이 완료 되 면 백업의 ID, 이름, 크기, 상태 및 타임 스탬프가 반환 됩니다. 다음은 그 예입니다. 
```console
{
  "ID": "8085723fcbae4aafb24798c1458f4bb7",
  "name": "backup12082020-0250pm",
  "size": "9.04 MiB",
  "state": "Done",
  "timestamp": "2020-12-08 22:50:22+00:00"
}
```
`+xx:yy` 백업이 수행 된 시간의 표준 시간대를 나타냅니다. 이 예제에서 "+ 00:00"은 UTC 시간 (UTC + 00 시간 00 분)을 의미 합니다.

> [!NOTE]
> 아직 다음 작업을 수행할 수 없습니다.
> - 자동 백업 예약
> - 수행 중인 백업 진행률 표시

## <a name="list-backups"></a>백업 나열

복원에 사용할 수 있는 백업을 나열 하려면 다음 명령을 실행 합니다.

```console
azdata arc postgres backup list --server-name <servergroup name>
```

다음은 그 예입니다. 

```console
azdata arc postgres backup list --server-name postgres01
```

다음과 같은 출력을 반환 합니다.

```output
ID                                Name                   Size       State    Timestamp
--------------------------------  ---------------------  ---------  -------  -------------------------
d744303b1b224ef48be9cba4f58c7cb9  backup12072020-0731pm  13.83 MiB  Done     2020-12-08 03:32:09+00:00
c4f964d28da34318a420e6d14374bd36  backup12072020-0819pm  9.04 MiB   Done     2020-12-08 04:19:49+00:00
a304c6ef99694645a2a90ce339e94714  backup12072020-0822pm  9.1 MiB    Done     2020-12-08 04:22:26+00:00
47d1f57ec9014328abb0d8fe56020760  backup12072020-0827pm  9.06 MiB   Done     2020-12-08 04:27:22+00:00
8085723fcbae4aafb24798c1458f4bb7  backup12082020-0250pm  9.04 MiB   Done     2020-12-08 22:50:22+00:00
```

Timestamp 열은 백업이 수행 된 특정 시점 UTC를 나타냅니다.

## <a name="restore-a-backup"></a>백업 복원
이 섹션에서는 전체 복원 또는 지정 시간 복원을 수행 하는 방법을 보여 줍니다. 전체 백업을 복원 하는 경우 백업의 전체 콘텐츠를 복원 합니다. 지정 시간 복원을 수행 하는 경우 지정 시간까지 복원 합니다. 이 시점 이후에 수행 된 모든 트랜잭션은 복원 되지 않습니다.

### <a name="restore-a-full-backup"></a>전체 백업 복원
백업의 전체 콘텐츠를 복원 하려면 다음 명령을 실행 합니다.
```console
azdata arc postgres backup restore --server-name <target server group name> [--source-server-name <source server group name> --backup-id <backup id>]
or
azdata arc postgres backup restore -sn <target server group name> [-ssn <source server group name> --backup-id <backup id>]
```
<!--To read the general format of restore command, run: azdata arc postgres backup restore --help -->

위치:
- __backup-id__ 는 위에 표시 된 백업 나열 명령에 표시 된 백업의 id입니다.
Azure Arc enabled PostgreSQL Hyperscale 서버 그룹을 구성 하는 모든 노드에서 분산 된 전체 복원을 조정 합니다. 즉, 코디네이터와 작업자 노드의 모든 데이터를 복원 합니다.

#### <a name="examples"></a>예:

__Postgres01 서버 그룹을 자체로 복원 합니다.__

```console
azdata arc postgres backup restore -sn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```

이 작업은 PostgreSQL 버전 12 이상 에서만 지원 됩니다.

__서버 그룹 postgres01을 다른 서버 그룹 postgres02로 복원 합니다.__

```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```
이 작업은 버전 11부터 PostgreSQL 버전의 모든 버전에서 지원 됩니다. 복원 작업을 수행 하기 전에 대상 서버 그룹을 만든 후에는 동일한 구성 이어야 하며 원본 서버 그룹과 동일한 백업 PVC를 사용 해야 합니다.

복원 작업이 완료 되 면 명령줄에 다음과 같은 출력이 반환 됩니다.

```json
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "state": "Done"
}
```

> [!NOTE]
> 아직 다음 작업을 수행할 수 없습니다.
> - 이름을 표시 하 여 백업을 복원 합니다.
> - 복원 작업의 진행 상태를 표시 합니다.


### <a name="do-a-point-in-time-restore"></a>특정 시점 복원 수행

서버 그룹을 특정 시점까지 복원 하려면 명령을 실행 합니다.
```console
azdata arc postgres backup restore --server-name <target server group name> --source-server-name <source server group name> --time <point in time to restore to>
or
azdata arc postgres backup restore -sn <target server group name> -ssn <source server group name> -t <point in time to restore to>
```

복원 명령의 일반 형식을 읽으려면를 실행 `azdata arc postgres backup restore --help` 합니다.

여기서 `time` 는를 복원 하는 시점입니다. 타임 스탬프 또는 숫자와 접미사 ( `m` 분, `h` 시간, `d` 일 또는 `w` 주)를 제공 합니다. 예를 들면 `1.5h` 90 분 뒤로 돌아갑니다.

#### <a name="examples"></a>예:
__Postgres01 서버 그룹의 특정 시점 복원을 수행 합니다.__

서버 그룹에 대 한 특정 시점 복원은 아직 수행할 수 없습니다.

__Postgres01 서버 그룹에 대 한 특정 타임 스탬프로 postgres02 하는 서버 그룹의 특정 시점 복원을 수행 합니다.__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "2020-12-08 04:23:48.751326+00"
``` 

이 예에서는 서버 그룹 postgres01가 2020 년 12 월 8 일에 년 12 월 8 일에 postgres02 상태를 서버 그룹으로 복원 합니다. "+ 00"은 지정 된 특정 시점의 표준 시간대를 나타냅니다. 표준 시간대를 지정 하지 않으면 복원 작업을 실행 하는 클라이언트의 표준 시간대가 사용 됩니다.

다음은 그 예입니다. 
- `2020-12-08 04:23:48.751326+00`UTC로 해석 됩니다. `2020-12-08 04:23:48.751326`
- 태평양 표준 시간대 (PST = UTC + 08)에 있는 경우 `2020-12-08 04:23:48.751326` 는 UTC로 해석 됩니다 `2020-12-08 12:23:48.751326` .이 작업은 PostgreSQL 버전 11 버전의 모든 버전에서 지원 됩니다. 대상 서버 그룹은 복원 작업 전에 만들어야 하며 원본 서버 그룹과 동일한 백업 PVC를 사용 해야 합니다.


__다른 서버 그룹에 대 한 postgres01 서버 그룹의 지정 시간 복원을 수행 하 여 과거의 특정 시간으로 postgres02 합니다.__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "22m"
```

이 예에서는 서버 그룹 postgres02 서버 그룹 postgres01가 22 분 전 상태로 복원 됩니다.
이 작업은 버전 11부터 PostgreSQL 버전의 모든 버전에서 지원 됩니다. 대상 서버 그룹은 복원 작업 전에 만들어야 하며 원본 서버 그룹과 동일한 백업 PVC를 사용 해야 합니다.

> [!NOTE]
> 아직 다음 작업을 수행할 수 없습니다.
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

다음은 그 예입니다. 

```console
azdata arc postgres backup delete -sn postgres01 -n MyBackup091720200110am
{
  "ID": "5b0481dfc1c94b4cac79dd56a1bb21f4",
  "name": "MyBackup091720200110am",
  "state": "Done"
}
```

이전 단락에서 설명한 대로 백업 나열 명령을 실행 하 여 백업의 이름 및 ID를 검색할 수 있습니다.

Delete 명령에 대 한 자세한 내용을 보려면 다음을 실행 하십시오.

```console
azdata arc postgres backup delete --help
```

## <a name="next-steps"></a>다음 단계
- 서버 그룹에 대 한 [규모 확장 (작업자 노드 추가)을](scale-out-postgresql-hyperscale-server-group.md) 참조 하세요.
- 서버 그룹 [확장 또는 축소 (메모리/vcores 늘리기/줄이기)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) 를 참조 하세요.
