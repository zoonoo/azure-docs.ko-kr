---
title: "첫 번째 Azure SQL Database 디자인 | Microsoft Docs"
description: "첫 번째 Azure SQL Database를 디자인하는 방법을 알아봅니다."
services: sql-database
documentationcenter: 
author: janeng
manager: jstrauss
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial-develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 03/30/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: 0d02954829ebac9275c014f7dac7e1ec423b0fc1
ms.lasthandoff: 04/21/2017


---

# <a name="design-your-first-azure-sql-database"></a>첫 번째 Azure SQL Database 디자인

이 자습서에서는 대학에 대한 데이터베이스를 빌드하여 학년 및 수강 신청을 추적합니다. 이 자습서는 [Azure Portal](https://portal.azure.com/) 및 SSMS([SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx))를 사용하여 Azure SQL Database 논리 서버에 Azure SQL Database를 만들고 데이터베이스에 테이블을 추가하고 테이블에 데이터를 로드하고 데이터베이스를 쿼리하는 방법에 대해 설명합니다. 또한 SQL Database [특정 시점 복원](sql-database-recovery-using-backups.md#point-in-time-restore) 기능을 사용하여 데이터베이스를 이전의 특정 시점으로 복원하는 방법에 대해서도 설명합니다.

이 자습서를 완료하려면 최신 버전의 SSMS([SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx))를 설치했는지 확인합니다. 

## <a name="log-in-to-the-azure-portal"></a>Azure 포털에 로그인

[Azure 포털](https://portal.azure.com/)에 로그인합니다.

## <a name="create-a-blank-sql-database-in-azure"></a>Azure에서 빈 SQL Database 만들기

Azure SQL Database는 일련의 정의된 [계산 및 저장소 리소스](sql-database-service-tiers.md)를 사용하여 만들어집니다. 데이터베이스는 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md) 및 [Azure SQL Database 논리 서버](sql-database-features.md)에서 만들어집니다. 

빈 SQL Database를 만들려면 다음 단계를 수행합니다. 

1. Azure Portal의 왼쪽 위에 있는 **새로 만들기** 단추를 클릭합니다.

2. **새로 만들기** 페이지에서 **데이터베이스**를 선택하고 **데이터베이스** 페이지에서 **SQL Database**를 선택합니다. 

    ![빈 데이터베이스 만들기](./media/sql-database-design-first-database/create-empty-database.png)

3. 위의 이미지에 표시된 대로 다음과 같은 정보를 사용하여 SQL Database 형식을 작성합니다.     

   - 데이터베이스 이름: **mySampleDatabase**
   - 리소스 그룹: **myResourceGroup**
   - 원본: **빈 데이터베이스**

4. **서버**를 클릭하여 새 데이터베이스에 새 서버를 만들고 구성합니다. 전역적으로 고유한 서버 이름을 지정하는 **새 서버 형식**을 채우고 서버 관리자 로그인에 이름을 제공한 다음 사용자가 선택한 암호를 지정합니다. 

    ![create database-server](./media//sql-database-design-first-database/create-database-server.png)
5. **선택**을 클릭합니다.

6. **가격 책정 계층**을 클릭하고 새 데이터베이스의 서비스 계층 및 성능 수준을 지정합니다. 이 자습서에서는 **20 DTU** 및 **250**GB의 저장소를 선택합니다.

    ![create database-s1](./media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

7. **Apply**를 클릭합니다.  

8. **만들기**를 클릭하여 데이터베이스를 프로비전합니다. 프로비전을 완료하는 데 약 1분 30초가 걸립니다. 

9. 도구 모음에서 **알림**을 클릭하여 배포 프로세스를 모니터링합니다.

    ![알림](./media/sql-database-get-started-portal/notification.png)


## <a name="create-a-server-level-firewall-rule"></a>서버 수준 방화벽 규칙 만들기

Azure SQL Database는 방화벽으로 보호됩니다. 기본적으로 서버 및 서버 내의 데이터베이스에 대한 모든 연결은 거부됩니다. 클라이언트의 IP 주소에서 연결을 허용하도록 서버에 대한 [SQL Database 서버 수준 방화벽 규칙](sql-database-firewall-configure.md)을 만들려면 다음 단계를 수행합니다. 

1. 배포가 완료되면 왼쪽 메뉴에서 **SQL Database**를 클릭하고 **SQL Database** 페이지에서 새 데이터베이스인 **mySampleDatabase**를 클릭합입니다. 데이터베이스에 대한 개요 페이지가 열리고 이 페이지에 정규화된 서버 이름(예: **mynewserver-20170313.database.windows.net**)이 표시되며 추가 구성을 위한 옵션도 제공됩니다.

      ![서버 방화벽 규칙](./media/sql-database-design-first-database/server-firewall-rule.png) 

2. 이전 이미지에 표시된 대로 도구 모음에서 **서버 방화벽 설정**을 클릭합니다. SQL Database 서버에 대한 **방화벽 설정** 페이지가 열립니다. 

3. 도구 모음에서 **클라이언트 IP 추가**를 클릭하고 **저장**을 클릭합니다. 현재 IP 주소에 대한 서버 수준 방화벽 규칙이 생성됩니다.

      ![set server firewall rule](./media/sql-database-design-first-database/server-firewall-rule-set.png) 

4. **확인**을 클릭한 후 **X**를 클릭하여 **방화벽 설정** 페이지를 닫습니다.

이제 SQL Server Management Studio 또는 선택한 다른 도구를 사용하여 데이터베이스와 해당 서버에 연결할 수 있습니다.

> [!NOTE]
> SQL Database는 포트 1433을 통해 통신합니다. 회사 네트워크 내에서 연결을 시도하는 경우 포트 1433을 통한 아웃바운드 트래픽이 네트워크 방화벽에서 허용되지 않을 수 있습니다. 이 경우 IT 부서에서 포트 1433을 열지 않으면 Azure SQL Database 서버에 연결할 수 없습니다
>

## <a name="get-connection-information"></a>연결 정보 가져오기

Azure Portal에 있는 Azure SQL Database 서버의 정규화된 서버 이름을 가져옵니다. 정규화된 서버 이름을 사용하여 SQL Server Management Studio를 사용하는 서버에 연결합니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 메뉴에서 **SQL Database**를 선택하고 **SQL Database** 페이지에서 데이터베이스를 클릭합니다. 
3. 데이터베이스의 경우 Azure Portal의 **Essentials** 창에서 **서버 이름**을 찾고 복사합니다.

    ![연결 정보](./media/sql-database-connect-query-ssms/connection-information.png) 

## <a name="connect-to-your-database-using-sql-server-management-studio"></a>SQL Server Management Studio를 사용하여 데이터베이스에 연결

[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)를 사용하여 Azure SQL Database 서버에 연결합니다.

1. SQL Server Management Studio를 엽니다.

2. **서버에 연결** 대화 상자에서 다음 정보를 입력합니다.
   - **서버 유형**: 데이터베이스 엔진을 지정합니다.
   - **서버 이름**: **mynewserver20170313.database.windows.net**과 같은 정규화된 서버 이름을 입력합니다.
   - **인증**: SQL Server 인증을 지정합니다.
   - **로그인**: 서버 관리자 계정을 입력합니다.
   - **암호**: 서버 관리자 계정의 암호를 입력합니다.


   <img src="./media/sql-database-connect-query-ssms/connect.png" alt="connect to server" style="width: 780px;" />

3. **서버에 연결** 대화 상자에서 **옵션**을 클릭합니다. **데이터베이스에 연결** 섹션에서 **mySampleDatabase**를 입력하여 이 데이터베이스에 연결합니다.

   ![서버에서 db에 연결](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. **Connect**를 클릭합니다. SSMS에서 개체 탐색기 창이 열립니다. 

5. 개체 탐색기에서 **데이터베이스**를 확장한 다음 **mySampleDatabase**를 확장하여 샘플 데이터베이스에 있는 개체를 봅니다.

   ![데이터베이스 개체](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="create-tables-in-the-database"></a>데이터베이스에서 테이블 만들기 

[Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)을 사용하여 대학의 학생 관리 시스템을 모델링하는 네 개의 테이블이 있는 데이터베이스 스키마 만들기

- 사람
- 과정
- 학생
- 대학의 학생 관리 시스템을 모델링하는 크레딧

다음 다이어그램에서는 이러한 테이블 간의 관계를 보여 줍니다. 이러한 테이블 중 일부는 다른 테이블의 열을 참조합니다. 예를 들어 Student 테이블은 **Person** 테이블의 **PersonId** 열을 참조합니다. 다이어그램에 대해 학습하여 이 자습서에서 테이블 간의 관계를 이해합니다. 효과적인 데이터베이스 테이블을 만드는 방법에 대한 자세한 내용은 [효과적인 데이터베이스 테이블 만들기](https://msdn.microsoft.com/library/cc505842.aspx)를 참조하세요. 데이터 형식을 선택하는 방법은 [데이터 형식](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql)을 참조하세요.

> [!NOTE]
> 또한 [SQL Server Management Studio의 테이블 디자이너](https://msdn.microsoft.com/library/hh272695.aspx)를 사용하여 테이블을 만들고 디자인할 수도 있습니다. 

![테이블 관계](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. 개체 탐색기에서 **mySampleDatabase**를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 클릭합니다. 데이터베이스에 연결된 비어 있는 쿼리 창이 열립니다.

2. 쿼리 창에서 다음 쿼리를 실행하여 데이터베이스에 4개의 테이블을 만듭니다. 

   ```sql 
   -- Create Person table

    CREATE TABLE Person
    (
      PersonId      INT IDENTITY PRIMARY KEY,
      FirstName     NVARCHAR(128) NOT NULL,
      MiddelInitial NVARCHAR(10),
      LastName      NVARCHAR(128) NOT NULL,
      DateOfBirth   DATE NOT NULL
    )
   
   -- Create Student table
 
    CREATE TABLE Student
    (
      StudentId INT IDENTITY PRIMARY KEY,
      PersonId  INT REFERENCES Person (PersonId),
      Email     NVARCHAR(256)
    )
    
   -- Create Course table
 
    CREATE TABLE Course
    (
      CourseId  INT IDENTITY PRIMARY KEY,
      Name      NVARCHAR(50) NOT NULL,
      Teacher   NVARCHAR(256) NOT NULL
    ) 

   -- Create Credit table
 
    CREATE TABLE Credit
    (
      StudentId   INT REFERENCES Student (StudentId),
      CourseId    INT REFERENCES Course (CourseId),
      Grade       DECIMAL(5,2) CHECK (Grade <= 100.00),
      Attempt     TINYINT,
      CONSTRAINT  [UQ_studentgrades] UNIQUE CLUSTERED
      (
        StudentId, CourseId, Grade, Attempt
      )
    )
   ```

![테이블 만들기](./media/sql-database-design-first-database/create-tables.png)

3. 사용자가 만든 테이블을 보려면 SQL Server Management Studio 개체 탐색기에서 '테이블' 노드를 확장합니다.

   ![ssms 테이블 생성](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>테이블에 데이터 로드

1. 다운로드 폴더에 **SampleTableData**라는 폴더를 만들어 데이터베이스의 샘플 데이터를 저장합니다. 

2. 다음 링크를 마우스 오른쪽 단추로 클릭하고 **SampleTableData** 폴더에 샘플 데이터를 저장합니다. 

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. 명령 프롬프트 창을 열고 SampleTableData 폴더로 이동합니다.

4. 다음 명령을 실행하여 테이블에 샘플 데이터를 삽입합니다. **ServerName**, **DatabaseName**, **UserName** 및 **Password** 값은 사용자 환경에 대한 값으로 바꿉니다.
  
   ```bcp
   bcp Course in SampleCourseData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   ```

이제 앞에서 만든 테이블에 샘플 데이터가 로드되었습니다.

## <a name="query-the-tables"></a>테이블 쿼리

다음 쿼리를 실행하여 데이터베이스 테이블에서 정보를 검색합니다. SQL 쿼리 작성에 대한 자세한 내용은 [SQL 쿼리 작성](https://technet.microsoft.com/library/bb264565.aspx)을 참조하세요. 첫 번째 쿼리는 4개의 테이블을 모두 조인하여 'Dominick Pope' 선생님의 학생 중에 성적이 75%보다 높은 모든 학생을 찾습니다. 두 번째 쿼리는 4개의 테이블을 모두 조인하여 'Noe Coleman'이 등록한 적 있는 모든 과정을 찾습니다.

1. SQL Server Management Studio 쿼리 창에서 다음 쿼리를 실행합니다.

   ```sql 
   -- Find the students taught by Dominick Pope who have a grade higher than 75%

    SELECT  person.FirstName,
        person.LastName,
        course.Name,
        credit.Grade
    FROM  Person AS person
        INNER JOIN Student AS student ON person.PersonId = student.PersonId
        INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
        INNER JOIN Course AS course ON credit.CourseId = course.courseId
    WHERE course.Teacher = 'Dominick Pope' 
        AND Grade > 75
   ```

2. SQL Server Management Studio 쿼리 창에서 다음 쿼리를 실행합니다.

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled

    SELECT  course.Name,
        course.Teacher,
        credit.Grade
    FROM  Course AS course
        INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
        INNER JOIN Student AS student ON student.StudentId = credit.StudentId
        INNER JOIN Person AS person ON person.PersonId = student.PersonId
    WHERE person.FirstName = 'Noe'
        AND person.LastName = 'Coleman'
   ```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>이전 시점으로 데이터베이스 복원 

실수로 테이블을 삭제한 경우를 가정해 보겠습니다. 이런 경우는 쉽게 복구할 수 없는 경우입니다. Azure SQL Database를 사용하면 최근 35일 내 특정 시점으로 돌아가서 이 특정 시점을 새 데이터베이스에 복원할 수 있습니다. 이 데이터베이스를 사용하여 삭제된 데이터를 복구할 수 있습니다. 다음 단계를 수행하면 샘플 데이터베이스가 테이블이 추가되기 이전 시점으로 복원됩니다.

1. 데이터베이스에 대한 SQL Database 페이지의 도구 모음에서 **복원**을 클릭합니다. **복원** 페이지가 열립니다.

   ![복원](./media/sql-database-design-first-database/restore.png)

2. 필요한 정보로 **복원** 양식을 채웁니다.
    * 데이터베이스 이름: 데이터베이스 이름을 입력합니다. 
    * 지정 시간: 복원 양식의 **지정 시간** 탭 
    * 복원 지점: 데이터베이스를 변경하기 이전 시간 선택
    * 대상 서버: 데이터베이스를 복원할 때는 이 값을 변경할 수 없습니다. 
    * 탄력적 데이터베이스 풀: **없음** 선택  
    * 가격 책정 계층: **20DTU** 및 **250**GB의 저장소를 선택합니다.

   ![복원 시점](./media/sql-database-design-first-database/restore-point.png)

3. **확인**을 클릭하여 데이터베이스를 테이블이 추가되기 이전 [시점으로 복원](sql-database-recovery-using-backups.md#point-in-time-restore)합니다. 다른 시점으로 데이터베이스를 복원하면 지정한 시점([서비스 계층](sql-database-service-tiers.md)에 대한 보존 기간 이내에서 제공)에서 원본 데이터베이스가 있는 같은 서버에 중복 데이터베이스가 생성됩니다.

## <a name="next-steps"></a>다음 단계 

일반적인 작업을 위한 PowerShell 샘플을 보려면 [SQL Database PowerShell 샘플](sql-database-powershell-samples.md)을 참조하세요.

