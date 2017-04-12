---
title: "Azure 앱 서비스에 로컬 Git 배포"
description: "Azure 앱 서비스에 로컬 Git 배포를 사용하는 방법에 대해 알아봅니다."
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2016
ms.author: dariagrigoriu
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 657554ee3929572632dc007d1a6500e59e2a6b97
ms.lasthandoff: 03/22/2017


---
# <a name="local-git-deployment-to-azure-app-service"></a>Azure 앱 서비스에 로컬 Git 배포
이 자습서에서는 로컬 컴퓨터의 Git 리포지토리에서 [Azure 앱 서비스] 에 앱을 배포하는 방법을 보여 줍니다. 앱 서비스는 **Azure 포털** 의 [로컬 Git]배포 옵션을 통해 이 접근 방식을 지원합니다.  
이 문서에 설명되어 있는 대부분의 Git 명령은 [여기](app-service-web-get-started.md)에 설명된 대로 [Azure 명령줄 인터페이스]를 사용하여 앱 서비스 앱을 만드는 경우 자동으로 수행됩니다.

## <a name="prerequisites"></a>필수 조건
이 자습서를 완료하려면 다음이 필요합니다.

* Git. [여기](http://www.git-scm.com/downloads)에서 설치 이진 파일을 다운로드할 수 있습니다.  
* Git에 대한 기본 지식.
* Microsoft Azure 계정. 계정이 없는 경우 [무료 평가판을 등록](https://azure.microsoft.com/pricing/free-trial)하거나 [Visual Studio 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)할 수 있습니다.

> [!NOTE]
> Azure 계정을 등록하기 전에 Azure App Service를 시작하려면 [App Service 평가](https://azure.microsoft.com/try/app-service/)로 이동합니다. App Service에서 단기 스타터 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.  
> 
> 

## <a name="Step1"></a>1단계: 로컬 리포지토리 만들기
다음 작업을 수행하여 새 Git 리포지토리를 만드세요.

1. **GitBash** (Windows) 또는 **Bash** (Unix Shell)와 같은 명령줄 도구를 시작합니다. OS X 시스템에서는 **터미널** 응용 프로그램을 통해 명령줄에 액세스할 수 있습니다.
2. 배포할 콘텐츠가 있는 디렉터리로 이동합니다.
3. 다음 명령을 사용하여 새 Git 리포지토리를 초기화합니다.
   
        git init

## <a name="Step2"></a>2단계: 콘텐츠 커밋
앱 서비스는 다양한 프로그래밍 언어로 만들어진 응용 프로그램을 지원합니다. 

1. 리포지토리에 콘텐츠가 이미 포함된 경우 이 항목을 건너뛰고 아래의 항목 2로 이동합니다. 리포지토리에 콘텐츠가 포함되어 있지 않은 경우 다음과 같이 정적 .html 파일로 채웁니다. 
   
   * 텍스트 편집기를 사용하여 Git 리포지토리의 루트에 **index.html** 이라는 새 파일을 만듭니다.
   * index.html 파일 내용으로 *Hello Git!*
2. 명령줄에서 Git 리포지토리의 루트 아래에 있는지 확인합니다. 다음 명령을 사용하여 리포지토리에 파일을 추가합니다.
   
        git add -A 
3. 그리고 나서 다음 명령을 사용하여 리포지토리에 변경 내용을 커밋합니다.
   
        git commit -m "Hello Azure App Service"

## <a name="Step3"></a>3단계: App Service 앱 리포지토리 사용
앱 서비스 앱에서 Git 리포지토리를 사용할 수 있도록 하려면 다음 단계를 수행합니다.

1. [로컬 Git]에 로그인합니다.
2. App Service의 앱 블레이드에서 **설정 > 배포 원본**을 클릭합니다. **원본 선택**을 클릭한 다음 **로컬 Git 리포지토리**를 클릭하고 **확인**을 클릭합니다.  
   
    ![로컬 Git 리포지토리](./media/app-service-deploy-local-git/local_git_selection.png)
3. Azure에서 리포지토리를 처음 설정하는 경우 로그인 자격 증명을 만들어야 합니다. 이를 사용하여 Azure 리포지토리에 로그인하고 로컬 Git 리포지토리에서 변경 내용을 푸시합니다. 앱의 블레이드에서 **설정> 배포 자격 증명**을 클릭한 다음 배포 사용자 이름 및 암호를 구성합니다. 완료되면 **저장**을 클릭합니다.
   
    ![](./media/app-service-deploy-local-git/deployment_credentials.png)

## <a name="Step4"></a>4단계: 프로젝트 배포
로컬 Git를 사용하여 앱을 앱 서비스에 게시하려면 다음 단계를 사용합니다.

1. Azure Portal의 앱 블레이드에서 **Git URL**에 대한 **설정 > 속성**을 클릭합니다.
   
    ![](./media/app-service-deploy-local-git/git_url.png)
   
    **Git URL** 은 로컬 리포지토리로부터 배포할 원격 참조입니다. 다음 단계에서 이 URL을 사용합니다.
2. 명령줄을 사용하여 로컬 Git 리포지토리의 루트에 있는지 확인합니다.
3. `git remote` 를 사용하여 1단계의 **Git URL** 에 나열된 원격 참조를 추가합니다. 명령은 다음과 유사합니다.
   
        git remote add azure https://<username>@localgitdeployment.scm.azurewebsites.net:443/localgitdeployment.git         
   > [!NOTE]
   > **remote** 명령은 명명된 참조를 원격 리포지토리에 추가합니다. 이 경우 웹 앱의 리포지토리용으로 'azure'라는 이름의 참조를 만듭니다.
   > 
   > 
4. 방금 만든 새 **azure** 원격을 사용하여 앱 서비스에 콘텐츠를 푸시합니다.
   
        git push azure master
   
    Azure 포털에서 배포 자격 증명을 다시 설정할 때 이전에 만든 암호를 입력하라는 메시지가 나타납니다. 암호를 입력합니다(Gitbash는 암호를 입력할 때 콘솔에 별표를 표시하지 않음). 
5. Azure 포털에서 앱으로 돌아갑니다. 가장 최근 푸시의 로그 항목이 **배포** 블레이드에 표시됩니다. 
   
    ![](./media/app-service-deploy-local-git/deployment_history.png)
6. 앱 블레이드의 위쪽에서 **찾아보기** 단추를 클릭하여 콘텐츠가 배포되었는지 확인합니다. 

## <a name="Step5"></a>문제 해결
다음은 Git를 사용하여 Azure에서 앱 서비스 앱을 게시할 때 주로 발생하는 문제 또는 오류입니다.

- - -
**증상**: '[siteURL]'에 액세스할 수 없음: [scmAddress]에 연결하지 못했습니다.

**원인**: 이 오류는 앱이 가동 및 실행되지 않는 경우에 발생할 수 있습니다.

**해결 방법**: Azure 포털에서 앱을 시작합니다. 앱이 실행되지 않으면 Git 배포가 작동하지 않습니다. 

- - -
**증상**: 'hostname' 호스트를 확인할 수 없음

**원인**: 이 오류는 'azure' 원격을 만들 때 입력한 주소 정보가 올바르지 않을 경우에 발생할 수 있습니다.

**해결 방법**: `git remote -v` 명령을 사용하여 모든 원격을 관련 URL과 함께 나열합니다. 'azure' 원격의 URL이 올바른지 확인합니다. 필요한 경우 제거하고 올바른 URL을 사용하여 이 원격을 다시 만드세요.

- - -
**증상**: 공통 참조 없음 및 지정하지 않음; 아무것도 안 함. '마스터'와 같은 분기를 지정해야 할 수도 있습니다.

**원인**: 이 오류는 git push 작업을 수행할 때 분기를 지정하지 않은 경우 및 Git에서 사용하는 push.default 값을 설정하지 않은 경우에 발생할 수 있습니다.

**해결 방법**: 마스터 분기를 지정하고 push 작업을 다시 수행하세요. 예:

    git push azure master

- - -
**증상**: src refspec [branchname]이 전혀 일치하지 않음

**원인**: 이 오류는 'azure' 원격의 마스터가 아닌 다른 분기에 푸시하려는 경우 발생할 수 있습니다.

**해결 방법**: 마스터 분기를 지정하고 push 작업을 다시 수행하세요. 예:

    git push azure master

- - -
**증상**: RPC 실패; 결과=22, HTTP 코드 = 502.

**원인**: 이 오류는 HTTPS를 통해 큰 git 리포지토리를 푸시하려고 시도하는 경우 발생할 수 있습니다.

**해결 방법**: 더 큰 postBuffer를 만들도록 로컬 컴퓨터에서 git 구성을 변경합니다.

    git config --global http.postBuffer 524288000

- - -
**증상**: 오류 - 변경 내용이 원격 리포지토리에 커밋되었으나 웹앱이 업데이트되지 않음

**원인**: 이 오류는 추가 필수 모듈을 지정하는 package.json 파일이 포함된 Node.js 앱을 배포하는 경우에 발생할 수 있습니다.

**해결 방법**: 'npm ERR!'을 포함하는 추가 메시지를 이 오류 이전에 기록해야 합니다. 이러한 메시지는 오류 원인에 대한 추가 내용을 제공할 수 있습니다. 다음은 이 오류 및 해당 'npm ERR!' 메시지의 알려진 원인입니다. 메시지:

* **잘못된 package.json 파일**: npm ERR! 종속성을 읽을 수 없음
* **Windows용 바이너리 배포가 없는 네이티브 모듈**:
  
  * npm ERR! \`cmd "/c" "node-gyp rebuild"\` failed with 1
    
      또는
  * npm ERR! [modulename@version] preinstall: \`make || gmake\`

## <a name="additional-resources"></a>추가 리소스
* [Git 설명서](http://git-scm.com/documentation)
* [프로젝트 Kudu 설명서](https://github.com/projectkudu/kudu/wiki)
* [Azure 앱 서비스에 연속 배포](app-service-continuous-deployment.md)
* [Azure용 PowerShell 사용 방법](/powershell/azureps-cmdlets-docs)
* [Azure 명령줄 인터페이스를 사용하는 방법](../cli-install-nodejs.md)

[Azure 앱 서비스]: https://azure.microsoft.com/documentation/articles/app-service-changes-existing-services/
[Azure Developer Center]: http://www.windowsazure.com/en-us/develop/overview/
[로컬 Git]: https://portal.azure.com
[Git website]: http://git-scm.com
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Azure 명령줄 인터페이스]: https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-azure-resource-manager/

[Using Git with CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[Quick Start - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart

