---
title: "Azure SQL Database 백업에서 단일 테이블 복원 | Microsoft Docs"
description: "Azure SQL 데이터베이스 백업에서 단일 테이블을 복원하는 방법을 알아봅니다."
services: sql-database
documentationcenter: 
author: dalechen
manager: cshepard
editor: 
ms.assetid: 340b41bd-9df8-47fb-adfc-03216de38a5e
ms.service: sql-database
ms.custom: business continuity
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: daleche
ms.translationtype: Human Translation
ms.sourcegitcommit: 9992b6a2bf73fd84c7c47783d1f4f13e10889a81
ms.openlocfilehash: 8c074243db2ae729c4fd1a483e5ac40fbbebd750
ms.contentlocale: ko-kr
ms.lasthandoff: 01/19/2017


---
# <a name="how-to-restore-a-single-table-from-an-azure-sql-database-backup"></a>Azure SQL 데이터베이스 백업에서 단일 테이블을 복원하는 방법
SQL 데이터베이스의 일부 데이터를 실수로 변경했지만 이제 영향을 받는 단일 테이블을 복구하려는 상황에 처할 수 있습니다. 이 문서에서는 SQL 데이터베이스 [자동 백업](sql-database-automated-backups.md)중 하나에서 데이터베이스의 단일 테이블을 복원하는 방법을 설명합니다.

## <a name="preparation-steps-rename-the-table-and-restore-a-copy-of-the-database"></a>준비 단계: 테이블 이름 바꾸기 및 데이터베이스의 복사본 복원
1. 복원된 복사본으로 대체하려는 Azure SQL 데이터베이스의 테이블을 식별합니다. Microsoft SQL Management Studio를 사용하여 테이블 이름을 바꿉니다. 예를 들어 테이블의 이름을 &lt;table name&gt;_old로 바꿉니다.
   
   > [!NOTE]
   > 차단되지 않으려면 이름을 바꾸는 테이블에서 실행 중인 작업이 없어야 합니다. 문제가 발생하면 유지 관리 기간 동안 이 절차를 수행해야 합니다.
   >

2. [지정 지점 복원](sql-database-recovery-using-backups.md#point-in-time-restore) 단계를 사용하여 복구하려는 시점으로 데이터베이스의 백업을 복원합니다.
   
   > [!NOTE]
   > 복원된 데이터베이스의 이름은 **Adventureworks2012_2016-01-01T22-12Z**와 같은 DBName+TimeStamp 형식입니다. 이 단계는 서버에서 기존 데이터베이스 이름을 덮어쓰지 않습니다. 이것은 안전 조치이며 현재 데이터베이스를 삭제하고 프로덕션 사용을 위해 복원된 데이터베이스의 이름을 변경하기 전에 사용자가 복원된 데이터베이스를 확인하려는 용도로 제공됩니다.
   
## <a name="copying-the-table-from-the-restored-database-by-using-the-sql-database-migration-tool"></a>SQL 데이터베이스 마이그레이션 도구를 사용하여 복원된 데이터베이스에서 테이블 복사

1. [SQL 데이터베이스 마이그레이션 마법사](https://sqlazuremw.codeplex.com)를 다운로드하여 설치합니다.
2. SQL Database 마이그레이션 마법사를 열고 **프로세스 선택** 페이지에서 **분석/마이그레이션의 데이터베이스**를 선택하고 **다음**을 클릭합니다.

   ![SQL 데이터베이스 마이그레이션 마법사 - 프로세스 선택](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/1.png)

3. **서버에 연결** 대화 상자에서 다음 설정을 적용합니다.

   * 서버 이름: **SQL server**
   * 응용 프로그램: **SQL Server Authentication**
   * 로그인: **로그인**
   * 암호: **암호**
   * 데이터베이스: **Master DB (모든 데이터베이스 나열)**
   
   > [!NOTE]
   > 기본적으로 마법사는 사용자의 로그인 정보를 저장합니다. 저장하지 않으려면 **로그인 정보 삭제**를 선택합니다.
   >
   
     ![SQL 데이터베이스 마이그레이션 마법사 - 원본 선택 - 1단계](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/2.png)
4. **원본 선택** 대화 상자에서는 **준비 단계** 섹션의 복원된 데이터베이스 이름을 원본으로 선택하고 **다음**을 클릭합니다.
   
    ![SQL 데이터베이스 마이그레이션 마법사 - 원본 선택 - 2단계](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/3.png)
5. **개체 선택** 대화 상자에서는 **특정 데이터베이스 개체 선택** 옵션을 선택한 다음 대상 서버로 마이그레이션하려는 테이블(다수 가능)을 선택합니다.
   ![SQL 데이터베이스 마이그레이션 마법사 - 개체 선택](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/4.png)
6. **스크립트 마법사 요약** 페이지에서 SQL 스크립트를 생성할 준비가 되었는지 묻는 메시지가 표시되면 **예**를 클릭합니다. 또한 나중에 사용할 TSQL 스크립트를 저장하는 옵션이 있습니다.
   ![SQL 데이터베이스 마이그레이션 마법사 - 스크립트 마법사 요약](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/5.png)
7. **결과 요약** 페이지에서 **다음**을 클릭합니다.
   ![SQL 데이터베이스 마이그레이션 마법사 - 결과 요약](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/6.png)
8. **대상 서버 연결 설정** 페이지에서 **서버에 연결**을 클릭한 다음 세부 정보를 다음과 같이 입력합니다.
   
   * **서버 이름**: 대상 서버 인스턴스
   * **인증**: **SQL Server 인증** 로그인 자격 증명을 입력합니다.
   * **데이터베이스**: **마스터 DB(모든 데이터베이스를 나열)**. 이 옵션은 대상 서버에 있는 모든 데이터베이스를 나열합니다.
     
     ![SQL 데이터베이스 마이그레이션 마법사 - 대상 서버 연결 설정](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/7.png)
9. **연결**을 클릭하고 테이블을 이동하려는 대상 데이터베이스를 선택하고 **다음**을 클릭합니다. 이전에 생성된 스크립트의 실행을 완료해야 하고 대상 데이터베이스로 복사된 새로 이동시킨 테이블이 표시되어야 합니다.

## <a name="verification-step"></a>확인 단계

- 새로 복사된 테이블을 쿼리하고 테스트하여 데이터가 그대로 유지되는지 확인합니다. 확인되면 이름이 바뀐 테이블 형식 **준비 단계** 섹션을 삭제할 수 있습니다. (예를 들어 &lt;테이블 이름&gt;_old).

## <a name="next-steps"></a>다음 단계
[SQL 데이터베이스 자동 백업](sql-database-automated-backups.md)


