---
title: 자동 튜닝 개요
description: Azure SQL Database는 SQL 쿼리를 분석하고 사용자 워크로드에 자동으로 적용됩니다.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: d4762c86268353ff0464ff3919250cd86f0038d4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79214108"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Azure SQL Database에서 자동 조정

자동 조정은 AI 및 기계 학습을 기반으로 하는 지속적인 성능 조정을 통해 최대 성능 및 안정적인 워크 로드를 제공 합니다. Azure SQL Database

자동 조정은 기본 제공 인텔리전스를 사용하여 데이터베이스에서 실행된 쿼리를 지속적으로 모니터링하고 해당 성능을 자동으로 개선하는 완전히 관리되는 지능형 성능 서비스입니다. 이것은 변화하는 워크로드에 동적으로 데이터베이스를 적용하고 튜닝 권장 사항을 적용함으로써 달성됩니다. 자동 조정은 AI를 통해 Azure의 모든 데이터베이스에서 수평적으로 학습하고 동적으로 조정 작업을 개선합니다. 에서 자동 튜닝을 사용 하 여 데이터베이스를 실행 하면 성능이 향상 됩니다.

자동 조정 Azure SQL Database는 안정적이 고 성능이 가장 많은 데이터베이스 작업을 제공 하기 위해 사용할 수 있는 가장 중요 한 기능 중 하나일 수 있습니다.

## <a name="what-can-automatic-tuning-do-for-you"></a>자동 조정에서 수행할 수 있는 작업

- Azure SQL 데이터베이스의 자동화된 성능 조정
- 성능 향상의 자동화된 확인
- 자동화된 롤백 및 자체 수정
- 조정 기록
- 수동 배포를 위한 조정 작업 T-SQL 스크립트
- 자동 관리 워크로드 성능 모니터링
- 수십만 개의 데이터베이스에서 기능 확장
- DevOps 리소스와 총 소유 비용에 미치는 긍정적인 영향

## <a name="safe-reliable-and-proven"></a>안전하고 신뢰할 수 있으며 검증됨

Azure SQL Database의 데이터베이스에 적용 되는 튜닝 작업은 가장 강력한 워크 로드의 성능에 대해 완벽 하 게 안전 합니다. 시스템은 사용자 워크로드를 방해하지 않도록 주의하여 설계되었습니다. 자동 조정 권장 사항은 사용률이 낮은 경우에만 적용됩니다. 시스템 또한 워크로드 성능을 보호하기 위해 일시적으로 자동 조정 작업을 사용하지 않도록 설정할 수도 있습니다. 이러한 경우 "시스템에서 사용 하지 않도록 설정" 메시지는 Azure Portal 표시 됩니다. 자동 조정 작업은 리소스 우선 순위가 가장 높은 워크로드를 인식합니다.

자동 조정 메커니즘은 더욱 진보하여 Azure에서 실행되는 수백만 개의 데이터베이스에서 완성도가 높아졌습니다. 적용된 자동 조정 작업이 자동으로 검증되어 워크로드 성능이 긍정적으로 향상됩니다. 회귀된 성능 권장 사항은 동적으로 감지되어 즉시 되돌려집니다. 기록된 조정 기록을 통해 각 Azure SQL Database의 성능 조정을 명확히 추적할 수 있습니다.

![자동 조정 작동 방식](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

자동 조정은 SQL Server 자동 조정 엔진과 핵심 논리를 공유 하는 Azure SQL Database. 기본 제공 인텔리전스 메커니즘에 대한 추가 기술 정보는 [SQL Server 자동 조정](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)을 참조하세요.

자동 조정이 작동하는 방법과 일반적인 사용 시나리오에 대한 개요는 다음에 포함된 비디오를 참조하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-automatic-tuning/player]

## <a name="enable-automatic-tuning"></a>자동 조정 사용

Azure Portal 또는 [ALTER database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) t-sql 문을 사용 하 여 [단일 및 풀링된 데이터베이스에 대해 자동 조정을 사용 하도록 설정할](sql-database-automatic-tuning-enable.md) 수 있습니다. [ALTER database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-mi-current) t-sql 문을 사용 하 여 관리 되는 인스턴스 배포에서 인스턴스 데이터베이스에 대해 자동 조정을 사용 하도록 설정 합니다.

## <a name="automatic-tuning-options"></a>자동 조정 옵션

Azure SQL Database에서 사용 가능한 자동 조정 옵션은 다음과 같습니다.

| 자동 조정 옵션 | 단일 데이터베이스 및 풀링된 데이터베이스 지원 | 인스턴스 데이터베이스 지원 |
| :----------------------------- | ----- | ----- |
| **인덱스 만들기** -작업의 성능을 향상 시키고 인덱스를 만들며 쿼리 성능이 향상 되었는지 자동으로 확인할 수 있는 인덱스를 식별 합니다. | yes | 예 |
| **DROP INDEX** -고유 인덱스를 제외한 매일 중복 인덱스와 중복 인덱스를 식별 하 고, 오랜 시간 (> 90 일) 동안 사용 되지 않은 인덱스를 식별 합니다. 이 옵션은 파티션 전환 및 인덱스 힌트를 사용하는 애플리케이션과 호환되지 않습니다. Premium 및 중요 비즈니스용 서비스 계층에서는 사용 하지 않는 인덱스를 삭제할 수 없습니다. | yes | 예 |
| **마지막으로 성공한 계획 강제 적용** (자동 계획 수정)-이전 양호한 계획 보다 느린 실행 계획을 사용 하 여 SQL 쿼리를 식별 하 고 회귀 된 계획 대신 마지막으로 알려진 좋은 계획을 사용 하 여 쿼리 합니다. | yes | yes |

### <a name="automatic-tuning-for-single-and-pooled-databases"></a>단일 및 풀링된 데이터베이스에 대 한 자동 조정

단일 및 풀링된 데이터베이스에 대 한 자동 조정에서는 **인덱스 만들기**, **DROP INDEX**및 **마지막으로 성공한 계획** 데이터베이스 관리자 권장 사항을 사용 하 여 데이터베이스 성능을 최적화 합니다. 자세한 내용은 Azure Portal, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction)및 [REST API](https://docs.microsoft.com/rest/api/sql/serverautomatictuning)의 [데이터베이스 관리자 권장 사항](sql-database-advisor-portal.md)을 참조 하세요.

Azure Portal를 사용 하 여 튜닝 권장 구성을 수동으로 적용 하거나 자동 튜닝을 통해 조정 권장 구성을 자율적으로 적용할 수 있습니다. 시스템에서 조정 권장 사항이 자율적으로 적용되도록 하는 경우 좋은 점은 워크로드 성능에 긍정적인 이득이 있는지 자동으로 확인하고, 뛰어난 성능 향상이 감지되지 않는 경우에는 조정 권장 사항을 자동으로 되돌립니다. 자주 실행되지 않는 조정 권장 사항의 영향을 받는 쿼리에 대한 유효성 검사 단계는 최대 72시간이 소요될 수 있습니다.

T-sql을 통해 튜닝 권장 사항을 적용 하는 경우 자동 성능 유효성 검사 및 반전 메커니즘을 사용할 수 없습니다. 이러한 방식으로 적용 되는 권장 사항은 활성 상태로 유지 되며 24-48 시간에 대 한 튜닝 권장 사항 목록에 표시 됩니다. 시스템에서 자동으로 인출 합니다. 권장 사항을 더 일찍 제거 하려면 Azure Portal에서 취소할 수 있습니다.

자동 조정 옵션은 데이터베이스당 별도로 설정 또는 해제하거나 SQL Database 서버에서 구성하고 서버에서 설정을 상속하는 모든 데이터베이스에서 적용할 수 있습니다. SQL Database 서버는 자동 조정 설정에 대해 Azure 기본값을 상속할 수 있습니다. 현재 Azure에서는 기본적으로 FORCE_LAST_GOOD_PLAN 및 CREATE_INDEX는 사용하고 DROP_INDEX는 사용하지 않도록 설정됩니다.

> [!IMPORTANT]
> 3 2020 월을 기준으로 Azure 기본값 자동 조정에 대 한 변경 내용은 다음과 같이 적용 됩니다.
>
> - 새 Azure 기본값은 FORCE_LAST_GOOD_PLAN = enabled, CREATE_INDEX = disabled 및 DROP_INDEX = disabled입니다.
> - 자동 조정 기본 설정이 없는 기존 서버는 새 Azure 기본값을 상속 하도록 자동으로 구성 됩니다. 이는 정의 되지 않은 상태에서 자동 조정을 위한 서버 설정이 현재 있는 모든 고객에 게 적용 됩니다.
> - 새 서버를 만들 때 새로 만든 서버가 자동으로 새 Azure 기본값을 상속 하도록 구성 됩니다 (이전에는 자동 튜닝 구성이 정의 되지 않은 상태에 있는 경우와 다름).

서버에서 자동 조정 옵션을 구성 하 고 부모 서버에 속하는 데이터베이스에 대 한 설정을 상속 하는 것은 많은 수의 데이터베이스에 대 한 자동 튜닝 옵션의 관리를 간소화 하므로 자동 조정을 구성 하는 데 권장 되는 방법입니다.

자동 조정 권장 사항에 대 한 전자 메일 알림 작성에 대 한 자세한 내용은 [자동 조정에 대 한 전자 메일 알림](sql-database-automatic-tuning-email-notifications.md)을 참조 하세요.

### <a name="automatic-tuning-for-instance-databases"></a>인스턴스 데이터베이스 자동 조정

관리 되는 인스턴스 배포의 인스턴스 데이터베이스 자동 튜닝은 **마지막으로 성공한 요금제**만 지원 합니다. T-sql을 통한 자동 조정 옵션을 구성 하는 방법에 대 한 자세한 내용은 자동 [조정에서 자동 계획 수정](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) 및 [자동 계획 수정](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning?view=sql-server-ver15#automatic-plan-correction)을 도입 하는 방법을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- 자동 조정에 사용 되는 기본 제공 인텔리전스에 대 한 자세한 내용은 [AZURE SQL 데이터베이스](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/)를 튜닝 하는 인공 지능을 참조 하세요.
