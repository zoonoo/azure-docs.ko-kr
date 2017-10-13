---
title: "Azure SQL Database에 대한 자동 조정 사용 | Microsoft Docs"
description: "Azure SQL Database에서 쉽게 자동 조정을 사용할 수 있습니다."
services: sql-database
documentationcenter: 
author: vvasic
manager: drasumic
editor: vvasic
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 09/19/2016
ms.author: vvasic
ms.openlocfilehash: 378e5a6a8cea80a551666c3692f8aa650620f66d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="enable-automatic-tuning"></a>자동 조정 사용

Azure SQL Database는 지속적으로 쿼리를 모니터링하고 워크로드의 성능 향상을 위해 수행할 수 있는 작업을 식별하는 자동으로 관리되는 데이터 서비스입니다. 권장 사항을 검토하고 수동으로 적용하거나 Azure SQL Database에서 정정 작업을 자동으로 적용하도록 할 수 있습니다. 이는 **자동 조정 모드**로 알려져 있습니다. 서버 또는 데이터베이스 수준에서 자동 조정을 사용할 수 있습니다.

## <a name="enable-automatic-tuning-on-server"></a>서버에서 자동 조정 사용

Azure SQL Database 서버에서 자동 조정을 사용하려면 Azure Portal에서 서버로 이동한 다음 메뉴에서 **자동 조정**을 선택합니다. 사용하려는 자동 조정 옵션을 선택하고 **적용**을 선택합니다.

![서버](./media/sql-database-automatic-tuning-enable/server.png)

서버의 자동 조정 옵션은 서버의 모든 데이터베이스에 적용됩니다. 기본적으로 모든 데이터베이스는 부모 서버에서 구성을 상속하지만 이는 각 데이터베이스에 대해 개별적으로 재정의되고 지정될 수 있습니다.

## <a name="configure-automatic-tuning-on-database"></a>데이터베이스에서 자동 조정 구성

Azure Portal을 통해 각 데이터베이스에서 개별적으로 자동 조정 구성을 지정할 수 있습니다.

> [!NOTE]
> 동일한 구성 설정을 모든 데이터베이스에 대해 자동으로 적용할 수 있도록 서버 수준에서 자동 조정 구성을 관리하는 것이 좋습니다. 데이터베이스가 동일한 서버에서 서로 다른 경우 개별 데이터베이스에서 자동 조정을 구성합니다.
>

단일 데이터베이스에서 자동 조정을 사용하려면 Azure Portal에서 데이터베이스로 이동한 다음 **자동 조정**을 선택합니다. 옵션을 선택하여 서버에서 설정을 상속하도록 단일 데이터베이스를 구성하거나 데이터베이스에 대한 구성을 개별적으로 지정할 수 있습니다.

![데이터베이스](./media/sql-database-automatic-tuning-enable/database.png)

적절한 구성을 선택한 후 **적용**을 클릭합니다.

## <a name="next-steps"></a>다음 단계
* 자동 조정 및 성능을 개선할 수 있는 방법에 대한 자세한 내용은 [자동 조정 문서](sql-database-automatic-tuning.md)를 참조하세요.
* Azure SQL Database 성능 권장 사항에 대한 개요는 [성능 권장 사항](sql-database-advisor.md)을 참조하세요.
* 상위 쿼리의 성능에 미치는 영향을 알아보려면 [Query Performance Insights](sql-database-query-performance.md)를 참조하세요.