<properties 
	pageTitle="하이브리드 연결을 사용하여 Azure 모바일 서비스에서 온-프레미스 SQL Server에 연결 - Azure 모바일 서비스" 
	description="하이브리드 연결을 사용하여 Azure 모바일 서비스에서 온-프레미스 SQL Server에 연결하는 방법에 대해 알아봅니다." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/16/2015" 
	ms.author="glenga"/>

  
# 하이브리드 연결을 사용하여 Azure 모바일 서비스에서 온-프레미스 SQL Server에 연결 

엔터프라이즈를 클라우드로 전환하는 경우 모든 자산을 지금 바로 마이그레이션할 수 없을 수도 있습니다. 하이브리드 연결로 Azure 모바일 서비스를 온-프레미스 자산에 안전하게 연결할 수 있습니다. 이러한 방식으로 Azure를 사용하여 온-프레미스 데이터를 모바일 클라이언트에 액세스할 수 있게 됩니다. 지원되는 자산에는 Microsoft SQL Server, MySQL, HTTP 웹 API, 대부분의 사용자 지정 웹 서비스를 비롯하여 정적 TCP 포트에서 실행되는 모든 리소스가 포함됩니다. 하이브리드 연결은 SAS(공유 액세스 서명) 권한 부여를 사용하여 모바일 서비스 및 온-프레미스 하이브리드 연결 관리자에서 하이브리드 연결로의 연결에 대한 보안을 유지합니다. 자세한 내용은 [하이브리드 연결 개요](../integration-hybrid-connection-overview.md)를 참조하세요.

이 자습서에서는 서비스와 함께 프로비전된 기본 Azure SQL 데이터베이스가 아닌 로컬 온-프레미스 SQL Server 데이터베이스를 사용하도록 .NET 백 엔드 모바일 서비스를 수정하는 방법을 알아봅니다. [이 문서](http://blogs.msdn.com/b/azuremobile/archive/2014/05/12/connecting-to-an-external-database-with-node-js-backend-in-azure-mobile-services.aspx)에 설명된 대로 JavaScript 백 엔드 모바일 서비스에서도 하이브리드 연결이 지원됩니다.

##필수 조건##

이 자습서를 사용하려면 다음이 필요합니다.

- **기존 .NET 백 엔드 모바일 서비스** <br/>자습서 [모바일 서비스 시작]에 따라 [Azure 관리 포털]에서 새 .NET 백 엔드 모바일 서비스를 만들고 다운로드합니다.

[AZURE.INCLUDE [hybrid-connections-prerequisites](../../includes/hybrid-connections-prerequisites.md)]

## SQL Server Express 설치, TCP/IP 사용 및 SQL Server 데이터베이스 온-프레미스 만들기

[AZURE.INCLUDE [hybrid-connections-create-on-premises-database](../../includes/hybrid-connections-create-on-premises-database.md)]

## 하이브리드 연결 만들기

[AZURE.INCLUDE [hybrid-connections-create-new](../../includes/hybrid-connections-create-new.md)]

## 온-프레미스 하이브리드 연결 관리자를 설치하여 연결 완료

[AZURE.INCLUDE [hybrid-connections-install-connection-manager](../../includes/hybrid-connections-install-connection-manager.md)]

## SQL Server 데이터베이스에 연결하도록 모바일 서비스 프로젝트를 구성합니다.

이 단계에서는 온-프레미스 데이터베이스에 대한 연결 문자열을 정의하고 이 연결을 사용하여 모바일 서비스를 수정합니다.

1. Visual Studio 2013에서 .NET 백 엔드 모바일 서비스를 정의하는 프로젝트를 엽니다. 

	.NET 백 엔드 프로젝트를 다운로드하는 방법에 대해서는 [모바일 서비스 시작하기](mobile-services-dotnet-backend-windows-store-dotnet-get-started.md)를 참조하세요.

2. 솔루션 탐색기에서 Web.config 파일을 열고 **connectionStrings** 섹션을 찾아 다음과 같은 새로운 SqlClient 항목을 추가함으로써 온-프레미스 SQL Server 데이터베이스를 가리킵니다.
	
	    <add name="OnPremisesDBConnection" 
         connectionString="Data Source=OnPremisesServer,1433;
         Initial Catalog=OnPremisesDB;
         User ID=HybridConnectionLogin;
         Password=<**secure_password**>;
         MultipleActiveResultSets=True"
         providerName="System.Data.SqlClient" />

	이 문자열에서 `<**secure_password**>`를 *HbyridConnectionLogin*에 대해 만든 암호로 바꿔야 합니다.
	
3. Visual Studio에서 **저장**을 클릭하여 Web.config 파일을 저장합니다.

	> [AZURE.NOTE]이 연결 설정은 로컬 컴퓨터에서 실행할 때 사용됩니다. Azure에서 실행하는 경우 이 설정은 포털에 정의된 연결 설정으로 재정의됩니다.

4. **Models** 폴더를 확장하여 *Context.cs*로 끝나는 데이터 모델 파일을 엽니다.

6. **DbContext** 인스턴스 생성자를 변형하여 다음 코드 조각과 비슷한 기본 **DbContext** 생성자에 `OnPremisesDBConnection` 값을 전달합니다.

        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base("OnPremisesDBConnection")
            {
            }
        }

	이제 서비스는 SQL Server 데이터베이스에 새 연결을 사용합니다.
 
##데이터베이스 연결을 로컬로 테스트

Azure에 게시하고 하이브리드 연결을 사용하기 전에 로컬로 실행할 때 데이터베이스 연결이 작동하는지 확인하는 것이 좋습니다. 이렇게 하면 하이브리드 연결 사용을 게시하고 시작하기 전에 연결 문제를 보다 쉽게 진단하고 수정할 수 있습니다.

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]

## 온-프레미스 연결 문자열을 사용하도록 Azure 업데이트

이제 데이터베이스 연결을 확인했으므로 Azure에서 사용할 수 있도록 새 연결 문자열에 대한 앱 설정을 추가하고 모바일 서비스를 Azure에 게시해야 합니다.

1. [Azure 관리 포털]에서 모바일 서비스로 이동합니다.
  
1. **구성** 탭을 클릭하고 **연결 문자열** 섹션을 찾습니다.

	![온-프레미스 데이터베이스에 대한 연결 문자열](./media/mobile-services-dotnet-backend-hybrid-connections-get-started/11.png)

2. `OnPremisesDBConnection`이라는 새 연결 **SQL Server** 문자열을 다음과 같은 값과 함께 추가합니다.

		Server=OnPremisesServer,1433;Database=OnPremisesDB;User ID=HybridConnectionsLogin;Password=<**secure_password**>


	`<**secure_password**>`를 *HybridConnectionLogin*의 보안 암호로 바꿉니다.

2. **저장**을 눌러 하이브리드 연결과 방금 만든 연결 문자열을 저장합니다.

3. Visual Studio를 사용하여 업데이트된 모바일 서비스 프로젝트를 Azure에 게시합니다.

	서비스 시작 페이지가 표시됩니다.

4. 시작 페이지에서 이전처럼 **지금 시도** 단추를 사용하거나 모바일 서비스에 연결된 클라이언트 앱을 사용하여 데이터베이스 변경 내용을 생성하는 일부 작업을 호출합니다.

	>[AZURE.NOTE]API 도움말 페이지를 시작하기 위해 **지금 시도** 단추를 사용하는 경우 사용자의 응용 프로그램 키를 암호(빈 사용자 이름)로 제공해야 합니다.

4. SQL Server Management Studio에서 SQL Server 인스턴스에 연결하고 개체 탐색기를 열어 **OnPremisesDB** 데이터베이스 및 **테이블**을 확장합니다.

5. **hybridService1.TodoItems** 테이블을 마우스 오른쪽 단추로 클릭하고 **상위 1000개의 행 선택**을 선택하여 결과를 봅니다.

	앱에서 생성된 변경 내용은 모바일 서비스에서 하이브리드 연결을 사용하여 온-프레미스 데이터베이스에 저장되었습니다.

##참고 항목##
 
+ [하이브리드 연결 웹 사이트](../../services/biztalk-services/)
+ [하이브리드 연결 개요](../integration-hybrid-connection-overview.md)
+ [BizTalk 서비스: 대시보드, 모니터, 확장, 구성 및 하이브리드 연결 탭](../biztalk-dashboard-monitor-scale-tabs.md)
+ [.NET 백 엔드 모바일 서비스에 대한 데이터 모델 변경 방법](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md)

<!-- IMAGES -->

<!-- Links -->
[Azure 관리 포털]: http://manage.windowsazure.com
[모바일 서비스 시작]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md

<!---HONumber=July15_HO4-->