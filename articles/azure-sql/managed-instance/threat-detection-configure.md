---
title: Advanced Threat Protection 구성
titleSuffix: Azure SQL Managed Instance
description: Advanced Threat Protection은 Azure SQL Managed Instance의 잠재적인 보안 위협을 표시하는 비정상적인 데이터베이스 활동을 검색합니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 12/01/2020
ms.openlocfilehash: 69bebcf872f55055117acf5cef410d1f89eafe34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96446903"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-managed-instance"></a>Azure SQL Managed Instance에서 Advanced Threat Protection 구성
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Advanced Threat Protection](../database/threat-detection-overview.md)은 [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md)에서 비정상적이며 잠재적으로 유해한 데이터베이스 액세스 또는 악용 시도를 표시하는 비정상 활동을 검색합니다. Advanced Threat Protection은 **잠재적인 SQL 삽입**, **비정상적인 위치 또는 데이터 센터에서 액세스**, **생소한 보안 주체 또는 잠재적으로 위험한 애플리케이션의 액세스**, **무차별 SQL 자격 증명 대입 공격** 을 식별할 수 있습니다. 세부 정보는 [Advanced Threat Protection 경고](../database/threat-detection-overview.md#alerts)를 참조하세요.

[이메일 알림](../database/threat-detection-overview.md#explore-detection-of-a-suspicious-event) 또는 [Azure Portal](../database/threat-detection-overview.md#explore-alerts-in-the-azure-portal)을 통해 탐지된 위협에 대한 알림을 받을 수 있습니다.

[Advanced Threat Protection](../database/threat-detection-overview.md)은 고급 SQL 보안 기능 통합 패키지인 [Azure Defender for SQL](../database/azure-defender-for-sql.md) 제품에 포함되어 있습니다. Azure Defender for SQL 중앙 포털을 통해 Advanced Threat Protection에 액세스하고 관리할 수 있습니다.

##  <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. 보호할 SQL Managed Instance의 인스턴스 구성 페이지로 이동합니다. **보안** 에서 **Security Center** 를 선택합니다.
3. Azure Defender for SQL 구성 페이지에서
   - Azure Defender for SQL을 **활성화** 합니다.
   - 비정상적인 데이터베이스 활동 감지 시 보안 경고를 수신하도록 이메일 주소로 **알림 보내기** 를 구성합니다.
   - 비정상적인 위협 감사 레코드가 저장되는 **Azure Storage 계정** 을 선택합니다.
   - 구성할 **Advanced Threat Protection 형식** 을 선택합니다. [Advanced Threat Protection 경고](../database/threat-detection-overview.md)에 대해 자세히 알아봅니다.
4. **저장** 을 클릭하여 새롭거나 업데이트된 Azure Defender for SQL 정책을 저장합니다.

   :::image type="content" source="../database/media/azure-defender-for-sql/set-up-advanced-threat-protection-mi.png" alt-text="Advanced Threat Protection 설정":::

## <a name="next-steps"></a>다음 단계

- [Advanced Threat Protection](../database/threat-detection-overview.md)에 대해 자세히 알아봅니다.
- 관리되는 인스턴스에 대한 자세한 내용은 [Azure SQL Managed Instance 정의](sql-managed-instance-paas-overview.md)를 참조하세요.
- [Azure SQL Database에 대한 Advanced Threat Protection](../database/threat-detection-configure.md)에 대해 자세히 알아보세요.
- [SQL Managed Instance 감사](./auditing-configure.md)에 대해 자세히 알아봅니다.
- [Azure Security Center](../../security-center/security-center-introduction.md)에 대해 자세히 알아봅니다.