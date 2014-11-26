<properties linkid="manage-services-how-to-configure-websites" urlDisplayName="How to configure" pageTitle="How to configure websites - Azure service management" metaKeywords="Azure websites, configuring Azure websites, Azure SQL database, Azure MySQL" description="Learn how to configure websites in Azure, including how to configure a website to use a SQL Database or MySQL database." metaCanonical="" services="web-sites" documentationCenter="" title="How to Configure Websites" authors="mwasson" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/18/2014" ms.author="mwasson" />

# 웹 사이트를 구성하는 방법

Azure 관리 포털에서 웹 사이트의 구성 옵션 및 데이터베이스와 같은 다른 Azure 리소스의 링크를 변경할 수 있습니다.

## 목차

-   [방법: 웹 사이트의 구성 옵션 변경][방법: 웹 사이트의 구성 옵션 변경]
-   [방법: SQL 데이터베이스를 사용하도록 웹 사이트 구성][방법: SQL 데이터베이스를 사용하도록 웹 사이트 구성]
-   [방법: MySQL 데이터베이스를 사용하도록 웹 사이트 구성][방법: MySQL 데이터베이스를 사용하도록 웹 사이트 구성]
-   [방법: 사용자 지정 도메인 이름 구성][방법: 사용자 지정 도메인 이름 구성]
-   [방법: SSL을 사용하도록 웹 사이트 구성][방법: SSL을 사용하도록 웹 사이트 구성]
-   [다음 단계][다음 단계]

## <a name="howtochangeconfig"></a>방법: 웹 사이트의 구성 옵션 변경

<!-- HOW TO: CHANGE CONFIGURATION OPTIONS FOR A WEBSITE -->

웹 사이트의 구성 옵션을 설정하려면

1.  [관리 포털][관리 포털]에서 웹 사이트의 관리 페이지를 엽니다.
2.  **구성** 탭을 클릭합니다.

**구성** 탭에 다음 섹션이 있습니다.

### 일반

**프레임워크 버전**. 앱에서 다음 프레임워크를 사용하는 경우 이러한 옵션을 설정합니다.

-   **.NET Framework**: .NET Framework 버전을 설정합니다.
-   **PHP**: PHP 버전을 설정하거나, PHP를 사용하지 않도록 설정하려면 **끄기**를 선택합니다.
-   **Java**: 표시된 버전을 선택하여 Java를 사용하도록 설정하거나, **끄기**를 선택하여 Java를 사용하지 않도록 설정합니다.
-   Java를 사용하도록 설정한 경우 **웹 컨테이너** 옵션을 사용하여 Tomcat 버전과 Jetty 버전 간에 선택합니다.
-   **Python**: Python 버전을 설정하거나, Python을 사용하지 않도록 설정하려면 **끄기**를 선택합니다.

기술적인 이유로, 웹 사이트에 Java를 사용하도록 설정하면 .NET, PHP 및 Python 옵션은 사용하지 않도록 설정됩니다.

**관리되는 파이프라인 모드**. IIS [파이프라인 모드][파이프라인 모드]를 설정합니다. 이전 버전의 IIS가 필요한 레거시 웹 사이트가 있지 않으면 통합(기본값)으로 설정된 상태로 그대로 둡니다.

**플랫폼**. 응용 프로그램이 32비트 환경에서 실행되는지 또는 64비트 환경에서 실행되는지 선택합니다. 64비트 환경에는 기본 또는 표준 모드가 필요합니다. 무료 및 공유 모드는 항상 32비트 환경에서 실행됩니다.

**웹 소켓**. WebSocket 프로토콜을 사용하도록 설정하려면 **켜기**를 설정합니다. 예를 들어 웹 사이트에서 [ASP.NET SignalR][ASP.NET SignalR] 또는 [socket.io][socket.io]를 사용하는 경우가 여기에 해당합니다.

**Always On**. 기본적으로 웹 사이트는 일정 기간 동안 유휴 상태인 경우 언로드됩니다. 이를 통해 시스템 리소스가 절약됩니다. 기본 또는 표준 모드에서는 **Always On**을 사용하도록 설정하여 사이트를 항상 로드된 상태로 유지할 수 있습니다. 사이트에서 지속형 웹 작업을 실행하는 경우 **Always On**을 사용하도록 설정해야 합니다. 그렇지 않으면 웹 작업이 안정적으로 실행되지 않을 수 있습니다.

**Visual Studio Online에서 편집**. Visual Studio Online에서 라이브 코드 편집 기능을 사용하도록 설정합니다. 사용하도록 설정된 경우 대시보드 탭의 **간략 상태** 섹션 아래에 **Visual Studio Online에서 편집**이라는 링크가 표시됩니다. 이 링크를 클릭하면 웹 사이트를 온라인에서 직접 편집할 수 있습니다. 인증해야 하는 경우 기본 배포 자격 증명을 사용할 수 있습니다.

참고: 소스 제어에서 배포를 사용하도록 설정한 경우 배포가 Visual Studio Online 편집기에서 변경한 사항을 덮어쓸 수 있습니다.

### 인증서

기본 또는 표준 모드에서는 사용자 지정 도메인에 대해 SSL 인증서를 업로드할 수 있습니다. 자세한 내용은 [Azure 웹 사이트에 HTTPS 사용][Azure 웹 사이트에 HTTPS 사용]을 참조하세요.

업로드한 인증서가 여기에 나열됩니다. 인증서를 업로드한 후 해당 구독 및 지역의 웹 사이트에 할당할 수 있습니다. 유효한 도메인 내의 모든 사이트에 와일드카드 인증서를 사용할 수 있습니다. 해당 인증서에 대한 활성 바인딩이 없는 경우에만 인증서를 삭제할 수 있습니다.

### 도메인 이름

웹 사이트에 대한 추가 도메인 이름을 보거나 추가합니다. 자세한 내용은 [Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성][Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성]을 참조하세요.

### SSL 바인딩

SSL 인증서를 업로드한 경우 사용자 지정 도메인 이름에 바인딩할 수 있습니다. 자세한 내용은 [Azure 웹 사이트에 HTTPS 사용][Azure 웹 사이트에 HTTPS 사용]을 참조하세요.

### 배포

이 섹션은 소스 제어에서 배포를 사용하도록 설정한 경우에만 나타납니다. 배포를 구성하려면 이러한 설정을 사용하십시오.

-   **Git URL**. Azure 웹 사이트에 대한 Git 리포지토리를 만든 경우 이 URL이 콘텐츠를 푸시하는 URL입니다.
-   **배포 트리거 URL**. 이 URL은 커밋이 리포지토리로 푸시될 때 배포를 트리거하는 GitHub, CodePlex, Bitbucket 또는 기타 리포지토리에 설정될 수 있습니다.
-   **배포할 분기**. 콘텐츠를 푸시할 때 콘텐츠가 배포될 분기를 지정합니다.

소스 제어에서 배포를 설정하려면 **대시보드** 탭을 표시하고 **소스 제어에서 배포 설정**을 클릭합니다.

### 응용 프로그램 진단

로깅을 지원하는 웹 응용 프로그램의 진단 정보를 기록하는 옵션입니다.

-   **파일 시스템**. 웹 사이트의 파일 시스템에 로그를 기록합니다. 파일 시스템 로깅은 12시간 동안 유지됩니다. 웹 사이트의 로그는 FTP 공유에서 액세스할 수 있습니다. [FTP 자격 증명][FTP 자격 증명]을 참조하세요.
-   **테이블 저장소**. Azure 테이블 저장소에 로그를 기록합니다. 시간 제한은 없으며, 사용하지 않도록 설정할 때까지 로깅이 사용 가능한 상태로 유지됩니다.
-   **Blob 저장소**. Azure Blob 저장소에 로그를 기록합니다. 시간 제한은 없으며, 사용하지 않도록 설정할 때까지 로깅이 사용 가능한 상태로 유지됩니다.

**로깅 수준**. 로깅을 사용하도록 설정하면 이 옵션은 기록될 정보의 양(오류, 경고, 정보 또는 세부 정보 표시)을 지정합니다.

**테이블 저장소 관리**. 테이블 저장소를 사용하도록 설정한 경우 이 단추를 클릭하여 저장소 계정 및 테이블 이름을 설정합니다.

**Blob 저장소 관리.** Blob 저장소를 사용하도록 설정한 경우 이 단추를 클릭하여 저장소 계정 및 Blob 저장소 이름을 설정합니다.

### 사이트 진단

웹 사이트에 대한 진단 정보를 수집하는 옵션입니다.

**웹 서버 로깅**. 웹 서버 로깅을 사용하도록 설정합니다. 로그는 W3C 확장 로그 파일 형식으로 저장됩니다. Azure 저장소 또는 웹 사이트의 파일 시스템에 로그를 저장할 수 있습니다.

-   **파일 시스템**을 선택한 경우 대시보드 관리 페이지의 "FTP 진단 로그" 아래에 나열된 FTP 사이트에 로그가 저장됩니다. [FTP 자격 증명][FTP 자격 증명]을 참조하세요.
-   **파일 시스템**을 선택한 경우 **할당량** 상자를 사용하여 로그 파일의 최대 디스크 공간 크기를 설정합니다. 최소값은 25MB이고 최대값은 100MB입니다. 기본값은 35MB입니다. 할당량에 도달하는 경우 가장 오래된 파일부터 연속적으로 최신 파일로 덮어씁니다. 100MB보다 큰 기록을 유지해야 하는 경우 더 많은 저장 용량을 지원하는 Azure 저장소를 사용하세요.
-   필요한 경우 일정 기간 후 파일을 자동으로 삭제하려면 **보존 설정**을 클릭합니다. 기본적으로 로그는 삭제되지 않습니다.

**자세한 오류 메시지**. 사용하도록 설정한 경우 자세한 오류 메시지가 .htm 파일로 저장됩니다. 파일을 보려면 대시보드 페이지의 "FTP 진단 로그" 아래에 나열된 FTP 사이트로 이동합니다. 파일은 FTP 사이트의 /LogFiles/DetailedErrors 아래에 저장됩니다. [FTP 자격 증명][FTP 자격 증명]을 참조하세요.

**실패한 요청 추적**. 사용하도록 설정한 경우 실패한 요청이 XML 파일에 로깅됩니다. 파일을 보려면 대시보드 페이지의 "FTP 진단 로그" 아래에 나열된 FTP 사이트로 이동합니다. [FTP 자격 증명][FTP 자격 증명]을 참조하세요. 파일은 /LogFiles/W3SVC*xxx*(여기서 xxx는 고유 식별자) 아래에 저장됩니다. 이 폴더에는 하나의 XSL 파일 및 하나 이상의 XML 파일이 포함되어 있습니다. XSL 파일은 XML 파일 내용의 서식을 지정하고 필터링하는 기능을 제공하므로 XSL 파일을 다운로드해야 합니다.

**원격 디버깅**. 원격 디버깅을 사용하도록 설정합니다. 이를 사용하도록 설정하면 Visual Studio의 원격 디버거를 사용하여 Azure 웹 사이트에 직접 연결할 수 있습니다. 원격 디버깅은 48시간 동안 사용 가능한 상태로 유지됩니다.

**참고**: 원격 디버깅은 20자를 초과하는 사이트 이름 또는 사용자 이름에서는 작동하지 않습니다.

### 모니터링

기본 또는 표준 모드에서는 지리적으로 분산된 최대 세 곳의 HTTP 또는 HTTPS 끝점에 대한 가용성을 테스트할 수 있습니다. HTTP 응답 코드가 오류(4xx 또는 5xx)이거나 응답에 30초 넘게 걸리는 경우 모니터링 테스트가 실패합니다. 지정된 모든 위치에서 모니터링 테스트가 성공하는 경우 끝점은 사용 가능한 것으로 간주됩니다.

자세한 내용은 [방법: 웹 끝점 상태 모니터링][방법: 웹 끝점 상태 모니터링]을 참조하세요.

### 개발자 분석

**추가 기능**을 선택하여 목록에서 분석 추가 기능을 선택하거나, Azure 저장소로 이동하여 분석 추가 기능을 선택합니다. **사용자 지정**을 선택하면 목록에서 New Relic 같은 분석 공급자를 선택할 수 있습니다. 사용자 지정 공급자를 사용하는 경우 **공급자 키** 상자에 라이선스 키를 입력해야 합니다.

Azure 웹 사이트에서 New Relic 사용에 대한 자세한 내용은 [Azure 웹 사이트에서 New Relic 응용 프로그램 성능 관리][Azure 웹 사이트에서 New Relic 응용 프로그램 성능 관리]를 참조하세요.

### 앱 설정

시작할 때 웹 응용 프로그램에서 로드되는 이름/값 쌍입니다.

-   .NET 사이트의 경우 런타임에 이러한 설정이 .NET 구성 AppSettings에 주입되어 기존 설정을 재정의합니다.

-   PHP, Python, Java 및 Node 응용 프로그램에서는 런타임에 환경 변수로 이러한 설정에 액세스할 수 있습니다. 각 앱 설정에 대해 두 개의 환경 변수가 만들어집니다. 하나는 앱 설정 항목에 의해 이름이 지정되고, 다른 하나는 이름에 APPSETTING\_ 접두사가 붙습니다. 둘 다 같은 값을 포함합니다.

### 연결 문자열

연결된 리소스의 연결 문자열입니다.

.NET 사이트의 경우 런타임에 이러한 연결 문자열이 .NET 구성 connectionStrings 설정에 주입되어 키가 연결된 데이터베이스 이름과 같은 기존 항목을 재정의합니다.

PHP, Python, Java 및 Node 응용 프로그램에서는 런타임에 이러한 설정을 환경 변수로 사용할 수 있으며, 환경 변수 앞에는 연결 형식이 옵니다. 환경 변수 접두사는 다음과 같습니다.

-   SQL Server: SQLCONNSTR\_
-   MySQL: MYSQLCONNSTR\_
-   SQL 데이터베이스: SQLAZURECONNSTR\_
-   사용자 지정: CUSTOMCONNSTR\_

예를 들어 MySql 연결 문자열 이름이 connectionstring1로 지정된 경우 환경 변수 `MYSQLCONNSTR_connectionString1`을 통해 액세스될 수 있습니다.

**참고**: 데이터베이스 리소스를 웹 사이트에 연결할 때도 연결 문자열이 만들어집니다. 이러한 방식으로 만들어진 연결 문자열은 구성 관리 페이지에서 읽기 전용으로 볼 수 있습니다.

### 기본 문서

웹 사이트의 기본 문서는 웹 사이트의 특정 페이지를 지정하지 않은 경우에 표시되는 웹 페이지입니다. 웹 사이트에 목록의 파일이 두 개 이상 포함된 경우 기본 문서를 목록의 맨 위에 두어야 합니다.

웹 응용 프로그램에서는 정적 콘텐츠를 제공하는 대신 URL을 기반으로 라우팅되는 모듈을 사용할 수도 있습니다(이 경우에도 기본 문서는 없음).

### 처리기 매핑

이 영역을 사용하여 사용자 지정 스크립트 프로세서를 추가해 특정 파일 확장명에 대한 요청을 처리할 수 있습니다.

-   **확장명**. 처리할 파일 확장명입니다(예: \*.php 또는 handler.fcgi).
-   **스크립트 프로세서 경로**. 스크립트 프로세서의 절대 경로입니다. 파일 확장명과 일치하는 파일에 대한 요청이 스크립트 프로세서에서 처리됩니다. `D:\home\site\wwwroot` 경로를 사용하여 사이트의 루트 디렉터리를 참조합니다.
-   **추가 인수**. 스크립트 프로세서에 대한 선택적 명령줄 인수입니다.

### 가상 응용 프로그램 및 디렉터리

웹 사이트와 연결된 가상 응용 프로그램 및 디렉터리를 구성하려면 각 가상 디렉터리와 사이트 루트에 상대적인 해당 실제 경로를 지정합니다. 경우에 따라 **응용 프로그램** 확인란을 선택하여 가상 디렉터리를 사이트 구성의 응용 프로그램으로 표시할 수 있습니다.

<!-- HOW TO: CONFIGURE A WEBSITE TO USE A SQL DATABASE -->

## <a name="howtoconfigSQL"></a>방법: SQL 데이터베이스를 사용하도록 웹 사이트 구성

SQL 데이터베이스에 웹 사이트를 연결하려면 다음 단계를 따르세요.

1.  [관리 포털][1]에서 **웹 사이트**를 선택하여 현재 로그온된 계정으로 만든 웹 사이트의 목록을 표시합니다.

2.  웹 사이트 목록에서 웹 사이트를 선택하여 해당 웹 사이트의 **관리** 페이지를 엽니다.

3.  **연결된 리소스** 탭을 클릭하면 **You have no linked resources**가 나타나는 **연결된 리소스** 페이지가 표시됩니다.

4.  **연결된 리소스**를 클릭하여 **Link a Resource** 마법사를 엽니다.

5.  **새 리소스 만들기**를 클릭하여 웹 사이트에 연결될 수 있는 리소스 유형 목록을 표시합니다.

6.  **SQL 데이터베이스**를 클릭하여 **Link Database** 마법사를 표시합니다.

7.  **Link Database** 마법사의 3~4페이지에 있는 필수 필드를 완성하고 4페이지의 **마침** 확인 표시를 클릭합니다.

Azure는 지정한 매개 변수로 SQL 데이터베이스를 만들고 웹 사이트에 데이터베이스를 연결합니다.

<!-- HOW TO: CONFIGURE A WEBSITE TO USE A MYSQL DATABASE -->

## <a name="howtoconfigMySQL"></a>방법: MySQL 데이터베이스를 사용하도록 웹 사이트 구성

MySQL 데이터베이스를 사용하도록 웹 사이트를 구성하려면 SQL 데이터베이스를 사용하는 것과 동일한 단계를 따르되, **리소스 연결** 마법사에서 **SQL 데이터베이스** 대신 **MySQL 데이터베이스**를 선택합니다.

또는 **사용자 지정 만들기** 옵션을 사용하여 웹 사이트를 만들 수 있습니다. **데이터베이스** 드롭다운에서 **새 MySQL 데이터베이스 만들기** 또는 **Use an existing MySQL database** 중 하나를 선택합니다.

## <a name="howtodomain"></a>방법: 사용자 지정 도메인 이름 구성

사용자 지정 도메인 이름을 사용하도록 웹 사이트를 구성하는 방법에 대한 자세한 내용은 [Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성][Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성]을 참조하세요.

## <a name="howtoconfigSSL"></a>방법: SSL을 사용하도록 웹 사이트 구성

Azure에서 사용자 지정 도메인에 대해 SSL을 구성하는 방법에 대한 자세한 내용은 [Azure 웹 사이트에 HTTPS 사용][2]을 참조하십시오.

## <a name="next"></a>다음 단계

-   [웹 사이트 크기를 조정하는 방법][웹 사이트 크기를 조정하는 방법]

-   [웹 사이트를 모니터링하는 방법(영문)][웹 사이트를 모니터링하는 방법(영문)]

  [방법: 웹 사이트의 구성 옵션 변경]: #howtochangeconfig
  [방법: SQL 데이터베이스를 사용하도록 웹 사이트 구성]: #howtoconfigSQL
  [방법: MySQL 데이터베이스를 사용하도록 웹 사이트 구성]: #howtoconfigMySQL
  [방법: 사용자 지정 도메인 이름 구성]: #howtodomain
  [방법: SSL을 사용하도록 웹 사이트 구성]: #howtoconfigSSL
  [다음 단계]: #next
  [관리 포털]: https://manage.windowsazure.com/
  [파이프라인 모드]: http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
  [ASP.NET SignalR]: http://www.asp.net/signalr
  [socket.io]: http://azure.microsoft.com/ko-KR/documentation/articles/web-sites-nodejs-chat-app-socketio/
  [Azure 웹 사이트에 HTTPS 사용]: href="http://www.windowsazure.com/ko-KR/documentation/articles/web-sites-configure-ssl-certificate/
  [Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성]: http://www.windowsazure.com/ko-KR/documentation/articles/web-sites-custom-domain-name/
  [FTP 자격 증명]: http://azure.microsoft.com/ko-KR/documentation/articles/web-sites-manage#ftp-credentials
  [방법: 웹 끝점 상태 모니터링]: http://go.microsoft.com/fwLink/?LinkID=279906&clcid=0x409
  [Azure 웹 사이트에서 New Relic 응용 프로그램 성능 관리]: http://www.windowsazure.com/ko-KR/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/
  [1]: http://manage.windowsazure.com
  [2]: http://www.windowsazure.com/ko-KR/documentation/articles/web-sites-configure-ssl-certificate/
  [웹 사이트 크기를 조정하는 방법]: http://www.windowsazure.com/ko-KR/documentation/articles/web-sites-scale/
  [웹 사이트를 모니터링하는 방법(영문)]: http://www.windowsazure.com/ko-KR/documentation/articles/web-sites-monitor/
