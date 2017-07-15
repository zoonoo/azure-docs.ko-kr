---
title: "Go 언어를 사용하여 PostgreSQL용 Azure Database에 연결 | Microsoft Docs"
description: "이 빠른 시작에서는 PostgreSQL용 Azure Database의 데이터를 연결하고 쿼리하는 데 사용할 수 있는 Go 프로그래밍 언어 샘플을 제공합니다."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.devlang: go
ms.topic: hero-article
ms.date: 06/29/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: f3e7e57993b5253a895640854672da431c00854b
ms.contentlocale: ko-kr
ms.lasthandoff: 07/04/2017

---

# PostgreSQL용 Azure Database: Go 언어를 사용하여 데이터 연결 및 쿼리
<a id="azure-database-for-postgresql-use-go-language-to-connect-and-query-data" class="xliff"></a>
이 빠른 시작에서는 [Go](https://golang.org/) 언어로 작성된 코드를 사용하여 PostgreSQL용 Azure Database에 연결하는 방법을 보여줍니다. SQL 문을 사용하여 데이터베이스의 데이터를 쿼리, 삽입, 업데이트 및 삭제하는 방법을 보여 줍니다. 이 문서에서는 개발자가 Go를 사용하여 개발하는 것에 익숙하고 PostgreSQL용 Azure Database 작업에 익숙하지 않다고 가정합니다.

## 필수 조건
<a id="prerequisites" class="xliff"></a>
이 빠른 시작에서는 다음과 같은 가이드 중 하나에서 만들어진 리소스를 시작 지점으로 사용합니다.
- [DB 만들기 - 포털](quickstart-create-server-database-portal.md)
- [DB 만들기 - Azure CLI](quickstart-create-server-database-azure-cli.md)

## Go 및 pq 설치
<a id="install-go-and-pq" class="xliff"></a>
- 해당 플랫폼과 일치하는 [설치 지침](https://golang.org/doc/install)에 따라 Go를 다운로드하고 설치합니다.
- C:\Postgresql\과 같은 프로젝트 폴더를 만듭니다. 명령줄을 사용하여 디렉터리를 프로젝트 폴더로 변경합니다(예: `cd C:\Postgres\`).
- 동일한 디렉터리에서 `go get github.com/lib/pq` 명령을 입력하여 프로젝트 폴더에 [Pure Go Postgres 드라이버(pq)](https://github.com/lib/pq)를 다운로드합니다.

## Go 코드 작성 및 실행
<a id="build-and-run-go-code" class="xliff"></a> 
- 코드를 *.go 확장명을 가진 텍스트 파일에 저장하고 `C:\postgres\read.go`와 같은 프로젝트 폴더에 저장합니다.
- 코드를 실행하려면 프로젝트 폴더 `cd postgres`로 디렉터리를 변경한 후 `go run read.go` 명령을 입력하여 응용 프로그램을 컴파일하고 실행합니다.
- 원시 응용 프로그램 `go build read.go`에 코드를 작성하려면 read.exe를 시작하여 응용 프로그램을 실행합니다.

## 연결 정보 가져오기
<a id="get-connection-information" class="xliff"></a>
PostgreSQL용 Azure Database에 연결하는 데 필요한 연결 정보를 가져옵니다. 정규화된 서버 이름 및 로그인 자격 증명이 필요합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure Portal의 왼쪽 메뉴에서 **모든 리소스**를 클릭하고 방금 만든 서버를 검색합니다(예: **mypgserver-20170401**).
3. **mypgserver-20170401**서버 이름을 클릭합니다.
4. 서버의 **개요** 페이지를 선택합니다. **서버 이름** 및 **서버 관리자 로그인 이름**을 기록해 둡니다.
 ![PostgreSQL용 Azure Database - 서버 관리자 로그인](./media/connect-go/1-connection-string.png)
5. 서버 로그인 정보를 잊어버린 경우 **개요** 페이지로 이동하여 서버 관리자 로그인 이름을 확인합니다. 필요한 경우 암호를 다시 설정합니다.

## 테이블 연결 및 생성
<a id="connect-and-create-a-table" class="xliff"></a>
**CREATE TABLE** SQL 문 다음에 테이블에 행을 추가하는 **INSERT INTO** SQL 문을 사용하여 테이블을 연결 및 생성하려면 다음 코드를 사용합니다.

이 코드는 [sql package](https://golang.org/pkg/database/sql/), Postgres 서버와 통신할 때 드라이버로 사용되는 [pq package](http://godoc.org/github.com/lib/pq), 명령줄에 인쇄된 입력 및 출력을 위한 [fmt package](https://golang.org/pkg/fmt/) 등 세 개의 패키지를 가져옵니다.

이 코드는 [sql.Open()](http://godoc.org/github.com/lib/pq#Open) 메서드를 호출하여 PostgreSQL용 Azure Database에 연결하고 [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping) 메서드를 사용하여 연결을 확인합니다. [데이터베이스 핸들](https://golang.org/pkg/database/sql/#DB)은 이러한 과정 내내 사용되며 데이터베이스 서버에 대한 연결 풀을 보유합니다. 이 코드는 [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) 메서드를 여러 번 호출하여 여러 SQL 명령을 실행합니다. 매번 사용자 지정 checkError() 메서드가 오류 발생 여부를 확인하고 오류가 발생하면 서둘러 종료합니다.


`HOST`, `DATABASE`, `USER` 및 `PASSWORD` 매개 변수는 원하는 값으로 바꾸세요. 

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    // Initialize connection string.
    var connectionString string = fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Drop previous table of same name if one exists.
    _, err = db.Exec("DROP TABLE IF EXISTS inventory;")
    checkError(err)
    fmt.Println("Finished dropping table (if existed)")

    // Create table.
    _, err = db.Exec("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
    checkError(err)
    fmt.Println("Finished creating table")

    // Insert some data into table.
    sql_statement := "INSERT INTO inventory (name, quantity) VALUES ($1, $2);"
    _, err = db.Exec(sql_statement, "banana", 150)
    checkError(err)
    _, err = db.Exec(sql_statement, "orange", 154)
    checkError(err)
    _, err = db.Exec(sql_statement, "apple", 100)
    checkError(err)
    fmt.Println("Inserted 3 rows of data")
}
```

## 데이터 읽기
<a id="read-data" class="xliff"></a>
**SELECT** SQL 문을 사용하여 데이터를 연결하고 읽으려면 다음 코드를 사용하세요. 

이 코드는 [sql package](https://golang.org/pkg/database/sql/), Postgres 서버와 통신할 때 드라이버로 사용되는 [pq package](http://godoc.org/github.com/lib/pq), 명령줄에 인쇄된 입력 및 출력을 위한 [fmt package](https://golang.org/pkg/fmt/) 등 세 개의 패키지를 가져옵니다.

이 코드는 [sql.Open()](http://godoc.org/github.com/lib/pq#Open) 메서드를 호출하여 PostgreSQL용 Azure Database에 연결하고 [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping) 메서드를 사용하여 연결을 확인합니다. [데이터베이스 핸들](https://golang.org/pkg/database/sql/#DB)은 이러한 과정 내내 사용되며 데이터베이스 서버에 대한 연결 풀을 보유합니다. Select 쿼리는 [db.Query()](https://golang.org/pkg/database/sql/#DB.Query) 메서드를 호출하여 실행되고, 결과 행은 [rows](https://golang.org/pkg/database/sql/#Rows) 유형의 변수에 보관됩니다. 코드는 [rows.Scan()](https://golang.org/pkg/database/sql/#Rows.Scan) 메서드를 사용하여 현재 행의 열 데이터 값을 읽고, 더 이상 행이 존재하지 않을 때까지 [rows.Next()](https://golang.org/pkg/database/sql/#Rows.Next) 반복기를 사용하여 행에 대해 반복됩니다. 각 행의 열 값은 콘솔에 출력됩니다. 매번 사용자 지정 checkError() 메서드가 오류 발생 여부를 확인하고 오류가 발생하면 서둘러 종료합니다.

`HOST`, `DATABASE`, `USER` 및 `PASSWORD` 매개 변수는 원하는 값으로 바꾸세요. 

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString string = fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Read rows from table.
    var id int
    var name string
    var quantity int

    sql_statement := "SELECT * from inventory;"
    rows, err := db.Query(sql_statement)
    checkError(err)

    for rows.Next() {
        switch err := rows.Scan(&id, &name, &quantity); err {
        case sql.ErrNoRows:
            fmt.Println("No rows were returned")
        case nil:
            fmt.Printf("Data row = (%d, %s, %d)\n", id, name, quantity)
        default:
            checkError(err)
        }
    }
}
```

## 데이터 업데이트
<a id="update-data" class="xliff"></a>
**UPDATE** SQL 문을 사용하여 데이터를 연결하고 업데이트하려면 다음 코드를 사용하세요.

이 코드는 [sql package](https://golang.org/pkg/database/sql/), Postgres 서버와 통신할 때 드라이버로 사용되는 [pq package](http://godoc.org/github.com/lib/pq), 명령줄에 인쇄된 입력 및 출력을 위한 [fmt package](https://golang.org/pkg/fmt/) 등 세 개의 패키지를 가져옵니다.

이 코드는 [sql.Open()](http://godoc.org/github.com/lib/pq#Open) 메서드를 호출하여 PostgreSQL용 Azure Database에 연결하고 [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping) 메서드를 사용하여 연결을 확인합니다. [데이터베이스 핸들](https://golang.org/pkg/database/sql/#DB)은 이러한 과정 내내 사용되며 데이터베이스 서버에 대한 연결 풀을 보유합니다. 코드는 [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) 메서드를 호출하여 테이블을 업데이트하는 SQL 문을 실행합니다. 사용자 지정 checkError() 메서드가 오류 발생 여부를 확인하고 오류가 발생하면 서둘러 종료합니다.

`HOST`, `DATABASE`, `USER` 및 `PASSWORD` 매개 변수는 원하는 값으로 바꾸세요. 
```go
package main

import (
  "database/sql"
  _ "github.com/lib/pq"
  "fmt"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    
    // Initialize connection string.
    var connectionString string = 
        fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Modify some data in table.
    sql_statement := "UPDATE inventory SET quantity = $2 WHERE name = $1;"
    _, err = db.Exec(sql_statement, "banana", 200)
    checkError(err)
    fmt.Println("Updated 1 row of data")
}
```

## 데이터 삭제
<a id="delete-data" class="xliff"></a>
**DELETE** SQL 문을 사용하여 데이터를 연결하고 읽으려면 다음 코드를 사용하세요. 

이 코드는 [sql package](https://golang.org/pkg/database/sql/), Postgres 서버와 통신할 때 드라이버로 사용되는 [pq package](http://godoc.org/github.com/lib/pq), 명령줄에 인쇄된 입력 및 출력을 위한 [fmt package](https://golang.org/pkg/fmt/) 등 세 개의 패키지를 가져옵니다.

이 코드는 [sql.Open()](http://godoc.org/github.com/lib/pq#Open) 메서드를 호출하여 PostgreSQL용 Azure Database에 연결하고 [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping) 메서드를 사용하여 연결을 확인합니다. [데이터베이스 핸들](https://golang.org/pkg/database/sql/#DB)은 이러한 과정 내내 사용되며 데이터베이스 서버에 대한 연결 풀을 보유합니다. 코드는 [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) 메서드를 호출하여 테이블을 업데이트하는 SQL 문을 실행합니다. 사용자 지정 checkError() 메서드가 오류 발생 여부를 확인하고 오류가 발생하면 서둘러 종료합니다.

`HOST`, `DATABASE`, `USER` 및 `PASSWORD` 매개 변수는 원하는 값으로 바꾸세요. 
```go
package main

import (
  "database/sql"
  _ "github.com/lib/pq"
  "fmt"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    
    // Initialize connection string.
    var connectionString string = 
        fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Delete some data from table.
    sql_statement := "DELETE FROM inventory WHERE name = $1;"
    _, err = db.Exec(sql_statement, "orange")
    checkError(err)
    fmt.Println("Deleted 1 row of data")
}
```

## 다음 단계
<a id="next-steps" class="xliff"></a>
> [!div class="nextstepaction"]
> [내보내기 및 가져오기를 사용하여 데이터베이스 마이그레이션](./howto-migrate-using-export-and-import.md)

