---
title: "Azure Portal: Azure SQL Database를 BACPAC 파일로 내보내기 | Microsoft Docs"
description: "Azure Portal을 사용하여 Azure SQL Database를 BACPAC 파일로 내보내기"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 3d04be3d2427bc59d24bfaad227730991b61265b
ms.openlocfilehash: ed983134e468a69c0e89387a3211479630a278e3


---
# <a name="export-an-azure-sql-database-to-a-bacpac-file-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure SQL Database를 BACPAC 파일로 내보내기

이 문서에서는 [Azure Portal](https://portal.azure.com)을 사용하여 Azure SQL Database를 Azure Blob Storage에 저장된 BACPAC 파일로 내보내기 위한 지침을 제공합니다. BACPAC 파일로 내보내기의 개요는 [BACPAC로 내보내기](sql-database-export.md)를 참조하세요.

> [!NOTE]
> [SQL Server Management Studio](sql-database-export-ssms.md), [PowerShell](sql-database-export-powershell.md) 또는 [SQLPackage](sql-database-export-sqlpackage.md)를 사용하여 Azure SQL Database 파일을 BACPAC 파일로 내보낼 수도 있습니다.
>

## <a name="prerequisites"></a>필수 조건

이 문서를 완료하려면 다음이 필요합니다.

* Azure 구독.
* Azure SQL 데이터베이스. 
* 표준 저장소에 BACPAC를 저장할 Blob 컨테이너가 있는 [Azure 표준 저장소 계정](../storage/storage-create-storage-account.md) .

## <a name="export-your-database"></a>데이터베이스 내보내기

1. [Azure 포털](https://portal.azure.com)로 이동합니다.
2. 내보낼 데이터베이스에 대한 SQL 데이터베이스 블레이드를 엽니다.
3. 내보내기 중에 트랜잭션이 발생하지 않는지 확인합니다. 

   > [!IMPORTANT]
   > BACPAC 파일의 트랜잭션 일관성을 보장하는 가장 좋은 방법은 [데이터베이스의 사본을 만든](sql-database-copy.md) 다음 데이터베이스 사본을 내보내는 것입니다. 
   > 

4. **SQL 데이터베이스**를 클릭합니다.
5. 보관할 데이터베이스를 클릭합니다.
6. SQL 데이터베이스 블레이드에서 **내보내기**를 클릭하여 **데이터베이스 내보내기** 블레이드를 엽니다.
   
   ![내보내기 단추][1]
7. **저장소**를 클릭하고 저장소 계정 및 BACPAC을 저장할 Blob 컨테이너를 선택합니다.
   
   ![데이터베이스 내보내기][2]
8. 인증 유형을 선택합니다. 
9. 내보내려는 데이터베이스가 포함된 Azure SQL Server의 적절한 인증 자격 증명을 입력합니다.
10. **확인**을 클릭하여 데이터베이스를 내보냅니다. **확인** 을 클릭하면 데이터베이스 내보내기 요청이 생성되고 서비스에 제출됩니다. 내보내기에 걸리는 시간은 데이터베이스의 크기와 복잡성 및 서비스 수준에 따라 달라집니다. 
11. 받은 알림을 봅니다.
   
   ![내보내기 알림][3]

## <a name="monitor-the-progress-of-the-export-operation"></a>내보내기 작업의 진행률 모니터링
1. **SQL Server**를 클릭합니다.
2. 보관한 원본 데이터베이스를 포함하는 서버를 클릭합니다.
3. 작업까지 아래로 스크롤합니다.
4. SQL Server 블레이드에서 **가져오기/내보내기 기록**을 클릭합니다.
   
   ![가져오기 내보내기 기록][4]

## <a name="verify-the-bacpac-is-in-your-storage-container"></a>BACPAC이 저장소 컨테이너에 있는지 확인
1. **저장소 계정**을 클릭합니다.
2. BACPAC 보관 파일을 저장한 저장소 계정을 클릭합니다.
3. **컨테이너** 를 클릭하고 데이터베이스를 내보낸 컨테이너를 선택하여 세부 정보를 확인합니다(여기서 BACPAC을 다운로드 및 저장할 수 있음).
   
   ![.bacpac 파일 세부 정보][5]    

## <a name="next-steps"></a>다음 단계

* 보관을 위해 데이터베이스를 내보내는 방법의 대안으로 사용되는 Azure SQL Database 백업의 장기 백업 보존에 대해 알아보려면 [장기 백업 보존](sql-database-long-term-retention.md)을 참조하세요.
* SQL Server 데이터베이스에 BACPAC를 가져오는 방법에 대해 자세히 알아보려면 [SQL Server 데이터베이스로 BACPCAC 가져오기](https://msdn.microsoft.com/library/hh710052.aspx)

<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-notification.png
[4]: ./media/sql-database-export/export-history.png
[5]: ./media/sql-database-export/bacpac-archive.png




<!--HONumber=Feb17_HO2-->


