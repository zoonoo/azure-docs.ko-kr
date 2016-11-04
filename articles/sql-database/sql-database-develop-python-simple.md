---
title: Python을 사용하여 SQL 데이터베이스에 연결 | Microsoft Docs
description: Azure SQL 데이터베이스에 연결하는 데 사용할 수 있는 Python 코드 샘플을 제시합니다.
services: sql-database
documentationcenter: ''
author: meet-bhagdev
manager: jhubbard
editor: ''

ms.service: sql-database
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 10/05/2016
ms.author: meetb

---
# <a name="connect-to-sql-database-by-using-python"></a>Python을 사용하여 SQL 데이터베이스에 연결
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

이 토픽에서는 Python을 사용하여 Azure SQL Database를 연결 및 쿼리하는 방법을 보여 줍니다. Windows, Ubuntu Linux 또는 Mac 플랫폼에서 이 샘플을 실행할 수 있습니다.

## <a name="step-1:-create-a-sql-database"></a>1단계: SQL 데이터베이스 만들기
샘플 데이터베이스를 만드는 방법을 알아보려면 [시작 페이지](sql-database-get-started.md) 를 참조하세요.  안내에 따라 **AdventureWorks 데이터베이스 템플릿**을 만드는 것이 중요합니다. 아래 표시된 샘플은 **AdventureWorks 스키마**에서만 작동합니다. 데이터베이스를 만들었으면 [시작 페이지](sql-database-get-started.md)에 설명된 대로 방화벽 규칙을 사용하여 IP 주소에 액세스할 수 있도록 설정해야 합니다.

## <a name="step-2:-configure-development-environment"></a>2단계: 개발 환경 구성
### <a name="**mac-os**"></a>**Mac OS**
### <a name="install-the-required-modules"></a>필요한 모듈 설치
터미널을 열고 설치

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    brew install FreeTDS
    sudo -H pip install pymssql=2.1.1

### <a name="**linux-(ubuntu)**"></a>**Linux(Ubuntu)**
터미널을 열고 Python 스크립트를 만들려는 디렉터리로 이동합니다. 다음 명령을 입력하여 **FreeTDS** 및 **pymssql**을 설치합니다. pymssql은 FreeTDS를 사용하여 SQL Databases에 연결합니다.

    sudo apt-get --assume-yes update
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    sudo apt-get --assume-yes install python-dev python-pip
    sudo pip install pymssql=2.1.1

### <a name="**windows**"></a>**Windows**
[**여기**](http://www.lfd.uci.edu/~gohlke/pythonlibs/#pymssql)에서 pymssql을 설치합니다. 

올바른 whl 파일을 선택했는지 확인합니다. 예: 64비트 컴퓨터에서 Python 2.7을 사용하는 경우 pymssql‑2.1.1‑cp27‑none‑win_amd64.whl을 선택합니다. .whl 파일을 다운로드한 후 C:/Python27 폴더에 넣습니다.

이제 명령줄에서 pip를 사용하여 pymssql 드라이버를 설치합니다. C:/Python27로 디렉터리를 변경하고 다음을 실행합니다.

    pip install pymssql‑2.1.1‑cp27‑none‑win_amd64.whl

pip 사용을 활성화하는 방법은 [여기](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)에서 찾을 수 있습니다.

## <a name="step-3:-run-sample-code"></a>3단계: 샘플 코드 실행
**sql_sample.py**라는 파일을 만들고 그 안에 다음 코드를 붙여 넣습니다. 다음을 사용하여 명령줄에서 이 코드를 실행할 수 있습니다.

    python sql_sample.py

### <a name="connect-to-your-sql-database"></a>SQL 데이터베이스 연결
SQL 데이터베이스에 연결하는 데 [pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html) 함수가 사용됩니다.

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


### <a name="execute-an-sql-select-statement"></a>SQL SELECT 문 실행
[cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute) 함수를 사용하여 SQL 데이터베이스에 대한 쿼리에서 결과 집합을 검색할 수 있습니다. 이 함수는 본질적으로 모든 쿼리를 허용하며, [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone)을 사용하여 반복될 수 있는 결과 집합을 반환합니다.

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
    row = cursor.fetchone()
    while row:
        print str(row[0]) + " " + str(row[1]) + " " + str(row[2])   
        row = cursor.fetchone()


### <a name="insert-a-row,-pass-parameters,-and-retrieve-the-generated-primary-key"></a>행을 삽입하고, 매개 변수를 전달하며, 생성된 기본 키를 검색합니다.
SQL Database에서 [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) 속성 및 [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) 개체를 사용하여 [기본 키](https://msdn.microsoft.com/library/ms179610.aspx) 값을 자동으로 생성할 수 있습니다. 

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
    row = cursor.fetchone()
    while row:
        print "Inserted Product ID : " +str(row[0])
        row = cursor.fetchone()


### <a name="transactions"></a>트랜잭션
이 코드 예제는 다음과 같은 트랜잭션의 사용법을 보여줍니다.

* 트랜잭션 시작
* 데이터 행 삽입
* 트랜잭션을 롤백하여 삽입 취소 

sql_sample.py 내부에 다음 코드를 붙여 넣습니다.

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("BEGIN TRANSACTION")
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
    cnxn.rollback()

## <a name="next-steps"></a>다음 단계
* [SQL 데이터베이스 개발 개요](sql-database-develop-overview.md)
* [SQL Server용 Microsoft Python Driver](https://msdn.microsoft.com/library/mt652092.aspx)
* [Python 개발자 센터](/develop/python/)를 방문합니다.

## <a name="additional-resources"></a>추가 리소스
* [Azure SQL 데이터베이스를 사용한 다중 테넌트 SaaS 응용 프로그램 디자인 패턴](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* 모든 [SQL 데이터베이스의 기능](https://azure.microsoft.com/services/sql-database/)

<!--HONumber=Oct16_HO2-->


