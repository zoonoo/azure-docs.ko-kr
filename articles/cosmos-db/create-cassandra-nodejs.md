---
title: '빠른 시작: Cassandra API와 Node.js - Azure Cosmos DB'
description: 이 빠른 시작은 Node.js와 함께 Azure Cosmos DB Cassandra API를 사용하여 프로필 애플리케이션을 만드는 방법을 보여줍니다.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: devx-track-js
ms.openlocfilehash: b9e036df91eecadc701664a19905a92c142b7585
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591901"
---
# <a name="quickstart-build-a-cassandra-app-with-nodejs-sdk-and-azure-cosmos-db"></a>빠른 시작: Node.js SDK 및 Azure Cosmos DB를 사용하여 Cassandra 앱 빌드
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.JS](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

이 빠른 시작에서는 Azure Cosmos DB Cassandra API 계정을 만들고 GitHub에서 복제된 Cassandra Node.js 앱을 사용하여 Cassandra 데이터베이스 및 컨테이너를 만듭니다. Azure Cosmos DB는 글로벌 배포 및 수평적 크기 조정 기능을 사용하여 문서, 테이블, 키 값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있는 다중 모델 데이터베이스 서비스입니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 또는 Azure 구독, 요금 및 약정 없이 [Azure Cosmos DB 평가판](https://azure.microsoft.com/try/cosmosdb/)을 사용할 수 있습니다.

또한 다음 항목도 필요합니다.
* [Node.js](https://nodejs.org/dist/v0.10.29/x64/node-v0.10.29-x64.msi) 버전 v0.10.29 이상
* [Git](https://git-scm.com/)

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

문서 데이터베이스를 만들려면 Azure Cosmos DB로 Cassandra 계정을 만들어야 합니다.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>샘플 애플리케이션 복제

이제 GitHub에서 Cassandra API 앱을 복제하고 연결 문자열을 설정한 다음, 실행해 보겠습니다. 프로그래밍 방식으로 데이터를 사용하여 얼마나 쉽게 작업할 수 있는지 알게 될 것입니다. 

1. 명령 프롬프트를 엽니다. 이름이 `git-samples`인 새 폴더를 만듭니다. 그런 다음, 명령 프롬프트를 닫습니다.

    ```bash
    md "C:\git-samples"
    ```

2. git bash와 같은 git 터미널 창을 엽니다. `cd` 명령을 사용하여 샘플 앱을 설치할 새 폴더로 변경합니다.

    ```bash
    cd "C:\git-samples"
    ```

3. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 이 명령은 컴퓨터에서 샘플 앱의 복사본을 만듭니다.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>코드 검토

이 단계는 선택 사항입니다. 코드로 데이터베이스 리소스를 만드는 방법을 알아보려는 경우 다음 코드 조각을 검토할 수 있습니다. 이 코드 조각은 모두 `C:\git-samples\azure-cosmos-db-cassandra-nodejs-getting-started` 폴더에 있는 `uprofile.js` 파일에서 가져온 것입니다. 그렇지 않으면 [연결 문자열 업데이트](#update-your-connection-string)로 건너뛸 수 있습니다. 

* 사용자 이름 및 암호 값은 Azure Portal의 연결 문자열 페이지를 사용하여 설정되었습니다. `path\to\cert`는 X509 인증서의 경로를 제공합니다. 

   ```javascript
   var ssl_option = {
        cert : fs.readFileSync("path\to\cert"),
        rejectUnauthorized : true,
        secureProtocol: 'TLSv1_2_method'
        };
   const authProviderLocalCassandra = new cassandra.auth.PlainTextAuthProvider(config.username, config.password);
   ```

* `client`는 contactPoint 정보를 사용하여 초기화됩니다. contactPoint는 Azure Portal에서 검색됩니다.

    ```javascript
    const client = new cassandra.Client({contactPoints: [config.contactPoint], authProvider: authProviderLocalCassandra, sslOptions:ssl_option});
    ```

* `client`는 Azure Cosmos DB Cassandra API에 연결됩니다.

    ```javascript
    client.connect(next);
    ```

* 새 keyspace가 생성됩니다.

    ```javascript
    function createKeyspace(next) {
        var query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {\'class\': \'NetworkTopologyStrategy\', \'datacenter1\' : \'1\' }";
        client.execute(query, next);
        console.log("created keyspace");    
  }
    ```

* 새 테이블이 생성됩니다.

   ```javascript
   function createTable(next) {
       var query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        client.execute(query, next);
        console.log("created table");
   },
   ```

* 키/값 엔터티가 삽입됩니다.

    ```javascript
        function insert(next) {
            console.log("\insert");
            const arr = ['INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (1, \'AdrianaS\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (2, \'JiriK\', \'Toronto\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (3, \'IvanH\', \'Mumbai\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (4, \'IvanH\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (5, \'IvanaV\', \'Belgaum\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (6, \'LiliyaB\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (7, \'JindrichH\', \'Buenos Aires\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (8, \'AdrianaS\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (9, \'JozefM\', \'Seattle\')'];
            arr.forEach(element => {
            client.execute(element);
            });
            next();
        },
    ```

* 모든 키 값을 가져오는 쿼리입니다.

    ```javascript
        function selectAll(next) {
            console.log("\Select ALL");
            var query = 'SELECT * FROM uprofile.user';
            client.execute(query, function (err, result) {
            if (err) return next(err);
            result.rows.forEach(function(row) {
                console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
            }, this);
            next();
            });
        },
    ```  

* 키 값을 가져오는 쿼리입니다.

    ```javascript
        function selectById(next) {
            console.log("\Getting by id");
            var query = 'SELECT * FROM uprofile.user where user_id=1';
            client.execute(query, function (err, result) {
            if (err) return next(err);
            result.rows.forEach(function(row) {
                console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
            }, this);
            next();
            });
        }
    ```  

## <a name="update-your-connection-string"></a>연결 문자열 업데이트

이제 Azure Portal로 다시 이동하여 연결 문자열 정보를 가져와서 앱에 복사합니다. 연결 문자열을 통해 앱이 호스트된 데이터베이스와 통신할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)의 Azure Cosmos DB 계정에서 **연결 문자열** 을 선택합니다. 

1. 화면 오른쪽에 있는 :::image type="icon" source="./media/create-cassandra-nodejs/copy.png"::: 단추를 사용하여 상위 값인 CONTACT POINT 값을 복사합니다.

    :::image type="content" source="./media/create-cassandra-nodejs/keys.png" alt-text="Azure Portal, 연결 문자열 페이지에서 CONTACT POINT, USERNAME 및 PASSWORD를 보고 복사":::

1. `config.js` 파일을 엽니다. 

1. 포털의 CONTACT POINT 값을 줄 4의 `<FillMEIN>`에 붙여넣습니다.

    이제 줄 4는 다음과 같이 보일 것입니다. 

    `config.contactPoint = "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com:10350"`

1. 포털의 USERNAME 값을 복사하여 줄 2의 `<FillMEIN>`에 붙여넣습니다.

    줄 2는 다음과 같이 보일 것입니다. 

    `config.username = 'cosmos-db-quickstart';`

1. 포털의 PASSWORD 값을 복사하여 줄 3의 `<FillMEIN>`에 붙여넣습니다.

    줄 3은 다음과 같이 보일 것입니다.

    `config.password = '2Ggkr662ifxz2Mg==';`

1. `config.js` 파일을 저장합니다.

## <a name="use-the-x509-certificate"></a>X509 인증서 사용

1. [https://cacert.omniroot.com/bc2025.crt](https://cacert.omniroot.com/bc2025.crt)에서 로컬로 Baltimore CyberTrust Root 인증서를 다운로드합니다. 파일 확장명 `.cer`을 사용하여 파일의 이름을 바꿉니다.

   인증서에 일련 번호 `02:00:00:b9` 및 SHA1 지문 `d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`가 있습니다.

2. `uprofile.js`를 열고 새 인증서를 가리키도록 `path\to\cert`를 변경합니다.

3. `uprofile.js`를 저장합니다.

> [!NOTE]
> 이후 단계에서 인증서 관련 오류가 발생하고 Windows 머신에서 실행되고 있는 경우 .crt 파일을 아래의 Microsoft .cer 형식으로 올바르게 변환하는 프로세스를 수행했는지 확인합니다.
> 
> .crt 파일을 두 번 클릭하여 인증서 표시로 엽니다. 
>
> :::image type="content" source="./media/create-cassandra-nodejs/crtcer1.gif" alt-text="인증서 창을 보여주는 스크린샷.":::
>
> 인증서 마법사에서 다음을 누릅니다. Base-64로 인코딩된 X.509(.CER)를 선택한 후, 다음을 선택합니다.
>
> :::image type="content" source="./media/create-cassandra-nodejs/crtcer2.gif" alt-text="Base-64로 인코딩된 X.509(.CER) 옵션을 보여주는 스크린샷.":::
>
> 찾아보기를 선택하여 대상을 찾고 파일 이름을 입력합니다.
> 다음을 선택한 다음, 완료를 선택합니다.
>
> 이제 올바른 형식의 .cer 파일이 있어야 합니다. `uprofile.js`의 경로가 이 파일을 가리키는지 확인합니다.

## <a name="run-the-nodejs-app"></a>Node.js 앱 실행

1. git 터미널 창에서 이전에 복제한 샘플 디렉터리에 있는지 확인합니다.

    ```bash
    cd azure-cosmos-db-cassandra-nodejs-getting-started
    ```

2. `npm install`을 실행하여 필요한 npm 모듈을 설치합니다.

3. `node uprofile.js`를 실행하여 노드 애플리케이션을 시작합니다.

4. 명령줄에서 예상대로 결과가 나타나는지 확인하세요.

    :::image type="content" source="./media/create-cassandra-nodejs/output.png" alt-text="출력 보기 및 확인":::

    프로그램 실행을 중지하고 콘솔 창을 닫으려면 CTRL+C를 누릅니다. 

5. Azure Portal에서 **데이터 탐색기** 를 열어 이 새 데이터를 쿼리/수정/사용합니다. 

    :::image type="content" source="./media/create-cassandra-nodejs/data-explorer.png" alt-text="데이터 탐색기에서 데이터 보기"::: 

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal에서 SLA 검토

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Cassandra API를 사용하여 Azure Cosmos DB 계정을 만들고 Cassandra 데이터베이스 및 컨테이너를 만드는 Cassandra Node.js 앱을 실행하는 방법을 알아보았습니다. 이제 Azure Cosmos DB 계정으로 추가 데이터를 가져올 수 있습니다. 

> [!div class="nextstepaction"]
> [Azure Cosmos DB로 Cassandra 데이터 가져오기](cassandra-import-data.md)