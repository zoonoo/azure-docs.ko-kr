---
title: Azure Arc enabled PostgreSQL Hyperscale 서버 그룹을 삭제 합니다.
description: Azure Arc 사용 설정 Postgres Hyperscale 서버 그룹 삭제
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: cacef205b614170df210e03ddf3978a64a90ea22
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151934"
---
# <a name="delete-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc enabled PostgreSQL Hyperscale 서버 그룹을 삭제 합니다.

이 문서에서는 Azure Arc 설정에서 서버 그룹을 삭제 하는 단계에 대해 설명 합니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-the-server-group"></a>서버 그룹 삭제

예를 들어 아래 설정에서 _postgres01_ 인스턴스를 삭제 하 려 한다고 가정해 보겠습니다.

```console
azdata arc postgres server list
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

Delete 명령의 일반적인 형식은 다음과 같습니다.
```console
azdata arc postgres server delete -n <server group name>
```
Delete 명령에 대 한 자세한 내용을 보려면 다음을 실행 하십시오.
```console
azdata arc postgres server delete --help
```

### <a name="lets-delete-the-server-group-used-in-this-example"></a>이 예제에서 사용 된 서버 그룹을 삭제 하겠습니다.
```console
azdata arc postgres server delete -n postgres01
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>Kubernetes 영구 볼륨 클레임 회수 (Pvc)

서버 그룹을 삭제 해도 연결 된 [pvc](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)는 제거 되지 않습니다. 이것은 의도적인 것입니다. 인스턴스가 실수로 삭제된 경우 사용자가 데이터베이스 파일에 액세스하도록 도와줍니다. PVC는 반드시 삭제할 필요는 없습니다. 그러나 권장됩니다. 이러한 PVC를 회수하지 않으면 Kubernetes 클러스터에서 디스크 공간이 부족하다고 간주하므로 오류가 발생합니다. PVC를 회수하려면 다음 단계를 수행합니다.

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1. 삭제 한 서버 그룹의 Pvc를 나열 합니다.
Pvc를 나열 하려면 다음 명령을 실행 합니다.
```console
kubectl get pvc [-n <namespace name>]
```

즉, 삭제 한 서버 그룹에 대 한 pvc, 특히 Pvc의 목록을 반환 합니다. 예를 들면 다음과 같습니다.
```console
kubectl get pvc
NAME                STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-postgres01-0   Bound    pvc-72ccc225-dad0-4dee-8eae-ed352be847aa   5Gi        RWO            default        2d18h
data-postgres01-1   Bound    pvc-ce6f0c51-faed-45ae-9472-8cdf390deb0d   5Gi        RWO            default        2d18h
data-postgres01-2   Bound    pvc-5a863ab9-522a-45f3-889b-8084c48c32f8   5Gi        RWO            default        2d18h
data-postgres01-3   Bound    pvc-00e1ace3-1452-434f-8445-767ec39c23f2   5Gi        RWO            default        2d15h
logs-postgres01-0   Bound    pvc-8b810f4c-d72a-474a-a5d7-64ec26fa32de   5Gi        RWO            default        2d18h
logs-postgres01-1   Bound    pvc-51d1e91b-08a9-4b6b-858d-38e8e06e60f9   5Gi        RWO            default        2d18h
logs-postgres01-2   Bound    pvc-8e5ad55e-300d-4353-92d8-2e383b3fe96e   5Gi        RWO            default        2d18h
logs-postgres01-3   Bound    pvc-f9e4cb98-c943-45b0-aa07-dd5cff7ea585   5Gi        RWO            default        2d15h
```
이 서버 그룹에는 8 개의 Pvc가 있습니다.

### <a name="2-delete-each-of-the-pvcs"></a>2. 각 Pvc를 삭제 합니다.
삭제 한 서버 그룹의 각 PostgreSQL Hyperscale 노드 (코디네이터 및 작업자)에 대 한 데이터 및 로그 Pvc를 삭제 합니다.
이 명령의 일반적인 형식은 다음과 같습니다. 
```console
kubectl delete pvc <name of pvc>  [-n <namespace name>]
```

예를 들면 다음과 같습니다.
```console
kubectl delete pvc data-postgres01-0
kubectl delete pvc data-postgres01-1 
kubectl delete pvc data-postgres01-2
kubectl delete pvc data-postgres01-3
kubectl delete pvc logs-postgres01-0
kubectl delete pvc logs-postgres01-1
kubectl delete pvc logs-postgres01-2
kubectl delete pvc logs-postgres01-3
```

이러한 각 kubectl 명령은 PVC가 성공적으로 삭제 되었는지 확인 합니다. 예를 들면 다음과 같습니다.
```console
persistentvolumeclaim "data-postgres01-0" deleted
```
  

>**참고** 표시 된 것 처럼, Pvc를 삭제 하지 않으면 오류가 발생 하는 상황에서 Kubernetes 클러스터를 가져올 수 있습니다. 이러한 오류 중 일부에는 azdata를 사용 하 여 Kubernetes 클러스터에 로그인 할 수 없는 항목이 포함 될 수 있습니다 .이 저장소 문제로 인해 pod가 제거 될 수 있습니다 (정상적인 Kubernetes 동작).
>
> 예를 들어 다음과 유사한 로그에 메시지가 표시 될 수 있습니다.  
    > 주석: microsoft.com/ignore-pod-health: true  
    > 상태: 실패  
    > 이유: 제거 되었습니다.  
    > 메시지: 리소스가 부족 합니다. 삭제 된 저장소입니다. 컨테이너 컨트롤러가 16372Ki를 사용 하 고 있습니다 .이는 0의 요청을 초과 합니다.
    
## <a name="next-step"></a>다음 단계
[Azure Arc 사용 PostgreSQL Hyperscale](create-postgresql-hyperscale-server-group.md) 만들기
