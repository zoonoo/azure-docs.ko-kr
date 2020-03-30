---
title: 고급 위협 보호 - 관리형 인스턴스 구성
description: 고급 위협 보호는 관리되는 인스턴스의 데이터베이스에 대한 잠재적보안 위협을 나타내는 비정상적인 데이터베이스 활동을 감지합니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 69292a934af8b8777f11ab58ed3fe306abf8b408
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822557"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-database-managed-instance"></a>Azure SQL 데이터베이스 관리 인스턴스에서 고급 위협 보호 구성

[관리되는 인스턴스에](sql-database-managed-instance-index.yml) 대한 [고급 위협 보호는](sql-database-threat-detection-overview.md) 데이터베이스에 액세스하거나 악용하려는 비정상적이고 잠재적으로 유해한 시도를 나타내는 비정상적인 활동을 감지합니다. 고급 위협 보호는 **잠재적인 SQL 주입,** **비정상적인 위치 또는 데이터 센터에서의 액세스,** 익숙하지 않은 주 또는 잠재적으로 유해한 [Advanced Threat Protection alerts](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts) **응용 프로그램에서의 액세스**및 **Brute force SQL 자격 증명을** 식별할 수 있습니다.

[이메일 알림](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) 또는 [Azure Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)을 통해 탐지된 위협에 대한 알림을 받을 수 있습니다.

[고급 위협 보호는](sql-database-threat-detection-overview.md) 고급 SQL 보안 기능을 위한 통합 패키지인 ADS(고급 [데이터](sql-database-advanced-data-security.md) 보안) 제공의 일부입니다. 중앙의 SQL ADS 포털을 통해 Advanced Threat Protection에 액세스하고 관리할 수 있습니다.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Azure 포털에서 고급 위협 보호 설정

1. 에서 [https://portal.azure.com](https://portal.azure.com)Azure 포털을 시작합니다.
2. 보호할 관리되는 인스턴스의 구성 페이지로 이동합니다. **설정** 페이지에서 고급 **데이터 보안을**선택합니다.
3. 고급 데이터 보안 구성 페이지에서
   - 고급 데이터 보안을 **켭니다.**
   - 비정상적인 데이터베이스 활동이 감지되면 보안 경고를 받도록 **전자 메일 목록을** 구성합니다.
   - 비정상적인 위협 감사 레코드가 저장되는 **Azure Storage 계정**을 선택합니다.
   - 구성하려는 **고급 위협 보호 유형을** 선택합니다. [고급 위협 보호 경고에](sql-database-threat-detection-overview.md)대해 자세히 알아보십시오.
4. 새 또는 업데이트된 고급 데이터 보안 정책을 저장하려면 **저장을 클릭합니다.**

   ![고급 위협 보호](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

   > [!NOTE]
   > 스크린샷의 가격이 항상 현재 가격을 반영하는 것은 아니며 예입니다.

## <a name="next-steps"></a>다음 단계

- [고급 위협 보호](sql-database-threat-detection-overview.md)에 대해 자세히 알아보십시오.
- [관리되는 인스턴스란?](sql-database-managed-instance.md)에서 관리되는 인스턴스에 대해 자세히 알아봅니다.
- [단일 데이터베이스에 대한 고급 위협 보호에](sql-database-threat-detection.md)대해 자세히 알아보십시오.
- [관리되는 인스턴스 감사에](https://go.microsoft.com/fwlink/?linkid=869430)대해 자세히 알아봅니다.
- [Azure 보안 센터에](https://docs.microsoft.com/azure/security-center/security-center-intro)대해 자세히 알아봅니다.
