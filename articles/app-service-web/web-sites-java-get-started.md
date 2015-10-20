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
	ms.date="08/31/2015"
	ms.author="robmcm"/>

# Azure 앱 서비스에서 Java 웹 앱 만들기

> [AZURE.SELECTOR]
- [.Net](web-sites-dotnet-get-started.md)
- [Node.js](web-sites-nodejs-develop-deploy-mac.md)
- [Java](web-sites-java-get-started.md)
- [PHP - Git](web-sites-php-mysql-deploy-use-git.md)
- [PHP - FTP](web-sites-php-mysql-deploy-use-ftp.md)
- [Python](web-sites-python-ptvs-django-mysql.md)

이 자습서에서는 Azure 마켓플레이스 또는 [Azure 앱 서비스의 웹앱 기능][]에 있는 구성 UI를 통해 Java를 사용하여 Microsoft Azure에서 웹앱을 만드는 방법을 보여 줍니다.

이러한 기술을 사용하지 않으려는 경우(예: 응용 프로그램 컨테이너를 사용자 지정하려는 경우) [Azure에 사용자 지정 Java 웹앱 업로드](web-sites-java-custom-upload.md)를 참조하세요.

> [AZURE.NOTE]이 자습서를 완료하려면 Microsoft Azure 계정이 필요합니다. 계정이 없는 경우 [MSDN 구독자 혜택을 활성화][]하거나 [무료 평가판을 등록][]할 수 있습니다.

Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려는 경우 [앱 서비스 평가][]로 이동하세요. 여기서 신용 카드와 약정 없이 앱 서비스에서 수명이 짧은 스타터 웹앱을 즉시 만들 수 있습니다.

## Azure 마켓플레이스를 사용하여 Java 웹앱 만들기

이 정보는 Azure 마켓플레이스를 사용하여 웹 앱의 Java 응용 프로그램 컨테이너인 Apache Tomcat 또는 Jetty를 선택하는 방법을 보여 줍니다.

다음은 Azure 마켓플레이스에서 Tomcat을 사용하여 빌드한 웹앱이 표시되는 방식을 보여 줍니다.

<!--todo:![Web app using Apache Tomcat](./media/web-sites-java-get-started/tomcat.png)-->

다음은 Azure 마켓플레이스에서 Jetty를 사용하여 빌드한 웹앱이 표시되는 방식을 보여 줍니다.

<!--todo:![Web app using Jetty](./media/web-sites-java-get-started/jetty.png)-->

1. [Azure 포털](http://go.microsoft.com/fwlink/?LinkId=529715)에 로그인합니다.
2. 페이지의 왼쪽 아래에서 **새로 만들기**를 클릭합니다.
3. **웹 + 모바일** 블레이드를 클릭합니다.
4. **웹 + 모바일** 블레이드 하단에서 **Azure 마켓플레이스**를 클릭합니다.
5. **웹**을 클릭합니다.
6. **웹** 페이지 상단에는 검색 텍스트 상자가 있습니다. 이 텍스트 상자에 **Apache Tomcat** 또는 **Jetty**와 같이 원하는 Java 응용 프로그램 서버를 입력합니다.
4. 원하는 Java 응용 프로그램 서버를 클릭합니다.
5. **만들기**를 클릭합니다.
6. URL 이름을 지정합니다.
6. 지역을 선택합니다. 예를 들어 **미국 서부**를 선택합니다.
7. **만들기**를 클릭합니다.

잠시 후 웹 앱이 만들어집니다. 웹앱을 보려면 Azure 포털 내 **웹앱** 블레이드에서 웹앱을 클릭한 다음 해당 URL을 클릭합니다.

앱 컨테이너로 웹 앱을 만들었으므로 **다음 단계** 섹션에서 웹 사이트에 응용 프로그램 업로드에 대한 자세한 내용을 알아보십시오.

## Azure 구성 UI를 사용하여 Java 웹앱 만들기

이 정보는 Azure 구성 UI를 사용하여 웹 앱의 Java 응용 프로그램 컨테이너인 Apache Tomcat 또는 Jetty를 선택하는 방법을 보여 줍니다.

1. Azure 포털에 로그인합니다.
2. 페이지의 왼쪽 아래에서 **새로 만들기**를 클릭합니다.
3. **웹 + 모바일** 블레이드를 클릭합니다.
4. **웹 + 모바일** 블레이드 하단에서 **Azure 마켓플레이스**를 클릭합니다.
5. **웹**을 클릭합니다.
6. **웹 앱**을 클릭합니다.
7. **만들기**를 클릭합니다.
8. URL 이름을 지정합니다.
9. 지역을 선택합니다. 예를 들어 **미국 서부**를 선택합니다.
10. **만들기**를 클릭합니다.
11. 웹 앱을 만들면 **모든 설정**을 클릭합니다.
12. **응용 프로그램 설정**을 클릭합니다.
13. 원하는 Java 버전을 클릭합니다.
14. 웹 컨테이너의 옵션(예: Tomcat 및 Jetty)이 표시됩니다. 원하는 **웹 컨테이너**를 선택합니다.
15. **Save**를 클릭합니다.

잠시 후 웹 앱이 Java 기반이 됩니다. Java 기반임을 확인하려면 해당 URL을 클릭합니다. 페이지에서 새 웹 앱이 Java 기반 웹 앱이라는 텍스트를 표시합니다.

앱 컨테이너로 웹 앱을 만들었으므로 **다음 단계** 섹션에서 웹 사이트에 응용 프로그램 업로드에 대한 자세한 내용을 알아보십시오.

## 다음 단계

이제 Azure에서 Java 웹 앱으로 실행되는 Java 응용 프로그램 서버가 생성되었습니다. 고유한 응용 프로그램 또는 웹 페이지를 추가하려면 [Java 웹앱에 응용 프로그램 또는 웹 페이지 추가](web-sites-java-add-app.md)를 참조하세요.

자세한 내용은 [Java개발자 센터](/develop/java/)를 참조하세요.

## 변경된 내용

* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스 및 기존 Azure 서비스][]를 참조하세요.
* 이전 포털에서 새 포털로의 변경에 대한 지침은 [Azure 포털 탐색에 대한 참조][]를 참조하세요.

<!-- External Links -->
[MSDN 구독자 혜택을 활성화]: http://go.microsoft.com/fwlink/?LinkId=623901
[무료 평가판을 등록]: http://go.microsoft.com/fwlink/?LinkId=623901
[Azure 앱 서비스의 웹앱 기능]: http://go.microsoft.com/fwlink/?LinkId=529714
[앱 서비스 평가]: http://go.microsoft.com/fwlink/?LinkId=523751
[Azure 앱 서비스 및 기존 Azure 서비스]: http://go.microsoft.com/fwlink/?LinkId=529714
[Azure 포털 탐색에 대한 참조]: http://go.microsoft.com/fwlink/?LinkId=529715

<!---HONumber=Oct15_HO3-->