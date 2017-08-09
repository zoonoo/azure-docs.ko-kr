---
title: "Redis Cache를 사용하도록 Spring Boot Initializer 앱을 구성하는 방법"
description: "Azure Redis Cache를 사용하도록 Spring Boot Initializer를 사용하여 만든 응용 프로그램을 구성하는 방법에 대해 알아봅니다."
services: redis-cache
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
keywords: Spring, Spring Boot Starter, Redis Cache
ms.assetid: 
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: java
ms.topic: article
ms.date: 7/21/2017
ms.author: robmcm;zhijzhao;yidon
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: ea85a9cfe7079ade33a437987798a165a056dc02
ms.contentlocale: ko-kr
ms.lasthandoff: 07/28/2017

---

# <a name="how-to-configure-a-spring-boot-initializer-app-to-use-redis-cache"></a>Redis Cache를 사용하도록 Spring Boot Initializer 앱을 구성하는 방법

## <a name="overview"></a>개요

**[Spring Framework]**는 Java 개발자가 엔터프라이즈 수준의 응용 프로그램을 만드는 데 도움이 되는 오픈 소스 솔루션입니다. 해당 플랫폼 맨 위에 빌드되는 인기 있는 프로젝트 중 하나가 [Spring Boot]입니다. 이 프로젝트는 독립 실행형 Java 응용 프로그램을 만들기 위한 간단한 방법을 제공합니다. Spring Boot을 시작하는 개발자를 도우려면 <https://github.com/spring-guides/>에서 몇 가지 샘플 Spring Boot 패키지를 사용할 있습니다. 기본 Spring Boot 프로젝트 목록에서 선택하는 것 외에도 **[Spring Initializr]**를 통해 사용자 지정 Spring Boot 응용 프로그램을 만들기 시작하는 개발자에게 도움을 줍니다.

이 문서에서는 Azure Portal을 사용하여 Redis Cache를 만들고, **Spring Initializr**를 사용하여 사용자 지정 응용 프로그램을 만든 다음 Redis Cache를 사용하여 데이터를 저장하고 검색하는 Java 웹 응용 프로그램을 만드는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

이 문서의 단계를 수행하기 위해 다음 필수 구성 요소가 필요합니다.

* Azure 구독. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택]을 활성화하거나 [무료 Azure 계정]에 등록할 수 있습니다.

* [JDK(Java Development Kit)](http://www.oracle.com/technetwork/java/javase/downloads/), 버전 1.7 이상

* [Apache Maven](http://maven.apache.org/), 버전 3.0 이상

## <a name="create-a-redis-cache-on-azure"></a>Azure에 Redis 캐시 만들기

1. <https://portal.azure.com/>의 Azure Portal로 이동하고 **+새로 만들기**의 항목을 클릭합니다.

   ![Azure 포털][AZ01]

1. **데이터베이스**를 클릭하고 **Redis Cache**를 클릭합니다.

   ![Azure 포털][AZ02]

1. **새 Redis Cache** 블레이드에서 캐시에 **DNS 이름**을 입력하고 **구독**, **리소스 그룹**, **위치** 및 **가격 책정 계층**을 지정합니다. 이러한 옵션을 지정한 경우 **만들기**를 클릭하여 캐시를 만듭니다.

   ![Azure 포털][AZ03]

1. 캐시가 완료되면 Azure **대시보드**뿐만 아니라 **모든 리소스** 및 **Redis Caches** 블레이드에서도 나열된 것을 확인할 수 있습니다. 해당 위치 중 하나에서 캐시를 클릭하여 캐시의 속성 블레이드를 열 수 있습니다.

   ![Azure 포털][AZ04]

1. 캐시의 속성 목록이 포함된 블레이드가 표시되는 경우 **선택키**를 클릭하고 캐시의 선택키를 복사합니다.

   ![Azure 포털][AZ05]

## <a name="create-a-custom-application-using-the-spring-initializr"></a>Spring Initializr를 사용하여 사용자 지정 응용 프로그램 만들기

1. <https://start.spring.io/>로 이동합니다.

1. **Java**에서 **Maven** 프로젝트를 생성한다고 지정하고, 응용 프로그램에 대한 **그룹** 및 **아티팩트** 이름을 입력한 다음 Spring Initializr의 **정식 버전으로 전환**하는 링크를 클릭합니다.

   ![기본 Spring Initializr 옵션][SI01]

   > [!NOTE]
   >
   > Spring Initializr는 **그룹** 및 **아티팩트** 이름을 사용하여 패키지 이름을 만듭니다(예: *com.contoso.myazuredemo*).
   >

1. **웹** 섹션까지 아래로 스크롤하고 **웹**의 확인란을 선택한 다음 **NoSQL** 섹션까지 아래로 스크롤하고 **Redis**의 확인란을 선택하고 페이지 아래쪽으로 스크롤한 다음 **프로젝트를 생성**하는 단추를 클릭합니다.

   ![전체 Spring Initializr 옵션][SI02]

1. 메시지가 표시되면 로컬 컴퓨터의 경로에 프로젝트를 다운로드합니다.

   ![사용자 지정 Spring Boot 프로젝트 다운로드][SI03]

1. 로컬 시스템에서 파일의 압축을 푼 후에 사용자 지정 Spring Boot 응용 프로그램을 편집할 준비를 합니다.

   ![사용자 지정 Spring Boot 프로젝트 파일][SI04]

## <a name="configure-your-custom-spring-boot-to-use-your-redis-cache"></a>Redis Cache를 사용하도록 사용자 지정 Spring Boot 구성

1. 앱의 *리소스* 디렉터리에서 *application.properties* 파일을 찾거나 아직 없는 경우 해당 파일을 만듭니다.

   ![application.properties 파일 찾기][RE01]

1. 텍스트 편집기에서 *application.properties* 파일을 찾고 파일에 다음 줄을 추가하고 샘플 값을 캐시의 적절한 속성으로 바꿉니다.

   ```yaml
   # Specify the DNS URI of your Redis cache.
   spring.redisHost=myspringbootcache.redis.cache.windows.net

   # Specify the access key for your Redis cache.
   spring.redisPassword=447564652c20426f6220526f636b7321
   ```

   ![application.properties 파일 편집][RE02]

1. *application.properties* 파일을 저장하고 닫습니다.

1. 패키지의 소스 폴더 아래에서 *controller*라는 폴더를 만듭니다.

   `C:\SpringBoot\myazuredemo\src\main\java\com\contoso\myazuredemo\controller`

   또는

   `/users/example/home/myazuredemo/src/main/java/com/contoso/myazuredemo/controller`

1. 방금 만든 *controller* 폴더에서 *HelloController.java*라는 파일을 만들어 다음 코드를 추가합니다.

   ```java
   package com.contoso.myazuredemo;

   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RestController;
   import org.springframework.beans.factory.annotation.Value;
   import redis.clients.jedis.Jedis;
   import redis.clients.jedis.JedisShardInfo;

   @RestController
   public class HelloController {
   
      // Retrieve the DNS name for your cache.
      @Value("${spring.redisHost}")
      private String redisHost;

      // Retrieve the access key for your cache.
      @Value("${spring.redisPassword}")
      private String redisPassword;

      @RequestMapping("/")
      // Define the Hello World controller.
      public String hello() {
      
         // Create a JedisShardInfo object to connect to your Redis cache.
         JedisShardInfo jedisShardInfo = new JedisShardInfo(redisHost, 6380, true);
         // Specify your access key.
         jedisShardInfo.setPassword(redisPassword);
         // Create a Jedis object to store/retrieve information from your cache.
         Jedis jedis = new Jedis(jedisShardInfo);

         // Add a Hello World string to your cache.
         jedis.set("greeting", "Hello World!");

         // Return the string from your cache.
         return jedis.get("greeting");
      }
   }
   ```
   
   여기에서 `com.contoso.myazuredemo`를 프로젝트의 패키지 이름으로 바꾸어야 합니다.

1. *HelloController.java* 파일을 저장하고 닫습니다.

1. Maven을 사용하여 Spring Boot 응용 프로그램을 빌드하고 실행합니다. 예:

   ```shell
   mvn package
   java -jar target/myazuredemo-0.0.1-SNAPSHOT.jar
   ```

1. 웹 브라우저를 통해 http://localhost:8080 으로 이동하여 웹앱을 테스트하거나 사용 가능한 curl이 있는 경우 다음 예제와 같이 구문을 사용합니다.

   ```shell
   curl http://localhost:8080
   ```

   샘플 컨트롤러에서 "Hello World!" 메시지가 표시되어야 합니다. 이 메시지는 Redis Cache에서 동적으로 검색됩니다.

## <a name="next-steps"></a>다음 단계

Azure에서 Spring Boot 응용 프로그램을 사용 하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure App Service에 Spring Boot 응용 프로그램 배포](../app-service/app-service-deploy-spring-boot-web-app-on-azure.md)

* [Azure Container Service의 Kubernetes 클러스터에 Spring Boot 응용 프로그램 실행](../container-service/container-service-deploy-spring-boot-app-on-kubernetes.md)

Java와 함께 Azure를 사용하는 방법에 대한 자세한 내용은 [Azure Java 개발자 센터] 및 [Visual Studio Team Services용 Java 도구]를 참조하세요.

Azure에서 Java로 Redis Cache를 시작하는 방법에 대한 자세한 내용은 [Java에서 Azure Redis Cache를 사용하는 방법][Redis Cache with Java]을 참조하세요.

<!-- URL List -->

[Azure Java 개발자 센터]: https://azure.microsoft.com/develop/java/
[무료 Azure 계정]: https://azure.microsoft.com/pricing/free-trial/
[Visual Studio Team Services용 Java 도구]: https://java.visualstudio.com/
[MSDN 구독자 혜택]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/
[Redis Cache with Java]: cache-java-get-started.md

<!-- IMG List -->

[AZ01]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ01.png
[AZ02]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ02.png
[AZ03]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ03.png
[AZ04]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ04.png
[AZ05]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ05.png

[SI01]: ./media/cache-java-spring-boot-initializer-with-redis-cache/SI01.png
[SI02]: ./media/cache-java-spring-boot-initializer-with-redis-cache/SI02.png
[SI03]: ./media/cache-java-spring-boot-initializer-with-redis-cache/SI03.png
[SI04]: ./media/cache-java-spring-boot-initializer-with-redis-cache/SI04.png

[RE01]: ./media/cache-java-spring-boot-initializer-with-redis-cache/RE01.png
[RE02]: ./media/cache-java-spring-boot-initializer-with-redis-cache/RE02.png

