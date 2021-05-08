---
title: 데이터베이스 마이그레이션 시나리오 상태
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service에서 지원하는 마이그레이션 시나리오 상태에 대해 알아봅니다.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 07/08/2020
ms.openlocfilehash: 35ef2ea3f03ca86aea34f736de4317d75a44ae59
ms.sourcegitcommit: dd425ae91675b7db264288f899cff6add31e9f69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2021
ms.locfileid: "108330515"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Azure Database Migration Service에서 지원하는 마이그레이션 시나리오 상태

Azure Database Migration Service는 오프라인(일회성) 마이그레이션과 온라인(연속 동기화) 마이그레이션 모두를 위해 다른 마이그레이션 시나리오(원본/대상 쌍)를 지원하도록 디자인되었습니다. Azure Database Migration Service가 제공하는 시나리오 범위는 시간이 지날수록 확장되고 있습니다. 새로운 시나리오를 정기적으로 추가되고 있습니다. 이 문서는 현재 Azure Database Migration Service에서 지원하는 마이그레이션 시나리오와 상태(프라이빗 미리 보기, 퍼블릭 미리 보기 또는 일반 공급)를 설명합니다.

## <a name="offline-versus-online-migrations"></a>오프라인 및 온라인 마이그레이션

Azure Database Migration Service를 사용하면, 오프라인 또는 온라인 마이그레이션을 수행할 수 있습니다. *오프라인* 마이그레이션을 사용하는 경우 애플리케이션 가동 중지 시간이 마이그레이션 시작과 동시에 시작됩니다. 마이그레이션이 완료될 때 새 환경으로 이동하는 데 필요한 시간 동안만 가동 중지하도록 제한하려면 ‘온라인’ 마이그레이션을 사용합니다. 오프라인 마이그레이션의 경우 테스트를 통해 가동 중지 시간이 용납 가능한 수준인지 확인하고, 허용할 수 없다면 온라인 마이그레이션을 수행하는 것이 좋습니다.

## <a name="migration-scenario-status"></a>마이그레이션 시나리오 상태

Azure Database Migration Service에서 지원하는 마이그레이션 시나리오 상태는 시간에 따라 달라집니다. 일반적으로 시나리오는 처음에 **프라이빗 미리 보기** 로 릴리스됩니다. 프라이빗 미리 보기 후에는 시나리오 상태가 **퍼블릭 미리 보기** 로 변경됩니다. Azure Database Migration Service 사용자는 직접 사용자 인터페이스에서 퍼블릭 미리 보기로 마이그레이션 시나리오를 사용해 볼 수 있습니다. 가입은 필요하지 않습니다.  그러나 모든 지역에서 퍼블릭 미리 보기로 마이그레이션 시나리오를 사용할 수 있는 것은 아니며, 최종 릴리스 전에 추가 변경 사항이 발생할 수 있습니다. 퍼블릭 미리 보기 후에는 시나리오 상태가 **일반 공급** 으로 변경됩니다. GA(일반 공급)는 최종 릴리스 상태이며 기능이 완전히 갖춰지고 모든 사용자가 액세스할 수 있습니다.

## <a name="migration-scenario-support"></a>마이그레이션 시나리오 지원

다음 표에서는 Azure Database Migration Service를 사용 시에 지원되는 마이그레이션 시나리오를 보여 줍니다.

> [!NOTE]
> 아래에 지원되는 것으로 표시되는 시나리오가 사용자 인터페이스에 나타나지 않으면 [Azure Database Migrations에 문의](mailto:AskAzureDatabaseMigrations@service.microsoft.com)하여 추가 정보를 요청하세요.

### <a name="offline-one-time-migration-support"></a>오프라인(일회성) 마이그레이션 지원

다음 표에서는 오프라인 마이그레이션에 대한 Azure Database Migration Service 지원을 보여 줍니다.

| 대상  | 원본 | 지원 | 상태 |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | GA |
|   | RDS SQL | X |  |
|   | Oracle | X |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL | X |  |
|   | Oracle | X |   |
| **Azure SQL VM** | SQL Server | ✔ | GA |
|   | Oracle | X |   |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **MySQL용 Azure DB** | MySQL | ✔ |   |
|   | RDS MySQL | X |   |
| **Azure DB for PostgreSQL - 단일 서버** | PostgreSQL | X |
|  | RDS PostgreSQL | X |   |
| **Azure DB for PostgreSQL - 하이퍼스케일(Citus)** | PostgreSQL | X |
|  | RDS PostgreSQL | X |   |

### <a name="online-continuous-sync-migration-support"></a>온라인(연속 동기화) 마이그레이션 지원

다음 표에서는 온라인 마이그레이션에 대한 Azure Database Migration Service 지원을 보여 줍니다.

| 대상  | 원본 | 지원 | 상태 |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | X |  |
|   | RDS SQL | X |  |
|   | Oracle | X |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL | X |  |
|   | Oracle | X |  |
| **Azure SQL VM** | SQL Server | X |   |
|   | Oracle  | X |  |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **MySQL용 Azure DB** | MySQL | ✔ | GA |
|   | RDS MySQL | ✔ | GA |
| **Azure DB for PostgreSQL - 단일 서버** | PostgreSQL | ✔ | GA |
|   | Azure DB for PostgreSQL - 단일 서버 | ✔ | GA |
|   | RDS PostgreSQL | ✔ | GA |
|   | Oracle | ✔ | 퍼블릭 미리 보기(2021년 5월 1일 이후에는 사용되지 않음) |
| **Azure DB for PostgreSQL - 하이퍼스케일(Citus)** | PostgreSQL | ✔ | GA |
|   | RDS PostgreSQL | ✔ | GA |

> [!IMPORTANT]
> "Oracle을 Azure Database for PostgreSQL로" 마이그레이션 시나리오(현재 미리 보기 상태)는 2021년 5월 1일 이후에는 더 이상 사용할 수 없습니다. Ora2pg와 같은 대체 도구를 통해 계속 지원을 제공하고 Oracle에서 PostgreSQL로의 마이그레이션을 위한 최상의 마이그레이션 환경을 제공할 예정입니다. 마이그레이션 모범 사례는 [Oracle을 Azure Database for PostgreSQL로 마이그레이션 가이드](https://aka.ms/OracletoPGguide)를 참조하세요.


## <a name="next-steps"></a>다음 단계

Azure Database Migration Service 및 지역별 가용성에 대한 개요는 [Azure Database Migration Service란?](dms-overview.md) 문서를 참조하세요.
