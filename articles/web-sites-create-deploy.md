<properties  linkid="manage-services-how-to-create-websites" urlDisplayName="How to create" pageTitle="How to create web sites - Azure service management" metaKeywords="Azure creating web site, Azure deleting website" description="Learn how to create a web site using the Azure Management Portal." metaCanonical="" services="web-sites" documentationCenter="" title="How to Create and Deploy a Web Site" authors="timamm" solutions="" manager="" editor="" />

# 웹 사이트를 만드는 방법

이 항목에서는 갤러리에서 또는 관리 포털을 사용하여 웹 사이트를 만드는 방법을 보여 줍니다.

만든 Azure 웹 사이트에 콘텐츠를 배포하는 방법에 대한 자세한 내용은 [Azure 웹 사이트](/ko-kr/documentation/services/web-sites/)의 **배포** 섹션을 참조하십시오.

## 목차

* [방법: 관리 포털을 사용하여 웹 사이트 만들기](#createawebsiteportal)
* [방법: 갤러리에서 웹 사이트 만들기](#howtocreatefromgallery)
* [방법: 웹 사이트 삭제](#deleteawebsite)
* [다음 단계](#nextsteps)

## <a name="createawebsiteportal"></a>방법: 관리 포털을 사용하여 웹 사이트 만들기

Azure에서 웹 사이트를 만들려면 다음 단계를 따르십시오.

1.  [Azure 관리 포털][1]에 로그인합니다.

2.  관리 포털의 왼쪽 아래에서 **Create New** 아이콘을 클릭합니다.

3.  **Web Site** 아이콘, **Quick Create** 아이콘을 차례로 클릭하고 URL의 값을 입력한 다음 페이지 오른쪽 맨 아래에 있는 **Create Web Site** 옆의 확인 표시를 클릭합니다.

4.  웹 사이트가 만들어지면 **Creating web site <*web site name*> succeeded**라는 텍스트가 표시됩니다. 포털 페이지 맨 아래에 있는 **Browse**를 클릭하여 웹 사이트를 찾을 수 있습니다.

5.  포털에서 웹 사이트 목록에 표시되는 웹 사이트의 이름을 클릭하여 웹 사이트의 **Quick Start** 관리 페이지를 엽니다.

6.  **Quick Start** 페이지에는 웹 사이트 개발 도구를 가져오거나, 웹 사이트에 대한 게시를 설정하거나, TFS 또는 Git와 같은 소스 제어 공급자에서 배포를 설정하는 옵션이 제공됩니다. 기본적으로 웹 사이트에 대해 FTP 게시가 설정되며 FTP 호스트 이름이 **Dashboard** 페이지의 **Quick Glance** 섹션에서 **FTP Host Name** 아래에 표시됩니다. FTP 또는 Git를 사용하여 게시하기 전에 웹 사이트에 콘텐츠를 배포할 때 FTP 호스트 또는 Git 리포지토리에 인증할 수 있도록 **Dashboard** 페이지에서 **Reset deployment credentials** 옵션을 선택합니다.

7.  **Configure** 관리 페이지에서는 웹 사이트 설정이 다음 범주로 표시됩니다.

* **일반**: 웹 응용 프로그램에 필요한 .NET framework 또는 PHP 버전을 설정합니다. 표준 모드의 사이트에서는 **Platform** 옵션을 사용하여 32비트 또는 64비트 환경을 선택할 수 있습니다.

* **인증서**: 표준 모드에서는 사용자 지정 도메인에 대해 SSL 인증서를 업로드할 수 있습니다.

* **도메인 이름**: 공유 및 표준 모드에서는 웹 사이트의 사용자 지정 도메인 이름을 보고 관리할 수 있습니다.

* **SSL 바인딩**: 표준 모드에서는 IP 기반 또는 SNI 기반 SSL을 사용하여 사용자 지정 도메인 이름에 SSL 인증서를 할당할 수 있습니다.

* **배포**: 소스 제어에서 배포를 설정하는 경우 여기서 배포를 구성할 수 있습니다.

* **응용 프로그램 진단**: 추적을 사용하여 계측된 웹 응용 프로그램에서 진단 추적을 수집하기 위한 옵션을 설정합니다.

* **사이트 진단**: 웹 사이트에 대한 진단 정보를 수집하기 위한 로깅 옵션을 설정합니다. 옵션에는 웹 서버 로깅, 자세한 오류 메시지 및 실패한 요청 추적이 포함됩니다.

* **모니터링**: 표준 모드의 웹 사이트에서는 이 기능을 사용하여 HTTP 또는 HTTPS 끝점의 가용성을 테스트할 수 있습니다.

* **앱 설정**: 시작할 때 웹 응용 프로그램에서 로드되는 이름/값 쌍을 지정합니다. .NET 사이트의 경우 런타임에 이러한 설정이 .NET 구성 **AppSettings**에 주입되어 기존 설정을 재정의합니다. PHP 및 노드 사이트에서는 런타임에 이러한 설정을 환경 변수로 사용할 수 있습니다.

* **연결 문자열**: 연결 문자열을 보고 편집합니다. .NET 사이트의 경우 런타임에 이러한 연결 문자열이 .NET 구성 **connectionStrings** 설정에 주입되어 키가 연결된 데이터베이스 이름과 같은 기존 항목을 재정의합니다. PHP 및 노드 사이트에서는 런타임에 이러한 설정을 환경 변수로 사용할 수 있습니다.

* **기본 문서**: 웹 사이트의 기본 문서는 사용자가 웹 사이트로 이동할 때 기본적으로 표시되는 페이지입니다. 아직 없는 경우 웹 응용 프로그램의 기본 문서를 이 목록에 추가합니다. 웹 사이트의 기본 문서가 목록의 맨 위에 와야 합니다.

* **처리기 매핑**: \*.php 등의 특정 파일 확장명에 대한 요청을 처리할 스크립트 프로세서를 지정합니다.

## <a name="howtocreatefromgallery"></a>방법: 갤러리에서 웹 사이트 만들기

[WACOM.INCLUDE [website-from-gallery](../includes/website-from-gallery.md)]

## <a name="deleteawebsite"></a>방법: 웹 사이트 삭제

웹 사이트는 Azure 관리 포털의 **Delete** 아이콘을 사용하여 삭제됩니다. **Delete** 아이콘은 Azure 포털에서 **Web Sites**를 클릭하여 모든 웹 사이트를 나열할 때 및 각 웹 사이트 관리 페이지의 맨 아래에서 사용할 수 있습니다.

## <a name="nextsteps"></a>다음 단계

자세한 내용은 [Azure 웹 사이트](/ko-kr/documentation/services/web-sites/)를 참조하십시오.



[1]: http://manage.windowsazure.com/