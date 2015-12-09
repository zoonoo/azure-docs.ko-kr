<properties
	pageTitle="하이브리드 연결을 사용하여 Azure 모바일 앱을 온-프레미스 SQL Server에 연결 | Microsoft Azure"
	description="하이브리드 연결을 사용하여 앱 서비스 모바일 앱에서 온-프레미스 SQL Server에 연결하는 방법에 대해 알아봅니다."
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="get-started-article"
	ms.date="10/23/2015"
	ms.author="glenga"/>


# 하이브리드 연결을 사용하여 모바일 앱에서 온-프레미스 SQL Server에 연결

엔터프라이즈를 클라우드로 전환하는 경우 모든 자산을 지금 바로 마이그레이션할 수 없을 수도 있습니다. 하이브리드 연결을 사용하면 Azure 앱 서비스의 모바일 앱 기능이 온-프레미스 자산에 안전하게 연결할 수 있습니다. 이러한 방식으로 Azure를 사용하여 온-프레미스 데이터를 모바일 클라이언트에 액세스할 수 있게 됩니다. 지원되는 자산에는 Microsoft SQL Server, MySQL, HTTP 웹 API, 대부분의 사용자 지정 웹 서비스를 비롯하여 정적 TCP 포트에서 실행되는 모든 리소스가 포함됩니다. 하이브리드 연결은 SAS(공유 액세스 서명) 권한 부여를 사용하여 모바일 서비스 및 온-프레미스 하이브리드 연결 관리자에서 하이브리드 연결로의 연결에 대한 보안을 유지합니다. 자세한 내용은 [하이브리드 연결 개요](../integration-hybrid-connection-overview.md)를 참조하세요.

이 자습서에서는 서비스와 함께 프로비전된 기본 Azure SQL 데이터베이스가 아닌 로컬 온-프레미스 SQL Server 데이터베이스를 사용하도록 모바일 앱 .NET 백 엔드를 수정하는 방법을 알아봅니다.

[AZURE.INCLUDE [app-service-mobile-to-web-and-api](../../includes/app-service-mobile-to-web-and-api.md)]

## 필수 조건 ##

이 자습서를 사용하려면 다음이 필요합니다.

- **기존 모바일 앱 백엔드**는 <br/>[빠른 시작 자습서](app-service-mobile-windows-store-dotnet-get-started.md)를 따라 새 .NET 백 엔드 모바일 앱을 [Azure 포털]에서 만들고 다운로드합니다.

[AZURE.INCLUDE [hybrid-connections-prerequisites](../../includes/hybrid-connections-prerequisites.md)]

## SQL Server Express 설치, TCP/IP 사용 및 SQL Server 데이터베이스 온-프레미스 만들기

[AZURE.INCLUDE [hybrid-connections-create-on-premises-database](../../includes/hybrid-connections-create-on-premises-database.md)]

## 하이브리드 연결 만들기

모바일 앱 백 엔드의 코드 부분에 대해 새 하이브리드 연결 및 BizTalk 서비스를 만들어야 하고 이것은 웹앱입니다.

1. [Azure 포털]에서 모바일 앱으로 이동하고 설정에서 **네트워킹**을 클릭합니다.

	![웹앱으로 이동](./media/app-service-mobile-dotnet-backend-hybrid-connections-get-started/mobile-app-link-to-web-app-backend.png)

	

2. **하이브리드 연결** 섹션에서 **하이브리드 연결 끝점 구성**을 클릭합니다.

	![하이브리드 연결](./media/app-service-mobile-dotnet-backend-hybrid-connections-get-started/start-hybrid-connection.png)

2. 하이브리드 연결 블레이드에서 **추가** 그런 다음 **새 하이브리드 연결**을 클릭합니다.

3. **하이브리드 연결 블레이드 만들기**에서 하이브리드 연결에 대한 **이름** 및 **호스트 이름**을 제공하고 **포트**를 `1433`로 설정합니다.

	![하이브리드 연결 만들기](./media/app-service-mobile-dotnet-backend-hybrid-connections-get-started/create-hybrid-connection.png)

4. **Biz Talk 서비스**를 클릭하고 BizTalk 서비스의 이름을 입력하고 **확인**을 클릭합니다.

	이 자습서에서는 **mobile1**을 사용합니다. 새로운 BizTalk 서비스의 고유한 이름을 지정해야 합니다.

	프로세스가 완료되면 **알림** 영역은 녹색 **성공**으로 깜박이며 **하이브리드 연결** 블레이드는 **연결 되지 않음** 상태와 함께 새 하이브리드 연결을 표시합니다 .

	![1개의 하이브리드 연결 생성됨](./media/app-service-mobile-dotnet-backend-hybrid-connections-get-started/hybrid-connection-created.png)

여기서 클라우드 하이브리드 연결 인프라의 중요한 부분을 완료했습니다. 이제 해당하는 온-프레미스 부분을 만듭니다.

## 온-프레미스 하이브리드 연결 관리자를 설치하여 연결 완료

[AZURE.INCLUDE [app-service-hybrid-connections-manager-install](../../includes/app-service-hybrid-connections-manager-install.md)]

## SQL Server 데이터베이스에 연결하려면 모바일 앱 백 엔드 프로젝트를 구성합니다.

이 단계에서는 온-프레미스 데이터베이스에 대한 연결 문자열을 정의하고 이 연결을 사용하여 모바일 앱 백 엔드를 수정합니다.

1. Visual Studio 2013에서 모바일 앱 백 엔드를 정의하는 프로젝트를 엽니다.

	.NET 백 엔드 프로젝트를 다운로드하는 방법에 대해서는 [빠른 시작 자습서](app-service-mobile-windows-store-dotnet-get-started.md)를 참조하십시오.

2. 솔루션 탐색기에서 Web.config 파일을 열고 **connectionStrings** 섹션을 찾아 다음과 같은 새로운 SqlClient 항목을 추가함으로써 온-프레미스 SQL Server 데이터베이스를 가리킵니다.

	    <add name="OnPremisesDBConnection"
         connectionString="Data Source=OnPremisesServer,1433;
         Initial Catalog=OnPremisesDB;
         User ID=HybridConnectionLogin;
         Password=<**secure_password**>;
         MultipleActiveResultSets=True"
         providerName="System.Data.SqlClient" />

	이 문자열에서 `<**secure_password**>`을 *HybridConnectionLogin* 에 대해 만든 암호로 대체해야 합니다.

3. Visual Studio에서 **저장**을 클릭하여 Web.config 파일을 저장합니다.

	> [AZURE.NOTE]이 연결 설정은 로컬 컴퓨터에서 실행할 때 사용됩니다. Azure에서 실행하는 경우 이 설정은 포털에 정의된 연결 설정으로 재정의됩니다.

4. **Models** 폴더를 확장하여 *Context.cs* 로 끝나는 데이터 모델 파일을 엽니다.

6. **DbContext** 인스턴스 생성자를 변형하여 다음 코드 조각과 비슷한 기본 **DbContext** 생성자에 `OnPremisesDBConnection` 값을 전달합니다.

        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base("OnPremisesDBConnection")
            {
            }
        }

	이제 서비스는 SQL Server 데이터베이스에 새 연결을 사용합니다.

## 온-프레미스 연결 문자열을 사용하도록 Azure 업데이트

그 다음, 새 연결 문자열에 대한 앱 설정을 추가하여 Azure에서 사용합니다.

1. 모바일 앱에 대한 웹앱 백 엔드 코드의 [Azure 포털]로 돌아가서 **모든 설정** 및 **응용 프로그램 설정**을 차례로 클릭합니다.

3. **웹앱 설정** 블레이드에서 **연결 문자열** 아래로 스크롤하고 `Server=OnPremisesServer,1433;Database=OnPremisesDB;User ID=HybridConnectionsLogin;Password=<**secure_password**>`와 같은 값을 가지는 `OnPremisesDBConnection`로 지정한 새 **SQL Server** 연결 문자열을 추가합니다.

	온-프레미스 데이터베이스의 보안 암호로 `<**secure_password**>`을(를) 바꿉니다.

	![온-프레미스 데이터베이스에 대한 연결 문자열](./media/app-service-mobile-dotnet-backend-hybrid-connections-get-started/set-sql-server-database-connection.png)

2. **저장**을 눌러 하이브리드 연결과 방금 만든 연결 문자열을 저장합니다.

## Azure에서 모바일 앱 백엔드 게시 및 테스트

마지막으로 Azure에 모바일 앱 백 엔드를 게시하고 하이브리드 연결을 사용하는지 확인하여 온-프레미스 데이터베이스에 데이터를 저장해야 합니다.

3. Visual Studio에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**, 그런 다음 **웹 게시**를 클릭하며 **Microsoft Azure 웹 사이트**를 클릭합니다.

	Visual Studio를 사용하는 대신 [Git 사용하여 백엔드 게시](mobile-services-dotnet-backend-store-code-source-control.md)할 수 있습니다.

2. Azure 자격 증명으로 로그인하여 **기존 웹 사이트 선택**에서 서비스를 선택합니다.

	Visual Studio는 Azure에서 직접 게시 설정을 다운로드합니다.

3. 마지막으로 **게시**를 클릭합니다.

	게시가 완료된 후 서비스를 다시 시작하 고 백 엔드 시작 페이지가 표시됩니다.

4. 모바일 앱에 연결된 클라이언트 앱을 사용하여 데이터베이스 변경 내용을 생성하는 몇 가지 작업을 호출합니다.

5. SQL Server Management Studio에서 SQL Server 인스턴스에 연결하고 개체 탐색기를 열어 **OnPremisesDB** 데이터베이스 및 **테이블**을 확장합니다.

6. **hybridService1.TodoItems** 테이블을 마우스 오른쪽 단추로 클릭하고 **상위 1000개의 행 선택**을 선택하여 결과를 봅니다.

	하이브리드 연결을 사용하여 클라이언트 앱에서 생성된 변경 내용을 온-프레미스 데이터베이스의 모바일 앱 백 엔드에 저장합니다.

## 참고 항목 ##

+ [하이브리드 연결 웹 사이트](../../services/biztalk-services/)
+ [하이브리드 연결 개요](../integration-hybrid-connection-overview.md)
+ [BizTalk 서비스: 대시보드, 모니터, 확장, 구성 및 하이브리드 연결 탭](../biztalk-dashboard-monitor-scale-tabs.md)
+ [.NET 백 엔드 모바일 서비스에 대한 데이터 모델 변경 방법](../mobile-services-dotnet-backend-how-to-use-code-first-migrations.md)

<!-- IMAGES -->


<!-- Links -->
[Azure 포털]: https://portal.azure.com/
[Azure classic portal]: http://go.microsoft.com/fwlink/p/?linkid=213885
[Get started with Mobile Services]: ../mobile-services-windows-store-dotnet-get-started.md

<!---HONumber=AcomDC_1203_2015-->