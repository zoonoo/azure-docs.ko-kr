---
title: Azure Cosmos DB용 SQL API에 대한 Node.js 자습서
description: SQL API를 사용하여 Azure Cosmos DB에 연결하고 쿼리하는 방법을 보여주는 Node.js 자습서
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: dech
Customer intent: As a developer, I want to build a Node.js console application to access and manage SQL API account resources in Azure Cosmos DB, so that customers can better use the service.
ms.openlocfilehash: fe925ed408f64424de8da98f6e182a06a41bf015
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58075052"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>자습서: JavaScript SDK를 사용하여 Azure Cosmos DB SQL API 데이터를 관리하는 Node.js 콘솔 앱 빌드

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET(미리 보기)](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core(미리 보기)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [비동기 Java](sql-api-async-java-get-started.md)
> * [Node.JS](sql-api-nodejs-get-started.md)
> 

개발자는 NoSQL 문서 데이터를 사용하는 애플리케이션을 가지고 있을 수도 있습니다. Azure Cosmos DB의 SQL API 계정을 사용하여 이 문서 데이터를 저장하고 액세스할 수 있습니다. 이 자습서에서는 Azure Cosmos DB 리소스를 만들고 쿼리하는 Node.js 콘솔 애플리케이션을 빌드하는 방법을 보여 줍니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * Azure Cosmos DB 계정 만들기 및 연결
> * 애플리케이션 설치
> * 데이터베이스 만들기
> * 컨테이너를 만듭니다.
> * 컨테이너에 항목 추가
> * 항목, 컨테이너 및 데이터베이스에 대한 기본 작업 수행

## <a name="prerequisites"></a>필수 조건 

다음 리소스가 있는지 확인합니다.

* 활성 Azure 계정. 아직 구독하지 않은 경우 [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) v6.0.0 이상이 필요합니다.

## <a name="create-azure-cosmos-db-account"></a>Azure Cosmos DB 계정 만들기

Azure Cosmos DB 계정을 만들어 보겠습니다. 사용하려는 계정이 이미 있는 경우 [Node.js 응용 프로그램 설치](#SetupNode)로 건너뛸 수 있습니다. Azure Cosmos DB 에뮬레이터를 사용하는 경우 [Azure Cosmos DB 에뮬레이터](local-emulator.md)의 단계에 따라 에뮬레이터를 설정하고 [Node.js 애플리케이션 설정](#SetupNode)으로 건너뜁니다. 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupNode"></a>Node.js 애플리케이션 설치

애플리케이션을 빌드하는 코드를 작성하기 전에 앱에 대한 프레임워크를 빌드할 수 있습니다. 다음 단계를 실행하여 프레임워크 코드가 있는 Node.js 애플리케이션을 설치합니다.

1. 자주 사용하는 터미널을 엽니다.
2. Node.js 애플리케이션을 저장하려는 폴더 또는 디렉터리를 찾습니다.
3. 다음 명령을 사용하여 두 개의 빈 JavaScript 파일을 만듭니다.

   * Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```

   * Linux/OS X:
     * ```touch app.js```
     * ```touch config.js```

4. `package.json` 파일을 만들고 초기화합니다. 다음 명령을 사용합니다.
   * ```npm init -y```

5. npm을 통해 @azure/cosmos 모듈을 설치합니다. 다음 명령을 사용합니다.
   * ```npm install @azure/cosmos --save```

## <a id="Config"></a>앱의 구성 설정

이제 앱이 있으므로 Azure Cosmos DB와 통신할 수 있는지 확인합니다. 다음 단계와 같이 몇 가지 구성 설정을 업데이트하면 앱에서 Azure Cosmos DB와 통신하도록 설정할 수 있습니다.

1. 원하는 텍스트 편집기에서 ```config.js```을 엽니다.

1. 아래 코드 조각을 복사하여 붙여넣고 ```config.endpoint``` 및 ```config.primaryKey``` 속성을 Azure Cosmos DB 엔드포인트 URI 및 기본 키로 설정합니다. 이러한 구성은 모두 [Azure Portal](https://portal.azure.com)에서 찾을 수 있습니다.

   ![Azure Portal에서 키 가져오기 스크린샷][keys]

   ```javascript
   // ADD THIS PART TO YOUR CODE
   var config = {}

   config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
   config.primaryKey = "~your primary key here~";
   ``` 

1. ```database```, ```container``` 및 ```items``` 데이터를 복사하여 ```config.endpoint``` 및 ```config.primaryKey``` 속성을 설정한 아래의 ```config``` 개체에 붙여넣습니다. 데이터베이스에 저장하려는 데이터가 이미 있는 경우 여기서 데이터를 정의하는 대신 Azure Cosmos DB의 데이터 마이그레이션 도구를 사용할 수 있습니다.

   ```javascript
   var config = {}

   config.endpoint = "~your Azure Cosmos DB account endpoint uri here~";
   config.primaryKey = "~your primary key here~";

   config.database = {
      "id": "FamilyDatabase"
   };

   config.container = {
     "id": "FamilyContainer"
   };

   config.items = {
      "Andersen": {
          "id": "Anderson.1",
          "lastName": "Andersen",
          "parents": [{
            "firstName": "Thomas"
        }, {
                "firstName": "Mary Kay"
            }],
        "children": [{
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [{
                "givenName": "Fluffy"
            }]
        }],
        "address": {
            "state": "WA",
            "county": "King",
            "city": "Seattle"
        }
    },
    "Wakefield": {
        "id": "Wakefield.7",
        "parents": [{
            "familyName": "Wakefield",
            "firstName": "Robin"
        }, {
                "familyName": "Miller",
                "firstName": "Ben"
            }],
        "children": [{
            "familyName": "Merriam",
            "firstName": "Jesse",
            "gender": "female",
            "grade": 8,
            "pets": [{
                "givenName": "Goofy"
            }, {
                    "givenName": "Shadow"
                }]
        }, {
                "familyName": "Miller",
                "firstName": "Lisa",
                "gender": "female",
                "grade": 1
            }],
        "address": {
            "state": "NY",
            "county": "Manhattan",
            "city": "NY"
        },
        "isRegistered": false
      }
   };
   ```

   JavaScript SDK에서는 일반 용어인 *컨테이너* 및 *항목*을 사용합니다. 컨테이너는 컬렉션, 그래프 또는 테이블입니다. 항목은 문서, 에지/꼭짓점 또는 행이며, 컨테이너 내부의 콘텐츠입니다. 

1. 마지막으로 ```config``` 개체를 내보내므로 ```app.js``` 파일 내에서 참조할 수 있습니다.

   ```javascript
        },
        "isRegistered": false
       }
   };

   // ADD THIS PART TO YOUR CODE
   module.exports = config;
   ```

## <a id="Connect"></a>Azure Cosmos DB 계정에 연결

1. 텍스트 편집기에서 빈 ```app.js``` 파일을 엽니다. 다음 코드를 복사하고 붙여넣어서 ```@azure/cosmos``` 모듈 및 새로 만든 ```config``` 모듈을 가져옵니다.

   ```javascript
   // ADD THIS PART TO YOUR CODE
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');
   ```

1. 이전에 저장한 ```config.endpoint``` 및 ```config.primaryKey```를 사용하는 코드를 복사하고 붙여넣어서 새 CosmosClient를 만듭니다.

   ```javascript
   const config = require('./config');

   // ADD THIS PART TO YOUR CODE
   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });
   ```
   
> [!Note]
> **Cosmos DB 에뮬레이터**에 연결하는 경우 사용자 지정 연결 정책을 만들어서 SSL 확인을 사용하지 않도록 설정합니다.
>   ```
>   const connectionPolicy = new cosmos.ConnectionPolicy ()
>   connectionPolicy.DisableSSLVerification = true
>
>   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey }, connectionPolicy });
>   ```

Azure Cosmos DB 클라이언트를 시작하는 코드가 생겼으니, Azure Cosmos DB 리소스를 작업하는 방법을 알아보겠습니다.

## <a name="create-a-database"></a>데이터베이스 만들기

1. 아래 코드를 복사하여 붙여넣고, 데이터베이스 ID와 컨테이너 ID를 설정합니다. 이러한 ID를 통해 Azure Cosmos DB 클라이언트에서 올바른 데이터베이스와 컨테이너를 찾을 수 있습니다.

   ```javascript
   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   // ADD THIS PART TO YOUR CODE
   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   ```

   데이터베이스는 `createIfNotExists` 또는 **Databases** 클래스의 create 함수를 사용하여 만들 수 있습니다. 데이터베이스는 여러 컨테이너에 분할된 항목의 논리적 컨테이너입니다. 

2. **createDatabase** 및 **readDatabase** 메서드를 복사하여 ```databaseId``` 및 ```containerId``` 정의 아래의 app.js 파일에 붙여넣습니다. **createDatabase** 함수는 id ```FamilyDatabase```를 사용하여 새 데이터베이스를 만들며, 아직 없는 경우 ```config```에서 지정됩니다. **readDatabase** 함수는 데이터베이스의 정의를 읽어서 데이터베이스가 있는지 확인합니다.

   ```javascript
   /**
    * Create the database if it does not exist
    */
   async function createDatabase() {
       const { database } = await client.databases.createIfNotExists({ id: databaseId });
       console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
      const { body: databaseDefinition } = await client.database(databaseId).read();
      console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }
   ```

3. **createDatabase** 및 **readDatabase** 함수를 설정한 아래 코드를 복사하고 붙여넣어서 종료 메시지를 인쇄하는 도우미 함수 **exit**를 추가합니다. 

   ```javascript
   // ADD THIS PART TO YOUR CODE
   function exit(message) {
      console.log(message);
      console.log('Press any key to exit');
      process.stdin.setRawMode(true);
      process.stdin.resume();
      process.stdin.on('data', process.exit.bind(process, 0));
   };
   ```

4. **exit** 함수를 설정한 아래 코드에 복사하고 붙여넣어서 **createDatabase** 및 **readDatabase** 함수를 호출합니다.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error \${JSON.stringify(error)}`) });
   ```

   이제 ```app.js```의 코드가 다음 코드와 비슷하게 보입니다.

   ```javascript
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');

   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;

    /**
    * Create the database if it does not exist
    */
    async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { body: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error) }`) });
   ```

5. 터미널에서 ```app.js``` 파일을 찾고 다음 명령을 실행합니다. 

   ```bash 
   node app.js
   ```

## <a id="CreateContainer"></a>컨테이너 만들기

다음으로, 데이터를 저장하고 쿼리할 수 있도록 Azure Cosmos DB 계정 내에 컨테이너를 만듭니다. 

> [!WARNING]
> 컨테이너를 만드는 것은 가격 책정에 영향을 줍니다. 예상되는 결과를 확인하려면 [가격 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 방문하세요.

컨테이너는 `createIfNotExists` 또는 **Containers** 클래스의 create 함수를 사용하여 만들 수 있습니다.  컨테이너는 항목(SQL API의 경우 JSON 문서) 및 관련 JavaScript 애플리케이션 논리로 구성됩니다.

1. **createContainer** 및 **readContainer** 함수를 복사하여 app.js 파일의 **readDatabase** 함수 아래에 붙여넣습니다. **createContainer** 함수는 아직 ID가 없으면 ```config```에서 지정된 ```containerId```를 사용하여 새 컨테이너를 만듭니다. **readContainer** 함수는 컨테이너 정의를 읽어서 컨테이너가 있는지 확인합니다.

   ```javascript
   /**
   * Create the container if it does not exist
   */
   async function createContainer() {
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
   * Read the container definition
   */
   async function readContainer() {
     const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
     console.log(`Reading container:\n${containerDefinition.id}\n`);
   }
   ```

1. **readDatabase** 호출 아래의 코드를 복사하고 붙여넣어서 **createContainer** 및 **readContainer** 함수를 실행합니다.

   ```javascript
   createDatabase()
     .then(() => readDatabase())

     // ADD THIS PART TO YOUR CODE
     .then(() => createContainer())
     .then(() => readContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

   이제 ```app.js```의 코드는 다음과 비슷합니다.

   ```javascript
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');

   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;

   /**
   * Create the database if it does not exist
   */
   async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { body: databaseDefinition } = await client.database(databaseId).read();
     console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Create the container if it does not exist
   */
   async function createContainer() {
     const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
     console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
   * Read the container definition
   */
   async function readContainer() {
     const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
     console.log(`Reading container:\n${containerDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. 터미널에서 ```app.js``` 파일을 찾고 다음 명령을 실행합니다. 

   ```bash 
   node app.js
   ```

## <a id="CreateItem"></a>항목 만들기

항목은 **Items** 클래스의 create 함수를 사용하여 만들 수 있습니다. SQL API를 사용하는 경우 항목은 사용자 정의(임의) JSON 콘텐츠인 문서로 프로젝션됩니다. 이제 Azure Cosmos DB에 항목을 삽입할 수 있습니다.

1. **createFamilyItem** 함수를 복사하여 **readContainer** 함수 아래에 붙여넣습니다. **createFamilyItem** 함수는 ```config``` 개체에 저장된 JSON 데이터를 포함하는 항목을 만듭니다. ID를 만들기 전에 같은 ID를 가진 항목이 있는지 확인하겠습니다.

   ```javascript
   /**
   * Create family item if it does not exist
   */
   async function createFamilyItem(itemBody) {
     try {
         // read the item to see if it exists
         const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).read();
         console.log(`Item with family id ${itemBody.id} already exists\n`);
     }
     catch (error) {
        // create the family item if it does not exist
        if (error.code === HttpStatusCodes.NOTFOUND) {
            const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
            console.log(`Created family item with id:\n${itemBody.id}\n`);
        } else {
            throw error;
        }
     }
   };
   ```

1. **readContainer** 호출 아래의 코드를 복사하고 붙여넣어서 **createFamilyItem** 함수를 실행합니다.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. 터미널에서 ```app.js``` 파일을 찾고 다음 명령을 실행합니다. 

   ```bash 
   node app.js
   ```


## <a id="Query"></a>Azure Cosmos DB 리소스 쿼리
Azure Cosmos DB는 각 컨테이너에 저장된 JSON 문서에 대해 다양한 쿼리를 지원합니다. 다음 샘플 코드는 컨테이너의 문서에 대해 실행할 수 있는 쿼리를 보여줍니다.

1. **createFamilyItem** 함수 아래의 **queryContainer** 함수를 복사하여 app.js 파일에 붙여넣습니다. Azure Cosmos DB는 아래와 같이 SQL과 비슷한 쿼리를 지원합니다.

   ```javascript
   /**
   * Query the container using SQL
    */
   async function queryContainer() {
     console.log(`Querying container:\n${config.container.id}`);

     // query to return all children in a family
     const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { result: results } = await client.database(databaseId).container(containerId).items.query(querySpec).toArray();
    for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
   };
   ```

1. **createFamilyItem** 호출 아래의 코드를 복사하고 붙여넣어서 **queryContainer** 함수를 실행합니다.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))

     // ADD THIS PART TO YOUR CODE
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. 터미널에서 ```app.js``` 파일을 찾고 다음 명령을 실행합니다.

   ```bash 
   node app.js
   ```


## <a id="ReplaceItem"></a>항목 바꾸기
Azure Cosmos DB는 항목의 콘텐츠 바꾸기를 지원합니다.

1. **queryContainer** 함수 아래의 **replaceFamilyItem** 함수를 복사하여 app.js 파일에 붙여넣습니다. 자식 요소의 '등급' 속성을 이전 값 5에서 6으로 변경했습니다.

   ```javascript
   // ADD THIS PART TO YOUR CODE
   /**
   * Replace the item by ID.
   */
   async function replaceFamilyItem(itemBody) {
      console.log(`Replacing item:\n${itemBody.id}\n`);
      // Change property 'grade'
      itemBody.children[0].grade = 6;
      const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).replace(itemBody);
   };
   ```

1. **queryContainer** 호출 아래의 코드를 복사하고 붙여넣어서 **replaceFamilyItem** 함수를 실행합니다. 또한 **queryContainer**를 호출하는 코드를 다시 추가하여 항목이 성공적으로 변경되었는지 확인합니다.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     .then(() => queryContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => replaceFamilyItem(config.items.Andersen))
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. 터미널에서 ```app.js``` 파일을 찾고 다음 명령을 실행합니다.

   ```bash 
   node app.js
   ```


## <a id="DeleteItem"></a>항목 삭제

Azure Cosmos DB는 JSON 항목 삭제를 지원합니다.

1. **deleteFamilyItem** 함수를 복사하여 **replaceFamilyItem** 함수 아래에 붙여넣습니다.

   ```javascript
   /**
   * Delete the item by ID.
   */
   async function deleteFamilyItem(itemBody) {
      await client.database(databaseId).container(containerId).item(itemBody.id).delete(itemBody);
      console.log(`Deleted item:\n${itemBody.id}\n`);
   };
   ```

1. 두 번째 **queryContainer** 호출 아래의 코드를 복사하고 붙여넣어서 **deleteFamilyItem** 함수를 실행합니다.

   ```javascript
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer
      ())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())

    // ADD THIS PART TO YOUR CODE
      .then(() => deleteFamilyItem(config.items.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. 터미널에서 ```app.js``` 파일을 찾고 다음 명령을 실행합니다. 

   ```bash 
   node app.js
   ```


## <a id="DeleteDatabase"></a>데이터베이스 삭제

만든 데이터베이스를 삭제하면 데이터베이스와 모든 자식 리소스(컨테이너, 항목 등)가 제거됩니다.

1. **cleanup** 함수를 복사하여 **deleteFamilyItem** 함수 아래에 붙여넣어 데이터베이스와 모든 자식 리소스를 제거합니다.

   ```javascript
   /**
   * Cleanup the database and container on completion
   */
   async function cleanup() {
     await client.database(databaseId).delete();
   }
   ```

1. **deleteFamilyItem** 호출 아래의 코드를 복사하고 붙여넣어서 **cleanup** 함수를 실행합니다.

   ```javascript
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())
      .then(() => deleteFamilyItem(config.items.Andersen))

      // ADD THIS PART TO YOUR CODE
      .then(() => cleanup())
      // ENDS HERE

      .then(() => { exit(`Completed successfully`); })
      .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

## <a id="Run"></a>Node.js 애플리케이션 실행

전체적으로 코드는 다음과 비슷할 것입니다.

```javascript
const CosmosClient = require('@azure/cosmos').CosmosClient;

const config = require('./config');

const endpoint = config.endpoint;
const masterKey = config.primaryKey;

const HttpStatusCodes = { NOTFOUND: 404 };

const databaseId = config.database.id;
const containerId = config.container.id;

const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

/**
 * Create the database if it does not exist
 */
async function createDatabase() {
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    console.log(`Created database:\n${database.id}\n`);
}

/**
 * Read the database definition
 */
async function readDatabase() {
    const { body: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
}

/**
 * Create the container if it does not exist
 */
async function createContainer() {
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    console.log(`Created container:\n${config.container.id}\n`);
}

/**
 * Read the container definition
 */
async function readContainer() {
    const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
}

/**
 * Create family item if it does not exist
 */
async function createFamilyItem(itemBody) {
    try {
        // read the item to see if it exists
        const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).read();
        console.log(`Item with family id ${itemBody.id} already exists\n`);
    }
    catch (error) {
        // create the family item if it does not exist
        if (error.code === HttpStatusCodes.NOTFOUND) {
            const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
            console.log(`Created family item with id:\n${itemBody.id}\n`);
        } else {
            throw error;
        }
    }
};

/**
 * Query the container using SQL
 */
async function queryContainer() {
    console.log(`Querying container:\n${config.container.id}`);

    // query to return all children in a family
    const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { result: results } = await client.database(databaseId).container(containerId).items.query(querySpec).toArray();
    for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
};

/**
 * Replace the item by ID.
 */
async function replaceFamilyItem(itemBody) {
    console.log(`Replacing item:\n${itemBody.id}\n`);
    // Change property 'grade'
    itemBody.children[0].grade = 6;
    const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).replace(itemBody);
};

/**
 * Delete the item by ID.
 */
async function deleteFamilyItem(itemBody) {
    await client.database(databaseId).container(containerId).item(itemBody.id).delete(itemBody);
    console.log(`Deleted item:\n${itemBody.id}\n`);
};

/**
 * Cleanup the database and container on completion
 */
async function cleanup() {
    await client.database(databaseId).delete();
}

/**
 * Exit the app with a prompt
 * @param {message} message - The message to display
 */
function exit(message) {
    console.log(message);
    console.log('Press any key to exit');
    process.stdin.setRawMode(true);
    process.stdin.resume();
    process.stdin.on('data', process.exit.bind(process, 0));
}

createDatabase()
    .then(() => readDatabase())
    .then(() => createContainer())
    .then(() => readContainer())
    .then(() => createFamilyItem(config.items.Andersen))
    .then(() => createFamilyItem(config.items.Wakefield))
    .then(() => queryContainer())
    .then(() => replaceFamilyItem(config.items.Andersen))
    .then(() => queryContainer())
    .then(() => deleteFamilyItem(config.items.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
```

터미널에서 ```app.js``` 파일을 찾고 다음 명령을 실행합니다. 

```bash 
node app.js
```

시작한 앱의 출력이 표시됩니다. 출력은 아래의 예제 텍스트와 일치해야 합니다.

   ```
    Created database:
    FamilyDatabase

    Reading database:
    FamilyDatabase

    Created container:
    FamilyContainer

    Reading container:
    FamilyContainer

    Created family item with id:
    Anderson.1

    Created family item with id:
    Wakefield.7

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing item:
    Anderson.1

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleted item:
    Anderson.1

    Completed successfully
    Press any key to exit
   ```

## <a id="GetSolution"></a>전체 Node.js 자습서 솔루션 다운로드 

이 자습서의 단계를 완료할 시간이 없거나 코드를 다운로드하려는 경우 [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started )에서 가져올 수 있습니다. 

이 문서의 모든 코드가 포함된 시작 솔루션을 실행하려면 다음이 필요합니다. 

* [Azure Cosmos DB 계정][create-account]. 
* GitHub에서 제공하는 [시작](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started) 솔루션. 

npm을 통해 프로젝트의 종속성을 설치합니다. 다음 명령을 사용합니다. 

* ```npm install``` 

다음으로, ```config.js``` 파일에서 config.endpoint 및 config.primaryKey 값을 [3단계: 앱의 구성 설정](#Config)에서 설명한 대로 업데이트합니다.  

그런 다음, 터미널에서 ```app.js``` 파일을 찾아서 다음 명령을 실행합니다.  

```bash  
node app.js 
```

## <a name="clean-up-resources"></a>리소스 정리

이러한 리소스가 더 이상 필요하지 않은 경우 리소스 그룹, Azure Cosmos DB 계정 및 모든 관련 리소스를 삭제할 수 있습니다. 이렇게 하려면 Azure Cosmos DB 계정에 사용한 리소스 그룹을 선택하고, **삭제**를 선택한 다음, 삭제할 리소스 그룹의 이름을 확인합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Cosmos DB 계정 모니터링](monitor-accounts.md)

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png
