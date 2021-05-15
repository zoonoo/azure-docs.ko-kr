---
title: Azure Data Studio를 사용하여 Azure Arc 지원 PostgreSQL 하이퍼스케일 만들기
description: Azure Data Studio를 사용하여 Azure Arc 지원 PostgreSQL 하이퍼스케일 만들기
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e2007d8f0c558d35c0507b6e12bce6d6777fad52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92310908"
---
# <a name="create-azure-arc-enabled-postgresql-hyperscale-using-azure-data-studio"></a>Azure Data Studio를 사용하여 Azure Arc 지원 PostgreSQL 하이퍼스케일 만들기

이 문서에서는 Azure Data Studio를 사용하여 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 프로비저닝하는 단계를 안내합니다.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connect-to-the-azure-arc-data-controller"></a>Azure Arc 데이터 컨트롤러에 연결

인스턴스를 만들기 전에 아직 로그인하지 않은 경우 Azure Arc 데이터 컨트롤러에 로그인합니다.

```console
azdata login
```

그러면 컨트롤러에 로그인하기 위해 데이터 컨트롤러를 만든 네임스페이스, 사용자 이름, 암호를 입력하라는 프롬프트가 표시됩니다.

> 네임스페이스의 유효성을 검사해야 하는 경우 ```kubectl get pods -A```를 실행하여 클러스터에 있는 모든 네임스페이스의 목록을 가져올 수 있습니다.

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>OpenShift 사용자만을 위한 임시 예비 단계

다음 단계로 이동하기 전에 이 단계를 구현합니다. PostgreSQL 하이퍼스케일 서버 그룹을 기본 프로젝트가 아닌 프로젝트의 Red Hat OpenShift에 배포하려면 클러스터에 대해 다음 명령을 실행하여 보안 제약 조건을 업데이트해야 합니다. 이 명령은 PostgreSQL 하이퍼스케일 서버 그룹을 실행하는 서비스 계정에 필요한 권한을 부여합니다. SCC(보안 컨텍스트 제약 조건) **_arc-data-scc_** 은 Azure Arc 데이터 컨트롤러를 배포할 때 추가한 제약 조건입니다.

```console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

_**Server-group-name** 은 다음 단계에서 배포할 서버 그룹의 이름입니다._
   
OpenShift의 SCC에 대한 자세한 내용은 [OpenShift 설명서](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html)를 참조하세요.
이제 다음 단계를 구현할 수 있습니다.

## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 만들기

1. Azure Data Studio 시작
1. 연결 탭에서 왼쪽 위에 있는 점 세 개를 클릭하고 “새 배포”를 선택합니다.
1. 배포 옵션에서 **PostgreSQL 하이퍼스케일 서버 그룹 - Azure Arc** 를 선택합니다.
    >[!NOTE]
    > 현재 설치되어 있지 않으면 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]를 설치할지 묻는 프롬프트가 표시될 수 있습니다.
1. 개인정보취급방침 및 사용 조건에 동의하고 맨 아래에서 **선택** 을 클릭합니다.
1. PostgreSQL 하이퍼스케일 서버 그룹 배포 - Azure Arc 블레이드에서 다음 정보를 입력합니다.
   - 서버 그룹의 이름을 입력합니다.
   - 서버 그룹의 _postgres_ 관리자 사용자에 대한 암호를 입력하고 확인합니다.
   - 데이터에 적절한 스토리지 클래스 선택
   - 로그에 적절한 스토리지 클래스 선택
   - 백업에 적절한 스토리지 클래스 선택
   - 프로비저닝할 작업자 노드 수 선택
1. **배포** 단추를 클릭합니다.

이렇게 하면 데이터 컨트롤러에서 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 생성이 시작됩니다.

몇 분 후에 생성이 완료됩니다.

## <a name="next-steps"></a>다음 단계
- [Azure Data Studio를 사용하여 서버 그룹 관리](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md)
- [서버 그룹 모니터링](monitor-grafana-kibana.md)
- PostgreSQL 하이퍼스케일용 Azure Database의 개념 및 방법 가이드를 읽고, 여러 PostgreSQL 하이퍼스케일 노드에 데이터를 분산하고, Postgres 하이퍼스케일용 Azure Database의 모든 기능을 활용해 보세요. :
    * [노드 및 테이블](../../postgresql/concepts-hyperscale-nodes.md)
    * [애플리케이션 유형 확인](../../postgresql/concepts-hyperscale-app-type.md)
    * [배포 열 선택](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [테이블 공동 배치](../../postgresql/concepts-hyperscale-colocation.md)
    * [테이블 분산 및 수정](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [다중 테넌트 데이터베이스 설계](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [실시간 분석 대시보드 설계](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* 위 문서에서는 **Azure Portal에 로그인** 과 **Azure Database for PostgreSQL 만들기 – 하이퍼스케일(Citus)** 섹션을 건너뛰었습니다. Azure Arc 배포의 나머지 단계를 구현합니다. 이러한 섹션은 Azure 클라우드에서 PaaS 서비스로 제공되는 Azure Database용 PostgreSQL 하이퍼스케일(Citus)에 한정되지만, 문서의 다른 부분은 Azure Arc 지원 PostgreSQL 하이퍼스케일에 직접 적용할 수 있습니다.

- [Azure Database for PostgreSQL 하이퍼스케일 서버 그룹 스케일 아웃](scale-out-postgresql-hyperscale-server-group.md)
- [스토리지 구성 및 Kubernetes 스토리지 개념](storage-configuration.md)
- [Kubernetes 리소스 모델](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

