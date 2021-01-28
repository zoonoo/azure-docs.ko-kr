---
title: Azure Arc 사용 PostgreSQL Hyperscale 이란?
description: Azure Arc 사용 PostgreSQL Hyperscale 이란?
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 10f21067f48155a394ac20337d77e3e82aae64d8
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98985940"
---
# <a name="what-is-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 사용 PostgreSQL Hyperscale 이란?

Azure Arc enabled PostgreSQL Hyperscale은 Azure Arc 사용 데이터 서비스의 일부로 사용 가능한 데이터베이스 서비스 중 하나입니다. Azure Arc를 통해 선택한 인프라 및 Kubernetes를 사용하여 온-프레미스, 에지 및 퍼블릭 클라우드 환경의 Azure 데이터 서비스를 실행할 수 있습니다. Azure Arc에서 사용 하도록 설정 된 데이터 서비스의 가치 제안 명확히:
- 항상 최신
- 탄력적 확장
- 셀프서비스 프로비저닝
- 통합 관리
- 연결되지 않은 시나리오 지원

자세한 내용은 다음을 참조 하세요.
- [Azure Arc 지원 데이터 서비스란?](overview.md)
- [연결 모드 및 요구 사항](connectivity.md)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="compare-solutions"></a>솔루션 비교

이 섹션에서는 Azure Arc 사용 PostgreSQL Hyperscale이 Azure Database for PostgreSQL Citus (Hyperscale)와 어떻게 다른 지에 대해 설명 합니다.

## <a name="azure-database-for-postgresql-hyperscale-citus"></a>Azure Database for PostgreSQL 하이퍼스케일(Citus)

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale.png" alt-text="PostgreSQL Hyperscale (Citus)에 대 한 Azure SQL Database":::

Azure (PaaS)에서 Database as a Service로 제공 되는 postgres 데이터베이스 엔진의 대규모 폼 요소입니다. Citus 확장을 기반으로 하 여 하이퍼 크기 조정 환경을 가능 하 게 합니다. 이 폼 팩터에서 서비스는 Microsoft 데이터 센터에서 실행 되며 Microsoft에서 운영 됩니다.

## <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 사용 PostgreSQL Hyperscale

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale-arc.png" alt-text="Azure Arc 사용 PostgreSQL Hyperscale":::

Azure Arc 사용 데이터 서비스에서 사용할 수 있는 postgres 데이터베이스 엔진의 대규모 폼 요소입니다. 또한 하이퍼 크기 조정 환경을 가능 하 게 하는 Citus 확장을 기반으로 합니다. 이 폼 팩터에서 고객은 시스템을 호스트 하 고 운영 하는 인프라를 제공 합니다.

## <a name="next-steps"></a>다음 단계
- **사용해 보세요.** Azure Kubernetes 서비스 (AKS), AWS 탄력적 Kubernetes 서비스 (EKS), Google Cloud Kubernetes Engine (GKE) 또는 Azure VM에서 [Azure Arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) 를 빠르게 시작 하세요. 

- **직접 만듭니다.** 사용자 고유의 Kubernetes 클러스터를 만들려면 다음 단계를 따르세요. 
   1. [클라이언트 도구 설치](install-client-tools.md)
   2. [Azure Arc 데이터 컨트롤러 만들기](create-data-controller.md)
   3. [Azure Arc에서 Azure Database for PostgreSQL Hyperscale server 그룹 만들기](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Azure Arc 사용 데이터 서비스에 대 한 자세한 정보](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Azure Arc에 대해 읽기](https://aka.ms/azurearc)
