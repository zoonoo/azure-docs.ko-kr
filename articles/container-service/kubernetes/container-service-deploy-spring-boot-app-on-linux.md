---
title: "Azure Container Service에서 Linux에 Spring Boot Web App 배포 | Microsoft Docs"
description: "이 자습서에서는 Microsoft Azure에서 Linux Web App으로 Spring Boot 응용 프로그램을 배포하는 단계를 설명합니다."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 06/21/2017
ms.author: asirveda;robmcm
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 6035b0e2fe27b46c441012144caf545eb17e1825
ms.contentlocale: ko-kr
ms.lasthandoff: 08/08/2017

---

# <a name="deploy-a-spring-boot-application-on-linux-in-azure-container-service"></a>Azure Container Service에서 Linux에 Spring Boot 응용 프로그램 배포

[Spring Framework]는 Java 개발자가 엔터프라이즈 수준의 응용 프로그램을 만드는 데 도움이 되는 오픈 소스 솔루션입니다. Java를 기반으로 하여 빌드되는 인기 있는 프로젝트 중 하나가 [Spring Boot]입니다. 이 프로젝트는 독립 실행형 Java 응용 프로그램을 만드는 간단한 방법을 제공합니다.

[Docker]는 개발자가 컨테이너에서 실행되는 응용 프로그램의 배포, 크기 조정 및 관리를 자동화하는 데 도움이 되는 오픈 소스 솔루션입니다.

이 자습서에서는 Docker를 사용하여 [Azure Container Service]에서 Spring Boot 응용 프로그램을 개발하고 Linux 호스트에 배포하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* Azure 구독. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택]을 활성화하거나 [무료 Azure 계정]에 등록할 수 있습니다.
* [Azure CLI(명령줄 인터페이스)]
* 최신 [JDK(Java Developer Kit)]
* Apache의 [Maven] 빌드 도구(버전 3)
* [Git] 클라이언트
* [Docker] 클라이언트

> [!NOTE]
>
> 이 자습서의 가상화 요구 사항으로 인해 가상 컴퓨터에서는 이 문서의 단계를 따를 수 없습니다. 대신 가상화 기능이 지원되는 실제 컴퓨터를 사용해야 합니다.
>

## <a name="create-the-spring-boot-on-the-docker-getting-started-web-app"></a>Spring Boot on the Docker 시작 웹앱 만들기

다음 단계는 간단한 Spring Boot 웹 응용 프로그램을 만들어 로컬에서 테스트하는 데 도움이 됩니다.

1. 명령 프롬프트를 엽니다. 응용 프로그램을 저장할 로컬 디렉터리를 만들고 해당 디렉터리로 변경합니다. 예:
   ```
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   -- 또는 --
   ```
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

2. [Spring Boot on Docker 시작하기] 샘플 프로젝트를 방금 만든 디렉터리에 복제합니다. 예:
   ```
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

3. 디렉터리를 완료된 프로젝트로 변경합니다. 예:
   ```
   cd gs-spring-boot-docker/complete
   ```

4. **선택적 단계**: 포함된 Tomcat 서버를 기본 포트 8080이 아닌 포트 80에서 실행하려는 경우(예: Spring Boot 프로젝트를 로컬로 테스트하려는 경우) 다음 단계를 사용하여 포트를 구성할 수 있습니다.

   a. 디렉터리를 리소스 디렉터리로 변경합니다. 예:
   ```
   cd src/main/resources
   ```

   b. 텍스트 편집기에서 **application.yml** 파일을 엽니다.

   c. **서버** 수정: 서버가 포트 80에서 실행되도록 설정합니다. 예:
   ```
   server:
      port: 80
   ```

   d. **application.yml** 파일을 저장하고 닫습니다.

   e. 디렉터리를 완료된 프로젝트의 루트 폴더로 다시 변경합니다. 예:
   ```
   cd ../../..
   ```

5. Maven을 사용하여 JAR 파일을 빌드합니다. 예:
   ```
   mvn package
   ```

6. 웹앱이 만들어지면 JAR 파일이 위치한 `target` 디렉터리로 이동한 후 웹앱을 시작합니다. 예:
   ```
   cd target
   java -jar gs-spring-boot-docker-0.1.0.jar
   ```

7. 웹 브라우저를 사용하여 로컬로 이동하여 웹앱을 테스트합니다. 예를 들어 curl이 사용 가능하고 포트 80에서 실행하도록 Tomcat 서버를 구성했다면 다음 명령을 사용할 수 있습니다.
   ```
   curl http://localhost
   ```

8. **Hello Docker World!**라는 메시지가 표시되어야 합니다.

   ![로컬로 샘플 앱 찾아보기][SB01]

## <a name="create-an-azure-container-registry-to-use-as-a-private-docker-registry"></a>Azure Container Registry를 만들어서 개인 Docker 레지스트리로 사용

다음 단계에서는 Azure Portal을 사용하여 Azure Container Registry를 만드는 방법을 설명합니다.

> [!NOTE]
> Azure Portal 대신 Azure CLI를 사용하려는 경우 [Azure CLI 2.0을 사용하여 전용 Docker 컨테이너 레지스트리 만들기][1]의 단계에 따르세요.

1. [Azure Portal]에 로그인합니다.

    Azure Portal에서 계정에 로그인한 후 [Azure Portal을 사용하여 전용 Docker 컨테이너 레지스트리 만들기]의 단계에 따르세요. 이 문서의 단계가 여기에 요약되어 있습니다.

2. **+ 새로 만들기**에 대한 메뉴 아이콘을 선택합니다.

3. **컨테이너**, **Azure Container Registry**를 차례로 선택합니다.

   ![새로운 Azure Container Registry 만들기][AR01]

4. Azure Container Registry 템플릿에 대한 정보 페이지가 표시되면 **만들기**를 클릭합니다.

   ![새로운 Azure Container Registry 만들기][AR02]

5. **컨테이너 레지스트리 만들기** 블레이드가 표시된 경우

   a. **레지스트리 이름**과 **리소스 그룹**을 입력합니다.  
   
   b. **관리 사용자**에 **활성화**를 선택합니다.
   
   c. **만들기**를 선택합니다.

   ![Azure Container Registry 설정 구성][AR03]

6. 컨테이너 레지스트리를 만든 후 Azure Portal에서 이동하십시오. 그런 다음 **액세스 키**를 선택합니다. 다음 단계를 위해 사용자 이름 및 암호를 기록해 둡니다.

   ![Azure Container Registry 액세스 키][AR04]

## <a name="configure-maven-to-use-your-azure-container-registry-access-keys"></a>Azure Container Registry 선택키를 사용하도록 Maven 구성

1. Maven 설치를 위한 구성 디렉터리로 이동합니다. 그런 다음 텍스트 편집기로 **settings.xml** 파일을 엽니다.

2. 이 자습서의 이전 섹션에서 **settings.xml** 파일의 `<servers>` 컬렉션에 Azure Container Registry 액세스 설정을 추가합니다. 예:

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry</id>
         <username>wingtiptoysregistry</username>
         <password>AbCdEfGhIjKlMnOpQrStUvWxYz</password>
      </server>
   </servers>
   ```

3. Spring Boot 응용 프로그램에 대해 완료된 프로젝트 디렉터리로 이동합니다(예: **C:\SpringBoot\gs-spring-boot-docker\complete** 또는 **/users/robert/SpringBoot/gs-spring-boot-docker/complete**). 그런 다음 텍스트 편집기로 **pom.xml** 파일을 엽니다.

1. **pom.xml** 파일의 `<properties>` 컬렉션을 업데이트합니다. 이 자습서의 이전 섹션에서 사용한 Azure Container Registry의 로그인 서버 값을 사용합니다. 예:

   ```xml
   <properties>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
   </properties>
   ```

1. **pom.xml** 파일의 `<plugins>` 컬렉션을 업데이트하여 `<plugin>`에 이 자습서의 이전 섹션에서 사용한 Azure Container Registry의 로그인 서버 주소 및 레지스트리 이름이 포함되도록 합니다. 예:

   ```xml
   <plugin>
      <groupId>com.spotify</groupId>
      <artifactId>docker-maven-plugin</artifactId>
      <version>0.4.11</version>
      <configuration>
         <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
         <dockerDirectory>src/main/docker</dockerDirectory>
         <resources>
            <resource>
               <targetPath>/</targetPath>
               <directory>${project.build.directory}</directory>
               <include>${project.build.finalName}.jar</include>
            </resource>
         </resources>
         <serverId>wingtiptoysregistry</serverId>
         <registryUrl>https://wingtiptoysregistry.azurecr.io</registryUrl>
      </configuration>
   </plugin>
   ```

1. Spring Boot 응용 프로그램의 완료된 프로젝트 디렉터리로 이동합니다. 그런 다음, 다음 명령을 실행하여 응용 프로그램을 다시 빌드하고 컨테이너를 Azure Container Registry로 푸시합니다.

   ```
   mvn package docker:build -DpushImage
   ```

> [!NOTE]
>
> Docker 컨테이너를 성공적으로 만들었더라도 Azure에 Docker 컨테이너를 푸시할 때 다음 중 하나와 비슷한 오류 메시지가 표시될 수 있습니다.
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: no basic auth credentials`
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: Incomplete Docker registry authorization credentials. Please provide all of username, password, and email or none.`
>
> 이런 경우 Docker 명령줄에서 Azure에 로그인해야 합니다. 예:
>
> `docker login -u wingtiptoysregistry -p "AbCdEfGhIjKlMnOpQrStUvWxYz" wingtiptoysregistry.azurecr.io`
>
> 그런 다음 명령줄에서 컨테이너를 푸시할 수 있습니다. 예:
>
> `docker push wingtiptoysregistry.azurecr.io/gs-spring-boot-docker`
>

## <a name="create-a-web-app-on-linux-on-azure-app-service-by-using-your-container-image"></a>컨테이너 이미지를 사용하여 Azure App Service에서 Linux에 웹앱 만들기

1. [Azure Portal]에 로그인합니다.

1. **+ 새로 만들기**의 메뉴 아이콘을 클릭하고 **웹 + 모바일**을 클릭합니다.

2.  **Linux의 웹앱**을 클릭합니다.

   ![Azure Portal에서 새로운 웹앱 만들기][LX01]

3. **Linux의 웹앱** 블레이드가 표시되면 다음 단계를 수행합니다.

   a. **앱 이름**에 고유한 이름을 입력합니다. 예: *wingtiptoyslinux*

   b. 드롭다운 목록에서 **구독**을 선택합니다.

   c. 새 리소스 그룹을 만들려면 기존 **리소스 그룹**을 선택하거나 이름을 지정합니다.

   d. **컨테이너 구성**을 클릭하고 다음 정보를 입력합니다.

      * **개인 레지스트리**를 선택합니다.

      * **이미지 및 선택적 태그**: 이전에 사용한 컨테이너 이름을 지정합니다. 예: "*wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest*

      * **서버 URL**: 이전에 사용한 레지스트리 URL을 지정합니다. 예: *https://wingtiptoysregistry.azurecr.io*

      * **로그인 사용자 이름** 및 **암호**: 이전 단계에서 사용한 **액세스 키**의 로그인 자격 증명을 지정합니다.

   e. 모든 이전 정보를 입력한 후 **확인**을 선택합니다.

   ![웹앱 설정 구성][LX02]

1. **만들기**를 클릭합니다.

> [!NOTE]
> Azure에서는 표준 포트 80 또는 8080에서 실행되는 포함된 Tomcat 서버에 인터넷 요청을 자동으로 매핑합니다. 그러나 사용자 지정 포트에서 포함된 Tomcat 서버를 실행하도록 구성한 경우 포함된 Tomcat 서버에 포트를 정의하는 웹앱에 환경 변수를 추가해야 합니다. 이렇게 하려면 다음 단계를 수행합니다.
>
>1. [Azure Portal]에 로그인합니다.

>2. **App Services** 아이콘을 선택합니다. (다음 이미지에서 항목 #1를 참조하세요.)

>3. 목록에서 웹앱을 선택합니다. (다음 이미지에서 항목 #2를 참조하세요.)

>4. **응용 프로그램 설정**을 클릭합니다. (다음 이미지에서 항목 #3을 참조하세요.)

>5. **앱 설정** 섹션에서 **PORT**라고 하는 새로운 환경 변수를 추가합니다. 그런 다음, 값에 대한 사용자 지정 포트 번호를 입력합니다. (다음 이미지에서 항목 #4를 참조하세요.)

>6. **저장**을 선택합니다. (다음 이미지에서 항목 #5를 참조하세요.)

> ![Azure Portal에서 사용자 지정 포트 번호 저장][LX03]
>

## <a name="next-steps"></a>다음 단계

Azure에서 Spring Boot 응용 프로그램을 사용 하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure App Service에 Spring Boot 응용 프로그램 배포][2]


- [Azure Container Service의 Kubernetes 클러스터에 Spring Boot 응용 프로그램 실행](container-service-deploy-spring-boot-app-on-kubernetes.md)

## <a name="additional-resources"></a>추가 리소스

Java와 함께 Azure를 사용하는 방법에 대한 자세한 내용은 [Azure Java 개발자 센터] 및 [Visual Studio Team Services용 Java 도구]를 참조하세요.

Spring Boot on Docker 샘플 프로젝트에 대한 자세한 내용은 [Spring Boot on Docker 시작]을 참조하세요.

자체 Spring Boot 응용 프로그램을 시작하는 데 도움이 필요하면 [Spring Initializr](https://start.spring.io/)를 참조하세요.

간단한 Spring Boot 응용 프로그램을 만들기 시작하는 방법에 대한 자세한 내용은 [Spring Initializr](https://start.spring.io/)를 참조하세요.

Azure와 함께 사용자 지정 Docker 이미지를 사용하는 방법에 대한 추가 예제를 보려면 [Linux에서 Azure Web App에 대한 사용자 지정 Docker 이미지 사용]을 참조하세요.

<!-- URL List -->

[Azure CLI(명령줄 인터페이스)]: /cli/azure/overview
[Azure Container Service]: https://azure.microsoft.com/services/container-service/
[Azure Java 개발자 센터]: https://azure.microsoft.com/develop/java/
[Azure Portal]: https://portal.azure.com/
[Azure Portal을 사용하여 전용 Docker 컨테이너 레지스트리 만들기]: /azure/container-registry/container-registry-get-started-portal
[Linux에서 Azure Web App에 대한 사용자 지정 Docker 이미지 사용]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[무료 Azure 계정]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[JDK(Java Developer Kit)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Visual Studio Team Services용 Java 도구]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[MSDN 구독자 혜택]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Boot on Docker 시작하기]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/container-service-deploy-spring-boot-app-on-linux/SB01.png
[SB02]: ./media/container-service-deploy-spring-boot-app-on-linux/SB02.png

[AR01]: ./media/container-service-deploy-spring-boot-app-on-linux/AR01.png
[AR02]: ./media/container-service-deploy-spring-boot-app-on-linux/AR02.png
[AR03]: ./media/container-service-deploy-spring-boot-app-on-linux/AR03.png
[AR04]: ./media/container-service-deploy-spring-boot-app-on-linux/AR04.png

[LX01]: ./media/container-service-deploy-spring-boot-app-on-linux/LX01.png
[LX02]: ./media/container-service-deploy-spring-boot-app-on-linux/LX02.png
[LX03]: ./media/container-service-deploy-spring-boot-app-on-linux/LX03.png

<!--Reference links in article-->
[1]: https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli/
[2]: https://docs.microsoft.com/azure/app-service/app-service-deploy-spring-boot-web-app-on-azure/


---

