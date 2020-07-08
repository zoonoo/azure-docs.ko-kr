---
title: 고급 위협 보호
titleSuffix: Azure SQL Database, SQL Managed Instance, & Azure Synapse Analytics
description: Advanced Threat Protection은 Azure SQL Database, Azure SQL Managed Instance 및 Azure Synapse Analytics에서 잠재적 보안 위협을 나타내는 비정상적인 데이터베이스 활동을 검색 합니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: 822d4267d5b163ddda50302e2caea5c3ade3b7c4
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85985374"
---
# <a name="advanced-threat-protection-for-azure-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Azure SQL Database, SQL Managed Instance 및 Azure Synapse 분석에 대 한 고급 위협 방지
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Azure SQL Database](sql-database-paas-overview.md), [azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) 및 [azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 에 대 한 Advanced Threat Protection은 비정상적인 활동을 감지 하 여 데이터베이스에 액세스 하거나 악용 하려는 비정상적인 시도를 감지 합니다.

Advanced Threat Protection은 고급 SQL 보안 기능을 위한 통합 패키지인 [고급 데이터 보안](advanced-data-security.md) 제품의 일부입니다. 중앙의 SQL ADS 포털을 통해 Advanced Threat Protection에 액세스하고 관리할 수 있습니다.

## <a name="overview"></a>개요

Advanced Threat Protection은 비정상적인 활동에 대 한 보안 경고를 제공 하 여 고객이 발생할 때 잠재적 위협을 감지 하 고 대응할 수 있도록 하는 새로운 보안 계층을 제공 합니다. 사용자는 의심스러운 데이터베이스 활동, 잠재적 취약성 및 SQL 삽입 공격뿐만 아니라 비정상적인 데이터베이스 액세스 및 쿼리 패턴에 대한 경고도 받습니다. Advanced Threat Protection은 경고를 [Azure Security Center](https://azure.microsoft.com/services/security-center/)와 통합 하 여 의심 스러운 활동의 세부 정보를 포함 하 고 위협을 조사 하 고 완화 하는 방법에 대 한 작업을 권장 합니다. Advanced Threat Protection을 사용 하면 보안 전문가가 되거나 고급 보안 모니터링 시스템을 관리할 필요 없이 데이터베이스에 대 한 잠재적 위협을 쉽게 해결할 수 있습니다.

전체 조사 환경에서는 감사를 사용 하도록 설정 하 여 Azure storage 계정의 감사 로그에 데이터베이스 이벤트를 기록 하는 것이 좋습니다.  감사를 사용 하도록 설정 하려면 Azure SQL Database 및 azure [SQL Managed Instance 감사](../managed-instance/auditing-configure.md) [에 대 한 감사](../../azure-sql/database/auditing-overview.md) 를 참조 하세요.

## <a name="alerts"></a>경고

Azure SQL Database용 Advanced Threat Protection은 비정상적이며 잠재적으로 유해한 데이터베이스 액세스 또는 악용 시도를 나타내는 비정상 활동을 탐지합니다. Azure SQL Database에 대 한 경고 목록은 [Azure Security Center의 SQL Database 및 SQL Data Warehouse에 대 한 경고](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-sql-db-and-warehouse)를 참조 하세요.

## <a name="explore-detection-of-a-suspicious-event"></a>의심 스러운 이벤트 검색 살펴보기

비정상적인 데이터베이스 활동이 감지되면 메일 알림을 받습니다. 메일에는 비정상적인 활동의 특징, 데이터베이스 이름, 서버 이름, 애플리케이션 이름, 이벤트 시간을 비롯한 의심스러운 보안 이벤트에 대한 정보가 제공됩니다. 또한 전자 메일에는 가능한 원인에 대한 정보 및 데이터베이스에 대한 잠재적인 위협을 조사하고 완화시키기 위해 권장되는 조치가 제공됩니다.

![비정상적인 활동 보고서](./media/threat-detection-overview/anomalous_activity_report.png)

1. 전자 메일의 **최근 SQL 경고 보기** 링크를 클릭 하 여 Azure Portal를 시작 하 고 데이터베이스에서 감지 된 활성 위협에 대 한 개요를 제공 하는 Azure Security Center 경고 페이지를 표시 합니다.

   ![활동 위협](./media/threat-detection-overview/active_threats.png)

1. 특정 경고를 클릭하여 이 위협을 조사하고 향후 위협을 수정하기 위한 추가 세부 정보 및 조치를 확인합니다.

   예를 들어 SQL 삽입은 데이터 기반 애플리케이션 공격에 사용되는 인터넷 상의 가장 대표적인 웹 애플리케이션 보안 문제 중 하나입니다. 공격자는 애플리케이션의 취약성을 이용하여 악의적인 SQL 문을 애플리케이션 항목 필드에 삽입하고 데이터베이스의 데이터를 침범하거나 수정합니다. SQL 삽입 경고의 경우, 경고 세부 정보에 악용된 취약한 SQL 문이 포함되어 있습니다.

   ![특정 경고](./media/threat-detection-overview/specific_alert.png)

## <a name="explore-alerts-in-the-azure-portal"></a>Azure Portal에서 경고 살펴보기

Advanced Threat Protection은 경고를 [Azure security center](https://azure.microsoft.com/services/security-center/)와 통합 합니다. 데이터베이스 내의 라이브 SQL Advanced Threat Protection 타일과 Azure Portal의 SQL 광고 블레이드는 활성 위협의 상태를 추적 합니다.

경고 Azure Security Center 페이지를 시작 하 고 데이터베이스에서 감지 된 활성 SQL 위협에 대 한 개요를 보려면 **Advanced Threat Protection 경고** 를 클릭 합니다.

   ![Advanced Threat Protection 경고](./media/threat-detection-overview/threat_detection_alert.png)

   ![Advanced Threat Protection alert2](./media/threat-detection-overview/threat_detection_alert_atp.png)

## <a name="next-steps"></a>다음 단계

- [Azure Synapse & Azure SQL Database의 Advanced Threat Protection](threat-detection-configure.md)에 대해 자세히 알아보세요.
- [AZURE SQL Managed Instance의 Advanced Threat Protection](../managed-instance/threat-detection-configure.md)에 대해 자세히 알아보세요.
- [고급 데이터 보안](advanced-data-security.md)에 대해 자세히 알아보세요.
- [Azure SQL Database 감사](../../azure-sql/database/auditing-overview.md) 에 대 한 자세한 정보
- [Azure security center](https://docs.microsoft.com/azure/security-center/security-center-intro) 에 대 한 자세한 정보
- 가격 책정에 대 한 자세한 내용은 [Azure SQL Database 가격 책정 페이지](https://azure.microsoft.com/pricing/details/sql-database/) 를 참조 하세요.  
