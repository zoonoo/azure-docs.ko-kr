---
title: Azure 스프링 클라우드 응용 프로그램에 Azure Cosmos DB 바인딩 | Microsoft Docs
description: Azure 스프링 클라우드 응용 프로그램에 Azure Cosmos DB를 바인딩하는 방법에 대해 알아봅니다.
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: d051fd431180e9cb86f1df4642fb5e947103c007
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038731"
---
# <a name="tutorial-bind-an-azure-cosmos-db-to-your-azure-spring-cloud-application"></a>자습서: Azure 스프링 클라우드 응용 프로그램에 Azure Cosmos DB 바인딩

Azure 스프링 클라우드를 사용 하면 스프링 부팅 응용 프로그램을 수동으로 구성 하는 대신 응용 프로그램에 자동으로 선택 하는 Azure 서비스를 바인딩할 수 있습니다. 이 문서에서는 응용 프로그램을 Azure Cosmos DB에 바인딩하는 방법을 보여 줍니다.

필수 조건:
* 배포 된 Azure 스프링 클라우드 인스턴스입니다.  시작 하려면 [빠른](spring-cloud-quickstart-launch-app-cli.md) 시작을 따르세요.
* 최소 권한 수준의 참여자 인 Azure Cosmos DB 계정

## <a name="bind-azure-cosmos-db"></a>바인딩 Azure Cosmos DB

Azure Cosmos DB에는 바인딩을 지 원하는 5 가지 API 형식이 있습니다.

1. Azure Cosmos DB 데이터베이스를 만듭니다. 데이터베이스 만들기에 대 한 도움말은 [이 문서를 참조 하세요](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal) . 데이터베이스의 이름을 기록 합니다. 우리 이름은 `testdb`입니다.

1. 사용자의 API 유형에 따라 스프링 클라우드 응용 프로그램의 `pom.xml`에서 다음 종속성 중 하나를 추가 합니다.
    
    #### <a name="api-type-core-sql"></a>API 유형: Core(SQL)

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
        <version>2.1.6</version>
    </dependency>
    ```
    
    #### <a name="api-type-mongodb"></a>API 유형: MongoDB

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-mongodb</artifactId>
    </dependency>
    ```

    #### <a name="api-type-cassandra"></a>API 유형: Cassandra

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-cassandra</artifactId>
    </dependency>
    ```

    #### <a name="api-type-gremlin-graph"></a>API 유형: Gremlin(그래프)

    ```xml
    <dependency>
        <groupId>com.microsoft.spring.data.gremlin</groupId>
        <artifactId>spring-data-gremlin</artifactId>
        <version>2.1.7</version>
    </dependency>
    ```

    #### <a name="api-type-azure-table"></a>API 유형: Azure 테이블

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-storage-spring-boot-starter</artifactId>
        <version>2.0.5</version>
    </dependency>
    ```

1. @No__t-0을 사용 하 여 현재 배포를 업데이트 하거나 `az spring-cloud app deployment create`을 사용 하 여이 변경에 대 한 새 배포를 만듭니다.  이러한 명령은 새 종속성이 있는 응용 프로그램을 업데이트 하거나 만듭니다.

1. Azure Portal의 Azure 스프링 클라우드 서비스 페이지로 이동 합니다. 이전 단계에서 업데이트 하거나 배포한 동일한 응용 프로그램입니다. **응용 프로그램 대시보드** 를 찾고 Cosmos DB 바인딩할 응용 프로그램을 선택 합니다. 그런 다음 `Service binding`을 선택 하 고 `Create service binding` 단추를 선택 합니다. 양식을 작성 하 고, **바인딩 유형** `Azure Cosmos DB`, API 유형, 데이터베이스 이름 및 Azure Cosmos DB 계정을 선택 합니다.

    > [!NOTE]
    > Cassandra를 사용 하는 경우 데이터베이스 이름에 대 한 키 공간을 사용 합니다.

1. 응용 프로그램 페이지에서 **다시 시작** 단추를 선택 하 여 응용 프로그램을 다시 시작 합니다.

1. 서비스가 올바르게 바인딩되어 있는지 확인 하려면 바인딩 이름을 선택 하 고 세부 정보를 확인 합니다. @No__t-0 필드는 다음과 비슷해야 합니다.

    ```
    azure.cosmosdb.uri=https:/<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure 스프링 클라우드 응용 프로그램을 CosmosDB에 바인딩하는 방법에 대해 알아보았습니다.  응용 프로그램을 Azure Redis Cache에 바인딩하는 방법을 알아보려면 다음 자습서를 계속 진행 합니다.

> [!div class="nextstepaction"]
> [Azure 스프링 클라우드 응용 프로그램을 Azure Redis Cache에 바인딩합니다](spring-cloud-tutorial-bind-redis.md).
