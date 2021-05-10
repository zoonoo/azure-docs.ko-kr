---
title: 백업 및 복원 – 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: 실수로 손상 또는 삭제되지 않도록 데이터 보호
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 90b2a39b9a5f3b4d011ff1a1ef3651dff75a1cf6
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968308"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL에서 백업 및 복원 - 하이퍼스케일(Citus)

Azure Database for PostgreSQL – 하이퍼스케일(Citus)은 각 노드의 백업을 자동으로 만들고 로컬 중복 스토리지에 저장합니다. 백업을 사용하여 하이퍼스케일(Citus) 클러스터를 지정된 시간으로 복원할 수 있습니다. 백업 및 복원은 실수로 인한 손상이나 삭제로부터 데이터를 보호하므로 비즈니스 연속성 전략의 필수적인 부분입니다.

## <a name="backups"></a>Backup

Azure Database for PostgreSQL은 하루에 한 번 이상 데이터 파일과 데이터베이스 트랜잭션 로그의 스냅샷 백업을 수행합니다. 백업을 통해 보존 기간 내의 특정 시점으로 서버를 복원할 수 있습니다. 보존 기간은 모든 클러스터에 대해 최근 35일이며, 모든 백업은 AES 256비트 암호화를 사용하여 암호화됩니다.

가용성 영역을 지원하는 Azure 지역에서 백업 스냅샷은 세 가지 가용성 영역에 저장됩니다. 하나 이상의 가용성 영역이 온라인 상태이면 하이퍼스케일(Citus) 클러스터를 복원할 수 있습니다.

백업 파일을 내보낼 수 없습니다. Azure Database for PostgreSQL의 복원 작업에만 사용할 수 있습니다.

### <a name="backup-storage-cost"></a>백업 스토리지 비용

현재 백업 스토리지 가격 책정에 대한 자세한 내용은 Azure Database for PostgreSQL - 하이퍼스케일(Citus) [가격 책정](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/) 페이지를 참조하세요.

## <a name="restore"></a>복원

Azure Database for PostgreSQL에서 하이퍼스케일(Citus) 클러스터를 복원하면 원래 노드의 백업으로부터 새 클러스터가 만들어집니다. 

> [!IMPORTANT]
>동일한 구독 및 리소스 그룹 내에서 클러스터 이름이 다른 하이퍼스케일(Citus) 클러스터만 복원할 수 있습니다.


> [!IMPORTANT]
> 삭제된 하이퍼스케일(Citus) 클러스터는 복원할 수 없습니다. 클러스터를 삭제하면 클러스터에 속한 모든 노드가 삭제되어 복구할 수 없습니다. 배포 후에 실수로 인한 삭제 또는 예기치 않은 변경에서 클러스터 리소스를 보호하려면 관리자는 [관리 잠금](../azure-resource-manager/management/lock-resources.md)을 활용할 수 있습니다.

### <a name="point-in-time-restore-pitr"></a>PITR(특정 시점 복원)

최근 35일 이내의 특정 시점으로 클러스터를 복원할 수 있습니다.
특정 시점 복원은 여러 시나리오에서 유용합니다. 예를 들어 사용자가 실수로 데이터를 삭제하거나, 중요한 테이블 또는 데이터베이스를 삭제하거나, 애플리케이션이 실수로 올바른 데이터를 잘못된 데이터로 덮어쓰는 경우입니다.

복원 프로세스는 원래와 동일한 Azure 지역, 구독, 리소스 그룹에 새 클러스터를 만듭니다. 클러스터에는 동일한 수의 노드, vCores 수, 스토리지 크기, 사용자 역할, PostgreSQL 버전, Citus 확장 버전과 같은 원래 구성이 있습니다.

방화벽 설정 및 PostgreSQL 서버 매개 변수는 원래 서버 그룹에서 보존되지 않으며 기본값으로 다시 설정됩니다. 방화벽에서 모든 연결을 차단합니다. 복원 후에는 해당 설정을 수동으로 조정해야 합니다.

> [!IMPORTANT]
> 하이퍼스케일(Citus) 클러스터의 특정 시점 복원을 수행하려면 지원 요청을 열어야 합니다.

### <a name="post-restore-tasks"></a>복원 후 작업

복구 메커니즘에서 복원한 후에 다음을 수행하여 사용자 및 애플리케이션을 백업하고 실행해야 합니다.

* 새 서버가 원래 서버를 교체하기 위한 것이라면 클라이언트와 클라이언트 애플리케이션을 새 서버로 리디렉션합니다.
* 사용자가 연결할 수 있도록 적절한 서버 수준 방화벽이 있는지 확인합니다. 이러한 규칙은 원래 서버 그룹에서 복사되지 않습니다.
* 필요에 따라 PostgreSQL 서버 매개 변수를 조정합니다. 원래 서버 그룹에서 매개 변수가 복사되지 않습니다.
* 적절한 로그인 및 데이터베이스 수준 권한이 있는지 확인합니다.
* 필요에 따라 경고를 구성합니다.

## <a name="next-steps"></a>다음 단계

*  [Azure 가용성 영역](../availability-zones/az-overview.md) 알아보기
* 하이퍼스케일(Citus) 서버 그룹에서  [제안된 경고](./howto-hyperscale-alert-on-metric.md#suggested-alerts)를 설정합니다.
