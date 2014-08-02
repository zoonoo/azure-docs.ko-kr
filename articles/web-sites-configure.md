<properties linkid="manage-services-how-to-configure-websites" urlDisplayName="How to configure" pageTitle="How to configure web sites - Azure service management" metaKeywords="Azure websites, configuring Azure websites, Azure SQL database, Azure MySQL" description="Learn how to configure web sites in Azure, including how to configure a web site to use a SQL Database or MySQL database." metaCanonical="" services="web-sites" documentationCenter="" title="How to Configure Web Sites" authors="timamm" solutions="" manager="" editor="mollybos" />

웹 사이트를 구성하는 방법
=========================

Azure 관리 포털에서 웹 사이트의 구성 옵션을 변경하고 웹 사이트를 다른 Azure 리소스에 연결할 수 있습니다. 예를 들어 웹 사이트를 SQL 데이터베이스에 연결하여 추가 기능을 제공할 수 있습니다. 또한 웹 사이트를 구성하여 기존 또는 새로운 MySQL 데이터베이스를 사용할 수 있습니다.

목차
----

-   [방법: 웹 사이트의 구성 옵션 변경](#howtochangeconfig)
-   [방법: SQL 데이터베이스를 사용하도록 웹 사이트 구성](#howtoconfigSQL)
-   [방법: MySQL 데이터베이스를 사용하도록 웹 사이트 구성](#howtoconfigMySQL)
-   [방법: 사용자 지정 도메인 이름 구성](#howtodomain)
-   [방법: SSL을 사용하도록 웹 사이트 구성](#howtoconfigSSL)
-   [다음 단계](#next)

방법: 웹 사이트의 구성 옵션 변경
--------------------------------

웹 사이트의 구성 옵션을 변경하려면 다음 단계를 따르십시오.

1.  관리 포털에서 웹 사이트의 관리 페이지를 엽니다.
2.  **구성** 탭을 클릭하여 **구성** 관리 페이지를 엽니다.
3.  필요에 따라 웹 사이트의 다음 구성 옵션을 설정합니다.
    -   **일반**
        -   **.NET Framework 버전** - 웹 응용 프로그램에 .NET Framework가 사용되는 경우 웹 응용 프로그램에 필요한 Framework 버전을 설정합니다.
        -   **PHP Version** - 웹 응용 프로그램에 PHP가 사용되는 경우 웹 응용 프로그램에 필요한 PHP 버전을 설정합니다.
        -   **Java Version** - 표시된 버전의 Java를 선택하여 웹 응용 프로그램에 사용하도록 설정하거나, **끄기**를 선택하여 Java를 사용하지 않도록 설정합니다. 웹 응용 프로그램에 Java를 사용하도록 설정하는 경우 **Web Container** 옵션을 사용하여 Tomcat 및 Jetty 버전 중에서 선택할 수 있습니다.

            **참고**: 기술적인 이유로, 웹 사이트에 Java를 사용하도록 설정하면 .NET, PHP 및 Python 옵션은 사용하지 않도록 설정됩니다.

        -   **Python Version** - Python 버전이 표시되며, 이는 구성이 불가능합니다.
        -   **관리되는 파이프라인 모드** - **클래식** 및 **통합**의 두 옵션이 제공되며 통합이 기본값입니다. 레거시 웹 사이트를 이전 버전의 IIS에서만 실행하는 경우에만 클래식 옵션을 사용해야 합니다.
        -   **플랫폼** - 표준 모드의 사이트인 경우 응용 프로그램 실행 환경을 32비트 또는 64비트 중에서 선택할 수 있습니다. 무료 및 공유 모드의 사이트는 항상 32비트 환경에서 실행됩니다.
        -   **웹 소켓** - 웹 사이트를 사용하여 채팅과 같은 실시간 요청 패턴 응용 프로그램을 사용하려면 **켜기**를 선택합니다.
        -   **Always On** - 기본적으로 웹 사이트는 일정 기간 동안 유휴 상태인 경우 언로드됩니다. 이를 통해 시스템 리소스가 절약됩니다. 사이트가 항상 로드된 상태여야 하는 경우 표준 모드의 사이트에 **Always On** 설정을 사용하도록 설정할 수 있습니다. **Always On**을 사용하지 않도록 설정하면 지속형 웹 작업이 안정적으로 실행되지 않을 수 있으므로, 사이트에서 지속형 웹 작업을 실행하는 경우 **Always On**을 사용하도록 설정해야 합니다.
        -   **Edit in Visual Studio Online** - Visual Studio Online에서 라이브 코드 편집 기능을 사용하도록 설정하려면 **켜기**를 선택합니다. 이 구성 변경을 저장한 후 대시보드 탭의 **간략 상태** 섹션에 **Edit in Visual Studio Online**이라는 링크가 표시됩니다. 링크를 클릭하면 웹 사이트를 온라인에서 직접 편집할 수 있습니다. 인증해야 하는 경우 기본 배포 자격 증명을 사용할 수 있습니다.

            **참고**: '소스 제어에서 배포'를 사용하도록 설정한 경우 배포 사이트가 Visual Studio Online 편집기에서 변경한 사항을 덮어쓸 수 있습니다. 따라서 Visual Studio Online에서 사이트 콘텐츠를 직접 편집하려는 경우 '소스 제어에서 배포'를 사용하지 않는 것이 가장 좋습니다.

    -   **인증서** - 표준 모드에서만 **업로드**를 클릭하여 사용자 지정 도메인용 SSL 인증서를 업로드할 수 있습니다. 업로드한 인증서가 여기에 나열됩니다. 와일드카드("별") 인증서(별표가 있는 인증서)가 지원됩니다. 인증서를 업로드한 후 구독 및 지역에 있는 모든 웹 사이트에 할당할 수 있습니다. 별 인증서는 오직 한 번만 업로드해야 하지만 유효한 도메인 내의 모든 사이트에 사용될 수 있습니다. 사이트에 지정된 인증서에 대해 활성화된 바인딩이 없는 경우에만 인증서를 삭제할 수 있습니다.
        **참고:** 사용자 지정 도메인은 공유 및 표준 모드에서만 사용할 수 있으며, 사용자 지정 도메인에 대한 SSL 지원은 표준 모드에서만 사용할 수 있습니다. Azure에서 사용자 지정 도메인에 대해 SSL을 구성하는 방법에 대한 자세한 내용은 [Azure 웹 사이트에 HTTPS 사용](http://www.windowsazure.com/en-us/documentation/articles/web-sites-configure-ssl-certificate/)을 참조하십시오.
    -   **도메인 이름** - 여기서 웹 사이트의 추가 도메인 이름을 보거나 추가합니다. **도메인 관리**를 클릭하여 사용자 지정 도메인을 추가할 수 있습니다. 사용자 지정 도메인은 **공유** 및 **표준** 모드에서만 사용할 수 있습니다. **크기 조정** 관리 페이지에서 웹 사이트 모드를 변경할 수 있습니다. 사용자 지정 도메인은 무료 모드에서 사용할 수 없습니다. 사용자 지정 도메인 구성에 대한 자세한 내용은 [Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성](http://www.windowsazure.com/en-us/documentation/articles/web-sites-custom-domain-name/)을 참조하십시오.
    -   **SSL 바인딩** - 사용자 지정 도메인에 대한 SSL 바인딩은 표준 모드에서만 사용할 수 있습니다. 특정 도메인 이름에 대한 SSL 모드(**SNI**, **IP** 또는 **No SSL**)를 선택합니다. SNI 또는 IP를 선택하는 경우 업로드한 인증서에서 도메인에 대한 인증서를 지정할 수 있습니다. Azure에서 사용자 지정 도메인에 대해 SSL을 구성하는 방법에 대한 자세한 내용은 [Azure 웹 사이트에 HTTPS 사용](http://www.windowsazure.com/en-us/documentation/articles/web-sites-configure-ssl-certificate/)을 참조하십시오. SNI에 대한 자세한 내용은 [서버 이름 표시](http://en.wikipedia.org/wiki/Server_Name_Indication)를 참조하십시오.
    -   **배포** - 다음 설정을 사용하여 배포를 구성합니다.
        -   **Git URL** - Azure 웹 사이트에서 Git 리포지토리를 만든 경우 이 URL이 콘텐츠가 푸시되는 위치가 됩니다.
        -   **배포 트리거 URL** - 이 URL은 커밋이 리포지토리로 푸시될 때 배포를 트리거하는 GitHub, CodePlex, Bitbucket 또는 기타 리포지토리에 설정될 수 있습니다.
        -   **배포할 분기** - 이 옵션을 통해 콘텐츠를 푸시할 때 콘텐츠가 배포될 분기를 지정할 수 있습니다.
    -   **응용 프로그램 진단** - 추적을 사용하여 코드가 계측된 웹 응용 프로그램에서 진단 추적을 수집하기 위한 옵션을 설정합니다. 응용 프로그램 진단에 사용할 수 있는 로깅 옵션은 다음과 같습니다.
        -   **Application Logging (File System)** - 웹 사이트의 파일 시스템에 응용 프로그램 로그를 기록하려는 경우 **켜기**를 선택합니다. 이를 사용하도록 설정하면 파일 시스템 로깅이 12시간 동안 유지됩니다. 웹 사이트의 로그는 FTP 공유에서 액세스할 수 있습니다. FTP 공유에 대한 링크는 **대시보드**에서 찾을 수 있습니다. **간략 상태**에서 **FTP Diagnostic Logs** 또는 **FTPS Diagnostic Logs**를 선택합니다.
        -   **Application Logging (Storage)** - Azure 저장소 계정에 응용 프로그램 로그를 기록하려는 경우 **켜기**를 선택합니다. 저장소 계정에 대한 로깅에는 시간 제한이 없으며 사용하지 않도록 설정할 때까지 사용 상태로 유지됩니다. 기본적으로 로그는 WAWSAppLogTable이라는 테이블에 저장됩니다.
        -   **로깅 수준** - 로깅을 사용하도록 설정하면 이 옵션은 기록될 정보의 양(오류, 경고, 정보 또는 세부 정보 표시)을 지정합니다.
        -   **Diagnostic Storage** - **Manage Connection**을 클릭하면 Azure 저장소 계정에 로그를 저장하는 데 사용하는 다음 옵션이 있는 **Manage diagnostic storage** 대화 상자가 열립니다.
            -   **저장소 계정 이름** - 로그를 저장할 저장소 계정을 선택합니다.
            -   **저장소 액세스 키** - 선택한 저장소 계정의 키를 표시합니다. 저장소 계정의 키를 재생성한 경우 수동으로 새 키를 여기에 입력하거나 **동기화** 단추 중 하나를 사용합니다. 동기화 단추는 현재 로그온한 사용자가 선택한 저장소 계정에 대한 액세스 권한을 가진 경우에만 사용할 수 있습니다.
            -   **Synchronize Primary Key** - Azure 저장소 계정의 최신 기본 키를 검색합니다.
            -   **Synchronize Secondary Key** - Azure 저장소 계정의 최신 보조 키를 검색합니다.
                **참고:** Azure 저장소 액세스 키에 대한 자세한 내용은 [방법: 저장소 액세스 키 보기, 복사 및 재생성](http://www.windowsazure.com/en-us/documentation/articles/storage-manage-storage-account/#regeneratestoragekeys)(영문)을 참조하십시오.
    -   **사이트 진단** - 웹 사이트에 대한 진단 정보를 수집하기 위해 다음과 같은 옵션을 설정합니다.
        -   **웹 서버 로깅** - 웹 사이트에 웹 서버 로깅을 사용하도록 설정할지 여부를 지정합니다. 웹 서버 로그는 W3C 확장 로그 파일 형식으로 저장됩니다. 로그는 Azure 저장소 또는 파일 시스템에 저장할 수 있습니다.
            **팁**: 파일 시스템의 최대 로그 저장 크기는 100MB입니다. 이보다 큰 기록을 유지해야 하는 경우 더 많은 저장 용량을 지원하는 Azure 저장소를 사용하십시오.
            -   웹 서버 로그를 Azure 저장소 계정에 저장하려면 **저장소**를 선택한 후 **저장소 관리**를 선택합니다. **Manage Storage for Site Diagnostics** 대화 상자에서 **저장소 계정** 옵션을 사용하여 로그를 보관할 컨테이너의 Azure 저장소 계정을 선택합니다. **Azure Blob Container** 옵션을 사용하여 로그를 보관할 컨테이너를 선택하거나 **Create a new blob container**를 선택하여 새 컨테이너 이름을 지정할 수 있는 **Blob Name** 상자를 사용하도록 설정합니다.
                **참고**: 저장소 계정이 아직 없는 경우 Azure 포털의 **저장소** 섹션으로 이동하고 **새로 만들기**를 클릭하여 계정을 만들 수 있습니다.
            -   **파일 시스템**을 선택하는 경우 로그는 대시보드 관리 페이지의 **FTP Diagnostic Logs** 아래에 나열된 FTP 사이트에 저장됩니다. 파일 시스템 저장소를 사용하도록 설정하면 **할당량** 상자를 사용하여 로그 파일의 최대 디스크 공간 크기를 설정할 수 있습니다. 최소값은 25MB이고 최대값은 100MB입니다. 기본값은 35MB입니다. 할당량에 도달하는 경우 가장 오래된 파일부터 연속적으로 최신 파일로 덮어씁니다.
            -   기본적으로 Azure 저장소 계정의 웹 서버 로그는 절대 삭제되지 않습니다. 일정 기간 후 로그가 자동으로 삭제되도록 지정하려면 **Set Retention**을 선택하고 **보존 기간** 상자에 로그를 보관할 기간(일)을 입력합니다. 파일 시스템 저장소에 대해서도 **Set Retention** 옵션을 사용할 수 있습니다.
        -   **자세한 오류 메시지** - 웹 사이트에 대해 자세한 오류 메시지를 로깅할지 여부를 지정합니다. 이를 사용하도록 설정하면 자세한 오류 메시지가 .htm 파일 형식으로 대시보드 관리 페이지의 FTP Diagnostic Logs 아래에 나열된 FTP 사이트에 저장됩니다. 지정된 FTP 사이트에 연결된 후 /LogFiles/DetailedErrors/로 이동하여 자세한 오류 메시지가 포함된 .htm 파일을 검색합니다.
        -   **실패한 요청 추적** - 실패한 요청 추적을 사용하도록 설정할지 여부를 지정합니다. 이를 사용하도록 설정하면 실패한 요청 추적 출력이 XML 파일에 기록되고 대시보드 관리 페이지의 FTP Diagnostic Logs 아래에 나열된 FTP 사이트에 저장됩니다. 지정된 FTP 사이트에 연결된 후 /LogFiles/W3SVC########\#로 이동하여 실패한 요청 추적 출력이 포함된 XML 파일을 검색합니다. 여기서 ########\#은 웹 사이트의 고유 식별자를 나타냅니다.
            **중요**
            /LogFiles/W3SVC########\#/ 폴더에는 하나의 XSL 파일과 하나 이상의 XML 파일이 포함되어 있습니다. XSL 파일은 XML 파일을 Internet Explorer에서 볼 때 XML 파일의 내용에 서식을 지정하고 필터링하는 기능을 제공하므로 XSL 파일은 XML 파일과 동일한 디렉터리에 다운로드해야 합니다.
        -   **원격 디버깅** - 선택한 Visual Studio 2012 또는 Visual Studio 2013에서 원격 디버깅을 사용하도록 설정하려면 이 옵션을 **켜기**로 설정합니다. 이를 사용하도록 설정하면 Visual Studio의 원격 디버거를 사용하여 Azure 웹 사이트에 바로 연결할 수 있습니다.
             **참고**: 원격 디버깅은 48시간 동안만 사용하도록 설정되며 20자를 초과하는 사이트 이름 또는 사용자 이름에서는 작동하지 않습니다.
    -   **모니터링** - 표준 모드의 웹 사이트에서는 HTTP 또는 HTTPS 끝점의 가용성을 테스트할 수 있습니다. 지리적으로 분산된 최대 3곳의 끝점을 테스트할 수 있습니다. HTTP 응답 코드가 400 이상이거나 응답에 30초 넘게 걸리는 경우 모니터링 테스트가 실패합니다. 지정된 모든 위치에서 모니터링 테스트가 성공하는 경우 끝점은 사용 가능한 것으로 간주됩니다.
    -   **developer analytics** - **추가 기능**을 선택하여 목록에서 분석 추가 기능을 선택하거나, Azure 저장소로 이동하여 분석 추가 기능을 선택합니다. **사용자 지정**을 선택하면 목록에서 New Relic 같은 분석 공급자를 선택할 수 있습니다. 사용자 지정 공급자를 사용하는 경우 **공급자 키** 상자에 라이선스 키를 입력해야 합니다.
         **참고**: Azure 웹 사이트에서 New Relic 사용에 대한 자세한 내용은 [Azure 웹 사이트에서 New Relic 응용 프로그램 성능 관리](http://www.windowsazure.com/en-us/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/)(영문)를 참조하십시오.
    -   **앱 설정** - 시작할 때 웹 응용 프로그램에서 로드되는 이름/값 쌍을 지정합니다. .NET 사이트의 경우 런타임에 이러한 설정이 .NET 구성 AppSettings에 주입되어 기존 설정을 재정의합니다. PHP 및 노드 사이트에서는 런타임에 이러한 설정을 환경 변수로 사용할 수 있습니다.
    -   **연결 문자열** - 연결된 리소스의 연결 문자열을 봅니다. .NET 사이트의 경우 런타임에 이러한 연결 문자열이 .NET 구성 connectionStrings 설정에 주입되어 키가 연결된 데이터베이스 이름과 같은 기존 항목을 재정의합니다. PHP 및 노드 사이트에서는 런타임에 이러한 설정을 환경 변수로 사용할 수 있으며, 환경 변수 앞에는 연결 형식이 옵니다. 환경 변수 접두사는 다음과 같습니다.
        -   SQL Server: SQLCONNSTR\_
        -   MySQL: MYSQLCONNSTR\_
        -   SQL 데이터베이스: SQLAZURECONNSTR\_
        -   사용자 지정: CUSTOMCONNSTR\_

        예를 들어 MySql 연결 문자열 이름이 connectionstring1로 지정된 경우 환경 변수 `MYSQLCONNSTR_connectionString1`을 통해 액세스될 수 있습니다.
        **참고**: 연결 문자열은 웹 사이트에 데이터베이스 리소스를 연결할 때 만들어지고 구성 관리 페이지에서 읽기 전용으로 볼 수 있습니다.
    -   **기본 문서** - 목록에 아직 없는 경우 웹 사이트의 기본 문서를 이 목록에 추가합니다. 웹 사이트의 기본 문서는 사용자가 웹 사이트로 이동할 때 웹 사이트의 특정 페이지를 지정하지 않은 경우에 표시되는 웹 페이지입니다. 따라서 웹 사이트 http://contoso.com이 있고 기본 문서가 default.htm으로 설정된 경우 사용자가 브라우저에서 http://www.contoso.com을 지정하면 http://www.contoso.com/default.htm으로 라우팅됩니다. 웹 사이트 목록에 파일이 두 개 이상 포함된 경우 목록의 파일 순서를 변경하여 웹 사이트의 기본 문서가 목록의 맨 위에 오도록 해야 합니다.
    -   **처리기 매핑** - 특정 파일 확장명에 대한 요청을 처리할 사용자 지정 스크립트 프로세서를 추가합니다. **확장명** 상자에 처리할 파일 확장명(예: \*.php 또는 handler.fcgi)을 지정합니다. 이 패턴과 일치하는 파일에 대한 요청은 **Script Processor Path** 상자에 지정한 스크립트 프로세서에 의해 처리됩니다. 스크립트 프로세서에 대해 절대 경로가 필요합니다(D:\\home\\site\\wwwroot가 사이트의 루트 디렉터리 참조에 사용됨). 스크립트 프로세서의 선택적 명령줄 인수는 **Additional Arguments (Optional)** 상자에 지정할 수 있습니다.
    -   **virtual applications and directories** - 웹 사이트에 연결된 가상 응용 프로그램 및 디렉터리를 구성하려면 각 가상 디렉터리를 지정하고 사이트 루트에 상대적인 실제 경로를 지정합니다. 경우에 따라 **응용 프로그램** 확인란을 선택하여 가상 디렉터리를 사이트 구성의 응용 프로그램으로 표시할 수 있습니다.

4 .  **구성** 관리 페이지 맨 아래에 있는 **저장**을 클릭하여 구성 변경 내용을 저장합니다.

## 방법: SQL 데이터베이스를 사용하도록 웹 사이트 구성

SQL 데이터베이스에 웹 사이트를 연결하려면 다음 단계를 따르십시오.

1.  [관리 포털](http://manage.windowsazure.com)에서 **웹 사이트**를 선택하여 현재 로그온된 계정으로 만든 웹 사이트의 목록을 표시합니다.

2.  웹 사이트 목록에서 웹 사이트를 선택하여 웹 사이트의 **관리** 페이지를 엽니다.

3.  **연결된 리소스** 탭을 클릭하면 **You have no linked resources**가 나타나는 **연결된 리소스** 페이지가 표시됩니다.

4.  **연결된 리소스**를 클릭하여 **Link a Resource** 마법사를 엽니다.

5.  **Create a new resource**를 클릭하여 웹 사이트에 연결될 수 있는 리소스 유형 목록을 표시합니다.

6.  **SQL 데이터베이스**를 클릭하여 **Link Database** 마법사를 표시합니다.

7.  **Link Database** 마법사의 3\~4페이지에 있는 필수 필드를 완성하고 4페이지의 **마침** 확인 표시를 클릭합니다.

Azure는 지정한 매개 변수로 SQL 데이터베이스를 만들고 웹 사이트에 데이터베이스를 연결합니다.

## 방법: MySQL 데이터베이스를 사용하도록 웹 사이트 구성
MySQL 데이터베이스를 사용하도록 웹 사이트를 구성하려면 SQL 데이터베이스를 사용하는 것과 동일한 단계를 따르고 **Link a Resource** 마법사에서 **SQL 데이터베이스** 대신 **MySQL 데이터베이스**를 선택합니다.

또는 **사용자 지정 만들기** 옵션을 사용하여 웹 사이트를 만들 수 있습니다. **데이터베이스** 드롭다운에서 **새 MySQL 데이터베이스 만들기** 또는 **Use an existing MySQL database** 중 하나를 선택합니다.

## 방법: 사용자 지정 도메인 이름 구성
----------------------------------

사용자 지정 도메인 이름을 사용하도록 웹 사이트를 구성하는 방법에 대한 자세한 내용은 [Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성](http://www.windowsazure.com/en-us/documentation/articles/web-sites-custom-domain-name/)을 참조하십시오.

방법: SSL을 사용하도록 웹 사이트 구성
-------------------------------------

Azure에서 사용자 지정 도메인에 대해 SSL을 구성하는 방법에 대한 자세한 내용은 [Azure 웹 사이트에 HTTPS 사용](http://www.windowsazure.com/en-us/documentation/articles/web-sites-configure-ssl-certificate/)을 참조하십시오.

다음 단계
---------

-   [웹 사이트 크기를 조정하는 방법](http://www.windowsazure.com/en-us/documentation/articles/web-sites-scale/)

-   [웹 사이트를 모니터링하는 방법](http://www.windowsazure.com/en-us/documentation/articles/web-sites-monitor/)


