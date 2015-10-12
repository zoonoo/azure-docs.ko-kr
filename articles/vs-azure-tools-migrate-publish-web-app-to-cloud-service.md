<properties
   pageTitle="Visual Studio | Microsoft Azure에서 Azure 클라우드 서비스로 웹 응용 프로그램을 마이그레이션 및 게시하는 방법"
   description="Visual Studio를 사용하여 Azure 클라우드 서비스로 웹 응용 프로그램을 마이그레이션 및 게시하는 방법에 대해 알아보세요."
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tglee" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="09/30/2015"
   ms.author="kempb" />

# 방법: Visual Studio에서 Azure 클라우드 서비스로 웹 응용 프로그램 마이그레이션 및 게시

Azure의 호스팅 서비스와 확장성을 활용하기 위해 웹 응용 프로그램을 Azure 클라우드 서비스로 마이그레이션 및 게시해야 하는 경우가 있습니다. 기존 응용 프로그램을 최소한 변경하고 Azure에서 웹 응용 프로그램을 실행할 수 있습니다.

>[AZURE.NOTE]이 토픽은 웹 사이트가 아닌 클라우드 서비스에 배포에 대한 것입니다. 웹 사이트에 배포에 대한 자세한 내용은 [Azure 앱 서비스에서 웹앱 배포](web-sites-deploy.md)를 참조하세요.

Visual C# 및 Visual Basic에 지원되는 특정 템플릿 목록을 보려면 이 항목의 뒷부분에 나오는 **지원되는 프로젝트 템플릿**을 참조하세요.

우선 Visual Studio에서 Azure용 웹 응용 프로그램을 사용하도록 설정해야 합니다. 다음 그림은 배포에 사용할 Azure 프로젝트를 추가하여 기존 웹 응용 프로그램을 게시하는 주요 단계를 보여 줍니다. 이 프로세스는 필요한 웹 역할이 포함된 Azure 프로젝트를 솔루션에 추가합니다. 현재 웹 프로젝트 유형을 기준으로, 서비스 패키지에 배포를 위한 추가 어셈블리가 필요할 경우 어셈블리의 프로젝트 속성도 업데이트됩니다.

![Microsoft Azure에 웹 응용 프로그램 게시](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC748917.png)

>[AZURE.NOTE]솔루션의 웹 프로젝트에 대해서만 **변환** > **Azure 클라우드 서비스 프로젝트로 변환** 명령이 표시됩니다. 예를 들어 솔루션에서 Silverlight 프로젝트에 대해서는 명령을 사용할 수 없습니다. 서비스 패키지를 만들거나 응용 프로그램을 Azure에 게시할 경우 경고 또는 오류가 발생할 수 있습니다. 이러한 경고와 오류를 확인하여 Azure에 배포하기 전에 문제를 해결할 수 있습니다. 예를 들어 어셈블리 누락에 대한 경고가 표시될 수 있습니다. 경고를 오류로 처리하는 방법은 [Visual Studio에서 Azure 클라우드 서비스 프로젝트 구성](vs-azure-tools-configuring-an-azure-project.md)을 참조하세요. 응용 프로그램을 빌드하고 계산 에뮬레이터를 사용하여 로컬로 실행하거나 Azure에 게시할 경우 **오류 목록** 창에 **지정한 경로 및/또는 파일 이름이 너무 깁니다.** 오류가 표시될 수 있습니다. 이 오류는 정규화된 Azure 프로젝트 이름의 길이가 너무 길기 때문에 발생합니다. 전체 경로를 포함한 프로젝트 이름의 길이는 146자를 초과할 수 없습니다. 예를 들어 `c:\users<user name>\documents\visual studio 2015\Projects\SilverlightApplication4\SilverlightApplication4.Web.Azure.ccproj`는 Silverlight 응용 프로그램용으로 만든 Azure 프로젝트의 파일 경로가 포함된 전체 프로젝트 이름입니다. 정규화된 프로젝트 이름의 길이를 줄이기 위해 솔루션을 경로가 짧은 다른 디렉토리로 이동해야 할 수 있습니다.

Visual Studio에서 Azure에 웹 응용 프로그램을 마이그레이션 및 게시하려면 다음 단계를 따릅니다.

## Azure 배포용 웹 응용 프로그램 사용 설정

### Azure 배포용 웹 응용 프로그램을 사용하도록 설정하려면

1. Azure에 배포하기 위해 웹 응용 프로그램을 사용하도록 설정하려면 솔루션에서 웹 프로젝트에 대한 바로 가기 메뉴를 열고 Azure 배포 프로젝트 추가를 선택합니다.

    다음과 같은 동작이 실행됩니다.

    - `<name of the web project>.Azure`라는 Azure 프로젝트가 응용 프로그램용 솔루션에 추가됩니다.

    - 웹 프로젝트의 웹 역할이 이 Azure 프로젝트에 추가됩니다.

    - **로컬 복사** 속성이 MVC 2, MVC 3, MVC 4, Silverlight 비즈니스 응용 프로그램에 필요한 어셈블리에 대해 true로 설정됩니다. 그러면 이러한 어셈블리가 배포에 사용되는 서비스 패키지에 추가됩니다.

  >[AZURE.IMPORTANT]이 웹 응용 프로그램에 필요한 다른 어셈블리 또는 파일이 있는 경우 이러한 파일에 대한 속성을 수동으로 설정해야 합니다. 이러한 속성에 대한 자세한 내용은 이 문서의 뒷부분에서 **서비스 패키지에 파일 포함** 섹션을 참조하세요.

  >[AZURE.NOTE]솔루션의 Azure 프로젝트에 특정 웹 프로젝트의 웹 역할이 이미 있는 경우 이 웹 프로젝트의 바로 가기 메뉴에 **변환** > **Azure 클라우드 서비스 프로젝트로 변환**이 표시되지 않습니다.

  웹 응용 프로그램에 여러 웹 프로젝트가 있고 각 웹 프로젝트에 대해 웹 역할을 만들려는 경우 각 웹 프로젝트에 대해 이 절차의 단계를 수행해야 합니다. 그러면 각 웹 역할에 대해 별도의 Azure 프로젝트가 생성됩니다. 각 웹 프로젝트는 별도로 게시됩니다. 또는 웹 응용 프로그램에서 기존 Azure 프로젝트에 다른 웹 역할을 수동으로 추가할 수 있습니다. 이렇게 하려면 Azure 프로젝트의 **역할** 폴더에 대한 바로 가기 메뉴를 열고 **추가**를 선택한 다음 **솔루션의 웹 역할 프로젝트**를 선택합니다. 그런 다음 웹 역할로 추가할 프로젝트를 선택하고 **확인** 단추를 선택합니다.

## 응용 프로그램에 Azure SQL 데이터베이스 사용

온-프레미스 SQL Server 데이터베이스를 사용하는 웹 응용 프로그램에 대한 연결 문자열이 있을 경우 Azure에서 호스팅하는 SQL 데이터베이스 인스턴스를 사용하도록 이 연결 문자열을 변경해야 합니다.

>[AZURE.IMPORTANT]구독에서 사용자가 SQL 데이터베이스를 사용할 수 있도록 설정해야 합니다. Azure 관리 포털에서 구독에 액세스할 경우 구독에서 어떤 서비스를 제공하는지 확인할 수 있습니다. 다음 지침은 릴리스된 관리 포털에 적용됩니다. 미리 보기 관리 포털을 사용하는 경우 다음 절차로 건너 뜁니다.|

### 연결 문자열에 대한 웹 역할로 SQL 데이터베이스 인스턴스를 사용하려면

1. Azure 관리 포털에서 SQL 데이터베이스 인스턴스를 만들려면 [SQL 데이터베이스 서버 만들기](http://go.microsoft.com/fwlink/?LinkId=225109) 문서에 나오는 단계를 따르세요.

    >[AZURE.NOTE]SQL 데이터베이스 인스턴스에 대해 방화벽 규칙을 설정하는 경우 **Windows Azure 서비스가 서버에 액세스할 수 있도록 허용합니다.** 확인란을 선택해야 합니다.

1. 연결 문자열에 사용할 SQL 데이터베이스 인스턴스를 만들려면 [SQL 데이터베이스 만들기](http://go.microsoft.com/fwlink/?LinkId=225110) 문서에 나오는 단계를 따르세요.

1. 연결 문자열에 사용할 ADO.NET 연결 문자열을 복사하려면 Azure 관리 포털에서 다음 단계를 수행합니다.

  1. **데이터베이스** 단추를 선택한 다음 SQL 데이터베이스 인스턴스를 만드는 데 사용하는 구독의 노드를 엽니다.

  1. SQL 인스턴스의 가용 인스턴스를 표시하려면 **SQL 데이터베이스** 노드를 선택합니다.

  1. 데이터베이스 속성을 표시하려면 데이터베이스를 선택합니다. **속성** 보기가 나타납니다.

      >[AZURE.NOTE]**속성** 보기가 나타나지 않을 경우 구분선을 사용하여 열어야 할 수 있습니다.

  1. 연결 문자열을 표시하려면 보기 옆에 있는 줄임표(...) 단추를 선택합니다.

    **연결 문자열** 대화 상자가 나타납니다.

  1. ADO.NET 연결 문자열을 복사하려면 텍스트를 강조 표시하고 Ctrl+C 키를 선택합니다.

  1. 대화 상자를 닫으려면 **닫기** 단추를 선택합니다.

1. web.config 파일의 연결 문자열을 바꾸어 이 SQL 데이터베이스 인스턴스를 사용하도록 하려면 web.config 파일을 열고 기존 문자열 항목을 강조 표시한 다음 Ctrl+V 키를 선택합니다. SQL 데이터베이스 인스턴스의 ADO.NET 연결 문자열이 기존 연결 문자열을 대체합니다.

1. 또한 연결 문자열에 매개 변수 `MultipleActiveResultSets=True`을(를) 추가해야 합니다. 연결 문자열은 다음과 같은 형식이어야 합니다.

    ```
    connectionString=”Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"
    ```

1. (선택 사항) web.config에서 연결 문자열을 직접 변경하는 다른 방법은 서비스 패키지를 만드는 데 사용하는 빌드 구성에 따라 web.config 변환 파일 중 하나에 섹션을 추가하는 것입니다. Web.Debug.Config 파일 또는 Web.Release.Config 파일을 엽니다. 이 파일에 다음 섹션을 추가합니다.

    ```
    XMLCopy<connectionStrings><addname="DefaultConnection"connectionString="Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"xdt:Transform="SetAttributes"xdt:Locator="Match(name)"/></connectionStrings>
    ```

1. 수정한 파일을 저장하고 응용 프로그램을 다시 게시합니다.

### Azure 관리 포털을 사용하여 SQL 데이터베이스 인스턴스를 사용하려면

1. [Azure 관리 포털](http://go.microsoft.com/fwlink/?LinkID=213885)에서 SQL 데이터베이스 노드를 선택합니다.

  - 사용하려는 SQL 데이터베이스 인스턴스가 표시되면 선택하여 엽니다.

  - 인스턴스를 만들지 않은 경우 적절한 링크를 선택한 다음 인스턴스를 만듭니다.

1. 데이터베이스 인스턴스를 열거나 만든 다음 **연결 문자열** 링크를 선택합니다.

1. 페이지 아래쪽에서 방화벽 설정을 구성하기 위한 링크를 선택하고 기본 값을 수락하거나 필요한 값을 구성합니다.

1. ADO.NET 연결 문자열을 복사해서 web.config 파일의 온-프레미스 데이터베이스에 대한 기존 연결 문자열을 붙여 넣은 다음 `MultipleActiveResultSets=True`을(를) 추가합니다.

## Azure에 웹 응용 프로그램 게시

### Azure에 웹 응용 프로그램을 게시하려면

1. Azure 계산 에뮬레이터를 사용하여 로컬 개발 환경에서 응용 프로그램을 테스트하려면 웹 역할에 대한 Azure 프로젝트의 바로 가기 메뉴를 열고 **시작 프로젝트로 설정**을 선택합니다. 그런 다음 **디버그** > **디버깅 시작**(키보드: **F5**)을 선택합니다.

    **Azure 디버깅 환경 시작** 대화 상자가 열리고 응용 프로그램이 브라우저에서 시작됩니다. 계산 에뮬레이터에서 각 유형의 웹 응용 프로그램을 시작하는 방법은 이 섹션의 표를 참조하세요.

1. Azure에 게시할 응용 프로그램의 서비스를 설정하려면 Microsoft 계정과 Azure 구독이 있어야 합니다. [Visual Studio에서 Azure 응용 프로그램 게시 또는 배포 준비](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md) 항목에 나와 있는 단계를 따라 서비스를 설정합니다.

1. Azure에 웹 응용 프로그램을 게시하려면 웹 프로젝트의 바로 가기 메뉴를 열고 **Azure에 게시**를 선택합니다.

    **Azure 응용 프로그램 게시** 대화 상자가 열리고 Visual Studio에서 배포 프로세스를 시작합니다. 응용 프로그램을 게시하는 방법은 [Azure Tools를 사용하여 클라우드 서비스 게시](vs-azure-tools-publishing-a-cloud-service.md)에서 **Visual Studio에서 Azure 응용 프로그램 게시** 섹션을 참조하세요.

    >[AZURE.NOTE]Azure 프로젝트에서 웹 응용 프로그램을 게시할 수도 있습니다. 이렇게 하려면 Azure 프로젝트의 바로 가기 메뉴를 열고 **게시**를 선택합니다.

1. 배포 진행률을 보려면 **Azure 활동 로그** 창을 확인합니다. 배포 프로세스가 시작되면 이 로그가 자동으로 표시됩니다. 아래 그림과 같이 활동 로그의 개별 항목을 확장하여 자세한 정보를 표시할 수 있습니다.

    ![VST\_AzureActivityLog](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744149.png)

1. (선택 사항) 배포 프로세스를 취소하려면 활동 로그에서 개별 항목의 바로 가기 메뉴를 열고 **취소한 후 제거**를 선택합니다. 그러면 배포 프로세스가 중단되고 Azure에서 배포 환경이 삭제됩니다.

    >[AZURE.NOTE]이 배포 환경을 배포한 후 제거하려면 Azure 관리 포털을 사용해야 합니다.

1. (선택 사항) 역할 인스턴스가 시작되면 Visual Studio에서 **클라우드 탐색기** 또는 **서버 탐색기**의 **Azure 계산** 노드에 자동으로 배포 환경을 표시합니다. 여기에서 개별 역할 인스턴스의 상태를 볼 수 있습니다.

    다음 그림은 역할 인스턴스가 아직 초기화 중인 동안 **서버 탐색기**의 역할 인스턴스를 보여 줍니다.

    ![VST\_DeployComputeNode](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744134.png)

1. 배포 후 응용 프로그램에 액세스하려면 **Azure 활동 로그**에 **완료됨** 상태가 나타날 때 배포 옆의 화살표를 선택합니다. 그러면 Azure에 웹 응용 프로그램의 URL이 표시됩니다. Azure에서 특정 유형의 웹 응용 프로그램을 시작하는 방법은 다음 표를 참조하세요.

    다음 표에는 Azure에서 특정 웹 응용 프로그램을 시작하거나 Azure 계산 에뮬레이터를 사용하여 웹 응용 프로그램을 로컬로 실행 또는 디버깅하는 방법을 자세히 나열되어 있습니다.

    |웹 응용 프로그램 유형|계산 에뮬레이터를 사용하여 로컬로 실행/디버깅|Azure에서 실행|
    |---|---|---|
    |ASP.NET 웹 응용 프로그램|메뉴 모음에서 **디버깅** > **디버깅 시작**을 선택합니다(키보드: **F5** 키 선택).|**Azure 활동 로그**의 **배포** 탭에 표시된 URL 하이퍼링크를 선택하여 브라우저에서 시작 페이지를 로드합니다.|
    |ASP.NET MVC 2 웹 응용 프로그램|메뉴 모음에서 **디버깅** > **디버깅 시작**을 선택합니다(키보드: **F5** 키 선택).|**Azure 활동 로그**의 **배포** 탭에 표시된 URL 하이퍼링크를 선택하여 브라우저에서 시작 페이지를 로드합니다.|
    |ASP.NET MVC 3 웹 응용 프로그램|메뉴 모음에서 **디버깅** > **디버깅 시작**을 선택합니다(키보드: **F5** 키 선택).|**Azure 활동 로그**의 **배포** 탭에 표시된 URL 하이퍼링크를 선택하여 브라우저에서 시작 페이지를 로드합니다.|
    |ASP.NET MVC 4 웹 응용 프로그램|메뉴 모음에서 **디버깅** > **디버깅 시작**을 선택합니다(키보드: **F5** 키 선택).|**Azure 활동 로그**의 **배포** 탭에 표시된 URL 하이퍼링크를 선택하여 브라우저에서 시작 페이지를 로드합니다.|
    |ASP.NET 빈 웹 응용 프로그램|웹 프로젝트의 시작 페이지로 설정하는 응용 프로그램에 .aspx 페이지를 추가해야 합니다. 그런 다음 메뉴 모음에서 **디버깅** > **디버깅 시작**을 선택합니다(키보드: **F5** 키 선택).|응용 프로그램에 기본 .aspx 페이지가 있는 경우 **Azure 활동 로그**의 **배포** 탭에 표시된 URL 하이퍼링크를 선택합니다. 그러면 이 페이지가 브라우저에서 로드됩니다. 다른 .aspx 페이지가 있는 경우 URL에 `<url for deployment>/<name of page>.aspx` 형식을 사용하여 이 특정 페이지를 탐색해야 합니다.|
    |Silverlight 응용 프로그램|메뉴 모음에서 **디버깅** > **디버깅 시작**을 선택합니다(키보드: **F5** 키 선택).|URL에 다음 형식을 사용하여 응용 프로그램의 특정 페이지를 탐색해야 합니다. `<url for deployment>/<name of page>.aspx`|
    |Silverlight 비즈니스 응용 프로그램|메뉴 모음에서 **디버깅** > **디버깅 시작**을 선택합니다(키보드: **F5** 키 선택).|URL에 다음 형식을 사용하여 응용 프로그램의 특정 페이지를 탐색해야 합니다. `<url for deployment>/<name of page>.aspx`|
    |Silverlight 탐색 응용 프로그램|메뉴 모음에서 **디버깅** > **디버깅 시작**을 선택합니다(키보드: **F5** 키 선택).|URL에 다음 형식을 사용하여 응용 프로그램의 특정 페이지를 탐색해야 합니다. `<url for deployment>/<name of page>.aspx`|
    |WCF 서비스 응용 프로그램|.svc 파일을 WCF 서비스 프로젝트의 시작 페이지로 설정해야 합니다. 그런 다음 메뉴 모음에서 **디버깅** > **디버깅 시작**을 선택합니다(키보드: **F5** 키 선택).|URL에 `<url for deployment>/<name of service file>.svc` 형식을 사용하여 응용 프로그램의 svc 파일을 탐색해야 합니다.|
    |WCF 워크플로 서비스 응용 프로그램|.svc 파일을 WCF 서비스 프로젝트의 시작 페이지로 설정해야 합니다. 그런 다음 메뉴 모음에서 **디버깅** > **디버깅 시작**을 선택합니다(키보드: **F5** 키 선택).|URL에 `<url for deployment>/<name of service file>.svc` 형식을 사용하여 응용 프로그램의 svc 파일을 탐색해야 합니다.|
    |ASP.NET 동적 엔터티|메뉴 모음에서 **디버깅** > **디버깅 시작**을 선택합니다(키보드: **F5** 키 선택).|연결 문자열을 업데이트해야 합니다(다음 섹션 참조). 또한 URL에 다음 형식을 사용하여 응용 프로그램의 특정 페이지를 탐색해야 합니다. `<url for deployment>/<name of page>.aspx`|
    |SQL에 대한 ASP.NET Dynamic Data Linq|메뉴 모음에서 **디버깅** > **디버깅 시작**을 선택합니다(키보드: **F5** 키 선택).|응용 프로그램에 SQL Azure 데이터베이스 사용(이 항목의 이전 섹션 참조) 절차에 나온 단계를 따라야 합니다. 또한 URL에 다음 형식을 사용하여 응용 프로그램의 특정 페이지를 탐색해야 합니다. `<url for deployment>/<name of page>.aspx`|

## ASP.NET 동적 엔터티의 연결 문자열 업데이트

### ASP.NET 동적 엔터티의 연결 문자열을 업데이트하려면

1. ASP.NET 동적 엔터티 웹 응용 프로그램에 사용할 수 있는 SQL Azure 데이터베이스를 만들려면 이 항목의 앞부분에 나오는 **응용 프로그램에 SQL Azure 데이터베이스 사용** 절차의 단계를 따르세요.

1. Azure 관리 포털에서 이 데이터베이스에 필요한 테이블과 필드를 추가합니다.

1. 이 응용 프로그램 유형의 연결 문자열은 web.config 파일에 다음과 같은 형식이 있습니다.

    ```
    <addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=";data source=<server name>\SQLEXPRESS;initial catalog=<database name>;integrated security=True;multipleactiveresultsets=True;App=EntityFramework";"providerName="System.Data.EntityClient"/>
    ```

    다음과 같이 SQL Azure 데이터베이스의 ADO.NET 연결 문자열을 사용하여 *connectionString* 값을 업데이트합니다.

    ```
    XMLCopy<addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=";Server=tcp:<SQL Azure server name>.database.windows.net,1433;Database=<database name>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;multipleactiveresultsets=True;App=EntityFramework";"providerName="System.Data.EntityClient"/>
    ```

1. 연결 문자열에서 변경한 내용으로 web.config 파일을 저장하려면 메뉴 모음에서 **파일** > **web.config 저장**을 선택합니다.

## 지원되는 프로젝트 템플릿

Azure에 웹 응용 프로그램을 게시하려면 응용 프로그램이 아래 표에 나열된 C# 또는 Visual Basic에 대한 프로젝트 템플릿 중 하나를 사용해야 합니다.

|프로젝트 템플릿 그룹|프로젝트 템플릿|
|---|---|
|웹|ASP.NET 웹 응용 프로그램|
|웹|ASP.NET MVC 2 웹 응용 프로그램|
|웹|ASP.NET MVC 3 웹 응용 프로그램|
|웹|ASP.NET MVC4 웹 응용 프로그램|
|웹|ASP.NET 빈 웹 응용 프로그램|
|웹|ASP.NET MVC 2 빈 웹 응용 프로그램|
|웹|ASP.NET 동적 데이터 엔터티 웹 응용 프로그램|
|웹|SQL 웹 응용 프로그램에 대한 ASP.NET Dynamic Data Linq|
|Silverlight|Silverlight 응용 프로그램|
|Silverlight|Silverlight 비즈니스 응용 프로그램|
|Silverlight|Silverlight 탐색 응용 프로그램|
|WCF|WCF 서비스 응용 프로그램|
|WCF|WCF 워크플로 서비스 응용 프로그램|
|워크플로|WCF 워크플로 서비스 응용 프로그램|

## 다음 단계
게시에 대한 자세한 내용은 [Visual Studio에서 Azure 응용 프로그램 게시 또는 배포 준비](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md)를 참조하세요. 또한 [명명된 인증 자격 증명 설정](vs-azure-tools-setting-up-named-authentication-credentials.md)도 참조하세요.

<!---HONumber=Oct15_HO1-->