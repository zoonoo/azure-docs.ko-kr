---
title: Advanced Threat Protection 구성
titleSuffix: Azure SQL Managed Instance
description: Advanced Threat Protection은 Azure SQL Managed Instance의 데이터베이스에 대 한 잠재적인 보안 위협을 나타내는 비정상적인 데이터베이스 활동을 검색 합니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 09/21/2020
ms.openlocfilehash: d2ea14356bf85c795769f1d406f1571f36adaa38
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91617922"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-managed-instance"></a>Azure SQL Managed Instance에서 Advanced Threat Protection 구성
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[AZURE SQL Managed Instance](sql-managed-instance-paas-overview.md) 에 대 한 [Advanced Threat Protection](../database/threat-detection-overview.md) 은 비정상적인 활동을 감지 하 여 데이터베이스에 액세스 하거나 악용 하려는 비정상적인 시도를 감지 합니다. Advanced Threat Protection은 **잠재적 sql 삽입**, **비정상적인 위치 또는 데이터 센터에서 액세스**, **익숙하지 않은 사용자 또는 잠재적으로 유해한 응용 프로그램에서 액세스**, **무차별 암호 대입 SQL 자격 증명** 등을 식별할 수 있습니다. [advanced threat protection 경고](../database/threat-detection-overview.md#alerts)에서 자세한 정보를 참조 하세요.

[전자 메일 알림을](../database/threat-detection-overview.md#explore-detection-of-a-suspicious-event) 통해 검색 된 위협에 대 한 알림을 받거나 [Azure Portal](../database/threat-detection-overview.md#explore-alerts-in-the-azure-portal) 수 있습니다.

[Advanced Threat Protection](../database/threat-detection-overview.md) 은 고급 sql 보안 기능을 위한 통합 패키지인 [Sql 용 Azure Defender](../database/azure-defender-for-sql.md)  제공의 일부입니다. 고급 위협 방지는 SQL portal 용 중앙 Azure Defender를 통해 액세스 하 고 관리할 수 있습니다.

##  <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com)에 로그인 합니다. 
2. 보호할 SQL Managed Instance 인스턴스의 구성 페이지로 이동 합니다. **보안**아래에서 **security center**를 선택 합니다.
3. Azure Defender for SQL 구성 페이지에서
   - SQL 용 Azure Defender **를** 켭니다.
   - 비정상적인 데이터베이스 활동이 감지 되 면 보안 경고를 수신 하도록 **전자 메일 목록을** 구성 합니다.
   - 비정상적인 위협 감사 레코드가 저장되는 **Azure Storage 계정**을 선택합니다.
   - 구성할 **고급 위협 방지 유형을** 선택 합니다. [Advanced Threat Protection 경고](../database/threat-detection-overview.md)에 대해 자세히 알아보세요.
4. **저장** 을 클릭 하 여 새로운 또는 업데이트 된 SQL 정책 용 Azure Defender를 저장 합니다.

## <a name="next-steps"></a>다음 단계

- [Advanced Threat Protection](../database/threat-detection-overview.md)에 대해 자세히 알아보세요.
- 관리 되는 인스턴스에 대 한 자세한 내용은 [AZURE SQL Managed Instance 정의](sql-managed-instance-paas-overview.md)를 참조 하세요.
- [Azure SQL Database에 대 한 Advanced Threat Protection](../database/threat-detection-configure.md)에 대해 자세히 알아보세요.
- [SQL Managed Instance 감사](https://go.microsoft.com/fwlink/?linkid=869430)에 대해 자세히 알아보세요.
- [Azure security center](https://docs.microsoft.com/azure/security-center/security-center-intro)에 대해 자세히 알아보세요.
