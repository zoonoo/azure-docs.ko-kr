---
title: "서버 간, 구독 간, Azure 내부 및 외부로 데이터베이스를 이동합니다."
description: "Azure SQL Database에서 데이터 및 데이터베이스를 복사, 이동 및 마이그레이션하는 빠른 단계입니다."
services: sql-database
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
ms.assetid: dea01868-5757-41e2-826c-ec26361a339c
ms.service: sql-database
ms.custom: migrate and move
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: v-shysun
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: bc88bb49c601f525c62c4dac4716a3f77a30172e


---
# <a name="move-databases-between-servers-between-subscriptions-and-in-and-out-of-azure"></a>서버 간, 구독 간 및 Azure에서/로 데이터베이스 이동
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>동일한 구독의 다른 서버에 데이터베이스를 이동하려면
* [Azure Portal](https://portal.azure.com)에서 **SQL Database**를 클릭하고 목록에서 데이터베이스를 선택한 후 **복사**를 클릭합니다. 자세한 내용은 [Azure SQL Database 복사](sql-database-copy.md) 를 참조하세요.

## <a name="to-move-a-database-between-subscriptions"></a>구독 간에 데이터베이스를 이동하려면
* [Azure 포털](https://portal.azure.com)에서 **SQL Server** 를 클릭하고, 목록에서 데이터베이스를 호스팅하는 서버를 선택합니다. **이동**을 클릭한 후 이동할 리소스와 이동 대상인 구독을 선택합니다.

## <a name="to-migrate-a-sql-database-into-azure"></a>SQL Database를 Azure로 마이그레이션하려면
* 데이터베이스 호환성을 확인한 후 필요에 따라 적합한 마이그레이션 방법을 선택합니다. [SQL Server 데이터베이스 마이그레이션](sql-database-cloud-migrate.md)에 대한 지침 및 옵션을 따릅니다.

## <a name="to-create-a-copy-of-a-database-for-use-outside-of-azure"></a>Azure 외부에서 사용할 데이터베이스 복사본을 만들려면
* [BACPAC 파일을 내보냅니다.](sql-database-export.md)




<!--HONumber=Nov16_HO3-->


