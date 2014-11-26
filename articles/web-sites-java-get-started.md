<properties linkid="develop-java-tutorials-web-site-get-started" urlDisplayName="Get started with Azure" pageTitle="Get started with Microsoft Azure Websites using Java" metaKeywords="" description="This tutorial shows you how to deploy a Java website to Microsoft Azure." metaCanonical="" services="web-sites" documentationCenter="Java" title="Get started with Azure and Java" videoId="" scriptId="" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# Azure 웹 사이트 및 Java 시작

이 자습서에서는 Azure 응용 프로그램 갤러리 또는 Azure 웹 사이트 구성 UI를 사용하여 Java를 사용하는 Microsoft Azure에서 웹 사이트를 만드는 방법을 보여 줍니다.

이러한 기술을 사용하지 않으려는 경우 예를 들어 응용 프로그램 컨테이너를 사용자 지정하려는 경우 [Azure에 사용자 지정 Java 웹 사이트 업로드][Azure에 사용자 지정 Java 웹 사이트 업로드]를 참조하십시오.

> [WACOM.NOTE] 이 자습서를 완료하려면 Microsoft Azure 계정이 필요합니다. 계정이 없는 경우 [MSDN 구독자 혜택을 활성화][MSDN 구독자 혜택을 활성화]하거나 [무료 평가판을 등록][무료 평가판을 등록]할 수 있습니다.

# Azure 응용 프로그램 갤러리를 사용하여 Java 웹 사이트 만들기

이 정보는 Azure 응용 프로그램 갤러리를 사용하여 웹 사이트의 Java 응용 프로그램 컨테이너인 Apache Tomcat 또는 Jetty를 선택하는 방법을 보여 줍니다.

다음은 응용 프로그램 갤러리에서 Tomcat을 사용하여 빌드한 웹 사이트를 표시하는 방법을 보여 줍니다.

![Apache Tomcat을 사용하는 웹 사이트][Apache Tomcat을 사용하는 웹 사이트]

다음은 응용 프로그램 갤러리에서 Jetty를 사용하여 빌드한 웹 사이트를 표시하는 방법을 보여 줍니다.

![Jetty를 사용하는 웹 사이트][Jetty를 사용하는 웹 사이트]

1.  Microsoft Azure 관리 포털에 로그인합니다.
2.  **새로 만들기**를 클릭하고, **계산**, **웹 사이트**, **갤러리에서**를 차례로 클릭합니다.
3.  앱 목록에서 Java 응용 프로그램 서버(예: **Apache Tomcat** 또는 **Jetty**) 중 하나를 선택합니다.
4.  **다음**을 클릭합니다.
5.  URL 이름을 지정합니다.
6.  지역을 선택합니다. 예를 들면 **미국 서부**를 선택합니다.
7.  **완료**를 클릭합니다.

잠시 후 웹 사이트가 만들어집니다. 웹 사이트를 보려면 Azure 관리 포털의 **웹 사이트** 보기에서 상태가 **실행 중**으로 표시될 때까지 기다린 후 웹 사이트의 URL을 클릭합니다.

앱 컨테이너로 웹 사이트를 만들었으므로 **다음 단계** 섹션에서 웹 사이트에 응용 프로그램 업로드에 대한 자세한 내용을 알아보세요.

# Azure 구성 UI를 사용하여 Java 웹 사이트 만들기

이 정보는 Azure 구성 UI를 사용하여 웹 사이트의 Java 응용 프로그램 컨테이너인 Apache Tomcat 또는 Jetty를 선택하는 방법을 보여 줍니다.

1.  Microsoft Azure 관리 포털에 로그인합니다.
2.  **새로 만들기**를 클릭하고, **계산**, **웹 사이트**, **빠른 생성**을 차례로 클릭합니다.
3.  URL 이름을 지정합니다.
4.  지역을 선택합니다. 예를 들면 **미국 서부**를 선택합니다.
5.  **완료**를 클릭합니다. 잠시 후 웹 사이트가 만들어집니다. 웹 사이트를 보려면 Azure 관리 포털의 **웹 사이트** 보기에서 상태가 **실행 중**으로 표시될 때까지 기다린 후 웹 사이트의 URL을 클릭합니다.
6.  여전히 Azure 관리 포털의 **웹 사이트** 보기에서 웹 사이트 이름을 클릭하여
    대시보드를 엽니다.
7.  **구성**을 클릭합니다.
8.  **일반** 섹션에서 사용 가능한 버전을 클릭하여 **Java**를 사용하도록 설정합니다.
9.  웹 컨테이너의 옵션(예: Tomcat 및 Jetty)이 표시됩니다. 사용할 웹 컨테이너를 선택합니다.
10. **저장**을 클릭합니다.

잠시 후 웹 사이트가 Java 기반이 됩니다. 해당 웹 사이트가 Java 기반인지 확인하려면 Azure 관리 포털의 **웹 사이트** 보기에서 상태가 **실행 중**으로 표시될 때까지 기다린 후 웹 사이트의 URL을 클릭합니다. 페이지에서 새로운 사이트가 Java 기반 웹 사이트라는 텍스트를 표시합니다.

앱 컨테이너로 웹 사이트를 만들었으므로 **다음 단계** 섹션에서 웹 사이트에 응용 프로그램 업로드에 대한 자세한 내용을 알아보세요.

# 다음 단계

이제 Azure에서 Java 웹 사이트로 실행되는 Java 응용 프로그램 서버가 생성되었습니다. 고유한 응용 프로그램 또는 웹 페이지를 추가하려면 [Java 웹 사이트에 응용 프로그램 또는 웹 페이지 추가][Java 웹 사이트에 응용 프로그램 또는 웹 페이지 추가]를 참조하십시오.

  [Azure에 사용자 지정 Java 웹 사이트 업로드]: ../web-sites-java-custom-upload
  [MSDN 구독자 혜택을 활성화]: /ko-KR/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
  [무료 평가판을 등록]: /ko-KR/pricing/free-trial/?WT.mc_id=A261C142F
  [Apache Tomcat을 사용하는 웹 사이트]: ./media/web-sites-java-get-started/tomcat.png
  [Jetty를 사용하는 웹 사이트]: ./media/web-sites-java-get-started/jetty.png
  [Java 웹 사이트에 응용 프로그램 또는 웹 페이지 추가]: ../web-sites-java-add-app
