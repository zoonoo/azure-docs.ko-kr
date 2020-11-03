---
title: Azure Arc 사용 데이터 서비스-릴리스 정보
description: 최신 릴리스 정보
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 10/29/2020
ms.topic: conceptual
ms.openlocfilehash: e7312ffd4d55f0403359f8aad2d0a8433a716f77
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280371"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>릴리스 정보-Azure Arc 사용 데이터 서비스 (미리 보기)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="october-2020"></a>2020년 10월 

### <a name="breaking-changes"></a>주요 변경 내용

이 릴리스에는 다음과 같은 주요 변경 사항이 도입 되었습니다. 

* PostgreSQL CRD (사용자 지정 리소스 정의) 파일의 `shards` 이름은로 바뀝니다 `workers` . 이 단어 ( `workers` )는 명령줄 매개 변수 이름과 일치 합니다.

* `azdata arc postgres server delete` postgres 인스턴스를 삭제 하기 전에 확인 메시지를 표시 합니다.  `--force`프롬프트를 건너뛰려면를 사용 합니다.

### <a name="additional-changes"></a>추가 변경 내용

* 새 선택적 매개 변수가 호출 된에 추가 되었습니다 `azdata arc postgres server create` `--volume-claim mounts` . 값은 볼륨 클레임 탑재를 쉼표로 구분한 목록입니다. 볼륨 클레임 탑재는 볼륨 유형 및 PVC 이름 쌍입니다. 지금은의 볼륨 유형에만 허용 `backup` 됩니다.  PostgreSQL에서 볼륨 형식이 이면 `backup` PVC가에 탑재 됩니다 `/mnt/db-backups` .  이를 통해 Postgres 인스턴스 간에 백업을 공유할 수 있으므로 하나의 Postgres 인스턴스 백업을 다른 인스턴스로 복원할 수 있습니다.

* PostgresSQL 사용자 지정 리소스 정의에 대 한 새 짧은 이름: 

  * `pg11` 

  * `pg12`

* 에서 제공 하는 원격 분석 업로드는 사용자에 게 다음을 제공 합니다.

   * Azure에 업로드 된 지점의 수

     또는 

   * Azure에 로드 된 데이터가 없으면 다시 시도 하 라는 메시지가 표시 됩니다.

* `azdata arc dc debug copy-logs` 이제 폴더에서 읽고 `/var/opt/controller/log` postgres 로그를 수집 합니다.

*   백업을 만들고 복원 하는 동안 작업 표시기를 표시 합니다.

* `azdata arc postrgres backup list` 에는 이제 백업 크기 정보가 포함 됩니다.

* Azure Portal에서 SQL Managed Instance admin name 속성이 개요 블레이드의 오른쪽 열에 추가 되었습니다.



## <a name="september-2020"></a>2020년 9월

Azure Arc 사용 데이터 서비스는 공개 미리 보기에 대해 릴리스됩니다. Arc 사용 데이터 서비스를 사용 하면 어디서 나 데이터 서비스를 관리할 수 있습니다.

- SQL Managed Instance
- PostgreSQL 하이퍼스케일

자세한 지침은 [Azure Arc에서 사용할 수 있는 데이터 서비스는 무엇 인가요?](overview.md)

## <a name="known-limitations-and-issues"></a>알려진 제한 사항 및 문제

- SQL 관리 되는 인스턴스 이름은 13 자를 초과할 수 없습니다.
- Azure Arc 데이터 컨트롤러 또는 데이터베이스 인스턴스에 대 한 전체 업그레이드를 수행 하지 않습니다.
- Arc 지원 데이터 서비스 컨테이너 이미지가 서명되지 않았습니다.  서명되지 않은 컨테이너 이미지를 끌어올 수 있도록 Kubernetes 노드를 구성해야 할 수 있습니다.  예를 들어, Docker를 컨테이너 런타임으로 사용 하는 경우 DOCKER_CONTENT_TRUST = 0 환경 변수를 설정 하 고 다시 시작할 수 있습니다.  다른 컨테이너 런타임에는 [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration)와 같은 유사한 옵션이 있습니다.
- Azure Portal에서 Azure Arc 사용 SQL 관리 되는 인스턴스 또는 PostgreSQL Hyperscale 서버 그룹을 만들 수 없습니다.
- 지금은 NFS를 사용 하는 경우 `allowRunAsRoot` `true` Azure Arc 데이터 컨트롤러를 만들기 전에 배포 프로필 파일에서를로 설정 해야 합니다.
- SQL 및 PostgreSQL 로그인 인증만.  Azure Active Directory 또는 Active Directory는 지원 되지 않습니다.
- OpenShift를 사용 하 여 데이터 컨트롤러를 만들려면 완화 된 보안 제약 조건이 필요 합니다.  자세한 내용은 설명서를 참조하십시오.
- PostgresSQL Hyperscale worker 노드의 수는 _축소할_ 수 없습니다.
- Azure Arc 데이터 컨트롤러 및 데이터베이스 인스턴스를 사용 하는 Azure Stack 허브에서 AKS 엔진 (Azure Kubernetes Service Engine)을 사용 하는 경우 최신 Kubernetes 버전으로 업그레이드할 수 없습니다. Kubernetes 클러스터를 업그레이드 하기 전에 Azure Arc data controller 및 모든 데이터베이스 인스턴스를 제거 합니다.
- Preview는 Postgres 버전 11 엔진에 대 한 백업/복원을 지원 하지 않습니다. Postgres 버전 12에 대 한 백업/복원만 지원 합니다.
- Azure Kubernetes 서비스 (AKS)는 [여러 가용성 영역](../../aks/availability-zones.md) 에 걸쳐 있는 클러스터가 현재 azure Arc 사용 데이터 서비스에서 지원 되지 않습니다. 이 문제를 방지 하려면 Azure Portal에서 AKS 클러스터를 만들 때 영역을 사용할 수 있는 지역을 선택 하는 경우 선택 컨트롤에서 모든 영역을 선택 취소 합니다. 다음 이미지를 참조하세요.

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="없음을 지정 하려면 각 영역에 대 한 확인란의 선택을 취소 합니다.":::

## <a name="next-steps"></a>다음 단계
  
> **작업을 시도해보시겠습니까?**  
> AKS(Azure Kubernetes Service), AWS EKS(Elastic Kubernetes Service), GKE(Google Cloud Kubernetes Engine) 또는 Azure VM에서 [Azure Arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services)를 사용하여 빠르게 시작하세요.

[클라이언트 도구 설치](install-client-tools.md)

[Azure Arc 데이터 컨트롤러 만들기](create-data-controller.md)(먼저 클라이언트 도구를 설치해야 함)

[Azure Arc에서 Azure SQL 관리형 인스턴스 만들기](create-sql-managed-instance.md)(먼저 Azure Arc 데이터 컨트롤러를 만들어야 함)

[Azure Arc에서 Azure Database for PostgreSQL 하이퍼스케일 서버 그룹 만들기](create-postgresql-hyperscale-server-group.md)(먼저 Azure Arc 데이터 컨트롤러를 만들어야 함)
