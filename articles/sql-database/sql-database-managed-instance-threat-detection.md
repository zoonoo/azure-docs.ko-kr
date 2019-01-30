---
title: 위협 탐지 - Azure SQL Database Managed Instance | Microsoft Docs
description: 위협 검색은 Managed Instance의 데이터베이스에 대한 잠재적인 보안 위협을 나타내는 비정상적인 데이터베이스 활동을 검색합니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
manager: craigg
ms.date: 12/06/2018
ms.openlocfilehash: f87b7093a732639db8a53ebec68fcae8f29735f3
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54158672"
---
# <a name="configure-threat-detection-preview-in-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance에서 위협 탐지(미리 보기) 구성

[SQL Database Managed Instance](sql-database-managed-instance-index.yml)를 위한 Azure SQL [위협 탐지](sql-database-threat-detection-overview.md)는 데이터베이스에 액세스하거나 악용하려는 비정상적이고 잠재적인 해로운 시도를 나타내는 비정상적인 활동을 감지합니다. 위협 탐지는 **잠재적인 SQL 삽입**, **비정상적인 위치 또는 데이터 센터에서 액세스**, **생소한 보안 주체 또는 잠재적으로 위험한 애플리케이션의 액세스** 및 **무차별 SQL 자격 증명 대입 공격**을 식별할 수 있습니다. 자세한 내용은 [위협 탐지 경고](sql-database-threat-detection-overview.md#azure-sql-database-threat-detection-alerts)를 참조하세요.

[이메일 알림](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) 또는 [Azure Portal](sql-database-threat-detection-overview.md#explore-threat-detection-alerts-for-your-database-in-the-azure-portal)을 통해 탐지된 위협에 대한 알림을 받을 수 있습니다.

[위협 탐지](sql-database-threat-detection-overview.md)는 고급 SQL 보안 기능용 통합 패키지인 [SQL ATP(Advanced Threat Protection)](sql-advanced-threat-protection.md) 제품의 일부입니다. Threat Detection은 중앙 SQL ATP 포털을 통해 액세스하고 관리할 수 있습니다. 위협 탐지 서비스는 처음 30일간 무료이며 Managed Instance당 매월 15달러가 부과됩니다.

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Azure Portal에서 Managed Instance에 대한 위협 검색 설정

1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal을 시작합니다.
2. 보호할 Managed Instance의 구성 페이지로 이동합니다. **설정** 페이지에서 **위협 검색**을 선택합니다.
3. 위협 검색 구성 페이지에서
   - 위협 감지를 **켭니다**
   - 비정상적인 데이터베이스 활동이 감지되는 경우 보안 경고를 수신할 **메일 목록**을 구성합니다.
   - 비정상적인 위협 감사 레코드가 저장되는 **Azure Storage 계정**을 선택합니다.
4. **저장**을 클릭하여 새롭거나 업데이트된 위협 검색 정책을 저장합니다.

   ![위협 검색](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="next-steps"></a>다음 단계

- [위협 탐지](sql-database-threat-detection-overview.md)에 대해 자세히 알아봅니다.
- [Managed Instance란?](sql-database-managed-instance.md)에서 Managed Instance에 대해 자세히 알아봅니다.
- [Single Database를 위한 위협 탐지](sql-database-threat-detection.md)에 대해 자세히 알아봅니다.
- [Managed Instance 감사](https://go.microsoft.com/fwlink/?linkid=869430)에 대해 자세히 알아봅니다.
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)에 대해 자세히 알아봅니다.
