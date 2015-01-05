<properties urlDisplayName="How to create" pageTitle="웹 사이트를 만드는 방법 - Azure 서비스 관리" metaKeywords="Azure creating website, Azure deleting website" description="Learn how to create a website using the Azure Management Portal." metaCanonical="" services="web-sites" documentationCenter="" title="How to Create and Deploy a Website" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/24/2014" ms.author="cephalin" />

#웹 사이트를 만드는 방법

이 항목에서는 갤러리에서 또는 관리 포털을 사용하여 웹 사이트를 만드는 방법을 보여 줍니다.

만든 Azure 웹 사이트에 콘텐츠를 배포하는 방법에 대한 자세한 내용은 **배포** 섹션([Azure 웹 사이트](/ko-kr/documentation/services/web-sites/))을 참조하세요.

## 목차 ##

- [방법: 관리 포털을 사용하여 웹 사이트 만들기](#createawebsiteportal)
- [방법: 갤러리에서 웹 사이트 만들기](#howtocreatefromgallery)
- [방법: 웹 사이트 삭제](#deleteawebsite)
- [다음 단계](#nextsteps)

##<a name="createawebsiteportal"></a>방법: 관리 포털을 사용하여 웹 사이트 만들기

Azure에서 웹 사이트를 만들려면 다음 단계를 따르세요.
	
1. [Azure 관리 포털](http://manage.windowsazure.com/)에 로그인합니다.

2. 관리 포털의 왼쪽 아래에서 **새로 만들기** 아이콘을 클릭합니다.

3. **웹 사이트**아이콘, **빠른 생성** 아이콘을 차례로 클릭하고 URL의 값을 입력한 다음 페이지 오른쪽 맨 아래에 있는 **웹 사이트 만들기** 옆의 확인 표시를 클릭합니다.

4. 웹 사이트가 만들어지면 **웹 사이트 <*웹 사이트 이름*>을(를) 만들었습니다.**라는 텍스트가 표시됩니다. 포털 페이지 맨 아래에 있는 **찾아보기**를 클릭하여 웹 사이트를 찾을 수 있습니다.

5. 포털에서 웹 사이트 목록에 표시되는 웹 사이트의 이름을 클릭하여 웹 사이트의 **퀵 스타트** 관리 페이지를 엽니다.

6. **퀵 스타트** 페이지에는 웹 사이트 개발 도구를 가져오거나, 웹 사이트에 대한 게시를 설정하거나, TFS 또는 Git와 같은 소스 제어 공급자에서 배포를 설정하는 옵션이 제공됩니다. 기본적으로 웹 사이트에 대해 FTP 게시가 설정되며 FTP 호스트 이름이 **대시보드** 페이지의 **간략 상태** 섹션에서 **FTP 호스트 이름** 아래에 표시됩니다. FTP 또는 Git를 사용하여 게시하기 전에 웹 사이트에 콘텐츠를 배포할 때 FTP 호스트 또는 Git 리포지토리에 인증할 수 있도록 **대시보드** 페이지에서 **배포 자격 증명 다시 설정** 옵션을 선택합니다.

7. **구성** 관리 페이지에 다음과 같은 웹 사이트 설정이 표시됩니다.

	- 웹 응용 프로그램에 대한 .NET framework 또는 PHP 버전
	- SSL 바인딩
	- 사용자 지정 도메인 이름
	- 로깅 옵션
	- Azure 환경에 대한 앱 설정(예를 들어 개발 환경의 Web.config에서 <appSettings> 재정의)
	- 연결 문자열(예를 들어 개발 환경의 Web.config에서 <connectionStrings> 재정의)
	- 특정 파일 확장명(예: *.php)에 대한 스크립트 프로세서

##<a name="howtocreatefromgallery"></a>방법: 갤러리에서 웹 사이트 만들기

[WACOM.INCLUDE [website-from-gallery](../includes/website-from-gallery.md)]

##<a name="deleteawebsite"></a>방법: 웹 사이트 삭제
웹 사이트는 Azure 관리 포털의 **삭제** 아이콘을 사용하여 삭제됩니다. **삭제** 아이콘은 Azure 포털에서 **웹 사이트**를 클릭하여 모든 웹 사이트를 나열할 때 및 각 웹 사이트 관리 페이지의 맨 아래에서 사용할 수 있습니다.

##<a name="nextsteps"></a>다음 단계

자세한 내용은 [Azure 웹 사이트](/ko-kr/documentation/services/web-sites/)를 참조하세요.

<!--HONumber=35.1-->
