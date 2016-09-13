<properties
	pageTitle="5분 내에 Azure에 첫 번째 웹앱 배포 | Microsoft Azure"
	description="몇 단계를 통해 샘플 앱을 배포하여 앱 서비스에서 웹앱을 실행하는 작업이 얼마나 쉬운지 알아봅니다. 5분 내에 실제 개발을 시작하고 즉시 결과를 확인하세요."
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""
/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="05/12/2016"
	ms.author="cephalin"
/>

# 5분 내에 Azure에 첫 번째 웹앱 배포

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

이 자습서를 통해 [Azure 앱 서비스](../app-service/app-service-value-prop-what-is.md)에 첫 번째 웹앱을 배포합니다. App Service를 사용하여 웹앱, [모바일 앱 백 엔드](/documentation/learning-paths/appservice-mobileapps/) 및 [API Apps](../app-service-api/app-service-api-apps-why-best-platform.md)을 만들 수 있습니다.

사용자는 최소한의 작업으로 다음을 수행할 수 있습니다.

- 샘플 웹 앱을 배포합니다(ASP.NET, PHP, Node.js, Java 또는 Python 중 선택).
- 잠시 후 실시간으로 실행 중인 앱을 확인합니다.
- [Git 커밋을 푸시](https://git-scm.com/docs/git-push)하는 것과 똑같은 방식으로 웹앱을 업데이트합니다.

또한 처음으로 [Azure 포털](https://portal.azure.com)을 살펴보고 제공되는 기능을 알아봅니다.

## 필수 조건

- [Git를 설치](http://www.git-scm.com/downloads)합니다.
- [Azure CLI 설치](../xplat-cli-install.md)
- Microsoft Azure 계정을 가져옵니다. 계정이 없는 경우 [무료 평가판을 등록](/pricing/free-trial/?WT.mc_id=A261C142F)하거나 [Visual Studio 구독자 혜택을 활성화](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)할 수 있습니다.

>[AZURE.NOTE] 작업에서 웹앱을 확인합니다. [App Service 체험](http://go.microsoft.com/fwlink/?LinkId=523751)에서는 신용 카드와 약정 없이 수명이 짧은 스타터 앱을 즉시 만들 수 있습니다.

## 웹 앱 배포

Azure 앱 서비스에 웹 앱을 배포하겠습니다.

1. 새 Windows 명령 프롬프트, PowerShell 창, Linux 셸 또는 OS X 터미널을 엽니다. `git --version` 및 `azure --version`를 실행하여 Git 및 Azure CLI가 컴퓨터에 설치되어 있는지 확인합니다.

    ![Azure에서 첫 번째 웹앱에 CLI 도구가 설치되는지를 테스트합니다.](./media/app-service-web-get-started/1-test-tools.png)

    도구를 설치하지 않은 경우 다운로드 링크는 [필수 구성 요소](#Prerequisites)를 참조하세요.

1. 작업 디렉터리(`CD`)를 변경하고 샘플 앱을 다음과 같이 복제합니다.

        git clone <github_sample_url>

    ![Azure에서 첫 번째 웹앱에 앱 샘플 코드를 복제합니다.](./media/app-service-web-get-started/2-clone-sample.png)

    *&lt;github\_sample\_url>*의 경우 원하는 프레임워크에 따라 다음 URL 중 하나를 사용합니다.

    - JS: HTML + CSS + [https://github.com/Azure-Samples/app-service-web-html-get-started.git](https://github.com/Azure-Samples/app-service-web-html-get-started.git)
    - ASP.NET: [https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git](https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git)
    - PHP(CodeIgniter): [https://github.com/Azure-Samples/app-service-web-php-get-started.git](https://github.com/Azure-Samples/app-service-web-php-get-started.git)
    - Node.js (Express): [https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git](https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git)
    - Java: [https://github.com/Azure-Samples/app-service-web-java-get-started.git](https://github.com/Azure-Samples/app-service-web-java-get-started.git)
    - Python(Django): [https://github.com/Azure-Samples/app-service-web-python-get-started.git](https://github.com/Azure-Samples/app-service-web-python-get-started.git)

2. 샘플 앱의 리포지토리로 변경합니다. 예:

        cd app-service-web-html-get-started

3. 다음과 같이 Azure에 로그인합니다.

        azure login

    로그인 프로세스를 계속하려면 도움말 메시지를 따릅니다.

    ![Azure에 로그인하여 첫 번째 웹앱을 만듭니다.](./media/app-service-web-get-started/3-azure-login.png)

4. 다음 명령을 사용하여 Azure에 고유한 앱 이름을 가진 App Service 앱 리소스를 만듭니다. 대화 상자가 나타나면 원하는 지역의 수를 지정합니다.

        azure site create --git <app_name>

    ![Azure에서 첫 번째 웹앱에 Azure 리소스를 만듭니다.](./media/app-service-web-get-started/4-create-site.png)

    >[AZURE.NOTE] Azure 구독에 대한 배포 자격 증명을 전혀 설정하지 않은 경우 배포 자격 증명을 만들라는 메시지가 나타납니다. App Service는 Git 배포 및 FTP 로그인에만 Azure 계정 자격 증명이 아닌 이러한 자격 증명을 사용합니다.

    이제 Azure에서 앱이 생성되었습니다. 또한, 현재 디렉터리가 Git 초기화되어 새로운 앱 서비스 앱에 Git 원격으로 연결됩니다. 앱 URL(http://&lt;app_name>.azurewebsites.net)로 이동하여 기본 HTML 페이지를 볼 수 있지만, 지금은 거기에 사용자 고유의 코드를 가져와 보겠습니다.

4. Git로 코드를 푸시하듯이 새 App Service 앱에 샘플 코드를 배포합니다.

        git push azure master

    ![Azure에서 첫 번째 웹앱에 코드를 푸시합니다.](./media/app-service-web-get-started/5-push-code.png)

    언어 프레임워크 중 하나를 사용하는 경우 다양한 출력을 표시합니다. `git push`이 Azure에 코드를 배치할 뿐만 아니라 배포 엔진에서 배포 작업을 트리거하기 때문입니다. 프로젝트(리포지토리) 루트에 package.json(Node.js) 또는 requirements.txt(Python) 파일이 있거나 ASP.NET 프로젝트에 packages.config 파일이 있는 경우 배포 스크립트가 사용자에게 필요한 패키지를 복원합니다. 또한 PHP 앱의 composer.json 파일을 자동으로 처리하도록 [작성기 확장](web-sites-php-mysql-deploy-use-git.md#composer)을 설정할 수 있습니다.

축하합니다. Azure 앱 서비스에 앱을 배포하셨습니다.

## 실시간으로 실행 중인 앱 확인

Azure에서 라이브로 실행 중인 앱을 보려면 리포지토리의 디렉터리에서 이 명령을 실행합니다.

    azure site browse

## 앱 업데이트

이제 언제든지 Git를 사용하여 프로젝트(리포지토리) 루트에서 푸시하여 라이브 사이트를 업데이트할 수 있습니다. 앱을 처음으로 Azure에 배포했을 때와 같은 방식으로 수행합니다. 예를 들어 로컬에서 테스트한 새로운 변경 내용을 푸시하고 싶을 때마다 프로젝트(리포지토리) 루트에서 다음 명령을 실행합니다.

    git add .
    git commit -m "<your_message>"
    git push azure master

## Azure Portal에서 앱 확인

이제 Azure 포털로 이동하여 여러분이 만든 앱을 살펴보겠습니다.

1. Azure 구독을 보유한 Microsoft 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 표시줄에서 **App Services**를 클릭합니다.

3. 방금 만든 앱을 클릭하여 포털에서 해당 페이지([블레이드](../azure-portal-overview.md)라고 부르는)를 엽니다. 사용자 편의를 위해 기본적으로 **설정** 블레이드도 열립니다.

    ![Azure에서 첫 번째 웹앱의 포털 보기입니다.](./media/app-service-web-get-started/portal-view.png)

App Service 앱의 포털 블레이드는 앱을 구성, 모니터링, 보호하고 문제를 해결할 수 있는 다양한 설정과 도구를 표시합니다. 이 인터페이스에 익숙해지도록 잠시 시간을 내어 몇 가지 간단한 작업을 수행해 보세요. (작업 수는 스크린샷에 있는 수에 해당합니다.)

1. 앱을 중지합니다.
2. 앱을 다시 시작합니다.
3. **리소스 그룹** 링크를 클릭하여 리소스 그룹에 배포된 모든 리소스를 확인합니다.
4. **설정** > **속성**을 클릭하여 앱에 대한 다른 정보를 확인합니다.
5. **도구**를 클릭하여 앱을 모니터링하고 문제를 해결할 수 있는 유용한 도구에 액세스합니다.

## 다음 단계

- 다음 수준으로 Azure 앱을 이동합니다. 인증을 사용하여 앱을 보호할 수 있습니다. 요구에 따라 규모를 조정합니다. 몇 가지 성능 경고를 설정합니다. 이 모든 작업이 클릭 몇 번으로 가능합니다. [첫 번째 웹앱에 기능 추가](app-service-web-get-started-2.md)를 참조하세요.
- Git 및 Azure CLI 외에도 Azure에 웹앱을 배포하는 다른 여러 가지 방법이 있습니다. [Azure 앱 서비스에 앱 배포](../app-service-web/web-sites-deploy.md)을 참조하세요. 문서 맨 위에 있는 프레임워크를 선택하여 언어 프레임워크에 대한 선호되는 개발 및 배포 단계를 찾습니다.

<!---HONumber=AcomDC_0907_2016-->