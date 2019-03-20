---
title: 데이터베이스 마이그레이션 시나리오 상태 | Microsoft Docs
description: Azure Database Migration Service에서 지원하는 마이그레이션 시나리오 상태에 대해 알아봅니다.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: b07f36055f9c0690450e8d4ab5abacb8c84d9bf2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182543"
---
# <a name="status-of-migration-scenarios-supported-by-the-azure-database-migration-service"></a>Azure Database Migration Service에서 지원하는 마이그레이션 시나리오 상태
Azure Database Migration Service는 오프라인(일회성) 및 온라인(연속 동기화) 마이그레이션 둘 다에 대한 다양한 마이그레이션 시나리오(원본/대상 쌍)를 지원하도록 디자인되었습니다. Azure Database Migration Service가 제공하는 시나리오 범위는 시간에 따라 확장되고 있습니다. 새로운 시나리오를 정기적으로 추가되고 있습니다. 이 문서는 현재 Azure Database Migration Service에서 지원하는 마이그레이션 시나리오와 상태(비공개[또는 제한된] 미리 보기, 공개 미리 보기 또는 일반 공급)를 설명합니다.

## <a name="offline-versus-online-migrations"></a>오프라인 및 온라인 마이그레이션
Azure Database Migration Service를 사용하여 데이터베이스를 Azure에 마이그레이션할 때는 오프라인 또는 온라인 마이그레이션을 수행할 수 있습니다. *오프라인* 마이그레이션을 사용하는 경우 애플리케이션 가동 중지 시간이 마이그레이션 시작과 동시에 시작됩니다. *온라인* 마이그레이션의 경우에는 가동 중지 시간이 마이그레이션 완료 시 새 환경으로 전환하는 데 필요한 시간으로 제한됩니다. 오프라인 마이그레이션을 테스트하여 가동 중지 시간이 용납 가능한 수준인지 판단하고, 용납되지 않는다면 온라인 마이그레이션을 수행하는 것이 좋습니다.

## <a name="migration-scenario-status"></a>마이그레이션 시나리오 상태
Azure Database Migration Service에서 지원하는 각 마이그레이션 시나리오의 상태는 시간에 따라 달라집니다. 일반적으로 시나리오는 처음에 **비공개 미리 보기**로 릴리스되며, 이 기능을 활용하기 위해 고객은 [DMS 미리 보기 사이트](https://aka.ms/dms-preview)를 통해 추천서를 제출해야 합니다. 비공개 미리 보기가 완료되면 시나리오 상태가 **공개 미리 보기**로 변경됩니다. Azure Database Migration Service의 모든 사용자는 공개 미리 보기로 제공되는 마이그레이션 시나리오를 활용할 수 있습니다. 그러나 모든 지역에서 마이그레이션 시나리오를 사용할 수 있는 것은 아니며, 해당 기능이 최종 릴리스 전에 추가로 변경될 수 있습니다. 마이그레이션 시나리오가 최종 릴리스된 상태로 **일반 공급**되면 기능이 완성된 것이며 Azure Database Migration Service의 모든 사용자가 액세스할 수 있습니다. 

## <a name="migration-scenario-support"></a>마이그레이션 시나리오 지원

다음 표에서는 Azure Database Migration Service를 사용할 때 지원되는 마이그레이션 시나리오를 보여 줍니다.

> [!NOTE]
> 아래에 지원되는 것으로 표시되는 시나리오가 사용자 인터페이스 내에 나타나지 않으면 [데이터 마이그레이션 팀](mailto:datamigrationteam@microsoft.com)에 추가 정보를 요청하세요.

### <a name="offline-one-time-migration-support"></a>오프라인(일회성) 마이그레이션 지원
다음 표에서는 오프라인 마이그레이션에 대한 Azure Database Migration Service 지원을 보여 줍니다.

| 대상  | 원본 | 지원 |
| ------------- | ------------- | :-------------: |
| **Azure SQL DB**  | SQL Server | ✔ |
|   | RDS SQL  |  ✔ |
|   | Oracle  |   |
| **Azure SQL DB MI**  | SQL Server  | ✔ |
|   | RDS SQL  | ✔ |
|   | Oracle  | ✔  |
| **Azure SQL VM**  | SQL Server | ✔ |
|   | Oracle  |   |
| **Azure Cosmos DB**  | MongoDB | ✔ |
| **Azure DB for MySQL**  | MySQL |  |
|   | RDS MySQL  |  |
| **Azure DB for PostgreSQL**  | PostgreSQL |  |
|  | RDS PostgreSQL  |  |

### <a name="online-continuous-sync-migration-support"></a>온라인(연속 동기화) 마이그레이션 지원
다음 표에서는 온라인 마이그레이션에 대한 Azure Database Migration Service 지원을 보여 줍니다.

| 대상  | 원본 | 지원 |
| ------------- | ------------- | :-------------: |
| **Azure SQL DB**  | SQL Server | ✔ |
|   | RDS SQL  |   |
|   | Oracle  |  ✔ |
| **Azure SQL DB MI**  | SQL Server  | ✔ |
|   | RDS SQL  |  |
|   | Oracle  | ✔  |
| **Azure SQL VM**  | SQL Server  |   |
|   | Oracle  | ✔  |
| **Azure Cosmos DB**  | MongoDB  | ✔ |
| **Azure DB for MySQL**  | MySQL | ✔ |
|   | RDS MySQL  | ✔ |
| **Azure DB for PostgreSQL**  | PostgreSQL | ✔ |
|  | RDS PostgreSQL  | ✔ |

## <a name="next-steps"></a>다음 단계
Azure Database Migration Service 및 국가별 가용성에 대한 개요는 [Azure Database Migration Service란?](dms-overview.md) 문서를 참조하세요. 
