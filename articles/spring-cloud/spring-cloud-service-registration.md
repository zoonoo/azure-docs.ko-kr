---
title: 서비스 레지스트리 및 검색 자동화
description: Spring 클라우드 서비스 레지스트리를 사용하여 서비스 검색 및 등록을 자동화하는 방법 알아보기
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: brendm
ms.openlocfilehash: 6c217096f0ba4200f49bd1fd8056768a6f6f6dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278851"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>스프링 클라우드 서비스 검색 및 등록

서비스 검색은 마이크로 서비스 기반 아키텍처의 핵심 요구 사항입니다.  각 클라이언트를 수동으로 구성하는 데는 시간이 걸리며 인적 오류의 가능성이 발생합니다.  Azure 스프링 클라우드 서비스 레지스트리가 이 문제를 해결합니다.  일단 구성되면 서비스 레지스트리 서버는 응용 프로그램의 마이크로 서비스에 대한 서비스 등록 및 검색을 제어합니다. 서비스 레지스트리 서버는 배포된 마이크로 서비스의 레지스트리를 유지 관리하고, 클라이언트 측 로드 밸런싱을 가능하게 하며, DNS에 의존하지 않고 클라이언트에서 서비스 공급자를 분리합니다.

## <a name="register-your-application-using-spring-cloud-service-registry"></a>스프링 클라우드 서비스 레지스트리를 사용하여 응용 프로그램 등록

응용 프로그램이 Spring 클라우드 서비스 레지스트리를 사용하여 서비스 등록 및 검색을 관리하려면 먼저 응용 프로그램의 *pom.xml* 파일에 여러 종속성이 포함되어야 합니다.
*pom.xml에* *스프링 클라우드 스타터-넷플릭스-유레카-클라이언트* 및 *스프링 클라우드 스타터-azure-스프링-클라우드-클라이언트에* 대한 종속성을 포함합니다.

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
    </dependency>
```

## <a name="update-the-top-level-class"></a>최상위 클래스 업데이트

마지막으로 응용 프로그램의 최상위 클래스에 추가합니다.

 ```java
    package foo.bar;

    import org.springframework.boot.SpringApplication;
    import org.springframework.cloud.client.SpringCloudApplication;

    @SpringCloudApplication
    public class DemoApplication {
        public static void main(String... args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
 ```

Spring 클라우드 서비스 레지스트리 서버 끝점은 응용 프로그램에서 환경 변수로 주입됩니다.  이제 마이크로 서비스는 서비스 레지스트리 서버에 자신을 등록하고 다른 종속 마이크로 서비스를 검색할 수 있습니다.

변경 내용이 서버에서 모든 마이크로 서비스로 전파되는 데 몇 분 정도 걸릴 수 있습니다.
