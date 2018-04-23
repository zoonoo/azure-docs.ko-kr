---
title: 기존 Azure 데이터 웨어하우스를 Premium Storage로 마이그레이션 | Microsoft Docs
description: 기존 데이터 웨어하우스를 Premium Storage로 마이그레이션하기 위한 지침
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: barbkess
editor: ''
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 03/15/2018
ms.author: elbutter;barbkess
ms.openlocfilehash: 3b43bc17b7f9cf80a9520c5c573be3a48d82e4e7
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2018
---
# <a name="migrate-your-data-warehouse-to-premium-storage"></a>데이터 웨어하우스를 Premium Storage로 마이그레이션
Azure SQL Data Warehouse는 최근에 도입된 [큰 성능 예측 가능성을 위한 Premium Storage][premium storage for greater performance predictability]입니다. 이제 표준 저장소에 있는 기존 데이터 웨어하우스를 Premium Storage로 마이그레이션할 수 있습니다. 자동 마이그레이션을 활용하거나, 마이그레이션할 시기를 제어하려면(가동 중지 시간 포함) 직접 마이그레이션할 수도 있습니다.

둘 이상의 데이터 웨어하우스가 있는 경우 [자동 마이그레이션 일정][automatic migration schedule]을 사용하여 마이그레이션되는 시점을 확인하세요.

## <a name="determine-storage-type"></a>저장소 유형 결정
다음 날짜 이전에 데이터 웨어하우스를 생성한 경우 현재 표준 저장소를 사용하고 있는 것입니다.

| **지역** | **이 날짜 이전에 만든 데이터 웨어하우스** |
|:--- |:--- |
| 오스트레일리아 동부 |2018년 1월 1일 |
| 중국 동부 |2016년 11월 1일 |
| 중국 북부 |2016년 11월 1일 |
| 독일 중부 |2016년 11월 1일 |
| 독일 북동부 |2016년 11월 1일 |
| 인도 서부 |2018년 2월 1일 |
| 일본 서부 |2018년 2월 1일 |
| 미국 중북부 |2016년 11월 10일 |

## <a name="automatic-migration-details"></a>자동 마이그레이션 세부 정보
기본적으로 [자동 마이그레이션 일정][automatic migration schedule] 동안 하위 지역의 현지 시간으로 오후 6시~오전 6시 동안 데이터를 마이그레이션합니다. 마이그레이션하는 동안 기존 데이터 웨어하우스를 사용할 수 없습니다. 마이그레이션은 데이터 웨어하우스당 1TB의 저장소마다 약 1시간이 소요됩니다. 또한 자동 마이그레이션의 어느 부분에서도 비용이 청구되지 않습니다.

> [!NOTE]
> 마이그레이션이 완료되면 데이터 웨어하우스는 다시 온라인 상태가 되어 사용할 수 있게 됩니다.
>
>

Microsoft는 다음 단계에 따라 마이그레이션을 완료하며 사용자는 개입할 필요가 없습니다. 이 예제에서는 표준 저장소의 기존 데이터 웨어하우스의 이름이 현재 "MyDW"라고 가정합니다.

1. Microsoft는 "MyDW"를 "MyDW_DO_NOT_USE_[타임스탬프]"로 바꿉니다.
2. Microsoft는 "MyDW_DO_NOT_USE_[타임스탬프]"를 일시 중지합니다. 이 시간 동안 백업이 수행됩니다. 이 과정에서 문제가 발생한 경우 여러 일시 중지 및 다시 시작이 표시될 수 있습니다.
3. Microsoft는 2단계에서 수행된 백업에서 Premium Storage에 "MyDW"라는 새 데이터 웨어하우스를 만듭니다. 복원이 완료될 때까지 "MyDW"는 나타나지 않습니다.
4. 복원이 완료되면 "MyDW"는 동일한 데이터 웨어하우스 단위 및 마이그레이션 이전 상태(일시 중지 또는 활성)로 돌아갑니다.
5. 마이그레이션이 완료되면 Microsoft는 "MyDW_DO_NOT_USE_[타임스탬프]"를 삭제합니다.

> [!NOTE]
> 이러한 설정은 마이그레이션의 일부로 수행되지는 않습니다.
>
> * 데이터베이스 수준에서 감사를 다시 사용하도록 설정해야 합니다.
> * 데이터베이스 수준에서 방화벽 규칙을 다시 추가해야 합니다. 서버 수준에서 방화벽 규칙은 영향을 받지 않습니다.
>
>

### <a name="automatic-migration-schedule"></a>자동 마이그레이션 일정
자동 마이그레이션은 다음 중단 일정 동안 오후 6시 - 오전 6시(하위 지역별 현지 시간)에 발생합니다.

| **지역** | **예상된 시작 날짜** | **예상된 종료 날짜** |
|:--- |:--- |:--- |
| 오스트레일리아 동부 |2018년 3월 19일 |2018년 3월 20일 |
| 중국 동부 |이미 마이그레이션됨 |이미 마이그레이션됨 |
| 중국 북부 |이미 마이그레이션됨 |이미 마이그레이션됨 |
| 독일 중부 |이미 마이그레이션됨 |이미 마이그레이션됨 |
| 독일 북동부 |이미 마이그레이션됨 |이미 마이그레이션됨 |
| 인도 서부 |2018년 3월 19일 |2018년 3월 20일 |
| 일본 서부 |2018년 3월 19일 |2018년 3월 20일 |
| 미국 중북부 |이미 마이그레이션됨 |이미 마이그레이션됨 |

## <a name="self-migration-to-premium-storage"></a>Premium Storage로 자체 마이그레이션
가동 중지가 발생하는 시간을 제어하려는 경우 아래 단계를 사용하여 표준 저장소의 기존 데이터 웨어하우스를 Premium Storage로 마이그레이션할 수 있습니다. 이 옵션을 선택하면 해당 하위 지역에서 자동 마이그레이션을 시작하기 전에 자체 마이그레이션을 완료해야 합니다. 이렇게 하면 자동 마이그레이션이 충돌할 위험을 피할 수 있습니다([자동 마이그레이션 일정][automatic migration schedule] 참조).

### <a name="self-migration-instructions"></a>자체 마이그레이션 지침
데이터 웨어하우스를 직접 마이그레이션하려면 백업 및 복원 기능을 사용하세요. 마이그레이션의 복원 부분은 데이터 웨어하우스당 1TB 저장소마다 약 1시간 정도가 소요될 예정입니다. 마이그레이션이 완료된 후 동일한 이름을 유지하려는 경우 [마이그레이션 중에 이름을 바꾸기 위한 단계][steps to rename during migration]를 따르세요.

1. 데이터 웨어하우스를 [일시 중지][Pause]합니다. 
2. 가장 최근의 스냅숏에서 [복원][Restore]합니다.
3. 표준 저장소의 기존 데이터 웨어하우스를 삭제합니다. **이 단계의 수행에 실패하면 두 데이터 웨어하우스에 대해 비용이 청구됩니다.**

> [!NOTE]
>
> 데이터 웨어하우스를 복원할 때 데이터 웨어하우스 일시 중지 후에 사용 가능한 가장 최근 복원 지점이 표시되는지 확인합니다.
>
> 이러한 설정은 마이그레이션의 일부로 수행되지는 않습니다.
>
> * 데이터베이스 수준에서 감사를 다시 사용하도록 설정해야 합니다.
> * 데이터베이스 수준에서 방화벽 규칙을 다시 추가해야 합니다. 서버 수준에서 방화벽 규칙은 영향을 받지 않습니다.
>
>

#### <a name="rename-data-warehouse-during-migration-optional"></a>마이그레이션 중 데이터 웨어하우스의 이름 바꾸기(선택 사항)
동일한 논리 서버의 두 개의 데이터베이스는 동일한 이름을 가질 수 없습니다. 이제 SQL Data Warehouse는 데이터 웨어하우스 이름 바꾸기 기능을 지원합니다.

이 예제에서는 표준 저장소의 기존 데이터 웨어하우스의 이름이 현재 "MyDW"라고 가정합니다.

1. 다음 ALTER DATABASE 명령을 사용하여 "MyDW"를 다른 이름으로 바꿉니다. (이 예제에서는 "MyDW_BeforeMigration"으로 이름을 지정합니다.) 이 명령은 기존 트랜잭션을 모두 중지하며, 작업 성공을 위해 master 데이터베이스에서 수행되어야 합니다.
   ```
   ALTER DATABASE CurrentDatabasename MODIFY NAME = NewDatabaseName;
   ```
2. "MyDW_BeforeMigration"을 [일시 중지][Pause]합니다. 
3. 가장 최근의 스냅숏에서 이전 이름(예: "MyDW")을 갖는 새 데이터베이스로 [복원][Restore]합니다.
4. "MyDW_BeforeMigration"을 삭제합니다. **이 단계의 수행에 실패하면 두 데이터 웨어하우스에 대해 비용이 청구됩니다.**


## <a name="next-steps"></a>다음 단계
Premium Storage로 변경하여 데이터 웨어하우스의 기반 아키텍처에서 데이터베이스 blob 파일 수도 증가시켰습니다. 이 변경을 통해 성능을 최대한 개선하려면 다음 스크립트를 사용하여 클러스터형 columnstore 인덱스를 다시 작성하세요. 이 스크립트는 일부 기존 데이터를 추가 Blob에 강제로 적용하여 작동합니다. 아무 작업도 하지 않으면 자연스럽게 시간이 지나면서 테이블에 더 많은 데이터를 로드함에 따라 데이터가 재배포됩니다.

데이터 웨어하우스에 문제가 발생하는 경우 [지원 티켓을 만들고][create a support ticket] 가능한 원인으로 "Premium Storage로 마이그레이션"을 참조하세요.

<!--Image references-->

<!--Article references-->
[automatic migration schedule]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[create a support ticket]: sql-data-warehouse-get-started-create-support-ticket.md
[Azure paired region]: best-practices-availability-paired-regions.md
[main documentation site]: services/sql-data-warehouse.md
[Pause]: sql-data-warehouse-manage-compute-portal.md
[Restore]: sql-data-warehouse-restore-database-portal.md
[steps to rename during migration]: #optional-steps-to-rename-during-migration
[scale compute power]: quickstart-scale-compute-portal.md
[mediumrc role]: resource-classes-for-workload-management.md

<!--MSDN references-->


<!--Other Web references-->
[Premium Storage for greater performance predictability]: https://azure.microsoft.com/en-us/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/
[Azure Portal]: https://portal.azure.com
