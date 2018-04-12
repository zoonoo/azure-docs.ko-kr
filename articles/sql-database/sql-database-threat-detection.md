---
title: 위협 감지 - Azure SQL Database | Microsoft Docs
description: 위협 감지는 데이터베이스에 대한 잠재적인 보안 위협을 나타내는 비정상적인 데이터베이스 활동을 감지합니다.
services: sql-database
author: rmatchoro
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 04/01/2018
ms.author: ronmat
ms.openlocfilehash: c4a94ab9c7e0dab9e8c25e54fdd0a30b28b7a8a3
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="sql-database-threat-detection"></a>SQL Database 위협 감지

SQL 위협 감지는 데이터베이스를 액세스하거나 악용하려는 비정상적이고 잠재적으로 해로운 시도를 나타내는 비정상적인 활동을 감지합니다.

## <a name="overview"></a>개요

SQL 위협 감지는 비정상적인 활동에 대한 보안 경고를 제공하여 잠재적인 위협이 발생하면 고객이 이를 감지하고 대응할 수 있도록 하는 새로운 차원의 보안을 제공합니다.  사용자는 의심스러운 데이터베이스 활동, 잠재적 취약성 및 SQL 삽입 공격뿐만 아니라 비정상적인 데이터베이스 액세스 패턴에 대한 경고도 받습니다. SQL 위협 감지 경고는 의심스러운 활동에 대한 세부 정보 제공하고 위협을 조사하고 완화하는 방법에 대한 조치를 권장합니다. 사용자는 데이터베이스의 데이터를 액세스, 침범 또는 악용하려는 시도로 인해 의심스러운 이벤트가 발생했는지를 판단하기 위해서 [SQL Database 감사](sql-database-auditing.md)를 사용하여 의심스러운 이벤트를 살펴볼 수 있습니다. 위협 감지는 보안 전문가가 되거나 고급 보안 모니터링 시스템을 관리할 필요 없이 데이터베이스에 대한 잠재적인 위협에 간단하게 대처할 수 있도록 합니다.

예를 들어 SQL 삽입은 데이터 기반 응용 프로그램 공격에 사용되는 인터넷 상의 일반적인 웹 응용 프로그램 보안 문제 중 하나입니다. 공격자는 응용 프로그램의 취약성을 이용하여 악의적인 SQL 문을 응용 프로그램 항목 필드에 삽입하고 데이터베이스의 데이터를 침범하거나 수정합니다.

SQL 위협 감지의 경고는 [Azure Security Center](https://azure.microsoft.com/services/security-center/) 와 통합됩니다. 보호되는 각 SQL Database 서버에 대한 비용은 Azure Security Center 표준 계층과 동일한 가격($15/노드/월, 여기서 보호되는 각 SQL Database 서버를 하나의 노드로 계산)으로 청구됩니다.  

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Azure Portal에서 데이터베이스에 대한 위협 검색 설정
1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal을 시작합니다.
2. 모니터링할 SQL Database의 구성 페이지로 이동합니다. 설정 페이지에서 **감사 및 위협 감지**를 선택합니다. 
    ![탐색 창][1]
3. **감사 및 위협 감지** 구성 페이지에서 감사를 **켜면** 위협 감지 설정이 표시됩니다.
  
    ![탐색 창][2]
4. 위협 감지를 **켭니다**
5. 비정상적인 데이터베이스 활동이 감지되는 경우 보안 경고를 수신할 메일 목록을 구성합니다.
6. **감사 및 위협 감지** 페이지에서 **저장**을 클릭하여 새로운 또는 업데이트된 감사 및 위협 감지 설정을 저장합니다.
       
    ![탐색 창][3]

## <a name="set-up-threat-detection-using-powershell"></a>PowerShell을 사용하여 위협 감지 설정

스크립트 예제는 [PowerShell을 사용하여 감사 및 위협 감지 구성](scripts/sql-database-auditing-and-threat-detection-powershell.md)을 참조하세요.

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>의심스러운 이벤트 감지 시 비정상적인 데이터베이스 활동 살펴보기
1. 비정상적인 데이터베이스 활동이 감지되면 메일 알림을 받습니다. <br/>
   메일에는 비정상적인 활동의 특징, 데이터베이스 이름, 서버 이름, 응용 프로그램 이름, 이벤트 시간을 비롯한 의심스러운 보안 이벤트에 대한 정보가 제공됩니다. 또한 전자 메일에는 가능한 원인에 대한 정보 및 데이터베이스에 대한 잠재적인 위협을 조사하고 완화시키기 위해 권장되는 조치가 제공됩니다.<br/>
     
    ![탐색 창][4]
2. 전자 메일 경고에는 SQL 감사 로그에 대한 직접 링크가 포함됩니다. 이 링크를 클릭하면 Azure Portal이 열리고 의심스러운 이벤트가 발생한 시점의 SQL 감사 레코드가 열립니다. 의심스러운 데이터베이스 활동에 대한 자세한 내용을 보려면 감사 레코드를 클릭하여 실행된 SQL 문을 더 쉽게 찾아서(액세스한 사람, 수행한 작업 및 수행 시기) 이벤트가 합법적인지 또는 악의적인지(예: SQL 삽입에 대한 응용 프로그램 취약점이 악용되어 누군가가 중요한 데이터를 침범했는지 등)를 판단하세요.<br/>
   ![탐색 창][5]


## <a name="explore-threat-detection-alerts-for-your-database-in-the-azure-portal"></a>Azure Portal에서 데이터베이스에 대한 위협 감지 경고 탐색

SQL Database 위협 감지는 경고를 [Azure Security Center](https://azure.microsoft.com/services/security-center/)와 통합합니다. Azure Portal의 데이터베이스 페이지 내 실제 SQL 보안 타일은 활성 위협의 상태를 추적합니다. 

   ![탐색 창][6]
   
1. SQL 보안 타일을 클릭하면 Azure Security Center 경고 페이지가 열리고 데이터베이스에서 감지된 활성 SQL 위협에 대한 개요가 제공됩니다. 

  ![탐색 창][7]

2. 특정 경고를 클릭하면 이 위협을 조사하고 향후 위협을 수정하기 위한 추가 세부 정보 및 조치가 제공됩니다.

  ![탐색 창][8]


## <a name="next-steps"></a>다음 단계

* 위협 감지에 대해 자세히 알아보려면 [Azure 블로그](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-general-availability-in-spring-2017/)를 참조하세요. 
* [Azure SQL Database 감사](sql-database-auditing.md)에 대한 자세한 정보
* [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)에 대한 자세한 정보
* 가격 책정에 대한 자세한 내용은 [SQL Database 가격 책정 페이지](https://azure.microsoft.com/pricing/details/sql-database/)를 참조하세요.  
* PowerShell 스크립트 예제는 [PowerShell을 사용하여 감사 및 위협 감지 구성](scripts/sql-database-auditing-and-threat-detection-powershell.md)을 참조하세요.



<!--Image references-->
[1]: ./media/sql-database-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-database-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-database-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-database-threat-detection/4_td_email.png
[5]: ./media/sql-database-threat-detection/5_td_audit_record_details.png
[6]: ./media/sql-database-threat-detection/6_td_security_tile_view_alerts.png
[7]: ./media/sql-database-threat-detection/7_td_SQL_security_alerts_list.png
[8]: ./media/sql-database-threat-detection/8_td_SQL_security_alert_details.png


