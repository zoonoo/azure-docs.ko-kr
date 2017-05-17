---
title: "Linux에서 Azure App Service Web App을 사용하여 Ruby 앱 만들기 | Microsoft Docs"
description: "Linux에서 Azure App Service Web App을 사용하여 Ruby 앱을 만드는 방법을 알아봅니다."
keywords: azure app service, linux, oss
services: app-service
documentationcenter: 
author: wesmc7777
manager: erikre
editor: 
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/05/2017
ms.author: wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c63bf790a50b894ea1732ae3142e6c1ff9ccbd4c
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---
# <a name="create-a-ruby-app-with-azure-web-app-on-linux---preview"></a>Linux에서 Azure Web App을 사용하여 Ruby 앱 만들기 - 미리 보기

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

## <a name="overview"></a>개요

이 자습서에서는 기본 ruby on rails 응용 프로그램을 로컬로 만들고 Linux의 Azure Web App에 배포하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

* [Ruby 2.3.3 이상](https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller)이 개발 컴퓨터에 설치되어 있어야 합니다.
* [Git](https://git-scm.com/downloads)이 개발 컴퓨터에 설치되어 있어야 합니다.
* [활성 Azure 구독](https://azure.microsoft.com/pricing/free-trial/)
* 이 자습서는 Ubuntu 환경의 컨텍스트에서 작성되었습니다. 모든 시스템 명령은 bash와 관련되어 있습니다.


## <a name="create-a-new-local-rails-application"></a>새 로컬 rails 응용 프로그램 만들기

1. 새 앱에 대한 디렉터리를 만들고 해당 디렉터리로 변경합니다.

        mkdir ~/workspace
        cd ~/workspace

2. Ruby를 초기화하고 `ruby -v` 명령을 사용하여 버전을 확인합니다.

    ![Ruby init](./media/app-service-linux-ruby-get-started/ruby-version.png)

3. `gem install rails` 명령을 사용하여 rails를 설치합니다.

    ![Install rails](./media/app-service-linux-ruby-get-started/install-rails.png)

4. 다음 명령을 사용하여 **hello-world**라는 rails 응용 프로그램을 만듭니다.

    Rails 5.1.0 이상을 사용하는 경우 다음 명령에 표시된 것처럼 `--skip-yarn`을 포함합니다.

        rails new hello-world --skip-yarn

    5.1.0 이전 Rails 버전에서는 다음 명령을 사용합니다.

        rails new hello-world 

    ![New Hello-world](./media/app-service-linux-ruby-get-started/rails-new-hello-world.png)

    ![New Hello-world](./media/app-service-linux-ruby-get-started/rails-new-hello-world-2.png)

    Rails 5.1 이상을 사용하는 경우 `--skip-yarn` 옵션을 사용하지 않으면 package.json이 만들어집니다. 여기서는 배포에 포함하지 않을 것입니다. 대안으로 package.json 파일을 삭제하거나 다음과 같이 디렉터리의 *.git-ignore*에 추가할 수 있습니다. 

        # Ignore package.json
        /package.json

5. *hello-world* 디렉터리로 변경한 후 서버를 시작합니다.

        cd hello-world
        rails server

    ![Start Hello-world](./media/app-service-linux-ruby-get-started/start-hello-world-server.png)
    
6. 웹 브라우저를 사용하여 `http://localhost:3000`으로 이동한 후 앱을 로컬로 테스트합니다.    

    ![Hello-world](./media/app-service-linux-ruby-get-started/hello-world.png)

## <a name="prepare-the-app-for-azure"></a>앱을 Azure에서 사용하도록 준비

기본적으로 ruby 이미지는 `-e production` 플래그를 지정하여 서버를 실행합니다. 이러한 환경에서는 Linux의 Azure Web App을 위한 몇 가지 설정이 필요합니다. 컨테이너가 해당 설정 일부를 처리합니다(예: `SECRET_KEY_BASE` 설정). 기본 경로를 구성해야 합니다. 그렇지 않으면 사이트를 탐색할 때 404 오류가 발생합니다.

기본 경로를 구성하려면

1. 편집할 수 있게 *~/workspace/hello-world/config/routes.rb*를 엽니다. 스크린샷에 표시된 것처럼 다음을 추가합니다. 

        root 'application#hello'

    ![routes.rb](./media/app-service-linux-ruby-get-started/routes-rb.png)


2. 편집할 수 있게 *~/workspace/hello-world/app/controllers/application_controller.rb*를 엽니다. 스크린샷에 표시된 것처럼 다음을 추가합니다.

        def hello
            render html: "Hello, world from Azure Web App on Linux!"
        end

    ![routes.rb](./media/app-service-linux-ruby-get-started/application-controller-rb.png)


3. 이제 앱이 구성되었습니다. 웹 브라우저를 사용하여 `http://localhost:3000`으로 이동한 후 루트 방문 페이지를 확인합니다.

    ![Hello World가 구성됨](./media/app-service-linux-ruby-get-started/hello-world-configured.png)

## <a name="create-a-ruby-website-on-azure"></a>Azure에서 ruby 웹 사이트 만들기

1. [Azure Portal](http://portal.azure.com)로 이동한 후 구독에 로그인합니다. 다음 스크린샷에 표시된 것처럼 **Linux의 웹앱**을 추가합니다.

    ![Linux에서 웹앱 만들기](./media/app-service-linux-ruby-get-started/top-level-create.png)

2. 다음 스크린샷과 같이 **만들기** 블레이드가 열립니다.

    ![만들기 블레이드](./media/app-service-linux-ruby-get-started/create-blade.png)


    1. 웹앱에 이름을 지정합니다.
    2. 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다. ([제한 사항 섹션](app-service-linux-intro.md)에서 사용 가능한 지역 참조)
    3. 기존 Azure App Service 계획을 선택하거나 새 App Service 계획을 만듭니다. ([제한 사항 섹션](app-service-linux-intro.md)에서 App Service 정보 참조)
    4. 컨테이너 구성에 대해 **Ruby 2.3** 기본 제공 런타임 스택을 선택합니다.
    5. 웹앱에 대해 **대시보드에 고정**을 클릭합니다.
    6. **만들기**를 클릭합니다.

3. 웹앱이 만들어지면 **개요** 블레이드가 표시됩니다. 새 웹앱에 대한 **URL**을 복사한 후 브라우저에서 엽니다. 다음 시작 페이지가 표시됩니다.

    ![시작 페이지](./media/app-service-linux-ruby-get-started/splash-page.png)


## <a name="deploy-your-application"></a>응용 프로그램 배포

이 자습서에서는 Git을 사용하여 로컬 Ruby 응용 프로그램을 Azure에 배포합니다.

1. 새 Azure 웹 사이트에는 이미 Git이 구성되어 있습니다. 웹앱 이름을 삽입한 후 다음 URL로 이동하여 Git 배포 URL을 찾을 수 있습니다.

        https://{your web app name}.scm.azurewebsites.net/api/scm/info

    Git URL은 웹앱 이름에 따라 다음과 같은 형식을 갖습니다.

        https://{your web app name}.scm.azurewebsites.net/{your web app name}.git

2. 다음 명령을 실행하여 Azure 웹 사이트에 로컬 응용 프로그램을 배포합니다.

        cd ~/workspace/hello-world
        git init
        git remote add azure <Git deployment URL from above>
        git add -A
        git commit -m "Initial deployment commit"
        git push master

    원격 배포 작업이 성공을 보고하는지 확인합니다.

    ![웹앱 배포](./media/app-service-linux-ruby-get-started/deployment-success.png)

    원격 기능이 중단되었다는 오류가 표시되어도 배포가 여전히 진행 중일 수 있습니다. 이 경우 브라우저에서 다음 URL로 이동합니다.

        https://{your web app name}.scm.azurewebsites.net/api/deployments

3. 배포가 완료되면 웹앱을 다시 시작하여 배포를 적용합니다. [Azure Portal](http://portal.azure.com)에서 웹앱의 **개요** 블레이드로 이동합니다.

    도구 모음에서 **다시 시작**을 클릭합니다.

    ![웹앱 다시 시작](./media/app-service-linux-ruby-get-started/restart-web-app.png)

4. 사이트로 이동한 후 업데이트가 라이브 상태인지 확인합니다. 

    앱이 다시 시작되는 동안 사이트로 이동하려고 하면 HTTP 상태 코드 오류 503(서버를 사용할 수 없음)이 표시됩니다. 완전하게 다시 시작하는 데 2~3분 정도 걸릴 수 있습니다.

        http://{your web app name}.azurewebsites.net

    ![업데이트된 웹앱](./media/app-service-linux-ruby-get-started/hello-world-updated.png)
    

## <a name="next-steps"></a>다음 단계
Linux의 Azure App Service Web App에 대한 자세한 내용은 다음 링크를 참조하세요. [당사 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)에 질문 및 문제를 게시할 수도 있습니다.

* [Linux의 App Service에서 웹앱 만들기](app-service-linux-how-to-create-web-app.md)
* [Linux에서 App Service에 대한 사용자 지정 Docker 이미지를 사용하는 방법](app-service-linux-using-custom-docker-image.md)
* [Linux의 웹앱에 있는 Node.js용 PM2 구성 사용](app-service-linux-using-nodejs-pm2.md)
* [Linux의 Azure App Service 웹앱에서 .NET Core 사용](app-service-linux-using-dotnetcore.md)
* [Linux의 Azure App Service Web Apps에 대한 FAQ](app-service-linux-faq.md)


