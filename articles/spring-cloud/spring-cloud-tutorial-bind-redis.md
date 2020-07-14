---
title: Azure Cache for Redis를 Azure Spring Cloud 애플리케이션에 바인딩
description: Azure Cache for Redis를 Azure Spring Cloud 애플리케이션에 바인딩하는 방법 알아보기
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: ddf239fc6415b3dbee6051d4199cd29e5244e4d2
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86142141"
---
# <a name="bind-azure-cache-for-redis-to-your-azure-spring-cloud-application"></a>Azure Cache for Redis를 Azure Spring Cloud 애플리케이션에 바인딩 

Spring Boot 애플리케이션을 수동으로 구성하는 대신 Azure Spring Cloud를 사용하여 선택한 Azure 서비스를 사용자 애플리케이션에 자동으로 바인딩할 수 있습니다. 이 문서에서는 Azure Cache for Redis에 애플리케이션을 바인딩하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

* 배포된 Azure Spring Cloud 인스턴스
* Azure Cache for Redis 서비스 인스턴스
* Azure CLI용 Azure Spring Cloud 확장

배포된 Azure Spring Cloud 인스턴스가 없는 경우 [Azure Spring Cloud 앱 배포에 대한 빠른 시작](spring-cloud-quickstart-launch-app-portal.md)의 단계를 따릅니다.

## <a name="bind-azure-cache-for-redis"></a>Azure Cache for Redis 바인딩

1. 프로젝트의 pom.xml 파일에 다음 종속성을 추가합니다.

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. `application.properties` 파일에서 `spring.redis.*` 속성을 제거합니다.

1. `az spring-cloud app update`를 사용하여 현재 배포를 업데이트하거나 `az spring-cloud app deployment create`를 사용하여 새 배포를 만듭니다.

1. Azure Portal에서 Azure Spring Cloud 서비스 페이지로 이동합니다. **Application Dashboard**로 이동하여 Azure Cache for Redis에 바인딩할 애플리케이션을 선택합니다. 이 애플리케이션은 이전 단계에서 업데이트하거나 배포한 애플리케이션과 동일합니다.

1. **서비스 바인딩**을 선택하고 **서비스 바인딩 만들기**를 선택합니다. **바인딩 유형** 값 **Azure Cache for Redis**, 사용자의 Azure Cache for Redis 서버 및 **기본** 키 옵션을 선택하도록 양식을 작성합니다.

1. 앱을 다시 시작합니다. 이제 바인딩이 작동합니다.

1. 서비스 바인딩이 올바른지 확인하려면 바인딩 이름을 선택하고 해당 세부 정보를 확인합니다. `property` 필드는 다음과 같이 표시되어야 합니다.
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Spring Cloud 애플리케이션을 Azure Cache for Redis에 바인딩하는 방법을 알아보았습니다. 서비스를 애플리케이션에 바인딩하는 방법에 대한 자세한 내용은 [Azure Database for MySQL 인스턴스에 바인딩](spring-cloud-tutorial-bind-mysql.md)을 참조하세요.
