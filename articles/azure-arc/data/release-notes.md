---
title: Azure Arc 사용 데이터 서비스-릴리스 정보
description: 최신 릴리스 정보
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 5908083be4e6ed389b606754ffef41a4a371c3e3
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938724"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>릴리스 정보-Azure Arc 사용 데이터 서비스 (미리 보기)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="september-2020"></a>2020년 9월

Azure Arc 사용 데이터 서비스는 공개 미리 보기에 대해 릴리스됩니다. Arc 사용 데이터 서비스를 사용 하면 어디서 나 데이터 서비스를 관리할 수 있습니다.

- SQL Managed Instance
- PostgreSQL 하이퍼스케일

자세한 지침은 [Azure Arc에서 사용할 수 있는 데이터 서비스는 무엇 인가요?](overview.md)

## <a name="next-steps"></a>다음 단계

> **작업을 시도해보시겠습니까?**  
> Azure Kubernetes 서비스 (AKS), AWS 탄력적 Kubernetes 서비스 (EKS), Google Cloud Kubernetes Engine (GKE) 또는 Azure VM에서 [Azure Arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) 를 빠르게 시작 하세요.

[클라이언트 도구 설치](install-client-tools.md)

[Azure Arc 데이터 컨트롤러 만들기](create-data-controller.md) (클라이언트 도구를 먼저 설치 해야 함)

Azure [arc에서 AZURE SQL 관리 되는 인스턴스 만들기](create-sql-managed-instance.md) (먼저 azure arc 데이터 컨트롤러를 만들어야 함)

[Azure arc에서 Azure Database for PostgreSQL Hyperscale server 그룹 만들기](create-postgresql-hyperscale-server-group.md) (먼저 azure arc 데이터 컨트롤러를 만들어야 함)

## <a name="known-limitations-and-issues"></a>알려진 제한 사항 및 문제

- SQL 관리 되는 인스턴스 이름은 13 자를 초과할 수 없습니다.
- Azure Arc 데이터 컨트롤러 또는 데이터베이스 인스턴스에 대 한 전체 업그레이드를 수행 하지 않습니다.
- Arc 사용 데이터 서비스 컨테이너 이미지는 서명 되지 않습니다.  서명 되지 않은 컨테이너 이미지를 끌어올 수 있도록 Kubernetes 노드를 구성 해야 할 수 있습니다.  예를 들어, Docker를 컨테이너 런타임으로 사용 하는 경우 DOCKER_CONTENT_TRUST = 0 환경 변수를 설정 하 고 다시 시작할 수 있습니다.  다른 컨테이너 런타임에는 [Openshift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration)와 같은 유사한 옵션이 있습니다.
- Azure Portal에서 Azure Arc 사용 SQL 관리 되는 인스턴스 또는 PostgreSQL Hyperscale 서버 그룹을 만들 수 없습니다.
- 지금은 NFS를 사용 하는 경우 Azure Arc 데이터 컨트롤러를 만들기 전에 배포 프로필 파일에서 allowRunAsRoot를 true로 설정 해야 합니다.
- SQL 및 PostgreSQL 로그인 인증만.  Azure Active Directory 또는 Active Directory는 지원 되지 않습니다.
- OpenShift를 사용 하 여 데이터 컨트롤러를 만들려면 완화 된 보안 제약 조건이 필요 합니다.  자세한 내용은 설명서를 참조하십시오.
- Postgres Hyperscale worker 노드 수는 _축소할_ 수 없습니다.
- Azure Arc 데이터 컨트롤러 및 데이터베이스 인스턴스를 사용 하는 Azure Stack 허브에서 AKS 엔진 (Azure Kubernetes Service Engine)을 사용 하는 경우 최신 Kubernetes 버전으로 업그레이드할 수 없습니다. Kubernetes 클러스터를 업그레이드 하기 전에 Azure Arc data controller 및 모든 데이터베이스 인스턴스를 제거 합니다.
- Preview는 Postgres 버전 11 엔진에 대 한 백업/복원을 지원 하지 않습니다. Postgres 버전 12에 대 한 백업/복원만 지원 합니다.
