---
title: Azure Database for MySQL 인스턴스를 Azure Spring Cloud 애플리케이션에 바인딩하는 방법
description: Azure Database for MySQL 인스턴스를 Azure Spring Cloud 애플리케이션에 바인딩하는 방법을 알아봅니다.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 0287bc8a11c359acce5fbf12d3a51e99a9f5420e
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297147"
---
# <a name="bind-an-azure-database-for-mysql-instance-to-your-azure-spring-cloud-application"></a>Azure Database for MySQL 인스턴스를 Azure Spring Cloud 애플리케이션에 바인딩 

Azure Spring Cloud를 사용하면 Spring Boot 애플리케이션을 수동으로 구성하는 대신, 선택한 Azure 서비스를 애플리케이션에 자동으로 바인딩할 수 있습니다. 이 문서에서는 애플리케이션을 Azure Database for MySQL 인스턴스에 바인딩하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

* 배포된 Azure Spring Cloud 인스턴스
* Azure Database for MySQL 계정
* Azure CLI

Azure Spring Cloud 인스턴스를 아직 배포하지 않은 경우 [빠른 시작: Azure Portal을 사용하여 Azure Spring Cloud 애플리케이션 시작](spring-cloud-quickstart.md)의 지침에 따라 첫 번째 Spring Cloud 앱을 배포합니다.

## <a name="bind-your-app-to-your-azure-database-for-mysql-instance"></a>Azure Database for MySQL 인스턴스에 앱 바인딩

1. Azure Database for MySQL 계정의 관리 사용자 이름 및 암호를 기록해 둡니다. 

1. 서버에 연결하고, MySQL 클라이언트에서 **testdb**라는 데이터베이스를 만든 다음, 관리자가 아닌 새 계정을 만듭니다.

1. 프로젝트의 *pom.xml* 파일에서 다음 종속성을 추가합니다.

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. *application.properties* 파일에서 `spring.datasource.*` 속성을 제거합니다.

1. `az spring-cloud app update` 명령을 실행하여 현재 배포를 업데이트하거나, `az spring-cloud app deployment create` 명령을 실행하여 이 변경 사항에 대한 새 배포를 만듭니다.  두 명령은 애플리케이션을 새 종속성으로 업데이트하거나 새 종속성이 있는 애플리케이션을 만듭니다.

1. Azure Portal의 **Azure Spring Cloud** 서비스 페이지에서 **애플리케이션 대시보드**를 찾은 다음, Azure Database for MySQL 인스턴스에 바인딩할 애플리케이션을 선택합니다.  이 애플리케이션은 이전 단계에서 업데이트하거나 배포한 것과 동일한 애플리케이션입니다. 

1. **서비스 바인딩**을 선택한 다음, **서비스 바인딩 만들기** 단추를 선택합니다. 

1. 양식을 작성하고, **바인딩 유형**으로 **Azure MySQL**을 선택하고, 이전에 사용한 것과 동일한 데이터베이스 이름과 첫 번째 단계에서 적어둔 사용자 이름 및 암호를 사용합니다.

1. 앱을 다시 시작하면 이 바인딩이 작동합니다.

1. 서비스 바인딩이 올바른지 확인하려면 바인딩 이름을 선택하고 해당 세부 정보를 확인합니다. `property` 필드는 다음과 같이 표시되어야 합니다.
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Spring Cloud 애플리케이션을 Azure Database for MySQL 인스턴스에 바인딩하는 방법을 알아보았습니다. 서비스를 애플리케이션에 바인딩하는 방법에 대한 자세한 내용은 [Azure Cosmos DB 데이터베이스를 Azure Spring Cloud 애플리케이션에 바인딩](spring-cloud-tutorial-bind-cosmos.md)을 참조하세요.
