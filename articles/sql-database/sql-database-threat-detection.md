---
title: Advanced Threat Protection 구성
description: 고급 위협 보호는 단일 데이터베이스 또는 탄력적 풀에서 데이터베이스에 대한 잠재적보안 위협을 나타내는 비정상적인 데이터베이스 활동을 탐지합니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 8eb8e4fccc17fe31def671cf6e8edb19d867b244
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822501"
---
# <a name="azure-sql-database-advanced-threat-protection-for-single-or-pooled-databases"></a>단일 또는 풀풀된 데이터베이스에 대한 Azure SQL 데이터베이스 고급 위협 보호

단일 및 풀링된 데이터베이스에 대한 [고급 위협 보호는](sql-database-threat-detection-overview.md) 데이터베이스에 액세스하거나 악용하려는 비정상적인 시도를 나타내는 비정상적인 활동을 감지합니다. 고급 위협 보호는 **잠재적인 SQL 주입,** **비정상적인 위치 또는 데이터 센터에서의 액세스,** 익숙하지 않은 주 또는 잠재적으로 유해한 [Advanced Threat Protection alerts](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts) **응용 프로그램에서의 액세스**및 **Brute force SQL 자격 증명을** 식별할 수 있습니다.

[이메일 알림](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) 또는 [Azure Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)을 통해 탐지된 위협에 대한 알림을 받을 수 있습니다.

[고급 위협 보호는](sql-database-threat-detection-overview.md) 고급 SQL 보안 기능을 위한 통합 패키지인 ADS(고급 [데이터](sql-database-advanced-data-security.md) 보안) 제공의 일부입니다. 중앙의 SQL ADS 포털을 통해 Advanced Threat Protection에 액세스하고 관리할 수 있습니다.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Azure 포털에서 고급 위협 보호 설정

1. 에서 [https://portal.azure.com](https://portal.azure.com)Azure 포털을 시작합니다.
2. 보호할 Azure SQL Database 서버의 구성 페이지로 이동합니다. 보안 설정에서 **Advanced Data Security**를 선택합니다.
3. **Advanced Data Security** 구성 페이지에서 다음을 수행합니다.

   - 서버에서 Advanced Data Security를 사용하도록 설정합니다.
   - **Advanced Threat Detection 설정**의 **경고 전송 대상** 텍스트 상자에 비정상적인 데이터베이스 활동 발견 시 보안 경고를 받을 이메일 목록을 입력합니다.
  
   ![고급 위협 보호 설정](./media/sql-database-threat-detection/set_up_threat_detection.png)

   > [!NOTE]
   > 스크린샷의 가격이 항상 현재 가격을 반영하는 것은 아니며 예입니다.

## <a name="set-up-advanced-threat-protection-using-powershell"></a>PowerShell을 사용하여 Advanced Threat Protection 설정

스크립트 예제의 경우 [PowerShell을 사용하여 감사 및 고급 위협 보호 구성을](scripts/sql-database-auditing-and-threat-detection-powershell.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

- [고급 위협 보호](sql-database-threat-detection-overview.md)에 대해 자세히 알아보십시오.
- [관리되는 인스턴스의 고급 위협 보호에](sql-database-managed-instance-threat-detection.md)대해 자세히 알아보십시오.  
- [고급 데이터 보안](sql-database-advanced-data-security.md)에 대해 자세히 알아봅니다.
- [감사](sql-database-auditing.md)에 대해 자세히 알아보기
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)에 대해 자세히 알아봅니다.
- 가격 책정에 대한 자세한 내용은 [SQL Database 가격 책정 페이지를](https://azure.microsoft.com/pricing/details/sql-database/) 참조하십시오.  
