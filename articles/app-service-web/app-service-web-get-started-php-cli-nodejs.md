---
title: "5분 내에 Azure에 첫 번째 PHP 웹앱 배포 | Microsoft Docs"
description: "샘플 PHP 앱을 배포하여 App Service에서 웹앱을 실행하는 작업이 얼마나 쉬운지 알아봅니다. 실제 개발을 신속하게 수행하기 시작하고 즉시 결과를 봅니다."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 21acd587-b772-4d89-be06-9a7429c33c7a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/04/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 27b5ead5a60b05f84974391954f1f087da63c2e9


---
# <a name="deploy-your-first-php-web-app-to-azure-in-five-minutes"></a>5분 내에 Azure에 첫 번째 PHP 웹앱 배포

> [!div class="op_single_selector"]
> * [첫 번째 HTML 사이트](app-service-web-get-started-html-cli-nodejs.md)
> * [첫 번째 .NET 앱](app-service-web-get-started-dotnet-cli-nodejs.md)
> * [첫 번째 PHP 앱](app-service-web-get-started-php-cli-nodejs.md)
> * [첫 번째 Node.js 앱](app-service-web-get-started-nodejs-cli-nodejs.md)
> * [첫 번째 Python 앱](app-service-web-get-started-python-cli-nodejs.md)
> * [첫 번째 Java 앱](app-service-web-get-started-java.md)
> 
> 

이 자습서를 통해 [Azure App Service](../app-service/app-service-value-prop-what-is.md)에 첫 번째 PHP 웹앱을 배포합니다.
App Service를 사용하여 웹앱, [모바일 앱 백 엔드](/documentation/learning-paths/appservice-mobileapps/) 및 [API 앱](../app-service-api/app-service-api-apps-why-best-platform.md)을 만들 수 있습니다.

다음을 수행합니다. 

* Azure App Service에서 웹앱을 만듭니다.
* 샘플 PHP 코드를 배포합니다.
* 프로덕션 환경에서 라이브로 코드 실행을 참조하세요.
* [Git 커밋을 푸시](https://git-scm.com/docs/git-push)하는 것과 똑같은 방식으로 웹앱을 업데이트합니다.

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="cli-versions-to-complete-the-task"></a>태스크를 완료하기 위한 CLI 버전

다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

- [Azure CLI 1.0](app-service-web-get-started-php-cli-nodejs.md) - 클래식 및 리소스 관리 배포 모델용 CLI
- [Azure CLI 2.0(미리 보기)](app-service-web-get-started-php.md) - 리소스 관리 배포 모델용 차세대 CLI

## <a name="prerequisites"></a>필수 조건
* [Git](http://www.git-scm.com/downloads)
* [Azure CLI](../xplat-cli-install.md).
* Microsoft Azure 계정. 계정이 없는 경우 [무료 평가판을 등록](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)하거나 [Visual Studio 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)할 수 있습니다.

> [!NOTE]
> Azure 계정 없이 [App Service를 체험](https://azure.microsoft.com/try/app-service/)할 수 있습니다. 시작 앱을 만들고 최대 한 시간 동안 해당 앱을 사용하여 재생합니다. -- 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.
> 
> 

## <a name="deploy-a-php-web-app"></a>PHP 웹앱 배포
1. 새 Windows 명령 프롬프트, PowerShell 창, Linux 셸 또는 OS X 터미널을 엽니다. `git --version` 및 `azure --version`를 실행하여 Git 및 Azure CLI가 컴퓨터에 설치되어 있는지 확인합니다.
   
    ![Azure에서 첫 번째 웹앱에 CLI 도구가 설치되는지를 테스트합니다.](./media/app-service-web-get-started/1-test-tools.png)
   
    도구를 설치하지 않은 경우 다운로드 링크는 [필수 구성 요소](#Prerequisites) 를 참조하세요.
2. 다음과 같이 Azure에 로그인합니다.
   
        azure login
   
    로그인 프로세스를 계속하려면 도움말 메시지를 따릅니다.
   
    ![Azure에 로그인하여 첫 번째 웹앱을 만듭니다.](./media/app-service-web-get-started/3-azure-login.png)
3. Azure CLI를 ASM 모드로 변경한 다음 App Service에 대한 배포 사용자를 설정합니다. 나중에 자격 증명을 사용하여 코드를 배포합니다.
   
        azure config mode asm
        azure site deployment user set --username <username> --pass <password>
4. 작업 디렉터리(`CD`)를 변경하고 샘플 PHP 앱을 다음과 같이 복제합니다.
   
        git clone https://github.com/Azure-Samples/app-service-web-php-get-started.git
5. 샘플 앱의 리포지토리로 변경합니다. 예:
   
        cd app-service-web-php-get-started
6. 고유한 앱 이름과 이전에 구성한 배포 사용자를 사용하여 Azure에서 App Service 응용 프로그램 리소스를 만듭니다. 대화 상자가 나타나면 원하는 지역의 수를 지정합니다.
   
        azure site create <app_name> --git --gitusername <username>
   
    ![Azure에서 첫 번째 웹앱에 Azure 리소스를 만듭니다.](./media/app-service-web-get-started-languages/php-site-create.png)
   
    이제 Azure에서 앱이 생성되었습니다. 또한, 현재 디렉터리가 Git 초기화되어 새로운 앱 서비스 앱에 Git 원격으로 연결됩니다.
    앱 URL(http://&lt;app_name>.azurewebsites.net)을 검색하면 기본 HTML 페이지를 볼 수 있지만 지금은 이 URL에서 사용자 고유의 코드를 가져옵니다.
7. Git로 코드를 푸시하듯이 새 Azure 앱에 샘플 코드를 배포합니다. 메시지가 표시되면 이전에 구성한 암호를 사용합니다.
   
        git push azure master
   
    ![Azure에서 첫 번째 웹앱에 코드를 푸시합니다.](./media/app-service-web-get-started-languages/php-git-push.png)
   
    `git push`는 Azure에 코드를 배치할 뿐만 아니라 배포 엔진에서 배포 작업을 트리거합니다. 또한 PHP 앱의 composer.json 파일을 자동으로 처리하도록  [작성기 확장](web-sites-php-mysql-deploy-use-git.md#composer) 을 설정할 수 있습니다.

축하합니다. Azure 앱 서비스에 앱을 배포하셨습니다.

## <a name="see-your-app-running-live"></a>실시간으로 실행 중인 앱 확인
Azure에서 라이브로 실행 중인 앱을 보려면 리포지토리의 디렉터리에서 이 명령을 실행합니다.

    azure site browse

## <a name="make-updates-to-your-app"></a>앱 업데이트
이제 언제든지 Git를 사용하여 프로젝트(리포지토리) 루트에서 푸시하여 라이브 사이트를 업데이트할 수 있습니다. 사용자의 코드를 처음으로 배포했을 때와 같은 방식으로 수행합니다. 예를 들어 로컬에서 테스트한 새로운 변경 내용을 푸시하고 싶을 때마다 프로젝트(리포지토리) 루트에서 다음 명령을 실행하기만 하면 됩니다.

    git add .
    git commit -m "<your_message>"
    git push azure master

## <a name="next-steps"></a>다음 단계
[Azure에 Laravel 웹앱을 만들고 구성하여 배포합니다](app-service-web-php-get-started-cli-nodejs.md). 이 자습서를 따라 수행하면 다음과 같이 Azure에서 PHP 웹앱을 실행하는 데 필요한 기본 기술을 배우게 됩니다.

* PowerShell/Bash로부터 Azure의 앱을 만들어 구성합니다.
* PHP 버전을 설정합니다.
* 루트 응용 프로그램 디렉터리에 있지 않은 시작 파일을 사용합니다.
* Composer 자동화를 사용합니다.
* 특정 환경 변수에 액세스합니다.
* 일반적인 오류 문제를 해결합니다.

또는 첫 번째 웹앱으로 더 많은 작업을 수행합니다. 예:

* [사용자의 코드를 Azure에 배포하는 다른 방법](web-sites-deploy.md)을 시도해 보세요. 예를 들어 GitHub 리포지토리 중 하나에서 배포하려면 **배포 옵션**에서 **로컬 Git 리포지토리** 대신에 **GitHub**를 선택합니다.
* 다음 수준으로 Azure 앱을 이동합니다. 사용자를 인증합니다. 요구에 따라 규모를 조정합니다. 몇 가지 성능 경고를 설정합니다. 이 모든 작업이 클릭 몇 번으로 가능합니다. [첫 번째 웹앱에 기능 추가](app-service-web-get-started-2.md)를 참조하세요.




<!--HONumber=Jan17_HO3-->


