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
ms.date: 03/30/2020
ms.openlocfilehash: 7488fd5f8a572788933856f03bb0ad4351885704
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80518220"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Azure SQL Database에서 자동 조정

Azure SQL Database 자동 튜닝은 AI 및 기계 학습을 기반으로 하는 지속적인 성능 조정을 통해 최고 성능과 안정적인 워크로드를 제공합니다.

자동 조정은 기본 제공 인텔리전스를 사용하여 데이터베이스에서 실행된 쿼리를 지속적으로 모니터링하고 해당 성능을 자동으로 개선하는 완전히 관리되는 지능형 성능 서비스입니다. 이것은 변화하는 워크로드에 동적으로 데이터베이스를 적용하고 튜닝 권장 사항을 적용함으로써 달성됩니다. 자동 조정은 AI를 통해 Azure의 모든 데이터베이스에서 수평적으로 학습하고 동적으로 조정 작업을 개선합니다. 자동 튜닝을 통해 데이터베이스가 오래 실행될수록 성능이 향상됩니다.

Azure SQL Database 자동 튜닝은 안정적이고 성능이 우수한 데이터베이스 워크로드를 제공할 수 있는 가장 중요한 기능 중 하나일 수 있습니다.

## <a name="what-can-automatic-tuning-do-for-you"></a>자동 튜닝이 수행할 수 있는 일

- Azure SQL 데이터베이스의 자동화된 성능 조정
- 성능 향상의 자동화된 확인
- 자동화된 롤백 및 자체 수정
- 조정 기록
- 수동 배포를 위한 조정 작업 T-SQL 스크립트
- 자동 관리 워크로드 성능 모니터링
- 수십만 개의 데이터베이스에서 기능 확장
- DevOps 리소스와 총 소유 비용에 미치는 긍정적인 영향

## <a name="safe-reliable-and-proven"></a>안전하고 신뢰할 수 있으며 검증됨

Azure SQL Database의 데이터베이스에 적용되는 튜닝 작업은 가장 격렬한 워크로드의 성능에 완전히 안전합니다. 시스템은 사용자 워크로드를 방해하지 않도록 주의하여 설계되었습니다. 자동 조정 권장 사항은 사용률이 낮은 경우에만 적용됩니다. 시스템 또한 워크로드 성능을 보호하기 위해 일시적으로 자동 조정 작업을 사용하지 않도록 설정할 수도 있습니다. 이 경우 Azure 포털에 "시스템에서 사용 안 함" 메시지가 표시됩니다. 자동 조정 작업은 리소스 우선 순위가 가장 높은 워크로드를 인식합니다.

자동 조정 메커니즘은 더욱 진보하여 Azure에서 실행되는 수백만 개의 데이터베이스에서 완성도가 높아졌습니다. 적용된 자동 조정 작업이 자동으로 검증되어 워크로드 성능이 긍정적으로 향상됩니다. 회귀된 성능 권장 사항은 동적으로 감지되어 즉시 되돌려집니다. 기록된 조정 기록을 통해 각 Azure SQL Database의 성능 조정을 명확히 추적할 수 있습니다.

![자동 조정 작동 방식](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Azure SQL Database 자동 튜닝은 SQL Server 자동 튜닝 엔진과 핵심 논리를 공유합니다. 기본 제공 인텔리전스 메커니즘에 대한 추가 기술 정보는 [SQL Server 자동 조정](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)을 참조하세요.

자동 조정이 작동하는 방법과 일반적인 사용 시나리오에 대한 개요는 다음에 포함된 비디오를 참조하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-automatic-tuning/player]

## <a name="enable-automatic-tuning"></a>자동 조정 사용

Azure [포털에서 단일 및 풀링된 데이터베이스에 대해 자동 튜닝을 사용하도록 설정하거나](sql-database-automatic-tuning-enable.md) [ALTER 데이터베이스](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) T-SQL 문을 사용할 수 있습니다. [ALTER](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-mi-current) 데이터베이스 T-SQL 문을 사용하여 관리되는 인스턴스 배포에서 인스턴스 데이터베이스에 대한 자동 튜닝을 사용하도록 설정합니다.

## <a name="automatic-tuning-options"></a>자동 조정 옵션

Azure SQL Database에서 사용 가능한 자동 조정 옵션은 다음과 같습니다.

| 자동 튜닝 옵션 | 단일 데이터베이스 및 풀링된 데이터베이스 지원 | 인스턴스 데이터베이스 지원 |
| :----------------------------- | ----- | ----- |
| **인덱스 만들기** - 워크로드의 성능을 향상시키고 인덱스를 생성하며 쿼리 성능이 향상되었는지 자동으로 확인할 수 있는 인덱스를 식별합니다. | 예 | 예 |
| **DROP INDEX** - 고유 인덱스와 오랜 시간 동안 사용되지 않은 인덱스(>90일)를 제외한 매일 중복 및 중복 인덱스를 식별합니다. 이 옵션은 파티션 전환 및 인덱스 힌트를 사용하는 애플리케이션과 호환되지 않습니다. 프리미엄 및 비즈니스 중요 서비스 계층에는 사용하지 않는 인덱스 삭제가 지원되지 않습니다. | 예 | 예 |
| **FORCE LAST GOOD PLAN(자동** 계획 수정) - 이전 의 양호한 계획보다 느린 실행 계획을 사용하여 SQL 쿼리를 식별하고 회귀된 계획 대신 마지막으로 알려진 올바른 계획을 사용하는 쿼리를 식별합니다. | 예 | 예 |

### <a name="automatic-tuning-for-single-and-pooled-databases"></a>단일 및 풀링된 데이터베이스를 위한 자동 튜닝

단일 및 풀링된 데이터베이스에 대한 자동 튜닝은 **CREATE INDEX,** **DROP INDEX**및 FORCE LAST **GOOD PLAN** 데이터베이스 어드바이저 권장 사항을 사용하여 데이터베이스 성능을 최적화합니다. 자세한 내용은 [Azure 포털,](sql-database-advisor-portal.md) [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction)및 REST [API의](https://docs.microsoft.com/rest/api/sql/serverautomatictuning)데이터베이스 관리자 권장 사항을 참조하십시오.

Azure 포털을 사용하여 수동으로 튜닝 권장 사항을 적용하거나 자동 튜닝이 튜닝 권장 사항을 자율적으로 적용하도록 할 수 있습니다. 시스템에서 조정 권장 사항이 자율적으로 적용되도록 하는 경우 좋은 점은 워크로드 성능에 긍정적인 이득이 있는지 자동으로 확인하고, 뛰어난 성능 향상이 감지되지 않는 경우에는 조정 권장 사항을 자동으로 되돌립니다. 자주 실행되지 않는 조정 권장 사항의 영향을 받는 쿼리에 대한 유효성 검사 단계는 최대 72시간이 소요될 수 있습니다.

T-SQL을 통해 튜닝 권장 사항을 적용하는 경우 자동 성능 유효성 검사 및 반전 메커니즘을 사용할 수 없습니다. 이러한 방식으로 적용된 권장 사항은 활성 상태로 유지되며 24-48시간 동안 조정 권장 사항 목록에 표시됩니다. 시스템이 자동으로 철회되기 전에. 권장 사항을 더 빨리 제거하려면 Azure 포털에서 삭제할 수 있습니다.

자동 조정 옵션은 데이터베이스당 별도로 설정 또는 해제하거나 SQL Database 서버에서 구성하고 서버에서 설정을 상속하는 모든 데이터베이스에서 적용할 수 있습니다. SQL Database 서버는 자동 튜닝 설정에 대한 Azure 기본값을 상속할 수 있습니다. 현재 Azure에서는 기본적으로 FORCE_LAST_GOOD_PLAN 및 CREATE_INDEX는 사용하고 DROP_INDEX는 사용하지 않도록 설정됩니다.

> [!IMPORTANT]
> 3월 현재 자동 튜닝을 위한 Azure 기본값에 대한 2020변경사항은 다음과 같이 적용됩니다.
>
> - 새 Azure 기본값은 FORCE_LAST_GOOD_PLAN = 활성화되고 CREATE_INDEX = 사용 안 함 및 DROP_INDEX = 사용 안 함입니다.
> - 자동 튜닝 기본 설정이 구성된 기존 서버는 새 Azure 기본값을 상속하도록 자동으로 구성됩니다. 이는 현재 정의되지 않은 상태에서 자동 튜닝을 위한 서버 설정을 가지고 있는 모든 고객에게 적용됩니다.
> - 생성된 새 서버는 새 Azure 기본값을 상속하도록 자동으로 구성됩니다(새 서버 를 만들 때 자동 튜닝 구성이 정의되지 않은 상태일 때이전과 는 달리).

서버에서 자동 튜닝 옵션을 구성하고 상위 서버에 속한 데이터베이스에 대한 설정을 상속하는 것은 많은 수의 데이터베이스에 대한 자동 튜닝 옵션 관리를 단순화하기 때문에 자동 튜닝을 구성하는 데 권장되는 방법입니다.

자동 튜닝 권장 사항에 대한 전자 메일 알림을 빌드하는 방법에 대해 알아보려면 [자동 튜닝에 대한 전자 메일 알림을](sql-database-automatic-tuning-email-notifications.md)참조하십시오.

### <a name="automatic-tuning-for-instance-databases"></a>인스턴스 데이터베이스에 대한 자동 튜닝

관리되는 인스턴스 배포의 인스턴스 데이터베이스에 대한 자동 튜닝은 **FORCE LAST GOOD PLAN.** T-SQL을 통한 자동 튜닝 옵션 구성에 대한 자세한 내용은 [자동 조정을 참조하여 자동 계획 수정](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) 및 자동 계획 [수정을](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning?view=sql-server-ver15#automatic-plan-correction)소개합니다.

## <a name="next-steps"></a>다음 단계

- 자동 튜닝에 사용되는 기본 제공 인텔리전스에 대해 자세히 알아보려면 [Azure SQL 데이터베이스를 조정하는 인공 지능을](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/)참조하십시오.
- 자동 튜닝이 이 아래에서 작동하는 방법을 알아보려면 [Microsoft Azure SQL Database에서 수백만 개의 데이터베이스를 자동으로 인덱싱하는](https://www.microsoft.com/en-us/research/uploads/prod/2019/02/autoindexing_azuredb.pdf)것을 참조하십시오.
