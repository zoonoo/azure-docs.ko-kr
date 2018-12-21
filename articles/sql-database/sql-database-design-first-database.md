---
title: '자습서: SSMS를 사용하여 첫 번째 Azure SQL Database 디자인 | Microsoft Docs'
description: SQL Server Management Studio를 사용하여 첫 번째 Azure SQL Database를 디자인하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 9fa36b9b87a8e9591b0c863826cd2278a29ba28e
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956060"
---
# <a name="tutorial-design-your-first-azure-sql-database-using-ssms"></a>자습서: SSMS를 사용하여 첫 번째 Azure SQL Database 디자인

Azure SQL Database는 Microsoft Cloud(Azure)의 관계형 DBaaS(Database-As-A-Service)입니다. 이 자습서에서는 Azure Portal 및 SSMS[(SQL Server Management Studio)](https://msdn.microsoft.com/library/ms174173.aspx)를 사용하는 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Portal에서 데이터베이스 만들기*
> * Azure Portal에서 서버 수준 방화벽 규칙 설정
> * SSMS로 데이터베이스에 연결
> * SSMS를 사용하여 테이블 만들기
> * BCP를 사용하여 데이터 대량 로드
> * SSMS를 사용하여 데이터 쿼리

*Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

> [!NOTE]
> 이 자습서를 사용하기 위해 여기서는 [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md)을 사용하지만, 사용자는 [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md) 선택 옵션을 사용할 수도 있습니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음 항목이 설치되어 있어야 합니다.

- [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx)(최신 버전)
- [BCP 및 SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433)(최신 버전)

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-a-blank-database"></a>빈 데이터베이스 만들기

Azure SQL Database는 일련의 정의된 [계산 및 저장소 리소스](sql-database-service-tiers-dtu.md)를 사용하여 만들어집니다. 데이터베이스는 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md) 및 [Azure SQL Database 논리 서버](sql-database-features.md)에 만들어집니다.

빈 SQL Database를 만들려면 다음 단계를 수행합니다.

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 클릭합니다.

1. **새로 만들기** 페이지의 Azure Marketplace 섹션에서 **데이터베이스**를 선택한 다음, **추천** 섹션에서 **SQL Database**를 클릭합니다.

   ![빈 데이터베이스 만들기](./media/sql-database-design-first-database/create-empty-database.png)

   1. 위의 이미지에 표시된 대로 다음과 같은 정보를 사용하여 **SQL Database** 형식을 작성합니다.

      | 설정       | 제안 값 | 설명 |
      | ------------ | ------------------ | ------------------------------------------------- |
      | **데이터베이스 이름** | *yourDatabase* | 유효한 데이터베이스 이름은 [데이터베이스 식별자](/sql/relational-databases/databases/database-identifiers)를 참조하세요. |
      | **구독** | *yourSubscription*  | 구독에 대한 자세한 내용은 [구독](https://account.windowsazure.com/Subscriptions)을 참조하세요. |
      | **리소스 그룹** | *yourResourceGroup* | 유효한 리소스 그룹 이름은 [명명 규칙 및 제한 사항](/azure/architecture/best-practices/naming-conventions)을 참조하세요. |
      | **원본 선택** | 빈 데이터베이스 | 빈 데이터베이스를 만들도록 지정합니다. |

   1. **서버**를 클릭하여 기존 서버를 사용하거나 새 서버를 만들고 데이터베이스에 맞게 구성합니다. 서버를 선택하거나 **새 서버 만들기**를 클릭하고 **새 서버** 양식에 다음 정보를 입력합니다.

      | 설정       | 제안 값 | 설명 |
      | ------------ | ------------------ | ------------------------------------------------- |
      | **서버 이름** | 전역적으로 고유한 이름 | 유효한 서버 이름은 [명명 규칙 및 제한 사항](/azure/architecture/best-practices/naming-conventions)을 참조하세요. |
      | **서버 관리자 로그인** | 모든 유효한 이름 | 유효한 로그인 이름은 [데이터베이스 식별자](/sql/relational-databases/databases/database-identifiers)를 참조하세요. |
      | **암호** | 유효한 암호 | 암호는 8자 이상이어야 하며 대문자, 소문자, 숫자 및 영숫자가 아닌 문자 범주 중 세 가지 범주의 문자를 사용해야 합니다. |
      | **위치**: | 모든 유효한 위치 | 지역에 대한 자세한 내용은 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요. |

      ![create database-server](./media/sql-database-design-first-database/create-database-server.png)

      **선택**을 클릭합니다.

   1. **가격 책정 계층**을 클릭하여 서비스 계층, DTU나 vCore 개수 및 저장소 크기를 지정합니다. 각 서비스 계층에 대해 사용할 수 있는 DTU/vCore 및 스토리지 수에 대한 옵션을 살펴볼 수 있습니다. 기본적으로 **표준** [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md)이 선택되지만, [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)을 선택할 수도 있습니다.

      > [!IMPORTANT]
      > 현재 다음 지역을 제외한 모든 지역에서 프리미엄 계층의 스토리지 1TB 이상을 사용할 수 있습니다. 영국 북부, 미국 중서부, 영국 남부2, 중국 동부, USDoDCentral, 독일 중부, USDoDEast, US Gov 남서부, US Gov 중남부, 독일 북동부, 중국 북부, US Gov 동부 다른 지역에서 프리미엄 계층 저장소 크기는 1TB로 제한됩니다. [P11-P15 현재 제한 사항]( sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)을 참조하세요.

      서버 계층, DTU 수, 스토리지 양을 선택한 후 **적용**을 클릭합니다.

   1. 빈 데이터베이스에 대한 **데이터 정렬**을 입력합니다(이 자습서의 경우 기본값 사용). 데이터 정렬에 대한 자세한 내용은 [데이터 정렬](/sql/t-sql/statements/collations)을 참조하세요.

1. 이제 **SQL Database** 양식을 완료했으므로 **만들기**를 클릭하여 데이터베이스를 프로비전합니다. 이 단계를 완료하는 데 몇 분이 걸릴 수 있습니다.

1. 도구 모음에서 **알림**을 클릭하여 배포 프로세스를 모니터링합니다.

     ![알림](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-firewall-rule"></a>방화벽 규칙 만들기

SQL 데이터베이스 서비스는 서버 수준에서 방화벽을 만듭니다. 방화벽은 외부 애플리케이션 및 도구가 서버 및 서버의 데이터베이스에 연결하지 못하게 차단합니다. 데이터베이스에 대한 외부 연결을 사용하려면 먼저 IP 주소에 대한 규칙을 방화벽에 추가해야 합니다. 다음 단계에 따라 [SQL 데이터베이스 서버 수준 방화벽 규칙을 만듭니다](sql-database-firewall-configure.md).

> [!NOTE]
> SQL 데이터베이스는 포트 1433을 통해 통신합니다. 회사 네트워크 내에서 연결을 시도하는 경우 포트 1433을 통한 아웃바운드 트래픽이 네트워크 방화벽에서 허용되지 않을 수 있습니다. 이 경우 관리자가 1433 포트를 열지 않으면 Azure SQL Database 서버에 연결할 수 없습니다.

1. 배포가 완료되면 왼쪽 메뉴에서 **SQL 데이터베이스**를 클릭한 다음, **SQL 데이터베이스** 페이지에서 *yourDatabase*를 클릭합니다. 정규화된 **서버 이름**(예: *yourserver.database.windows.net*)을 표시하고 추가 구성 옵션을 제공하는 데이터베이스 개요 페이지가 열립니다.

1. SQL Server Management Studio에서 서버 및 데이터베이스에 연결하는 데 사용할 수 있도록 이 정규화된 서버 이름을 복사합니다.

   ![서버 이름](./media/sql-database-design-first-database/server-name.png)

1. 도구 모음에서 **서버 방화벽 설정**을 클릭합니다. SQL 데이터베이스 서버의 **방화벽 설정** 페이지가 열립니다.

   ![서버 방화벽 규칙](./media/sql-database-design-first-database/server-firewall-rule.png)

   1. 도구 모음에서 **클라이언트 IP 추가**를 클릭하여 현재 IP 주소를 새 방화벽 규칙에 추가합니다. 방화벽 규칙은 단일 IP 주소 또는 IP 주소의 범위에 1433 포트를 열 수 있습니다.

   1. **저장**을 클릭합니다. 논리 서버의 1433 포트를 여는 현재 IP 주소에 서버 수준 방화벽 규칙이 생성됩니다.

   1. **확인**을 클릭한 후 **방화벽 설정** 페이지를 닫습니다.

이제 IP 주소가 방화벽을 통과할 수 있습니다. 이제 SQL Server Management Studio 또는 원하는 다른 도구를 사용하여 SQL 데이터베이스 서버 및 해당 데이터베이스에 연결할 수 있습니다. 이전에 만든 서버 관리자 계정을 사용해야 합니다.

> [!IMPORTANT]
> SQL 데이터베이스 방화벽을 통한 액세스는 기본적으로 모든 Azure 서비스에 대해 사용됩니다. 이 페이지에서 **끄기**를 클릭하여 모든 Azure 서비스에 대해 사용하지 않도록 설정합니다.

## <a name="connect-to-the-database"></a>데이터베이스에 연결

[SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms)를 사용하여 Azure SQL 데이터베이스 서버에 연결합니다.

1. SQL Server Management Studio를 엽니다.

1. **서버에 연결** 대화 상자에서 다음 정보를 입력합니다.

   | 설정       | 제안 값 | 설명 |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **서버 유형** | 데이터베이스 엔진 | 이 값은 필수입니다. |
   | **서버 이름** | 정규화된 서버 이름 | 예: *yourserver.database.windows.net*. |
   | **인증** | SQL Server 인증 | SQL 인증은 이 자습서에서 구성한 유일한 인증 유형입니다. |
   | **로그인** | 서버 관리자 계정 | 서버를 만들 때 지정한 계정입니다. |
   | **암호** | 서버 관리자 계정의 암호 | 서버를 만들 때 지정한 암호입니다. |

   ![서버 연결](./media/sql-database-design-first-database/connect.png)

   1. **서버에 연결** 대화 상자에서 **옵션**을 클릭합니다. **데이터베이스에 연결** 섹션에서 *yourDatabase*를 입력하여 이 데이터베이스에 연결합니다.

      ![서버에서 db에 연결](./media/sql-database-design-first-database/options-connect-to-db.png)  

   1. **Connect**를 클릭합니다. SSMS에서 **개체 탐색기** 창이 열립니다.

1. **개체 탐색기**에서 **데이터베이스**를 확장한 다음, *yourDatabase*를 확장하여 샘플 데이터베이스에 있는 개체를 봅니다.

   ![데이터베이스 개체](./media/sql-database-design-first-database/connected.png)  

## <a name="create-tables-in-the-database"></a>데이터베이스에서 테이블 만들기

[Transact-SQL](/sql/t-sql/language-reference)을 사용하여 대학의 학생 관리 시스템을 모델링하는 네 개의 테이블이 있는 데이터베이스 스키마 만들기

- 사람
- 과정
- 학생
- 크레딧

다음 다이어그램에서는 이러한 테이블 간의 관계를 보여 줍니다. 이러한 테이블 중 일부는 다른 테이블의 열을 참조합니다. 예를 들어 *Student* 테이블은 *Person* 테이블의 *PersonId* 열을 참조합니다. 다이어그램에 대해 학습하여 이 자습서에서 테이블 간의 관계를 이해합니다. 효과적인 데이터베이스 테이블을 만드는 방법에 대한 자세한 내용은 [효과적인 데이터베이스 테이블 만들기](https://msdn.microsoft.com/library/cc505842.aspx)를 참조하세요. 데이터 형식을 선택하는 방법은 [데이터 형식](/sql/t-sql/data-types/data-types-transact-sql)을 참조하세요.

> [!NOTE]
> 또한 [SQL Server Management Studio의 테이블 디자이너](/sql/ssms/visual-db-tools/design-database-diagrams-visual-database-tools)를 사용하여 테이블을 만들고 디자인할 수도 있습니다.

![테이블 관계](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. **개체 탐색기**에서 *yourDatabase*를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 선택합니다. 데이터베이스에 연결된 비어 있는 쿼리 창이 열립니다.

1. 쿼리 창에서 다음 쿼리를 실행하여 데이터베이스에 4개의 테이블을 만듭니다.

   ```sql
   -- Create Person table
   CREATE TABLE Person
   (
       PersonId INT IDENTITY PRIMARY KEY,
       FirstName NVARCHAR(128) NOT NULL,
       MiddelInitial NVARCHAR(10),
       LastName NVARCHAR(128) NOT NULL,
       DateOfBirth DATE NOT NULL
   )

   -- Create Student table
   CREATE TABLE Student
   (
       StudentId INT IDENTITY PRIMARY KEY,
       PersonId INT REFERENCES Person (PersonId),
       Email NVARCHAR(256)
   )

   -- Create Course table
   CREATE TABLE Course
   (
       CourseId INT IDENTITY PRIMARY KEY,
       Name NVARCHAR(50) NOT NULL,
       Teacher NVARCHAR(256) NOT NULL
   )

   -- Create Credit table
   CREATE TABLE Credit
   (
       StudentId INT REFERENCES Student (StudentId),
       CourseId INT REFERENCES Course (CourseId),
       Grade DECIMAL(5,2) CHECK (Grade <= 100.00),
       Attempt TINYINT,
       CONSTRAINT [UQ_studentgrades] UNIQUE CLUSTERED
       (
           StudentId, CourseId, Grade, Attempt
       )
   )
   ```

   ![테이블 만들기](./media/sql-database-design-first-database/create-tables.png)

1. 만든 테이블을 보려면 **개체 탐색기**에서 *yourDatabase* 아래에 있는 **테이블** 노드를 확장합니다.

   ![ssms 테이블 생성](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>테이블에 데이터 로드

1. 다운로드 폴더에 *sampleData*라는 폴더를 만들어 데이터베이스의 샘플 데이터를 저장합니다.

1. 다음 링크를 마우스 오른쪽 단추로 클릭하고 *sampleData* 폴더에 샘플 데이터를 저장합니다.

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

1. 명령 프롬프트 창을 열고 *sampleData* 폴더로 이동합니다.

1. 다음 명령을 실행하여 테이블에 샘플 데이터를 삽입하고 *서버*, *데이터베이스*, *사용자* 및 *암호* 값을 해당 환경에 맞는 값으로 바꿉니다.
  
   ```cmd
   bcp Course in SampleCourseData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   ```

이제 앞에서 만든 테이블에 샘플 데이터가 로드되었습니다.

## <a name="query-data"></a>쿼리 데이터

다음 쿼리를 실행하여 데이터베이스 테이블에서 정보를 검색합니다. SQL 쿼리 작성에 대한 자세한 내용은 [SQL 쿼리 작성](https://technet.microsoft.com/library/bb264565.aspx)을 참조하세요. 첫 번째 쿼리는 4개 테이블을 모두 조인하여 'Dominick Pope' 선생님의 학생 중에 성적이 75%보다 높은 학생을 찾습니다. 두 번째 쿼리는 4개 테이블을 모두 조인하여 'Noe Coleman'이 등록한 적이 있는 과정을 찾습니다.

1. SQL Server Management Studio 쿼리 창에서 다음 쿼리를 실행합니다.

   ```sql
   -- Find the students taught by Dominick Pope who have a grade higher than 75%
   SELECT  person.FirstName, person.LastName, course.Name, credit.Grade
   FROM  Person AS person
       INNER JOIN Student AS student ON person.PersonId = student.PersonId
       INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
       INNER JOIN Course AS course ON credit.CourseId = course.courseId
   WHERE course.Teacher = 'Dominick Pope'
       AND Grade > 75
   ```

1. 쿼리 창에서 다음 쿼리를 실행합니다.

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled
   SELECT  course.Name, course.Teacher, credit.Grade
   FROM  Course AS course
       INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
       INNER JOIN Student AS student ON student.StudentId = credit.StudentId
       INNER JOIN Person AS person ON person.PersonId = student.PersonId
   WHERE person.FirstName = 'Noe'
       AND person.LastName = 'Coleman'
   ```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 여러 가지 기본적인 데이터베이스 작업에 대해 알아보았습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 데이터베이스 만들기
> * 방화벽 규칙 설정
> * SQL Server Management Studio[(SSMS)](https://msdn.microsoft.com/library/ms174173.aspx)를 사용하여 데이터베이스에 연결
> * 테이블 만들기
> * 데이터 대량 로드
> * 해당 데이터 쿼리

Visual Studio 및 C#을 사용하여 데이터베이스를 설계하는 방법에 대한 자세한 내용을 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [Azure SQL Database 설계 및 C#과 ADO.NET에 연결T](sql-database-design-first-database-csharp.md)
