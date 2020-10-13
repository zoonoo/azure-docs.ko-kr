---
title: Backup 및 restore – Hyperscale (Citus)-Azure Database for PostgreSQL
description: 실수로 손상 되거나 삭제 되지 않도록 데이터 보호
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 2d781ca7293d4bd95ae62eadc50295ca14c2d381
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91314933"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL-Hyperscale (Citus)의 백업 및 복원

Azure Database for PostgreSQL – Hyperscale (Citus)은 각 노드의 백업을 자동으로 만들고 로컬 중복 저장소에 저장 합니다. 백업을 사용 하 여 Citus (Hyperscale) 클러스터를 지정 된 시간으로 복원할 수 있습니다. 백업 및 복원은 실수로 인한 손상이나 삭제로부터 데이터를 보호하므로 비즈니스 연속성 전략의 필수적인 부분입니다.

## <a name="backups"></a>Backup

하루에 한 번, Azure Database for PostgreSQL는 데이터 파일과 데이터베이스 트랜잭션 로그의 스냅숏 백업을 수행 합니다. 백업을 사용 하면 보존 기간 내의 특정 시점으로 서버를 복원할 수 있습니다. (보존 기간은 현재 모든 클러스터에 대해 35 일입니다.) 모든 백업은 AES 256 비트 암호화를 사용 하 여 암호화 됩니다.

가용성 영역을 지 원하는 Azure 지역에서 백업 스냅숏은 세 가지 가용성 영역에 저장 됩니다. 하나 이상의 가용성 영역이 온라인 상태 이면 Citus (Hyperscale) 클러스터는 복원 가능한입니다.

백업 파일을 내보낼 수 없습니다. Azure Database for PostgreSQL의 복원 작업에만 사용할 수 있습니다.

### <a name="backup-storage-cost"></a>백업 스토리지 비용

현재 백업 저장소 가격 책정에 대 한 자세한 내용은 Azure Database for PostgreSQL-Hyperscale (Citus) [가격 책정 페이지](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)를 참조 하세요.

## <a name="restore"></a>복원

Azure Database for PostgreSQL에서 Citus (Hyperscale) 클러스터를 복원 하면 원래 노드의 백업에서 새 클러스터가 만들어집니다. 

> [!IMPORTANT]
>동일한 구독 및 리소스 그룹 내에서 다른 클러스터 이름으로 Hyperscale (Citus) 클러스터를 복원할 수 있습니다.


> [!IMPORTANT]
> 삭제 된 Citus (Hyperscale) 클러스터는 복원할 수 없습니다. 클러스터를 삭제 하면 해당 클러스터에 속하는 모든 노드가 삭제 되며 복구할 수 없습니다. 클러스터 리소스를 보호 하기 위해 실수로 삭제 하거나 예기치 않은 변경에서 배포 후에는 관리자가 [관리 잠금을](/azure/azure-resource-manager/management/lock-resources)활용할 수 있습니다.

### <a name="point-in-time-restore-pitr"></a>지정 시간 복원 (PITR)

클러스터를 최근 35 일 내의 특정 시점으로 복원할 수 있습니다.
특정 시점 복원은 여러 시나리오에서 유용합니다. 예를 들어 사용자가 실수로 데이터를 삭제 하거나 중요 한 테이블이 나 데이터베이스를 삭제 하거나 응용 프로그램이 잘못 된 데이터를 사용 하 여 적절 한 데이터를 실수로 덮어쓰는 경우입니다.

복원 프로세스는 원래와 동일한 Azure 지역, 구독 및 리소스 그룹에 새 클러스터를 만듭니다. 클러스터에는 원래의 구성이 있습니다. 동일한 노드 수, vCores 수, 저장소 크기, 사용자 역할, PostgreSQL 버전 및 Citus 확장의 버전이 있습니다.

방화벽 설정 및 PostgreSQL 서버 매개 변수는 원래 서버 그룹에서 유지 되지 않으며 기본값으로 다시 설정 됩니다. 방화벽에서 모든 연결을 차단 합니다. 복원 후에는 이러한 설정을 수동으로 조정 해야 합니다.

> [!IMPORTANT]
> Citus (Hyperscale) 클러스터의 지정 시간 복원을 수행 하려면 지원 요청을 열어야 합니다.

### <a name="post-restore-tasks"></a>복원 후 작업

복구 메커니즘에서 복원한 후에는 다음을 수행 하 여 사용자 및 응용 프로그램을 백업 하 고 실행 해야 합니다.

* 새 서버가 원래 서버를 교체하기 위한 것이라면 클라이언트와 클라이언트 애플리케이션을 새 서버로 리디렉션합니다.
* 사용자가 연결할 수 있도록 적절 한 서버 수준 방화벽 및 VNet 규칙이 준비 되어 있는지 확인 합니다. 이러한 규칙은 원래 서버 그룹에서 복사 되지 않습니다.
* 필요에 따라 PostgreSQL 서버 매개 변수를 조정 합니다. 원래 서버 그룹에서 매개 변수가 복사 되지 않습니다.
* 적절한 로그인 및 데이터베이스 수준 권한이 있는지 확인합니다.
* 필요에 따라 경고를 구성합니다.

## <a name="next-steps"></a>다음 단계

*  [Azure 가용성 영역](/azure/availability-zones/az-overview)에 대해 알아봅니다.
* Citus (Hyperscale) 서버 그룹에서 [제안 된 경고](/azure/postgresql/howto-hyperscale-alert-on-metric#suggested-alerts) 를 설정 합니다.
