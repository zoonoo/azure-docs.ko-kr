---
title: "PostgreSQL용 Azure 데이터베이스에서 Python 읽기 | Microsoft Docs"
description: "사전 지식이 없다는 가정 하에, Python 코드 샘플을 실행하여 PostgreSQL용 Azure 데이터베이스의 테이블에서 데이터를 쓰고 읽는 방법을 설명합니다."
services: postgresql
author: MightyPen
ms.author: genemi
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 42f5e8191e52d1e2a4502a961158b6fec1c061d1
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---
# <a name="python-reading-from-azure-database-for-postgresql"></a>PostgreSQL용 Azure 데이터베이스에서 Python 읽기


이 문서에서는 SQL SELECT 문을 사용하여 테이블에서 행을 읽는 간단한 Python 프로그램을 제공합니다. 또한 Python 프로그램을 실행하는 데 필요한 모든 필수 구성 요소를 찾아서 설치하는 방법도 설명합니다.

샘플 Python 프로그램 및 설명된 Python 관련 도구는 Linux, Mac 및 Windows를 비롯한 다양한 플랫폼에 동일하게 적용됩니다. 


## <a name="install-the-python-interpreter"></a>Python 인터프리터 설치


이 문서의 Python 코드 샘플은 Python 인터프리터 버전 2.7용으로 작성되었으며 버전 3.x에서는 실행할 수 없습니다.

Python 인터프리터 2.7 버전을 다음에서 다운로드하여 설치합니다.

- [python.org에서 Python 인터프리터 다운로드](https://www.python.org/downloads/)

설치한 후 명령줄에서 인터프리터를 찾아 실행할 수 있다는 사실을 입증합니다. 다음과 같은 명령을 사용합니다.

`python.exe -?`


## <a name="install-pipexe-the-python-module-installer"></a>Python 모듈 설치 관리자인 pip.exe 설치


Python 인터프리터를 설치하면 *Scripts/*라는 하위 디렉터리에 pip.exe도 설치되었을 것입니다. Pip.exe는 특수화된 Python 모듈을 설치합니다. pip.exe를 찾아서 실행할 수 있음을 입증합니다.

`pip.exe`

pip.exe를 실행할 수 없는 경우 **get-pip.py**라는 Python 유틸리티 프로그램 파일이 있는지 여부를 확인합니다. get-pip.py가 있으면 이 프로그램을 실행하여 pip.exe를 가져올 수 있습니다.

`python.exe get-pip.py`


## <a name="install-psycopg-the-connection-module"></a>연결 모듈 psycopg 설치


Python 프로그램에는 프로그램을 PostgreSQL용 Azure 데이터베이스 서버에 연결하는 방법을 알고 있는 모듈이 필요합니다. 연결 모듈의 이름은 **psycopg2**입니다. 자세한 정보를 원할 경우 다음을 참조하세요.

- [psycopg2 웹 사이트](http://initd.org/psycopg/)

다음 pip.exe install 명령을 사용하여 psycopg2를 설치합니다.

`pip.exe install psycopg2`


## <a name="create-an-azure-database-for-postgresql-server"></a>PostgreSQL용 Azure 데이터베이스 서버 만들기


PostgreSQL용 Azure 데이터베이스 서버에 대한 액세스 권한이 아직 없는 경우 서버를 만드는 방법을 설명하는 다음 설명서를 참조하세요.

- [Azure Portal을 사용하여 PostgreSQL용 Azure 데이터베이스 만들기](quickstart-create-server-database-portal.md)
- [Azure CLI를 사용하여 PostgreSQL용 Azure 데이터베이스 만들기](quickstart-create-server-database-azure-cli.md)


## <a name="obtain-the-connection-string-values"></a>연결 문자열 값 획득


Azure Portal에서 PostgreSQL용 Azure 데이터베이스 서버에 대한 연결 문자열 값을 얻을 수 있습니다. 다음 표에 설명된 대로 매개 변수 값이 필요합니다.

- *Name* 열 &ndash;에는 psycopg2가 반환하는 매개 변수 식별자가 표시됩니다.
- *-Symbol* 열 &ndash;에는 샘플 Python 프로그램 *PythonDriver.py*에 필요한 매개 변수 식별자가 표시됩니다.


| 이름 | -Symbol | 값 예제 |
| :--  | :--     | :--           |
| host | -h | myazurepostgresql.database.windows.net |
| 사용자 | -U | myalias@myazurepostgresql |
| dbname | 일시 중지되고 | postgres<br />*(모든 PostgreSQL 서버에는 **postgres**라는 데이터베이스가 있습니다.)* |
| 포트 | -p | 5432*(이 특정 값 5432일 수 있음)* |
| password | -P | MySecretPassword |
||||


## <a name="the-python-sample-program"></a>Python 샘플 프로그램


이 섹션에서는 샘플 Python 프로그램에 대한 소스 코드를 제공합니다. 이 프로그램은 이 문서 뒷부분에서 실행합니다.

```python
# PythonDriver.py
# Python version 2.7

import psycopg2
import sys
import getopt

def main():

    host = ""
    user = ""
    dbname = ""
    port = ""
    password = ""

    try:
        opts, args = getopt.getopt(sys.argv[1:], "h:U:d:p:P:", [])
    except getopt.GetoptError as exc:
        print str(exc)
        usage()
        exit(2)

    for o, a in opts:
        if o == "-h": host = str(a)
        if o == "-U": user = str(a)
        if o == "-d": dbname = str(a)
        if o == "-p": port = str(a)
        if o == "-P": password = str(a)

    conn_string = "host={0} user={1} dbname={2} password={3}".format(host, user, dbname, password)

    try:
        # Create a connection.  Raise an exception if cannot connect.
        conn = psycopg2.connect(conn_string) 
        cursor = conn.cursor()

        #cursor.execute("DROP TABLE testpy1;")
        #conn.commit()

        cursor.execute("CREATE TABLE testpy1 (id serial PRIMARY KEY, num integer, data varchar);")

        cursor.execute("INSERT INTO testpy1 (num, data) VALUES (%s, %s)", (100, "First'row"))
        cursor.execute("INSERT INTO testpy1 (num, data) VALUES (%s, %s)", (110, "Second_row"))
        conn.commit()

        cursor.execute("SELECT id, num, data FROM testpy1;")
        result = cursor.fetchone()

        # Optionally, you can comment these clean-up lines to leave
        # the testpy1 table available for your inspection by pgAdmin. 
        cursor.execute("DROP TABLE testpy1;")
        conn.commit()

    except Exception as exc:
        print "FAILED ", exc
        exit(1)

    print "SUCCESS: ", result

if __name__ == "__main__":
    main()
```


## <a name="command-lines-to-run-the-sample-program"></a>샘플 프로그램을 실행하기 위한 명령줄


이 섹션에는 실제 확인 출력과 함께 Python 샘플 프로그램을 테스트하는 데 사용되는 명령줄이 표시됩니다.

앞서 설명된 매개 변수의 경우, 여기에 표시된 샘플 값을 실제 연결 문자열 값으로 바꾸어야 합니다.

이전 샘플 Python 프로그램을 실행하기 위한 정확한 구문이 약간 달라질 수 있습니다. 정확한 구문은 사용 중인 운영 체제 및 사용하는 콘솔 유형에 따라 다릅니다.


#### <a name="windows-cmdexe-console"></a>Windows cmd.exe 콘솔


다음 코드 블록은 샘플 Python 프로그램의 실제 테스트 실행을 나타냅니다. 간단한 cmd.exe 명령줄이 사용되었습니다. 각 명령줄 다음에 '^' 줄 연속 문자가 입력되었습니다.

1. Enter 키를 눌렀습니다.
2. **More?** 구가 표시되었습니다.
3. 전체 줄의 다른 부분도 입력되었습니다.

이 줄 연속 기술은 예제의 너비가 너무 커서 출력 시 및 여기에 제대로 표시되지 않는 경우를 방지하기 위해 사용되었습니다.

```cmd
set Prompt=[$P\]$_$+$G$G$S

[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
>> .\python.exe ".\_myalias\PythonDriver.py" ^
More?  -h myazurepostgresql.database.windows.net ^
More?  -p 5432 ^
More?  -d postgres ^
More?  -U myalias@myazurepostgresql ^
More?  -P mySecretPassword
SUCCESS:  (1, 100, "First'row")

[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
>>
```

프로그램이 실행되었음을 확인하기 위해 표시되는 **SUCCESS** 줄을 볼 수 있습니다.

대체 기술로, 전체 명령줄을 .bat 파일에 추가할 수 있습니다. 그런 후 cmd.exe 명령줄에서 .bat 파일을 실행할 수 있습니다.


#### <a name="powershell-file"></a>PowerShell 파일


PowerShell 콘솔에서 명령줄은 줄 연속 문자를 지원하지 않습니다. 따라서 이 PowerShell 섹션에서는 PowerShell 파일에 명령을 추가합니다. 그런 후 PowerShell 명령줄에서 파일을 실행합니다.

다음 코드를 *PythonDriverRun.ps1* 파일에 복사합니다.

```powershell
# PythonDriverRun.ps1
cd C:\Users\myalias\AppData\Local\Programs\Python\Python27\

.\python.exe `
 .\_myalias\PythonDriver.py `
 -h myazurepostgresql.database.windows.net `
 -p 5432 `
 -d postgres `
 -U myalias@myazurepostgresql `
 -P mySecretPassword
```

다음과 같이 PythonDriverRun.ps1을 실행합니다. 확인을 위해 프로그램이 출력하는 **SUCCESS** 줄을 볼 수 있습니다.

```cmd
[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
0 >> .\_myalias\PythonDriverRun.ps1
SUCCESS:  (1, 100, "First'row")

[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
0 >>
```

PowerShell 명령줄에서 직접 PythonDriverRun.ps1을 실행하는 것을 선호하는 경우 '&' 문자와 공백을 추가해야 합니다. 선행 '&'를 추가하지 않으면 확인 메시지가 너무 빠르게 사라져서 볼 수 없습니다.

```
[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
0 >> & .\python.exe ".\_myalias\PythonDriver.py" -h myazurepostgresql.database.windows.net -p 5432 -d postgres -U myalias@myazurepostgresql -P mySecretPassword
SUCCESS:  (1, 100, "First'row")
```


## <a name="install-pgadmin-to-inspect-your-server"></a>서버를 검사를 위해 pgAdmin 설치


PythonDriverRun.ps1 프로그램은 종료될 때 프로그램이 만든 testpy1 테이블을 삭제하여 자체적으로 정리를 진행합니다. '#'를 사용하여 **DROP TABLE** 문을 실행하는 소스 코드 줄을 주석 처리하는 옵션도 제공됩니다. 이 옵션을 사용하면 나중에 테이블을 검사할 수 있게 테이블은 유지됩니다.

pgAdmin 도구를 사용하면 모든 PostgreSQL 서버 및 서버 내의 개체를 검사할 수 있습니다. Microsoft SQL Server 또는 Azure SQL Database의 사용자는 SSMS(SQL Server Management Studio) 및 pgAdmin 간에 유사성을 알게 됩니다.

원할 경우 **pgAdmin**을 설치하여 서버 및 **testpy1** 테이블을 검사할 수 있습니다.


#### <a name="1-install-pgadmin"></a>1. pgAdmin 설치


pgAdmin에 대한 설치 지침은 다음에서 사용할 수 있습니다.

- [http://www.pgadmin.org/](http://www.pgadmin.org/)

**pgAdmin4.exe**는 단순히 pgAdmin.exe가 아니라 실행 파일의 이름일 수 있습니다.

Windows 컴퓨터에서 pgAdmin4.exe를 실행하려면 명령줄에서 다음 명령과 유사한 명령을 입력합니다.

`"C:\Program Files (x86)\pgAdmin 4\v1\runtime\pgAdmin4.exe"`


#### <a name="2-connect-pgadmin-to-your-server"></a>2. pgAdmin을 서버에 연결

 
pgAdmin UI가 표시되면 **브라우저** 창을 찾습니다. 그런 후 마우스 오른쪽 단추로 **서버** > **만들기** > **서버**를 클릭합니다. 여기서 용어 *만들기*는 PostgreSQL용 Azure 데이터베이스 서버로의 연결을 비롯하여 기존 PostgreSQL 서버에 대한 *연결*을 만드는 것을 의미합니다.

연결이 설정되면 개체의 트리가 **브라우저** 창에 표시됩니다.


#### <a name="3-navigate-in-the-pgadmin-tree-to-your-table"></a>3. pgAdmin 트리에서 테이블 탐색


테이블을 보려면 다음과 같이 트리 요소를 확장합니다.

- **Servers** &gt; *[YourServerHere]* &gt; **Databases** &gt; postgres &gt; **Schemas** &gt; public &gt; **tables** &gt; testpy1

![트리에 테이블 testpy1을 표시하는 pgAdmin](./media/connect-python/pgAdmin-postgresql-table-testpy1.jpg)


#### <a name="4-drop-the-testpy1-table"></a>4. testpy1 테이블 삭제


최종 정리를 위해 testpy1 노드를 마우스 오른쪽 단추로 클릭하고 **삭제**를 클릭합니다.


## <a name="next-steps"></a>다음 단계

- [Azure SQL 데이터베이스에 대한 Python 연결](../sql-database/sql-database-connect-query-python.md)
- [PostgreSQL용 Azure 데이터베이스에 대한 연결 라이브러리](concepts-connection-libraries.md)

