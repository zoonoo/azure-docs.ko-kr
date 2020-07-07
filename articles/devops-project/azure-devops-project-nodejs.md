---
title: GatsbyJS 및 Azure DevOps 스타터를 사용 하 여 PWA에 대 한 CI/CD 파이프라인 만들기
description: DevOps Starter를 사용하면 Azure를 쉽게 시작할 수 있습니다. 빠른 몇 단계로 원하는 Azure 서비스에서 앱을 시작할 수 있습니다.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: arob98
manager: angrobe
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: angrobe
ms.custom: mvc
ms.openlocfilehash: 7db4fa2a780a3a1f53ecd73a40c247583cb6a79a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82233860"
---
# <a name="create-a-cicd-pipeline-in-azure-pipelines-for-nodejs-with-azure-devops-starter"></a>Azure DevOps 스타터를 사용 하 여 Node.js Azure Pipelines에서 CI/CD 파이프라인 만들기

이 빠른 시작에서는 [GatsbyJS](https://www.gatsbyjs.org/) 및 간소화 된 Azure Devops 스타터 생성 환경을 사용 하 여 nodejs 프로그레시브 웹 앱 (PWA)을 만듭니다. 완료되면 Azure Pipelines에 PWA용 CI(지속적인 통합) 및 CD(지속적인 배포) 파이프라인이 있습니다. Azure DevOps 스타터는 개발, 배포 및 모니터링에 필요한 항목을 설정 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Azure DevOps](https://azure.microsoft.com/services/devops/) 조직

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

DevOps Starter는 Azure Pipelines에 CI/CD 파이프라인을 만듭니다. 새 Azure DevOps 조직을 만들거나 기존 조직을 사용할 수 있습니다. 또한 DevOps Starter는 선택한 Azure 구독에서 Azure 리소스를 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고, 왼쪽 창에서 **리소스 만들기**를 선택합니다. 

   ![Azure Portal에서 Azure 리소스 만들기](_img/azure-devops-project-nodejs/create-azure-resource.png)

1. 검색 상자에 **DevOps Starter**를 입력한 다음, 선택합니다. **추가**를 클릭하여 새 항목을 만듭니다.

    ![DevOps Starter 대시보드](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>샘플 애플리케이션 및 Azure 서비스 선택

1. Node.js 샘플 애플리케이션을 선택합니다.   

    ![Node.js 샘플 선택](_img/azure-devops-project-nodejs/select-nodejs.png) 

1. 기본 샘플 프레임 워크는 **Express.js**됩니다. 선택 항목을 **단순 Node.js 앱** 으로 변경 하 고 **다음**을 선택 합니다. 

    ![단순 Node.js 앱을 선택 합니다.](_img/azure-devops-project-nodejs/select-nodejs-framework.png) 

1. 이 단계에서 사용할 수 있는 배포 대상은 2 단계에서 선택한 응용 프로그램 프레임 워크에 따라 결정 됩니다. 이 예제에서 **Windows 웹 앱** 은 기본 배포 대상입니다. **Web App for Containers** 집합을 그대로 두고 **다음**을 선택 합니다.

    ![배포 대상 선택](_img/azure-devops-project-nodejs/select-web-server.png)

## <a name="configure-a-project-name-and-an-azure-subscription"></a>프로젝트 이름 및 Azure 구독 구성

1. DevOps 스타터 만들기 워크플로의 마지막 단계에서 프로젝트 이름을 할당 하 고, Azure 구독을 선택 하 고, **완료**를 선택 합니다.  

    ![프로젝트 이름 할당 및 구독 선택](_img/azure-devops-project-nodejs/assign-project-name.png)

1. 프로젝트가 빌드되고 응용 프로그램이 Azure에 배포 되는 동안 요약 페이지가 표시 됩니다. 잠시 후 git 리포지토리, 간판 보드, 배포 파이프라인, 테스트 계획 및 앱에 필요한 아티팩트를 포함 하는 프로젝트가 [Azure DevOps 조직](https://dev.azure.com/) 에 생성 됩니다.  

## <a name="managing-your-project"></a>프로젝트 관리

1. **모든 리소스** 로 이동 하 여 Devops 스타터를 찾습니다. **Devops 스타터**를 선택 합니다.

    ![리소스 목록의 Azure DevOps 대시보드](_img/azure-devops-project-nodejs/devops-starter-list.png)

1. 프로젝트 홈 페이지, 코드 리포지토리, CI/CD 파이프라인 및 실행 중인 앱에 대 한 링크를 표시 하는 대시보드로 이동 합니다. **프로젝트 홈 페이지** 를 선택 하 여 **Azure devops** 에서 응용 프로그램을 확인 하 고, 다른 브라우저 탭에서 **응용 프로그램 끝점** 을 선택 하 여 라이브 샘플 앱을 확인 합니다. 이 샘플은 나중에 GatsbyJS 생성 된 PWA를 사용 하도록 변경 됩니다.

    ![Azure DevOps 대시보드](_img/azure-devops-project-nodejs/devops-projects-dashboard.png) 

1. Azure DevOps 프로젝트에서 공동 작업을 위해 팀 멤버를 초대 하 고 작업 추적을 시작 하도록 간판 보드를 설정할 수 있습니다. 자세한 내용은 [여기](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops?view=azure-devops)를 참조하세요.

![Azure DevOps 개요](_img/azure-devops-project-nodejs/azure-devops-overview.png)

## <a name="clone-the-repo-and-install-your-gatsby-pwa"></a>리포지토리를 복제 하 고 Gatsby PWA를 설치 합니다.

DevOps 스타터는 Azure Repos 또는 GitHub에서 git 리포지토리를 만듭니다. 이 예제에서는 Azure 리포지토리를 만들었습니다. 다음 단계는 리포지토리를 복제 하 고 변경 하는 것입니다.

1. **Devops 프로젝트** 에서 **리포지토리** 을 선택 하 고 **복제**를 클릭 합니다.  사용자의 데스크톱에 git 리포지토리를 복제 하는 다양 한 메커니즘이 있습니다.  개발 환경에 적합 한 항목을 선택 합니다.  

    ![리포지토리 복제](_img/azure-devops-project-nodejs/clone-the-repo.png)

1. 리포지토리가 바탕 화면에 복제 되 면 시작 템플릿을 변경 합니다. 터미널에서 GatsbyJS CLI를 설치 하 여 시작 합니다.

   ```powershell
    npm install -g gatsby
   ```

1. 터미널에서 리포지토리의 루트로 이동 합니다. 여기에는 다음과 같은 세 개의 폴더가 포함 됩니다.

    ```powershell
    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----        2/23/2020  10:42 PM                Application
    d-----        2/23/2020   3:05 PM                ArmTemplates
    d-----        2/23/2020   3:05 PM                Tests
    ```
    
1. 응용 프로그램 폴더의 모든 파일을 Gatsby 스타터로 바꿀 예정 이기 때문에 모든 파일을 원하지 않습니다. 다음 명령을 순서 대로 실행 하 여 자릅니다.
    
    ```powershell
    cp .\Application\Dockerfile .
    rmdir Application
    ```

1. Gatsby CLI를 사용 하 여 샘플 PWA를 생성 합니다. `gatsby new`터미널에서를 실행 하 여 PWA 마법사를 시작 하 고 `gatsby-starter-blog` 시작 템플릿을 선택 합니다. 이 샘플은 다음과 유사 합니다.

    ```powershell
    c:\myproject> gatsby new
    √ What is your project called? ... my-gatsby-project
    ? What starter would you like to use? » - Use arrow-keys. Return to submit.
        gatsby-starter-default
        gatsby-starter-hello-world
    >   gatsby-starter-blog
        (Use a different starter)
    ```
    
1. 이제 라는 폴더가 있습니다 `my-gatsby-project` . 이름을로 바꾸고를 `Application` 복사 `Dockerfile` 합니다.
    
    ```powershell
    mv my-gatsby-project Application
    mv Dockerfile Application
    ```
    
1. 자주 사용 하는 편집기에서 Dockerfile을 열고 첫 번째 줄을에서 `FROM node:8` 로 변경 합니다 `FROM node:12` . 이렇게 변경 하면 컨테이너가 버전 8.x 대신 버전 4.x를 사용 하 고 Node.js. GatsbyJS에는 최신 버전의 Node.js 필요 합니다.

1. 그런 다음 응용 프로그램 폴더에서 파일에 package.js를 열고 [스크립트 필드](https://docs.npmjs.com/files/package.json#scripts) 를 편집 하 여 개발 및 프로덕션 서버가 사용 가능한 모든 네트워크 인터페이스 (예: 0.0.0.0) 및 포트 80를 수신 하는지 확인 합니다. 이러한 설정이 없으면 컨테이너 app service는 컨테이너 내에서 실행 되는 Node.js 앱으로 트래픽을 라우팅할 수 없습니다. `scripts`필드는 아래와 유사 합니다. 특히, `develop` `serve` 및 `start` 대상을 기본값에서 변경 하려고 합니다.

    ```json
      "scripts": {
        "build": "gatsby build",
        "develop": "gatsby develop  -H 0.0.0.0 -p 80",
        "format": "prettier --write \"**/*.{js,jsx,json,md}\"",
        "start": "npm run serve",
        "serve": "npm run build && gatsby serve -H 0.0.0.0 -p 80",
        "clean": "gatsby clean",
        "test": "echo \"Write tests! -> https://gatsby.dev/unit-testing\" && exit 1"
      }
    ```
    
## <a name="edit-your-cicd-pipelines"></a>CI/CD 파이프라인 편집

1. 이전 섹션에서 코드를 커밋하기 전에 빌드 및 릴리스 파이프라인을 변경 합니다. ' 빌드 파이프라인 '을 편집 하 고 Node.js 버전 12. x를 사용 하도록 노드 작업을 업데이트 합니다. **작업 버전** 필드를 1. x로 설정 하 고 **버전** 필드를 12. x로 설정 합니다.

    ![업데이트 Node.js 12. x](_img/azure-devops-project-nodejs/build-pipeline-update-node.png)

1. 이 빠른 시작에서는 단위 테스트를 만들지 않으며 빌드 파이프라인에서 해당 단계를 사용 하지 않도록 설정 하 고 있습니다. 테스트를 작성 하는 경우 이러한 단계를 다시 활성화할 수 있습니다. **테스트 종속성 설치** 및 **단위 테스트 실행** 이라는 레이블이 지정 된 작업을 마우스 오른쪽 단추로 클릭 하 여 선택 합니다.

    ![빌드 테스트 사용 안 함](_img/azure-devops-project-nodejs/disable-build-unittests.png)

1. 릴리스 파이프라인을 편집 합니다.

    ![릴리스 파이프라인 편집](_img/azure-devops-project-nodejs/edit-release-pipeline.png)

1. 빌드 파이프라인과 마찬가지로, 12. x를 사용 하도록 노드 작업을 변경 하 고 두 개의 테스트 작업을 사용 하지 않도록 설정 합니다. 릴리스는이 스크린샷과 비슷합니다.

    ![완료 된 릴리스 파이프라인](_img/azure-devops-project-nodejs/release-pipeline-complete.png)

1. 브라우저의 왼쪽에서 **views/index.pug** 파일로 이동합니다.

1. **편집**을 선택하고 h2 제목을 변경합니다.  예를 들어 **Azure DevOps 스타터를 사용 하 여 바로 시작** 을 입력 하거나 다른 변경을 수행 합니다.

1. **커밋**을 선택하고 변경 내용을 저장합니다.

1. 브라우저에서 DevOps Starter 대시보드로 이동합니다.   
이제 빌드가 진행되고 있다고 표시됩니다. 변경 내용은 자동으로 빌드 및 CI/CD 파이프라인을 통해 배포 됩니다.

## <a name="commit-your-changes-and-examine-the-azure-cicd-pipeline"></a>변경 내용 커밋 및 Azure CI/CD 파이프라인 검사

앞의 두 단계에서 git 리포지토리에 Gatsby 생성 된 PWA를 추가 하 고 파이프라인을 편집 하 여 코드를 빌드하고 배포 했습니다. 코드를 커밋하고 빌드 및 릴리스 파이프라인을 통해 진행률을 볼 수 있습니다.

1. 터미널의 프로젝트 git 리포지토리의 루트에서 다음 명령을 실행 하 여 Azure DevOps 프로젝트에 코드를 푸시합니다.

    ```powershell
    git add .
    git commit -m "My first Gatsby PWA"
    git push
    ```
    
1. 빌드는 완료 되는 즉시 시작 됩니다 `git push` . **Azure DevOps 대시보드의**진행 상황을 따를 수 있습니다.

3. 몇 분 후에 빌드 및 릴리스 파이프라인이 완료되고 PWA가 컨테이너에 배포됩니다. 위의 대시보드에서 **애플리케이션 엔드포인트** 링크를 클릭하면 블로그의 Gatsby 스타터 프로젝트가 표시됩니다.

## <a name="clean-up-resources"></a>리소스 정리

리소스가 더 이상 필요하지 않을 경우 만든 Azure App Service 및 기타 관련 리소스를 삭제할 수 있습니다. DevOps Starter 대시보드의 **삭제** 기능을 사용합니다.

## <a name="next-steps"></a>다음 단계

CI/CD 프로세스를 구성하면 빌드 및 릴리스 파이프라인이 자동으로 만들어집니다. 팀의 요구 사항에 맞게 이러한 빌드 및 릴리스 파이프라인을 변경할 수 있습니다. CI/CD 파이프라인에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [CD 프로세스 사용자 지정](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

