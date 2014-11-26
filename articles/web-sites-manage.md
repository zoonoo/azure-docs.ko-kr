<properties linkid="manage-scenarios-how-to-manage-websites" urlDisplayName="How to manage" pageTitle="How to manage websites - Microsoft Azure service management" metaKeywords="Azure portal website management" description="A reference for the Portal website management pages in Microsoft Azure. Details are provided for each website management page." metaCanonical="" services="web-sites" documentationCenter="" title="How to Manage Websites" authors="cephalin"  solutions="" writer="mwasson" manager="wpickett" editor=""  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/18/2014" ms.author="mwasson" />

# <a name="howtomanage"></a>Azure 관리 포털을 통해 웹 사이트 관리

Azure 포털에서 페이지 또는 "탭" 집합을 사용하여 웹 사이트를 관리합니다. 각 웹 사이트 관리 페이지는 아래에 설명되어 있습니다.

## 빠른 시작

**빠른 시작** 관리 페이지에는 다음 섹션이 포함되어 있습니다.

-   **도구 얻기** - [WebMatrix 설치][WebMatrix 설치](영문) 및 [Microsoft Azure SDK][Microsoft Azure SDK](영문)에 대한 링크를 제공합니다.
-   **앱 게시** - 웹 사이트의 게시 프로필을 다운로드하고, 웹 사이트의 배포 자격 증명을 다시 설정하고, 스테이징된 게시(배포) 슬롯을 스테이징되지 않은 사이트에 추가하고, 스테이징된 게시에 대해 알아볼 수 있는 링크를 제공합니다.
-   **소스 제어 통합** - TFS, CodePlex, GitHub, Dropbox, Bitbucket 또는 로컬 Git와 같은 웹 사이트 또는 소스 제어 도구에서 배포를 설정하고 관리합니다.

## 대시보드

**대시보드** 관리 페이지에는 다음이 포함되어 있습니다.

웹 사이트 사용량을 특정 메트릭의 측정으로 요약하는 차트입니다.

-   **CPU 시간** - 웹 사이트 CPU 사용량에 대한 측정입니다.
-   **데이터 입력** - 웹 사이트가 클라이언트에서 받은 데이터에 대한 측정입니다.
-   **데이터 출력** - 웹 사이트에서 클라이언트에 보낸 데이터에 대한 측정입니다.
-   **HTTP 서버 오류** - 전송된 HTTP "5xx 서버 오류" 메시지의 개수입니다.
-   **요청** - 웹 사이트에 대한 모든 클라이언트 요청 개수입니다.

**참고:** 페이지 아래에 있는 **메트릭 추가**를 선택하여 **모니터** 관리 페이지에서 성능 메트릭을 더 추가할 수 있습니다. 자세한 내용은 [웹 사이트를 모니터링하는 방법][웹 사이트를 모니터링하는 방법]을 참조하십시오.

**웹 끝점 상태** - 모니터링을 위해 구성된 웹 끝점의 목록입니다. 끝점이 구성되어 있지 않은 경우 **웹 끝점 모니터링 구성**을 클릭하고 **구성** 관리 페이지의 **모니터링** 섹션으로 이동하십시오. 자세한 내용은 [웹 사이트를 모니터링하는 방법][웹 사이트를 모니터링하는 방법]을 참조하십시오.

**자동 크기 조정 상태** - 표준 모드에서 필요한 만큼만 사용할 수 있도록 리소스를 자동으로 확장할 수 있습니다. 자동 크기 조정을 사용하려면 **자동 크기 조정 구성**을 선택하여 **크기 조정** 페이지로 이동합니다. 웹 사이트가 무료 또는 공유 모드에 있는 경우 표준 모드로 변경해야(**크기 조정** 페이지에서 변경할 수 있음) 자동 크기 조정을 구성할 수 있습니다. **자동 크기 조정 작업 로그**를 클릭하면 웹 사이트의 자동 크기 작업 기록을 볼 수 있는 **관리 서비스** 포털로 이동됩니다. 기본 쿼리는 24시간 동안으로 지정되지만 쿼리를 변경할 수 있습니다.

**사용 개요** - 이 섹션은 웹 사이트 CPU, 파일 시스템 및 메모리 사용량에 대한 통계를 보여 줍니다.

**연결된 리소스** - 웹 사이트에 연결된 SQL 또는 MySQL 데이터베이스와 같은 리소스 목록 또는 Microsoft Azure 저장소 계정을 표시하는 섹션입니다. 리소스를 관리할 리소스 이름을 클릭합니다. MySQL 데이터베이스가 있는 경우 이름을 클릭하면 ClearDB 관리 페이지로 이동됩니다. ClearDB 관리 페이지에서 성능 메트릭을 보거나 필요한 경우 MySQL 데이터베이스를 업그레이드할 수 있는 ClearDB 대시보드로 이동할 수 있습니다. 리소스 목록이 표시되지 않으면 **연결된 리소스 관리**를 클릭하여 웹 사이트의 리소스에 링크를 추가할 수 있는 **연결된 리소스** 페이지로 이동합니다.

**간략 상태** 섹션은 다음 요약 정보 및 링크를 포함합니다(설정에 따라, 아래 나열된 옵션 중 일부는 나타나지 않을 수 있음).

-   **적용 가능한 추가 기능 보기** - **스토어에서 구입** 대화 상자가 열리면 웹 사이트의 추가 기능을 제공하는 추가 기능을 구입하도록 선택할 수 있습니다. 지역이나 환경에 따라 일부 추가 기능은 사용하지 못할 수 있습니다.
-   **연결 문자열 보기** - 웹 사이트의 데이터베이스 연결 문자열을 봅니다.
-   **게시 프로필 다운로드** - 웹 사이트의 게시 프로필을 다운로드하려면 이 링크를 클릭합니다. 게시 프로필에는 FTP 및 Git를 사용하여 웹 사이트에 콘텐츠를 업로드하는 데 필요한 자격 증명(사용자 이름 및 암호) 및 URL이 포함됩니다. 프로필 파일은 XML 형식이며 텍스트 편집기에서 볼 수 있습니다.
-   **배포 자격 증명 설정** - FTP 또는 Git를 사용하여 웹 사이트에 콘텐츠를 업로드하는 데 사용할 사용자 이름 및 암호를 만들려면 클릭합니다. 이러한 자격 증명을 사용하여 구독의 웹 사이트에 콘텐츠를 푸시할 수 있습니다. [FTP 자격 증명][FTP 자격 증명]을 참조하세요. **참고**: Microsoft 계정(Live ID) 자격 증명을 사용해서는 FTP 호스트 또는 Git 리포지토리에 대한 인증을 받을 수 없습니다.
-   **게시 프로필 자격 증명 다시 설정** - 웹 사이트의 게시 프로필을 다시 설정합니다. 이전에 다운로드한 게시 프로필이 유효하지 않게 됩니다.
-   **소스 제어에서 배포 설정** - Team Foundation Service, CodePlex, GitHub, Dropbox, Bitbucket 또는 로컬 Git에서 연속 게시를 설정할 수 있는 대화 상자를 표시합니다.
-   **새 배포 슬롯 추가** - 표준 모드의 사이트에 대해, 이 기능을 사용하여 사이트의 스테이징 슬롯을 만듭니다. 스테이징 슬롯(스테이징된 사이트)을 사용하여 프로덕션으로 전환하기 전에 사이트 콘텐츠 및 구성의 유효성을 검사할 수 있습니다. 또한 사이트의 스테이징된 버전을 사용하여 콘텐츠 업데이트를 서서히 추가한 후 스테이징 슬롯에서 업데이트가 완료되었을 때 사이트를 프로덕션으로 전환할 수 있습니다. 이미 스테이징된 사이트에는 슬롯을 추가할 수 없습니다.
-   **Visual Studio Online에서 편집** - Microsoft Azure 포털에서 Visual Studio Online을 사용하여 직접 웹 사이트를 편집하려면 이 링크를 클릭합니다. 이 옵션은 **구성** 페이지에서 사용하도록 설정하지 않는 한 표시되지 않습니다.
-   **Dropbox에서 연결 끊기** - 배포를 위해 Dropbox에 대한 연결을 설정한 경우 이 링크를 통해 연결을 끊을 수 있습니다.
-   **Git 리포지토리 삭제** - Git 리포지토리가 있는 경우 이 링크를 통해 삭제할 수 있습니다.
-   **상태** - 웹 사이트가 실행 중인지 여부를 나타냅니다.
-   **관리 서비스** - Microsoft Azure 관리 서비스 포털에서 웹 사이트에 대한 작업 로그를 보려면 **작업 로그** 링크를 클릭합니다.
-   **가상 IP 주소** - **구성** 탭의 **SSL 바인딩** 섹션에서 웹 사이트에 대한 IP 기반 SSL 바인딩을 구성한 경우 웹 사이트의 가상 IP 주소를 표시합니다.
-   **사이트 URL** - 공개적으로 액세스 가능한 인터넷 웹 사이트 주소를 지정합니다.
-   **컴퓨팅 모드** - 웹 사이트가 무료, 공유, 기본 또는 표준 모드에서 실행 중인지 여부를 지정합니다. 웹 크기 조정 그룹 모드에 대한 자세한 내용은 [웹 사이트 크기를 조정하는 방법][웹 사이트 크기를 조정하는 방법]을 참조하십시오.
-   **FTP 호스트 이름** - FTP를 통해 웹 사이트에 게시할 때 사용할 URL을 지정합니다. [FTP 자격 증명][FTP 자격 증명]을 참조하세요.
-   **FTPS 호스트 이름** - FTPS를 통해 웹 사이트에 게시할 때 사용할 URL을 지정합니다. [FTP 자격 증명][FTP 자격 증명]을 참조하세요.
-   **배포 사용자/FTP 사용자** - FTP 또는 Git를 통해 Microsoft Azure에 웹 사이트를 배포할 때 사용되는 계정을 나타냅니다. [FTP 자격 증명][FTP 자격 증명]을 참조하세요.
-   **FTP 진단 로그** - **구성** 관리 페이지에서 진단 로깅을 사용하도록 설정한 경우 웹 사이트 진단 로그의 FTP 위치를 지정합니다.
-   **FTPS 진단 로그** - **구성** 관리 페이지에서 진단 로깅을 사용하도록 설정한 경우 웹 사이트 진단 로그의 FTPS 위치를 지정합니다.
-   **위치** - 웹 사이트를 호스트하는 데이터 센터의 지역을 지정합니다.
-   **구독 이름** - 웹 사이트가 연결된 구독 이름을 지정합니다.
-   **구독 ID** - 웹 사이트가 연결된 구독의 고유한 구독 ID(GUID)를 지정합니다.

## 배포

이 탭은 소스 제어에서 배포를 설정한 경우에만 나타납니다. **배포** 관리 페이지는 선택한 게시 방법을 사용하여 웹 사이트에 대해 수행한 모든 배포 요약을 제공합니다. 웹 사이트에 대해 Git 게시를 구성했지만 배포는 수행하지 않은 경우 **배포** 관리 페이지에서 GIT를 사용하여 웹 응용 프로그램을 웹 사이트에 배포하는 방법을 설명합니다.

## 모니터

**모니터** 관리 페이지는 웹 사이트의 사용량 정보를 표시하는 차트를 제공합니다. 기본적으로 차트는 위의 대시보드 섹션에서 설명한 **대시보드** 페이지의 차트와 동일한 메트릭을 표시합니다. 또한 HTTP 성공, HTTP 리디렉션, HTTP 401 오류, HTTP 403 오류, HTTP 404 오류 및 HTTP 406 오류 메트릭을 표시하도록 차트를 구성할 수도 있습니다. 이러한 메트릭에 대한 자세한 내용은 [웹 사이트를 모니터링하는 방법][웹 사이트를 모니터링하는 방법]을 참조하십시오.

## 웹 작업

웹 작업 관리 페이지에서 주문형, 예약형 또는 연속형 웹 사이트 작업을 만들 수 있습니다. 자세한 내용은 [Microsoft Azure 웹 사이트에서 WebJob 기능 사용 방법][Microsoft Azure 웹 사이트에서 WebJob 기능 사용 방법]을 참조하십시오.

## 구성

**구성** 관리 페이지는 응용 프로그램별 설정을 지정하는 데 사용됩니다.

자세한 내용은 [웹 사이트를 구성하는 방법][웹 사이트를 구성하는 방법]을 참조하세요.

## 크기 조정

**크기 조정** 관리 페이지에서 웹 크기 조정 그룹 모드(**무료**, **공유**, **기본** 또는 **표준**)를 지정할 수 있습니다. **공유**, **기본** 및 **표준** 모드가 보다 높은 처리량 및 성능을 제공합니다. **표준**, **기본** 및 **표준** 모드에서는, 현재 웹 사이트 및 동일한 웹 크기 조정 그룹의 다른 웹 사이트에 사용되는 가상 컴퓨터의 수인 **인스턴스 수**를 늘릴 수 있습니다.

**표준** 모드에서는 **인스턴스 크기**를 변경하여 각 인스턴스의 코어 수 및 메모리 용량도 늘릴 수 있습니다. 비용 효율성을 높이기 위해 Microsoft Azure에서 웹 사이트의 리소스를 동적으로 할당하도록 설정하는 **자동 크기 조정** 옵션을 선택할 수 있습니다.

웹 사이트의 크기 조정 옵션 구성에 대한 자세한 내용은 [웹 사이트 크기를 조정하는 방법][웹 사이트 크기를 조정하는 방법]을 참조하세요.

## 연결된 리소스

**연결된 리소스** 관리 페이지는 SQL 데이터베이스, MySQL 데이터베이스, Azure 저장소 계정 등 웹 사이트에서 사용 중인 Microsoft Azure 리소스의 목록을 제공합니다. 관리할 리소스의 이름을 클릭하십시오.

## 백업

**백업** 관리 페이지에서 웹 사이트의 자동 또는 수동 백업을 만들거나 웹 사이트를 이전 상태로 복원하거나 백업 중 하나를 기반으로 새 웹 사이트를 만들 수 있습니다. 자세한 내용은 [Microsoft Azure 웹 사이트 백업][Microsoft Azure 웹 사이트 백업](영문) 및 [Microsoft Azure 웹 사이트 복원][Microsoft Azure 웹 사이트 복원](영문)을 참조하십시오.

## 관리 페이지 아이콘

아이콘은 각 웹 사이트 관리 페이지의 맨 아래에 표시됩니다. 이 중 몇몇 아이콘은 여러 페이지에 표시되고 특정 페이지에만 표시되는 아이콘도 있습니다. 다음 아이콘이 **대시보드** 관리 페이지의 맨 아래에 표시됩니다.

-   **찾아보기** - 웹 사이트의 기본 페이지를 엽니다.
-   **중지** - 웹 사이트를 중지합니다.
-   **다시 시작** - 웹 사이트를 다시 시작합니다.
-   **도메인 관리** - 이 웹 사이트에 도메인을 매핑합니다. **무료** 크기 조정 모드의 사이트에는 사용할 수 없습니다.
-   **삭제** - 이 웹 사이트를 삭제합니다.
-   **WebMatrix** - WebMatrix에서 지원되는 웹 사이트를 엽니다. 웹 사이트를 변경하고 변경 내용을 Microsoft Azure의 웹 사이트에 다시 게시할 수 있습니다.

다음 아이콘은 **대시보드** 관리 페이지의 맨 아래에 표시되지 않지만 특정 작업을 수행할 수 있는 다른 관리 페이지의 맨 아래에는 표시됩니다.

-   **메트릭 추가** - **모니터** 관리 페이지의 맨 아래에 표시되며, 이 아이콘을 사용하여 모니터 관리 페이지에 표시되는 차트에 메트릭을 추가할 수 있습니다.
-   **연결** - **연결된 리소스** 관리 페이지의 맨 아래에 표시되며, 이 아이콘을 사용하여 다른 Microsoft Azure에 대한 관리 링크를 만들 수
-   있습니다. 예를 들어 웹 사이트에서 SQL 데이터베이스에 액세스하는 경우 **연결**을 클릭하여 데이터베이스 리소스에 대한 관리 링크를 만들 수 있습니다.

## FTP 자격 증명

사용할 수 있는 FTP 자격 증명 집합에는 *배포* 자격 증명과 *게시 프로필* 자격 증명의 두 가지가 있습니다. 주요 차이점은 다음과 같습니다.

**배포 자격 증명**

-   Microsoft 계정과 연결됩니다.
-   계정과 연결된 모든 구독의 모든 웹 사이트에 배포하는 데 사용할 수 있습니다.
-   사용자 이름/암호를 선택할 수 있습니다.
-   일반적으로 Git 또는 FTP 배포에 사용됩니다.

**게시 프로필 자격 증명**

-   단일 웹 사이트에 연결됩니다.
-   사용자 이름 또는 암호를 선택할 수 없습니다.
-   일반적으로 웹 배포에 사용되지만 FTP에 사용될 수도 있습니다.

두 가지 자격 증명 집합 중 하나를 선택할 수 있습니다. FTP 및 FTPS 호스트 이름은 대시보드의 **간략 상태** 아래에 나열됩니다.

### 배포 자격 증명 사용

배포 자격 증명을 설정하려면

1.  관리 포털에서 웹 사이트의 **대시보드** 탭으로 이동합니다.
2.  **배포 자격 증명 설정**을 클릭합니다.
3.  대화 상자에서 사용자 이름 및 암호를 입력합니다.

참고: 2단계에서 배포 자격 증명이 이미 있는 경우 포털에서 **배포 자격 증명 다시 설정** 메시지를 표시합니다. 이를 클릭하여 새 암호를 설정하거나 사용자 이름을 변경합니다.

배포 자격 증명은 Microsoft 계정과 연결됩니다. 사용자 이름 또는 암호를 변경한 경우 계정과 연결된 모든 웹 사이트에 변경 내용이 적용됩니다. Azure 구독에 여러 관리자가 있는 경우 각 관리자가 고유한 자격 증명을 가집니다.
전체 FTP 사용자 이름은 “website\\username”입니다. 이는 포털의 **간략 상태** 아래에 **배포/FTP 사용자**로 나열됩니다.

### 게시 프로필 자격 증명 사용

각 웹 사이트에 고유한 게시 프로필 자격 증명이 있습니다. 이러한 자격 증명을 보려면 다음을 수행합니다.

1.  관리 포털에서 웹 사이트의 **대시보드** 탭으로 이동합니다.
2.  **게시 프로필 다운로드**를 클릭합니다.

게시 프로필 이름은 XML 파일입니다. 여기에는 각각 웹 배포와 FTP에 대한 프로필 두 개가 포함되어 있습니다.

    <publishData>
      <publishProfile
        profileName="contoso - Web Deploy"
        publishMethod="MSDeploy"
        publishUrl="contoso.scm.azurewebsites.net:443"
        msdeploySite="contoso"
        userName="$contoso"
        userPWD="abc1234..."
        destinationAppUrl="http://contoso.azurewebsites.net"
        SQLServerDBConnectionString=""
        mySQLDBConnectionString=""
        hostingProviderForumLink="" 
        controlPanelLink="http://windows.azure.com">
        <databases/>
      </publishProfile>
      <publishProfile 
        profileName="contoso - FTP" 
        publishMethod="FTP" 
        publishUrl="ftp://waws-prod-bay-003.ftp.azurewebsites.windows.net/site/wwwroot" 
        ftpPassiveMode="True" 
        userName="contoso\$contoso" 
        userPWD=" abc1234..."  
        destinationAppUrl="http://contoso.azurewebsites.net" 
        SQLServerDBConnectionString="" 
        mySQLDBConnectionString="" 
        hostingProviderForumLink="" 
        controlPanelLink="http://windows.azure.com">
        <databases/>
      </publishProfile>
    </publishData>

`publishMethod="FTP"`를 사용하여 프로필을 찾습니다. 사용자 이름은 `userName` 아래에 나열되고, 암호는 `userPWD` 아래에 나열됩니다.

암호를 다시 설정하려면 **게시 프로필 자격 증명 다시 설정**을 클릭합니다. 새 자격 증명을 얻으려면 게시 프로필을 다시 다운로드합니다. 게시 프로필 자격 증명은 웹 사이트와 연결됩니다. 각 웹 사이트에 고유한 게시 프로필이 있습니다.

<!-- LINKS -->


  [WebMatrix 설치]: http://go.microsoft.com/fwlink/?LinkID=226244
  [Microsoft Azure SDK]: http://go.microsoft.com/fwlink/?LinkId=246928
  [웹 사이트를 모니터링하는 방법]: http://www.windowsazure.com/ko-KR/manage/services/web-sites/how-to-monitor-websites/
  [FTP 자격 증명]: #ftp-credentials
  [웹 사이트 크기를 조정하는 방법]: http://www.windowsazure.com/ko-KR/manage/services/web-sites/how-to-scale-websites
  [Microsoft Azure 웹 사이트에서 WebJob 기능 사용 방법]: http://www.windowsazure.com/ko-KR/documentation/articles/web-sites-create-web-jobs/
  [웹 사이트를 구성하는 방법]: http://www.windowsazure.com/ko-KR/manage/services/web-sites/how-to-configure-websites
  [Microsoft Azure 웹 사이트 백업]: http://www.windowsazure.com/ko-KR/documentation/articles/web-sites-backup/
  [Microsoft Azure 웹 사이트 복원]: http://www.windowsazure.com/ko-KR/documentation/articles/web-sites-restore/
