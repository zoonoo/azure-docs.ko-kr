---
title: "Eclipse용 Azure 도구 키트를 사용하여 Spring Boot 앱을 Docker 컨테이너로 게시 | Microsoft Docs"
description: "Eclipse용 Azure 도구 키트를 사용하여 Docker 컨테이너로 Microsoft Azure에 웹앱을 게시하는 방법을 알아봅니다."
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
ms.author: robmcm
ms.translationtype: Human Translation
ms.sourcegitcommit: 8be2bcb9179e9af0957fcee69680ac803fd3d918
ms.openlocfilehash: 36d8233b64144d87501cd4d4a9cac539ab143d38
ms.contentlocale: ko-kr
ms.lasthandoff: 06/23/2017


---

# <a name="publish-a-spring-boot-app-as-a-docker-container-using-the-azure-toolkit-for-eclipse"></a>Eclipse용 Azure 도구 키트를 사용하여 Spring Boot 앱을 Docker 컨테이너로 게시

**[Spring Framework]**는 Java 개발자가 엔터프라이즈 수준의 응용 프로그램을 만드는 데 도움이 되는 오픈 소스 솔루션입니다. 해당 플랫폼 맨 위에 빌드되는 인기 있는 프로젝트 중 하나가 [Spring Boot]입니다. 이 프로젝트는 독립 실행형 Java 응용 프로그램을 만들기 위한 간단한 방법을 제공합니다.

**[Docker]**는 개발자가 컨테이너에서 실행되는 응용 프로그램의 배포, 크기 조정 및 관리를 자동화하는 데 도움이 되는 오픈 소스 솔루션입니다.

이 자습서에서는 Eclipse용 Azure 도구 키트를 사용하여 Microsoft Azure에 Docker 컨테이너로 Spring Boot 응용 프로그램을 배포하는 단계를 안내합니다.

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="cloning-the-default-spring-boot-docker-app-repository"></a>기본 Spring Boot Docker 앱 리포지토리 복제

### <a name="importing-the-public-repository"></a>공용 리포지토리 가져오기

다음 단계는 IntelliJ를 사용하여 Spring Boot Docker 리포지토리를 로컬 컴퓨터로 복제하는 방법을 안내합니다. 명령줄을 사용하려는 경우 [Azure Container Service에서 Linux에 Spring Boot 응용 프로그램 배포][Deploy Spring Boot on Linux in ACS]를 참조하세요.

1. Eclipse를 엽니다.

1. **File**을 클릭한 다음 **Import**를 클릭합니다.

   ![File Import 메뉴][CL01]

1. **Import** 대화 상자가 열리면:

   a. **Git**를 확장합니다.

   b. **Projects from Git**를 강조 표시합니다.
   
   c. **다음**을 누릅니다.

   ![Import 대화 상자][CL02]

1. **Select Repository Source** 페이지에서:

   a. **Clone URI**를 강조 표시합니다.
   
   b. **다음**을 누릅니다.

   ![Select Repository Source][CL03]

1. **Source Git Repository** 페이지에서:

   a. 리포지토리 **URI**에 `https://github.com/spring-guides/gs-spring-boot-docker.git`를 입력합니다. 이렇게 하면 **Host** 및 **Repository path** 필드에 올바른 값이 자동으로 채워집니다.
   
   b. Spring Boot 리포지토리는 공개되어 있으므로 Git 사용자 이름과 암호를 입력하지 않아도 됩니다.
   
   c. **다음**을 누릅니다.

   ![Source Git Repository][CL04]

1. **Branch** 페이지에서 **Next**를 클릭합니다.

   ![Branch][CL05]

1. **Local Destination** 페이지에서:

   a. 로컬 리포지토리를 저장할 로컬 폴더를 지정합니다.
   
   b. **다음**을 누릅니다.

   ![Local Destination][CL06]

1. **Select a wizard to use for importing projects** 페이지에서:

   a. **Import as a general project**를 선택합니다.
   
   b. **다음**을 누릅니다.

   ![Local Destination][CL07]

1. **Import Projects** 페이지에서:

   a. **Project name**을 지정합니다.
   
   b. **마침**을 클릭합니다.

   ![Import Projects][CL08]

1. 리포지토리가 성공적으로 복제되면 Eclipse에 나열된 모든 파일을 볼 수 있습니다.

   ![로컬 리포지토리][CL09]

### <a name="creating-a-maven-project-from-your-local-repository"></a>로컬 리포지토리에서 Maven 프로젝트 만들기

Spring Boot Docker 리포지토리는 이 자습서에서 사용할 완성된 Maven 프로젝트를 포함하고 있습니다. 

1. **File**을 클릭한 다음 **Import**를 클릭합니다.

   ![File Import 메뉴][CL01]

1. **Import** 대화 상자가 열리면:

   a. **Maven**을 확장합니다.
   
   b. **Existing Maven Projects**를 강조 표시합니다.
   
   c. **다음**을 누릅니다.

   ![Import 대화 상자][MV01]

1. **Maven Projects** 페이지에서:

   a. **Root Directory**에 대해 로컬 리포지토리 내의 `complete` 폴더를 지정합니다.
   
   b. **Advanced** 섹션을 확장하고 **Name template**에 사용자 지정 이름을 입력합니다.
   
   c. 프로젝트 내에서 `pom.xml` 파일의 확인란을 선택합니다.
   
   d. **마침**을 클릭합니다.

   ![Maven Projects][MV02]

1. Maven 프로젝트가 성공적으로 열리면 Eclipse에 나열된 두 번째 프로젝트가 표시됩니다.

   ![로컬 Maven 프로젝트][MV03]

## <a name="building-your-spring-boot-app-with-maven"></a>Maven으로 Spring Boot 앱 빌드

1. Eclipse 프로젝트 탐색기에서 Maven 프로젝트를 강조 표시합니다.

1. **Run**, **Run As**, **Maven build**를 클릭합니다.

   ![Maven 빌드로 실행][BU01]

1. 응용 프로그램이 성공적으로 빌드되면 콘솔 창에 상태가 나열됩니다.

   ![성공적인 Maven 빌드][BU02]

## <a name="publishing-your-web-app-to-azure-using-a-docker-container"></a>Docker 컨테이너를 사용하여 Azure에 웹앱 게시

1. Eclipse 프로젝트 탐색기에서 Maven 프로젝트를 강조 표시합니다.

1. Azure **Publish** 메뉴를 클릭한 다음 **Publish as Docker container**를 클릭합니다.

   ![Publish as Docker container][PU01]

1. **Deploy Docker Container on Azure** 대화 상자가 표시되면:

   a. **Docker image name**에 사용자 지정 이름을 입력합니다.
   
   b. **Artifact to deploy**에 대해 방금 빌드한 `gs-spring-boot-docker-0.1.0.jar` 파일의 경로를 지정합니다.

   ![Docker 옵션 지정][PU02]

   모든 기존 Docker 호스트가 표시됩니다. 기존 호스트에 배포하려는 경우 4단계로 건너뛸 수 있습니다. 그렇지 않으면 다음 단계를 사용하여 새 호스트를 만들어야 합니다.

   a. **추가**를 클릭합니다.

      ![새 Docker 호스트 추가][PU03]

   b. **Create Docker Host** 대화 상자가 표시되면 기본값을 그대로 허용하도록 선택하거나 새 Docker 호스트에 대한 사용자 지정 설정을 지정할 수 있습니다. (다양한 설정의 자세한 설명은 [IntelliJ용 Azure 도구 키트를 사용하여 웹앱을 Docker 컨테이너로 게시][Publish Container with Azure Toolkit] 문서에서 사용할 수 있습니다.) 사용할 설정을 지정하면 **Next**를 클릭합니다.

      ![Docker 호스트 옵션 지정][PU04]

   c. Azure Key Vault에서 기존 로그인 자격 증명을 사용하도록 선택하거나 새 Docker 로그인 자격 증명을 입력하도록 선택할 수 있습니다. 옵션을 지정하면 **Finish**를 클릭합니다.

      ![Docker 호스트 자격 증명 지정][PU05]

1. Docker 호스트를 강조 표시한 다음 **Next**를 클릭합니다.

   ![사용할 Docker 호스트 선택][PU06]

1. **Deploy Docker Container on Azure** 대화 상자의 마지막 페이지에서 다음 옵션을 지정해야 합니다.

   a. Docker 컨테이너를 호스트하는 컨테이너에 대한 사용자 지정 이름을 지정하도록 선택하거나 기본값을 적용할 수 있습니다.

   b. 다음 구문 "*[외부 포트]*:*[내부 포트]*를 사용하여 docker 호스트에 대한 TCP 포트를 입력해야 합니다. 예를 들어 "80:8080"은 "80"의 외부 포트 및 "8080"의 기본 내부 Spring Boot 포트를 지정합니다.
   
      내부 포트를 사용자 지정한 경우(예: *application.yml* 파일을 편집하여) Azure에서 발생할 올바른 라우팅에 대한 포트 번호를 지정해야 합니다.

   c. 이러한 옵션을 구성했으면 **Finish**를 클릭합니다.

   ![Azure에 Docker 컨테이너 배포][PU07]

1. Azure 도구 키트의 게시가 완료되면 Azure 활동 로그에 상태에 대해 **Published**가 표시됩니다.

   ![Docker 호스트를 성공적으로 배포][PU08]

## <a name="next-steps"></a>다음 단계

[!INCLUDE [azure-toolkit-additional-resources](../includes/azure-toolkit-additional-resources.md)]

<!-- URL List -->

[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Deploy Spring Boot on Linux in ACS]: ./container-service/container-service-deploy-spring-boot-app-on-linux.md
[Docker]: https://www.docker.com/
[Publish Container with Azure Toolkit]: ./azure-toolkit-for-intellij-publish-as-docker-container.md
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[CL01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL01.png
[CL02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL02.png
[CL03]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL03.png
[CL04]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL04.png
[CL05]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL05.png
[CL06]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL06.png
[CL07]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL07.png
[CL08]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL08.png
[CL09]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL09.png

[MV01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/MV01.png
[MV02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/MV02.png
[MV03]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/MV03.png

[BU01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/BU01.png
[BU02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/BU02.png

[PU01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU01.png
[PU02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU02.png
[PU03]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU03.png
[PU04]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU04.png
[PU05]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU05.png
[PU06]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU06.png
[PU07]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU07.png
[PU08]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU08.png

