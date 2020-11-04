---
title: '빠른 시작: Cassandra API와 Python - Azure Cosmos DB'
description: 이 빠른 시작은 Python과 함께 Azure Cosmos DB의 Apache Cassandra API를 사용하여 프로필 애플리케이션을 만드는 방법을 보여 줍니다.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: python
ms.topic: quickstart
ms.date: 08/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: 8a76a37de64733ba3e0126c7885d8690d2d83c1b
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099725"
---
# <a name="quickstart-build-a-cassandra-app-with-python-sdk-and-azure-cosmos-db"></a>빠른 시작: Python SDK 및 Azure Cosmos DB를 사용하여 Cassandra 앱 빌드
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.JS](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

이 빠른 시작에서는 Azure Cosmos DB Cassandra API 계정을 만들고 GitHub에서 복제된 Cassandra Python 앱을 사용하여 Cassandra 데이터베이스 및 컨테이너를 만듭니다. Azure Cosmos DB는 글로벌 배포 및 수평적 크기 조정 기능을 사용하여 문서, 테이블, 키 값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있는 다중 모델 데이터베이스 서비스입니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 또는 Azure 구독 없이 [무료로 Azure Cosmos DB를 사용](https://azure.microsoft.com/try/cosmosdb/)할 수 있습니다.
- [Python 2.7.14 이상 또는 3.4 이상](https://www.python.org/downloads/).
- [Git](https://git-scm.com/downloads)
- [Apache Cassandra용 Python 드라이버](https://github.com/datastax/python-driver).

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

문서 데이터베이스를 만들려면 Azure Cosmos DB로 Cassandra 계정을 만들어야 합니다.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>샘플 애플리케이션 복제

이제 GitHub에서 Cassandra API 앱을 복제하고 연결 문자열을 설정한 다음, 실행해 보겠습니다. 프로그래밍 방식으로 데이터를 사용하여 얼마나 쉽게 작업할 수 있는지 알게 될 것입니다. 

1. 명령 프롬프트를 엽니다. 이름이 `git-samples`인 새 폴더를 만듭니다. 그런 다음, 명령 프롬프트를 닫습니다.

    ```bash
    md "C:\git-samples"
    ```

2. Git Bash와 같은 Git 터미널 창을 열고, `cd` 명령을 사용하여 샘플 앱을 설치할 새 폴더로 변경합니다.

    ```bash
    cd "C:\git-samples"
    ```

3. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 이 명령은 컴퓨터에서 샘플 앱의 복사본을 만듭니다.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-python-getting-started.git
    ```

## <a name="review-the-code"></a>코드 검토

이 단계는 선택 사항입니다. 코드로 데이터베이스 리소스를 만드는 방법을 알아보려는 경우 다음 코드 조각을 검토할 수 있습니다. 코드 조각은 모두 *pyquickstart.py* 파일에서 가져옵니다. 그렇지 않으면 [연결 문자열 업데이트](#update-your-connection-string)로 건너뛸 수 있습니다. 

* `cluster`는 Azure Portal에서 검색된 `contactPoint` 및 `port` 정보를 사용하여 초기화됩니다. 그런 다음, `cluster`는 `connect()` 메서드를 사용하여 Azure Cosmos DB Cassandra API에 연결됩니다. 인증된 연결은 사용자 이름, 암호 및 기본 인증서 또는 구성 파일 내에서 제공하는 경우 명시적 인증서를 사용하여 설정됩니다.

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="authenticateAndConnect":::

* 새 keyspace가 생성됩니다.

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="createKeyspace":::

* 새 테이블이 생성됩니다.

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="createTable":::

* 키/값 엔터티가 삽입됩니다.

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="insertData":::

* 모든 키 값을 가져오는 쿼리입니다.

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="queryAllItems":::
    
* 키 값을 가져오는 쿼리입니다.

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="queryByID":::

## <a name="update-your-connection-string"></a>연결 문자열 업데이트

이제 Azure Portal로 다시 이동하여 연결 문자열 정보를 가져와서 앱에 복사합니다. 연결 문자열을 통해 앱이 호스트된 데이터베이스와 통신할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)의 Azure Cosmos DB 계정에서 **연결 문자열** 을 선택합니다. 

1. 화면 오른쪽에 있는 :::image type="icon" source="./media/create-cassandra-python/copy.png"::: 단추를 사용하여 상위 값인 CONTACT POINT 값을 복사합니다. 

   :::image type="content" source="./media/create-cassandra-python/keys.png" alt-text="Azure Portal, 연결 문자열 블레이드에서 액세스 사용자 이름, 암호 및 접점 보기 및 복사":::

1. *config.py* 파일을 엽니다. 

1. 포털의 CONTACT POINT 값을 줄 10의 `<FILLME>`에 붙여넣습니다.

    줄 10은 다음과 같이 보일 것입니다. 

    `'contactPoint': 'cosmos-db-quickstarts.cassandra.cosmosdb.azure.com:10350'`

1. 포털의 USERNAME 값을 복사하여 줄 6의 `<FILLME>`에 붙여넣습니다.

    줄 6은 다음과 같이 보일 것입니다. 

    `'username': 'cosmos-db-quickstart',`
    
1. 포털의 PASSWORD 값을 복사하여 줄 8의 `<FILLME>`에 붙여넣습니다.

    줄 8은 다음과 같이 보일 것입니다.

    `'password' = '2Ggkr662ifxz2Mg==`';`

1. *config.py* 파일을 저장합니다.
    
## <a name="use-the-x509-certificate"></a>X509 인증서 사용

1. [https://cacert.omniroot.com/bc2025.crt](https://cacert.omniroot.com/bc2025.crt)에서 로컬로 Baltimore CyberTrust Root 인증서를 다운로드합니다. 파일 확장명 *.cer* 를 사용하여 파일 이름을 바꿉니다.

   인증서에 일련 번호 `02:00:00:b9` 및 SHA1 지문 `d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`가 있습니다.

2. *pyquickstart.py* 를 열고 새 인증서를 가리키도록 `path\to\cert`를 변경합니다.

3. *pyquickstart.py* 를 저장합니다.

## <a name="run-the-python-app"></a>Python 앱 실행

1. Git 터미널에서 cd 명령을 사용하여 `azure-cosmos-db-cassandra-python-getting-started` 폴더로 변경합니다. 

2. 다음 명령을 실행하여 필요한 모듈을 설치합니다.

    ```python
    python -m pip install cassandra-driver
    python -m pip install prettytable
    python -m pip install requests
    python -m pip install pyopenssl
    ```

2. 다음 명령을 실행하여 Python 애플리케이션을 시작합니다.

    ```
    python pyquickstart.py
    ```

3. 명령줄에서 예상대로 결과가 나타나는지 확인하세요.

    프로그램 실행을 중지하고 콘솔 창을 닫으려면 CTRL+C를 누릅니다. 

    :::image type="content" source="./media/create-cassandra-python/output.png" alt-text="출력 보기 및 확인":::
    
4. Azure Portal에서 **데이터 탐색기** 를 열어 이 새 데이터를 쿼리/수정/사용합니다. 

    :::image type="content" source="./media/create-cassandra-python/data-explorer.png" alt-text="데이터 탐색기에서 데이터 보기":::

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal에서 SLA 검토

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Cassandra API를 사용하여 Azure Cosmos DB 계정을 만들고 Cassandra 데이터베이스 및 컨테이너를 만드는 Cassandra Python 앱을 실행하는 방법을 알아보았습니다. 이제 Azure Cosmos DB 계정으로 추가 데이터를 가져올 수 있습니다. 

> [!div class="nextstepaction"]
> [Azure Cosmos DB로 Cassandra 데이터 가져오기](cassandra-import-data.md)

