---
title: '빠른 시작: 기본 계층 서버 그룹 만들기 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL'
description: Azure Database for PostgreSQL 하이퍼스케일(Citus) 기본 계층을 시작합니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 04/07/2021
ms.openlocfilehash: 07ace217e5299662a1c3145a225abc25f4f1f337
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023880"
---
# <a name="create-a-hyperscale-citus-basic-tier-server-group-in-the-azure-portal"></a>Azure Portal에서 하이퍼스케일(Citus) 기본 계층 서버 그룹 만들기

Azure Database for PostgreSQL - 하이퍼스케일(Citus)은 클라우드에서 항상 사용 가능한 PostgreSQL 데이터베이스를 실행, 관리 및 크기 조정하는 데 사용하는 관리형 서비스입니다. [기본 계층](concepts-hyperscale-tiers.md)은 초기 개발 및 테스트를 위한 편리한 배포 옵션입니다.

이 빠른 시작에서는 Azure Portal을 사용하여 하이퍼스케일(Citus) 서버 그룹을 만드는 방법을 보여줍니다. 서버 그룹을 프로비저닝하고 연결하여 쿼리를 실행할 수 있는지 확인합니다.

> [!IMPORTANT]
> 하이퍼스케일(Citus) 서비스 계층은 현재 미리 보기로 제공되고 있습니다.  이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
>
> [하이퍼스케일(Citus)의 미리 보기 기능](hyperscale-preview-features.md)에서 다른 새 기능의 전체 목록을 볼 수 있습니다.

[!INCLUDE [azure-postgresql-hyperscale-create-basic-tier](../../includes/azure-postgresql-hyperscale-create-basic-tier.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 하이퍼스케일(Citus) 서버 그룹을 프로비저닝하는 방법을 배웠습니다. 그 후 psql을 사용하여 이 서버 그룹에 연결하고, 스키마를 만들고, 데이터를 분산했습니다.

- 자습서에 따라 [확장성 있는 다중 테넌트 애플리케이션을 빌드](./tutorial-design-database-hyperscale-multi-tenant.md)합니다.
- 서버 그룹에 적합한 [처음 크기](howto-hyperscale-scale-initial.md)를 결정합니다.
