---
title: Azure Arc Managed Instance 고가용성 사용
titleSuffix: Deploy Azure Arc enabled Managed Instance with high availability
description: 고가용성을 사용 하 여 Azure Arc 사용 Managed Instance을 배포 하는 방법을 알아봅니다.
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: conceptual
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 92f5c900238fc5d40e22870e2f00f8adeb5d335f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032197"
---
# <a name="azure-arc-enabled-managed-instance-high-availability"></a>Azure Arc Managed Instance 고가용성 사용

Azure Arc enabled Managed Instance는 컨테이너 화 된 응용 프로그램으로 Kubernetes에 배포 되 고 상태 저장 집합 및 영구 저장소와 같은 Kubernetes 구문을 사용 하 여 기본 제공 상태 모니터링, 실패 검색 및 서비스 상태를 유지 하는 장애 조치 (failover) 메커니즘을 제공 합니다. 안정성을 높이기 위해 Azure Arc 사용 Managed Instance를 구성 하 여 고가용성 구성에서 추가 복제본으로 배포할 수도 있습니다. 모니터링, 실패 검색 및 자동 장애 조치 (failover)는 Arc data services 데이터 컨트롤러에 의해 관리 됩니다. 이 서비스는 사용자 개입 없이 가용성 그룹 설정, 데이터베이스 미러링 엔드포인트 구성에서 가용성 그룹에 데이터베이스 추가 또는 장애 조치(failover) 및 업그레이드 조정에 이르기까지 모든 기능을 제공합니다. 이 문서에서는 두 가지 유형의 고가용성을 모두 살펴봅니다.

## <a name="built-in-high-availability"></a>기본 제공되는 고가용성 

원격 영구 저장소가 구성 되 고 Arc 데이터 서비스 배포에 사용 되는 노드와 공유 될 때 Kubernetes에서 기본 제공 고가용성을 제공 합니다. 이 구성에서 Kubernetes는 클러스터 오케스트레이터의 역할을 수행합니다. 컨테이너 또는 기본 노드에서 관리 되는 인스턴스가 실패 하면 오 케 스트레이 터는 컨테이너의 다른 인스턴스를 부트스트랩 하 고 동일한 영구 저장소에 연결 합니다. 이 형식은 Azure Arc 사용 Managed Instance을 배포할 때 기본적으로 사용 하도록 설정 됩니다.

### <a name="verify-built-in-high-availability"></a>기본 제공 고가용성 확인

이 섹션에서는 Kubernetes에서 제공 하는 기본 제공 고가용성을 확인 합니다. 이 기능을 테스트 하는 단계를 수행 하는 경우 기존 관리 되는 인스턴스의 pod를 삭제 하 고 Kubernetes이이 작업에서 복구 되는지 확인 합니다. 

### <a name="prerequisites"></a>사전 요구 사항

- Kubernetes 클러스터에는 [공유 된 원격 저장소가](storage-configuration.md#factors-to-consider-when-choosing-your-storage-configuration) 있어야 합니다. 
- 단일 복제본을 사용 하 여 배포 된 Azure Arc Managed Instance (기본값)

1. Pod를 봅니다. 

   ```console
   kubectl get pods -n <namespace of data controller>
   ```

2. 관리 되는 인스턴스 pod를 삭제 합니다.

   ```console
   kubectl delete pod <name of managed instance>-0 -n <namespace of data controller>
   ```

   예

   ```output
   user@pc:/# kubectl delete pod sql1-0 -n arc
   pod "sql1-0" deleted
   ```

3. Pod를 확인 하 여 관리 되는 인스턴스가 복구 되 고 있는지 확인 합니다.

   ```console
   kubectl get pods -n <namespace of data controller>
   ```

   예

   ```output
   user@pc:/# kubectl get pods -n arc
   NAME                 READY   STATUS    RESTARTS   AGE
   sql1-0               2/3     Running   0          22s
   ```

Pod 내의 모든 컨테이너가 복구 된 후에 관리 되는 인스턴스에 연결할 수 있습니다.

## <a name="deploy-with-always-on-availability-groups"></a>Always On 가용성 그룹를 사용 하 여 배포

안정성을 높이기 위해 Azure Arc 사용 Managed Instance를 구성 하 여 고가용성 구성에서 추가 복제본과 함께 배포할 수 있습니다. 

가용성 그룹을 사용 하도록 설정 하는 기능:

- 여러 복제본을 사용 하 여 배포 된 경우 이라는 단일 가용성 그룹을 `containedag` 만듭니다. 기본적으로 `containedag`에는 주 복제본을 포함하여 세 개의 복제본이 있습니다. 가용성 그룹 만들기 또는 만들어진 가용성 그룹에 복제본 조인을 포함하여 가용성 그룹에 대한 모든 CRUD 작업은 내부적으로 관리됩니다. Azure Arc 사용 Managed Instance에서 추가 가용성 그룹을 만들 수 없습니다.

- 모든 사용자 및 시스템 데이터베이스(예: `master` 및 `msdb`)를 포함하여 모든 데이터베이스가 가용성 그룹에 자동으로 추가됩니다. 이 기능은 가용성 그룹 복제본 전체에서 단일 시스템 보기를 제공합니다. `containedag_master` `containedag_msdb` 인스턴스에 직접 연결 하는 경우와 데이터베이스를 모두 확인 합니다. `containedag_*` 데이터베이스는 가용성 그룹 내의 `master` 및 `msdb`를 나타냅니다.

- 외부 엔드포인트는 가용성 그룹 내의 데이터베이스에 연결하기 위해 자동으로 프로비저닝됩니다. 이 `<managed_instance_name>-svc-external` 엔드포인트는 가용성 그룹 수신기의 역할을 수행합니다.

### <a name="deploy"></a>배포

가용성 그룹을 사용 하 여 관리 되는 인스턴스를 배포 하려면 다음 명령을 실행 합니다.

```console
azdata arc sql mi create -n <name of instance> --replicas 3
```

### <a name="check-status"></a>상태 확인
인스턴스가 배포 된 후 다음 명령을 실행 하 여 인스턴스의 상태를 확인 합니다.

```console
azdata arc sql mi list
azdata arc sql mi show -n <name of instance>
```

예제 출력:

```output
user@pc:/# azdata arc sql mi list
ExternalEndpoint    Name    Replicas    State
------------------  ------  ----------  -------
20.131.31.58,1433   sql2    3/3         Ready

user@pc:/#  azdata arc sql mi show -n sql2
{
...
  "status": {
    "AGStatus": "Healthy",
    "externalEndpoint": "20.131.31.58,1433",
    "logSearchDashboard": "link to logs dashboard",
    "metricsDashboard": "link to metrics dashboard",
    "readyReplicas": "3/3",
    "state": "Ready"
  }
}
```

추가 번호 `Replicas` 와 `AGstatus` 가용성 그룹의 상태를 나타내는 필드를 확인 합니다. 모든 복제본이 설정 되 고 동기화 되 면이 값은 `healthy` 입니다. 

### <a name="restore-a-database"></a>데이터베이스 복원 
데이터베이스를 가용성 그룹으로 복원 하려면 추가 단계가 필요 합니다. 다음 단계에서는 데이터베이스를 관리 되는 인스턴스로 복원 하 고이를 가용성 그룹에 추가 하는 방법을 보여 줍니다. 

1. 새 Kubernetes 서비스를 만들어 주 인스턴스 외부 끝점을 노출 합니다.

    관리 되는 인스턴스에 연결 하 여 주 복제본을 호스트 하는 pod를 확인 하 고 다음을 실행 합니다.

    ```sql
    SELECT @@SERVERNAME
    ```
    Kubernetes 클러스터에서 nodePort 서비스를 사용 하는 경우 아래 명령을 실행 하 여 kubernetes 서비스를 주 인스턴스에 만듭니다. `podName`을 이전 단계에서 반환 된 서버의 이름으로 바꾸고,을 `serviceName` Kubernetes 서비스의 기본 이름으로 바꿉니다.

    ```bash
    kubectl -n <namespaceName> expose pod <podName> --port=1533  --name=<serviceName> --type=NodePort
    ```

    LoadBalancer 서비스의 경우 만들어진 서비스의 형식이 인 경우를 제외 하 고 동일한 명령을 실행 합니다 `LoadBalancer` . 다음은 그 예입니다.  

    ```bash
    kubectl -n <namespaceName> expose pod <podName> --port=1533  --name=<serviceName> --type=LoadBalancer
    ```

    다음은 Azure Kubernetes 서비스에 대해 실행 되는이 명령의 예입니다. 여기에서 주 복제본을 호스팅하는 pod는 `sql2-0` 다음과 같습니다.

    ```bash
    kubectl -n arc-cluster expose pod sql2-0 --port=1533  --name=sql2-0-p --type=LoadBalancer
    ```

    생성한 Kubernetes 서비스의 IP를 가져옵니다.

    ```bash
    kubectl get services -n <namespaceName>
    ```
2. 데이터베이스를 주 인스턴스 끝점으로 복원 합니다.

    주 인스턴스 컨테이너에 데이터베이스 백업 파일을 추가 합니다.

    ```console
    kubectl cp <source file location> <pod name>:var/opt/mssql/data/<file name> -n <namespace name>
    ```

    예제

    ```console
    kubectl cp /home/WideWorldImporters-Full.bak sql2-1:var/opt/mssql/data/WideWorldImporters-Full.bak -c arc-sqlmi -n arc
    ```

    아래 명령을 실행 하 여 데이터베이스 백업 파일을 복원 합니다.

    ```sql 
    RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/<file name>.bak'
    WITH MOVE '<database name>' to '/var/opt/mssql/data/<file name>.mdf'  
    ,MOVE '<database name>' to '/var/opt/mssql/data/<file name>_log.ldf'  
    ,RECOVERY, REPLACE, STATS = 5;  
    GO
    ```
    
    예제

    ```sql
    RESTORE Database WideWorldImporters
    FROM DISK = '/var/opt/mssql/data/WideWorldImporters-Full.BAK'
    WITH
    MOVE 'WWI_Primary' TO '/var/opt/mssql/data/WideWorldImporters.mdf',
    MOVE 'WWI_UserData' TO '/var/opt/mssql/data/WideWorldImporters_UserData.ndf',
    MOVE 'WWI_Log' TO '/var/opt/mssql/data/WideWorldImporters.ldf',
    MOVE 'WWI_InMemory_Data_1' TO '/var/opt/mssql/data/WideWorldImporters_InMemory_Data_1',
    RECOVERY, REPLACE, STATS = 5;  
    GO
    ```

3. 가용성 그룹에 데이터베이스를 추가합니다.

    AG에 데이터베이스를 추가 하려면 전체 복구 모드에서 실행 해야 하 고 로그 백업을 수행 해야 합니다. 아래 TSQL 문을 실행 하 여 복원 된 데이터베이스를 가용성 그룹에 추가 합니다.

    ```sql
    ALTER DATABASE <databaseName> SET RECOVERY FULL;
    BACKUP DATABASE <databaseName> TO DISK='<filePath>'
    ALTER AVAILABILITY GROUP containedag ADD DATABASE <databaseName>
    ```

    다음 예제에서는 인스턴스에 복원된 `WideWorldImporters`라는 데이터베이스를 추가합니다.

    ```sql
    ALTER DATABASE WideWorldImporters SET RECOVERY FULL;
    BACKUP DATABASE WideWorldImporters TO DISK='/var/opt/mssql/data/WideWorldImporters.bak'
    ALTER AVAILABILITY GROUP containedag ADD DATABASE WideWorldImporters
    ```

> [!IMPORTANT]
> 다음 명령을 실행하여 위에서 만든 Kubernetes 서비스를 삭제하여 정리하는 것이 좋습니다.
>
>```bash
>kubectl delete svc sql2-0-p -n arc
>```

### <a name="limitations"></a>제한 사항

Azure Arc 사용 가능 Managed Instance 가용성 그룹에는 [빅 데이터 클러스터 가용성 그룹과 동일한 제한이 있습니다. 자세한 내용을 보려면 여기를 클릭 하세요.](/sql/big-data-cluster/deployment-high-availability#known-limitations)

## <a name="next-steps"></a>다음 단계

[Azure Arc 지원 SQL Managed Instance의 특징 및 기능](managed-instance-features.md)에 대한 자세한 정보
