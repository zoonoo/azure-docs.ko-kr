---
title: 위협 탐지 - Azure SQL Database | Microsoft Docs
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
ms.date: 02/08/2019
ms.openlocfilehash: 60839fa06d8436d3d2202d1d97735c42c5768e9e
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793778"
---
# <a name="azure-sql-database-threat-detection-for-single-or-pooled-databases"></a>단일 또는 풀링된 데이터베이스에 대한 Azure SQL Database 위협 탐지

단일 및 풀링된 데이터베이스에 대한 [위협 탐지](sql-database-threat-detection-overview.md)는 데이터베이스에 액세스하거나 악용하려고 시도하는 비정상적이며 잠재적으로 해로운 시도를 나타내는 비정상적인 활동을 검색합니다. 위협 탐지는 **잠재적인 SQL 삽입**, **비정상적인 위치 또는 데이터 센터에서 액세스**, **생소한 보안 주체 또는 잠재적으로 위험한 애플리케이션의 액세스** 및 **무차별 SQL 자격 증명 대입 공격**을 식별할 수 있습니다. 자세한 내용은 [위협 탐지 경고](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)를 참조하세요.

[이메일 알림](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) 또는 [Azure Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)을 통해 탐지된 위협에 대한 알림을 받을 수 있습니다.

[위협 탐지](sql-database-threat-detection-overview.md)는 고급 SQL 보안 기능용 통합 패키지인 [ADS](sql-database-advanced-data-security.md)(Advanced Data Security) 제품에 포함되어 있습니다. 중앙 SQL ADS 포털을 통해 위협 탐지에 액세스하고 관리할 수 있습니다. Advanced Data Security 패키지는 처음 30일간 평가판으로 제공되며 논리 서버당 매월 15달러가 부과됩니다.

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Azure Portal에서 데이터베이스에 대한 위협 검색 설정

1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal을 시작합니다.
2. 보호할 Azure SQL Database 서버의 구성 페이지로 이동합니다. 보안 설정에서 **Advanced Data Security**를 선택합니다.
3. **Advanced Data Security** 구성 페이지에서 다음을 수행합니다.

   - 서버에서 Advanced Data Security를 사용하도록 설정합니다.
   - **Threat Detection 설정**의 **경고 전송 대상** 텍스트 상자에 비정상적인 데이터베이스 활동 발견 시 보안 경고를 받을 이메일 주소 목록을 입력합니다.
  
   ![위협 감지 설정](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>PowerShell을 사용하여 위협 감지 설정

스크립트 예제는 [PowerShell을 사용하여 감사 및 위협 감지 구성](scripts/sql-database-auditing-and-threat-detection-powershell.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [위협 탐지](sql-database-threat-detection-overview.md)에 대해 자세히 알아봅니다.
- [관리되는 인스턴스의 위협 탐지](sql-database-managed-instance-threat-detection.md)에 대해 자세히 알아봅니다.  
- [고급 데이터 보안](sql-database-advanced-data-security.md)에 대해 자세히 알아봅니다.
- [감사](sql-database-auditing.md)에 대해 자세히 알아보기
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)에 대해 자세히 알아보기
- 가격 책정에 대한 자세한 내용은 [SQL Database 가격 책정 페이지](https://azure.microsoft.com/pricing/details/sql-database/)를 참조하세요.  
