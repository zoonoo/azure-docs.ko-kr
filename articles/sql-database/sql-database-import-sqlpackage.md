---
title: "SqlPackage: BACPAC 파일에서 Azure SQL Database로 가져오기 | Microsoft Docs"
description: "이 문서에서는 SqlPackage 명령줄 유틸리티를 사용하여 BACPAC 파일에서 SQL Database로 데이터를 가져오는 방법을 설명합니다."
keywords: "Microsoft Azure SQL 데이터베이스, 데이터베이스 마이그레이션, 데이터베이스 가져오기, BACPAC 파일 가져오기, sqlpackage"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 424afa27-5f13-4ec3-98f6-99a511a6a2df
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 02/08/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e6f0d661465c813ec310b8c69ab1ee06e4f95401
ms.openlocfilehash: 23c5da7b9e171312274aec02262b139ee701deb1


---
# <a name="import-a-database-from-a-bacpac-file-to-azure-sql-database-using-sqlpackage"></a>SqlPackage를 사용하여 BACPAC 파일에서 Azure SQL Database로 데이터베이스 가져오기

이 문서에서는 [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 명령줄 유틸리티를 사용하여 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 파일에서 SQL Database로 데이터를 가져오는 방법을 설명합니다. 이 유틸리티는 최신 버전의 [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 및 [Visual Studio용 SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)가 함께 제공되며, Microsoft 다운로드 센터에서 직접 최신 버전의 [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876)를 다운로드할 수 있습니다.

> [!NOTE]
> 아래 단계에서는 사용자가 이미 SQL Database 서버를 프로비전했고, 연결 정보를 알고 있으며, 원본 데이터베이스가 호환됨을 확인했다고 가정합니다.
> 
> 

## <a name="import-the-database"></a>데이터베이스 가져오기
[SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) 명령줄 유틸리티를 사용하여 호환되는 SQL Server 데이터베이스(또는 Azure SQL Database)를 BACPAC 파일에서 가져옵니다.

> [!NOTE]
> 아래 단계에서는 사용자가 이미 Azure SQL Database 서버를 프로비전했으며 연결 정보를 알고 있다고 가정합니다.
>  

sqlpackage.exe 명령줄 유틸리티의 최신 버전이 포함된 디렉터리의 명령 프롬프트에서 다음 샘플 명령과 유사한 명령을 실행하여 Premium P11로 BACPAC 파일을 Azure SQL Database로 가져옵니다.

```
SqlPackage.exe /a:import /tcs:"Data Source=SERVER;Initial Catalog=DBNAME;User Id=USER;Password=PASSWORD" /sf:C:\db.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P11
```   

## <a name="next-steps"></a>다음 단계

* 성능 권장 사항을 비롯한 전체 SQL Server 데이터베이스 마이그레이션 프로세스에 대한 설명은 [Azure SQL Database에 SQL Server 데이터베이스 마이그레이션](sql-database-cloud-migrate.md)을 참조하세요.
* SQLPackage에 대한 자세한 내용은 [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx)를 참조하세요.
* 최신 버전의 SQLPackage를 다운로드하려면 [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876)를 참조하세요.
* BACPAC 파일을 사용한 마이그레이션에 관한 SQL Server 고객 자문 팀 블로그는 [BACPAC 파일을 사용하여 SQL Server에서 Azure SQL Database로 마이그레이션](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)을 참조하세요.




<!--HONumber=Feb17_HO2-->


