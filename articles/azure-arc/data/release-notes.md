---
title: Azure Arc 지원 데이터 서비스 - 릴리스 정보
description: 최신 릴리스 정보
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 06/02/2021
ms.topic: conceptual
ms.openlocfilehash: fc4dd4bcdb18b33fa9c6098c32e0aefecb7c46ee
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111439641"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>릴리스 정보 - Azure Arc 지원 데이터 서비스(미리 보기)

이 문서에서는 Azure Arc 지원 데이터 서비스의 기능, 특징, 최근에 릴리스된 기능 향상 및 개선된 기능을 중점적으로 설명합니다. 

## <a name="may-2021"></a>2021년 5월

이 미리 보기 릴리스는 2021년 6월 2일에 게시됩니다.

미리 보기 기능으로, 이 문서에 제시된 기술에는 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)이 적용됩니다.

### <a name="breaking-change"></a>주요 변경 내용

- Kubernetes 네이티브 배포 템플릿이 수정되었습니다. .yml 템플릿을 업데이트합니다.
    - 데이터 컨트롤러, 부트스트래퍼 및 SQL 관리형 인스턴스의 업데이트된 템플릿: [GitHub microsoft/azure-arc pr 574](https://github.com/microsoft/azure_arc/pull/574)
    - PostgreSQL 하이퍼스케일의 업데이트된 템플릿: [GitHub microsoft/azure-arc pr 574](https://github.com/microsoft/azure_arc/pull/574)

### <a name="whats-new"></a>새로운 기능

#### <a name="platform"></a>플랫폼

- Azure Portal에서 데이터 컨트롤러, SQL 관리형 인스턴스 및 PostgreSQL 하이퍼스케일 서버 그룹을 만들고 삭제합니다. 
- Azure Arc 데이터 서비스를 삭제할 때 포털 작업의 유효성을 검사합니다. 예를 들어 데이터 컨트롤러를 사용하여 배포된 SQL Managed Instance가 있는 경우 포털은 데이터 컨트롤러를 삭제하려고 할 때 경고를 표시합니다.
- Azure Portal을 사용하여 Arc 지원 데이터 컨트롤러를 배포할 때 사용자 지정 설정을 지원하도록 사용자 지정 구성 프로필을 만듭니다.
- 필요에 따라 직접 연결된 모드에서 Azure Log Analytics 작업 영역에 로그를 자동으로 업로드합니다.

####    <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일

이 릴리스에는 다음 기능이 추가되었습니다.

- 데이터 컨트롤러가 직접 연결 모드로 구성된 경우 Azure Portal에서 Azure Arc PostgreSQL 하이퍼스케일을 삭제합니다.
- Azure Portal의 Postgres 배포 페이지에 대한 Azure 데이터베이스에서 Azure Arc 지원 PostgreSQL 하이퍼스케일을 배포합니다. [Azure Database for PostgreSQL 배포 옵션 선택 - Microsoft Azure](https://ms.portal.azure.com/#create/Microsoft.PostgreSQLServer)를 참조하세요.
- Azure Portal에서 Azure Arc 지원 PostgreSQL 하이퍼스케일을 배포할 때 스토리지 클래스 및 Postgres 확장을 지정합니다.
- Azure Arc 지원 PostgreSQL 하이퍼스케일의 작업자 노드 수를 줄입니다. `azdata` 명령줄에서 이 작업을 수행할 수 있습니다(작업자 노드 수를 늘릴 때 스케일 아웃과 반대로 스케일 인이라고 함).

#### <a name="azure-arc-enabled-sql-managed-instance"></a>Azure Arc 지원 SQL Managed Instance

- Arc 지원 SQL Managed Instance에 대한 새 [Azure CLI 확장](/cli/azure/azure-cli-extensions-overview)은 `azdata arc sql mi <command>`와 동일한 명령을 사용합니다. 모든 Arc 지원 SQL Managed Instance 명령은 `az sql mi-arc`에 있습니다. 모든 Arc 관련 `azdata` 명령은 더 이상 사용되지 않으며 이후 릴리스에서 Azure CLI로 이동됩니다.

   확장을 추가하려면 다음을 수행합니다.
  
   ```azurecli
   az extension add --source https://azurearcdatacli.blob.core.windows.net/cli-extensions/arcdata-0.0.1-py2.py3-none-any.whl -y
   az sql mi-arc --help
   ```

- Transact-SQL을 사용하여 장애 조치(failover)를 수동으로 트리거합니다. 다음 명령을 순서대로 실행합니다.

   1. 주 복제본 엔드포인트 연결에서:
   
      ```sql
       ALTER AVAILABILITY GROUP current SET (ROLE = SECONDARY);
      ```

   1. 보조 복제본 엔드포인트 연결에서:
   
      ```sql
      ALTER AVAILABILITY GROUP current SET (ROLE = PRIMARY);
      ```
    
- `COPY_ONLY` 설정을 사용하지 않는 경우 Transact-SQL `BACKUP` 명령이 차단됩니다. 특정 시점 복원 기능을 지원합니다.

### <a name="known-issues"></a>알려진 문제

#### <a name="platform"></a>플랫폼

- Azure Portal을 사용하여 연결된 클러스터에서 데이터 컨트롤러, SQL 관리형 인스턴스 또는 PostgreSQL 하이퍼스케일 서버 그룹을 만들 수 있습니다. 다른 Azure Arc 지원 데이터 서비스 도구를 사용한 배포는 지원되지 않습니다. 특히 이 릴리스 중에 다음 도구를 사용하여 직접 연결 모드에서 데이터 컨트롤러를 배포할 수 없습니다.
   - Azure Data Studio
   - Azure Data CLI(`azdata`)
   - Kubernetes 네이티브 도구(`kubectl`)

   [Azure Arc 데이터 컨트롤러 배포 | 직접 연결 모드](deploy-data-controller-direct-mode.md)에서는 포털에서 데이터 컨트롤러를 만드는 방법을 설명합니다. 

- `kubectl`을 사용하여 Kubernetes 클러스터에서 직접 리소스를 만들 수 있지만 Azure Portal에는 반영되지 않습니다.

- 직접 연결 모드에서는 `azdata arc dc upload`를 사용한 사용량, 메트릭 및 로그의 업로드가 현재 차단되어 있습니다. 사용량은 자동으로 업로드됩니다. 간접 연결 모드에서 만든 데이터 컨트롤러에 대한 업로드는 계속 작동합니다.
- 직접 연결 모드에서 사용량 현황 데이터의 자동 업로드는 `–proxy-cert <path-t-cert-file>`을 통해 프록시를 사용하는 경우 실패합니다.
- Azure Arc 지원 SQL Managed Instance 및 Azure Arc 지원 PostgreSQL 하이퍼스케일은 GB18030 인증되지 않았습니다.
- 현재 kubernetes 클러스터당 직접 연결 모드에서는 Azure Arc 데이터 컨트롤러 하나만 지원됩니다.

#### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일

- 현재 NFS 스토리지에서는 특정 시점 복원이 지원되지 않습니다.
- `pg_cron` 확장을 동시에 사용하도록 설정하고 구성할 수는 없습니다. 이 경우 두 개의 명령을 사용해야 합니다. 사용하도록 설정하는 명령 하나와 구성하는 명령 하나를 사용해야 합니다. 예를 들면 다음과 같습니다.

   1. 확장 사용:
   
      ```console
      azdata arc postgres server edit -n myservergroup --extensions pg_cron 
      ```

   1. 서버 그룹을 다시 시작합니다.

   1. 확장 구성:
   
      ```console
      azdata arc postgres server edit -n myservergroup --engine-settings cron.database_name='postgres'
      ```

   다시 시작이 완료되기 전에 두 번째 명령을 실행하면 명령이 실패합니다. 이 경우 잠시 기다린 후 두 번째 명령을 다시 실행하면 됩니다.

- 추가 확장을 사용하도록 서버 그룹의 구성을 편집할 때 `--extensions` 매개 변수에 잘못된 값을 전달하면 사용되는 확장 목록이 서버 그룹을 만든 시간의 상태로 다시 설정되며 사용자가 추가 확장을 만들 수 없습니다. 이 문제가 발생할 때 사용할 수 있는 유일한 해결 방법은 서버 그룹을 삭제하고 다시 배포하는 것입니다.

## <a name="april-2021"></a>2021년 4월

이 미리 보기 릴리스는 2021년 4월 29일에 게시됩니다.

### <a name="whats-new"></a>새로운 기능

이 섹션에서는 이 릴리스에 대해 도입되거나 사용되는 새로운 기능에 대해 설명합니다. 

#### <a name="platform"></a>플랫폼

- 직접 연결된 클러스터는 원격 분석 정보를 자동으로 Azure에 업로드합니다. 

####    <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일

- Azure Arc 지원 PostgreSQL 하이퍼스케일은 이제 직접 연결 모드에서 지원됩니다. 이제 Azure Portal의 Azure Market Place에서 Azure Arc 지원 PostgreSQL 하이퍼스케일을 배포할 수 있습니다. 
- Azure Arc 지원 PostgreSQL 하이퍼스케일은 칼럼 형식 테이블 스토리지 기능을 제공하는 Citus 10.0 확장과 함께 제공됩니다.
- Azure Arc 지원 PostgreSQL 하이퍼스케일은 이제 전체 사용자/역할 관리를 지원합니다.
- Azure Arc 지원 PostgreSQL 하이퍼스케일은 이제 `Tdigest` 및 `pg_partman`을 사용하여 추가 확장을 지원합니다.
- 이제 Azure Arc 지원 PostgreSQL 하이퍼스케일에서 서버 그룹의 PostgreSQL 인스턴스 역할당 vCore 및 메모리 설정 구성을 지원합니다.
- 이제 Azure Arc 지원 PostgreSQL 하이퍼스케일에서 서버 그룹의 PostgreSQL 인스턴스 역할당 데이터베이스 엔진/서버 설정 구성을 지원합니다.

#### <a name="azure-arc-enabled-sql-managed-instance"></a>Azure Arc 지원 SQL Managed Instance

- 3개의 복제본을 사용하여 SQL Managed Instance로 데이터베이스를 복원하면 데이터베이스가 가용성 그룹에 자동으로 추가됩니다. 
- 3개의 복제본과 함께 배포된 SQL Managed Instance에서 보조 읽기 전용 엔드포인트에 연결합니다. `azdata arc sql endpoint list`를 사용하여 보조 읽기 전용 연결 엔드포인트를 확인합니다.

## <a name="march-2021"></a>2021년 3월

2021년 3월 릴리스는 2021년 4월 5일에 처음 도입되었으며, 최종 릴리스 단계는 2021년 4월 9일에 완료되었습니다.

Azure Data CLI(`azdata`) 버전 번호: 20.3.2. [Azure Data CLI(`azdata`) 설치](/sql/azdata/install/deploy-install-azdata)에서 `azdata`를 설치할 수 있습니다.

### <a name="data-controller"></a>데이터 컨트롤러

- 포털의 직접 연결 모드에서 Azure Arc 지원 데이터 서비스 데이터 컨트롤러를 배포합니다. [데이터 컨트롤러 배포 - 직접 연결 모드 - 필수 구성 요소](deploy-data-controller-direct-mode-prerequisites.md)에서 시작합니다.

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일

PostgreSQL에 대한 CRD(사용자 지정 리소스 정의)가 모두 단일 CRD로 통합되었습니다. 다음 표를 참조하세요.

|Release |CRD |
|-----|-----|
|2021년 2월 이전| postgresql-11s.arcdata.microsoft.com<br/>postgresql-12s.arcdata.microsoft.com |
|2021년 3월부터 | postgresqls.arcdata.microsoft.com

이전 설치를 정리할 때 이전 CRD를 삭제합니다. [이전 설치에서 정리](create-data-controller-using-kubernetes-native-tools.md#cleanup-from-past-installations)를 참조하세요.

### <a name="azure-arc-enabled-sql-managed-instance"></a>Azure Arc 지원 SQL Managed Instance

- 이제 직접 연결 모드의 Azure Portal에서 SQL Managed Instance를 만들 수 있습니다.

- 이제 3개의 복제본을 사용하여 SQL Managed Instance로 데이터베이스를 복원할 수 있습니다. 그러면 데이터베이스가 가용성 그룹에 자동으로 추가됩니다. 

- 이제 3개의 복제본과 함께 배포된 SQL Managed Instance에서 보조 읽기 전용 엔드포인트에 연결할 수 있습니다. `azdata arc sql endpoint list`를 사용하여 보조 읽기 전용 연결 엔드포인트를 확인합니다.

## <a name="february-2021"></a>2021년 2월

### <a name="new-capabilities-and-features"></a>새로운 기능 및 특징

Azure Data CLI(`azdata`) 버전 번호: 20.3.1. [Azure Data CLI(`azdata`) 설치](/sql/azdata/install/deploy-install-azdata)에서 `azdata`를 설치할 수 있습니다.

추가 업데이트는 다음과 같습니다.

- Azure Arc 지원 SQL Managed Instance
   - Always On 가용성 그룹을 사용한 고가용성

- Azure Arc 지원 PostgreSQL 하이퍼스케일 Azure Data Studio: 
   - 개요 페이지에 노드당 항목별로 작성된 서버 그룹의 상태 표시
   - 새 속성 페이지에 서버 그룹에 대한 자세한 정보 표시
   - **노드 매개 변수** 페이지에서 Postgres 엔진 매개 변수 구성

## <a name="january-2021"></a>2021년 1월

### <a name="new-capabilities-and-features"></a>새로운 기능 및 특징

Azure Data CLI(`azdata`) 버전 번호: 20.3.0. [Azure Data CLI(`azdata`) 설치](/sql/azdata/install/deploy-install-azdata)에서 `azdata`를 설치할 수 있습니다.

추가 업데이트는 다음과 같습니다.
- 17가지 새로운 언어에서 사용할 수 있도록 지역화된 포털
- Kube 네이티브 .yaml 파일의 사소한 변경
- Grafana 및 Kibana의 새 버전
- Azure Data Studio의 Notebook에서 azdata를 사용할 때 발생하는 Python 환경 문제가 해결됨
- pg_audit 확장을 PostgreSQL 하이퍼스케일에 사용할 수 있음
- PostgreSQL 하이퍼스케일 데이터베이스의 전체 복원을 수행할 때 백업 ID가 더 이상 필요하지 않음
- 서버 그룹을 구성하는 각 PostgreSQL 인스턴스에 대한 상태(성능 상태)가 보고됨

   이전 릴리스에서는 상태가 서버 그룹 수준에서 집계되었으며 PostgreSQL 노드 수준에서 항목별로 작성되지 않았습니다.

- PostgreSQL 배포가 create 명령에 표시된 볼륨 크기 매개 변수를 적용함
- 서버 그룹을 편집할 때 엔진 버전 매개 변수가 적용됨
- Azure Arc 지원 PostgreSQL 하이퍼스케일에 대한 Pod의 명명 규칙이 변경됨

    이제 `ServergroupName{c, w}-n` 형식으로 표시됩니다. 예를 들어 세 개의 노드(하나의 코디네이터 노드와 두 개의 작업자 노드)가 있는 서버 그룹은 다음과 같이 표시됩니다.
   - `Postgres01c-0`(코디네이터 노드)
   - `Postgres01w-0`(작업자 노드)
   - `Postgres01w-1`(작업자 노드)

## <a name="december-2020"></a>2020년 12월

### <a name="new-capabilities--features"></a>새로운 기능 및 특징

Azure Data CLI(`azdata`) 버전 번호: 20.2.5. [Azure Data CLI(`azdata`) 설치](/sql/azdata/install/deploy-install-azdata)에서 `azdata`를 설치할 수 있습니다.

`azdata arc sql endpoint list` 및 `azdata arc postgres endpoint list` 명령과 함께 Azure Data CLI(`azdata`)를 사용하여 SQL Managed Instance 및 PostgreSQL 하이퍼스케일의 엔드포인트를 확인합니다.

Azure Data Studio를 사용하여 SQL Managed Instance 리소스(CPU 코어 및 메모리) 요청 및 한도를 편집합니다.

Azure Arc 지원 PostgreSQL 하이퍼스케일은 PostgreSQL의 버전 11과 12에 대해 전체 백업 복원뿐만 아니라 특정 시점 복원을 지원합니다. 특정 시점 복원 기능을 사용하여 복원할 특정 날짜 및 시간을 나타낼 수 있습니다.

Azure Arc 지원 PostgreSQL 하이퍼스케일에 대한 Pod의 명명 규칙이 변경되었습니다. 이제 ServergroupName{r, s}-_n_ 형식으로 표시됩니다. 예를 들어 세 개의 노드(하나의 코디네이터 노드와 두 개의 작업자 노드)가 있는 서버 그룹은 다음과 같이 표시됩니다.
- `postgres02r-0`(코디네이터 노드)
- `postgres02s-0`(작업자 노드)
- `postgres02s-1`(작업자 노드)

### <a name="breaking-change"></a>주요 변경 내용

#### <a name="new-resource-provider"></a>새 리소스 공급자

이 릴리스에는 `Microsoft.AzureArcData`라는 업데이트된 [리소스 공급자](../../azure-resource-manager/management/azure-services-resource-providers.md)가 도입됩니다. 이 기능을 사용하려면 먼저 이 리소스 공급자를 등록해야 합니다. 

리소스 공급자를 등록하려면 다음을 수행합니다. 

1. Azure Portal에서 **구독** 을 선택합니다. 
2. 구독 선택
3. **설정** 아래에서 **리소스 공급자** 를 선택합니다. 
4. `Microsoft.AzureArcData`를 검색하고 **등록** 을 선택합니다. 

[Azure 리소스 공급자 및 종류](../../azure-resource-manager/management/resource-providers-and-types.md)에서 자세한 단계를 검토할 수 있습니다. 이렇게 변경하면 Azure Portal에 업로드한 기존 Azure 리소스도 모두 제거됩니다. 리소스 공급자를 사용하려면 데이터 컨트롤러를 업데이트하고 최신 `azdata` CLI를 사용해야 합니다.  

### <a name="platform-release-notes"></a>플랫폼 릴리스 정보

#### <a name="direct-connectivity-mode"></a>직접 연결 모드

이 릴리스에서는 직접 연결 모드가 도입되었습니다. 직접 연결 모드를 사용하면 데이터 컨트롤러에서 사용량 현황 정보를 Azure에 자동으로 업로드할 수 있습니다. 사용량 현황 업로드의 일부로 Arc 데이터 컨트롤러 리소스는 `azdata` 업로드를 통해 이미 만들어지지 않은 경우 포털에서 자동으로 만들어집니다.  

데이터 컨트롤러를 만들 때 직접 연결을 지정할 수 있습니다. 다음 예제에서는 `azdata arc dc create`를 사용하여 직접 연결 모드(`connectivity-mode direct`)를 사용하는 `arc`라는 데이터 컨트롤러를 만듭니다. 예제를 실행하려면 먼저 `<subscription id>`를 구독 ID로 바꿉니다.

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group my-resource-group --location eastus --connectivity-mode direct
```

## <a name="october-2020"></a>2020년 10월 

Azure Data CLI(`azdata`) 버전 번호: 20.2.3. [Azure Data CLI(`azdata`) 설치](/sql/azdata/install/deploy-install-azdata)에서 `azdata`를 설치할 수 있습니다.

### <a name="breaking-changes"></a>주요 변경 내용

이 릴리스에는 다음과 같은 호환성이 손상되는 변경이 포함되었습니다. 

* PostgreSQL CRD(사용자 지정 리소스 정의)에서 `shards`라는 용어의 이름이 `workers`로 바뀌었습니다. 이 용어(`workers`)는 명령줄 매개 변수 이름과 일치합니다.

* `azdata arc postgres server delete`는 postgres 인스턴스를 삭제하기 전에 확인 메시지를 표시합니다.  프롬프트를 건너뛰려면 `--force`를 사용하세요.

### <a name="additional-changes"></a>추가 변경 내용

* 새로운 선택적 매개 변수가 `--volume-claim mounts`라는 `azdata arc postgres server create`에 추가되었습니다. 값은 쉼표로 구분된 볼륨 클레임 탑재 목록입니다. 볼륨 클레임 탑재는 볼륨 유형 및 PVC 이름의 쌍입니다. 현재 지원되는 유일한 볼륨 유형은 `backup`입니다.  PostgreSQL에서 볼륨 유형이 `backup`이면 PVC가 `/mnt/db-backups`에 탑재됩니다.  따라서 PostgresSQL 인스턴스 간에 백업을 공유하여 한 PostgresSQL 인스턴스의 백업을 다른 인스턴스에서 복원할 수 있습니다.

* PostgresSQL 사용자 지정 리소스 정의의 새로운 짧은 이름은 다음과 같습니다. 
  * `pg11` 
  * `pg12`
* 원격 분석 업로드에서는 다음 중 하나를 사용자에게 제공합니다.
   * Azure에 업로드된 지점 수 또는 
   * Azure에 로드된 데이터가 없는 경우 다시 시도하라는 프롬프트
* 또한 `azdata arc dc debug copy-logs`는 `/var/opt/controller/log` 폴더에서 읽고 Linux에서 PostgreSQL 엔진 로그를 수집합니다.
*   PostgreSQL 하이퍼스케일을 사용하여 백업을 만들고 복원하는 동안 작업 표시기를 표시합니다.
* `azdata arc postrgres backup list`에 백업 크기 정보가 포함됩니다.
* SQL Managed Instance 관리자 이름 속성이 Azure Portal에서 개요 블레이드의 오른쪽 열에 추가되었습니다.
* Azure Data Studio는 PostgreSQL 하이퍼스케일에 대한 작업자 노드 수, vCore 및 메모리 설정 구성을 지원합니다. 
* 미리 보기는 Postgres 버전 11 및 12의 백업/복원을 지원합니다.

## <a name="september-2020"></a>2020년 9월

Azure Arc 지원 데이터 서비스는 퍼블릭 미리 보기용으로 릴리스되었습니다. Arc 지원 데이터 서비스를 사용하면 어디서나 데이터 서비스를 관리할 수 있습니다.

- SQL Managed Instance
- PostgreSQL 하이퍼스케일

해당 지침은 [What are Azure Arc 지원 데이터 서비스란?](overview.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

> **작업을 시도해보시겠습니까?**  
> AKS, AWS EKS(Elastic Kubernetes Service), GKE(Google Cloud Kubernetes Engine) 또는 Azure VM에서 [Azure Arc 빠른 시작](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)으로 빠르게 시작합니다.

- [클라이언트 도구 설치](install-client-tools.md)
- [Azure Arc 데이터 컨트롤러 만들기](create-data-controller.md)(먼저 클라이언트 도구를 설치해야 함)
- [Azure Arc에서 Azure SQL 관리형 인스턴스 만들기](create-sql-managed-instance.md)(먼저 Azure Arc 데이터 컨트롤러를 만들어야 함)
- [Azure Arc에서 Azure Database for PostgreSQL 하이퍼스케일 서버 그룹 만들기](create-postgresql-hyperscale-server-group.md)(먼저 Azure Arc 데이터 컨트롤러를 만들어야 함)
- [Azure 서비스의 리소스 공급자](../../azure-resource-manager/management/azure-services-resource-providers.md)
