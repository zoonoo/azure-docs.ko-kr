---
title: "Azure Portal: SQL Database 동적 데이터 마스킹 | Microsoft Docs"
description: "Azure Portal에서 SQL Database 동적 데이터 마스킹을 시작하는 방법"
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: 
ms.assetid: 2
ms.service: sql-database
ms.custom: secure and protect
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 11/22/2016
ms.author: ronitr; ronmat
translationtype: Human Translation
ms.sourcegitcommit: 07913359addaa49fe8d408a8e363f554419e4de2
ms.openlocfilehash: 16db41c83ca5c582aca7581ae9b6a03a3d99ab15
ms.lasthandoff: 02/16/2017


---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Azure Portal에서 SQL Database 동적 데이터 마스킹 시작

이 항목에서는 Azure Portal을 사용하여 [동적 데이터 마스킹](sql-database-dynamic-data-masking-get-started.md)을 구현하는 방법을 보여 줍니다. [Azure SQL Database cmdlet](https://msdn.microsoft.com/library/azure/mt574084.aspx) 또는 [REST API](https://msdn.microsoft.com/library/dn505719.aspx)를 사용하여 동적 데이터 마스킹을 구현할 수도 있습니다.


## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Azure 포털을 사용하여 데이터베이스에 대한 동적 데이터 마스킹 설정
1. [https://portal.azure.com](https://portal.azure.com)에서 Azure 포털을 시작합니다.
2. 마스킹할 중요 데이터가 포함된 데이터베이스의 설정 블레이드로 이동합니다.
3. **동적 데이터 마스킹** 타일을 클릭하여 **동적 데이터 마스킹** 구성 블레이드를 시작합니다.
   
   * 아래쪽의 **작업** 섹션으로 스크롤한 다음 **동적 데이터 마스킹**을 클릭해도 됩니다.
     
     ![탐색 창](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)<br/><br/>
4. **동적 데이터 마스킹** 구성 블레이드에 권장 엔진이 마스킹 대상으로 플래그한 몇 가지 데이터베이스 열이 나타날 수 있습니다. 권장을 적용하려면 하나 이상의 열에 대해 **마스크 추가**를 클릭합니다. 해당 열에 대해 기본 형식을 기초로 마스크가 생성됩니다. 마스킹 규칙을 클릭하고 마스킹 필드 형식을 원하는 다른 형식으로 편집하면 마스킹 기능을 변경할 수 있습니다. 반드시 **저장**을 클릭하여 설정을 저장합니다.
   
    ![탐색 창](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)<br/><br/>
5. 데이터베이스에서 열에 마스크를 추가하려면 **동적 데이터 마스킹** 구성 블레이드에서 **마스크 추가**를 클릭하여 **마스킹 규칙 추가** 구성 블레이드를 엽니다.
   
    ![탐색 창](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)<br/><br/>
6. **스키마**, **테이블** 및 **열**을 선택하여 마스킹하도록 지정되는 필드를 정의합니다.
7. 중요한 데이터 마스킹 범주 목록에서 **마스킹 필드 형식** 을 선택합니다.
   
    ![탐색 창](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)<br/><br/>        
8. 데이터 마스킹 규칙 블레이드에서 **저장** 을 클릭하여 동적 데이터 마스킹 정책의 마스킹 규칙 집합을 업데이트합니다.
9. 마스킹에서 제외되며 마스크되지 않은 중요 데이터에 액세스할 수 있는 SQL 사용자 또는 AAD ID를 입력합니다. 세미콜론으로 구분된 사용자 목록이어야 합니다. 관리자 권한이 있는 사용자는 항상 원래의 마스킹되지 않은 데이터에 액세스할 수 있습니다.
   
    ![탐색 창](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)
   
   > [!TIP]
   > 응용 프로그램 계층에서 권한이 있는 응용 프로그램 사용자에 대해 중요한 데이터를 표시할 수 있도록 지정하려면 응용 프로그램이 데이터베이스 쿼리에 사용할 SQL 사용자나 AAD ID를 추가합니다. 중요한 데이터의 노출을 최소화하려면 권한이 있는 사용자 수를 최소한으로 이 목록에 포함하는 것이 좋습니다.
   > 
   > 
10. 데이터 마스킹 구성 블레이드에서 **저장** 을 클릭하여 신규 또는 업데이트된 마스킹 정책을 저장합니다.


## <a name="next-steps"></a>다음 단계

* 동적 데이터 마스킹의 개요는 [동적 데이터 마스킹](sql-database-dynamic-data-masking-get-started.md)을 참조하세요.
* [Azure SQL Database cmdlet](https://msdn.microsoft.com/library/azure/mt574084.aspx) 또는 [REST API](https://msdn.microsoft.com/library/dn505719.aspx)를 사용하여 동적 데이터 마스킹을 구현할 수도 있습니다.
