---
title: Go를 사용하여 Azure SQL Database 쿼리 | Microsoft Docs
description: Go를 사용하여 Azure SQL Database에 연결하고 Transact-SQL 문을 사용하여 데이터를 쿼리하고 수정합니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: go
ms.topic: quickstart
author: David-Engel
ms.author: v-daveng
ms.reviewer: MightyPen
manager: craigg
ms.date: 12/07/2018
ms.openlocfilehash: 34b3ee54c48040eaa6f7b7569921678869baa84b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53092369"
---
# <a name="quickstart-use-go-to-query-an-azure-sql-database"></a>빠른 시작: Go를 사용하여 Azure SQL Database 쿼리

이 빠른 시작에서는 [Go](https://godoc.org/github.com/denisenkom/go-mssqldb) 프로그래밍 언어를 사용하여 Azure SQL 데이터베이스에 연결하고, Transact-SQL 문을 실행하여 데이터를 쿼리 및 수정하는 방법을 보여 줍니다. [Go](https://golang.org/)는 간단하고, 신뢰할 수 있으며, 효율적인 소프트웨어를 쉽게 빌드할 수 있는 오픈 소스 프로그래밍 언어입니다.  

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- 컴퓨터의 공용 IP 주소에 대해 구성된 [서버 수준 방화벽 규칙](sql-database-get-started-portal-firewall.md)입니다.

- 운영 체제에 맞게 설치된 Go 및 관련 소프트웨어:

    - **MacOS**: Homebrew 및 GoLang을 설치합니다. [1.2단계](https://www.microsoft.com/sql-server/developer-get-started/go/mac/) 참조
    - **Ubuntu**:  GoLang을 설치합니다. [1.2단계](https://www.microsoft.com/sql-server/developer-get-started/go/ubuntu/) 참조
    - **Windows**: GoLang을 설치합니다. [1.2단계](https://www.microsoft.com/sql-server/developer-get-started/go/windows/) 참조    

## <a name="sql-server-connection-information"></a>SQL 서버 연결 정보

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-go-project-and-dependencies"></a>Go 프로젝트 및 종속성 만들기

1. 터미널에서 이름이 **SqlServerSample**인 새 프로젝트 폴더를 만듭니다. 

   ```bash
   mkdir SqlServerSample
   ```

2. 디렉터리를 **SqlServerSample**로 변경하고 Go용 SQL Server 드라이버를 설치합니다.

   ```bash
   cd SqlServerSample
   go get github.com/denisenkom/go-mssqldb
   go install github.com/denisenkom/go-mssqldb
   ```

## <a name="create-sample-data"></a>샘플 데이터 만들기

1. 원하는 텍스트 편집기에서 이름이 **CreateTestData.sql**인 파일을 **SqlServerSample** 폴더에 만듭니다. 파일에서 스키마, 테이블을 만들고 몇몇 행을 삽입하는 다음 T-SQL 코드를 복사하여 붙여넣습니다.

   ```sql
   CREATE SCHEMA TestSchema;
   GO

   CREATE TABLE TestSchema.Employees (
     Id       INT IDENTITY(1,1) NOT NULL PRIMARY KEY,
     Name     NVARCHAR(50),
     Location NVARCHAR(50)
   );
   GO

   INSERT INTO TestSchema.Employees (Name, Location) VALUES
     (N'Jared',  N'Australia'),
     (N'Nikita', N'India'),
     (N'Tom',    N'Germany');
   GO

   SELECT * FROM TestSchema.Employees;
   GO
   ```

2. `sqlcmd`를 사용하여 데이터베이스에 연결하고 새로 만든 SQL 스크립트를 실행합니다. 서버, 데이터베이스, 사용자 이름 및 암호를 적절한 값으로 바꿉니다.

   ```bash
   sqlcmd -S your_server.database.windows.net -U your_username -P your_password -d your_database -i ./CreateTestData.sql
   ```

## <a name="insert-code-to-query-sql-database"></a>SQL 데이터베이스 쿼리 코드 삽입

1. **SqlServerSample** 폴더에 이름이 **sample.go**인 파일을 만듭니다.

2. 파일을 열고 다음 코드를 붙여넣습니다. 서버, 데이터베이스, 사용자 이름 및 암호에 적합한 값을 추가합니다. 이 예제에서는 GoLang Context 메서드를 사용하여 데이터베이스 서버에 연결된 상태인지 확인합니다.

   ```go
   package main

   import (
       _ "github.com/denisenkom/go-mssqldb"
       "database/sql"
       "context"
       "log"
       "fmt"
       "errors"
   )

   var db *sql.DB

   var server = "your_server.database.windows.net"
   var port = 1433
   var user = "your_username"
   var password = "your_password"
   var database = "your_database"

   func main() {
       // Build connection string
       connString := fmt.Sprintf("server=%s;user id=%s;password=%s;port=%d;database=%s;",
           server, user, password, port, database)

       var err error

       // Create connection pool
       db, err = sql.Open("sqlserver", connString)
       if err != nil {
           log.Fatal("Error creating connection pool: ", err.Error())
       }
       ctx := context.Background()
       err = db.PingContext(ctx)
       if err != nil {
           log.Fatal(err.Error())
       }
       fmt.Printf("Connected!\n")

       // Create employee
       createID, err := CreateEmployee("Jake", "United States")
       if err != nil {
           log.Fatal("Error creating Employee: ", err.Error())
       }
       fmt.Printf("Inserted ID: %d successfully.\n", createID)

       // Read employees
       count, err := ReadEmployees()
       if err != nil {
           log.Fatal("Error reading Employees: ", err.Error())
       }
       fmt.Printf("Read %d row(s) successfully.\n", count)

       // Update from database
       updatedRows, err := UpdateEmployee("Jake", "Poland")
       if err != nil {
           log.Fatal("Error updating Employee: ", err.Error())
       }
       fmt.Printf("Updated %d row(s) successfully.\n", updatedRows)

       // Delete from database
       deletedRows, err := DeleteEmployee("Jake")
       if err != nil {
           log.Fatal("Error deleting Employee: ", err.Error())
       }
       fmt.Printf("Deleted %d row(s) successfully.\n", deletedRows)
   }

   // CreateEmployee inserts an employee record
   func CreateEmployee(name string, location string) (int64, error) {
       ctx := context.Background()
       var err error

       if db == nil {
           err = errors.New("CreateEmployee: db is null")
           return -1, err
       }

       // Check if database is alive.
       err = db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := "INSERT INTO TestSchema.Employees (Name, Location) VALUES (@Name, @Location); select convert(bigint, SCOPE_IDENTITY());"

       stmt, err := db.Prepare(tsql)
       if err != nil {
          return -1, err
       }
       defer stmt.Close()

       row := stmt.QueryRowContext(
           ctx,
           sql.Named("Name", name),
           sql.Named("Location", location))
       var newID int64
       err = row.Scan(&newID)
       if err != nil {
           return -1, err
       }

       return newID, nil
   }

   // ReadEmployees reads all employee records
   func ReadEmployees() (int, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := fmt.Sprintf("SELECT Id, Name, Location FROM TestSchema.Employees;")

       // Execute query
       rows, err := db.QueryContext(ctx, tsql)
       if err != nil {
           return -1, err
       }

       defer rows.Close()

       var count int

       // Iterate through the result set.
       for rows.Next() {
           var name, location string
           var id int

           // Get values from row.
           err := rows.Scan(&id, &name, &location)
           if err != nil {
               return -1, err
           }

           fmt.Printf("ID: %d, Name: %s, Location: %s\n", id, name, location)
           count++
       }

       return count, nil
   }

   // UpdateEmployee updates an employee's information
   func UpdateEmployee(name string, location string) (int64, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := fmt.Sprintf("UPDATE TestSchema.Employees SET Location = @Location WHERE Name = @Name")

       // Execute non-query with named parameters
       result, err := db.ExecContext(
           ctx,
           tsql,
           sql.Named("Location", location),
           sql.Named("Name", name))
       if err != nil {
           return -1, err
       }

       return result.RowsAffected()
   }

   // DeleteEmployee deletes an employee from the database
   func DeleteEmployee(name string) (int64, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := fmt.Sprintf("DELETE FROM TestSchema.Employees WHERE Name = @Name;")

       // Execute non-query with named parameters
       result, err := db.ExecContext(ctx, tsql, sql.Named("Name", name))
       if err != nil {
           return -1, err
       }

       return result.RowsAffected()
   }
   ```

## <a name="run-the-code"></a>코드 실행

1. 명령 프롬프트에서 다음 명령을 실행합니다.

   ```bash
   go run sample.go
   ```

2. 출력을 확인합니다.

   ```text
   Connected!
   Inserted ID: 4 successfully.
   ID: 1, Name: Jared, Location: Australia
   ID: 2, Name: Nikita, Location: India
   ID: 3, Name: Tom, Location: Germany
   ID: 4, Name: Jake, Location: United States
   Read 4 row(s) successfully.
   Updated 1 row(s) successfully.
   Deleted 1 row(s) successfully.
   ```

## <a name="next-steps"></a>다음 단계

- [첫 번째 Azure SQL Database 디자인](sql-database-design-first-database.md)
- [Microsoft SQL Server용 Go 드라이버](https://github.com/denisenkom/go-mssqldb)
- [문제 보고 또는 질문(영문)](https://github.com/denisenkom/go-mssqldb/issues)

