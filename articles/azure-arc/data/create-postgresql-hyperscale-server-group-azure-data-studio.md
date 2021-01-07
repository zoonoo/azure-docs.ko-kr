---
title: Azure Data Studio를 사용 하 여 Azure Arc enabled PostgreSQL Hyperscale 만들기
description: Azure Data Studio를 사용 하 여 Azure Arc enabled PostgreSQL Hyperscale 만들기
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e2007d8f0c558d35c0507b6e12bce6d6777fad52
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92310908"
---
# <a name="create-azure-arc-enabled-postgresql-hyperscale-using-azure-data-studio"></a>Azure Data Studio를 사용 하 여 Azure Arc enabled PostgreSQL Hyperscale 만들기

이 문서에서는 Azure Data Studio를 사용 하 여 Azure Arc 사용 PostgreSQL Hyperscale 서버 그룹을 프로 비전 하는 단계를 안내 합니다.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connect-to-the-azure-arc-data-controller"></a>Azure Arc 데이터 컨트롤러에 연결

인스턴스를 만들기 전에 아직 로그인 하지 않은 경우 Azure Arc 데이터 컨트롤러에 로그인 합니다.

```console
azdata login
```

그런 다음 데이터 컨트롤러를 만든 네임 스페이스, 사용자 이름 및 컨트롤러에 로그인 하는 데 사용할 암호를 묻는 메시지가 표시 됩니다.

> 네임 스페이스의 유효성을 검사 해야 하는 경우를 실행 ```kubectl get pods -A``` 하 여 클러스터에 있는 모든 네임 스페이스의 목록을 가져올 수 있습니다.

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>OpenShift 사용자에 대 한 임시 및 임시 단계

다음 단계로 이동 하기 전에이 단계를 구현 합니다. PostgreSQL Hyperscale 서버 그룹을 기본값이 아닌 프로젝트의 Red Hat OpenShift에 배포 하려면 클러스터에 대해 다음 명령을 실행 하 여 보안 제약 조건을 업데이트 해야 합니다. 이 명령은 PostgreSQL Hyperscale 서버 그룹을 실행 하는 서비스 계정에 필요한 권한을 부여 합니다. SCC (보안 컨텍스트 제약 조건) **_arc-데이터-scc_** 는 Azure arc 데이터 컨트롤러를 배포할 때 추가한 것입니다.

```console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

_**서버 그룹 이름** 은 다음 단계에서 배포 하는 서버 그룹의 이름입니다._
   
OpenShift의 SCCs에 대 한 자세한 내용은 [openshift 설명서](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html)를 참조 하세요.
이제 다음 단계를 구현할 수 있습니다.

## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 만들기

1. 시작 Azure Data Studio
1. 연결 탭에서 왼쪽 위에 있는 세 개의 점을 클릭 하 고 "새 배포"를 선택 합니다.
1. 배포 옵션에서 **PostgreSQL Hyperscale 서버 그룹-Azure Arc** 를 선택 합니다.
    >[!NOTE]
    > 현재 설치 되어 있지 않으면 여기를 설치할지 묻는 메시지가 표시 될 수 있습니다 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] .
1. 개인 정보 및 사용 조건에 동의 하 고 맨 아래에서 **선택** 을 클릭 합니다.
1. PostgreSQL Hyperscale 서버 그룹 배포-Azure Arc 블레이드에서 다음 정보를 입력 합니다.
   - 서버 그룹의 이름을 입력 하십시오.
   - 서버 그룹의 _postgres_ administrator 사용자에 대 한 암호를 입력 하 고 확인 합니다.
   - 데이터에 적절 한 저장소 클래스를 선택 합니다.
   - 로그에 적절 한 저장소 클래스를 선택 합니다.
   - 백업에 적절 한 저장소 클래스를 선택 합니다.
   - 프로 비전 할 작업자 노드 수를 선택 합니다.
1. **배포** 단추를 클릭 합니다.

이렇게 하면 데이터 컨트롤러에서 Azure Arc enabled PostgreSQL Hyperscale 서버 그룹이 생성 되기 시작 합니다.

몇 분 후에 만들기를 성공적으로 완료 해야 합니다.

## <a name="next-steps"></a>다음 단계
- [Azure Data Studio를 사용 하 여 서버 그룹 관리](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md)
- [서버 그룹 모니터링](monitor-grafana-kibana.md)
- Azure Database for PostgreSQL Hyperscale의 개념 및 방법 가이드를 읽고 여러 PostgreSQL Hyperscale 노드에 데이터를 분산 하 고 Postgres Hyperscale 용 Azure Database의 모든 기능을 활용 하세요. :
    * [노드 및 테이블](../../postgresql/concepts-hyperscale-nodes.md)
    * [애플리케이션 유형 확인](../../postgresql/concepts-hyperscale-app-type.md)
    * [배포 열 선택](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [테이블 공동 배치](../../postgresql/concepts-hyperscale-colocation.md)
    * [테이블 분산 및 수정](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [다중 테 넌 트 데이터베이스 디자인](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [실시간 분석 대시보드 디자인](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* 위의 문서에서 **Azure Portal에 로그인**섹션을 건너뛰고 **Azure Database for PostgreSQL-Hyperscale (Citus) & 만듭니다**. Azure Arc 배포의 나머지 단계를 구현 합니다. 이러한 섹션은 Azure 클라우드에서 PaaS 서비스로 제공 되는 Citus (Azure Database for PostgreSQL Hyperscale)에 고유 하지만, 문서의 다른 부분은 Azure Arc enabled PostgreSQL Hyperscale에 직접 적용할 수 있습니다.

- [Azure Database for PostgreSQL 하이퍼스케일 서버 그룹 스케일 아웃](scale-out-postgresql-hyperscale-server-group.md)
- [저장소 구성 및 Kubernetes 저장소 개념](storage-configuration.md)
- [Kubernetes 리소스 모델](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

