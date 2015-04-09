<properties
	pageTitle="웹 앱을 만드는 방법 - Azure 서비스 관리"
	description="Azure 포털을 사용 하 여 웹 앱을 만드는 방법에 알아봅니다."
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="cephalin"/>

#웹 앱을 만드는 방법

이 주제에서는 갤러리 또는 Azure 포털을 사용하여 웹 앱을 만드는 방법을 보여줍니다.

사용자가 만든 웹 앱에 콘텐츠를 배포하는 방법에 대한 자세한 내용은 [Azure 웹 앱](http://go.microsoft.com/fwlink/?LinkId=529714)에서 **배포** 섹션을 참조하세요.

>[AZURE.NOTE] Azure 계정에 등록하기 전에 Azure 앱 서비스를 시작하려는 경우 [앱 서비스 시도](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동하십시오. 여기서 앱 서비스에서 수명이 짧은 스타터 웹 앱을 바로 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

##<a name="createawebsiteportal"></a>방법: Azure 포털을 사용 하 여 웹 앱을 만듭니다.

Azure에서 웹 앱을 만들려면 다음 단계를 따르십시오.

1. [Azure 포털](http://go.microsoft.com/fwlink/?LinkId=529715)에 로그인합니다.

2. Azure 포털의 왼쪽 아래에서 **새로 만들기** 아이콘을 클릭합니다.

3. **웹 + 모바일** 아이콘을 클릭하고 **웹 앱** 아이콘을 클릭하고 URL의 값을 입력한 다음 만들기 블레이드의 오른쪽 아래에서 **만들기**를 클릭합니다.

4. 웹 앱이 만들어지면 **리소스 그룹 <리소스 그룹 이름>에 배포 성공** 텍스트가 표시됩니다.

5. 포털에서 웹 앱의 목록에 표시된 웹 앱의 이름을 클릭하여 웹 앱의 블레이드를 엽니다.

6. 블레이드에서, 웹 앱 개발 도구를 가져오거나, 웹 앱에 대한 게시를 설정하거나, TFS 또는 Git와 같은 소스 제어 공급자에서 배포를 설정하는 옵션이 제공됩니다. 기본적으로 웹 앱에 대해 FTP 게시가 설정되고 FTP 호스트 이름은 웹 앱 블레이드의 **필수** 섹션에 표시됩니다. FTP 또는 Git로 게시하기 전에 웹 앱 블레이드에서 **게시 프로필 재설정** 옵션을 선택하여 웹 앱에 콘텐츠를 배포할 때 FTP 호스트 또는 Git 리포지토리에 대해 인증할 수 있도록 합니다.

7. **설정** 블레이드는 다음과 같은 웹 앱에 대한 설정을 표시합니다.

	- 웹 앱에 대한 NET, PHP, Java 또는 Python의 버전
	- Visual Studio Online에서 편집
	- SSL 바인딩
	- 사용자 지정 도메인 이름
	- 인증/권한 부여
	- 응용 프로그램 및 사이트 진단
	- 끝점 모니터링
	- 로깅 옵션
	- Azure 환경에 대한 앱 설정(예를 들어 개발 환경의 Web.config에서 <appSettings> 재정의)
	- 연결 문자열(예를 들어 개발 환경의 Web.config에서 <connectionStrings> 재정의)
	- 특정 파일 확장명(예: *.php)에 대한 스크립트 프로세서

##<a name="howtocreatefromgallery"></a> 방법: 갤러리에서 웹 앱 만들기

[AZURE.INCLUDE [website-from-gallery](../includes/website-from-gallery.md)]

##<a name="deleteawebsite"></a> 방법: 웹 앱 삭제
웹 앱은 Azure 포털의 **삭제** 아이콘을 사용하여 삭제됩니다. **삭제** 아이콘은 웹 앱 블레이드 상단에서 사용할 수 있습니다.

##<a name="nextsteps"></a> 다음 단계

자세한 내용은 [Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714)를 참조하세요.

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 다음을 참조하세요. [Azure 앱 서비스 및 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)
* 이전 포털에서 새 포털로의 변경에 대한 지침은 다음을 참조하세요. [미리 보기 포털 탐색에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=49-->