---
title: Azure 미디어 서비스 v3 API에 연결 - Java
description: 이 문서에서는 Java를 사용하여 Azure 미디어 서비스 v3 API에 연결하는 방법을 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2019
ms.author: juliako
ms.openlocfilehash: 6b0f21c3fa7a9c827f7201f4b899a33ea77eaf08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888498"
---
# <a name="connect-to-media-services-v3-api---java"></a>미디어 서비스 v3 API에 연결 - Java

이 문서에서는 서비스 주체 로그인 방법을 사용하여 Azure 미디어 서비스 v3 Java SDK에 연결하는 방법을 보여 주며 있습니다.

이 문서에서는 Visual Studio 코드가 샘플 앱을 개발하는 데 사용됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- [설치하려면 Visual Studio 코드로 Java 쓰기를](https://code.visualstudio.com/docs/java/java-tutorial) 따르십시오.

   - JDK
   - Apache Maven
   - 자바 확장 팩
- 변수를 `PATH` 설정하고 `JAVA_HOME` 환경화해야 합니다.
- [Media Services 계정 만들기](create-account-cli-how-to.md) 리소스 그룹 이름과 Media Services 계정 이름을 기억해야 합니다.
- [액세스 API](access-api-cli-how-to.md) 항목의 단계를 따릅니다. 이후 단계에서 필요한 구독 ID, 응용 프로그램 ID(클라이언트 ID), 인증 키(비밀) 및 테넌트 ID를 기록합니다.

또한 검토:

- [비주얼 스튜디오 코드의 자바](https://code.visualstudio.com/docs/languages/java)
- [VS 코드의 Java 프로젝트 관리](https://code.visualstudio.com/docs/java/java-project)

> [!IMPORTANT]
> [명명 규칙을 검토합니다.](media-services-apis-overview.md#naming-conventions)

## <a name="create-a-maven-project"></a>Maven 프로젝트 만들기

명령줄 도구를 `cd` 열고 프로젝트를 만들 디렉터리로 이동합니다.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

명령을 실행하면 `pom.xml`에서 `App.java`및 기타 파일이 만들어집니다. 

## <a name="add-dependencies"></a>종속성 추가

1. 비주얼 스튜디오 코드에서 프로젝트가 있는 폴더를 엽니다.
1. 찾기 및 열기`pom.xml`
1. 필요한 종속성 추가

    ```xml
   <dependency>
     <groupId>com.microsoft.azure.mediaservices.v2018_07_01</groupId>
     <artifactId>azure-mgmt-media</artifactId>
     <version>1.0.0-beta-3</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.rest</groupId>
     <artifactId>client-runtime</artifactId>
     <version>1.6.6</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>azure-client-authentication</artifactId>
     <version>1.6.6</version>
   </dependency>
    ```

## <a name="connect-to-the-java-client"></a>Java 클라이언트에 연결

1. 아래 `src\main\java\com\azure\ams` `App.java` 파일을 열고 패키지가 맨 위에 포함되어 있는지 확인합니다.

    ```java
    package com.azure.ams;
    ```
1. 패키지 문에서 다음과 같은 import 문을 추가합니다.
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. 요청을 해야 하는 Active Directory 자격 증명을 만들려면 다음 코드를 App 클래스의 기본 메서드에 추가하고 [Access API에서](access-api-cli-how-to.md)얻은 값을 설정합니다.
   
   ```java
   final String clientId = "00000000-0000-0000-0000-000000000000";
   final String tenantId = "00000000-0000-0000-0000-000000000000";
   final String clientSecret = "00000000-0000-0000-0000-000000000000";
   final String subscriptionId = "00000000-0000-0000-0000-000000000000";

   try {
      ApplicationTokenCredentials credentials = new ApplicationTokenCredentials(clientId, tenantId, clientSecret, AzureEnvironment.AZURE);
      credentials.withDefaultSubscriptionId(subscriptionId);

      MediaManager manager = MediaManager
              .configure()
              .withLogLevel(LogLevel.BODY_AND_HEADERS)
              .authenticate(credentials, credentials.defaultSubscriptionId());
      System.out.println("signed in");
   }
   catch (Exception e) {
      System.out.println("Exception encountered.");
      System.out.println(e.toString());
   }
   ```
1. 앱을 실행합니다.

## <a name="see-also"></a>참조

- [Media Services 개념](concepts-overview.md)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [자바 참조](https://aka.ms/ams-v3-java-ref)
- [com.마이크로소프트.azure.mediaservices.v2018_07_01:azure-mgmt-미디어](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>다음 단계

이제 엔터티를 `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` 포함하고 조작할 수 있습니다.

자세한 코드 예제는 [Java SDK 샘플 리포지토리를 참조하세요.](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/)
