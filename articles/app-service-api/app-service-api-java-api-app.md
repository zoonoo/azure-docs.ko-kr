<properties
	pageTitle="Azure 앱 서비스에서 Java API 앱 빌드 및 배포"
	description="Java API 앱 패키지를 만들고 Azure 엡 서비스에 배포하는 방법을 알아봅니다."
	services="app-service\api"
	documentationCenter="java"
	authors="pkefal"
	manager="mohisri" 
	editor="jimbe"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="get-started-article"
	ms.date="04/21/2015"
	ms.author="pakefali"/>

# Azure 앱 서비스에서 Java API 앱 빌드 및 배포

이 자습서에서는 Java 응용 프로그램을 만들고 [Git](http://git-scm.com)를 사용하여 Azure 앱 서비스 API 앱에 배포하는 방법을 보여 줍니다. 이 자습서의 지침은 Java를 실행할 수 있는 모든 운영 체제에 적용될 수 있습니다. 또한 이 자습서에서는 [Gradle](https://gradle.org)을 사용하여 Java 응용 프로그램에 대한 빌드 자동화 및 패키지 종속성 확인을 사용하도록 설정합니다. 마지막으로, [RESTEasy](http://resteasy.jboss.org/)를 사용하여 [JaxRS](https://jax-rs-spec.java.net/) 사양을 완전히 구현하는 RESTful 서비스를 만듭니다.

다음은 완성된 응용 프로그램의 스크린샷입니다.

![][sample-api-app-page]

## Azure 포털에서 API 앱 만들기

> [AZURE.NOTE]이 자습서를 완료하려면 Microsoft Azure 계정이 필요합니다. 계정이 없는 경우 [MSDN 구독자 혜택을 활성화](/pricing/member-offers/msdn-benefits-details/)하거나 [무료 평가판을 등록](/pricing/free-trial/)할 수 있습니다. 또한 무료 [앱 서비스 앱 샘플](http://tryappservice.azure.com)을 사용해 볼 수 있습니다.

1. [Azure Preview 포털](https://portal.azure.com)에 로그인합니다.

2. 포털의 왼쪽 아래에서 **새로 만들기**를 클릭합니다.

3. **웹 + 모바일 > API 앱**을 클릭합니다.

	![][portal-quick-create]

4. **이름** 값(예: JavaAPIApp)을 입력합니다.

5. 앱 서비스 계획을 선택하거나 새로 만듭니다. 새 계획을 만드는 경우 가격 책정 계층, 위치 및 기타 옵션을 선택합니다.

	![][portal-create-api]

6. **만들기**를 클릭합니다.

	![][api-app-blade]

	**시작 보드에 추가** 확인란을 선택된 상태로 두면 API 앱의 블레이드를 만든 후 포털에서 해당 블레이드가 자동으로 열립니다. 이 확인란의 선택을 취소한 경우 포털 홈 페이지에서 **알림**을 클릭하여 API 앱 만들기 상태를 보고, 알림을 클릭하여 새 API 앱의 블레이드로 이동합니다.

7. **설정 > 응용 프로그램 설정**을 클릭합니다.

9. 액세스 수준을 **공용(익명)**으로 설정합니다.

11. **Save**를 클릭합니다.

	![][set-api-app-access-level]

## 새 API 앱에 Git 게시 사용

[Git](http://git-scm.com)는 Azure 웹 사이트를 배포하는 데 사용할 수 있는 분산된 버전 제어 시스템입니다. API 앱에 대해 작성한 코드를 로컬 Git 리포지토리에 저장하고, 원격 리포지토리로 푸시하여 Azure에 코드를 배포합니다. API 앱은 웹앱을 기반으로 하기 때문에 이 배포 방법은 API 앱에서 사용할 수 있는 앱 서비스 웹앱의 기능입니다. Azure 앱 서비스의 API 앱은 웹 서비스를 호스트하는 추가 기능이 있는 웹앱입니다.

포털에서는 **API 앱** 블레이드에서 API 앱 관련 기능을 관리하고, **API 앱 호스트** 블레이드에서 웹앱과 공유되는 기능을 관리할 수 있습니다. 따라서 이 섹션에서는 **API 앱 호스트** 블레이드로 이동하여 Git 배포 기능을 구성합니다.

1. API 앱 블레이드에서 **API 앱 호스트**를 클릭합니다.

	![][api-app-host]

2. **API 앱** 블레이드의 **배포** 섹션을 찾아 **연속 배포 설정**을 클릭합니다. 블레이드의 이 부분을 보려면 아래로 스크롤해야 할 수도 있습니다.

	![][deployment-part]

3. **원본 선택 > 로컬 Git 리포지토리**를 클릭합니다.

5. **확인**을 클릭합니다.

	![][setup-git-publishing]

6. 이전에 API 앱 또는 다른 앱 서비스 앱을 게시하기 위해 배포 자격 증명을 설정하지 않은 경우 지금 설정합니다.

	* **배포 자격 증명 설정**을 클릭합니다.

	* 사용자 이름 및 암호를 만듭니다.

	* **Save**를 클릭합니다.

	![][deployment-credentials]

1. **API 앱 호스트** 블레이드에서 **설정 > 속성**을 클릭합니다. 배포할 원격 Git 리포지토리의 URL이 "GIT URL" 아래에 표시됩니다.

2. 이 자습서의 뒷부분에서 사용하기 위해 URL을 복사합니다.

	![][git-url]

## 새 API 앱에서 Java 런타임 사용

API 앱에서 Java 앱을 성공적으로 호스트할 수 있도록 하려면 Java 런타임을 설정하고 응용 프로그램 서버를 선택해야 합니다. 포털에서 이 작업을 간편하게 수행할 수 있습니다. 이 예제에서는 Java 7 및 Jetty에서 응용 프로그램을 호스트하도록 설정합니다.

1. API 앱 블레이드에서 **API 앱 호스트**를 클릭합니다.

	![][api-app-host]

2. **설정 > 응용 프로그램 설정**을 클릭합니다. Java를 사용하도록 설정하고 응용 프로그램 서버로 Jetty를 선택합니다. **저장**을 클릭합니다.

	![][api-app-enable-java]

그러면 API 앱에서 **Java 런타임이 설정**되고 사이트의 루트에 **webapps/** 폴더가 생성됩니다. 이 폴더에는 응용 프로그램의 모든.war 파일이 들어 있습니다.

## Java API 앱에 대한 코드 다운로드 및 검사

이 섹션에서는 JavaAPIApp 샘플의 일부분으로 제공되는 코드를 다운로드하여 살펴봅니다.

1. [이 GitHub 리포지토리](http://go.microsoft.com/fwlink/?LinkId=571009)에서 코드를 다운로드합니다. 리포지토리를 복제하거나 **Zip 다운로드**를 클릭하여 .zip 파일로 다운로드할 수 있습니다. .zip 파일을 다운로드한 경우 로컬 디스크에서 압축을 풉니다.

2. 샘플의 압축을 푼 폴더로 이동한 후 `build\libs` 폴더로 이동합니다.

	![][api-app-folder-browse]

3. 텍스트 편집기에서 **apiapp.json** 파일을 열고 내용을 검사합니다.

	![][apiapp-json]

	Azure 앱 서비스에서 Java 응용 프로그램을 API 앱으로 인식하기 위한 두 가지 전제 조건이 있습니다.

	+ *apiapp.json*이라는 파일이 응용 프로그램의 루트 디렉터리에 있어야 합니다.
	+ Swagger 2.0 메타데이터 끝점이 응용 프로그램에 의해 노출되어야 합니다. 이 끝점의 URL은 *apiapp.json* 파일에 지정되어 있습니다.

	**apiDefinition** 속성을 확인합니다. 이 URL의 경로는 API의 URL에 상대적이며, Swagger 2.0 끝점을 가리킵니다. Azure 앱 서비스에서는 이 속성을 사용하여 API의 정의를 검색하고 다양한 앱 서비스 API 앱 기능을 사용하도록 설정합니다.

4. `src\main\java\com\microsoft\trysamples\javaapiapp`으로 이동하여 **App.java** 파일을 열고 코드를 검사합니다.

	![][app-java]

	이 코드는 JaxRS용 Swagger 패키지를 사용하여 Swagger 2.0 끝점을 만듭니다.

		beanConfig.setVersion("1.0.0");
		beanConfig.setBasePath("/JavaAPIApp/api");
		beanConfig.setHost(websitehostname);
		beanConfig.setResourcePackage("com.microsoft.trysamples.javaapiapp");
		beanConfig.setSchemes(new String[]{"http", "https"});
		beanConfig.setScan(true);

	`setVersion` 메서드는 Swagger에서 제공하는 메타데이터에 API 버전을 설정합니다.

	`setBasePath` 메서드는 Swagger에서 메타데이터를 생성하는 데 사용하는 기본 경로를 설정합니다. 이 URL은 API 앱의 기본 경로에 상대적입니다.

	`setHost` 메서드는 API에서 수신 대기하는 호스트를 설정합니다. 이 예제에서는 로컬로 실행되는 경우 `localhost`, 응용 프로그램이 Azure 앱 서비스에서 실행되는 경우 API 앱 호스트 이름으로 동적으로 설정되도록 미리 몇 줄을 할당해 둔 `websitehostname` 변수를 사용합니다.

	`setResourcePackage` 메서드는 Swagger에서 검색하여 API 메타데이터가 포함된 Swagger.json 파일에 포함할 패키지를 설정합니다.

	`setSchemes` 메서드는 지원되는 스키마를 정의합니다.

	`setScan` 메서드는 Swagger에서 앱 설명서를 생성하도록 합니다.

	RESTEasy를 사용할 때 Swagger의 출력을 사용자 지정하는 데 사용할 수 있는 추가 메서드는 Swagger의 [Wiki 페이지](https://github.com/swagger-api/swagger-core/wiki/Swagger-Core-RESTEasy-2.X-Project-Setup-1.5#using-swaggers-beanconfig)에서 확인할 수 있습니다.

	> [AZURE.NOTE]Swagger 메타데이터 파일은 `/JavaAPIApp/api/swagger.json`에서 액세스할 수 있습니다.

## 로컬로 API 앱 코드 실행

이 섹션에서는 응용 프로그램을 배포하기 전에 로컬로 실행하여 작동 상태를 확인합니다.

1. 샘플을 다운로드한 폴더로 이동합니다.

2. 명령줄 프롬프트를 열고 다음 명령을 입력합니다.

		gradlew.bat

3. 명령이 완료되면 다음 명령을 입력합니다.

		gradlew.bat jettyRunWar

	명령줄 창 출력에 다음이 표시됩니다.

		17:25:49 INFO  JavaAPIApp runs at:
		17:25:49 INFO    http://localhost:8080/JavaAPIApp

5. 브라우저에서 `http://localhost:8080/JavaAPIApp/`로 이동합니다.

	다음 페이지가 표시됩니다.

	![][sample-api-app-page]

6. Swagger.json 파일을 보려면 `http://localhost:8080/JavaAPIApp/api/Swagger.json`로 이동합니다.

## Azure 앱 서비스에 API 앱 코드 게시

이 섹션에서는 Azure 앱 서비스에서 실행되는 API 앱에 샘플 응용 프로그램을 배포하기 위해 로컬 Git 리포지토리를 만들고 해당 리포지토리에서 Azure에 푸시합니다.

1. Git가 설치되지 않은 경우 [Git 다운로드 페이지](http://git-scm.com/download)에서 설치합니다.

1. 명령줄에서 샘플 응용 프로그램 디렉터리와 `build\libs`로 이동한 후 다음 명령을 입력하여 로컬 Git 리포지토리를 초기화합니다.

		git init


2. 다음 명령을 입력하여 리포지토리에 파일을 추가합니다.

		git add .
		git commit -m "Initial commit of the API App"

3. 앞에서 복사한 Git URL을 사용하여 이전에 만든 웹앱(API 앱 호스트)에 업데이트를 푸시하기 위한 원격 참조를 만듭니다.

		git remote add azure [URL for remote repository]

4. 다음 명령을 입력하여 변경 내용을 Azure에 푸시합니다.

		git push azure master

	이전에 만든 암호를 입력하라는 메시지가 나타납니다.

	이 명령의 출력은 배포에 성공했다는 메시지로 종료됩니다.

		remote: Deployment successful.
		To https://user@testsite.scm.azurewebsites.net/testsite.git
	 	* [new branch]      master -> master

## Azure 포털에서 API 정의 보기

API 앱에 API를 배포했으므로 이제 Azure 포털에서 API 정의를 볼 수 있습니다. 먼저 Azure에서 API 앱의 API 정의가 변경된 것을 인식할 수 있 *게이트웨이*를 다시 시작합니다. 게이트웨이는 리소스 그룹의 API 앱에 대한 API 관리 및 권한 부여를 처리하는 웹앱입니다.

6. Azure 포털에서 이전에 만든 API 앱에 대한 **API 앱** 블레이드로 이동하여 **게이트웨이** 링크를 클릭합니다.

	![][click-gateway]

7. **게이트웨이** 블레이드에서 **다시 시작**을 클릭합니다. 이제 이 블레이드를 닫을 수 있습니다.

	![][restart-gateway]

8. **API 앱** 블레이드에서 **API 정의**를 클릭합니다.

	![][api-definition-click]

	**API 정의** 블레이드에 한 가지 Get 메서드가 표시됩니다.

	![][api-definition-blade]

## Azure에서 샘플 응용 프로그램 실행

Azure 포털에서 API 앱에 대한 **API 앱 호스트** 블레이드로 이동하여 **찾아보기**를 클릭합니다.

![][browse-api-app-page]

이전에 샘플 앱을 로컬로 실행할 때 표시된 홈 페이지가 브라우저에 표시됩니다.

## 다음 단계

API 앱 백 엔드를 사용하는 Java 웹 응용 프로그램을 Azure에 배포했습니다. Azure에서 Java를 사용하는 방법에 대한 자세한 내용은 [Java 개발자 센터](/develop/java/)를 참조하세요.

[앱 서비스 평가](http://tryappservice.azure.com)에서 이 샘플 API 앱을 사용해 볼 수 있습니다.

[portal-quick-create]: ./media/app-service-api-java-api-app/portal-quick-create.png
[portal-create-api]: ./media/app-service-api-java-api-app/portal-create-api.png
[api-app-blade]: ./media/app-service-api-java-api-app/api-app-blade.png
[api-app-folder-browse]: ./media/app-service-api-java-api-app/api-app-folder-browse.png
[api-app-host]: ./media/app-service-api-java-api-app/api-app-host.png
[deployment-part]: ./media/app-service-api-java-api-app/continuous-deployment.png
[set-api-app-access-level]: ./media/app-service-api-java-api-app/set-api-app-access.png
[setup-git-publishing]: ./media/app-service-api-java-api-app/local-git-repo.png
[deployment-credentials]: ./media/app-service-api-java-api-app/deployment-credentials.png
[git-url]: ./media/app-service-api-java-api-app/git-url.png
[apiapp-json]: ./media/app-service-api-java-api-app/apiapp-json.png
[app-java]: ./media/app-service-api-java-api-app/app-java.png
[sample-api-app-page]: ./media/app-service-api-java-api-app/sample-api-app-page.png
[browse-api-app-page]: ./media/app-service-api-java-api-app/browse-api-app-page.png
[api-app-enable-java]: ./media/app-service-api-java-api-app/api-app-enable-java.png
[click-gateway]: ./media/app-service-api-java-api-app/clickgateway.png
[restart-gateway]: ./media/app-service-api-java-api-app/gatewayrestart.png
[api-definition-click]: ./media/app-service-api-java-api-app/apidef.png
[api-definition-blade]: ./media/app-service-api-java-api-app/apidefblade.png
 

<!---HONumber=August15_HO6-->