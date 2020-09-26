---
title: 스토리지 구성
description: Azure Arc 사용 데이터 서비스 저장소 구성 옵션을 설명 합니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: uc-msft
ms.author: umajay
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: c1560325f21fd60e6bdb2a64eb987359a7246ff2
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317330"
---
# <a name="storage-configuration"></a>스토리지 구성

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="kubernetes-storage-concepts"></a>Kubernetes 저장소 개념

Kubernetes는 기본 가상화 기술 스택 (선택 사항) 및 하드웨어에 대 한 인프라 추상화 계층을 제공 합니다. 저장소 **[클래스](https://kubernetes.io/docs/concepts/storage/storage-classes/)** 를 통해 Kubernetes에서 저장소를 추상화 하는 방법이 있습니다. Pod를 프로 비전 할 때 각 볼륨에 사용할 저장소 클래스를 지정할 수 있습니다. Pod가 프로 비전 될 때 저장소를 프로 비전 하기 위해 저장소 클래스 **[provisioner](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)** 가 호출 된 다음, 프로 비전 된 저장소에 **[영구 볼륨이](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)** 생성 된 후 pod가 영구적 볼륨 **[클레임](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims)** 에 의해 영구적 볼륨에 탑재 됩니다.

Kubernetes은 저장소 인프라 공급자가 Kubernetes를 확장 하는 드라이버 ("Addons" 라고도 함)를 연결 하는 방법을 제공 합니다. Storage addons는 **[컨테이너 저장소 인터페이스 표준을](https://kubernetes.io/blog/2019/01/15/container-storage-interface-ga/)** 준수 해야 합니다. 이러한 비 결정적인 **[CSI 드라이버 목록](https://kubernetes-csi.github.io/docs/drivers.html)** 에서 찾을 수 있는 수십 개의 addons 있습니다. 사용 하는 CSI 드라이버는 클라우드에서 호스트 되는 관리 되는 Kubernetes 서비스에서 실행 중인지 또는 하드웨어에 사용 중인 OEM 공급자와 같은 요소에 따라 달라 집니다.

다음 명령을 실행 하 여 Kubernetes 클러스터에 구성 된 저장소 클래스를 볼 수 있습니다.

``` terminal
kubectl get storageclass
```

Azure Kubernetes 서비스 (AKS) 클러스터의 예제 출력:

``` terminal
NAME                PROVISIONER                AGE
azurefile           kubernetes.io/azure-file   15d
azurefile-premium   kubernetes.io/azure-file   15d
default (default)   kubernetes.io/azure-disk   4d3h
managed-premium     kubernetes.io/azure-disk   4d3h
```

다음 명령을 실행 하 여 저장소 클래스에 대 한 세부 정보를 가져올 수 있습니다.

``` terminal
kubectl describe storageclass\<storage class name>
```

예:

``` terminal
kubectl describe storageclass/azurefile

Name:            azurefile
IsDefaultClass:  No
Annotations:     kubectl.kubernetes.io/last-applied-configuration={"allowVolumeExpansion":true,"apiVersion":"storage.k8s.io/v1beta1","kind":"StorageClass","metadata":{"annotations":{},"labels":{"kubernetes.io/cluster-service":"true"},"name":"azurefile"},"parameters":{"sku
Name":"Standard_LRS"},"provisioner":"kubernetes.io/azure-file"}

Provisioner:           kubernetes.io/azure-file
Parameters:            skuName=Standard_LRS
AllowVolumeExpansion:  True
MountOptions:          <none>
ReclaimPolicy:         Delete
VolumeBindingMode:     Immediate
Events:                <none>
```

다음 명령을 실행 하 여 현재 프로 비전 된 영구적 볼륨 및 영구적 볼륨 클레임을 볼 수 있습니다.

``` terminal
kubectl get persistentvolumes -n <namespace>

kubectl get persistentvolumeclaims -n <namespace>
```

영구 볼륨을 표시 하는 예제:

``` terminal

kubectl get persistentvolumes -n arc

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                      STORAGECLASS   REASON   AGE
pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            Delete           Bound    arc/sqldemo11-data-claim   default                 7d3h
pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            Delete           Bound    arc/data-metricsdb-0       default                 7d14h
pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            Delete           Bound    arc/sqldemo11-logs-claim   default                 7d3h
pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            Delete           Bound    arc/data-controller        default                 7d14h
pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            Delete           Bound    arc/logs-controller        default                 7d14h
pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            Delete           Bound    arc/logs-metricsdb-0       default                 7d14h
pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            Delete           Bound    arc/sqldemo10-logs-claim   default                 7d14h
pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            Delete           Bound    arc/sqldemo10-data-claim   default                 7d14h
pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            Delete           Bound    arc/data-controldb         default                 7d14h
pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            Delete           Bound    arc/logs-controldb         default                 7d14h
pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            Delete           Bound    arc/logs-logsdb-0          default                 7d14h
pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            Delete           Bound    arc/data-logsdb-0          default                 7d14h
```

영구적 볼륨 클레임을 표시 하는 예제:

``` terminal

kubectl get persistentvolumeclaims -n arc

NAME                   STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-controldb         Bound    pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            default        7d14h
data-controller        Bound    pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            default        7d14h
data-logsdb-0          Bound    pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            default        7d14h
data-metricsdb-0       Bound    pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            default        7d14h
logs-controldb         Bound    pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            default        7d14h
logs-controller        Bound    pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            default        7d14h
logs-logsdb-0          Bound    pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            default        7d14h
logs-metricsdb-0       Bound    pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            default        7d14h
sqldemo10-data-claim   Bound    pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            default        7d14h
sqldemo10-logs-claim   Bound    pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            default        7d14h
sqldemo11-data-claim   Bound    pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            default        7d4h
sqldemo11-logs-claim   Bound    pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            default        7d4h

```

## <a name="factors-to-consider-when-choosing-your-storage-configuration"></a>저장소 구성을 선택할 때 고려해 야 할 요소

올바른 저장소 클래스를 선택 하는 것은 데이터 복원 력 및 성능에 매우 중요 합니다. 잘못 된 저장소 클래스를 선택 하면 하드웨어 오류가 발생 하는 경우 데이터 손실이 발생할 수 있는 위험이 발생 하거나 성능이 저하 될 수 있습니다.

일반적으로 다음과 같은 두 가지 유형의 저장소가 있습니다.

- **로컬 저장소** 저장소-지정 된 노드의 로컬 하드 드라이브에 프로 비전 됩니다. 이러한 종류의 저장소는 성능 측면에서 이상적 이지만 여러 노드에 걸쳐 데이터를 복제 하 여 데이터 중복성을 위해 특별히 설계 해야 합니다.
- **원격, 공유 저장소** -일부 원격 저장 장치 (예: EBS 또는 Azure Files와 같은 SAN, NAS 또는 클라우드 저장소 서비스)에서 프로 비전 되는 저장소 저장소입니다. 이러한 종류의 저장소는 일반적으로 데이터 중복성을 자동으로 제공 하지만 일반적으로 로컬 저장소가 될 수 만큼 빠르지는 않습니다.

> [!NOTE]
> 지금은 NFS를 사용 하는 경우 Azure Arc 데이터 컨트롤러를 배포 하기 전에 배포 프로필 파일에서 allowRunAsRoot를 true로 설정 해야 합니다.

### <a name="data-controller-storage-configuration"></a>데이터 컨트롤러 저장소 구성

데이터 서비스에 대 한 Azure Arc의 일부 서비스는 데이터를 복제 하는 기능이 없기 때문에 원격 공유 저장소를 사용 하도록 구성 된 경우에 따라 달라 집니다. 이러한 서비스는 데이터 컨트롤러 pod의 컬렉션에서 찾을 수 있습니다.

|**서비스**|**영구적 볼륨 클레임**|
|---|---|
|**ElasticSearch**|`<namespace>/logs-logsdb-0`, `<namespace>/data-logsdb-0`|
|**InfluxDB**|`<namespace>/logs-metricsdb-0`, `<namespace>/data-metricsdb-0`|
|**컨트롤러 SQL 인스턴스**|`<namespace>/logs-controldb`, `<namespace>/data-controldb`|
|**컨트롤러 API 서비스**|`<namespace>/data-controller`|

데이터 컨트롤러를 프로 비전 할 때 이러한 각 영구적 볼륨에 사용할 저장소 클래스는--저장소 클래스를 전달 하 여 지정 됩니다. -sc 매개 변수를 `azdata arc dc create` 명령에 사용 하거나, 사용 되는 배포 템플릿 파일에 control.js의 저장소 클래스를 설정 하 여 사용 합니다.

기본적으로 제공 되는 배포 템플릿에는 대상 환경에 적합 한 기본 저장소 클래스가 지정 되어 있지만 배포 시에는이를 재정의할 수 있습니다. 배포 시 pod 데이터 컨트롤러의 저장소 클래스 구성을 변경 하려면 [배포 프로필](create-data-controller.md) 을 변경 하는 자세한 단계를 참조 하세요.

--저장소 클래스를 사용 하 여 저장소 클래스를 설정 하는 경우 -sc 매개 변수 저장소 클래스는 로그 및 데이터 저장소 클래스 모두에 사용 됩니다. 배포 템플릿 파일에서 저장소 클래스를 설정 하는 경우 로그 및 데이터에 대해 서로 다른 저장소 클래스를 지정할 수 있습니다.

데이터 컨트롤러 pod 저장소 클래스를 선택할 때 고려해 야 할 중요 한 요소는 다음과 같습니다.

- 데이터 내 구성을 보장 하기 위해 원격 공유 저장소 클래스를 사용 **해야** 하며 pod가 백업 될 때 pod 또는 노드가 영구 볼륨에 다시 연결할 수 있습니다.
- 컨트롤러 SQL 인스턴스, 메트릭 DB 및 로그 DB에 기록 되는 데이터는 일반적으로 매우 낮은 볼륨 이며 대기 시간을 구분 하지 않으므로 ultra fast 성능 저장소는 중요 하지 않습니다. Grafana 및 Kibana 인터페이스를 자주 사용 하는 사용자가 있고 많은 수의 데이터베이스 인스턴스를 사용 하는 경우 사용자는 더 빠르게 수행 하는 저장소의 이점을 누릴 수 있습니다.
- 필요한 저장소 용량은 각 데이터베이스 인스턴스에 대해 로그 및 메트릭이 수집 되기 때문에 배포 된 데이터베이스 인스턴스 수에 대 한 변수입니다. 데이터는 삭제 되기 전에 2 주 동안 로그 및 메트릭 DB에 보존 됩니다. 
- 배포 후 저장소 클래스를 변경 하는 것은 문서화 되지 않고 매우 어렵고 지원 되지 않습니다. 배포 시 저장소 클래스를 올바르게 선택 해야 합니다.

> [!NOTE]
> 저장소 클래스를 지정 하지 않으면 기본 저장소 클래스가 사용 됩니다. Kubernetes 클러스터당 기본 저장소 클래스는 하나만 있을 수 있습니다. [기본 저장소 클래스를 변경할](https://kubernetes.io/docs/tasks/administer-cluster/change-default-storage-class/)수 있습니다.

### <a name="database-instance-storage-configuration"></a>데이터베이스 인스턴스 저장소 구성

각 데이터베이스 인스턴스에는 데이터, 로그 및 백업 영구 볼륨이 있습니다. 이러한 영구 볼륨의 저장소 클래스는 배포 시 지정할 수 있습니다. 저장소 클래스를 지정 하지 않으면 기본 저장소 클래스가 사용 됩니다.

또는 명령을 사용 하 여 인스턴스를 만들 때 `azdata arc sql mi create` `azdata arc postgres server create` 저장소 클래스를 설정 하는 데 사용할 수 있는 두 가지 매개 변수가 있습니다.

> [!NOTE]
> 이러한 매개 변수 중 일부는 개발 중 이며 향후 릴리스에서 사용할 수 있게 될 예정 `azdata arc sql mi create` `azdata arc postgres server create` 입니다.

|매개 변수 이름, 짧은 이름|사용 목적|
|---|---|
|`--storage-class-data`, `-scd`|트랜잭션 로그 파일을 포함 하 여 모든 데이터 파일의 저장소 클래스를 지정 하는 데 사용 됩니다.|
|`--storage-class-logs`, `-scl`|모든 로그 파일에 대 한 저장소 클래스를 지정 하는 데 사용 됩니다.|
|`--storage-class-data-logs`, `-scdl`|데이터베이스 트랜잭션 로그 파일에 대 한 저장소 클래스를 지정 하는 데 사용 됩니다. **참고: 아직 사용할 수 없습니다.**|
|`--storage-class-backups`, `-scb`|모든 백업 파일에 대 한 저장소 클래스를 지정 하는 데 사용 됩니다. **참고: 아직 사용할 수 없습니다.**|

다음 표에서는 데이터 및 로그에 대해 영구적 볼륨에 매핑되는 Azure SQL Managed Instance 컨테이너 내의 경로를 나열 합니다.

|매개 변수 이름, 짧은 이름|Mssql-miaa 컨테이너 내의 경로|Description|
|---|---|---|
|`--storage-class-data`, `-scd`|/var/opt|Mssql 설치 및 기타 시스템 프로세스에 대 한 디렉터리를 포함 합니다. Mssql 디렉터리는 기본 데이터 (트랜잭션 로그 포함), 오류 로그 & 백업 디렉터리를 포함 합니다.|
|`--storage-class-logs`, `-scl`|/var/log|콘솔 출력 (stderr, stdout)을 저장 하는 디렉터리와 컨테이너 내의 프로세스에 대 한 기타 로깅 정보를 포함 합니다.|

다음 표에서는 데이터 및 로그에 대해 영구적 볼륨에 매핑되는 PostgreSQL instance 컨테이너 내의 경로를 나열 합니다.

|매개 변수 이름, 짧은 이름|Postgres 컨테이너 내의 경로|Description|
|---|---|---|
|`--storage-class-data`, `-scd`|/var/opt/postgresql|Postgres 설치용 데이터 및 로그 디렉터리를 포함 합니다.|
|`--storage-class-logs`, `-scl`|/var/log|콘솔 출력 (stderr, stdout)을 저장 하는 디렉터리와 컨테이너 내의 프로세스에 대 한 기타 로깅 정보를 포함 합니다.|

각 데이터베이스 인스턴스에는 데이터 파일, 로그 및 백업에 대 한 별도의 영구적 볼륨이 있습니다. 즉, 볼륨 provisioner에서 저장소를 프로 비전 하는 방법에 따라 이러한 각 파일 형식에 대해 i/o를 분리 하 게 됩니다. 각 데이터베이스 인스턴스에는 자체의 영구적 볼륨 클레임 및 영구적 볼륨이 있습니다.

지정 된 데이터베이스 인스턴스에 여러 데이터베이스가 있는 경우 모든 데이터베이스는 동일한 영구 볼륨 클레임, 영구 볼륨 및 저장소 클래스를 사용 합니다. 모든 백업-차등 로그 백업과 전체 백업은 동일한 영구 볼륨 클레임 및 영구적 볼륨을 사용 합니다. 데이터베이스 인스턴스 pod에 대 한 영구 볼륨 클레임은 다음과 같습니다.

|**인스턴스**|**영구적 볼륨 클레임**|
|---|---|
|**Azure SQL Managed Instance**|`<namespace>/logs-<instance name>-0`, `<namespace>/data-<instance name>-0`|
|**PostgreSQL 용 Azure 데이터베이스 인스턴스**|`<namespace>/logs--<instance name>-0`, `<namespace>/data--<instance name>-0`|
|**Azure PostgreSQL HyperScale**|`<namespace>/logs-<instance namme>-<ordinal>`, `<namespace>/data-<instance namme>-<ordinal>` *(서 수 범위는 0에서 w로, 여기서 w는 작업자 수)*|

데이터베이스 인스턴스 pod 저장소 클래스를 선택할 때 고려해 야 할 중요 한 요소는 다음과 같습니다.

- 데이터베이스 인스턴스는 단일 pod 패턴 또는 여러 pod 패턴으로 배포할 수 있습니다. 단일 pod 패턴의 예로는 Azure SQL 관리 되는 인스턴스 또는 범용 가격 책정 계층 Azure SQL 관리 되는 인스턴스의 개발자 인스턴스가 있습니다. 여러 pod 패턴의 예로는 고가용성의 비즈니스에 중요 한 가격 책정 계층 Azure SQL 관리 되는 인스턴스가 있습니다. (참고: 가격 책정 계층은 개발 중 이며 아직 고객에 게 제공 되지 않습니다.)  단일 pod 패턴으로 배포 된 데이터베이스 인스턴스는 데이터 내 구성을 보장 하기 위해 원격 공유 저장소 클래스를 사용 **해야 합니다** . 따라서 pod가 백업 될 때 pod 또는 노드가 영구 볼륨에 다시 연결할 수 있습니다. 이와 대조적으로 항상 사용 가능한 Azure SQL 관리 되는 인스턴스는 Always On 가용성 그룹을 사용 하 여 한 인스턴스에서 다른 인스턴스로 데이터를 동기식 또는 비동기식으로 복제 합니다. 특히 데이터가 동기적으로 복제 되는 경우 항상 데이터의 여러 복사본 (일반적으로 3 개의 복사본)이 있습니다. 이 때문에 데이터 및 로그 파일에 대 한 로컬 저장소 또는 원격 공유 저장소 클래스를 사용할 수 있습니다. 로컬 저장소를 사용 하는 경우 실패 한 pod, 노드 또는 저장소 하드웨어의 경우에도 데이터가 계속 유지 됩니다. 이러한 유연성을 제공 하는 경우 더 나은 성능을 위해 로컬 저장소를 사용 하도록 선택할 수 있습니다.
- 데이터베이스 성능은 주로 지정 된 저장 장치에 대 한 i/o 처리량의 기능입니다. 데이터베이스의 읽기 또는 쓰기 작업이 많은 경우 해당 유형의 작업을 위해 설계 된 아래 하드웨어를 포함 하는 저장소 클래스를 선택 해야 합니다. 예를 들어 데이터베이스가 쓰기에 주로 사용 되는 경우 RAID 0에서 로컬 저장소를 선택할 수 있습니다. 데이터베이스가 적은 양의 "핫 데이터"에 대 한 읽기에 주로 사용 되지만 콜드 데이터의 전체 저장소 볼륨이 많은 경우 계층화 된 저장소를 지 원하는 SAN 장치를 선택할 수 있습니다. 올바른 저장소 클래스를 선택 하는 것은 데이터베이스에 사용할 저장소 유형을 선택 하는 것과 크게 다르지 않습니다.
- 로컬 저장소 볼륨 provisioner를 사용 하는 경우 디스크 i/o의 경합을 방지 하기 위해 데이터, 로그 및 백업에 대해 프로 비전 된 로컬 볼륨이 각각 서로 다른 기본 저장소 장치에 있는지 확인 해야 합니다. 또한 OS는 별도의 디스크에 탑재 된 볼륨에 있어야 합니다. 이는 기본적으로 실제 하드웨어의 데이터베이스 인스턴스에 대해 수행 되는 것과 동일한 지침을 따릅니다.
- 지정 된 인스턴스의 모든 데이터베이스가 영구적 볼륨 클레임 및 영구적 볼륨을 공유 하기 때문에 동일한 데이터베이스 인스턴스에서 사용 중인 데이터베이스 인스턴스를 배치 하지 않아야 합니다. 가능 하면 i/o 경합을 방지 하기 위해 사용 중인 데이터베이스를 자체 데이터베이스 인스턴스로 분리 합니다. 또한 노드 레이블 대상 지정을 사용 하 여 여러 노드에 걸친 전체 i/o 트래픽을 분산 하기 위해 데이터베이스 인스턴스를 별도의 노드에 배치할 수 있습니다. 가상화를 사용 하는 경우 노드 수준 뿐만 아니라 지정 된 실제 호스트의 모든 노드 Vm에서 발생 하는 결합 된 i/o 작업을 분산 하는 것을 고려해 야 합니다.

## <a name="estimating-storage-requirements"></a>저장소 요구 사항 예상
상태 저장 데이터를 포함 하는 모든 pod는이 릴리스에서 두 개의 영구 볼륨을 사용 합니다. 하나는 데이터에 대 한 영구 볼륨이 고 다른 하나는 로그에 대해 영구적 볼륨 아래 표에는 단일 데이터 컨트롤러, Azure SQL 관리 되는 인스턴스, Azure Database for PostgreSQL 인스턴스 및 Azure PostgreSQL HyperScale 인스턴스에 필요한 영구 볼륨 수가 나와 있습니다.

|리소스 종류|상태 저장 pod 수|필요한 영구 볼륨 수|
|---|---|---|
|데이터 컨트롤러|4 ( `control` , `controldb` , `logsdb` , `metricsdb` )|4 * 2 = 8|
|Azure SQL Managed Instance|1|2|
|Azure Database for PostgreSQL 인스턴스|1| 2|
|Azure PostgreSQL HyperScale|1 + w (W = 작업자 수)|2 * (1 + W)|

아래 표에는 샘플 배포에 필요한 전체 영구적 볼륨 수가 나와 있습니다.

|리소스 종류|인스턴스 수|필요한 영구 볼륨 수|
|---|---|---|
|데이터 컨트롤러|1|4 * 2 = 8|
|Azure SQL Managed Instance|5|5 * 2 = 10|
|Azure Database for PostgreSQL 인스턴스|5| 5 * 2 = 10|
|Azure PostgreSQL HyperScale|2 (작업자 수 = 인스턴스당 4)|2 * 2 * (1 + 4) = 20|
|***영구적 볼륨의 총 수***||8 + 10 + 10 + 20 = 48|

이 계산은 저장소 provisioner 또는 환경을 기반으로 Kubernetes 클러스터에 대 한 저장소를 계획 하는 데 사용할 수 있습니다. 예를 들어 5 개의 노드가 있는 Kubernetes 클러스터에 로컬 저장소 provisioner를 사용 하는 경우 모든 노드 위의 샘플 배포에는 10 개의 영구적 볼륨에 대 한 저장소 이상이 필요 합니다. 마찬가지로 노드 5 개를 사용 하 여 AKS (Azure Kubernetes Service) 클러스터를 프로 비전 할 때 10 개의 데이터 디스크를 연결할 수 있는 노드 풀에 적절 한 VM 크기를 선택 하는 것이 중요 합니다. AKS 노드의 저장소 요구에 맞게 노드 크기를 조정 하는 방법에 대 한 자세한 내용은 [여기](../../aks/operator-best-practices-storage.md#size-the-nodes-for-storage-needs)에서 찾을 수 있습니다.

## <a name="choosing-the-right-storage-class"></a>올바른 저장소 클래스 선택

### <a name="on-premises-and-edge-sites"></a>온-프레미스 및에 지 사이트

Microsoft와 해당 OEM, OS 및 Kubernetes 파트너는 Azure Arc data services에 대 한 인증 프로그램에서 작업 하 고 있습니다. 이 프로그램은 인증 테스트 도구 키트의 비교 가능한 테스트 결과를 고객에 게 제공 합니다. 테스트를 통해 기능 호환성, 스트레스 테스트 결과, 성능 및 확장성을 평가할 수 있습니다. 이러한 각 테스트 결과는 사용 된 OS, 사용 된 Kubernetes 배포, HW 사용, 사용 된 CSI 추가 기능 및 사용 된 저장소 클래스를 표시 합니다. 이를 통해 고객은 요구 사항에 가장 적합 한 저장소 클래스, OS, Kubernetes 배포 및 HW를 선택할 수 있습니다. 이 프로그램 및 초기 테스트 결과에 대 한 자세한 내용은 Azure Arc data services의 일반 공급에 더 가까운 시간에 제공 됩니다.

#### <a name="public-cloud-managed-kubernetes-services"></a>공용 클라우드, 관리 되는 Kubernetes services

공용 클라우드 기반의 관리 되는 Kubernetes 서비스의 경우 다음과 같은 권장 사항을 적용할 수 있습니다.

|공용 클라우드 서비스|권장|
|---|---|
|**AKS(Azure Kubernetes Service)**|AKS (azure Kubernetes Service)에는 두 가지 유형의 저장소 Azure Files 및 Azure 디스크가 있습니다. 각 유형의 저장소에는 표준 (HDD) 및 프리미엄 (SSD)의 두 가지 가격 책정/성능 계층이 있습니다. 따라서 AKS에서 제공 되는 네 개의 저장소 클래스는 `azurefile` (Azure Files 표준 계층), ( `azurefile-premium` Azure Files 프리미엄 계층), `default` (azure 디스크 표준 계층) 및 `managed-premium` (azure 디스크 프리미엄 계층)입니다. 기본 저장소 클래스는 `default` (Azure 디스크 표준 계층)입니다. 의사 결정에 고려해 야 하는 유형과 계층 간에는 상당한 **[가격 차이가](https://azure.microsoft.com/en-us/pricing/details/storage/)** 있습니다. 고성능 요구 사항이 있는 프로덕션 워크 로드의 경우 `managed-premium` 모든 저장소 클래스에를 사용 하는 것이 좋습니다. 개발/테스트 워크 로드, 개념 증명 등의 경우 비용을 고려 하는 `azurefile` 것이 가장 저렴 한 옵션입니다. Azure의 네트워크에 연결 된 모든 저장소 장치인 원격 공유 저장소가 필요한 상황에는 네 가지 옵션을 모두 사용할 수 있습니다. [AKS 저장소](../../aks/concepts-storage.md)에 대해 자세히 알아보세요.|
|**AWS EKS(Elastic Kubernetes Service)**| Amazon의 탄력적 Kubernetes 서비스에는 [EBS CSI storage 드라이버](https://docs.aws.amazon.com/eks/latest/userguide/ebs-csi.html)를 기반으로 하는 하나의 기본 저장소 클래스가 있습니다. 프로덕션 작업에 권장 됩니다. EKS 클러스터에 추가할 수 있는 새로운 저장소 드라이버- [EFS CSI 저장소 드라이버가](https://docs.aws.amazon.com/eks/latest/userguide/efs-csi.html) 있지만 현재는 베타 단계 이며 변경 될 수 있습니다. 이 저장소 드라이버가 프로덕션에 대해 지원 되는 것을 AWS 하지만 베타 버전에서 변경 될 수 있으므로 사용 하지 않는 것이 좋습니다. EBS 저장소 클래스는 기본값이 며를 호출 `gp2` 합니다. [EKS 저장소](https://docs.aws.amazon.com/eks/latest/userguide/storage-classes.html)에 대해 자세히 알아보세요.|
|**GKE(Google Kubernetes Engine)**|GKE (Google Kubernetes Engine)에는 `standard` [gke 영구 디스크](https://kubernetes.io/docs/concepts/storage/volumes/#gcepersistentdisk)에 사용 되는 라는 저장소 클래스가 하나 뿐입니다. 유일한 경우 이며 기본값 이기도 합니다. 직접 연결 된 Ssd와 함께 사용할 수 있는 GKE에 대 한 [로컬 정적 볼륨 provisioner](https://cloud.google.com/kubernetes-engine/docs/how-to/persistent-volumes/local-ssd#run-local-volume-static-provisioner) 있지만 Google에서 유지 관리 또는 지원 되지 않으므로 사용 하지 않는 것이 좋습니다. [Gke 저장소](https://cloud.google.com/kubernetes-engine/docs/concepts/persistent-volumes)에 대해 자세히 알아보세요.
