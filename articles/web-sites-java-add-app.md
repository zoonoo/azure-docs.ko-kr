<properties linkid="develop-java-tutorials-web-site-add-app" urlDisplayName="Add an application to your Java web site" pageTitle="Add an application to your Java web site" metaKeywords="" description="This tutorial shows you how to add a page or application to your Java web site on Microsoft Azure." metaCanonical="" services="web-sites" documentationCenter="Java" title="Add an application to your Java web site" videoId="" scriptId="" authors="waltpo" solutions="" manager="keboyd" editor="mollybos" />

Azure에서 Java 웹 사이트에 응용 프로그램 추가
=============================================

이 자습서에서는 Azure 갤러리 또는 Azure 구성 UI를 사용하여 이전에 만든 Java 웹 사이트에 웹 페이지 또는 응용 프로그램을 추가하는 방법을 보여 줍니다.

소스 제어를 사용하여 연속 통합 시나리오에 포함된 응용 프로그램 또는 웹 페이지를 업로드할 수 있습니다. 웹 사이트에 소스 제어를 사용하는 방법에 대한 자세한 내용은 [소스 제어에서 Azure 웹 사이트로 게시](../web-sites-publish-source-control)에서 확인할 수 있습니다. 소스 제어에 대한 대안으로 이 문서에서는 FTP를 사용하여 응용 프로그램을 업로드하는 방법을 보여 줍니다.

이 자습서는 [Microsoft Azure 웹 사이트 및 Java 시작](../web-sites-java-get-started)의 단계를 이미 완료한 개발자를 대상으로 합니다.

FTP를 사용하여 웹 사이트 사용자 지정
====================================

FTP 자격 증명을 확인하고 해당 자격 증명을 사용하여 웹 사이트 콘텐츠에 액세스해야 합니다. 그런 다음 응용 프로그램을 실행하도록 콘텐츠를 수정할 수 있습니다. 여기에 나와 있는 예제에서는 파일 탐색기를 사용하여 FTP를 쉽게 사용하지만 다른 FTP 기술도 사용할 수 있습니다.

FTP 자격 증명을 사용하여 사이트 콘텐츠 액세스
---------------------------------------------

1.  Microsoft Azure 관리 포털에서 **웹 사이트** 보기로 이동합니다.
2.  **웹 사이트** 보기에서 웹 사이트의 이름을 클릭합니다.
3.  **대시보드**를 클릭합니다.
4.  **대시보드** 보기의 **간략 상태**에서 **게시 프로필 다운로드**를 클릭합니다. 이 파일을 로컬에 저장합니다. 사이트에 게시할(및 사이트에서 콘텐츠를 복사할) 권한이 있는 사용자 이름 및 암호가 포함되어 있으므로 이 파일의 보안을 유지하십시오.
5.  텍스트 편집기를 사용하여 다운로드한 게시 설정 파일을 엽니다. 해당 파일 내에서 **userName** 및 **userPwd**의 값을 확인합니다. 해당 항목은 사이트에 있는 파일에 액세스하는 데 사용할 사용자 이름 및 암호를 각각 나타냅니다.
6.  웹 사이트의 파일에 액세스하고 메시지가 표시되면 사용자 이름 및 암호를 지정합니다. 이 예제에서는 Internet Explorer에서 FTP를 사용하지만 다른 기술도 사용할 수 있습니다. FTP를 계속 사용하려면 **대시보드** 보기에서 **FTP 호스트 이름** 아래에 나열된 URL을 클릭합니다. FTP 호스트 이름은 게시 설정 파일 내에서도 확인할 수 있으며 **publishUrl**에 할당된 값입니다.
7.  사용자 이름 및 암호를 입력하라는 메시지가 표시되면 게시 설정 파일에서 **userName** 및 **userPwd**에 지정된 값을 사용합니다.
8.  Internet Explorer 내에 있는 경우 파일 탐색기 보기로 전환하려면 **보기**를 클릭한 후 **파일 탐색기에서 FTP 사이트 열기**를 클릭합니다.

웹 사이트의 webapps 폴더 액세스
-------------------------------

이제 Azure의 웹 사이트에 대한 파일 탐색기 보기에서 웹 사이트를 사용자 지정할 수 있습니다. 웹 사이트의 **webapps** 폴더로 응용 프로그램을 복사해야 합니다. 해당 폴더의 탐색 경로는 웹 사이트 설정 방법에 따라 다릅니다.

-   Azure 응용 프로그램 갤러리를 사용하여 웹 사이트를 설정한 경우 파일 탐색기에서 **site**, **wwwroot**, **bin**을 차례로 두 번 클릭하고 웹 사이트에서 사용하는 응용 프로그램 서버의 버전을 두 번 클릭한 후 **webapps**를 두 번 클릭합니다.
-   Azure 구성 UI를 사용하여 웹 사이트를 설정한 경우 파일 탐색기에서 **site**, **wwwroot**를 차례로 두 번 클릭한 후 **webapps**를 두 번 클릭합니다.
-   사용자 지정 업로드를 사용하여 웹 사이트를 설정한 경우 필요에 따라 **webapps** 폴더로 이동합니다.

FTP를 사용하여 웹 사이트에 WAR 파일을 추가하려면
------------------------------------------------

1.  위에서 설명한 대로 웹 사이트에 해당하는 기술을 사용하여 **webapps** 폴더로 이동합니다.
2.  WAR 파일을 **webapps** 폴더로 복사합니다.

응용 프로그램 서버에서 WAR 파일이 추가되었는지 검색하여 자동으로 로드합니다. 그런 다음 웹 사이트의 URL에 WAR 파일의 이름을 추가한 URL을 통해 브라우저에서 앱을 실행할 수 있습니다.

예를 들어 http://*mysitename*.azurewebsites.net/*mywar*로 이동합니다. 여기에서 *mysitename*은 URL에 지정한 이름이며, *mywar*는 앞에서 복사한 WAR 파일의 대/소문자 구분 이름(후행 **.war** 없음)입니다.

FTP를 사용하여 웹 사이트에 웹 페이지를 추가하려면
-------------------------------------------------

1.  **webapps** 폴더로 이동합니다.
2.  **webapps** 폴더 내에 새 폴더를 만듭니다.
3.  새 폴더를 엽니다.
4.  새 폴더에 웹 페이지를 추가합니다.

응용 프로그램 서버에서 새 폴더 및 웹 파일이 추가되었는지 검색하여 자동으로 로드합니다. 그런 다음 http://*mysitename*.azurewebsites.net/*myfolder*/*myfolder.jsp* 형식의 URL을 사용하여 JSP 파일을 실행합니다. 여기에서 *mysitename*은 URL에 지정한 이름이며, *myfolder*는 **webapps** 내에 만든 폴더이며, *myfile.jsp*는 기존에 만든 JSP 파일의 이름입니다.

파일(WAR 이외 파일)을 루트 디렉터리로 복사한 경우에는 해당 파일을 사용하기 전에 응용 프로그램 서버를 다시 시작해야 합니다. Azure에서 실행되는 Java 웹 사이트의 자동 로드 기능은 추가 중인 새 WAR 파일이나 **webapps** 폴더에 추가된 새 파일 또는 디렉터리를 기반으로 합니다.

