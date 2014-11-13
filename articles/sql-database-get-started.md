<properties urlDisplayName="How to create and provision" pageTitle="Azure SQL 데이터베이스 시작 - Azure" metaKeywords="" description="Azure에서 SQL 데이터베이스 만들기와 관리를 시작하는 방법에 대해 알아봅니다." metaCanonical="" services="sql-database" documentationCenter="" title="Azure SQL 데이터베이스 시작" authors="loclar"  solutions="" writer="" manager="jeffreyg" editor="tysonn"  />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="loclar" />

# Microsoft Azure SQL 데이터베이스 시작

이 자습서에서는 Azure 관리 포털을 사용하여 Microsoft Azure SQL 데이터베이스 관리의 기본 내용에 대해 알아봅니다. 처음으로 데이터베이스 관리를 접하는 경우 다음 단원의 내용을 따라 대략 30분 만에 핵심적인 기술을 배워볼 수 있습니다.

이 자습서에서는 사용자가 이전에 SQL Server 또는 Azure SQL 데이터베이스를 다루어 본 적이 없다고 가정합니다. 이 자습서를 완료하면 Azure에 샘플 데이터베이스를 만들고 관리 포털을 사용하여 기본적인 관리 작업을 수행하는 방법을 이해할 수 있게 됩니다.

Excel을 사용하여 Azure 플랫폼의 샘플 데이터베이스와 쿼리 시스템 및 사용자 데이터를 만들고 프로비전하게 됩니다.

## 목차

-   [1단계: Microsoft Azure 계정 만들기][1단계: Microsoft Azure 계정 만들기]
-   [2단계: Azure에 연결 및 데이터베이스 만들기][1단계: Microsoft Azure 계정 만들기]
-   [3단계: 방화벽 구성][3단계: 방화벽 구성]
-   [4단계: Transact-SQL 스크립트를 사용하여 데이터 및 스키마 추가][4단계: Transact-SQL 스크립트를 사용하여 데이터 및 스키마 추가]
-   [5단계: 스키마 만들기][5단계: 스키마 만들기]
-   [6단계: 데이터 삽입][6단계: 데이터 삽입]
-   [7단계: 관리 포털의 SQL 데이터베이스용 쿼리 샘플 및 시스템 데이터][7단계: 관리 포털의 SQL 데이터베이스용 쿼리 샘플 및 시스템 데이터]
-   [8단계: 데이터베이스 로그인 만들기 및 권한 할당][8단계: 데이터베이스 로그인 만들기 및 권한 할당]
-   [9단계: 다른 응용 프로그램에서 연결][9단계: 다른 응용 프로그램에서 연결]

## 1단계: Microsoft Azure 계정 만들기

1.  웹 브라우저를 열고 <http://azure.microsoft.com>으로 이동합니다.
    무료 계정으로 시작하려면 오른쪽 위에 있는 무료 체험을 클릭하고 단계를 따르세요.

2.  이제 계정이 만들어집니다. 시작할 준비가 되었습니다.

## 2단계: Azure에 연결 및 데이터베이스 만들기

1.  [관리 포털][관리 포털]에 로그인합니다. 다음과 같은 탐색 창이 표시됩니다.

    ![탐색 창][탐색 창]

2.  페이지 아래쪽에서 **새로 만들기**를 클릭합니다. **새로 만들기**를 클릭하면 사용자가 만들 수 있는 항목의 목록이 화면 위로 표시됩니다.

3.  **SQL 데이터베이스**를 클릭한 후 **사용자 지정 만들기**를 클릭합니다.

    ![탐색 창][1]

이 옵션을 선택하면 관리자 권한으로 새 서버 및 SQL 데이터베이스를 동시에 만들 수 있습니다. 시스템 관리자는 SQL 데이터베이스용 관리 포털에 연결하는 등 더 많은 작업을 수행할 수 있습니다. 관리 포털에 연결하는 작업은 자습서의 뒷부분에서 수행합니다.

1.  **사용자 지정 만들기**를 클릭하면 데이터베이스 설정 페이지가 나타납니다. 이 페이지에서 서버에 빈 SQL 데이터베이스를 만드는 기본 정보를 제공합니다. 테이블 및 데이터 추가는 뒷부분의 단계에서 수행합니다.

    데이터베이스 설정 페이지를 다음과 같이 작성합니다.

    ![탐색 창][2]

-   데이터베이스 이름으로 **School**을 입력합니다.

-   버전, 최대 크기 및 데이터 정렬에는 기본 설정을 사용합니다.

-   **새 SQL 데이터베이스 서버**를 선택합니다. 새 서버를 선택하면 관리자 계정 및 지역을 설정할 수 있는 두 번째 페이지가 추가됩니다.

-   모든 항목을 작성한 경우 화살표를 클릭하여 다음 페이지로 이동합니다.

1.  서버 설정 페이지를 다음과 같이 작성합니다.

    ![탐색 창][3]

-   관리자 이름을 공백 없이 한 단어로 입력합니다. SQL 데이터베이스는 암호화된 연결을 통해 SQL 인증을 사용하여 사용자 ID를 확인합니다. 입력한 이름을 사용하여 관리자 권한이 있는 새 SQL Server 인증 로그인이 만들어집니다. 관리자 이름은 Windows 사용자가 될 수 없고, Live ID 사용자 이름이어서도 안 됩니다. Windows 인증은 SQL 데이터베이스에서 지원되지 않습니다.

-   대문자 및 소문자 값과 숫자 또는 기호를 조합하여 8자 이상의 강력한 암호를 입력합니다. 암호 복잡성에 대한 자세한 내용을 보려면 도움말 풍선을 사용합니다.

-   지역을 선택합니다. 지역은 서버의 지리적 위치를 결정합니다. 지역은 쉽게 바꿀 수 없으므로 이 서버에 맞는 지역을 선택하세요. 사용자에게 가장 가까운 위치를 선택합니다. Azure 응용 프로그램 및 데이터베이스를 동일한 지역에 유지하면 발신용 대역폭 비용과 데이터 대기 시간이 절약됩니다.

-   SQL 데이터베이스용 관리 포털, Office 365의 Excel 또는 Azure SQL 보고를 사용하여 이 데이터베이스에 연결할 수 있도록 **Azure 서비스가 서버에 액세스할 수 있도록 허용합니다.** 확인란을 선택한 상태를 유지해야 합니다.

-   완료하면 페이지 맨 아래에 있는 확인 표시를 클릭합니다.

서버 이름을 지정하지 않았다는 점에 유의하세요. 전 세계에서 SQL 데이터베이스 서버에 액세스할 수 있어야 하므로, SQL 데이터베이스는 서버가 만들어질 때 적절한 DNS 항목을 구성합니다. 이 이름은 다른 DNS 항목과의 이름 충돌이 발생하지 않도록 생성됩니다. SQL 데이터베이스 서버의 이름은 변경할 수 없습니다.

방금 만든 **School** 데이터베이스를 호스트하는 서버의 이름을 보려면 왼쪽 탐색 창에서 **SQL 데이터베이스**를 클릭한 후에 **SQL 데이터베이스** 목록 보기에서 **School** 데이터베이스를 클릭합니다. **빠른 시작** 페이지에서 아래로 스크롤하여 서버 이름을 봅니다.

다음 단계에서는, SQL 데이터베이스 서버의 데이터베이스에 액세스할 수 있게 컴퓨터에서 실행 중인 응용 프로그램의 연결이 허용되도록 방화벽을 구성합니다.

## 3단계: 방화벽 구성

연결이 허용되도록 방화벽을 구성하려면 서버 페이지에 정보를 입력합니다.

**참고:** SQL 데이터베이스 서비스는 TDS 프로토콜에 사용되는 TCP 포트 1433에서만 사용 가능하므로 네트워크 및 로컬 컴퓨터의 방화벽에서 포트 1433의 나가는 TCP 통신을 허용하는지 확인하세요. 자세한 내용은 [SQL 데이터베이스 방화벽][SQL 데이터베이스 방화벽]을 참조하세요.

1.  왼쪽의 탐색 창에서 **SQL 데이터베이스**를 클릭합니다.

2.  페이지 위쪽에서 **서버**를 클릭합니다. 그런 다음, 방금 만든 서버를 클릭하여 서버 페이지를 엽니다.

3.  서버 페이지에서 **구성**을 클릭하여 **허용된 IP 주소** 설정을 연 후에 **허용된 IP 주소에 추가** 링크를 클릭합니다. 그러면 장치가 수신 대기 중인 라우터 또는 프록시 서버에서 연결 요청을 허용하는 새로운 방화벽 규칙이 만들어집니다.

4.  규칙 이름을 지정하고 시작 및 끝 IP 범위 값을 지정함으로써 추가적인 방화벽 규칙을 만듭니다.

5.  이 서버와 다른 Azure 서비스 간에 상호 작용을 활성화하려면 **Microsoft Azure 서비스**에 대해 **예**를 클릭합니다.

6.  변경 내용을 저장하려면 페이지 아래쪽에서 **저장**을 클릭합니다.

7.  규칙을 저장하면 다음 스크린샷과 유사한 페이지가 나타납니다.

    ![탐색 창][4]

이제 Azure의 SQL 데이터베이스 서버, 서버에 대한 액세스를 허용하는 방화벽 규칙, 데이터베이스 개체 및 관리자 로그인을 만들었습니다. 하지만 쿼리할 수 있는 작업 데이터베이스는 아직 없습니다. 쿼리가 가능하기 위해서는 데이터베이스에 스키마와 실제 데이터가 있어야 합니다.

이 자습서에서는 주어진 도구만 사용하기 때문에 SQL 데이터베이스용 관리 포털의 쿼리 창에서 Transact-SQL 스크립트를 실행하여 미리 정의된 데이터베이스를 만들어 보겠습니다.

관리 포털 사용 기술이 늘어나면서 SQL Server Data Tools의 디자인 표면 또는 프로그래밍 접근 방법을 포함하여 데이터베이스를 만들 수 있는 다른 방법도 알아볼 수 있습니다. 로컬 서버에서 실행되는 기존 SQL Server 데이터베이스가 이미 있는 경우 방금 설정한 Azure 서버에 이 데이터베이스를 쉽게 마이그레이션할 수 있습니다. 이 자습서의 끝 부분에 나오는 링크에서 마이그레이션 방법을 알아볼 수 있습니다.

## 4단계: Transact-SQL 스크립트를 사용하여 데이터 및 스키마 추가

이 단계에서는 두 개의 스크립트를 실행합니다. 첫 번째 스크립트는 테이블, 열 및 관계를 정의하는 스키마를 만듭니다. 두 번째 스크립트는 데이터를 추가합니다. 각 단계는 개별 연결과 상관없이 수행됩니다. SQL Server에서 이전에 데이터베이스를 만들어 본 적이 있는 경우 SQL 데이터베이스의 유일한 차이점 중 하나는 CREATE 및 INSERT 명령을 개별 배치로 실행해야 한다는 것입니다. SQL 데이터베이스는 전송 중인 데이터에 대한 공격을 최소화하기 위해 이 요구 사항을 설정합니다.

**참고:** 스키마 및 데이터 값은 이 [MSDN 문서][MSDN 문서]에서 가져온 것이며, SQL 데이터베이스에 사용하기 위해 수정했습니다.

1.  홈 페이지로 이동합니다. [관리 포털][관리 포털]에서, **School** 데이터베이스가 홈 페이지의 항목 목록에 나타납니다.

    ![탐색 창][5]

2.  **School**을 클릭하여 선택한 후에 페이지 맨 아래에 있는 **관리**를 클릭합니다. 그러면 SQL 데이터베이스용 관리 포털이 열립니다. 이 포털은 Azure 관리 포털과 별개입니다. 이 포털에서 Transact-SQL 명령 및 쿼리를 실행합니다.

3.  **School** 데이터베이스에 로그인하기 위한 관리자 로그인 이름 및 암호를 입력합니다. 이는 서버를 만들 때 지정한 관리자 로그인입니다.

4.  SQL 데이터베이스의 관리 포털에서 리본에 있는 **새 쿼리**를 클릭합니다. 빈 쿼리 창이 작업 영역에서 열립니다. 다음 단계에서는, 이 창에서 빈 데이터베이스에 구조체 및 데이터를 추가하는 미리 정의된 일련의 스크립트를 복사해 넣습니다.

## 5단계: 스키마 만들기

이 단계에서 다음 스크립트를 사용하여 스키마를 만듭니다. 이 스크립트는 먼저 동일한 이름의 기존 테이블을 검사하여 이름 충돌이 발생하지 않는지 확인하고, [CREATE TABLE][CREATE TABLE] 문을 사용하여 테이블을 만듭니다. 더 나아가, 이 스크립트는 [ALTER TABLE][ALTER TABLE] 문을 사용하여 기본 키 및 테이블 관계를 지정합니다.

다음 스크립트를 복사하여 쿼리 창에 붙여넣습니다. 창의 위쪽에서 **실행**을 클릭하여 스크립트를 실행합니다.

<div style="width:auto; height:600px; overflow:auto"><pre>
    -- Create the Department table.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[Department]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[Department](
        [DepartmentID] [int] NOT NULL,
        [Name] [nvarchar](50) NOT NULL,
        [Budget] [money] NOT NULL,
        [StartDate] [datetime] NOT NULL,
        [Administrator] [int] NULL,
     CONSTRAINT [PK_Department] PRIMARY KEY CLUSTERED 
    (
    [DepartmentID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    )
    END;
    GO

    -- Create the Person table.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[Person]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[Person](
        [PersonID] [int] IDENTITY(1,1) NOT NULL,
        [LastName] [nvarchar](50) NOT NULL,
        [FirstName] [nvarchar](50) NOT NULL,
        [HireDate] [datetime] NULL,
        [EnrollmentDate] [datetime] NULL,
     CONSTRAINT [PK_School.Student] PRIMARY KEY CLUSTERED   
    (
    [PersonID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    ) 
    END;
    GO

    -- Create the OnsiteCourse table.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[OnsiteCourse]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[OnsiteCourse](
        [CourseID] [int] NOT NULL,
        [Location] [nvarchar](50) NOT NULL,
        [Days] [nvarchar](50) NOT NULL,
        [Time] [smalldatetime] NOT NULL,
     CONSTRAINT [PK_OnsiteCourse] PRIMARY KEY CLUSTERED 
    (
        [CourseID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    ) 
    END;
    GO

    -- Create the OnlineCourse table.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[OnlineCourse]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[OnlineCourse](
        [CourseID] [int] NOT NULL,
        [URL] [nvarchar](100) NOT NULL,
     CONSTRAINT [PK_OnlineCourse] PRIMARY KEY CLUSTERED 
    (
        [CourseID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    ) 
    END;
    GO

    --Create the StudentGrade table.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[StudentGrade]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[StudentGrade](
        [EnrollmentID] [int] IDENTITY(1,1) NOT NULL,
        [CourseID] [int] NOT NULL,
        [StudentID] [int] NOT NULL,
        [Grade] [decimal](3, 2) NULL,
     CONSTRAINT [PK_StudentGrade] PRIMARY KEY CLUSTERED 
    (
        [EnrollmentID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    ) 
    END;
    GO

    -- Create the CourseInstructor table.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[CourseInstructor]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[CourseInstructor](
        [CourseID] [int] NOT NULL,
        [PersonID] [int] NOT NULL,
     CONSTRAINT [PK_CourseInstructor] PRIMARY KEY CLUSTERED 
    (
        [CourseID] ASC,
        [PersonID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    ) 
    END;
    GO

    -- Create the Course table.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[Course]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[Course](
        [CourseID] [int] NOT NULL,
        [Title] [nvarchar](100) NOT NULL,
        [Credits] [int] NOT NULL,
        [DepartmentID] [int] NOT NULL,
     CONSTRAINT [PK_School.Course] PRIMARY KEY CLUSTERED 
    (
        [CourseID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    )
    END;
    GO

    -- Create the OfficeAssignment table.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[OfficeAssignment]')
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[OfficeAssignment](
        [InstructorID] [int] NOT NULL,
        [Location] [nvarchar](50) NOT NULL,
        [Timestamp] [timestamp] NOT NULL,
     CONSTRAINT [PK_OfficeAssignment] PRIMARY KEY CLUSTERED 
    (
        [InstructorID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    )
    END;
    GO

    -- Define the relationship between OnsiteCourse and Course.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_OnsiteCourse_Course]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[OnsiteCourse]'))
    ALTER TABLE [dbo].[OnsiteCourse]  WITH CHECK ADD  
       CONSTRAINT [FK_OnsiteCourse_Course] FOREIGN KEY([CourseID])
    REFERENCES [dbo].[Course] ([CourseID]);
    GO
    ALTER TABLE [dbo].[OnsiteCourse] CHECK 
       CONSTRAINT [FK_OnsiteCourse_Course];
    GO

    -- Define the relationship between OnlineCourse and Course.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_OnlineCourse_Course]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[OnlineCourse]'))
    ALTER TABLE [dbo].[OnlineCourse]  WITH CHECK ADD  
       CONSTRAINT [FK_OnlineCourse_Course] FOREIGN KEY([CourseID])
    REFERENCES [dbo].[Course] ([CourseID]);
    GO
    ALTER TABLE [dbo].[OnlineCourse] CHECK 
       CONSTRAINT [FK_OnlineCourse_Course];
    GO
    -- Define the relationship between StudentGrade and Course.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_StudentGrade_Course]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[StudentGrade]'))
    ALTER TABLE [dbo].[StudentGrade]  WITH CHECK ADD  
       CONSTRAINT [FK_StudentGrade_Course] FOREIGN KEY([CourseID])
    REFERENCES [dbo].[Course] ([CourseID]);
    GO
    ALTER TABLE [dbo].[StudentGrade] CHECK 
       CONSTRAINT [FK_StudentGrade_Course];
    GO

    --Define the relationship between StudentGrade and Student.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_StudentGrade_Student]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[StudentGrade]'))   
    ALTER TABLE [dbo].[StudentGrade]  WITH CHECK ADD  
       CONSTRAINT [FK_StudentGrade_Student] FOREIGN KEY([StudentID])
    REFERENCES [dbo].[Person] ([PersonID]);
    GO
    ALTER TABLE [dbo].[StudentGrade] CHECK 
       CONSTRAINT [FK_StudentGrade_Student];
    GO

    -- Define the relationship between CourseInstructor and Course.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
     WHERE object_id = OBJECT_ID(N'[dbo].[FK_CourseInstructor_Course]')
     AND parent_object_id = OBJECT_ID(N'[dbo].[CourseInstructor]'))
    ALTER TABLE [dbo].[CourseInstructor]  WITH CHECK ADD  
     CONSTRAINT [FK_CourseInstructor_Course] FOREIGN KEY([CourseID])
    REFERENCES [dbo].[Course] ([CourseID]);
    GO
    ALTER TABLE [dbo].[CourseInstructor] CHECK 
      CONSTRAINT [FK_CourseInstructor_Course];
    GO

    -- Define the relationship between CourseInstructor and Person.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
      WHERE object_id = OBJECT_ID(N'[dbo].[FK_CourseInstructor_Person]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[CourseInstructor]'))
    ALTER TABLE [dbo].[CourseInstructor]  WITH CHECK ADD  
      CONSTRAINT [FK_CourseInstructor_Person] FOREIGN KEY([PersonID])
    REFERENCES [dbo].[Person] ([PersonID]);
    GO
    ALTER TABLE [dbo].[CourseInstructor] CHECK 
     CONSTRAINT [FK_CourseInstructor_Person];
    GO

    -- Define the relationship between Course and Department.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_Course_Department]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[Course]'))
    ALTER TABLE [dbo].[Course]  WITH CHECK ADD  
       CONSTRAINT [FK_Course_Department] FOREIGN KEY([DepartmentID])
    REFERENCES [dbo].[Department] ([DepartmentID]);
    GO
    ALTER TABLE [dbo].[Course] CHECK CONSTRAINT [FK_Course_Department];
    GO

    --Define the relationship between OfficeAssignment and Person.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
      WHERE object_id = OBJECT_ID(N'[dbo].[FK_OfficeAssignment_Person]')
      AND parent_object_id = OBJECT_ID(N'[dbo].[OfficeAssignment]'))
    ALTER TABLE [dbo].[OfficeAssignment]  WITH CHECK ADD  
      CONSTRAINT [FK_OfficeAssignment_Person] FOREIGN KEY([InstructorID])
    REFERENCES [dbo].[Person] ([PersonID]);
    GO
    ALTER TABLE [dbo].[OfficeAssignment] CHECK 
     CONSTRAINT [FK_OfficeAssignment_Person];
    GO
</pre></div>

## 6단계: 데이터 삽입

새 쿼리 창을 연 후 다음 스크립트를 붙여넣습니다. 스크립트를 실행하여 데이터를 삽입합니다. 이 스크립트는 [INSERT][INSERT] 문을 사용하여 각 열에 값을 추가합니다.

<div style="width:auto; height:600px; overflow:auto"><pre>
    -- Insert data into the Person table.
    SET IDENTITY_INSERT dbo.Person ON;
    GO
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (1, 'Abercrombie', 'Kim', '1995-03-11', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (2, 'Barzdukas', 'Gytis', null, '2005-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (3, 'Justice', 'Peggy', null, '2001-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (4, 'Fakhouri', 'Fadi', '2002-08-06', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (5, 'Harui', 'Roger', '1998-07-01', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (6, 'Li', 'Yan', null, '2002-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (7, 'Norman', 'Laura', null, '2003-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (8, 'Olivotto', 'Nino', null, '2005-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (9, 'Tang', 'Wayne', null, '2005-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (10, 'Alonso', 'Meredith', null, '2002-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (11, 'Lopez', 'Sophia', null, '2004-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (12, 'Browning', 'Meredith', null, '2000-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (13, 'Anand', 'Arturo', null, '2003-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (14, 'Walker', 'Alexandra', null, '2000-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (15, 'Powell', 'Carson', null, '2004-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (16, 'Jai', 'Damien', null, '2001-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (17, 'Carlson', 'Robyn', null, '2005-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (18, 'Zheng', 'Roger', '2004-02-12', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (19, 'Bryant', 'Carson', null, '2001-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (20, 'Suarez', 'Robyn', null, '2004-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (21, 'Holt', 'Roger', null, '2004-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (22, 'Alexander', 'Carson', null, '2005-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (23, 'Morgan', 'Isaiah', null, '2001-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (24, 'Martin', 'Randall', null, '2005-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (25, 'Kapoor', 'Candace', '2001-01-15', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (26, 'Rogers', 'Cody', null, '2002-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (27, 'Serrano', 'Stacy', '1999-06-01', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (28, 'White', 'Anthony', null, '2001-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (29, 'Griffin', 'Rachel', null, '2004-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (30, 'Shan', 'Alicia', null, '2003-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (31, 'Stewart', 'Jasmine', '1997-10-12', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (32, 'Xu', 'Kristen', '2001-7-23', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (33, 'Gao', 'Erica', null, '2003-01-30');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (34, 'Van Houten', 'Roger', '2000-12-07', null);
    GO
    SET IDENTITY_INSERT dbo.Person OFF;
    GO
    
</pre></div>

## 7단계: 관리 포털의 SQL 데이터베이스용 쿼리 샘플 및 시스템 데이터

작업을 확인하기 위해 방금 입력한 데이터를 반환하는 쿼리를 실행할 수 있습니다. 또한 SQL 데이터베이스 서버에서 실행 중인 데이터베이스에 대한 정보를 제공하는 기본 제공 저장 프로시저 및 데이터 관리를 실행할 수도 있습니다.

#### 쿼리 샘플 데이터

새 쿼리 창에서 다음 Transact-SQL 스크립트를 복사하고 실행하여 방금 추가한 일부 데이터를 검색해 봅니다.

<div style="width:auto; height:auto; overflow:auto"><pre>
    SELECT * From Person
</pre></div>

person 테이블에서 PersonID, LastName, FirstName, HireDate, EnrollmentDate 등 34개 행이 있는 결과 집합이 표시되어야 합니다.

#### 쿼리 시스템 데이터

또한 시스템 보기 및 기본 제공 저장 프로시저를 사용하여 서버에서 정보를 가져올 수도 있습니다. 이 자습서에서는 몇 가지 명령을 시도해 보겠습니다.

다음 명령을 실행하여 서버에서 사용 가능한 데이터베이스를 확인합니다.

    SELECT * FROM sys.databases  

이 명령을 실행하여 현재 서버에 연결된 사용자 목록을 반환합니다.

    SELECT user_name(),suser_sname()

이 저장 프로시저를 실행하여 **School** 데이터베이스의 모든 개체 목록을 반환합니다.

    EXEC SP_help

**School** 데이터베이스에 대한 포털 연결을 닫지 마세요. 잠시 뒤에 다시 포털 연결이 필요합니다.

## 8단계: 데이터베이스 로그인 만들기 및 권한 할당

SQL 데이터베이스에서 로그인을 만들고 Transact-SQL을 사용하여 권한을 부여할 수 있습니다. 이 단원에서는 Transact-SQL을 사용하여 세 가지 작업 즉,

1.  SQL Server 인증 로그인 만들기
2.  데이터베이스 사용자 만들기
3.  역할 구성원을 통한 권한 부여

SQL Server 인증 로그인은 서버 연결에 사용됩니다. SQL 데이터베이스 서버의 데이터베이스에 액세스하는 모든 사용자는 SQL Server 인증 로그인 이름 및 암호를 제공하여 연결합니다.

로그인을 만들려면 먼저 **master** 데이터베이스에 연결해야 합니다.

#### SQL Server 인증 로그인 만들기

1.  [관리 포털][관리 포털]에서, **SQL 데이터베이스**를 선택하고, **서버**를 클릭하고, 서버를 선택한 후 흰색 화살표를 클릭하여
    서버 페이지를 엽니다.

2.  빠른 시작 페이지에서 **서버 관리**를 클릭하여 SQL 데이터베이스용 관리 포털에 대한 새 연결을 엽니다.

3.  연결할 데이터베이스에 대해 **master**를 지정한 후, 사용자 이름 및 암호를 사용하여 로그인합니다. 이는 서버를 만들 때 지정한 관리자 로그인입니다.

4.  SQL 데이터베이스 관리 포털이 새 브라우저 창에서 열리고 **master**에 연결됩니다.

5.  페이지에 다음과 유사한 오류가 나타날 경우 무시합니다. **새 쿼리**를 클릭하여 **master** 데이터베이스에서 Transact-SQL 명령을 실행할 수 있는 쿼리 창을 엽니다.

    ![탐색 창][6]

6.  다음 명령을 복사하여 쿼리 창에 붙여넣습니다.

        CREATE LOGIN SQLDBLogin WITH password='Password1';

7.  이 명령을 실행하여 'SQLDBLogin'이라는 새 SQL Server 로그인을 만듭니다.

#### 데이터베이스 로그인 만들기 및 권한 할당

SQL Server 인증 로그인을 만든 후 다음 단계는 로그인과 연결된 데이터베이스 및 권한 수준을 할당하는 것입니다. 각 데이터베이스에서 **데이터베이스 사용자**를 만들어 그렇게 할 수 있습니다.

1.  **School** 데이터베이스에 연결하는 SQL 데이터베이스 관리 포털 페이지로 다시 돌아갑니다. 브라우저 창을 닫으면 앞부분의 단원인 "Transact-SQL 스크립트를 사용하여 데이터 및 스키마 추가"의 단계에 따라 **School** 데이터베이스에 대한 새 연결을 시작합니다.

    SQL 데이터베이스 관리 포털 페이지에서 **School** 데이터베이스 이름이 맨 위 왼쪽 모서리에 표시됩니다.

    ![탐색 창][7]

2.  **새 쿼리**를 클릭하여 새 쿼리 창을 열고 다음 문을 복사해 넣습니다.

        CREATE USER SQLDBUser FROM LOGIN SQLDBLogin;

3.  스크립트를 실행합니다. 이 스크립트는 로그인을 기반으로 하여 새 데이터베이스 사용자를 만듭니다.

이제 db\_datareader 역할을 사용하여 권한을 할당합니다. 이 역할에 할당된 데이터베이스 사용자는 데이터베이스의 모든 사용자 테이블에서 모든 데이터를 읽을 수 있습니다.

1.  새 쿼리 창을 연 후 다음 문을 입력하여 실행합니다. 이 문은 방금 만든 새 사용자에 db\_datareader 역할을 할당하는 기본 제공 저장 프로시저를 실행합니다.

        EXEC sp_addrolemember 'db_datareader', 'SQLDBUser';

이제 **School** 데이터베이스에 대한 읽기 전용 권한이 있는 새 SQL Server 인증 로그인이 만들어졌습니다. 이러한 단계에 따라 SQL Server 인증 로그인을 만들어 데이터에 대한 다른 수준의 액세스를 허용할 수 있습니다.

## 9단계: 다른 응용 프로그램에서 연결

운영 데이터베이스를 만들었으므로, Excel 통합 문서에서 이 데이터베이스에 연결할 수 있습니다.

#### Excel에서 연결

Microsoft Excel이 컴퓨터에 설치되어 있는 경우 다음 단계에 따라 샘플 데이터베이스에 연결할 수 있습니다.

1.  Excel의 데이터 탭에서 **기타 원본**을 클릭한 후 **SQL Server**를 클릭합니다.

2.  데이터 연결 마법사에서 SQL 데이터베이스 서버의 정규화된 도메인 이름과 데이터베이스에 대한 액세스 권한이 있는 SQL Server 인증 로그인을 차례로 입력합니다.

서버 이름은 Azure 관리 포털, SQL 데이터베이스, 서버 페이지, 대시보드의 **URL 관리**에서 확인할 수 있습니다. 서버 이름은 일련의 문자 및 숫자와 그 뒤에 오는 '.database.windows.net'으로 구성됩니다. 데이터베이스 연결 마법사에서 이 이름을 지정합니다. 이름을 지정할 때 http:// 또는 https:// 접두사를 포함하지 마세요.

SQL Server 인증 로그인을 입력합니다. 테스트를 위해서는 서버를 설정할 때 만든 관리자 로그인을 사용할 수 있습니다. 일반적인 데이터 액세스를 위해서는 방금 만든 것과 유사한 데이터베이스 사용자 로그인을 사용하세요.

![탐색 창][8]

1.  다음 페이지에서, **School** 데이터베이스를 선택한 후 **Person**을 선택합니다. **마침**을 클릭합니다. 로그인 정보를 요구하면 로그인 정보를 입력하고 **확인**을 클릭합니다.

2.  데이터 가져오기 대화 상자가 나타나 데이터를 가져오는 방법과 위치를 묻습니다. 기본 옵션을 선택하고 **확인**을 클릭합니다.

    ![탐색 창][9]

3.  워크시트에서 7단계의 쿼리 결과와 마찬가지로 PersonID, LastName, FirstName, HireDate, EnrollmentDate 등 person 테이블의 34개 행을 포함하고 있는 결과 집합이 표로 표시되어야 합니다.

Excel을 사용할 경우 테이블을 한 번에 하나만 가져올 수 있습니다. PowerPivot for Excel 추가 기능을 사용하는 것이 더 좋습니다. 이 추가 기능을 사용하면 여러 테이블을 단일 데이터 집합으로 가져와서 작업할 수 있습니다. PowerPivot 사용에 관한 자세한 내용은 본 자습서에서는 다루지 않지만 이 항목에서 [PowerPivot for Excel][PowerPivot for Excel]에 대한 추가 정보를 얻을 수 있습니다.

## 다음 단계

SQL 데이터베이스 및 관리 포털에 익숙해졌으므로, SQL Server 데이터베이스 관리자가 사용하는 기타 도구 및 기술을 시도해 볼 수 있습니다.

새 데이터베이스를 적극적으로 관리하기 위해 SQL Server Management Studio를 설치하여 사용해 볼 수 있습니다. Management Studio는 Azure에서 실행되는 데이터베이스를 포함하여 SQL Server 데이터베이스를 관리할 수 있는 주 데이터베이스 관리 도구입니다. Management Studio를 사용하여 쿼리를 나중에 사용하도록 저장하고, 새 테이블 및 저장 프로시저를 추가하고, 구문 검사기, IntelliSense 및 템플릿을 포함하는 다양한 스크립팅 환경에서 Transact-SQL 기술을 익힐 수 있습니다. 시작하려면 [SQL Server Management Studio를 사용하여 SQL 데이터베이스 관리][SQL Server Management Studio를 사용하여 SQL 데이터베이스 관리](영문)의 지침을 따르세요.

데이터베이스 관리자는 Transact-SQL 쿼리 및 데이터 정의 언어에 능숙해야 합니다. Transact-SQL을 처음 사용한다면 [자습서: Transact-SQL 문 쓰기][자습서: Transact-SQL 문 쓰기]를 시작하여 기본 기술을 더 익혀 보세요.

다른 방법으로도 SQL 데이터베이스로 온-프레미스 데이터베이스를 이동할 수 있습니다. 기존 데이터베이스가 있거나 샘플 데이터베이스를 다운로드하여 연습하는 경우 다음과 같은 다른 방법을 시도해 보세요.

-   [SQL 데이터베이스로 데이터베이스 마이그레이션][SQL 데이터베이스로 데이터베이스 마이그레이션]
-   [SQL 데이터베이스에서 데이터베이스 복사][SQL 데이터베이스에서 데이터베이스 복사]
-   [Azure 가상 컴퓨터에 SQL Server 데이터베이스 배포][Azure 가상 컴퓨터에 SQL Server 데이터베이스 배포]

  [1단계: Microsoft Azure 계정 만들기]: #Subscribe
  [3단계: 방화벽 구성]: #ConfigFirewall
  [4단계: Transact-SQL 스크립트를 사용하여 데이터 및 스키마 추가]: #AddData
  [5단계: 스키마 만들기]: #createschema
  [6단계: 데이터 삽입]: #insertData
  [7단계: 관리 포털의 SQL 데이터베이스용 쿼리 샘플 및 시스템 데이터]: #QueryDBSysData
  [8단계: 데이터베이스 로그인 만들기 및 권한 할당]: #DBLogin
  [9단계: 다른 응용 프로그램에서 연결]: #ClientConnection
  [관리 포털]: http://manage.windowsazure.com
  [탐색 창]: ./media/sql-database-get-started/1NavPaneDBSelected_SQLTut.png
  [1]: ./media/sql-database-get-started/2MainPageCustomCreateDB_SQLTut.png
  [2]: ./media/sql-database-get-started/3DatabaseSettings_SQLTut.PNG
  [3]: ./media/sql-database-get-started/4ServerSettings_SQLTut.PNG
  [SQL 데이터베이스 방화벽]: http://social.technet.microsoft.com/wiki/contents/articles/2677.sql-azure-firewall-ko-kr.aspx
  [4]: ./media/sql-database-get-started/7DBConfigFirewallSAVE_SQLTut.png
  [MSDN 문서]: http://msdn.microsoft.com/ko-kr/library/windowsazure/ee621790.aspx "MSDN 문서"
  [5]: ./media/sql-database-get-started/20MainPageHome_SQLTut.PNG
  [CREATE TABLE]: http://msdn.microsoft.com/ko-kr/library/windowsazure/ee336258.aspx
  [ALTER TABLE]: http://msdn.microsoft.com/ko-kr/library/windowsazure/ee336286.aspx
  [INSERT]: http://msdn.microsoft.com/ko-kr/library/windowsazure/ee336284.aspx
  [6]: ./media/sql-database-get-started/15DBPortalConnectMasterErr_SQLTut.PNG
  [7]: ./media/sql-database-get-started/12DBPortalNewQuery_SQLTut.PNG
  [8]: ./media/sql-database-get-started/16ExcelConnect_SQLTut.png
  [9]: ./media/sql-database-get-started/19ExcelImport_SQLTut.png
  [PowerPivot for Excel]: http://go.microsoft.com/fwlink/?LinkId=396969
  [SQL Server Management Studio를 사용하여 SQL 데이터베이스 관리]: http://www.azure.microsoft.com/ko-kr/documentation/articles/sql-database-manage-azure-ssms/
  [자습서: Transact-SQL 문 쓰기]: http://msdn.microsoft.com/ko-kr/library/ms365303.aspx
  [SQL 데이터베이스로 데이터베이스 마이그레이션]: http://msdn.microsoft.com/ko-kr/library/windowsazure/ee730904.aspx
  [SQL 데이터베이스에서 데이터베이스 복사]: http://msdn.microsoft.com/ko-kr/library/windowsazure/ff951624.aspx
  [Azure 가상 컴퓨터에 SQL Server 데이터베이스 배포]: http://msdn.microsoft.com/ko-kr/library/dn195938(v=sql.120).aspx
