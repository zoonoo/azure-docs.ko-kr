<properties
	pageTitle="Azure 앱 서비스에서 Java 웹앱 만들기 | Microsoft Azure"
	description="이 자습서에서는 Azure 앱 서비스에 Java 웹 앱을 배포하는 방법을 보여 줍니다."
	services="app-service\web"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor="jimbe"/>
<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="hero-article"
	ms.date="10/20/2015"
	ms.author="robmcm"/>

# Azure 앱 서비스에서 Java 웹 앱 만들기

> [AZURE.SELECTOR]
- [.Net](web-sites-dotnet-get-started.md)
- [Node.js](web-sites-nodejs-develop-deploy-mac.md)
- [Java](web-sites-java-get-started.md)
- [PHP - Git](web-sites-php-mysql-deploy-use-git.md)
- [PHP - FTP](web-sites-php-mysql-deploy-use-ftp.md)
- [Python](web-sites-python-ptvs-django-mysql.md)

이 자습서는 Azure Preview 포털을 사용하여 [Azure 앱 서비스에서 웹앱](http://go.microsoft.com/fwlink/?LinkId=529714)을 만드는 방법을 보여줍니다. Azure 마켓플레이스에서 웹앱 템플릿을 선택하거나 일반 웹앱을 만들고 Java용으로 수동으로 구성할 수 있습니다.

이러한 기술을 사용하지 않으려는 경우(예: 응용 프로그램 컨테이너를 사용자 지정하려는 경우) [Azure에 사용자 지정 Java 웹앱 업로드](web-sites-java-custom-upload.md)를 참조하세요.

> [AZURE.NOTE]이 자습서를 완료하려면 Microsoft Azure 계정이 필요합니다. 계정이 없는 경우 [MSDN 구독자 혜택을 활성화][]하거나 [무료 평가판을 등록][]할 수 있습니다.
>
> Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려는 경우 [앱 서비스 평가][]로 이동하세요. 여기서 신용 카드와 약정 없이 앱 서비스에서 수명이 짧은 스타터 웹앱을 즉시 만들 수 있습니다.

## Azure 마켓플레이스에서 웹앱 템플릿 선택

이 섹션에서는 Azure 마켓플레이스를 사용하여 Java 웹앱을 만드는 방법을 보여줍니다.

1. [Azure Preview 포털](https://portal.azure.com/)에 로그인합니다.

2. **새로 만들기 > 마켓플레이스**를 클릭합니다.

	![](./media/web-sites-java-get-started/newmarketplace.png)

3. **웹 + 모바일**을 클릭합니다.

	**마켓플레이스** 블레이드를 표시하고 **웹 + 모바일**을 선택하려면 왼쪽으로 스크롤해야 할 수 있습니다.

4. 검색 텍스트 상자에 **Apache Tomcat**, **Jetty**와 같은 Java 응용 프로그램 서버의 이름을 입력한 다음 Enter를 누릅니다.

5. 검색 결과에서 Java 응용 프로그램 서버를 클릭합니다.

	![](./media/web-sites-java-get-started/webmobilejetty.png)

6. 첫 번째 **Apache Tomcat** 또는 **Jetty** 블레이드에서 **만들기**를 클릭합니다.

	![](./media/web-sites-java-get-started/jettyblade.png)

7. 다음 **Apache Tomcat** 또는 **Jetty** 블레이드에서 **웹앱** 상자에 웹앱의 이름을 입력합니다.

	웹앱의 URL이 {name}.azurewebsites.net이기 때문에 이 이름은 azurewebsites.net 도메인에서 고유해야 합니다. 입력한 이름이 고유하지 않으면 빨간색 느낌표가 텍스트 상자에 나타납니다.

8. **리소스 그룹**을 선택하거나 새로 만듭니다.

	리소스 그룹에 대한 자세한 내용은 [Azure Preview 포털을 사용하여 Azure 리소스 관리](../resource-group-portal.md)를 참조하세요.

9. **앱 서비스 계획/위치**을 선택하거나 새로 만듭니다.

	앱 서비스 계획에 대한 자세한 내용은 [Azure 앱 서비스 계획 개요](../azure-web-sites-web-hosting-plans-in-depth-overview.md)를 참조하세요.

10. **만들기**를 클릭합니다.

	![](./media/web-sites-java-get-started/jettyportalcreate2.png)

	일반적으로 일 분 미만인 짧은 시간에 Azure는 새 웹앱 만들기를 마칩니다.

11. **웹앱 > {새로운 웹앱}**을 클릭합니다.

12. **URL**을 클릭하여 새 사이트를 찾습니다.

	![](./media/web-sites-java-get-started/jettyurl.png)

	Tomcat을 선택하는 경우 다음 예제와 유사한 페이지가 표시됩니다.

	![Apache Tomcat을 사용하는 웹앱](./media/web-sites-java-get-started/tomcat.png)

	Jetty를 선택하는 경우 다음 예제와 유사한 페이지가 표시됩니다.

	![Jetty를 사용하는 웹앱](./media/web-sites-java-get-started/jetty.png)

앱 컨테이너로 웹앱을 만들었으므로 [다음 단계](#next-steps) 섹션에서 응용 프로그램을 웹앱에 업로드 하는 방법을 알아보세요.

## 웹앱을 만들고 Java용으로 수동 구성하기

이 섹션은 웹앱을 만들고 Java용으로 수동으로 구성하는 방법을 보여줍니다.

1. [Azure Preview 포털](https://portal.azure.com/)에 로그인합니다.

2. **새로 만들기 > 웹 + 모바일**을 클릭합니다.


3. **웹 앱**을 클릭합니다.

4. **웹앱** 상자에서 웹앱에 대한 이름을 입력합니다.

	웹앱의 URL이 {name}.azurewebsites.net이기 때문에 이 이름은 azurewebsites.net 도메인에서 고유해야 합니다. 입력한 이름이 고유하지 않으면 빨간색 느낌표가 텍스트 상자에 나타납니다.

5. **리소스 그룹**을 선택하거나 새로 만듭니다.

	리소스 그룹에 대한 자세한 내용은 [Azure Preview 포털을 사용하여 Azure 리소스 관리](../resource-group-portal.md)를 참조하세요.

6. **앱 서비스 계획/위치**을 선택하거나 새로 만듭니다.

	앱 서비스 계획에 대한 자세한 내용은 [Azure 앱 서비스 계획 개요](../azure-web-sites-web-hosting-plans-in-depth-overview.md)를 참조하세요.

7. **만들기**를 클릭합니다.
 
8. 웹앱을 만들었으면 **웹앱 > {사용자의 웹앱}**을 클릭합니다.
 
9. **웹앱** 블레이드에서 **설정**을 클릭합니다.

10. **응용 프로그램 설정**을 클릭합니다.

11. 원하는 **Java 버전**을 클릭합니다.

12. 원하는 **웹 컨테이너**를 선택합니다.

13. **Save**를 클릭합니다.

	잠시 후 웹 앱이 Java 기반이 됩니다.

14. **웹앱 > {새로운 웹앱}**을 클릭합니다.

15. **URL**을 클릭하여 새 사이트를 찾습니다.

	웹 페이지에 Java 기반 웹앱을 만들었다는 확인 메시지가 표시됩니다.

## 다음 단계

이제 Azure 앱 서비스에서 Java 응용 프로그램 서버가 웹앱으로 실행됩니다. 웹앱에 직접 작성한 코드를 배포하려면 [Java 웹앱에 응용 프로그램 또는 웹 페이지 추가](web-sites-java-add-app.md)를 참조하세요.

Azure에서 Java 응용 프로그램을 개발하는 방법에 대한 자세한 내용은 [Java 개발자 센터](/develop/java/)를 참조하세요.

<!-- External Links -->
[MSDN 구독자 혜택을 활성화]: http://go.microsoft.com/fwlink/?LinkId=623901
[무료 평가판을 등록]: http://go.microsoft.com/fwlink/?LinkId=623901

[앱 서비스 평가]: http://go.microsoft.com/fwlink/?LinkId=523751

<!---HONumber=Nov15_HO1-->