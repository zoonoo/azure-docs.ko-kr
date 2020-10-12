---
title: Studio 3T를 사용 하 여 MongoDB에 대 한 Azure Cosmos DB의 API에 연결
description: Studio 3T를 사용 하 여 Azure Cosmos DB의 MongoDB API에 연결 하는 방법을 알아봅니다.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18
ms.openlocfilehash: 18a9f97d2cee9dd17345a1c8c0ae0efe442d79a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85799397"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Studio 3T를 사용 하 여 Azure Cosmos 계정에 연결

Studio 3T를 사용 하 여 MongoDB에 대 한 Azure Cosmos DB API에 연결 하려면 다음을 수행 해야 합니다.

* [Studio 3T](https://studio3t.com/)를 다운로드 하 여 설치 합니다.
* Azure Cosmos 계정의 [연결 문자열](connect-mongodb-account.md) 정보를 포함 합니다.

## <a name="create-the-connection-in-studio-3t"></a>Studio 3T에서 연결 만들기

Studio 3T 연결 관리자에 Azure Cosmos 계정을 추가 하려면 다음 단계를 사용 합니다.

1. [Azure Cosmos DB에 MongoDB 응용 프로그램 연결](connect-mongodb-account.md) 문서에 있는 지침을 사용 하 여 Azure Cosmos DB의 MongoDB API 계정에 대 한 연결 정보를 검색 합니다.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionStringBlade.png" alt-text="연결 문자열 페이지의 스크린샷":::

2. **연결**을 클릭하여 연결 관리자를 열려고 **새 연결**을 클릭합니다.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManager.png" alt-text="연결 문자열 페이지의 스크린샷":::
3. **새 연결** 창의 **서버** 탭에서 Azure COSMOS 계정의 호스트 (FQDN) 및 포트를 입력 합니다.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerServerTab.png" alt-text="연결 문자열 페이지의 스크린샷":::
4. **새 연결** 창의 **인증** 탭에서 **기본(MONGODB CR 또는 SCARM-SHA-1)** 인증 모드를 선택하고, 사용자 이름 및 암호를 입력합니다.  기본 인증 DB(관리자)를 그대로 사용하거나 고유한 값을 지정합니다.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png" alt-text="연결 문자열 페이지의 스크린샷":::
5. **새 연결** 창의 **SSL** 탭에서 **SSL 프로토콜을 사용하여 연결** 확인란 및 **서버 자체 서명된 SSL 인증서 허용** 라디오 단추를 확인합니다.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerSSLTab.png" alt-text="연결 문자열 페이지의 스크린샷":::
6. **연결 테스트** 단추를 클릭하여 연결 정보의 유효성을 검사하고 **확인**을 클릭하여 새 연결 창으로 돌아간 다음 **저장**을 클릭합니다.

    :::image type="content" source="./media/mongodb-mongochef/TestConnectionResults.png" alt-text="연결 문자열 페이지의 스크린샷":::

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Studio 3T를 사용하여 데이터베이스, 컬렉션 및 문서 만들기
Studio 3T를 사용하여 데이터베이스, 컬렉션 및 문서를 만들려면 다음 단계를 수행합니다.

1. **연결 관리자**에서 연결을 강조 표시하고 **연결**을 클릭합니다.

    :::image type="content" source="./media/mongodb-mongochef/ConnectToAccount.png" alt-text="연결 문자열 페이지의 스크린샷":::
2. 호스트를 마우스 오른쪽 단추로 클릭하고 **데이터베이스 추가**를 선택합니다.  데이터베이스 이름을 지정하고 **확인**을 클릭합니다.

    :::image type="content" source="./media/mongodb-mongochef/AddDatabase1.png" alt-text="연결 문자열 페이지의 스크린샷":::
3. 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **컬렉션 추가**를 선택합니다.  컬렉션 이름을 지정하고 **만들기**를 클릭합니다.

    :::image type="content" source="./media/mongodb-mongochef/AddCollection.png" alt-text="연결 문자열 페이지의 스크린샷":::
4. **컬렉션** 메뉴 항목을 클릭한 다음 **문서 추가**를 클릭합니다.

    :::image type="content" source="./media/mongodb-mongochef/AddDocument1.png" alt-text="연결 문자열 페이지의 스크린샷" },
        "isRegistered": false
    }
    ```

7. 샘플 쿼리를 실행합니다. 예를 들어 'Andersen' 성을 가진 가족을 검색하고 부모 및 상태 필드를 반환합니다.

    :::image type="content" source="./media/mongodb-mongochef/QueryDocument1.png" alt-text="연결 문자열 페이지의 스크린샷":::

## <a name="next-steps"></a>다음 단계

- Azure Cosmos DB의 API for MongoDB와 함께 [Robo 3T를 사용](mongodb-robomongo.md)하는 방법을 알아봅니다.
- Azure Cosmos DB의 API for MongoDB를 사용하여 MongoDB [샘플](mongodb-samples.md)을 살펴봅니다.
