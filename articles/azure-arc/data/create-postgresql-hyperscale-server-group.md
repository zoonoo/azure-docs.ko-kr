---
title: Azure Arc에서 Azure Database for PostgreSQL Hyperscale server 그룹 만들기
description: Azure Arc에서 Azure Database for PostgreSQL Hyperscale server 그룹 만들기
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7fdc367e3db298b60dc9a15453d58a738c13274a
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108306"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 만들기

이 문서에서는 Azure Arc에서 PostgreSQL Hyperscale 서버 그룹을 만드는 단계를 설명 합니다.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="getting-started"></a>시작
아래 항목을 이미 잘 알고 있는 경우이 단락을 건너뛸 수 있습니다.
만들기를 진행 하기 전에 다음과 같은 중요 한 항목을 읽어 볼 수 있습니다.
- [Azure Arc 사용 데이터 서비스 개요](overview.md)
- [연결 모드 및 요구 사항](connectivity.md)
- [저장소 구성 및 Kubernetes 저장소 개념](storage-configuration.md)
- [Kubernetes 리소스 모델](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

전체 환경을 직접 프로 비전 하지 않고 작업을 수행 하려는 경우 azure Kubernetes 서비스 (AKS), AWS 탄력적 Kubernetes 서비스 (EKS), Google Cloud Kubernetes Engine (GKE) 또는 Azure VM에서 [Azure Arc](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) 빠른 시작을 사용 하 여 빠르게 시작 하세요.


## <a name="login-to-the-azure-arc-data-controller"></a>Azure Arc 데이터 컨트롤러에 로그인 합니다.

인스턴스를 만들려면 먼저 Azure Arc 데이터 컨트롤러에 로그인 해야 합니다. 데이터 컨트롤러에 이미 로그인 한 경우이 단계를 건너뛸 수 있습니다.

```console
azdata login
```

그러면 사용자 이름, 암호 및 시스템 네임 스페이스를 묻는 메시지가 표시 됩니다.  

> 스크립트를 사용 하 여 데이터 컨트롤러를 만든 경우 네임 스페이스는 **arc** 여야 합니다.

```console
Namespace: arc
Username: arcadmin
Password:
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>OpenShift 사용자에 대 한 임시 및 임시 단계

다음 단계로 이동 하기 전에이 단계를 구현 합니다. PostgreSQL Hyperscale 서버 그룹을 기본값이 아닌 프로젝트의 Red Hat OpenShift에 배포 하려면 클러스터에 대해 다음 명령을 실행 하 여 보안 제약 조건을 업데이트 해야 합니다. 이 명령은 PostgreSQL Hyperscale 서버 그룹을 실행 하는 서비스 계정에 필요한 권한을 부여 합니다. SCC (보안 컨텍스트 제약 조건) **_arc-데이터-scc_** 는 Azure arc 데이터 컨트롤러를 배포할 때 추가한 것입니다.

```console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

_**서버 그룹 이름** 은 다음 단계에서 만들 서버 그룹의 이름입니다._
   
OpenShift의 SCCs에 대 한 자세한 내용은 [openshift 설명서](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html)를 참조 하세요.
이제 다음 단계를 구현할 수 있습니다.

## <a name="create-an-azure-database-for-postgresql-hyperscale-server-group"></a>Azure Database for PostgreSQL Hyperscale server 그룹 만들기

Azure Arc에서 Azure Database for PostgreSQL Hyperscale 서버 그룹을 만들려면 다음 명령을 사용 합니다.

```console
azdata arc postgres server create -n <name> --workers <# worker nodes with #>=2> --storage-class-data <storage class name> --storage-class-logs <storage class name> --storage-class-backups <storage class name>

#Example
#azdata arc postgres server create -n postgres01 --workers 2
```

> [!NOTE]
> - **사용할 수 있는 다른 명령줄 매개 변수가 있습니다.  을 실행 하 여 옵션의 전체 목록을 참조 하세요 `azdata arc postgres server create --help` .**
> - 백업에 사용 되는 저장소 클래스 (_--저장소-클래스-scb_)는 지정 되지 않은 경우 기본적으로 데이터 컨트롤러의 데이터 저장소 클래스를 사용 합니다.
> - --Volume size-* 매개 변수에서 허용 하는 단위는 Kubernetes 리소스 수량 (이러한 SI 접미사 (T, G, M, K, M) 또는 그에 해당 하는 두 가지 기능 (Ti, Gi, Mi, Ki))입니다.
> - 이름 길이는 12 자이 하 여야 하 고 DNS 명명 규칙을 따라야 합니다.
> - _Postgres_ standard 관리 사용자의 암호를 입력 하 라는 메시지가 표시 됩니다.  `AZDATA_PASSWORD`Create 명령을 실행 하기 전에 세션 환경 변수를 설정 하 여 대화형 프롬프트를 건너뛸 수 있습니다.
> - AZDATA_USERNAME를 사용 하 여 데이터 컨트롤러를 배포 하 고 동일한 터미널 세션에서 세션 환경 변수를 AZDATA_PASSWORD 하는 경우 AZDATA_PASSWORD의 값이 PostgreSQL Hyperscale 서버 그룹을 배포 하는 데 사용 됩니다. 다른 암호를 사용 하려면 (1) AZDATA_PASSWORD에 대 한 값을 업데이트 하거나 (2) AZDATA_PASSWORD 환경 변수를 삭제 하거나 해당 값을 삭제 하 여 서버 그룹을 만들 때 암호를 대화형으로 입력 하 라는 메시지를 표시 합니다.
> - PostgreSQL Hyperscale 데이터베이스 엔진의 기본 관리자 사용자 이름은 _postgres_ 이며이 시점에서 변경할 수 없습니다.
> - PostgreSQL Hyperscale 서버 그룹을 만들면 Azure에 리소스가 즉시 등록 되지 않습니다. [리소스 인벤토리](upload-metrics-and-logs-to-azure-monitor.md) 또는 [사용 현황 데이터](view-billing-data-in-azure.md) 를 azure에 업로드 하는 프로세스의 일부로 azure에서 리소스가 생성 되 고 Azure Portal에서 리소스를 볼 수 있게 됩니다.



## <a name="list-your-azure-database-for-postgresql-server-groups-created-in-your-arc-setup"></a>Arc 설정에서 만든 Azure Database for PostgreSQL 서버 그룹 나열

Azure Arc에서 PostgreSQL Hyperscale 서버 그룹을 보려면 다음 명령을 사용 합니다.

```console
azdata arc postgres server list
```


```output
Name        State     Workers
----------  --------  ---------
postgres01  Ready     2
```

## <a name="get-the-endpoints-to-connect-to-your-azure-database-for-postgresql-server-groups"></a>Azure Database for PostgreSQL 서버 그룹에 연결 하기 위한 끝점 가져오기

PostgreSQL 인스턴스에 대 한 끝점을 보려면 다음 명령을 실행 합니다.

```console
azdata arc postgres endpoint list -n <server group name>
```
예를 들면 다음과 같습니다.
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

PostgreSQL 인스턴스 끝점을 사용 하 여  [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio), [pgcli](https://www.pgcli.com/) Psql, pgcli 등 즐겨 사용 하는 도구에서 PostgreSQL hyperscale 서버 그룹에 연결할 수 있습니다.

Azure VM을 사용 하 여 테스트 하는 경우 아래 지침을 따르세요.

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Azure 가상 머신 배포에 대 한 특수 정보

Azure 가상 컴퓨터를 사용 하는 경우 끝점 IP 주소는 _공용_ ip 주소를 표시 하지 않습니다. 공용 IP 주소를 찾으려면 다음 명령을 사용 합니다.

```console
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

그런 다음 공용 IP 주소를 포트와 결합 하 여 연결을 만들 수 있습니다.

NSG (네트워크 보안 게이트웨이)를 통해 PostgreSQL Hyperscale 서버 그룹의 포트를 노출 해야 할 수도 있습니다. NSG ()를 통한 트래픽을 허용 하려면 다음 명령을 사용 하 여 수행할 수 있는 규칙을 추가 해야 합니다.

규칙을 설정 하려면 NSG의 이름을 알아야 합니다. 다음 명령을 사용 하 여 NSG를 확인 합니다.

```console
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

NSG의 이름이 있으면 다음 명령을 사용 하 여 방화벽 규칙을 추가할 수 있습니다. 여기에 있는 예제 값은 포트 30655에 대 한 NSG 규칙을 만들고 **모든** 원본 IP 주소에서 연결할 수 있도록 허용 합니다.  이 방법은 보안 모범 사례가 아닙니다.  팀 또는 조직의 IP 주소를 포함 하는 IP 주소 범위 또는 클라이언트 IP 주소와 관련 된-원본-주소-접두사 값을 지정 하 여 더 잘 잠글 수 있습니다.

아래의--destination-port-범위 매개 변수 값을 위의 ' azdata arc postgres server list ' 명령에서 가져온 포트 번호로 바꿉니다.

```console
az network nsg rule create -n db_port --destination-port-ranges 30655 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="connect-with-azure-data-studio"></a>Azure Data Studio를 사용하여 연결

Azure Data Studio를 열고 위의 외부 끝점 IP 주소와 포트 번호 및 인스턴스를 만들 때 지정한 암호를 사용 하 여 인스턴스에 연결 합니다.  *연결 유형* 드롭다운에서 PostgreSQL을 사용할 수 없는 경우 확장 탭에서 PostgreSQL를 검색 하 여 PostgreSQL 확장을 설치할 수 있습니다.

> [!NOTE]
> 연결 패널에서 [고급] 단추를 클릭 하 여 포트 번호를 입력 해야 합니다.

Azure VM을 사용 하는 경우 다음 명령을 통해 액세스할 수 있는 _공용_ IP 주소가 필요 합니다.

```console
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

## <a name="connect-with-psql"></a>Psql을 사용 하 여 연결

PostgreSQL Hyperscale 서버 그룹에 액세스 하려면 위에서 검색 한 PostgreSQL Hyperscale 서버 그룹의 외부 끝점을 전달 합니다.

이제 psql을 연결할 수 있습니다.

```console
psql postgresql://postgres:<EnterYourPassword>@10.0.0.4:30655
```

## <a name="next-steps"></a>다음 단계

- Azure Database for PostgreSQL Hyperscale의 개념 및 방법 가이드를 읽고 여러 PostgreSQL Hyperscale 노드에 데이터를 분산 하 고 Azure Database for PostgreSQL 하이퍼 규모의 모든 기능을 활용 하세요. :
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
- [영구적 볼륨 클레임 확장](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Kubernetes 리소스 모델](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)