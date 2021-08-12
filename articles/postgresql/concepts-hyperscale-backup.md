---
title: 백업 및 복원 – 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: 실수로 손상 또는 삭제되지 않도록 데이터 보호
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/14/2021
ms.openlocfilehash: 7681e9c28bbbbcec06bcc1cf2bf469f1b4189d79
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520176"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL에서 백업 및 복원 - 하이퍼스케일(Citus)

Azure Database for PostgreSQL – 하이퍼스케일(Citus)은 각 노드의 백업을 자동으로 만들고 로컬 중복 스토리지에 저장합니다. 백업을 사용하여 하이퍼스케일(Citus) 서버 그룹을 지정된 시간으로 복원할 수 있습니다.
백업 및 복원은 실수로 인한 손상이나 삭제로부터 데이터를 보호하므로 비즈니스 연속성 전략의 필수적인 부분입니다.

## <a name="backups"></a>Backup

Azure Database for PostgreSQL은 하루에 한 번 이상 데이터 파일과 데이터베이스 트랜잭션 로그의 스냅샷 백업을 수행합니다. 백업을 통해 보존 기간 내의 특정 시점으로 서버를 복원할 수 있습니다. 보존 기간은 모든 서버 그룹에 대해 최근 35일이며, 모든 백업은 AES 256비트 암호화를 사용하여 암호화됩니다.

가용성 영역을 지원하는 Azure 지역에서 백업 스냅샷은 세 가지 가용성 영역에 저장됩니다. 하나 이상의 가용성 영역이 온라인 상태이면 하이퍼스케일(Citus) 서버 그룹을 복원할 수 있습니다.

백업 파일을 내보낼 수 없습니다. Azure Database for PostgreSQL의 복원 작업에만 사용할 수 있습니다.

### <a name="backup-storage-cost"></a>백업 스토리지 비용

현재 백업 스토리지 가격 책정에 대한 자세한 내용은 Azure Database for PostgreSQL - 하이퍼스케일(Citus) [가격 책정](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/) 페이지를 참조하세요.

## <a name="restore"></a>복원

하이퍼스케일(Citus) 서버 그룹을 지난 35일 내의 특정 시점으로 복원할 수 있습니다.  특정 시점 복원은 여러 시나리오에서 유용합니다. 예를 들어 사용자가 실수로 데이터를 삭제하거나, 중요한 테이블 또는 데이터베이스를 삭제하거나, 애플리케이션이 실수로 올바른 데이터를 잘못된 데이터로 덮어쓰는 경우입니다.

> [!IMPORTANT]
> 삭제된 하이퍼스케일(Citus) 서버 그룹은 복원할 수 없습니다. 서버 그룹을 삭제하면 서버 그룹에 속한 모든 노드가 삭제되어 복구할 수 없습니다. 배포 후에 실수로 인한 삭제 또는 예기치 않은 변경에서 서버 그룹 리소스를 보호하려면 관리자는 [관리 잠금](../azure-resource-manager/management/lock-resources.md)을 활용할 수 있습니다.

복원 프로세스는 원래와 동일한 Azure 지역, 구독, 리소스 그룹에 새 서버 그룹을 만듭니다. 서버 그룹에는 동일한 수의 노드, vCores 수, 스토리지 크기, 사용자 역할, PostgreSQL 버전, Citus 확장 버전과 같은 원래 구성이 있습니다.

방화벽 설정 및 PostgreSQL 서버 매개 변수는 원래 서버 그룹에서 보존되지 않으며 기본값으로 다시 설정됩니다. 방화벽에서 모든 연결을 차단합니다. 복원 후에는 해당 설정을 수동으로 조정해야 합니다. 일반적으로 권장되는 [복원 후 작업](howto-hyperscale-restore-portal.md#post-restore-tasks) 목록을 참조하세요.

## <a name="next-steps"></a>다음 단계

* Azure Portal에서 [서버 그룹을 복원](howto-hyperscale-restore-portal.md)하는 단계를 참조하세요.
*  [Azure 가용성 영역](../availability-zones/az-overview.md) 알아보기
