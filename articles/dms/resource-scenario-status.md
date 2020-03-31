---
title: 데이터베이스 마이그레이션 시나리오 상태
titleSuffix: Azure Database Migration Service
description: Azure 데이터베이스 마이그레이션 서비스에서 지원하는 마이그레이션 시나리오의 상태에 대해 알아봅니다.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/05/2019
ms.openlocfilehash: 9652b78674d6a6b905eb049564d1b17cdc7c17b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254925"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Azure 데이터베이스 마이그레이션 서비스에서 지원하는 마이그레이션 시나리오의 상태

Azure 데이터베이스 마이그레이션 서비스는 오프라인(일회성) 및 온라인(연속 동기화) 마이그레이션모두에 대해 다양한 마이그레이션 시나리오(원본/대상 쌍)를 지원하도록 설계되었습니다. Azure 데이터베이스 마이그레이션 서비스에서 제공하는 시나리오 범위는 시간이 지남에 따라 확장됩니다. 새로운 시나리오를 정기적으로 추가되고 있습니다. 이 문서에서는 Azure Database 마이그레이션 서비스에서 현재 지원되는 마이그레이션 시나리오와 각 시나리오의 상태(개인 미리 보기, 공개 미리 보기 또는 일반 가용성)를 식별합니다.

## <a name="offline-versus-online-migrations"></a>오프라인 및 온라인 마이그레이션

Azure 데이터베이스 마이그레이션 서비스를 사용하면 오프라인 또는 온라인 마이그레이션을 수행할 수 있습니다. *오프라인* 마이그레이션을 사용하는 경우 애플리케이션 가동 중지 시간이 마이그레이션 시작과 동시에 시작됩니다. 마이그레이션이 완료될 때 새 환경으로 축소하는 데 필요한 시간으로 가동 중지 시간을 제한하려면 *온라인* 마이그레이션을 사용합니다. 오프라인 마이그레이션을 테스트하여 가동 중지 시간이 허용되는지 여부를 확인하는 것이 좋습니다. 그렇지 않은 경우 온라인 마이그레이션을 수행합니다.

## <a name="migration-scenario-status"></a>마이그레이션 시나리오 상태

Azure 데이터베이스 마이그레이션 서비스에서 지원하는 마이그레이션 시나리오의 상태는 시간에 따라 다릅니다. 일반적으로 시나리오는 개인 **미리 보기에서**먼저 릴리스됩니다. 비공개 미리 보기에 참여하려면 고객이 [DMS 미리 보기 사이트를](https://aka.ms/dms-preview)통해 후보를 제출해야 합니다. 비공개 미리 보기 후 시나리오 상태가 **공용 미리 보기로**변경됩니다. Azure 데이터베이스 마이그레이션 서비스 사용자는 사용자 인터페이스에서 직접 공개 미리 보기에서 마이그레이션 시나리오를 시도할 수 있습니다. 등록이 필요하지 않습니다.  그러나 공개 미리 보기의 마이그레이션 시나리오는 모든 지역에서 제공되지 않을 수 있으며 최종 릴리스 전에 추가 변경이 필요할 수 있습니다. 공개 미리 보기 후 시나리오 상태가 **일반적으로 가용성으로 변경됩니다.** 일반 공급 상태(GA)는 최종 릴리스 상태이며 모든 사용자가 기능을 완전하고 액세스할 수 있습니다.

## <a name="migration-scenario-support"></a>마이그레이션 시나리오 지원

다음 표는 Azure 데이터베이스 마이그레이션 서비스를 사용할 때 지원되는 마이그레이션 시나리오를 보여 주며 있습니다.

> [!NOTE]
> 아래에 지원되는 시나리오가 사용자 인터페이스 내에 나타나지 않으면 [Azure Database 마이그레이션](mailto:AskAzureDatabaseMigrations@service.microsoft.com) 요청 별칭에 문의하여 자세한 정보를 확인하십시오.

> [!IMPORTANT]
> Azure 데이터베이스 마이그레이션 서비스에서 현재 지원 되는 모든 시나리오를 개인 미리 보기에서 보려면 [DMS 미리 보기 사이트를](https://aka.ms/dms-preview)참조 하십시오.

### <a name="offline-one-time-migration-support"></a>오프라인(일회성) 마이그레이션 지원

다음 표에서는 오프라인 마이그레이션에 대한 Azure Database Migration Service 지원을 보여 줍니다.

| 대상  | 원본 | 고객 지원팀 | 상태 |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | GA |
|   | RDS SQL |  |  |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL |  |  |
|   | Oracle |  |   |
| **Azure SQL VM** | SQL Server | ✔ | GA |
|   | Oracle |   |   |
| **Azure 코스모스 DB** | MongoDB | ✔ | GA |
| **Azure DB for MySQL** | MySQL |   |   |
|   | RDS MySQL |   |   |
| **PostgreSQL용 Azure DB** | PostgreSQL |  |
|  | RDS PostgreSQL |   |   |

### <a name="online-continuous-sync-migration-support"></a>온라인(연속 동기화) 마이그레이션 지원

다음 표에서는 온라인 마이그레이션에 대한 Azure Database Migration Service 지원을 보여 줍니다.

| 대상  | 원본 | 고객 지원팀 | 상태 |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle | ✔ | 프라이빗 미리 보기 |
| **Azure SQL VM** | SQL Server |   |   |
|   | Oracle  |  |  |
| **Azure 코스모스 DB** | MongoDB | ✔ | GA |
| **Azure DB for MySQL** | MySQL | ✔ | GA |
|   | RDS MySQL | ✔ | GA |
| **PostgreSQL용 Azure DB** | PostgreSQL | ✔ | GA |
|   | RDS PostgreSQL | ✔ | GA |
|   | Oracle | ✔ | 퍼블릭 미리 보기 |

## <a name="next-steps"></a>다음 단계

Azure 데이터베이스 마이그레이션 서비스 및 지역 가용성에 대한 개요는 [Azure 데이터베이스 마이그레이션 서비스](dms-overview.md)입니다.
