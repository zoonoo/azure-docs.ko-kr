---
title: 위협 감지 - Azure SQL Database | Microsoft Docs
description: 위협 탐지는 단일 데이터베이스 또는 탄력적 풀에서 데이터베이스에 대한 잠재적인 보안 위협을 나타내는 비정상적인 데이터베이스 활동을 검색합니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 3f4a120e2aaf2925805bec26f721d5cfb4194bf1
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54041546"
---
# <a name="azure-sql-database-threat-detection-for-single-database"></a>Single Database에 대한 Azure SQL Database 위협 탐지

[SQL Database](sql-database-technical-overview.md) Single Database에 대한 Azure SQL [위협 탐지](sql-database-threat-detection-overview.md)는 데이터베이스에 액세스하거나 악용하려고 시도하는 비정상적이며 잠재적으로 해로운 시도를 나타내는 비정상적인 작업을 탐지합니다. 위협 탐지는 **잠재적인 SQL 삽입**, **비정상적인 위치 또는 데이터 센터에서 액세스**, **생소한 보안 주체 또는 잠재적으로 위험한 애플리케이션의 액세스** 및 **무차별 SQL 자격 증명 대입 공격**을 식별할 수 있습니다. 자세한 내용은 [위협 탐지 경고](sql-database-threat-detection-overview.md#azure-sql-database-threat-detection-alerts)를 참조하세요.

[이메일 알림](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) 또는 [Azure Portal](sql-database-threat-detection.md#explore-threat-detection-alerts-for-your-database-in-the-azure-portal)을 통해 탐지된 위협에 대한 알림을 받을 수 있습니다.

[위협 탐지](sql-database-threat-detection-overview.md)는 고급 SQL 보안 기능용 통합 패키지인 [SQL ATP(Advanced Threat Protection)](sql-advanced-threat-protection.md) 제품의 일부입니다. Threat Detection은 중앙 SQL ATP 포털을 통해 액세스하고 관리할 수 있습니다. 위협 탐지 서비스는 처음 30일간 무료이며 논리 서버당 매월 15달러가 부과됩니다.

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Azure Portal에서 데이터베이스에 대한 위협 검색 설정

1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal을 시작합니다.
2. 보호할 Azure SQL Database 서버의 구성 페이지로 이동합니다. 보안 설정에서 **Advanced Threat Protection**을 선택합니다.
3. **Advanced Threat Protection** 구성 페이지에서 다음을 수행합니다.

   - 서버에서 Advanced Threat Protection을 사용하도록 설정.
   - **Threat Detection 설정**의 **경고 전송 대상** 텍스트 상자에 비정상적인 데이터베이스 활동 발견 시 보안 경고를 받을 이메일 주소 목록을 입력합니다.
  
   ![위협 감지 설정](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>PowerShell을 사용하여 위협 감지 설정

스크립트 예제는 [PowerShell을 사용하여 감사 및 위협 감지 구성](scripts/sql-database-auditing-and-threat-detection-powershell.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [위협 탐지](sql-database-threat-detection-overview.md)에 대해 자세히 알아보기
- [Managed Instance의 위협 탐지](sql-database-managed-instance-threat-detection.md)에 대해 자세히 알아보기  
- [SQL Advanced Threat Protection](sql-advanced-threat-protection.md)에 대해 자세히 알아보기
- [Azure SQL Database 감사](sql-database-auditing.md)에 대한 자세한 정보
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)에 대한 자세한 정보
- 가격 책정에 대한 자세한 내용은 [SQL Database 가격 책정 페이지](https://azure.microsoft.com/pricing/details/sql-database/)를 참조하세요.  
