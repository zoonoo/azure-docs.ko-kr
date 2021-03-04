---
title: Azure Arc 사용 데이터 서비스-알려진 문제
description: 알려진 최신 문제
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/02/2021
ms.topic: conceptual
ms.openlocfilehash: 8100d9e12f107e0c4598876c46453b46c6ee4d0e
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122003"
---
# <a name="known-issues---azure-arc-enabled-data-services-preview"></a>알려진 문제-Azure Arc 사용 데이터 서비스 (미리 보기)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="february-2021"></a>2021년 2월

- 연결 된 클러스터 모드 사용 안 함

## <a name="introduced-prior-to-february-2021"></a>2 월 2021 일 이전에 도입 되었습니다.

### <a name="data-controller"></a>데이터 컨트롤러

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

### <a name="postgresql"></a>PostgreSQL

- Azure Arc enabled PostgreSQL Hyperscale은 지정 된 상대 시점으로 복원할 수 없는 경우 부정확 한 오류 메시지를 반환 합니다. 예를 들어 백업에 포함 된 것 보다 오래 된 복원 시점을 지정한 경우 다음과 같은 오류 메시지와 함께 복원이 실패 합니다. `ERROR: (404). Reason: Not found. HTTP response body: {"code":404, "internalStatus":"NOT_FOUND", "reason":"Failed to restore backup for server...}`
이 경우 백업이 있는 날짜 범위 내에 있는 특정 시점을 표시 한 후에 명령을 다시 시작 합니다. 백업을 나열 하 고이를 수행한 날짜를 확인 하 여이 범위를 확인 합니다.
- 특정 시점 복원은 서버 그룹에서 지원 됩니다. 지정 시간 복원 작업의 대상 서버는 백업을 수행한 서버 일 수 없습니다. 다른 서버 그룹 이어야 합니다. 그러나 전체 복원은 동일한 서버 그룹에 대해 지원 됩니다.
- 전체 복원을 수행할 때 백업 id가 필요 합니다. 기본적으로 백업 id를 표시 하지 않는 경우 최신 백업이 사용 됩니다. 이 릴리스에서는이 작업을 수행할 수 없습니다.

## <a name="next-steps"></a>다음 단계

> **작업을 시도해보시겠습니까?**  
> AKS, EKS (AWS 탄력적 Kubernetes Service), Google Cloud Kubernetes Engine (GKE) 또는 Azure VM에서 [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) 를 빠르게 시작 하세요.

- [클라이언트 도구 설치](install-client-tools.md)
- [Azure Arc 데이터 컨트롤러 만들기](create-data-controller.md)(먼저 클라이언트 도구를 설치해야 함)
- [Azure Arc에서 Azure SQL 관리형 인스턴스 만들기](create-sql-managed-instance.md)(먼저 Azure Arc 데이터 컨트롤러를 만들어야 함)
- [Azure Arc에서 Azure Database for PostgreSQL 하이퍼스케일 서버 그룹 만들기](create-postgresql-hyperscale-server-group.md)(먼저 Azure Arc 데이터 컨트롤러를 만들어야 함)
- [Azure 서비스의 리소스 공급자](../../azure-resource-manager/management/azure-services-resource-providers.md)
- [릴리스 정보-Azure Arc 사용 데이터 서비스 (미리 보기)](release-notes.md)
