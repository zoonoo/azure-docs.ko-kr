<properties linkid="manage-services-how-to-create-and-deploy-a-cloud-service" urlDisplayName="How to create and deploy" pageTitle="How to create and deploy a cloud service - Azure" metaKeywords="Azure creating cloud service, deleting cloud service" description="Learn how to create and deploy a cloud service using the Quick Create method in Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="How to Create and Deploy a Cloud Service" authors="ryanwi" solutions="" manager="" editor="" />

클라우드 서비스를 만들고 배포하는 방법
======================================

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

Azure 관리 포털은 클라우드 서비스를 만들고 배포하는 두 가지 방법으로 **빠른 생성**과 **사용자 지정 만들기**를 제공합니다.

이 토픽에서는 빠른 생성 방법을 사용하여 새 클라우드 서비스를 만든 다음 **업로드**를 사용하여 Azure에서 클라우드 서비스 패키지를 업로드하고 배포하는 방법에 대해 설명합니다. 이 방법을 사용하는 경우 Azure 관리 포털은 진행하면서 모든 요구 사항을 완료하는 데 사용할 수 있는 편리한 링크를 제공합니다. 클라우드 서비스를 만들 때 배포할 준비가 되면 **사용자 지정 만들기**를 사용하여 동시에 둘 다를 수행할 수 있습니다.

**참고** Windows TFS(Team Foundation Service)에서 클라우드 서비스를 게시하려는 경우 빠른 생성을 사용한 다음 **빠른 시작** 또는 대시보드에서 TFS 게시를 설정합니다. 자세한 내용은 [Team Foundation Service 미리 보기를 사용하여 Azure에 지속적인 전송](http://go.microsoft.com/fwlink/?LinkID=251796&clcid=0x409)(영문)을 참조하거나 **빠른 시작** 페이지에 대한 도움말을 참조하십시오.

목차
----

-   [개념](#concepts)
-   [앱 준비](#prepare)
-   [시작하기 전에](#begin)
-   [방법: 빠른 생성을 사용하여 클라우드 서비스 만들기](#quick)
-   [방법: 클라우드 서비스에 대한 인증서 업로드](#uploadcertificate)
-   [방법: 클라우드 서비스 배포](#deploy)

개념
----

Azure에서 응용 프로그램을 클라우드 서비스로 배포하려면 다음과 같은 세 가지 구성 요소가 필요합니다.

> -   **서비스 정의 파일** 클라우드 서비스 정의 파일(.csdef)은 역할 수를 포함하여 서비스 모델을 정의합니다.

> -   **서비스 구성 파일** 클라우드 서비스 구성 파일(.cscfg)은 역할 인스턴스 수를 포함하여 클라우드 서비스 및 개별 역할에 대한 구성 설정을 제공합니다.

> -   **서비스 패키지** 서비스 패키지(.cspkg)에는 응용 프로그램 코드와 서비스 정의 파일이 포함됩니다.

앱 준비
-------

클라우드 서비스를 배포하려면 먼저 응용 프로그램 코드 및 클라우드 서비스 구성 파일(.cscfg)에서 클라우드 서비스 패키지(.cspkg)를 만들어야 합니다. 각 클라우드 서비스 패키지에는 응용 프로그램 파일과 구성이 포함됩니다. 서비스 구성 파일은 구성 설정을 제공합니다.

Azure SDK는 필요한 배포 파일을 준비하는 도구를 제공합니다. [Azure 다운로드](http://www.windowsazure.com/ko-kr/develop/downloads/) 페이지에서 응용 프로그램 코드를 개발하려는 언어로 SDK를 설치할 수 있습니다.

클라우드 서비스를 처음 사용하는 경우 [Azure 코드 샘플](http://code.msdn.microsoft.com/windowsazure/)(영문)에서 샘플 클라우드 서비스 패키지(.cspkg) 및 서비스 구성 파일(.cscfg)을 다운로드하여 작업할 수 있습니다.

세 가지 클라우드 서비스 기능은 서비스 패키지를 내보내기 전에 특별히 구성해야 합니다.

-   데이터 암호화에 SSL(Secure Sockets Layer)을 사용하는 클라우드 서비스를 배포하려는 경우 SSL에 맞게 응용 프로그램을 구성합니다. 자세한 내용은 [HTTPS 끝점에서 SSL 인증서를 구성하는 방법](http://msdn.microsoft.com/ko-kr/library/windowsazure/ff795779.aspx)을 참조하십시오.

-   역할 인스턴스에 대한 원격 데스크톱 연결을 구성하려면 원격 데스크톱에 대한 역할을 구성합니다. 원격 액세스를 위한 서비스 정의 파일 준비에 대한 자세한 내용은 [역할에 대한 원격 데스크톱 연결 설정 개요](http://msdn.microsoft.com/ko-kr/library/windowsazure/gg433010.aspx)(영문)를 참조하십시오.

-   클라우드 서비스에 대해 자세한 모니터링을 구성하려면 클라우드 서비스에 Azure 진단을 사용하도록 설정합니다. *최소 모니터링*(기본 모니터링 수준)에서는 역할 인스턴스(가상 컴퓨터)에 대해 호스트 운영 체제에서 수집된 성능 카운터를 사용합니다. "자세한 모니터링\*에서는 역할 인스턴스 내 성능 데이터를 기반으로 추가 메트릭을 수집하여 응용 프로그램 처리 중 발생하는 문제를 보다 자세히 분석할 수 있습니다. Azure 진단을 사용하도록 설정하는 방법에 대해 알아보려면 [Azure에서 진단 사용](http://www.windowsazure.com/ko-kr/develop/net/common-tasks/diagnostics/)(영문)을 참조하십시오.

시작하기 전에
-------------

-   Azure SDK를 설치하지 않은 경우 **Azure SDK 설치**를 클릭하여 [Azure 다운로드 페이지](http://www.windowsazure.com/ko-kr/develop/downloads/)를 열고 코드를 개발하려는 언어의 SDK를 다운로드합니다. 이 작업은 나중에 수행할 수 있습니다.

-   역할 인스턴스에 인증서가 필요한 경우 인증서를 만듭니다. 클라우드 서비스에는 개인 키가 포함된 .pfx 파일이 필요합니다. 클라우드 서비스를 만들고 배포할 때 Azure에 인증서를 업로드할 수 있습니다. 인증서 만들기에 대한 자세한 내용은 [HTTPS 끝점에서 SSL 인증서를 구성하는 방법](http://msdn.microsoft.com/ko-kr/library/windowsazure/ff795779.aspx)을 참조하십시오.

-   클라우드 서비스를 선호도 그룹에 배포하려면 선호도 그룹을 만듭니다. 선호도 그룹을 사용하면 클라우드 서비스 및 다른 Azure 서비스를 지역의 동일한 위치에 배포할 수 있습니다. 선호도 그룹은 관리 포털의 **네트워크** 영역에 있는 **선호도 그룹** 페이지에서 만들 수 있습니다. 자세한 내용은 **선호도 그룹** 페이지에 대한 도움말을 참조하십시오.

방법: 빠른 생성을 사용하여 클라우드 서비스 만들기
-------------------------------------------------

1.  [관리 포털](http://manage.windowsazure.com/)에서 **새로 만들기**, **클라우드 서비스** 및 **빠른 생성**을 차례로 클릭합니다.

    ![CloudServices\_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png)

2.  **URL**에서 프로덕션 배포의 클라우드 서비스에 액세스하기 위한 공용 URL에 사용할 하위 도메인을 입력합니다. 프로덕션 배포의 URL 형식은 http://*myURL*.cloudapp.net입니다.

3.  **지역/선호도 그룹**에서 클라우드 서비스를 배포할 지역 또는 선호도 그룹을 선택합니다. 클라우드 서비스를 지역 내의 다른 Azure 서비스와 동일한 위치에 배포하려면 선호도 그룹을 선택합니다.

    > [WACOM.NOTE]
    > 선호도 그룹을 만들려면 관리 포털의 **네트워크** 영역을 열고 **선호도 그룹**을 클릭한 다음 **새 선호도 그룹 만들기** 또는 **만들기**를 클릭합니다. 이전 Azure 관리 포털에서 만든 선호도 그룹을 사용할 수 있습니다. 또한 Azure 서비스 관리 API를 사용하여 선호도 그룹을 만들고 관리할 수 있습니다. 자세한 내용은 [선호도 그룹에 대한 작업](http://msdn.microsoft.com/ko-kr/library/windowsazure/ee460798.aspx)을 참조하십시오.

4.  **클라우드 서비스 만들기**를 클릭합니다.

    창 아래쪽에 있는 메시지 영역에서 프로세스의 상태를 모니터링할 수 있습니다.

    **클라우드 서비스** 영역이 열리고 새 클라우드 서비스가 표시됩니다. 상태가 만들어짐으로 바뀌면 클라우드 서비스 만들기가 완료된 것입니다.

    ![CloudServices\_CloudServicesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png)

    클라우드 서비스의 역할에 SSL(Secure Sockets Layer) 데이터 암호화를 위한 인증서가 필요한 경우 인증서가 Azure에 업로드되지 않았으면 클라우드 서비스를 배포하기 전에 인증서를 업로드해야 합니다. 인증서를 업로드하면 역할 인스턴스에서 실행되는 모든 Windows 응용 프로그램에서 인증서에 액세스할 수 있습니다.

방법: 클라우드 서비스에 대한 인증서 업로드
------------------------------------------

1.  [관리 포털](http://manage.windowsazure.com/)에서 **클라우드 서비스**를 클릭합니다. 그런 다음 클라우드 서비스의 이름을 클릭하여 대시보드를 엽니다.

    ![CloudServices\_EmptyDashboard](./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png)

2.  아래와 같이 **인증서**를 클릭하여 **인증서** 페이지를 엽니다.

    ![CloudServices\_CertificatesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CertificatesPage.png)

3.  **새 인증서 추가** 또는 **업로드**를 클릭합니다. **인증서 추가**가 열립니다.

    ![CloudServices\_AddaCertificate](./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png)

4.  **인증서 파일**에서 **찾아보기**를 사용하여 사용할 인증서(.pfx 파일)를 선택합니다.

5.  **암호**에 인증서의 개인 키를 입력합니다.

6.  확인(확인 표시)을 클릭합니다.

    아래와 같이 메시지 영역에서 업로드의 진행률을 확인할 수 있습니다. 업로드가 완료되면 인증서가 테이블에 추가됩니다. 메시지 영역에서 아래쪽 화살표를 클릭하여 메시지를 닫거나 X를 클릭하여 메시지를 제거합니다.

    ![CloudServices\_CertificateProgress](./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png)

    대시보드 또는 **빠른 시작**에서 클라우드 서비스를 배포할 수 있습니다.

방법: 클라우드 서비스 배포
--------------------------

1.  [관리 포털](http://manage.windowsazure.com/)에서 **클라우드 서비스**를 클릭합니다. 그런 다음 클라우드 서비스의 이름을 클릭하여 대시보드를 엽니다.

2.  아래와 같이 **빠른 시작**(**대시보드** 왼쪽에 있는 아이콘)을 클릭하여 **빠른 시작** 페이지를 엽니다. 대시보드의 **업로드**를 사용하여 클라우드 서비스를 배포할 수도 있습니다.

    ![CloudServices\_QuickStartPage](./media/cloud-services-how-to-create-deploy/CloudServices_QuickStartPage.png)

3.  Azure SDK를 설치하지 않은 경우 **Azure SDK 설치**를 클릭하여 [Azure 다운로드 페이지](http://www.windowsazure.com/ko-kr/develop/downloads/)를 열고 코드를 개발하려는 언어의 SDK를 다운로드합니다.

    다운로드 페이지에서 웹 앱을 개발하는 클라이언트 라이브러리 및 소스 코드를 Node.js, Java, PHP 및 다른 언어로 설치하여 확장 가능한 Azure 클라우드 서비스로 배포할 수도 있습니다.

    > [WACOM.NOTE]
    > 이전에 만든 클라우드 서비스(이전에는 *호스티드 서비스*라고 함)의 경우 가상 컴퓨터(역할 인스턴스)의 게스트 운영 체제가 설치하는 Azure SDK와 호환되는지 확인해야 합니다. 자세한 내용은 [Azure SDK 릴리스 정보](http://msdn.microsoft.com/ko-kr/library/windowsazure/hh552718.aspx)를 참조하십시오.

4.  **새 프로덕션 배포** 또는 **새 스테이징 배포**를 클릭합니다.

    프로덕션에 배포하기 전에 Azure에서 클라우드 서비스를 테스트하려면 스테이징에 배포할 수 있습니다. 스테이징 환경에서는 클라우드 서비스의 GUID(Globally Unique Identifier)가 URL(*GUID*.cloudapp.net)에서 클라우드 서비스를 식별합니다. 프로덕션 환경에서는 보다 친근한 DNS 접두사가 할당되어 사용됩니다(예: *myservice*.cloudapp.net). 스테이징된 클라우드 서비스를 프로덕션으로 수준을 올릴 준비가 되면 **교환**을 사용하여 클라이언트 요청을 해당 배포로 리디렉션합니다.

    배포 환경을 선택하면 **패키지 업로드**가 열립니다.

    ![CloudServices\_UploadaPackage](./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png)

5.  **배포 이름**에 새 배포의 이름(예: MyCloudServicev1)을 입력합니다.

6.  **패키지**에서 **찾아보기**를 사용하여 사용할 서비스 패키지 파일(.cspkg)을 선택합니다.

7.  **구성**에서 **찾아보기**를 사용하여 사용할 서비스 구성 파일(.cscfg)을 선택합니다.

8.  클라우드 서비스에 인스턴스가 하나만 있는 역할이 포함되어 있으면 **Deploy even if one or more roles contain a single instance** 확인란을 선택하여 배포가 계속 진행되도록 합니다.

Azure는 모든 역할에 둘 이상의 인스턴스가 있는 경우에만 유지 관리 및 서비스 업데이트 중 클라우드 서비스에 대한 99.95%의 액세스를 보장할 수 있습니다. 필요한 경우 클라우드 서비스를 배포한 후 **크기 조정** 페이지에서 추가 역할 인스턴스를 추가할 수 있습니다. 자세한 내용은 [서비스 수준 계약](http://www.windowsazure.com/ko-kr/support/legal/sla/)을 참조하십시오.

1.  확인(확인 표시)을 클릭하여 클라우드 서비스 배포를 시작합니다.

    메시지 영역에서 배포의 상태를 모니터링할 수 있습니다. 아래쪽 화살표를 클릭하여 메시지를 숨깁니다.

    ![CloudServices\_UploadProgress](./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png)

### 배포가 완료되었는지 확인하려면

1.  **대시보드**를 클릭합니다.

2.  **간략 상태**에서 사이트 URL을 클릭하여 웹 브라우저에서 클라우드 서비스를 엽니다.

	![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)
