---
title: "DocumentDB용 NoSQL Node.js 자습서 | Microsoft Docs"
description: "DocumentDB Node.js SDK를 사용하여 NoSQL 데이터베이스 및 콘솔 응용 프로그램을 만드는 NoSQL Node.js 자습서입니다. DocumentDB는 JSON에 대한 NoSQL 데이터베이스입니다."
keywords: "node.js 자습서, 노드 데이터베이스"
services: documentdb
documentationcenter: node.js
author: AndrewHoh
manager: jhubbard
editor: monicar
ms.assetid: 14d52110-1dce-4ac0-9dd9-f936afccd550
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: node
ms.topic: hero-article
ms.date: 12/25/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 2b8ac838e9387b04467f03d0608da05b3edfdd26
ms.lasthandoff: 03/29/2017


---
# <a name="nosql-nodejs-tutorial-documentdb-nodejs-console-application"></a>NoSQL Node.js 자습서: DocumentDB Node.js 콘솔 응용 프로그램
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [MongoDB용 Node.js](documentdb-mongodb-samples.md)
> * [Node.JS](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

Azure DocumentDB Node.js SDK용 Node.js 자습서를 시작합니다. 이 자습서를 따라 하면 DocumentDB 리소스를 만들고 쿼리하는 콘솔 응용 프로그램이 생깁니다.

다음에 대해 설명합니다.

* DocumentDB 계정 만들기 및 연결
* 응용 프로그램 설정
* 노드 데이터베이스 만들기
* 컬렉션 만들기
* JSON 문서 만들기
* 컬렉션 쿼리
* 문서 바꾸기
* 문서 삭제
* 노드 데이터베이스 삭제

시간이 없으십니까? 염려하지 마십시오. [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started)에서 전체 솔루션을 사용할 수 있습니다. 빠른 지침은 [전체 솔루션 다운로드](#GetSolution) 를 참조하세요.

Node.js 자습서를 완료한 후에 이 페이지 위쪽 및 아래쪽에 있는 응답 단추를 통해 의견을 보내주세요. 직접 연락을 받고 싶은 경우 설명에 메일 주소를 포함하세요.

이제 시작하겠습니다.

## <a name="prerequisites-for-the-nodejs-tutorial"></a>Node.js 자습서의 필수 조건
다음 항목이 있는지 확인합니다.

* 활성 Azure 계정. 아직 구독하지 않은 경우 [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.
    * 또는 이 자습서에 [Azure DocumentDB 에뮬레이터](documentdb-nosql-local-emulator.md)를 사용할 수 있습니다.
* [Node.js](https://nodejs.org/) 버전 v0.10.29 이상

## <a name="step-1-create-a-documentdb-account"></a>1단계: DocumentDB 계정 만들기
DocumentDB 계정을 만들어 보겠습니다. 계정이 이미 있는 경우 [Node.js 응용 프로그램 설치](#SetupNode)로 건너뛸 수 있습니다. DocumentDB 에뮬레이터를 사용할 경우 [Azure DocumentDB 에뮬레이터](documentdb-nosql-local-emulator.md)의 단계에 따라 에뮬레이터를 설정하고 [Node.js 설정](#SetupNode)으로 건너뜁니다.

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="SetupNode"></a>2단계: Node.js 응용 프로그램 설치
1. 자주 사용하는 터미널을 엽니다.
2. Node.js 응용 프로그램을 저장하려는 폴더 또는 디렉터리를 찾습니다.
3. 다음 명령을 사용하여 두 개의 빈 JavaScript 파일을 만듭니다.
   * Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```
   * Linux/OS X:
     * ```touch app.js```
     * ```touch config.js```
4. npm을 통해 documentdb 모듈을 설치합니다. 다음 명령을 사용합니다.
   * ```npm install documentdb --save```

잘하셨습니다. 설치를 완료했으므로 코드를 작성해 보겠습니다.

## <a id="Config"></a>3단계: 앱의 구성 설정
원하는 텍스트 편집기에서 ```config.js```을 엽니다.

그런 다음 아래 코드 조각을 복사하고 붙여넣은 다음 속성 ```config.endpoint``` 및 ```config.primaryKey```를 DocumentDB 끝점 URI 및 기본 키로 설정합니다. 이러한 구성은 모두 [Azure 포털](https://portal.azure.com)에서 찾을 수 있습니다.

![Node.js 자습서 - 활성 허브, DocumentDB 계정 블레이드의 키 단추 및 키 블레이드의 URI, 기본 키 및 보조키 값이 강조 표시된 DocumentDB 계정을 보여 주는 Azure 포털의 스크린샷 - 노드 데이터베이스][keys]

    // ADD THIS PART TO YOUR CODE
    var config = {}

    config.endpoint = "~your DocumentDB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

```database id```, ```collection id``` 및 ```JSON documents```을 ```config.endpoint``` 및 ```config.authKey``` 속성을 설정한 아래의 ```config``` 개체로 설정합니다. 데이터베이스에 저장하려는 데이터가 이미 있다면 문서 정의를 추가하는 대신 DocumentDB의 [데이터 마이그레이션 도구](documentdb-import-data.md) 를 사용할 수 있습니다.

    config.endpoint = "~your DocumentDB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

    // ADD THIS PART TO YOUR CODE
    config.database = {
        "id": "FamilyDB"
    };

    config.collection = {
        "id": "FamilyColl"
    };

    config.documents = {
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


데이터베이스, 컬렉션 및 문서 정의는 DocumentDB ```database id```, ```collection id``` 및 문서 데이터의 역할을 합니다.

마지막으로 ```config``` 개체를 내보내므로 ```app.js``` 파일 내에서 참조할 수 있습니다.

            },
            "isRegistered": false
        }
    };

    // ADD THIS PART TO YOUR CODE
    module.exports = config;

## <a id="Connect"></a>4단계: DocumentDB 계정에 연결
텍스트 편집기에서 빈 ```app.js``` 파일을 엽니다. 다음 코드를 복사하고 붙여넣어서 ```documentdb``` 모듈 및 새로 만든 ```config``` 모듈을 가져옵니다.

    // ADD THIS PART TO YOUR CODE
    "use strict";

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");
    var url = require('url');

이전에 저장된 ```config.endpoint``` 및 ```config.primaryKey```를 사용하는 코드를 복사하고 붙여넣어서 새 DocumentClient를 만듭니다.

    var config = require("./config");
    var url = require('url');

    // ADD THIS PART TO YOUR CODE
    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

documentdb 계정을 시작하는 코드가 있다면 DocumentDB 리소스와 함께 작동하는지 살펴보겠습니다.

## <a name="step-5-create-a-node-database"></a>5단계: 노드 데이터베이스 만들기
다음 코드를 복사하고 붙여넣어서 찾을 수 없음, 데이터베이스 URL 및 컬렉션 URL에 대한 HTTP 상태를 설정합니다. 이러한 URL을 통해 DocumentDB 클라이언트가 올바른 데이터베이스 및 컬렉션을 찾을 수 있습니다.

    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

    // ADD THIS PART TO YOUR CODE
    var HttpStatusCodes = { NOTFOUND: 404 };
    var databaseUrl = `dbs/${config.database.id}`;
    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

**DocumentClient** 클래스의 [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) 함수를 사용하여 [데이터베이스](documentdb-resources.md#databases)를 만들 수 있습니다. 데이터베이스는 여러 컬렉션으로 분할된 문서 저장소의 논리적 컨테이너입니다.

```config``` 개체에 지정된 ```id```를 사용하여 app.js 파일에서 새 데이터베이스를 만들기 위해 **getDatabase** 함수를 복사하고 붙여넣습니다. 함수는 동일한 ```FamilyRegistry``` ID를 가진 데이터베이스가 이미 있는지 확인합니다. 파일이 존재하는 경우 새로 만드는 대신 해당 데이터베이스를 반환합니다.

    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

    // ADD THIS PART TO YOUR CODE
    function getDatabase() {
        console.log(`Getting database:\n${config.database.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDatabase(databaseUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDatabase(config.database, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

**getDatabase** 함수를 설정한 아래 코드를 복사하고 붙여넣어서 종료 메시지를 인쇄하고 **getDatabase** 함수를 호출하는 도우미 함수 **종료**를 추가합니다.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function exit(message) {
        console.log(message);
        console.log('Press any key to exit');
        process.stdin.setRawMode(true);
        process.stdin.resume();
        process.stdin.on('data', process.exit.bind(process, 0));
    }

    getDatabase()
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

터미널에서 ```app.js``` 파일을 찾고 다음 명령을 실행합니다. ```node app.js```

축하합니다. DocumentDB 데이터베이스가 성공적으로 생성되었습니다.

## <a id="CreateColl"></a>6단계: 컬렉션 만들기
> [!WARNING]
> **CreateDocumentCollectionAsync** 는 가격 책정 의미가 포함된 새 컬렉션을 만듭니다. 자세한 내용은 [가격 페이지](https://azure.microsoft.com/pricing/details/documentdb/)를 참조하세요.
> 
> 

**DocumentClient** 클래스의 [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) 함수를 사용하여 [컬렉션](documentdb-resources.md#collections)을 만들 수 있습니다. 컬렉션은 JSON 문서 및 관련 JavaScript 응용 프로그램 논리의 컨테이너입니다.

**getCollection** 함수를 복사하여 app.js 파일의 **getDatabase** 함수 아래에 붙여넣어 ```config``` 개체에 지정된 ```id```를 포함한 새 컬렉션을 만듭니다. 다시 동일한 ```FamilyCollection``` ID를 가진 컬렉션이 이미 있는지 확인합니다. 파일이 존재하는 경우 새로 만드는 대신 해당 컬렉션을 반환합니다.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getCollection() {
        console.log(`Getting collection:\n${config.collection.id}\n`);

        return new Promise((resolve, reject) => {
            client.readCollection(collectionUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createCollection(databaseUrl, config.collection, { offerThroughput: 400 }, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

**getDatabase**에 대한 호출 아래에 코드를 복사하고 붙여넣어서 **getCollection** 함수를 실행합니다.

    getDatabase()

    // ADD THIS PART TO YOUR CODE
    .then(() => getCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

터미널에서 ```app.js``` 파일을 찾고 다음 명령을 실행합니다. ```node app.js```

축하합니다. DocumentDB 컬렉션이 성공적으로 생성되었습니다.

## <a id="CreateDoc"></a>7단계: 문서 만들기
**DocumentClient** 클래스의 [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) 함수를 사용하여 [문서](documentdb-resources.md#documents)를 만들 수 있습니다. 문서는 사용자 정의(임의) JSON 콘텐츠입니다. 이제 DocumentDB 문서를 삽입할 수 있습니다.

```config``` 체에 저장된 JSON 데이터를 포함하는 문서를 만들기 위해 **getCollection** 함수 아래에 있는 **getFamilyDocument** 함수를 복사하고 붙여넣습니다. 다시 동일한 ID를 가진 문서가 이미 있는지 확인합니다.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Getting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDocument(documentUrl, { partitionKey: document.district }, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDocument(collectionUrl, document, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    };

**getCollection**에 대한 호출 아래에 코드를 복사하고 붙여넣어서 **getFamilyDocument** 함수를 실행합니다.

    getDatabase()
    .then(() => getCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

터미널에서 ```app.js``` 파일을 찾고 다음 명령을 실행합니다. ```node app.js```

축하합니다. DocumentDB 문서가 성공적으로 생성되었습니다.

![Node.js 자습서 - 계정, 데이터베이스, 컬렉션 및 문서 간의 계층 관계를 보여 주는 다이어그램 - 노드 데이터베이스](./media/documentdb-nodejs-get-started/node-js-tutorial-account-database.png)

## <a id="Query"></a>8단계: DocumentDB 리소스 쿼리
DocumentDB는 각 컬렉션에 저장된 JSON 문서에 대해 [다양한 쿼리](documentdb-sql-query.md) 를 지원합니다. 다음 샘플 코드에서는 컬렉션에는 문서에 대해 실행할 수 있는 쿼리를 보여줍니다.

**queryCollection** 함수를 복사하여 app.js 파일의 **getFamilyDocument** 함수 아래에 붙여넣습니다. DocumentDB는 아래와 같이 SQL과 비슷한 쿼리를 지원합니다. 복잡한 쿼리 작성에 대한 자세한 내용은 [쿼리 실습](https://www.documentdb.com/sql/demo) 및 [쿼리 설명서](documentdb-sql-query.md)를 확인합니다.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function queryCollection() {
        console.log(`Querying collection through index:\n${config.collection.id}`);

        return new Promise((resolve, reject) => {
            client.queryDocuments(
                collectionUrl,
                'SELECT VALUE r.children FROM root r WHERE r.lastName = "Andersen"'
            ).toArray((err, results) => {
                if (err) reject(err)
                else {
                    for (var queryResult of results) {
                        let resultString = JSON.stringify(queryResult);
                        console.log(`\tQuery returned ${resultString}`);
                    }
                    console.log();
                    resolve(results);
                }
            });
        });
    };


다음 다이어그램에서는 만든 컬렉션에 대해 DocumentDB SQL 쿼리 구문을 호출하는 방법을 보여 줍니다.

![Node.js 자습서 - 쿼리의 의미와 범위를 보여 주는 다이어그램 - 노드 데이터베이스](./media/documentdb-nodejs-get-started/node-js-tutorial-collection-documents.png)

DocumentDB 쿼리는 이미 단일 컬렉션으로 범위가 지정되었기 때문에 [FROM](documentdb-sql-query.md#FromClause) 키워드는 쿼리에서 선택 사항입니다. 따라서 "FROM Families f"를 "FROM root r" 또는 선택한 다른 변수 이름으로 교체할 수 있습니다. DocumentDB는 패밀리, 루트 또는 선택한 변수 이름이 기본적으로 현재 컬렉션을 참조하는 것으로 유추합니다.

**getFamilyDocument**에 대한 호출 아래에 코드를 복사하고 붙여넣어서 **queryCollection** 함수를 실행합니다.

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))

    // ADD THIS PART TO YOUR CODE
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

터미널에서 ```app.js``` 파일을 찾고 다음 명령을 실행합니다. ```node app.js```

축하합니다. 쿼리된 DocumentDB 문서가 성공적으로 생성되었습니다.

## <a id="ReplaceDocument"></a>9단계: 문서 바꾸기
DocumentDB는 JSON 문서 바꾸기를 지원합니다.

**replaceFamilyDocument** 함수를 복사하여 app.js 파일의 **queryCollection** 함수 아래에 붙여넣습니다.

                    }
                    console.log();
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function replaceFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Replacing document:\n${document.id}\n`);
        document.children[0].grade = 6;

        return new Promise((resolve, reject) => {
            client.replaceDocument(documentUrl, document, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

**queryCollection**에 대한 호출 아래에 코드를 복사하고 붙여넣어서 **replaceDocument** 함수를 실행합니다. 또한 **queryCollection** 을 다시 호출하는 코드를 추가하여 문서가 성공적으로 변경되었는지 확인합니다.

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

터미널에서 ```app.js``` 파일을 찾고 다음 명령을 실행합니다. ```node app.js```

축하합니다. DocumentDB 문서가 성공적으로 대체되었습니다.

## <a id="DeleteDocument"></a>10단계: 문서 삭제
DocumentDB는 JSON 문서 삭제를 지원합니다.

**deleteFamilyDocument** 함수를 복사하여 **replaceFamilyDocument** 함수 아래에 붙여넣습니다.

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function deleteFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Deleting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.deleteDocument(documentUrl, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

두 번째 **queryCollection**에 대한 호출 아래에 코드를 복사하고 붙여넣어서 **deleteDocument** 함수를 실행합니다.

    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

터미널에서 ```app.js``` 파일을 찾고 다음 명령을 실행합니다. ```node app.js```

축하합니다. DocumentDB 문서가 성공적으로 삭제되었습니다.

## <a id="DeleteDatabase"></a>11단계: 노드 데이터베이스 삭제
만든 데이터베이스를 삭제하면 데이터베이스와 모든 자식 리소스(컬렉션, 문서 등)가 제거됩니다.

**cleanup** 함수를 복사하여 **deleteFamilyDocument** 함수 아래에 붙여넣어 데이터베이스와 모든 자식 리소스를 제거합니다.

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function cleanup() {
        console.log(`Cleaning up by deleting database ${config.database.id}`);

        return new Promise((resolve, reject) => {
            client.deleteDatabase(databaseUrl, (err) => {
                if (err) reject(err)
                else resolve(null);
            });
        });
    }

**deleteFamilyDocument**에 대한 호출 아래의 코드를 복사하여 붙여넣어 **cleanup** 함수를 실행합니다.

    .then(() => deleteFamilyDocument(config.documents.Andersen))

    // ADD THIS PART TO YOUR CODE
    .then(() => cleanup())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

## <a id="Run"></a>12단계: Node.js 응용 프로그램 모두 함께 실행
함수를 호출는 시퀀스는 모두 다음과 같아야 합니다.

    getDatabase()
    .then(() => getCollection())
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

터미널에서 ```app.js``` 파일을 찾고 다음 명령을 실행합니다. ```node app.js```

시작한 앱의 출력이 표시됩니다. 출력은 아래의 예제 텍스트와 일치해야 합니다.

    Getting database:
    FamilyDB

    Getting collection:
    FamilyColl

    Getting document:
    Anderson.1

    Getting document:
    Wakefield.7

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing document:
    Anderson.1

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleting document:
    Anderson.1

    Cleaning up by deleting database FamilyDB
    Completed successfully
    Press any key to exit

축하합니다. Node.js 자습서를 만들고 완료했으며 첫 번째 DocumentDB 콘솔 응용 프로그램이 있습니다.

## <a id="GetSolution"></a>전체 Node.js 자습서 솔루션 다운로드
이 자습서의 단계를 완료할 시간이 없거나 코드를 다운로드하려는 경우 [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started)에서 가져올 수 있습니다.

이 문서의 모든 샘플을 포함하는 GetStarted 솔루션을 실행하려면 다음이 필요합니다.

* [DocumentDB 계정][documentdb-create-account].
* GitHub에서 제공하는 [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) 솔루션

npm을 통해 **documentdb** 모듈을 설치합니다. 다음 명령을 사용합니다.

* ```npm install documentdb --save```

다음으로 ```config.js``` 파일에서 [3단계: 앱의 구성 설정](#Config)에 설명한 대로 config.endpoint 및 config.authKey 값을 업데이트합니다. 

그런 다음 터미널에서 ```app.js``` 파일을 찾고 ```node app.js``` 명령을 실행합니다.

정말 간단하죠? 빌드하고 원하는 대로 진행하세요! 

## <a name="next-steps"></a>다음 단계
* 더 복잡한 Node.js 샘플을 찾으십니까? [DocumentDB를 사용하여 Node.js 응용 프로그램 빌드](documentdb-nodejs-application.md)를 참조하세요.
* [DocumentDB 계정 모니터링](documentdb-monitor-accounts.md)방법에 대해 자세히 알아봅니다.
* [쿼리 실습](https://www.documentdb.com/sql/demo)의 샘플 데이터 집합에 대해 쿼리를 실행합니다.
* [DocumentDB 설명서](https://azure.microsoft.com/documentation/services/documentdb/)페이지의 개발 섹션에서 프로그래밍 모델에 대해 자세히 알아봅니다.

[documentdb-create-account]: documentdb-create-account.md
[keys]: media/documentdb-nodejs-get-started/node-js-tutorial-keys.png

