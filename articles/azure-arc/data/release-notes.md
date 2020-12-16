---
title: Azure Arc 사용 데이터 서비스-릴리스 정보
description: 최신 릴리스 정보
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 12/09/2020
ms.topic: conceptual
ms.openlocfilehash: 935ba888352d2454a609a40866ef10ccf13a2dac
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605465"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>릴리스 정보-Azure Arc 사용 데이터 서비스 (미리 보기)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="december-2020"></a>2020년 12월

### <a name="new-capabilities--features"></a>& 기능을 제공 하는 새로운 기능

Azure Data CLI ( `azdata` ) 버전 번호: 20.2.5. 에서 다운로드 [https://aka.ms/azdata](https://aka.ms/azdata) 합니다.

및 명령과 함께 Azure Data CLI ()를 사용 하 여 SQL Managed Instance 및 PostgreSQL Hyperscale의 끝점을 확인 `azdata` `azdata arc sql endpoint list` `azdata arc postgres endpoint list` 합니다.

Azure Data Studio를 사용 하 여 SQL Managed Instance 리소스 (CPU 코어 및 메모리) 요청 및 제한을 편집 합니다.
     
이제 Azure Arc enabled PostgreSQL Hyperscale은 버전 11 및 12 PostgreSQL 모두에 대 한 전체 백업 복원과 더불어 지정 시간 복원을 지원 합니다. 지정 시간 복원 기능을 사용 하 여 복원할 특정 날짜와 시간을 지정할 수 있습니다.

Pod for Azure Arc enabled PostgreSQL Hyperscale의 명명 규칙이 변경 되었습니다. 이제 ServergroupName {r, s}-_n_ 형식으로 되어 있습니다. 예를 들어 세 개의 노드가 있는 서버 그룹 1 개 및 두 개의 작업자 노드는 다음과 같이 표시 됩니다.
- `postgres02r-0` (코디네이터 노드)
- `postgres02s-0` (작업자 노드)
- `postgres02s-1` (작업자 노드)

### <a name="breaking-change"></a>주요 변경 내용

#### <a name="new-resource-provider"></a>새 리소스 공급자

이 릴리스에는 이라는 업데이트 된 [리소스 공급자](../../azure-resource-manager/management/azure-services-resource-providers.md) 가 도입 되었습니다 `Microsoft.AzureArcData` . 이 기능을 사용 하려면 먼저이 리소스 공급자를 등록 해야 합니다. 

이 리소스 공급자를 등록 하려면: 

1. Azure Portal에서 **구독** 을 선택 합니다. 
2. 구독 선택
3. **설정** 아래에서 **리소스 공급자** 를 선택 합니다. 
4. 을 검색 `Microsoft.AzureArcData` 하 고 **등록** 을 선택 합니다. 

[Azure 리소스 공급자 및 형식](../../azure-resource-manager/management/resource-providers-and-types.md)에서 자세한 단계를 검토할 수 있습니다. 이렇게 변경 하면 Azure Portal에 업로드 한 기존 Azure 리소스도 모두 제거 됩니다. 리소스 공급자를 사용 하기 위해 데이터 컨트롤러를 업데이트 하 고 최신 CLI를 사용 해야 합니다 `azdata` .  

### <a name="platform-release-notes"></a>플랫폼 릴리스 정보

#### <a name="direct-connectivity-mode"></a>직접 연결 모드

이 릴리스에는 직접 연결 모드가 도입 되었습니다. 직접 연결 모드를 사용 하면 데이터 컨트롤러가 사용 정보를 Azure에 자동으로 업로드할 수 있습니다. 사용 현황 업로드의 일부로, Arc 데이터 컨트롤러 리소스가 이미 업로드를 통해 생성 되지 않은 경우 포털에 자동으로 만들어집니다 `azdata` .  

데이터 컨트롤러를 만들 때 직접 연결을 지정할 수 있습니다. 다음 예에서는 `azdata arc dc create` `arc` 직접 연결 모드 ()를 사용 하 여 라는 데이터 컨트롤러를 만듭니다 `connectivity-mode direct` . 예제를 실행 하기 전에를 `<subscription id>` 구독 ID로 바꾸십시오.

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group my-resource-group --location eastus --connectivity-mode direct
```

### <a name="known-issues"></a>알려진 문제

- AKS (Azure Kubernetes Service)에서 Kubernetes 버전 1.19은 지원 되지 않습니다.
- Kubernetes 1.19 `containerd` 는 지원 되지 않습니다.
- Azure의 데이터 컨트롤러 리소스는 현재 Azure 리소스입니다. Delete와 같은 모든 업데이트는 kubernetes 클러스터로 다시 전파 되지 않습니다.
- 인스턴스 이름은 13 자를 초과할 수 없습니다.
- Azure Arc 데이터 컨트롤러 또는 데이터베이스 인스턴스에 대 한 전체 업그레이드를 수행 하지 않습니다.
- Arc 지원 데이터 서비스 컨테이너 이미지가 서명되지 않았습니다.  서명되지 않은 컨테이너 이미지를 끌어올 수 있도록 Kubernetes 노드를 구성해야 할 수 있습니다.  예를 들어, Docker를 컨테이너 런타임으로 사용 하는 경우 DOCKER_CONTENT_TRUST = 0 환경 변수를 설정 하 고 다시 시작할 수 있습니다.  다른 컨테이너 런타임에는 [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration)와 같은 유사한 옵션이 있습니다.
- Azure Portal에서 Azure Arc 사용 SQL 관리 되는 인스턴스 또는 PostgreSQL Hyperscale 서버 그룹을 만들 수 없습니다.
- 지금은 NFS를 사용 하는 경우 `allowRunAsRoot` `true` Azure Arc 데이터 컨트롤러를 만들기 전에 배포 프로필 파일에서를로 설정 해야 합니다.
- SQL 및 PostgreSQL 로그인 인증만.  Azure Active Directory 또는 Active Directory는 지원 되지 않습니다.
- OpenShift를 사용 하 여 데이터 컨트롤러를 만들려면 완화 된 보안 제약 조건이 필요 합니다.  자세한 내용은 설명서를 참조하십시오.
- Azure Arc 데이터 컨트롤러 및 데이터베이스 인스턴스를 사용 하는 Azure Stack 허브에서 AKS (Azure Kubernetes Service) 엔진을 사용 하는 경우 최신 Kubernetes 버전으로 업그레이드할 수 없습니다. Kubernetes 클러스터를 업그레이드 하기 전에 Azure Arc data controller 및 모든 데이터베이스 인스턴스를 제거 합니다.
- [여러 가용성 영역](../../aks/availability-zones.md) 에 걸쳐 있는 AKS 클러스터는 현재 Azure Arc 사용 데이터 서비스에서 지원 되지 않습니다. 이 문제를 방지 하려면 Azure Portal에서 AKS 클러스터를 만들 때 영역을 사용할 수 있는 지역을 선택 하는 경우 선택 컨트롤에서 모든 영역을 선택 취소 합니다. 다음 이미지를 참조하세요.

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="없음을 지정 하려면 각 영역에 대 한 확인란의 선택을 취소 합니다.":::

#### <a name="postgresql"></a>PostgreSQL

- Azure Arc enabled PostgreSQL Hyperscale은 지정 된 상대 시점으로 복원할 수 없는 경우 부정확 한 오류 메시지를 반환 합니다. 예를 들어 백업에 포함 된 것 보다 오래 된 복원 시점을 지정한 경우 오류: (404)와 같은 오류 메시지와 함께 복원이 실패 합니다. 이유:를 찾을 수 없습니다. HTTP 응답 본문: {"code": 404, "internalStatus": "NOT_FOUND", "이유": "서버에 대 한 백업 복원 실패 ...}
이 경우 백업이 있는 날짜 범위 내에 있는 특정 시점을 표시 한 후에 명령을 다시 시작 합니다. 백업을 나열 하 고이를 수행한 날짜를 확인 하 여이 범위를 확인 합니다.
- 특정 시점 복원은 서버 그룹에서 지원 됩니다. 지정 시간 복원 작업의 대상 서버는 백업을 수행한 서버 일 수 없습니다. 다른 서버 그룹 이어야 합니다. 그러나 전체 복원은 동일한 서버 그룹에 대해 지원 됩니다.
- 전체 복원을 수행할 때 백업 id가 필요 합니다. 기본적으로 백업 id를 표시 하지 않는 경우 최신 백업이 사용 됩니다. 이 릴리스에서는이 작업을 수행할 수 없습니다.

## <a name="october-2020"></a>2020년 10월 

Azure Data CLI ( `azdata` ) 버전 번호: 20.2.3. 에서 다운로드 [https://aka.ms/azdata](https://aka.ms/azdata) 합니다.

### <a name="breaking-changes"></a>호환성이 손상되는 변경

이 릴리스에는 다음과 같은 주요 변경 사항이 도입 되었습니다. 

* PostgreSQL 사용자 지정 리소스 정의 (CRD)에서 용어의 `shards` 이름이로 바뀝니다 `workers` . 이 용어 ( `workers` )는 명령줄 매개 변수 이름과 일치 합니다.

* `azdata arc postgres server delete` postgres 인스턴스를 삭제 하기 전에 확인 메시지를 표시 합니다.  `--force`프롬프트를 건너뛰려면를 사용 합니다.

### <a name="additional-changes"></a>추가 변경 내용

* 새 선택적 매개 변수가 호출 된에 추가 되었습니다 `azdata arc postgres server create` `--volume-claim mounts` . 값은 볼륨 클레임 탑재를 쉼표로 구분한 목록입니다. 볼륨 클레임 탑재는 볼륨 유형 및 PVC 이름 쌍입니다. 현재 지원 되는 볼륨 유형은 뿐입니다 `backup` .  PostgreSQL에서 볼륨 형식이 이면 `backup` PVC가에 탑재 됩니다 `/mnt/db-backups` .  이렇게 하면 한 PostgresSQL 인스턴스의 백업을 다른 인스턴스에서 복원할 수 있도록 PostgresSQL 인스턴스 간에 백업을 공유할 수 있습니다.

* PostgresSQL 사용자 지정 리소스 정의에 대 한 새 짧은 이름: 
  * `pg11` 
  * `pg12`
* 원격 분석 업로드는 사용자에 게 다음 중 하나를 제공 합니다.
   * Azure에 업로드 된 지점의 수 또는 
   * Azure에 로드 된 데이터가 없으면 다시 시도 하 라는 메시지가 표시 됩니다.
* `azdata arc dc debug copy-logs` 이제 폴더에서 읽고 `/var/opt/controller/log` Linux에서 PostgreSQL 엔진 로그를 수집 합니다.
*   PostgreSQL Hyperscale을 사용 하 여 백업을 만들고 복원 하는 동안 작업 표시기를 표시 합니다.
* `azdata arc postrgres backup list` 에는 이제 백업 크기 정보가 포함 됩니다.
* Azure Portal에서 SQL Managed Instance admin name 속성이 개요 블레이드의 오른쪽 열에 추가 되었습니다.
* Azure Data Studio는 PostgreSQL Hyperscale의 작업자 노드 수, vCore 및 메모리 설정 구성을 지원 합니다. 
* Preview는 Postgres 버전 11 및 12에 대 한 백업/복원을 지원 합니다.

## <a name="september-2020"></a>2020년 9월

Azure Arc 사용 데이터 서비스는 공개 미리 보기에 대해 릴리스됩니다. Arc 사용 데이터 서비스를 사용 하면 어디서 나 데이터 서비스를 관리할 수 있습니다.

- SQL Managed Instance
- PostgreSQL 하이퍼스케일

자세한 지침은 [Azure Arc에서 사용할 수 있는 데이터 서비스는 무엇 인가요?](overview.md)

## <a name="next-steps"></a>다음 단계
  
> **작업을 시도해보시겠습니까?**  
> AKS, EKS (AWS 탄력적 Kubernetes Service), Google Cloud Kubernetes Engine (GKE) 또는 Azure VM에서 [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) 를 빠르게 시작 하세요.

- [클라이언트 도구 설치](install-client-tools.md)
- [Azure Arc 데이터 컨트롤러 만들기](create-data-controller.md)(먼저 클라이언트 도구를 설치해야 함)
- [Azure Arc에서 Azure SQL 관리형 인스턴스 만들기](create-sql-managed-instance.md)(먼저 Azure Arc 데이터 컨트롤러를 만들어야 함)
- [Azure Arc에서 Azure Database for PostgreSQL 하이퍼스케일 서버 그룹 만들기](create-postgresql-hyperscale-server-group.md)(먼저 Azure Arc 데이터 컨트롤러를 만들어야 함)
- [Azure 서비스의 리소스 공급자](../../azure-resource-manager/management/azure-services-resource-providers.md)
