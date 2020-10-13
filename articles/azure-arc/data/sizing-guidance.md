---
title: 크기 조정 지침
description: Azure Arc 사용 데이터 서비스 배포의 크기를 계획 합니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3bd54d8a23aca7e493cd3c0ddb7f057a6e1f5362
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761484"
---
# <a name="sizing-guidance"></a>크기 조정 지침

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="overview-of-sizing-guidance"></a>크기 조정 지침 개요

Azure Arc 데이터 서비스 배포를 계획할 때 Azure Arc 데이터 컨트롤러를 실행 하는 데 필요한 계산, 메모리 및 저장소의 올바른 크기와 배포할 SQL 관리 되는 인스턴스 및 PostgreSQL Hyperscale 서버 그룹의 수를 계획 해야 합니다.  Azure Arc enabled data services는 Kubernetes에 배포 되므로 시간이 지남에 따라 추가 계산 노드나 저장소를 추가 하 여 Kubernetes 클러스터에 용량을 더 추가할 수 있습니다.  이 가이드는 최소 요구 사항에 대 한 지침을 제공 하 고 몇 가지 일반적인 요구 사항에 권장 되는 크기에 대 한 지침을 제공 합니다

## <a name="general-sizing-requirements"></a>일반 크기 조정 요구 사항

> [!NOTE]
> 이 문서의 개념을 잘 모르는 경우 [Kubernetes 리소스 관리](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) 및 [Kubernetes 크기 표기법](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes)에 대해 자세히 알아볼 수 있습니다.

코어 수는 1 보다 크거나 같은 정수 값 이어야 합니다.

배포에 azdata를 사용 하는 경우 메모리 값은 두 숫자의 거듭제곱으로 지정 해야 합니다. 즉, 접미사: Ki, Mi 또는 Gi를 사용 합니다.

제한 값은 지정 된 경우 항상 요청 값 보다 커야 합니다.

코어에 대 한 제한 값은 SQL 관리 되는 인스턴스 및 PostgreSQL Hyperscale 서버 그룹에 대 한 청구 가능 메트릭입니다.

## <a name="minimum-deployment-requirements"></a>최소 배포 요구 사항

최소 크기의 Azure Arc 사용 데이터 서비스 배포는 Azure Arc 데이터 컨트롤러와 하나의 SQL 관리 되는 인스턴스 및 두 개의 작업자 노드를 포함 하는 하나의 PostgreSQL Hyperscale server 그룹으로 간주할 수 있습니다.  이 구성의 경우 Kubernetes 클러스터에서 _사용 가능한_ 용량의 16gb RAM 및 코어 4 개 이상이 필요 합니다.  최소 Kubernetes 노드 크기는 8gb RAM, 4 코어, 모든 Kubernetes 노드에서 사용 가능한 16gb RAM의 총 용량을 확인 해야 합니다.  예를 들어 1 개의 노드가 32 g b RAM 및 4 코어에 있을 수 있으며, 각각 16GB RAM 및 4 개 코어를 포함 하는 2 개의 노드가 있을 수 있습니다.

저장소 크기 조정에 대 한 자세한 내용은 [저장소 구성](storage-configuration.md) 문서를 참조 하세요.

## <a name="data-controller-sizing-details"></a>데이터 컨트롤러 크기 조정 세부 정보

데이터 컨트롤러는 API, 컨트롤러 서비스, 부트스트래퍼, 모니터링 데이터베이스 및 대시보드를 제공 하기 위해 Kubernetes 클러스터에 배포 되는 pod의 컬렉션입니다.  다음 표에서는 메모리 및 CPU 요청 및 제한에 대 한 기본값을 설명 합니다.

|Pod 이름|CPU 요청|메모리 요청|CPU 제한|메모리 제한|참고|
|---|---|---|---|---|---|
|**부트스트래퍼**|100m|100Mi|200m|200Mi||
|**control**|400m|2Gi|18m|2Gi||
|**controldb**|200m|3Gi|800m|6Gi||
|**controlwd**|10m|100Mi|100m|200Mi||
|**logsdb**|200m|1600Mi|2|1600Mi||
|**logsui**|100m|500Mi|2|2Gi||
|**metricsdb**|200m|800Mi|400m|2Gi||
|**metricsdc**|100m|200Mi|200m|300Mi|Metricsdc는 클러스터의 각 Kubernetes 노드에 생성 되는 daemonset입니다.  여기에 있는 테이블의 숫자는 _노드당_입니다. 데이터 컨트롤러를 만들기 전에 배포 프로필 파일에서 allowNodeMetricsCollection = false를 설정 하는 경우 metricsdc daemonset가 생성 되지 않습니다.|
|**metricsui**|20m|200Mi|500m|200Mi||
|**mgmtproxy**|200m|250Mi|500m|500Mi||

배포 프로필 파일 또는 datacontroller YAML 파일에서 controldb 및 control pod에 대 한 기본 설정을 재정의할 수 있습니다.  예제:

```yaml
  resources:
    controller:
      limits:
        cpu: "1000m"
        memory: "3Gi"
      requests:
        cpu: "800m"
        memory: "2Gi"
    controllerDb:
      limits:
        cpu: "800m"
        memory: "8Gi"
      requests:
        cpu: "200m"
        memory: "4Gi"
```

저장소 크기 조정에 대 한 자세한 내용은 [저장소 구성](storage-configuration.md) 문서를 참조 하세요.

## <a name="sql-managed-instance-sizing-details"></a>SQL 관리 되는 인스턴스 크기 조정 정보

각 SQL 관리 되는 인스턴스에는 다음과 같은 최소 리소스 요청이 있어야 합니다.
- 메모리: 2Gi
- 코어: 1

생성 된 각 SQL 관리 되는 인스턴스 pod에는 세 개의 컨테이너가 있습니다.
|컨테이너 이름|CPU 요청|메모리 요청|CPU 제한|메모리 제한|참고|
|---|---|---|---|---|---|
|fluentbit|100m|100Mi|지정되지 않음|지정되지 않음|Fluentbit container 리소스 요청은 SQL 관리 되는 인스턴스에 대해 지정 된 요청 _에 추가_ 됩니다.||
|호-sqlmi|사용자가 지정 되었거나 지정 되지 않았습니다.|사용자가 지정 되었거나 지정 되지 않았습니다.|사용자가 지정 되었거나 지정 되지 않았습니다.|사용자가 지정 되었거나 지정 되지 않았습니다.||
|collectd|지정되지 않음|지정되지 않음|지정되지 않음|지정되지 않음||

모든 영구 볼륨의 기본 볼륨 크기는 5Gi입니다.

## <a name="postgresql-hyperscale-server-group-sizing-details"></a>PostgreSQL Hyperscale 서버 그룹 크기 조정 세부 정보

각 PostgreSQL Hyperscale 서버 그룹 노드에는 다음과 같은 최소 리소스 요청이 있어야 합니다.
- 메모리: 256Mi
- 코어: 1

만든 각 PostgreSQL Hyperscale server 그룹 코디네이터 또는 작업자 pod에는 세 개의 컨테이너가 있습니다.
|컨테이너 이름|CPU 요청|메모리 요청|CPU 제한|메모리 제한|참고|
|---|---|---|---|---|---|
|fluentbit|100m|100Mi|지정되지 않음|지정되지 않음|Fluentbit container resource 요청은 PostgreSQL Hyperscale 서버 그룹 노드에 대해 지정 된 요청에 _추가_ 됩니다.|
|postgres|사용자가 지정 되었거나 지정 되지 않았습니다.|사용자 지정 또는 256Mi (기본값).|사용자가 지정 되었거나 지정 되지 않았습니다.|사용자가 지정 되었거나 지정 되지 않았습니다.||
|telegraf|지정되지 않음|지정되지 않음|지정되지 않음|지정되지 않음||

## <a name="cumulative-sizing"></a>누적 크기 조정

Azure Arc 사용 데이터 서비스에 필요한 환경의 전체 크기는 주로 생성 되는 데이터베이스 인스턴스의 수와 크기에 대 한 함수입니다.  전체 크기를 미리 예측 하 여 인스턴스 수가 증가 하 고 축소 되 고 각 데이터베이스 인스턴스에 필요한 리소스의 양이 변경 되는 것을 알 수 있습니다.

지정 된 Azure Arc 사용 데이터 서비스 환경의 기본 크기는 4 개 코어와 16gb RAM이 필요한 데이터 컨트롤러의 크기입니다.  여기에서 데이터베이스 인스턴스에 필요한 코어 및 메모리의 누적 합계를 상위에 추가할 수 있습니다.  SQL 관리 되는 인스턴스의 pod 수는 생성 된 SQL 관리 되는 인스턴스의 수와 같습니다.  PostgreSQL Hyperscale 서버 그룹의 경우 pod 수는 작업자 노드 수에 코디네이터 노드에 대 한 하나를 더한 값과 같습니다.  예를 들어 작업자 노드가 3 PostgreSQL 서버 그룹이 있는 경우 총 pod 수는 4입니다.

각 데이터베이스 인스턴스에 대해 요청 하는 코어 및 메모리 외에도 250m의 코어와 에이전트 컨테이너의 250M를 추가 해야 합니다.

다음은 크기 조정 계산의 예입니다.

요구 사항:

- **"SQL1"**: 1 개의 SQL 관리 되는 인스턴스 (16gb RAM, 4 개 코어)
- **"SQL2"**: 1 개의 SQL 관리 되는 인스턴스 (256 g b RAM, 16 개 코어)
- **"Postgres1"**: 1 PostgreSQL hyperscale server 그룹, RAM이 12 GB, 4 개 코어

크기 조정 계산:

- "SQL1"의 크기는 pod = 16.25 Gi RAM, 4.25 코어 당 에이전트의 경우 1 pod * ([16 Gi RAM, 4 코어] + [250Mi RAM, 250mi 코어])입니다.
- "SQL2" 크기는 pod = 256.25 Gi RAM, 16.25 코어 당 에이전트의 경우 1 pod * ([256 Gi RAM, 16 코어] + + [250Mi, 250mi 코어])입니다.
- SQL 1 및 SQL 2의 총 크기는 (16.25 g b + 256.25 Gi) = 272.5 g b RAM, (4.25 core + 16.25 코어) = 20.5 코어입니다.

- "Postgres1"의 크기는 (4 작업자 pod + 1 코디네이터 pod) * ([12gb RAM, 4 개 코어] + [250Mi, 250mi 코어]) (pod = 61.25 g b RAM, 21.25 코어)입니다.

- 데이터베이스 인스턴스에 필요한 총 용량은 272.5 g b RAM, SQL + 61.25 GB RAM의 경우 20.5 코어, PostgreSQL Hyperscale의 21.25 core = 333.75 GB RAM, 42.5 코어입니다.

- 데이터베이스 인스턴스와 데이터 컨트롤러에 필요한 총 용량은 333.75 g b RAM, 데이터베이스 인스턴스에 대 한 42.5 코어 + 16gb RAM, 데이터 컨트롤러용 코어 4 개 = 349.75 g b RAM, 46.5 코어입니다.

저장소 크기 조정에 대 한 자세한 내용은 [저장소 구성](storage-configuration.md) 문서를 참조 하세요.

## <a name="other-considerations"></a>기타 고려 사항

코어 또는 RAM에 대 한 지정 된 데이터베이스 인스턴스 크기 요청은 클러스터에서 사용 가능한 Kubernetes 노드의 용량을 초과할 수 없습니다.  예를 들어 Kubernetes 클러스터에 있는 가장 큰 Kubernetes 노드가 256 GB의 RAM과 24 코어 인 경우 512 GB의 RAM 및 48 코어 요청을 사용 하 여 데이터베이스 인스턴스를 만들 수 없습니다.  

Kubernetes에서 pod 생성을 효율적으로 예약 하 고 수요를 탄력적으로 확장 하 고 장기적으로 늘릴 수 있도록 Kubernetes 노드에서 사용 가능한 용량의 25% 이상을 유지 하는 것이 좋습니다.  

크기 조정 계산에서 Kubernetes system pod의 리소스 요구 사항 및 동일한 Kubernetes 클러스터에서 Azure Arc 사용 가능 데이터 서비스와 용량을 공유할 수 있는 다른 워크 로드를 반드시 추가 해야 합니다.

계획 된 유지 관리 및 재해 연속성 동안 고가용성을 유지 하기 위해 지정 된 시점에 사용할 수 없는 클러스터의 Kubernetes 노드 중 하나 이상을 계획 해야 합니다.  Kubernetes는 유지 관리를 위해 또는 실패로 인해 수행 된 지정 된 노드에서 실행 중이 던 pod의 일정을 다시 조정 하려고 합니다.  남은 노드에 사용 가능한 용량이 없는 경우 해당 pod는 사용 가능한 용량이 다시 생길 때까지 생성을 위해 다시 예약 되지 않습니다.  대형 데이터베이스 인스턴스에 대 한 추가 주의를 기울여야 합니다.  예를 들어 큰 데이터베이스 인스턴스의 리소스 요구 사항을 충족 하기에 충분 한 Kubernetes 노드가 크고 해당 노드가 실패 하는 경우 Kubernetes는 데이터베이스 인스턴스 pod를 다른 Kubernetes 노드로 예약할 수 없습니다.

[Kubernetes 클러스터 크기에 대 한 최대 제한을](https://kubernetes.io/docs/setup/best-practices/cluster-large/) 염두에 두십시오.

Kubernetes 관리자가 네임 스페이스/프로젝트에서 [리소스 할당량](https://kubernetes.io/docs/concepts/policy/resource-quotas/) 을 설정 했을 수 있습니다.  데이터베이스 인스턴스 크기를 계획할 때는이 점을 염두에 두어야 합니다.
