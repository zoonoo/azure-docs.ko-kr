---
title: "SSMS: 데이터베이스를 BACPAC 파일로 내보내기(Azure) | Microsoft Docs"
description: "이 문서에서는 SQL Server Management Studio의 데이터 계층 응용 프로그램 내보내기 마법사를 사용하여 SQL Server 데이터베이스를 BACPAC 파일로 내보내는 방법을 보여 줍니다."
keywords: "Microsoft Azure SQL 데이터베이스, 데이터베이스 마이그레이션, 데이터베이스 내보내기, BACPAC 파일 내보내기, 데이터 계층 응용 프로그램 내보내기 마법사"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 19c2dab4-81a6-411d-b08a-0ef79b90fbce
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 02/07/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 3f0af43c103e34535fd114e33c40da010ea69d69
ms.openlocfilehash: 87d9ae3a5c9c8b8edb02e212f652de5f2f796188


---
# <a name="export-an-azure-sql-database-or-a-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio"></a>SQL Server Management Studio를 사용하여 Azure SQL Database 또는 SQL Server 데이터베이스를 BACPAC 파일로 내보내기        
        
이 문서에서는 SQL Server Management Studio의 데이터 계층 응용 프로그램 내보내기 마법사를 사용하여 Azure SQL Database 또는 SQL Server 데이터베이스를 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 파일로 내보내는 방법을 보여 줍니다. BACPAC 파일로 내보내기의 개요는 [BACPAC로 내보내기](sql-database-export.md)를 참조하세요.    
        
1. 최신 버전의 SQL Server Management Studio가 있는지 확인합니다. 새로운 버전의 Management Studio는 매월 업데이트되어 Azure 포털의 업데이트와 동기화 상태를 유지합니다.        
           
   > [!IMPORTANT]        
   > Microsoft Azure 및 SQL 데이터베이스에 대한 업데이트와 동기화 상태를 유지하려면 항상 최신 버전의 Management Studio를 사용하는 것이 좋습니다. [SSMS 최신 버전](https://msdn.microsoft.com/library/mt238290.aspx)        
   >         
         
 2. Management Studio를 열고 개체 탐색기에서 원본 데이터베이스에 연결합니다.        
            
     ![작업 메뉴에서 데이터 계층 응용 프로그램 내보내기](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)        
 3. 개체 탐색기에서 원본 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **작업**을 가리킨 다음 **데이터 계층 응용 프로그램 내보내기...**를 클릭합니다.        
            
     ![작업 메뉴에서 데이터 계층 응용 프로그램 내보내기](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)        
 4. 내보내기 마법사에서 내보내기를 구성하여 로컬 디스크 위치 또는 Azure BLOB에 BACPAC 파일을 저장합니다. 내보낸 BACPAC 파일에는 항상 완전한 데이터베이스 스키마가 포함되며 기본적으로 모든 테이블의 데이터가 포함됩니다. 일부 또는 모든 테이블의 데이터를 제외하려면 고급 탭을 사용합니다. 예를 들어 모든 테이블이 아닌 참조 테이블에 대한 데이터만 내보내도록 선택할 수 있습니다.        
         
     ![설정 내보내기](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)        
         
    > [!IMPORTANT]        
    > Azure Blob 저장소로 BACPAC를 내보낼 때 표준 저장소를 사용합니다. 프리미엄 저장소에서 BACPAC 가져오기는 지원되지 않습니다.        
    >        
            
## <a name="next-steps"></a>다음 단계        
* [SSMS 최신 버전](https://msdn.microsoft.com/library/mt238290.aspx)
* SQL Server 데이터베이스에 BACPAC를 가져오는 방법에 대해 자세히 알아보려면 [SQL Server 데이터베이스로 BACPCAC 가져오기](https://msdn.microsoft.com/library/hh710052.aspx)
* 성능 권장 사항을 비롯한 전체 SQL Server 데이터베이스 마이그레이션 프로세스에 대한 설명은 [Azure SQL Database에 SQL Server 데이터베이스 마이그레이션](sql-database-cloud-migrate.md)을 참조하세요.
* 보관을 위해 데이터베이스를 내보내는 방법의 대안으로 사용되는 Azure SQL Database 백업의 장기 백업 보존에 대해 알아보려면 [장기 백업 보존](sql-database-long-term-retention.md)을 참조하세요.


    



<!--HONumber=Feb17_HO2-->


