---
title: "BACPAC 파일을 가져와 Azure SQL 데이터베이스 만들기 | Microsoft Docs"
description: "기존 BACPAC 파일을 가져와 Azure SQL 데이터베이스를 만듭니다."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: cf9a9631-56aa-4985-a565-1cacc297871d
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: ca15248d7ac5232e6308d8188332975f1d21c09b
ms.lasthandoff: 03/14/2017


---
# <a name="import-a-bacpac-file-to-create-an-azure-sql-database-using-the-azure-portal"></a>Azure Portal을 사용하여 BACPAC 파일을 가져와 Azure SQL 데이터베이스 만들기

이 문서에서는 [Azure 포털](https://portal.azure.com)을 사용하여 BACPAC 파일에서 Azure SQL 데이터베이스를 만드는 방법에 대한 지침을 제공합니다.

## <a name="prerequisites"></a>필수 조건

BACPAC에서 SQL Database를 가져오려면 다음이 필요합니다.

* Azure 구독. 
* Azure SQL 데이터베이스 V12 서버. V12 서버가 없는 경우 [첫 Azure SQL 데이터베이스 만들기](sql-database-get-started.md)문서의 단계에 따라 만듭니다.
* [Azure 저장소 계정(표준)](../storage/storage-create-storage-account.md) Blob 컨테이너에서 가져오려는 데이터베이스의 .bacpac 파일

> [!IMPORTANT]
> Azure Blob 저장소에서 BACPAC를 가져올 때 표준 저장소를 사용합니다. 프리미엄 저장소에서 BACPAC 가져오기는 지원되지 않습니다.
> 

## <a name="import-the-database"></a>데이터베이스 가져오기
SQL Server 블레이드를 엽니다.

1. [Azure 포털](https://portal.azure.com)로 이동합니다.
2. **SQL Server**를 클릭합니다.
3. 데이터베이스를 복원할 서버를 클릭합니다.
4. SQL Server 블레이드에서 **데이터베이스 가져오기**를 클릭하여 **데이터베이스 가져오기** 블레이드를 엽니다.
   
   ![데이터베이스 가져오기][1]
5. **저장소**를 클릭하고 저장소 계정, Blob 컨테이너 및 .bacpac 파일을 선택한 다음 **확인**을 클릭합니다.
   
   ![저장소 옵션 구성][2]
6. 새 데이터베이스에 대한 가격 책정 계층을 선택하고 **선택**을 클릭합니다. 탄력적 풀에 직접 데이터베이스 가져오기는 지원되지 않지만 먼저 독립 실행형 데이터베이스로 가져온 다음 데이터베이스를 풀로 이동할 수 있습니다.
   
   ![가격 책정 계층 선택][3]
7. BACPAC 파일에서 만들 데이터베이스에 대한 **데이터베이스 이름** 을 입력합니다.
8. 인증 유형을 선택한 후 서버에 대한 인증 정보를 제공합니다. 
9. **만들기** 를 클릭하여 BACPAC에서 데이터베이스를 만듭니다.
   
   ![데이터베이스 만들기][4]

**만들기** 를 클릭하면 데이터베이스 가져오기 요청이 서비스에 제출됩니다. 데이터베이스 크기에 따라 가져오기 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다.

## <a name="monitor-the-progress-of-the-import-operation"></a>가져오기 작업의 진행률 모니터링
1. **SQL Server**를 클릭합니다.
2. 복원할 서버를 클릭합니다.
3. SQL Server 블레이드의 작업 영역에서 **가져오기/내보내기 기록**을 클릭합니다.
   
   ![가져오기 내보내기 기록][5]
   ![가져오기 내보내기 기록][6]

4. 데이터베이스가 서버에서 라이브 상태인지 확인하려면 **SQL 데이터베이스**를 클릭하고 새 데이터베이스를 **온라인** 상태인지 확인합니다.

## <a name="next-steps"></a>다음 단계
* 가져온 SQL 데이터베이스에 연결하고 쿼리하는 방법을 알아보려면 [SQL Server Management Studio를 사용하여 SQL 데이터베이스에 연결하고 샘플 T-SQL 쿼리 수행](sql-database-connect-query-ssms.md)을 참조하세요.
* BACPAC 파일을 사용한 마이그레이션에 관한 SQL Server 고객 자문 팀 블로그는 [BACPAC 파일을 사용하여 SQL Server에서 Azure SQL Database로 마이그레이션](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)을 참조하세요.
* 성능 권장 사항을 비롯한 전체 SQL Server 데이터베이스 마이그레이션 프로세스에 대한 설명은 [Azure SQL Database에 SQL Server 데이터베이스 마이그레이션](sql-database-cloud-migrate.md)을 참조하세요.


<!--Image references-->
[1]: ./media/sql-database-import/import-database.png
[2]: ./media/sql-database-import/storage-options.png
[3]: ./media/sql-database-import/pricing-tier.png
[4]: ./media/sql-database-import/create.png
[5]: ./media/sql-database-import/import-history.png
[6]: ./media/sql-database-import/import-status.png

