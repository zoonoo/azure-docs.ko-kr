<properties 
	pageTitle="Eclipse에서 Azure용 Hello World 웹앱 만들기" 
	description="이 자습서에서는Eclipse용 Azure 도구 키트를 사용하여 Azure용 Hello World 웹앱을 만드는 방법을 보여 줍니다." 
	services="app-service\web" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="05/04/2016" 
	ms.author="robmcm"/>

# Eclipse에서 Azure용 Hello World 웹앱 만들기

이 자습서에서는 [Eclipse용 Azure 도구 키트]를 사용하여 기본 Hello World 응용 프로그램을 만들고 Azure에 웹앱으로 배포하는 방법을 보여 줍니다. 기본 JSP 예제는 편의를 위해 표시되지만 Azure 배포가 관련되는 한 아주 유사한 단계는 Java 서블릿에 적합합니다.

이 자습서를 완료한 경우 웹 브라우저에서 응용 프로그램을 보면 다음 그림과 같이 표시됩니다.

![][01]
 
## 필수 조건

* JDK(Java 개발자 키트), v 1.7 이상
* Eclipse IDE for Java EE Developers, Indigo 이상. <http://www.eclipse.org/downloads/>에서 다운로드할 수 있습니다.
* Java 기반 웹 서버 또는 응용 프로그램 서버의 배포(예: Apache Tomcat 또는 Jetty)
* Azure 구독(<https://azure.microsoft.com/ko-KR/free/> 또는 <http://azure.microsoft.com/pricing/purchase-options/>에서 구입할 수 있음)
* Eclipse용 Azure 도구 키트 자세한 내용은 [Eclipse용 Azure 도구 키트 설치]를 참조하세요.

## Hello World 응용 프로그램을 만들려면

먼저 java 프로젝트를 만듭니다.

1. Eclipse를 시작하고 메뉴에서 **File**, **New**, **Dynamic Web Project**를 차례로 클릭합니다. **File**, **New**를 차례로 클릭한 후 **Dynamic Web Project**가 사용 가능한 프로젝트로 표시되지 않는 경우 **File**, **New**, **Project...**를 차례로 클릭한 후 **Web**을 확장하고 **Dynamic Web Project**를 클릭한 후 **Next**를 클릭합니다.
1. 이 자습서에서는 프로젝트의 이름을 **MyHelloWorld**로 지정합니다. 화면이 다음과 유사하게 나타납니다. ![][02]
1. **마침**을 클릭합니다.
1. Eclipse의 Project Explorer 뷰 내에서 **MyHelloWorld**를 확장합니다. **WebContent**를 마우스 오른쪽 단추로 클릭하고 **New**를 클릭한 후 **JSP File**을 클릭합니다.
1. **New JSP File** 대화 상자에서 파일 이름을 **index.jsp**로 지정합니다. 부모 폴더를 **MyHelloWorld/WebContent**로 유지합니다.
1. **Select JSP Template** 대화 상자에서 이 자습서의 목적에 따라, **New JSP File (html)**을 선택한 후 **Finish**를 클릭합니다.
1. Eclipse에서 index.jsp 파일이 열리면 기존 `<body>` 요소 내에 **Hello World!**를 동적으로 표시하도록 텍스트를 추가합니다. 업데이트된 `<body>` 콘텐츠는 다음 예제와 유사하게 표시됩니다. `<body><b><% out.println("Hello World!"); %></b></body>` 
1. index.jsp를 저장합니다.

## Azure 웹앱 컨테이너에 응용 프로그램을 배포하려면

몇 가지 방법으로 Azure에 Java 웹 응용 프로그램을 배포할 수 있습니다. 이 자습서에서는 가장 간단한 방법 중 하나를 설명합니다. 즉, 특별한 프로젝트 형식이나 추가 도구 없이 Azure 웹앱 컨테이너에 응용 프로그램을 배포하는 방법을 설명합니다. JDK 및 웹 컨테이너 소프트웨어는 Azure에서 제공되므로 별도로 업로드할 필요가 없습니다. Java 웹앱만 있으면 됩니다. 따라서 응용 프로그램 게시 프로세스에 걸리는 시간이 몇 분이 아니라 몇 초입니다.

1. Eclipse의 Project Explorer에서 **MyHelloWorld**를 마우스 오른쪽 단추로 클릭합니다.

1. 상황에 맞는 메뉴에서 **Azure**를 선택하고 **Azure 웹앱으로 게시...**를 클릭합니다.

   ![][03]
   
   또는 프로젝트 탐색기에서 웹 응용 프로그램 프로젝트를 선택한 상태로 도구 모음에서 **게시** 드롭다운 단추를 클릭하고 다음에서 **Azure 웹앱으로 게시**를 선택할 수 있습니다.
   
   ![][publishDropdownButton]
   
1. Eclipse에서 Azure에 로그인하지 않은 경우 Azure 계정으로 로그인하라는 메시지가 표시됩니다.

   ![][04]
   
   참고: Azure 계정이 여러 개 있으면 로그인 프로세스 중에 일부 메시지가 동일한 것이더라도 두 번 이상 표시될 수 있습니다. 이 경우 로그인 지침에 따라 계속 진행합니다.
1. Azure 계정에 성공적으로 로그인하면 **구독 관리** 대화 상자에는 자격 증명과 연결된 구독 목록이 표시됩니다. 여러 구독이 나열된 경우 특정 하위 집합만 사용하려면 선택적으로 사용할 구독을 선택 취소할 수 있습니다. 구독을 선택했으면 **닫기**를 클릭합니다.

   ![][05]
   
1. **Azure 웹앱 컨테이너에 배포** 대화 상자가 나타나는 경우 이전에 만든 웹앱 컨테이너가 표시됩니다. 컨테이너를 만들지 않은 경우에는 목록이 비어 있습니다.

   ![][06]
   
1. 이전에 Azure 웹앱 컨테이너를 만들지 않은 경우 또는 응용 프로그램을 새 컨테이너에 게시하려는 경우 다음 단계를 사용합니다. 그렇지 않으면 기존 웹앱 컨테이너를 선택하고 아래의 7단계로 건너뜁니다.

  1. **새로 만들기...**를 클릭합니다.

  1. **새 웹앱 컨테이너** 대화 상자가 표시됩니다.

        ![][07]

  1. 웹앱 컨테이너에 **DNS 레이블**을 입력합니다. 이는 Azure에서 웹 응용 프로그램에 대한 호스트 URL의 리프 DNS 레이블을 형성합니다. 참고: 이름은 사용 가능해야 하며, Azure 웹앱 명명 요구 사항을 준수해야 합니다.

  1. **웹 컨테이너** 드롭다운 메뉴에서 응용 프로그램에 적절한 소프트웨어를 선택합니다.

        Currently, you can choose from Tomcat 8, Tomcat 7 or Jetty 9. A recent distribution of the selected software will be provided by Azure, and it will run on a recent distribution of JDK 8 created by Oracle and provided by Azure.

  1. **구독** 드롭다운 메뉴에서 이 배포에 사용할 구독을 선택합니다.

  1. **리소스 그룹** 드롭다운 메뉴에서 웹앱을 연결할 리소스 그룹을 선택합니다.

        Note: Azure Resource Groups allow you to group related resources together so that, for example, they can be deleted together.

        You can select an existing Resource Group (if you have any) and skip to step g below, or use the following these steps to create a new Resource Group:

      * **새로 만들기...**를 클릭합니다.

      * **새 리소스 그룹** 대화 상자가 표시됩니다.

            ![][08]

      * **이름** 텍스트 상자에서 새 리소스 그룹의 이름을 지정합니다.

      * **지역** 드롭다운 메뉴에서 리소스 그룹에 적절한 Azure 데이터 센터 위치를 선택합니다.

      * **확인**을 클릭합니다.

  1. **앱 서비스 계획** 드롭다운 메뉴에 선택한 리소스 그룹과 연결된 앱 서비스 계획이 나열됩니다.

        Note: An App Service Plan specifies information such as the location of your Web App, the pricing tier and the compute instance size. A single App Service Plan can be used for multiple Web Apps, which is why it is maintained separately from a specific Web App deployment.

        You can select an existing App Service Plan (if you have any) and skip to step h below, or use the following these steps to create a new App Service Plan:

      * **새로 만들기...**를 클릭합니다.

      * **새 앱 서비스 계획** 대화 상자가 표시됩니다.

            ![][09]

      * **이름** 텍스트 상자에서 새 앱 서비스 계획의 이름을 지정합니다.

      * **위치** 드롭다운 메뉴에서 계획에 적절한 Azure 데이터 센터 위치를 선택합니다.

      * **가격 책정 계층** 드롭다운 메뉴에서 계획에 적절한 가격 책정을 선택합니다. 테스트 목적으로 **Free**를 선택할 수 있습니다.

      * **인스턴스 크기** 드롭다운 메뉴에서 계획에 적절한 인스턴스 크기를 선택합니다. 테스트 목적으로 **Small**을 선택할 수 있습니다.

  1. 위 단계를 모두 완료한 경우 New Web App Container 대화 상자가 다음 그림과 유사하게 표시됩니다.

        ![][10]

  1. **확인**을 클릭하여 새 웹앱 컨테이너 만들기를 완료합니다.

        Wait a few seconds for the list of the Web App containers to be refreshed, and your newly-created web app container should now be selected in the list.

1. 이제 Azure에 웹앱의 초기 배포를 완료할 준비가 되었습니다.

    ![][11]

    **확인**을 클릭하여 선택한 웹앱 컨테이너에 Java 응용 프로그램을 배포합니다.

    참고: 기본적으로 응용 프로그램은 응용 프로그램 서버의 하위 디렉터리로 배포됩니다. 루트 응용 프로그램으로 배포하려면 **확인**을 클릭하기 전에 **루트에 배포** 확인란을 선택합니다.

1. 이제 웹앱의 배포 상태를 나타내는 **Azure 동작 로그** 보기를 확인합니다.

    ![][12]

    Azure에 웹앱을 배포하는 프로세스는 몇 초 내에 완료됩니다. 응용 프로그램이 준비되면 **상태** 열에 **게시됨**이라는 링크가 표시됩니다. 이 링크를 클릭하면 배포된 웹앱의 홈 페이지로 이동합니다.

## 웹앱 업데이트

실행 중인 기존 Azure 웹앱을 빠르고 쉽게 업데이트할 수 있으며, 두 가지 업데이트 옵션이 있습니다.

* 기존 Java 웹앱의 배포를 업데이트할 수 있습니다.
* 동일한 웹앱 컨테이너에 추가 Java 응용 프로그램을 게시할 수 있습니다.

두 경우 모두 프로세스는 동일하며 몇 초만 소요됩니다.

1. Eclipse Project Explorer에서 업데이트하거나 기존 웹앱 컨테이너에 추가할 Java 응용 프로그램을 마우스 오른쪽 단추로 클릭합니다.

2. 상황에 맞는 메뉴가 나타나면 **Azure**를 선택한 다음 **Azure 웹앱으로 게시...**를 선택합니다.

3. 이전에 이미 로그인했으므로 기존 웹앱 컨테이너 목록이 표시됩니다. Java 응용 프로그램을 게시하거나 다시 게시할 컨테이너를 선택하고 **확인**을 클릭합니다.

몇 초 후 **Azure 동작 로그** 보기에 업데이트된 배포가 **게시됨**로 표시되고, 웹 브라우저에서 업데이트된 응용 프로그램을 확인할 수 있습니다.

## 기존 웹앱 중지

기존 Azure 웹앱 컨테이너(여기에 배포된 모든 Java 응용 프로그램 포함)를 중지하려면 **Azure 탐색기** 보기를 사용하면 됩니다.

**Azure 탐색기** 보기가 열려 있지 않은 경우 Eclipse에서 **Window** 메뉴를 클릭한 다음 **보기 표시**, **기타...**, **Azure**, **Azure 탐색기**를 차례로 클릭합니다. 이전에 로그인하지 않은 경우 로그인하라는 메시지가 나타납니다.

**Azure 탐색기** 보기가 표시되면 다음 단계에 따라 웹앱을 중지합니다.

1. **Azure** 노드를 확장합니다.
2. **웹앱** 노드를 확장합니다. 
3. 원하는 웹앱을 마우스 오른쪽 단추로 클릭합니다.
5. 상황에 맞는 메뉴가 나타나면 **중지**를 클릭합니다. ![][13]

## 다음 단계

자세한 내용은 다음 링크를 참조하세요.

* [Java 개발자 센터](/develop/java/).
* [웹앱 개요](app-service-web-overview.md)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Eclipse용 Azure 도구 키트]: http://go.microsoft.com/fwlink/?LinkID=699529
[Eclipse용 Azure 도구 키트 설치]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[01]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/01-Web-Page.png
[02]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/02-Dynamic-Web-Project.png
[03]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/03-Context-Menu.png
[04]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/04-Log-In-Dialog.png
[05]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/05-Manage-Subscriptions-Dialog.png
[06]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/06-Deploy-To-Azure-Web-Container.png
[07]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/07-New-Web-App-Container-Dialog.png
[08]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/08-New-Resource-Group-Dialog.png
[09]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/09-New-Service-Plan-Dialog.png
[10]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/10-Completed-Web-App-Container-Dialog.png
[11]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/11-Completed-Deploy-Dialog.png
[12]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/12-Activity-Log-View.png
[13]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/13-Azure-Explorer-Web-App.png
[publishDropdownButton]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/publishDropdownButton.png

<!---HONumber=AcomDC_0504_2016-->