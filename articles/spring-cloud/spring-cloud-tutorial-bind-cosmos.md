---
title: Azure Cosmos DB를 Azure Spring Cloud 애플리케이션에 바인딩 | Microsoft Docs
description: Azure Cosmos DB를 Azure Spring Cloud 애플리케이션에 바인딩하는 방법 알아보기
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 76a2b9f305f041a19b8d7ace8234a804825f6a0e
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607125"
---
# <a name="tutorial-bind-an-azure-cosmos-db-to-your-azure-spring-cloud-application"></a>자습서: Azure Cosmos DB를 Azure Spring Cloud 애플리케이션에 바인딩

Azure Spring Cloud를 사용하면 Spring Boot 애플리케이션을 수동으로 구성하는 대신 선택한 Azure 서비스를 사용자 애플리케이션에 자동으로 바인딩할 수 있습니다. 이 문서에서는 Azure Cosmos DB에 애플리케이션을 바인딩하는 방법을 보여줍니다.

필수 조건:
* 배포된 Azure Spring Cloud 인스턴스.  [빠른 시작](spring-cloud-quickstart-launch-app-cli.md)을 따라 시작하세요.
* 최소 수준의 기여자 권한이 있는 Azure Cosmos DB 계정

## <a name="bind-azure-cosmos-db"></a>Azure Cosmos DB 바인딩

Azure Cosmos DB에는 바인딩을 지원하는 다음 5가지 API 형식이 있습니다.

1. Azure Cosmos DB 데이터베이스를 만듭니다. 데이터베이스 만들기에 대한 도움말은 [이 문서를 참조](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal)하세요. 데이터베이스의 이름을 기록합니다. 우리 데이터베이스의 이름은 `testdb`입니다.

1. API 형식에 따라 Spring Cloud 애플리케이션의 `pom.xml`에 다음 종속성 중 하나를 추가합니다.
    
    #### <a name="api-type-core-sql"></a>API 형식: Core(SQL)

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
        <version>2.1.6</version>
    </dependency>
    ```
    
    #### <a name="api-type-mongodb"></a>API 형식: MongoDB

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-mongodb</artifactId>
    </dependency>
    ```

    #### <a name="api-type-cassandra"></a>API 형식: Cassandra

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-cassandra</artifactId>
    </dependency>
    ```

    #### <a name="api-type-gremlin-graph"></a>API 형식: Gremlin(그래프)

    ```xml
    <dependency>
        <groupId>com.microsoft.spring.data.gremlin</groupId>
        <artifactId>spring-data-gremlin</artifactId>
        <version>2.1.7</version>
    </dependency>
    ```

    #### <a name="api-type-azure-table"></a>API 형식: Azure 테이블

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-storage-spring-boot-starter</artifactId>
        <version>2.0.5</version>
    </dependency>
    ```

1. `az spring-cloud app update`를 사용하여 현재 배포를 업데이트하거나 `az spring-cloud app deployment create`를 사용하여 이 변경 사항에 대한 새 배포를 만듭니다.  이러한 명령은 새 종속성이 있는 애플리케이션을 업데이트하거나 만듭니다.

1. Azure Portal에서 Azure Spring Cloud 서비스 페이지로 이동합니다. 이는 이전 단계에서 업데이트하거나 배포한 동일한 애플리케이션입니다. **애플리케이션 대시보드**를 찾아 Cosmos DB에 바인딩할 애플리케이션을 선택합니다. 다음으로, `Service binding`을 선택하고 `Create service binding` 단추를 선택합니다. **바인딩 형식** `Azure Cosmos DB`, API 형식, 데이터베이스 이름 및 Azure Cosmos DB 계정을 선택하여 양식을 작성합니다.

    > [!NOTE]
    > Cassandra를 사용하는 경우 데이터베이스 이름에 대한 키 공간을 사용합니다.

1. 애플리케이션 페이지에서 **다시 시작** 단추를 선택하여 애플리케이션을 다시 시작합니다.

1. 서비스가 올바르게 바인딩되었는지 확인하려면 바인딩 이름을 선택하고 세부 정보를 확인합니다. `property` 필드는 다음과 비슷합니다.

    ```
    azure.cosmosdb.uri=https:/<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Spring Cloud 애플리케이션을 CosmosDB에 바인딩하는 방법에 대해 알아보았습니다.  애플리케이션을 Azure Redis Cache에 바인딩하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Spring Cloud 애플리케이션을 Azure Redis Cache에 바인딩](spring-cloud-tutorial-bind-redis.md)
