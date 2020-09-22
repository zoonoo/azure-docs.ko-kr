---
title: Azure Arc 사용 SQL Managed Instance 삭제
description: Azure Arc 사용 SQL Managed Instance 삭제
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e531349e8f404380d9f0601caa3b66557c297062
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940852"
---
# <a name="delete-azure-arc-enabled-sql-managed-instance"></a>Azure Arc 사용 SQL Managed Instance 삭제
이 문서에서는 Azure Arc 사용 SQL Managed Instance를 삭제 하는 방법을 설명 합니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="view-existing-azure-arc-enabled-sql-managed-instances"></a>기존 Azure Arc 사용 SQL 관리 되는 인스턴스 보기
SQL 관리 되는 인스턴스를 보려면 다음 명령을 실행 합니다.

```console
azdata arc sql mi list
```

출력은 다음과 같이 표시 됩니다.

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
demo-mi 1/1         10.240.0.4:32023  Ready
```

## <a name="delete-a-azure-arc-enabled-sql-managed-instance"></a>Azure Arc 사용 SQL Managed Instance 삭제
SQL Managed Instance를 삭제 하려면 다음 명령을 실행 합니다.

```console
azdata arc sql mi delete -n <NAME_OF_INSTANCE>
```

출력은 다음과 같이 표시 됩니다.

```console
# azdata arc sql mi delete -n demo-mi
Deleted demo-mi from namespace arc
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>Kubernetes 영구 볼륨 클레임 회수 (Pvc)

SQL Managed Instance를 삭제 해도 연결 된 [pvc](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)는 제거 되지 않습니다. 이것은 의도적인 것입니다. 인스턴스 삭제가 실수로 발생 한 경우 사용자가 데이터베이스 파일에 액세스 하도록 도와 줍니다. Pvc는 반드시 삭제할 필요는 없습니다. 그러나 권장 됩니다. 이러한 Pvc를 회수 하지 않는 경우 Kubernetes 클러스터의 디스크 공간이 부족 하므로 결국 오류가 발생 합니다. 다음 단계를 수행 하 여 Pvc를 회수할 수 있습니다.

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1. 삭제 한 서버 그룹의 Pvc를 나열 합니다.
Pvc를 나열 하려면 다음 명령을 실행 합니다.
```console
kubectl get pvc
```

아래 예제에서는 삭제 한 SQL 관리 되는 인스턴스의 Pvc를 확인 합니다.
```console
# kubectl get pvc -n arc

NAME                    STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
data-demo-mi-0        Bound     pvc-1030df34-4b0d-4148-8986-4e4c20660cc4   5Gi        RWO            managed-premium   13h
logs-demo-mi-0        Bound     pvc-11836e5e-63e5-4620-a6ba-d74f7a916db4   5Gi        RWO            managed-premium   13h
```

### <a name="2-delete-each-of-the-pvcs"></a>2. 각 Pvc를 삭제 합니다.
삭제 한 SQL 관리 되는 각 인스턴스에 대 한 데이터 및 로그 Pvc를 삭제 합니다.
이 명령의 일반적인 형식은 다음과 같습니다. 
```console
kubectl delete pvc <name of pvc>
```

다음은 그 예입니다. 
```console
kubectl delete pvc data-demo-mi-0 -n arc
kubectl delete pvc logs-demo-mi-0 -n arc
```

이러한 각 kubectl 명령은 PVC가 성공적으로 삭제 되었는지 확인 합니다. 다음은 그 예입니다. 
```console
persistentvolumeclaim "data-demo-mi-0" deleted
persistentvolumeclaim "logs-demo-mi-0" deleted
```
  

> [!NOTE]
> 표시 된 것 처럼, Pvc를 삭제 하지 않으면 오류가 발생 하는 상황에서 Kubernetes 클러스터를 가져올 수 있습니다. 이러한 오류 중 일부에는 azdata를 사용 하 여 Kubernetes 클러스터에 로그인 할 수 없는 항목이 포함 될 수 있습니다 .이 저장소 문제로 인해 pod가 제거 될 수 있습니다 (정상적인 Kubernetes 동작).
>
> 예를 들어 다음과 유사한 로그에 메시지가 표시 될 수 있습니다.  
> - 주석: microsoft.com/ignore-pod-health: true  
> - 상태: 실패  
> - 이유: 제거 되었습니다.  
> - 메시지: 리소스가 부족 합니다. 삭제 된 저장소입니다. 컨테이너 컨트롤러가 16372Ki를 사용 하 고 있습니다 .이는 0의 요청을 초과 합니다.

## <a name="next-steps"></a>다음 단계

[Azure Arc 지원 SQL Managed Instance의 특징 및 기능](managed-instance-features.md)에 대한 자세한 정보

[데이터 컨트롤러를 만들기 시작](create-data-controller.md)

데이터 컨트롤러가 이미 만들어져 있나요? [Azure Arc 지원 SQL Managed Instance 만들기](create-sql-managed-instance.md)