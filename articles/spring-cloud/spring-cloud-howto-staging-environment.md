---
title: Azure Spring Cloud에서 스테이징 환경 설정 | Microsoft Docs
description: Azure Spring Cloud에서 청록색 배포를 사용하는 방법 알아보기
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 0a8536deac0103215cf362c07eb54bbf84701a6b
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016579"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Azure 스프링 클라우드에서 스테이징 환경 설정

**이 문서는 다음에 적용됩니다.** ✔️ Java

이 문서에서는 Azure 스프링 클라우드의 파랑-녹색 배포 패턴을 사용 하 여 스테이징 배포를 설정 하는 방법을 설명 합니다. 파랑-녹색 배포는 기존 (파란색) 버전을 라이브 상태로 유지 하 고 새로운 (녹색) 버전을 배포 하는 Azure DevOps 연속 배달 패턴입니다. 이 문서에서는 프로덕션 배포를 변경 하지 않고 스테이징 배포를 프로덕션에 배치 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

* 표준 가격 책정 계층의 Azure 스프링 클라우드 인스턴스
* Azure CLI에 대 한 [Azure 스프링 클라우드 확장](/cli/azure/azure-cli-extensions-overview)

이 문서에서는 스프링 Inr에서 빌드된 응용 프로그램을 사용 합니다. 이 예제에 다른 응용 프로그램을 사용 하려는 경우 스테이징 배포를 프로덕션 으로부터 구분 하기 위해 응용 프로그램의 공용 부분을 간단 하 게 변경 해야 합니다.

>[!TIP]
> [Azure Cloud Shell](https://shell.azure.com) 은이 문서의 지침을 실행 하는 데 사용할 수 있는 무료 대화형 셸입니다.  최신 버전의 Git, JDK, Maven 및 Azure CLI를 포함 하는 미리 설치 된 공통 Azure 도구를 포함 합니다. Azure 구독에 로그인 하는 경우 Cloud Shell 인스턴스를 시작 합니다. 자세히 알아보려면 [Azure Cloud Shell 개요](../cloud-shell/overview.md)를 참조 하세요.

Azure 스프링 클라우드에서 파랑-녹색 배포를 설정 하려면 다음 섹션의 지침을 따르세요.

## <a name="install-the-azure-cli-extension"></a>Azure CLI 확장 설치

다음 명령을 사용하여 Azure CLI용 Azure Spring Cloud 확장을 설치합니다.

```azurecli
az extension add --name spring-cloud
```
## <a name="prepare-the-app-and-deployments"></a>앱 및 배포 준비
응용 프로그램을 빌드하려면 다음 단계를 수행 합니다.

1. [이 구성](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client)에서 스프링 inr을 사용 하 여 샘플 앱에 대 한 코드를 생성 합니다.

2. 코드를 다운로드합니다.
3. 다음 Hellocontroller.java 소스 파일을 폴더에 추가 합니다 `\src\main\java\com\example\hellospring\` .

   ```java
   package com.example.hellospring; 
   import org.springframework.web.bind.annotation.RestController; 
   import org.springframework.web.bind.annotation.RequestMapping; 

   @RestController 

   public class HelloController { 

   @RequestMapping("/") 

     public String index() { 

         return "Greetings from Azure Spring Cloud!"; 
     } 

   } 
   ```
4. Jar 파일을 빌드합니다.

   ```azurecli
   mvn clean packge -DskipTests
   ```
5. Azure 스프링 클라우드 인스턴스에서 앱을 만듭니다.

   ```azurecli
   az spring-cloud app create -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --assign-endpoint
   ```
6. Azure 스프링 클라우드에 앱을 배포 합니다.

   ```azurecli
   az spring-cloud app deploy -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar
   ```
7. 스테이징 배포에 대 한 코드를 수정 합니다.

   ```java
   package com.example.hellospring; 
   import org.springframework.web.bind.annotation.RestController; 
   import org.springframework.web.bind.annotation.RequestMapping; 

   @RestController 

   public class HelloController { 

   @RequestMapping("/") 

     public String index() { 

         return "Greetings from Azure Spring Cloud! THIS IS THE GREEN DEPLOYMENT"; 
     } 

   } 
   ```
8. Jar 파일을 다시 빌드합니다.

   ```azurecli
   mvn clean packge -DskipTests
   ```
9. 녹색 배포를 만듭니다. 

   ```azurecli
   az spring-cloud app deployment create -n green --app demo -g <resourceGroup> -s <Azure Spring Cloud instance> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar 
   ```

## <a name="view-apps-and-deployments"></a>앱 및 배포 보기

다음 절차를 사용 하 여 배포 된 앱을 봅니다.

1. Azure Portal에서 Azure 스프링 클라우드 인스턴스로 이동 합니다.

1. 왼쪽 창에서 **앱** 창을 열어 서비스 인스턴스에 대 한 앱을 봅니다.

   ![앱 열기 창의 스크린샷](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. 앱을 선택 하 고 세부 정보를 볼 수 있습니다.

   ![앱에 대 한 세부 정보의 스크린샷](media/spring-cloud-blue-green-staging/app-overview.png)

1. **배포** 를 열어 앱의 모든 배포를 확인 합니다. 표는 프로덕션 및 스테이징 배포를 모두 보여 줍니다.

   ![나열 된 앱 배포를 보여 주는 스크린샷](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. URL을 선택 하 여 현재 배포 된 응용 프로그램을 엽니다.
    
   ![배포 된 응용 프로그램에 대 한 U R L을 보여 주는 스크린샷](media/spring-cloud-blue-green-staging/running-blue-app.png)

1. **상태** 열에서 **프로덕션** 을 선택 하 여 기본 앱을 표시 합니다.
    
   ![기본 앱에 대 한 U R L을 보여 주는 스크린샷](media/spring-cloud-blue-green-staging/running-default-app.png)

1. **상태** 열에서 **준비** 를 선택 하 여 준비 앱을 확인 합니다.
    
   ![준비 앱에 대 한 U R L을 보여 주는 스크린샷](media/spring-cloud-blue-green-staging/running-staging-app.png)

>[!TIP]
> * 테스트 끝점이 슬래시 (/)로 끝나는지 확인 하 여 CSS 파일이 올바르게 로드 되는지 확인 합니다.  
> * 브라우저에서 페이지를 보기 위해 로그인 자격 증명을 입력해야 하는 경우, [URL 디코드](https://www.urldecoder.org/)를 사용하여 테스트 엔드포인트를 디코드합니다. Url 디코딩은 url을 *https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.io/gateway/green* 형식으로 반환 합니다. 이 형식을 사용 하 여 끝점에 액세스 합니다.

>[!NOTE]    
> 구성 서버 설정은 스테이징 환경과 프로덕션 환경에 모두 적용 됩니다. 예를 들어 구성 서버에서 앱 게이트웨이의 컨텍스트 경로 *(* somepath)를 로 설정 하는 경우 녹색 배포 경로가 *https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.io/gateway/green/somepath/...* 로 변경 됩니다.
 
이 시점에서 공용 앱 게이트웨이를 방문 하면 새 변경 없이 이전 페이지가 표시 됩니다.

## <a name="set-the-green-deployment-as-the-production-environment"></a>녹색 배포를 프로덕션 환경으로 설정

1. 스테이징 환경에서 변경 내용을 확인 한 후 프로덕션으로 푸시할 수 있습니다. **앱**  >  **배포** 페이지에서 현재 **프로덕션** 중인 응용 프로그램을 선택 합니다.

1. 녹색 배포의 **등록 상태** 뒤에 있는 줄임표 (...)를 선택 하 고 **프로덕션으로 설정** 을 선택 합니다. 

   ![스테이징 빌드를 프로덕션으로 설정 하기 위한 선택 항목을 보여 주는 스크린샷](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. 앱의 URL에 변경 내용이 표시 되는지 확인 합니다.

   ![현재 프로덕션 환경에서 앱의 U R L을 보여 주는 스크린샷](media/spring-cloud-blue-green-staging/new-production-deployment.png)

>[!NOTE]
> 녹색 배포를 프로덕션 환경으로 설정한 후에는 이전 배포가 스테이징 배포가 됩니다.

## <a name="modify-the-staging-deployment"></a>스테이징 배포 수정

변경에 만족 하지 않는 경우 응용 프로그램 코드를 수정 하 고, 새 jar 패키지를 작성 하 고, Azure CLI를 사용 하 여 녹색 배포에 업로드할 수 있습니다.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>스테이징 배포 삭제

Azure Portal에서 스테이징 배포를 삭제 하려면 스테이징 배포에 대 한 페이지로 이동 하 여 **삭제** 단추를 선택 합니다.

또는 다음 명령을 실행 하 여 Azure CLI에서 스테이징 배포를 삭제 합니다.

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>다음 단계

* [Azure 스프링 클라우드 용 CI/CD](/azure/spring-cloud/spring-cloud-howto-cicd?pivots=programming-language-java)