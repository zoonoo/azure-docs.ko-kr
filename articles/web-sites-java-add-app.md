<properties urlDisplayName="Add an application to your Java website" pageTitle="Java 웹 사이트에 응용 프로그램 추가" metaKeywords="" description="This tutorial shows you how to add a page or application to your Java website on Microsoft Azure." metaCanonical="" services="web-sites" documentationCenter="Java" title="Add an application to your Java website" videoId="" scriptId="" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="robmcm" />

# Azure에서 Java 웹 사이트에 응용 프로그램 추가

[Microsoft Azure 웹 사이트 및 Java 시작](../web-sites-java-get-started)에 설명된 대로 Java 웹 사이트를 초기화한 후에는 **webapps** 폴더에 WAR을 배치하여 응용 프로그램을 업로드할 수 있습니다.

**webapps** 폴더의 탐색 경로는 웹 사이트 설정 방법에 따라 다릅니다.

- Azure 응용 프로그램 갤러리를 사용하여 웹 사이트를 설정한 경우 **webapps** 폴더의 경로는 **d:\home\site\wwwroot\bin\application\_server\webapps**(여기서 **application\_server**는 웹 사이트에 적용되는 응용 프로그램 서버의 이름) 형식입니다. 
- Azure 구성 UI를 사용하여 웹 사이트를 설정한 경우 **webapps** 폴더의 경로는 **d:\home\site\wwwroot\webapps** 형식입니다. 

소스 제어를 사용하여 연속 통합 시나리오에 포함된 응용 프로그램 또는 웹 페이지를 업로드할 수 있습니다. 웹 사이트에 소스 제어를 사용하는 방법에 대한 자세한 내용은 [소스 제어에서 Azure 웹 사이트로 게시](../web-sites-publish-source-control)에서 확인할 수 있습니다. FTP를 사용하여 응용 프로그램 또는 웹 페이지를 업로드할 수도 있습니다.

Tomcat 웹 사이트에 대한 참고 사항: **webapps** 폴더에 WAR 파일을 업로드하면 Tomcat 응용 프로그램 서버에서 해당 파일을 추가했는지 검색한 후 자동으로 로드합니다. 파일(WAR 이외 파일)을 루트 디렉터리로 복사한 경우에는 해당 파일을 사용하기 전에 응용 프로그램 서버를 다시 시작해야 합니다. Azure에서 실행되는 Tomcat Java 웹 사이트의 자동 로드 기능은 추가 중인 새 WAR 파일이나 **webapps** 폴더에 추가된 새 파일 또는 디렉터리를 기반으로 합니다. 
