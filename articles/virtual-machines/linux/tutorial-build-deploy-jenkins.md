---
title: "VSTS를 사용하여 Jenkins에서 Azure VM으로 CI/CD 설정 | Microsoft Docs"
description: "Jenkins를 사용하여 Visual Studio Team Services(VSTS) 또는 Microsoft Team Foundation Server(TFS)의 Release Management에서 Azure VM으로의 Node.js 앱 CI(지속적인 통합) 및 CD(지속적인 배포)를 설정하는 방법을 알아봅니다."
author: ahomer
manager: douge
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/19/2017
ms.author: ahomer
ms.custom: mvc
ms.openlocfilehash: d5c15d6ab36a8454d1c69bac498be89b990c7e33
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2017
---
# <a name="deploy-your-app-to-linux-vms-using-jenkins-and-vsts"></a>Jenkins 및 VSTS를 사용하여 Linux VM에 앱 배포

CI(지속적인 통합) 및 CD(지속적인 배포)는 코드를 작성, 릴리스 및 배포하는 데 사용할 수 있는 파이프라인입니다. VSTS(Visual Studio Team Services)는 Azure에 배포를 위해 모든 기능을 갖춘 완벽한 CI/CD 자동화 도구 집합을 제공합니다. Jenkins는 널리 사용되고 있는 타사 CI/CD 서버 기반 도구이며, 마찬가지로 CI/CD 자동화 기능을 제공합니다. Team Services와 Jenkins를 함께 사용하여 클라우드 앱이나 서비스를 제공하는 방법을 사용자 지정할 수 있습니다.

이 자습서에서는 Jenkins를 사용하여 **Node.js 웹앱**을 빌드하고, VSTS 또는 TFS(Team Foundation Server)를 사용하여 이 웹앱을 Linux 가상 컴퓨터가 포함된 [배포 그룹](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/)에 배포합니다.

다음을 수행합니다.

> [!div class="checklist"]
> * 샘플 앱 가져오기
> * Jenkins 플러그 인 구성
> * Node.js용 Jenkins 프리스타일 프로젝트 구성
> * VSTS 통합을 위한 Jenkins 구성
> * Jenkins 서비스 끝점 만들기
> * Azure 가상 컴퓨터용 배포 그룹 만들기
> * VSTS 릴리스 정의 만들기
> * 수동 및 CI 트리거 배포 실행

## <a name="before-you-begin"></a>시작하기 전에

* Jenkins 서버에 대한 액세스가 필요합니다. Jenkins 서버를 아직 만들지 않은 경우 [Azure Virtual Machine에 Jenkins 마스터 만들기](https://docs.microsoft.com/en-us/azure/jenkins/install-jenkins-solution-template)를 참조하십시오. 

* VSTS 계정(`https://{youraccount}.visualstudio.com`)에 로그인합니다. 
  [무료 VSTS 계정](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308)을 가질 수 있습니다.

  > [!NOTE]
  > 자세한 내용은 [VSTS에 연결](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services)을 참조하세요.

*  배포 대상으로 Linux 가상 컴퓨터가 필요합니다.  자세한 내용은 [Azure CLI로 Linux VM 만들기 및 관리](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/tutorial-manage-vm)를 참조하세요.

*  가상 컴퓨터의 인바운드 포트 80을 엽니다.  자세한 내용은 [Azure Portal을 사용하여 네트워크 보안 그룹 만들기](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-create-nsg-arm-pportal)를 참조하세요.

## <a name="get-the-sample-app"></a>샘플 앱 가져오기

Git 리포지토리에 저장되어 있는 앱을 배포해야 합니다.
이 자습서에서는 [GitHub에서 제공되는 이 샘플 앱](https://github.com/azooinmyluggage/fabrikam-node)을 사용하는 것이 좋습니다.  이 자습서에는 Node.js와 응용 프로그램을 설치하는 데 사용되는 샘플 스크립트가 포함됩니다.  자신의 리포지토리로 작업하려면 유사한 샘플을 구성해야 합니다.

1. 이 앱의 포크를 만든 다음, 이 자습서의 이후 단계에서 사용할 수 있도록 해당 위치(URL)를 적어 둡니다.  자세한 내용은 [리포지토리 포크](https://help.github.com/articles/fork-a-repo/)를 참조하세요.    

> [!NOTE]
> [Yeoman](http://yeoman.io/learning/index.html)을 사용하여 빌드된 이 앱은 **Express**, **bower**, **grunt**를 사용하며 몇몇 **npm** 패키지가 종속성으로 포함되어 있습니다.
> 샘플에는 Nginx를 설정하고 앱을 배포하는 스크립트도 포함되어 있습니다. 이 스크립트는 가상 컴퓨터에서 실행됩니다. 해당 스크립트 실행 시 Node, Nginx 및 PM2가 설치되고, Nginx 및 PM2가 구성된 다음, Node 앱이 시작됩니다.

## <a name="configure-jenkins-plugins"></a>Jenkins 플러그 인 구성

먼저 **NodeJS**와 **VS Team Services 연속 배포**용 Jenkins 플러그 인을 두 개 구성해야 합니다.

1. Jenkins 계정을 열고 **Manage Jenkins**(Jenkins 관리)를 선택합니다.
2. **Manage Jenkins**(Jenkins 관리) 페이지에서 **Manage Plugins**(플러그 인 관리)를 선택합니다.
3. 목록을 필터링하여 **NodeJS** 플러그 인을 찾아서 **다시 시작 없이 설치** 옵션을 선택합니다.
    ![Jenkins에 NodeJS 플러그 인 추가](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)
4. 목록을 필터링하여 **VS Team Services 연속 배포** 플러그 인을 찾아서 **다시 시작 없이 설치** 옵션을 선택합니다.
5. Jenkins 대시보드로 돌아가서 **Jenkins 관리**를 선택합니다.
6. **Global Tool Configuration**(글로벌 도구 구성)을 선택합니다.  **NodeJS**를 찾아서 **NodeJS 설치**를 클릭합니다.
7. **Install automatically**(자동으로 설치) 옵션을 사용하도록 설정한 다음 **이름** 값을 입력합니다.
8. **Save**를 클릭합니다.

## <a name="configure-a-jenkins-freestyle-project-for-nodejs"></a>Node.js용 Jenkins 프리스타일 프로젝트 구성

1. **새 항목**을 클릭합니다.  **항목 이름**을 입력합니다.
2. **프리스타일 프로젝트**를 선택합니다.  **확인**을 클릭합니다.
3. **Source Code Management**(소스 코드 관리) 탭에서 **Git**를 선택하고, 앱 코드가 포함된 리포지토리 및 분기의 세부 정보를 입력합니다.    
    ![빌드에 리포지토리 추가](media/tutorial-build-deploy-jenkins/jenkins-git.png)
4. **Build Triggers**(빌드 트리거) 탭에서 **Poll SCM**(SCM 폴링)을 선택하고 일정 `H/03 * * * *`을 입력하여, 3분마다 Git 리포지토리에서 변경 내용을 폴링하도록 합니다. 
5. **Build Environment**(빌드 환경) 탭에서 **Provide Node &amp; npm bin/ folder PATH**(노드 및 npm bin/ 폴더 경로 제공)를 선택하고 **NodeJS 설치** 값을 선택합니다. **npmrc file**(npmrc 파일)은 "use system default"(시스템 기본값 사용) 상태로 유지합니다.
6. **Build**(빌드) 탭에서 **Execute shell**(셸 실행)을 선택하고 `npm install` 명령을 입력하여 모든 종속성이 업데이트되었는지 확인합니다.


## <a name="configure-jenkins-for-vsts-integration"></a>VSTS 통합을 위한 Jenkins 구성

  > [!NOTE]
  다음 단계에서 사용하는 PAT(개인용 액세스 토큰)에 **VSTS의 릴리스(읽기, 쓰기, 실행 및 관리) 권한**이 있어야 합니다.
 
1.  아직 없는 경우 VSTS 계정에 PAT를 만듭니다. Jenkins가 VSTS 계정에 액세스하려면 이 정보가 필요합니다.  이 섹션의 다음 단계에 나오는 토큰 정보를 **저장**해야 합니다.
  생성하는 방법을 알아보려면 [VSTS 및 TFS에 대한 개인용 액세스 토큰은 어떻게 만드나요?](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate)를 참조하세요.
2. **Post-build Actions**(빌드 후 작업) 탭에서 **Add post-build action**(빌드 후 작업 추가)을 클릭합니다. **Archive the artifacts**(아티팩트 보관)를 선택합니다.
3. **Files to archive**(보관할 파일)에 `**/*`를 입력하여 모든 파일을 포함합니다.
4. 또 다른 작업을 만들려면 **Add post-build action**(빌드 후 작업 추가)을 클릭합니다.
5. **Trigger release in TFS/Team Services**(TFS/Team Services에서 릴리스 트리거)를 선택하고 다음과 같이 VSTS 계정에 대한 URI를 입력합니다. `https://{your-account-name}.visualstudio.com`
6. **팀 프로젝트** 이름을 입력합니다.
7. **릴리스 정의**의 이름을 선택합니다. (이 릴리스 정의는 나중에 VSTS에서 만듭니다.)
8. VSTS 또는 TFS 환경에 연결할 자격 증명을 선택합니다.  VSTS를 사용하는 경우 **사용자 이름**을 비워둡니다.
   온-프레미스 버전의 TFS를 사용하는 경우 **사용자 이름 및 암호**를 입력합니다.    
    ![Jenkins 빌드 후 작업 구성](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)
5. Jenkins 프로젝트를 **저장**합니다.

## <a name="create-a-jenkins-service-endpoint"></a>Jenkins 서비스 끝점 만들기

서비스 끝점이 있으면 VSTS에서 Jenkins에 연결할 수 있습니다.

1. VSTS에서 **서비스** 페이지를 열고 **새 서비스 끝점** 목록을 열어 **Jenkins**를 선택합니다.
    ![Jenkins 끝점 추가](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)
2. 연결의 이름을 입력합니다.
3. Jenkins 서버의 URL을 입력하고 **신뢰할 수 없는 SSL 인증서 수락** 옵션을 선택합니다.  예제 URL: `http://{YourJenkinsURL}.westcentralus.cloudapp.azure.com`
4. Jenkins 계정의 **사용자 이름 및 암호**를 입력합니다.
5. **연결 확인**을 선택하여 정보가 정확한지 확인합니다.
6. **확인**을 선택하여 서비스 끝점을 만듭니다.

## <a name="create-a-deployment-group-for-azure-virtual-machines"></a>Azure Virtual Machines용 배포 그룹 만들기

릴리스 정의가 가상 컴퓨터에 배포될 수 있도록 VSTS 에이전트를 등록하려면 [배포 그룹](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/)이 필요합니다.  배포 그룹을 사용하면 배포할 대상 컴퓨터의 논리 그룹을 쉽게 정의하고 각 컴퓨터에 필요한 에이전트를 설치할 수 있습니다.

   > [!NOTE]
   > 다음 단계에서 필수 구성 요소를 설치하고 **sudo 권한으로 스크립트를 실행하지 않도록** 하십시오.

1. **빌드 &amp; 릴리스** 허브의 **릴리스** 탭을 열고, **배포 그룹**을 연 다음, **+ 새로 만들기**를 선택합니다.
2. 배포 그룹의 이름을 입력하고 원하는 경우 설명을 입력합니다.
   그런 다음 **만들기**를 선택합니다.
3. 배포 대상 가상 컴퓨터의 운영 체제를 선택합니다.  예를 들어 **Ubuntu 16.04+**를 선택합니다.
4. **인증에 스크립트의 개인용 액세스 토큰 사용**을 선택합니다.
5. **시스템 필수 구성 요소** 링크를 선택합니다.  운영 체제의 필수 구성 요소를 설치합니다.
6. **클립보드에 스크립트 복사**를 선택하여 스크립트를 복사합니다.
7. 배포 대상 가상 컴퓨터에 **로그인**하고 스크립트를 **실행**합니다.  스크립트를 sudo 권한으로 실행하지 **마십시오**.
8. 설치가 끝나면 배포 그룹 태그를 묻는 메시지가 나타납니다.  기본값을 그대로 적용합니다.
9. VSTS에서 **배포 그룹**의 **대상**에서 새로 등록한 가상 컴퓨터를 확인합니다.

## <a name="create-a-vsts-release-definition"></a>VSTS 릴리스 정의 만들기

릴리스 정의는 앱을 배포하기 위해 VSTS가 실행하는 프로세스를 지정합니다.  이 예제에서는 셸 스크립트를 실행합니다.

VSTS에서 릴리스 정의를 만들려면 다음을 수행합니다.

1. **Build &amp; Release**(빌드 및 릴리스) 허브에서 **릴리스**를 열고 **Create release definition**(릴리스 정의 만들기)을 선택합니다. 
2. **Empty process**(빈 프로세스)로 시작을 선택하여 **빈** 템플릿을 선택합니다.
3. **아티팩트** 섹션에서 **+ 아티팩트 추가**를 클릭하고 **소스 유형**에 **Jenkins**를 선택합니다. Jenkins 서비스 끝점 연결을 선택합니다. 그런 다음 Jenkins 소스 작업을 선택하고 **추가**를 선택합니다.
4.  **환경 1** 옆에 있는 줄임표를 클릭합니다.  **배포 그룹 단계 추가**를 선택합니다.
5.  **배포 그룹**을 선택합니다.
5. **+**를 클릭하여 **배포 그룹 단계**에 작업을 추가합니다.
6. **셸 스크립트** 작업을 선택하고 **추가**를 클릭합니다.    
    **셸 스크립트** 작업은 Node.js를 설치하고 앱을 시작하기 위해 각 서버에서 실행할 스크립트의 구성을 제공하는 데 사용됩니다. 다음과 같이 구성합니다.
8. **스크립트 경로**:     
    `$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh`
9.  **고급**을 클릭한 다음 **작업 디렉터리 지정**을 사용하도록 설정합니다.
10.  **작업 디렉터리**: `$(System.DefaultWorkingDirectory)/Fabrikam-Node`
11. 릴리스 정의의 이름을 Jenkins에서 빌드의 **빌드 후 작업** 탭에 지정한 이름으로 편집합니다. 소스 아티팩트를 업데이트할 때 Jenkins가 새 릴리스를 트리거하려면 이 이름이 필요합니다.
12. **저장**을 클릭하고 **확인**을 클릭하여 릴리스 정의를 저장합니다.

## <a name="execute-manual-and-ci-triggered-deployments"></a>수동 및 CI 트리거 배포 실행

1. **+ 릴리스**와 **릴리스 만들기**를 차례로 선택합니다.
2. 강조 표시된 드롭다운 목록에서 완성한 빌드를 선택하고 **큐**를 선택합니다.
3. 팝업 메시지에서 릴리스 링크를 선택합니다. 예를 들어 "**Release-1** 릴리스를 만들었습니다."를 선택합니다.
4. **로그** 탭을 열어 릴리스 콘솔 출력을 확인합니다.
5. 브라우저에서 배포 그룹에 추가한 서버 중 하나의 URL을 엽니다. 예를 들어 `http://{your-server-ip-address}`를 입력합니다.
6. 소스 Git 리포지토리로 이동하여 app/views/index.jade 파일의 **h1** 제목 내용을 변경된 텍스트로 수정합니다.
7. 변경 내용을 **커밋**합니다.
8. 몇 분이 지나면 VSTS 또는 TFS의 **릴리스** 페이지에서 새 릴리스가 만들어진 것을 확인할 수 있습니다. 릴리스를 열어 배포가 진행되는지 확인합니다. 축하합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 릴리스를 위해 VSTS와 Jenkins 빌드를 사용하여 앱을 Azure에 배포하는 과정을 자동화했습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Jenkins에서 앱 빌드
> * VSTS 통합을 위한 Jenkins 구성
> * Azure 가상 컴퓨터용 배포 그룹 만들기
> * VM을 구성하고 앱을 배포하는 릴리스 정의 만들기

LAMP(Linux, Apache, MySQL 및 PHP) 스택을 배포하는 방법에 대한 자세한 내용을 보려면 다음 자습서로 이동하세요.

> [!div class="nextstepaction"]
> [LAMP 스택 배포](tutorial-lamp-stack.md)