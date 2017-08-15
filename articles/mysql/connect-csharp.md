---
title: "C#에서 MySQL용 Azure Database에 연결 | Microsoft Docs"
description: "이 빠른 시작에서는 MySQL용 Azure Database에서 데이터를 연결하고 쿼리하는 데 사용할 수 있는 C#(.NET) 코드 샘플을 제공합니다."
services: MySQL
author: seanli1988
ms.author: seal
manager: janders
editor: jasonwhowell
ms.service: MySQL-database
ms.custom: mvc
ms.devlang: csharp
ms.topic: hero-article
ms.date: 07/10/2017
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: f1488f6b4a240165c71c95f759af73d6b9fd7bfe
ms.contentlocale: ko-kr
ms.lasthandoff: 08/09/2017

---

# <a name="azure-database-for-mysql-use-net-c-to-connect-and-query-data"></a>MySQL용 Azure Database: .NET(C#)을 사용하여 데이터 연결 및 쿼리
이 빠른 시작에서는 C# 응용 프로그램을 사용하여 MySQL용 Azure Database에 연결하는 방법을 보여 줍니다. SQL 문을 사용하여 데이터베이스의 데이터를 쿼리, 삽입, 업데이트 및 삭제하는 방법을 보여 줍니다. 이 문서의 단계에서는 C#을 사용하여 개발하는 데 익숙하고 MySQL용 Azure Database를 처음 사용한다고 가정합니다.

## <a name="prerequisites"></a>필수 조건
이 빠른 시작에서는 다음과 같은 가이드 중 하나에서 만들어진 리소스를 시작 지점으로 사용합니다.
- [Azure Portal을 사용한 MySQL용 Azure Database 서버 만들기](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure CLI를 사용한 MySQL용 Azure Database 서버 만들기](./quickstart-create-mysql-server-database-using-azure-cli.md)

다음과 같은 작업도 필요합니다.
- [.NET](https://www.microsoft.com/net/download)을 설치합니다. 연결된 문서의 단계에 따라 사용하는 플랫폼(Windows, Ubuntu Linux 또는 macOS)에 맞는 .NET을 설치합니다. 
- [Visual Studio](https://www.visualstudio.com/downloads/)를 설치합니다.
- [MySQL용 ODBC 드라이버](https://dev.mysql.com/downloads/connector/odbc/)를 설치합니다.

## <a name="get-connection-information"></a>연결 정보 가져오기
MySQL용 Azure Database에 연결하는 데 필요한 연결 정보를 가져옵니다. 정규화된 서버 이름 및 로그인 자격 증명이 필요합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure Portal의 왼쪽 메뉴에서 **모든 리소스**를 클릭하고 방금 만든 서버를 검색합니다(예: **myserver4demo**).
3. 서버 이름을 클릭합니다.
4. 서버의 **속성** 페이지를 선택합니다. **서버 이름** 및 **서버 관리자 로그인 이름**을 기록해 둡니다.
 ![MySQL용 Azure Database 서버 이름](./media/connect-csharp/1_server-properties-name-login.png)
5. 서버 로그인 정보를 잊어버린 경우 **개요** 페이지로 이동하여 서버 관리자 로그인 이름을 확인하고 필요한 경우 암호를 다시 설정합니다.

## <a name="connect-create-table-and-insert-data"></a>테이블 연결, 생성 및 데이터 삽입
**CREATE TABLE** 및 **INSERT INTO** SQL 문을 사용하여 데이터를 연결하고 로드하려면 다음 코드를 사용하세요. 이 코드는 [Open()](https://msdn.microsoft.com/en-us/library/system.data.odbc.odbcconnection.open(v=vs.110).aspx) 메서드가 포함된 ODBC 클래스를 사용하여 MySQL에 대한 연결을 설정합니다. 그런 다음 [CreateCommand()](https://msdn.microsoft.com/en-us/library/system.data.odbc.odbcconnection.createcommand(v=vs.110).aspx) 메서드를 사용하고, CommandText 속성을 설정하고, [ExecuteNonQuery()](https://msdn.microsoft.com/en-us/library/system.data.odbc.odbccommand.executenonquery(v=vs.110).aspx) 메서드를 호출하여 데이터베이스 명령을 실행합니다. 

Host, DBName, User 및 Password 매개 변수는 서버 및 데이터베이스를 만들 때 지정한 값으로 바꾸세요. 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using MySql.Data;
using System.Data.Odbc;

namespace driver
{
    class MySQLCreate
    {
        static void Main(string[] args)
        {
            var conn = new OdbcConnection("DRIVER={MySQL ODBC 5.3 unicode Driver}; Server=myserver4demo.mysql.database.azure.com; Port=3306;" +
            " Database=quickstartdb; Uid=myadmin@myserver4demo; Pwd=server_admin_password; sslverify=0; Option=3;MULTI_STATEMENTS=1");

            Console.Out.WriteLine("Opening connection");
            conn.Open();

            var command = conn.CreateCommand();
            command.CommandText = "DROP TABLE IF EXISTS inventory;";
            command.ExecuteNonQuery();
            Console.Out.WriteLine("Finished dropping table (if existed)");

            command.CommandText = "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";
            command.ExecuteNonQuery();
            Console.Out.WriteLine("Finished creating table");

            command.CommandText =
                String.Format(
                    @"INSERT INTO inventory (name, quantity) VALUES ({0}, {1});
                    INSERT INTO inventory (name, quantity) VALUES ({2}, {3});
                    INSERT INTO inventory (name, quantity) VALUES ({4}, {5});",
                    "\'banana\'", 150,
                    "\'orange\'", 154,
                    "\'apple\'", 100
                    );

            int nRows = command.ExecuteNonQuery();
            Console.Out.WriteLine(String.Format("Number of rows inserted={0}", nRows));

            Console.Out.WriteLine("Closing connection");
            conn.Close();

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }

    }
}

```

## <a name="read-data"></a>데이터 읽기

**SELECT** SQL 문을 사용하여 데이터를 연결하고 읽으려면 다음 코드를 사용하세요. 이 코드는 [Open()](https://msdn.microsoft.com/en-us/library/system.data.odbc.odbcconnection.open(v=vs.110).aspx) 메서드가 포함된 ODBC 클래스를 사용하여 MySQL에 대한 연결을 설정합니다. 그리고 [CreateCommand()](https://msdn.microsoft.com/en-us/library/system.data.odbc.odbcconnection.createcommand(v=vs.110).aspx) 메서드와 [ExecuteReader()](https://msdn.microsoft.com/en-us/library/system.data.odbc.odbccommand.executereader(v=vs.110).aspx) 메서드를 사용하여 데이터베이스 명령을 실행합니다. 그런 다음 [Read()](https://msdn.microsoft.com/en-us/library/system.data.odbc.odbcdatareader.read(v=vs.110).aspx)를 사용하여 결과의 레코드로 이동합니다. 그런 다음 GetInt32 및 GetString을 사용하여 레코드의 값을 구문 분석합니다.

Host, DBName, User 및 Password 매개 변수는 서버 및 데이터베이스를 만들 때 지정한 값으로 바꾸세요. 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using MySql.Data;
using System.Data.Odbc;


namespace driver
{
    class MySQLRead
    {

        static void Main(string[] args)
        {
            var conn = new OdbcConnection("DRIVER={MySQL ODBC 5.3 unicode Driver}; Server=myserver4demo.mysql.database.azure.com; Port=3306;" +
            " Database=quickstartdb; Uid=myadmin@myserver4demo; Pwd=server_admin_password; sslverify=0; Option=3;MULTI_STATEMENTS=1");

            Console.Out.WriteLine("Opening connection");
            conn.Open();

            var command = conn.CreateCommand();
            command.CommandText = "SELECT * FROM inventory;";

            var reader = command.ExecuteReader();
            while (reader.Read())
            {
                Console.WriteLine(
                    string.Format(
                        "Reading from table=({0}, {1}, {2})",
                        reader.GetInt32(0).ToString(),
                        reader.GetString(1),
                        reader.GetInt32(2).ToString()
                        )
                    );
            }

            Console.Out.WriteLine("Closing connection");
            conn.Close();

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}


```

## <a name="update-data"></a>데이터 업데이트
**UPDATE** SQL 문을 사용하여 데이터를 연결하고 읽으려면 다음 코드를 사용하세요. 이 코드는 [Open()](https://msdn.microsoft.com/en-us/library/system.data.odbc.odbcconnection.open(v=vs.110).aspx) 메서드가 포함된 ODBC 클래스를 사용하여 MySQL에 대한 연결을 설정합니다. 그런 다음 [CreateCommand()](https://msdn.microsoft.com/en-us/library/system.data.odbc.odbcconnection.createcommand(v=vs.110).aspx) 메서드를 사용하고, CommandText 속성을 설정하고, [ExecuteNonQuery()](https://msdn.microsoft.com/en-us/library/system.data.odbc.odbccommand.executenonquery(v=vs.110).aspx) 메서드를 호출하여 데이터베이스 명령을 실행합니다.

Host, DBName, User 및 Password 매개 변수는 서버 및 데이터베이스를 만들 때 지정한 값으로 바꾸세요. 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data.Odbc;

namespace driver
{
    class MySQLUpdate
    {
        static void Main(string[] args)
        {
            var conn = new OdbcConnection("DRIVER={MySQL ODBC 5.3 unicode Driver}; Server=myserver4demo.mysql.database.azure.com; Port=3306;" +
            " Database=quickstartdb; Uid=myadmin@myserver4demo; Pwd=server_admin_password; sslverify=0; Option=3;MULTI_STATEMENTS=1");

            Console.Out.WriteLine("Opening connection");
            conn.Open();

            var command = conn.CreateCommand();
            command.CommandText =
            String.Format("UPDATE inventory SET quantity = {0} WHERE name = {1};",
                200,
                "\'banana\'"
                );

            int nRows = command.ExecuteNonQuery();
            Console.Out.WriteLine(String.Format("Number of rows updated={0}", nRows));

            Console.Out.WriteLine("Closing connection");
            conn.Close();

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}



```


## <a name="delete-data"></a>데이터 삭제
**DELETE** SQL 문을 사용하여 데이터를 연결하고 삭제하려면 다음 코드를 사용하세요. 

이 코드는 [Open()](https://msdn.microsoft.com/en-us/library/system.data.odbc.odbcconnection.open(v=vs.110).aspx) 메서드가 포함된 ODBC 클래스를 사용하여 MySQL에 대한 연결을 설정합니다. 그런 다음 [CreateCommand()](https://msdn.microsoft.com/en-us/library/system.data.odbc.odbcconnection.createcommand(v=vs.110).aspx) 메서드를 사용하고, CommandText 속성을 설정하고, [ExecuteNonQuery()](https://msdn.microsoft.com/en-us/library/system.data.odbc.odbccommand.executenonquery(v=vs.110).aspx) 메서드를 호출하여 데이터베이스 명령을 실행합니다.

Host, DBName, User 및 Password 매개 변수는 서버 및 데이터베이스를 만들 때 지정한 값으로 바꾸세요. 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data.Odbc;

namespace driver
{
    class MySQLDelete
    {
        static void Main(string[] args)
        {
            var conn = new OdbcConnection("DRIVER={MySQL ODBC 5.3 unicode Driver}; Server=myserver4demo.mysql.database.azure.com; Port=3306;" +
            " Database=quickstartdb; Uid=myadmin@myserver4demo; Pwd=server_admin_password; sslverify=0; Option=3;MULTI_STATEMENTS=1");

            Console.Out.WriteLine("Opening connection");
            conn.Open();

            var command = conn.CreateCommand();
            command.CommandText =
                String.Format("DELETE FROM inventory WHERE name = {0};",
                    "\'orange\'");
            int nRows = command.ExecuteNonQuery();
            Console.Out.WriteLine(String.Format("Number of rows deleted={0}", nRows));

            Console.Out.WriteLine("Closing connection");
            conn.Close();

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}

```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [덤프 및 복원을 사용하여 MySQL Database를 MySQL용 Azure Database로 마이그레이션](concepts-migrate-dump-restore.md)

