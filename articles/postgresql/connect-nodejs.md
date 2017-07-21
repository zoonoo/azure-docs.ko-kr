---
title: "Node.js에서 PostgreSQL용 Azure Database에 연결 | Microsoft Docs"
description: "이 빠른 시작에서는 PostgreSQL용 Azure Database의 데이터를 연결하고 쿼리하는 데 사용할 수 있는 Node.js 코드 샘플을 제공합니다."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 06/23/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 6a51fcf4f4494e5b32ccf6dabb19f8d004bb20d4
ms.contentlocale: ko-kr
ms.lasthandoff: 06/26/2017

---

# PostgreSQL용 Azure Database: Node.js를 사용하여 데이터 연결 및 쿼리
<a id="azure-database-for-postgresql-use-nodejs-to-connect-and-query-data" class="xliff"></a>
이 빠른 시작에서는 Windows, Ubuntu Linux, Mac 플랫폼에서 [Node.js](https://nodejs.org/)를 사용하여 PostgreSQL용 Azure Database에 연결하는 방법을 보여 줍니다. SQL 문을 사용하여 데이터베이스의 데이터를 쿼리, 삽입, 업데이트 및 삭제하는 방법을 보여 줍니다. 이 문서의 단계에서는 개발자가 Node.js를 사용하여 개발하는 것에 익숙하고 PostgreSQL용 Azure Database 작업에 익숙하지 않다고 가정합니다.

## 필수 조건
<a id="prerequisites" class="xliff"></a>
이 빠른 시작에서는 다음과 같은 가이드 중 하나에서 만들어진 리소스를 시작 지점으로 사용합니다.
- [DB 만들기 - 포털](quickstart-create-server-database-portal.md)
- [DB 만들기 - CLI](quickstart-create-server-database-azure-cli.md)

다음과 같은 작업도 필요합니다.
- [Node.js](https://nodejs.org)
- [pg](https://www.npmjs.com/package/pg) 패키지 설치 

## Node.js 설치
<a id="install-nodejs" class="xliff"></a> 
플랫폼에 따라 Node.js를 설치하려면:

### **Mac OS**
<a id="mac-os" class="xliff"></a>
**Node.js** 및 사용하기 쉬운 Mac OS X용 패키지 관리자인 **brew**를 설치하려면 다음 명령을 입력합니다.

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew install node
```

### **Linux(Ubuntu)**
<a id="linux-ubuntu" class="xliff"></a>
**Node.js** 및 Node.js용 패키지 관리자인 **npm**을 설치하려면 다음 명령을 입력합니다.

```bash
sudo apt-get install -y nodejs npm
```

### **Windows**
<a id="windows" class="xliff"></a>
[Node.js 다운로드 페이지](https://nodejs.org/en/download/)를 방문하여 원하는 Windows 설치 관리자 옵션을 선택합니다.

## Pg 클라이언트 설치
<a id="install-pg-client" class="xliff"></a>
[pg](https://www.npmjs.com/package/pg)를 설치합니다. 이는 PostgreSQL에 연결하고 쿼리하는 데 유용한 node.js용 순수 JavaScript 비차단 클라이언트입니다.

이렇게 하려면 pg 클라이언트를 설치하기 위한 명령줄에서 JavaScript에 대한 노드 패키지 관리자(npm)를 실행합니다.
```bash
npm install pg
```

설치된 패키지를 나열하여 설치를 확인합니다.
```bash
npm list
```
목록 명령 출력을 통해 각 구성 요소 버전을 확인합니다. 
```
`-- pg@6.2.3
  +-- buffer-writer@1.0.1
  +-- packet-reader@0.3.1
etc...
```

## 연결 정보 가져오기
<a id="get-connection-information" class="xliff"></a>
PostgreSQL용 Azure Database에 연결하는 데 필요한 연결 정보를 가져옵니다. 정규화된 서버 이름 및 로그인 자격 증명이 필요합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure Portal의 왼쪽 메뉴에서 **모든 리소스**를 클릭하고 방금 만든 **mypgserver-20170401** 서버를 검색합니다.
3. **mypgserver-20170401**서버 이름을 클릭합니다.
4. 서버의 **개요** 페이지를 선택합니다. **서버 이름** 및 **서버 관리자 로그인 이름**을 기록해 둡니다.
 ![PostgreSQL용 Azure Database - 서버 관리자 로그인](./media/connect-nodejs/1-connection-string.png)
5. 서버 로그인 정보를 잊어버린 경우 **개요** 페이지로 이동하여 서버 관리자 로그인 이름을 확인하고 필요한 경우 암호를 다시 설정합니다.

## Node.js에서 JavaScript 코드 실행
<a id="running-the-javascript-code-in-nodejs" class="xliff"></a>
`node`를 입력하여 bash 셸 또는 windows 명령 프롬프트에서 Node.js를 시작한 다음, 예시 JavaScript 코드를 복사하여 프롬프트에 붙여넣어 대화형으로 실행할 수 있습니다. 또는 JavaScript 코드를 텍스트 파일로 저장하고 파일 이름을 매개 변수로 지정한 `node filename.js`를 시작하여 실행할 수 있습니다.

## 테이블 연결, 생성 및 데이터 삽입
<a id="connect-create-table-and-insert-data" class="xliff"></a>
**CREATE TABLE** 및 **INSERT INTO** SQL 문을 사용하여 데이터를 연결하고 로드하려면 다음 코드를 사용하세요.
[pg.Client](https://github.com/brianc/node-postgres/wiki/Client) 개체는 PostgreSQL 서버와 상호 작용하는 데 사용됩니다. [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) 함수는 서버에 연결하는 데 사용됩니다. [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) 함수는 PostgreSQL 데이터베이스에 대해 SQL 쿼리를 실행하는 데 사용됩니다. 

host, dbname, user 및 password 매개 변수는 서버 및 데이터베이스를 만들 때 지정한 값으로 바꾸세요. 

```javascript
const pg = require('pg');

var config =
{
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: 'mypgsqldb',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(function (err)
{
    if (err)
        throw err;
    else
    {
        queryDatabase();
    }
});

function queryDatabase()
{
    client.query(
        ' \
            DROP TABLE IF EXISTS inventory; \
            CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER); \
            INSERT INTO inventory (name, quantity) VALUES (\'banana\', 150); \
            INSERT INTO inventory (name, quantity) VALUES (\'orange\', 154); \
            INSERT INTO inventory (name, quantity) VALUES (\'apple\', 100); \
        ',
        function (err)
    {
        console.log("Connection established");

        if (err)
            throw err;
        else
        {
            client.end(function (err)
            {
                if (err)
                    throw err;

                // Else closing connection finished without error
                console.log("Closed client connection");
            });

            console.log("Finished execution, exiting now");
            process.exit()
        }
    });
}
```

## 데이터 읽기
<a id="read-data" class="xliff"></a>
**SELECT** SQL 문을 사용하여 데이터를 연결하고 읽으려면 다음 코드를 사용하세요. [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) 개체는 PostgreSQL 서버와 상호 작용하는 데 사용됩니다. [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) 함수는 서버에 연결하는 데 사용됩니다. [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) 함수는 PostgreSQL 데이터베이스에 대해 SQL 쿼리를 실행하는 데 사용됩니다. 

host, dbname, user 및 password 매개 변수는 서버 및 데이터베이스를 만들 때 지정한 값으로 바꾸세요. 

```javascript
const pg = require('pg');

var config =
{
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: 'mypgsqldb',
    port: 5432,
    ssl: true
};


const client = new pg.Client(config);

client.connect(function (err)
{
    if (err)
        throw err;

    else
    {
        console.log("Connected to Azure Database for PostgreSQL server:" + config.host);
        queryDatabase();
    }
});

function queryDatabase()
{
    // Declare array to hold query result set
    const results = [];

    console.log("Running query to PostgreSQL server:" + config.host);

    // Perform query
    var query = client.query('SELECT * FROM inventory;');

    // Print result set
    query.on('row', function(row)
    {
        console.log("Read " + JSON.stringify(row));
    });

    // Exit program after execution
    query.on('end', function(row)
    {
        console.log("Finished execution, exiting now");
        process.exit()
    });
}
```

## 데이터 업데이트
<a id="update-data" class="xliff"></a>
**UPDATE** SQL 문을 사용하여 데이터를 연결하고 읽으려면 다음 코드를 사용하세요. [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) 개체는 PostgreSQL 서버와 상호 작용하는 데 사용됩니다. [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) 함수는 서버에 연결하는 데 사용됩니다. [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) 함수는 PostgreSQL 데이터베이스에 대해 SQL 쿼리를 실행하는 데 사용됩니다. 

host, dbname, user 및 password 매개 변수는 서버 및 데이터베이스를 만들 때 지정한 값으로 바꾸세요. 

```javascript
const pg = require('pg');

var config =
{
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: 'mypgsqldb',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(function (err)
{
    if (err)
        throw err;
    else
    {
        queryDatabase();
    }   
});

function queryDatabase()
{
    client.query('UPDATE inventory SET quantity= 1000 WHERE name=\'banana\';', function (err, result)
    {
        console.log("Connection established");

        if (err)
            throw err;
        else
        {
            client.end(function (err)
            {
                if (err)
                    throw err;
                
                // Else closing connection finished without error
                console.log("Closed client connection");
            });             
        }

        console.log("Finished execution, exiting now");
        process.exit()
    });
}
```

## 데이터 삭제
<a id="delete-data" class="xliff"></a>
**DELETE** SQL 문을 사용하여 데이터를 연결하고 읽으려면 다음 코드를 사용하세요. [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) 개체는 PostgreSQL 서버와 상호 작용하는 데 사용됩니다. [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) 함수는 서버에 연결하는 데 사용됩니다. [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) 함수는 PostgreSQL 데이터베이스에 대해 SQL 쿼리를 실행하는 데 사용됩니다. 

host, dbname, user 및 password 매개 변수는 서버 및 데이터베이스를 만들 때 지정한 값으로 바꾸세요. 

```javascript
const pg = require('pg');

var config =
{
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: 'mypgsqldb',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(function (err)
{
    if (err)
        throw err;
    else
    {
        queryDatabase();
    }   
});

function queryDatabase()
{
    client.query('DELETE FROM inventory WHERE name=\'apple\';', function (err, result)
    {
        console.log("Connection established");
        
        if (err)
            throw err;
        else
        {
            client.end(function (err)
            {
                if (err)
                    throw err;
                
                // Else closing connection finished without error
                console.log("Closed client connection");
            });

            console.log("Finished execution, exiting now");
            process.exit()          
        }
    }); 
}
```

## 다음 단계
<a id="next-steps" class="xliff"></a>
> [!div class="nextstepaction"]
> [내보내기 및 가져오기를 사용하여 데이터베이스 마이그레이션](./howto-migrate-using-export-and-import.md)

