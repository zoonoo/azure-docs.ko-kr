---
title: Advanced Threat Protection 구성
description: Advanced Threat Protection은 단일 데이터베이스 또는 탄력적 풀의 데이터베이스에 대 한 잠재적인 보안 위협을 나타내는 비정상적인 데이터베이스 활동을 검색 합니다.
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
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822501"
---
# <a name="azure-sql-database-advanced-threat-protection-for-single-or-pooled-databases"></a>단일 또는 풀링된 데이터베이스에 대 한 Advanced Threat Protection Azure SQL Database

단일 및 풀링된 데이터베이스에 대 한 [Advanced Threat Protection](sql-database-threat-detection-overview.md) 은 비정상적인 활동을 감지 하 여 데이터베이스에 액세스 하거나 악용 하려는 비정상적인 시도를 감지 합니다. Advanced Threat Protection은 **잠재적 sql 삽입**, **비정상적인 위치 또는 데이터 센터에서 액세스**, **익숙하지 않은 사용자 또는 잠재적으로 유해한 응용 프로그램에서 액세스**, **무차별 암호 대입 SQL 자격 증명** 을 식별할 수 있습니다. [Advanced Threat Protection 경고](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)에 대 한 자세한 내용.

[이메일 알림](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) 또는 [Azure Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)을 통해 탐지된 위협에 대한 알림을 받을 수 있습니다.

Advanced [Threat Protection](sql-database-threat-detection-overview.md) 은 고급 SQL 보안 기능을 위한 통합 패키지인 광고 ( [advanced data security](sql-database-advanced-data-security.md) ) 제품의 일부입니다. 고급 위협 방지는 중앙 SQL ADS 포털을 통해 액세스 하 고 관리할 수 있습니다.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Azure Portal에서 Advanced Threat Protection 설정

1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal을 시작합니다.
2. 보호할 Azure SQL Database 서버의 구성 페이지로 이동합니다. 보안 설정에서 **Advanced Data Security**를 선택합니다.
3. **Advanced Data Security** 구성 페이지에서 다음을 수행합니다.

   - 서버에서 Advanced Data Security를 사용하도록 설정합니다.
   - **Advanced Threat Detection 설정**의 **경고 전송 대상** 텍스트 상자에 비정상적인 데이터베이스 활동 발견 시 보안 경고를 받을 이메일 목록을 입력합니다.
  
   ![Advanced Threat Protection 설정](./media/sql-database-threat-detection/set_up_threat_detection.png)

   > [!NOTE]
   > 스크린샷의 가격은 항상 현재 가격을 반영 하지는 않으며 예입니다.

## <a name="set-up-advanced-threat-protection-using-powershell"></a>PowerShell을 사용 하 여 Advanced Threat Protection 설정

스크립트 예제는 [PowerShell을 사용 하 여 감사 및 고급 위협 방지 구성](scripts/sql-database-auditing-and-threat-detection-powershell.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Advanced Threat Protection](sql-database-threat-detection-overview.md)에 대해 자세히 알아보세요.
- [관리 되는 인스턴스의 Advanced Threat Protection](sql-database-managed-instance-threat-detection.md)에 대해 자세히 알아보세요.  
- [고급 데이터 보안](sql-database-advanced-data-security.md)에 대해 자세히 알아봅니다.
- [감사](sql-database-auditing.md)에 대해 자세히 알아보기
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)에 대해 자세히 알아보기
- 가격 책정에 대한 자세한 내용은 [SQL Database 가격 책정 페이지](https://azure.microsoft.com/pricing/details/sql-database/)를 참조하세요.  
