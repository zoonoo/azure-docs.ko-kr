---
title: AdventureWorks 샘플 데이터베이스를 Azure Arc 사용 PostgreSQL Hyperscale으로 복원
description: AdventureWorks 샘플 데이터베이스를 Azure Arc 사용 PostgreSQL Hyperscale으로 복원
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 2b51c5ca2295671a30fa6c0aee8d313c4c333900
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938964"
---
# <a name="restore-the-adventureworks-sample-database-to-azure-arc-enabled-postgresql-hyperscale"></a>AdventureWorks 샘플 데이터베이스를 Azure Arc 사용 PostgreSQL Hyperscale으로 복원

[AdventureWorks](/sql/samples/adventureworks-install-configure) 는 자습서에서 사용 되는 OLTP 데이터베이스를 포함 하는 예제 데이터베이스와 예제입니다. [SQL Server 샘플 GitHub 리포지토리의](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases)일부로 Microsoft에서 제공 하 고 유지 관리 합니다.

오픈 소스 프로젝트는 AdventureWorks 데이터베이스를 Azure Arc enabled PostgreSQL Hyperscale과 호환 되도록 변환 했습니다.
- [원본 프로젝트](https://github.com/lorint/AdventureWorks-for-Postgres)
- [PostgreSQL와 호환 되도록 CSV 파일을 미리 변환 하는 프로젝트를 따르세요.](https://github.com/NorfolkDataSci/adventure-works-postgres)

이 문서에서는 PostgreSQL Hyperscale 서버 그룹으로 복원 되는 AdventureWorks 샘플 데이터베이스를 가져오는 간단한 프로세스에 대해 설명 합니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="download-the-adventureworks-backup-file"></a>AdventureWorks 백업 파일 다운로드

PostgreSQL Hyperscale 서버 그룹 컨테이너에 AdventureWorks .sql 파일을 다운로드 합니다. 이 예제에서는 명령을 사용 하 여 `kubectl exec` PostgreSQL Hyperscale 서버 그룹 컨테이너에서 원격으로 명령을 실행 하 여 컨테이너에 파일을 다운로드 합니다. 에서 액세스할 수 있는 모든 위치에서이 파일을 다운로드할 수 있습니다 `curl` . PostgreSQL Hyperscale 서버 그룹 컨테이너에서 가져오려는 다른 데이터베이스 백업 파일이 있는 경우이 방법을 사용 합니다. PostgreSQL Hyperscale 서버 그룹 컨테이너에 있는 경우 데이터베이스 및 스키마를 만들고 데이터를 채우는 것이 쉽습니다.

다음과 같은 명령을 실행 하 여 파일을 다운로드 하기 전에 pod 이름 및 네임 스페이스 이름의 값을 바꿉니다.

> [!NOTE]
>  GitHub에서 파일을 다운로드 하려면 컨테이너에서 443 이상의 인터넷 연결이 필요 합니다.

> [!NOTE]
>  Postgres Hyperscale 서버 그룹의 코디네이터 노드에 대 한 pod 이름을 사용 합니다. 이름은 <server group name> -0입니다.  Pod 이름을 잘 모르는 경우 다음 명령을 실행 합니다. `kubectl get pod`

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres  -- /bin/bash -c "cd /tmp && curl -k -O https://raw.githubusercontent.com/microsoft/azure_arc/master/azure_arc_data_jumpstart/aks/arm_template/postgres_hs/AdventureWorks.sql"

#Example:
#kubectl exec postgres02-0 -n arc -c postgres -- /bin/bash -c "cd /tmp && curl -k -O https://raw.githubusercontent.com/microsoft/azure_arc/master/azure_arc_data_jumpstart/aks/arm_template/postgres_hs/AdventureWorks.sql"
```

## <a name="step-2-restore-the-adventureworks-database"></a>2 단계: AdventureWorks 데이터베이스 복원

마찬가지로 kubectl exec 명령을 실행 하 여 PostgreSQL Hyperscale 서버 그룹 컨테이너에 포함 된 psql CLI 도구를 사용 하 여 데이터베이스를 만들고 로드할 수 있습니다.

다음과 같은 명령을 실행 하 여 실행 하기 전에 pod 이름 및 네임 스페이스 이름의 값을 처음으로 대체 하는 빈 데이터베이스를 만듭니다.

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres -- psql --username postgres -c 'CREATE DATABASE "adventureworks";'

#Example
#kubectl exec postgres02-0 -n arc -c postgres -- psql --username postgres -c 'CREATE DATABASE "adventureworks";'
```

그런 다음이 명령을 실행 하 여 실행 하기 전에 pod 이름 및 네임 스페이스 이름의 값을 대체 하는 데이터베이스를 복원 합니다.

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres -- psql --username postgres -d adventureworks -f /tmp/AdventureWorks.sql

#Example
#kubectl exec postgres02-0 -n arc -c postgres -- psql --username postgres -d adventureworks -f /tmp/AdventureWorks.sql
```


> **참고: 규모를 확장 하 고 PostgreSQL Hyperscale 서버 그룹의 작업자 노드에 데이터/테이블을 분할/분산 시킬 때까지 Azure Arc에서 실행 하는 성능 이점이 크게 PostgreSQL. [제안 된 다음 단계](#suggested-next-steps)를 참조 하세요.**

## <a name="suggested-next-steps"></a>제안 된 다음 단계
- Azure Database for PostgreSQL Hyperscale의 개념 및 방법 가이드를 읽고 여러 PostgreSQL Hyperscale 노드에 데이터를 분산 하 고 Azure Database for PostgreSQL 하이퍼 규모의 모든 기능을 활용 하세요. :
    * [노드 및 테이블](../../postgresql/concepts-hyperscale-nodes.md)
    * [애플리케이션 유형 확인](../../postgresql/concepts-hyperscale-app-type.md)
    * [배포 열 선택](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [테이블 공동 배치](../../postgresql/concepts-hyperscale-colocation.md)
    * [테이블 분산 및 수정](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [다중 테 넌 트 데이터베이스 디자인](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [실시간 분석 대시보드 디자인](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

   > \* 위의 문서에서 **Azure Portal에 로그인**섹션을 건너뛰고 **Azure Database for PostgreSQL-Hyperscale (Citus) & 만듭니다**. Azure Arc 배포의 나머지 단계를 구현 합니다. 이러한 섹션은 Azure 클라우드에서 PaaS 서비스로 제공 되는 Citus (Azure Database for PostgreSQL Hyperscale)에 고유 하지만, 문서의 다른 부분은 Azure Arc enabled PostgreSQL Hyperscale에 직접 적용할 수 있습니다.

- [Azure Database for PostgreSQL 하이퍼 확장 서버 그룹 확장](scale-out-postgresql-hyperscale-server-group.md)
