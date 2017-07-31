---
title: "Python에서 PostgreSQL용 Azure Database에 연결 | Microsoft Docs"
description: "PostgreSQL용 Azure Database의 데이터를 연결하고 쿼리하는 데 사용할 수 있는 Python 코드 샘플을 제공합니다."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.devlang: python
ms.topic: hero-article
ms.date: 07/07/2017
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 3cd090b02887857a68271f021e3580e05660d1dc
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="azure-database-for-postgresql-use-python-to-connect-and-query-data"></a>PostgreSQL용 Azure Database: Python을 사용하여 데이터 연결 및 쿼리
이 빠른 시작은 [Python](https://python.org)을 사용하여 PostgreSQL용 Azure Database에 연결한 후 Mac OS, Ubuntu Linux 및 Windows 플랫폼의 데이터베이스에서 SQL 문을 사용하여 데이터를 쿼리, 삽입, 업데이트 및 삭제하는 방법을 보여 줍니다. 이 문서의 단계에서는 개발자가 Python을 사용하여 개발하는 것에 익숙하고 PostgreSQL용 Azure Database 작업에 익숙하지 않다고 가정합니다.

## <a name="prerequisites"></a>필수 조건
이 빠른 시작에서는 다음과 같은 가이드 중 하나에서 만들어진 리소스를 시작 지점으로 사용합니다.
- [DB 만들기 - 포털](quickstart-create-server-database-portal.md)
- [DB 만들기 - CLI](quickstart-create-server-database-azure-cli.md)

다음 항목도 필요합니다.
- [python](https://www.python.org/downloads/) 설치
- [pip](https://pip.pypa.io/en/stable/installing/) 패키지 설치([python.org](https://python.org)에서 다운로드한 Python 2 >=2.7.9 또는 Python 3 >=3.4 바이너리를 사용하는 경우 이미 설치되지만, pip를 업그레이드해야 합니다.)

## <a name="install-the-python-connection-libraries-for-postgresql"></a>PostgreSQL용 Python 연결 라이브러리 설치
데이터베이스를 연결하고 쿼리할 수 있는 [psycopg2](http://initd.org/psycopg/docs/install.html) 패키지를 설치합니다. psycopg2는 가장 일반적인 플랫폼(OSX, Linux, Windows)에 대한 [휠](http://pythonwheels.com/) 패키지의 형태로 [PyPI에서 사용할 수](https://pypi.python.org/pypi/psycopg2/) 있기 때문에, 모든 종속 관계를 포함한 모듈의 이진 버전을 가져오기 위해 pip 설치를 사용할 수 있습니다.

```cmd
pip install psycopg2
```
최신 버전의 pip을 사용하고 있는지 확인하세요(`pip install -U pip`과 같은 것을 사용하여 업그레이드할 수 있음).

## <a name="get-connection-information"></a>연결 정보 가져오기
PostgreSQL용 Azure Database에 연결하는 데 필요한 연결 정보를 가져옵니다. 정규화된 서버 이름 및 로그인 자격 증명이 필요합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure Portal의 왼쪽 메뉴에서 **모든 리소스**를 클릭하고 방금 만든 **mypgserver-20170401** 서버를 검색합니다.
3. **mypgserver-20170401**서버 이름을 클릭합니다.
4. 서버의 **개요** 페이지를 선택합니다. **서버 이름** 및 **서버 관리자 로그인 이름**을 기록해 둡니다.
 ![PostgreSQL용 Azure Database - 서버 관리자 로그인](./media/connect-python/1-connection-string.png)
5. 서버 로그인 정보를 잊어버린 경우 **개요** 페이지로 이동하여 서버 관리자 로그인 이름을 확인하고 필요한 경우 암호를 다시 설정합니다.

## <a name="how-to-run-python-code"></a>Python 코드를 실행하는 방법
- 원하는 텍스트 편집기를 사용하여 postgres.py라는 새 파일을 만들어 프로젝트 폴더에 저장합니다. 아래에서 보여 주는 코드 샘플을 복사하여 텍스트 파일에 붙여넣고 저장합니다. Windows OS에서 파일을 저장하는 경우 UTF-8 인코딩을 선택해야 합니다. 
- 코드를 실행하려면 명령 프롬프트 또는 Bash 셸을 시작합니다. 디렉터리를 프로젝트 폴더로 변경합니다(예: `cd postgresql`). 그런 다음 python 명령 다음에 파일 이름을 입력합니다(예: `python postgresql.py`).

> [!NOTE]
> Python 버전 3부터는 아래 코드 블록을 실행할 때 `SyntaxError: Missing parentheses in call to 'print'` 오류가 표시될 수 있습니다. 이 경우 `print "string"` 명령에 대한 각 호출을 함수 호출(괄호 사용)로 바꾸세요(예: `print("string")`).

## <a name="connect-create-table-and-insert-data"></a>테이블 연결, 생성 및 데이터 삽입
**INSERT** SQL 문이 있는 [psycopg2.connect](http://initd.org/psycopg/docs/connection.html) 함수를 사용하여 데이터를 연결하고 로드하려면 다음 코드를 사용하세요. [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) 함수는 PostgreSQL 데이터베이스에 대해 SQL 쿼리를 실행하는 데 사용됩니다. host, dbname, user 및 password 매개 변수는 서버 및 데이터베이스를 만들 때 지정한 값으로 바꾸세요.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print "Finished dropping table (if existed)"

# Create table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print "Finished creating table"

# Insert some data into table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print "Inserted 3 rows of data"

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="read-data"></a>데이터 읽기
[cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) 함수와 **SELECT** SQL 문을 사용하여 삽입된 데이터를 읽으려면 다음 코드를 사용하세요. 이 함수는 쿼리를 허용하며, [cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall)을 사용하여 반복될 수 있는 결과 집합을 반환합니다. host, dbname, user 및 password 매개 변수는 서버 및 데이터베이스를 만들 때 지정한 값으로 바꾸세요.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print "Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2]))

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="update-data"></a>데이터 업데이트
[cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) 함수와 **UPDATE** SQL 문을 사용하여 이전에 삽입된 인벤토리 행을 업데이트하려면 다음 코드를 사용하세요. host, dbname, user 및 password 매개 변수는 서버 및 데이터베이스를 만들 때 지정한 값으로 바꾸세요.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print "Updated 1 row of data"

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="delete-data"></a>데이터 삭제
[cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) 함수와 **DELETE** SQL 문을 사용하여 이전에 삽입된 인벤토리 항목을 삭제하려면 다음 코드를 사용하세요. host, dbname, user 및 password 매개 변수는 서버 및 데이터베이스를 만들 때 지정한 값으로 바꾸세요.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print "Deleted 1 row of data"

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [내보내기 및 가져오기를 사용하여 데이터베이스 마이그레이션](./howto-migrate-using-export-and-import.md)

