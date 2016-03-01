<properties
	pageTitle=".NET에서 첫 Azure 검색 응용 프로그램 시작 | Microsoft Azure | 호스트된 클라우드 검색 서비스"
	description="Azure 검색 .NET SDK의 .NET 클라이언트 라이브러리를 사용하여 Visual Studio 솔루션을 빌드하는 방법을 보여 주는 자습서입니다."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor="v-lincan"/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.date="02/09/2016"
	ms.author="heidist"/>

# .NET에서 첫 Azure 검색 응용 프로그램 시작
> [AZURE.SELECTOR]
- [.NET](search-get-started-dotnet.md)
- [Portal](search-get-started-portal.md)
 
Visual Studio 2013 이상에서 검색 환경에 Azure 검색을 사용하는 사용자 지정 .NET 검색 응용 프로그램을 빌드하는 방법에 대해 알아봅니다. 이 자습서에서는 [Azure 검색 .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)와 Azure 검색 서비스 REST API를 사용합니다.

이 샘플을 실행하려면 Azure 검색 서비스가 필요합니다. 이 서비스는 [Azure 포털](https://portal.azure.com)에서 등록할 수 있습니다. 단계별 지침은 [포털에서 Azure 검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

## 데이터 정보

이 샘플 응용 프로그램에서는 데이터 집합 크기를 줄이기 위해 Rhode Island 주에 대해 필터링된 [USGS(United States Geological Survey)](http://geonames.usgs.gov/domestic/download_data.htm)의 데이터를 사용합니다. 이 데이터를 사용하여 병원 및 학교와 같은 랜드마크 빌딩뿐만 아니라 강, 호수, 산 등의 지질학적 특징을 반환하는 검색 응용 프로그램을 빌드합니다.

이 응용 프로그램에서 **DataIndexer** 프로그램은 [Indexer](https://msdn.microsoft.com/library/azure/dn798918.aspx)를 사용하여 인덱스를 빌드 및 로드하며, 이를 통해 Azure SQL 데이터베이스에서 필터링된 USGS 데이터 집합을 검색합니다. 온라인 데이터 원본에 대한 자격 증명 및 연결 정보는 소스 코드에서 제공됩니다. 추가 구성은 필요하지 않습니다.

> [AZURE.NOTE] 무료 가격 책정 계층의 문서 제한(10,000개) 미만으로 유지하기 위해 이 데이터 집합에 필터를 적용했습니다. 다른 가격 책정 계층을 사용하는 경우 이 제한은 적용되지 않습니다. 각 가격 책정 계층의 용량에 대한 자세한 내용은 [제한 및 제약 조건](search-limits-quotas-capacity.md)을 참조하세요.

<a id="sub-2"></a>
## Azure 검색 서비스의 서비스 이름 및 api-key 찾기 ##

서비스를 만든 후 포털로 돌아가서 URL 또는 `api-key`를 가져옵니다. 검색 서비스에 연결하려면 URL과 호출을 인증할 `api-key`가 둘 다 있어야 합니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 점프 모음에서 **검색 서비스**를 클릭하여 구독에 프로비전된 Azure 검색 서비스를 모두 나열합니다.
3. 사용하려는 서비스를 선택합니다.
4. 서비스 대시보드에 기본 정보에 대한 타일 및 관리 키에 액세스할 수 있는 키 아이콘이 표시됩니다.

   ![][3]

3. 서비스 URL 및 관리 키를 복사합니다. 이는 나중에 Visual Studio 프로젝트에서 app.config 및 web.config 파일에 추가할 때 필요합니다.

## 새 프로젝트 및 솔루션 시작

이 솔루션에는 다음과 같은 두 가지 프로젝트가 포함되어 있습니다.

- **DataIndexer** - 데이터를 로드하는 데 사용되는 Visual C# 콘솔 응용 프로그램
- **SimpleSearchMVCApp** - 쿼리하고 검색 결과를 반환하는 데 사용되는 Visual C# ASP.NET MVC 웹 응용 프로그램

이 단계에서는 두 프로젝트를 모두 만듭니다.

1. **Visual Studio** > **새 프로젝트** > **Visual C#** > **콘솔 응용 프로그램**을 시작합니다.
2. 프로젝트 이름을 **DataIndexer**로 지정하고 솔루션 이름을 **AzureSearchDotNetDemo**로 지정합니다.
3. 솔루션 탐색기의 솔루션에서 **추가** > **새 프로젝트** > **Visual C#** > **ASP.NET 웹 응용 프로그램**을 마우스 오른쪽 단추로 클릭합니다.
4. 프로젝트 이름을 **SimpleSearchMVCApp**으로 지정합니다.
5. 새 ASP.NET 프로젝트에서 MVC 템플릿을 선택하고, 이 자습서에서 사용하지 않으려는 프로그램 아티팩트가 만들어 지지 않도록 옵션의 선택을 취소합니다.

   Azure 호스팅 및 단위 테스트에 대한 확인란의 선택을 취소하고 인증을 없음으로 설정합니다.

   ![][10]

프로젝트 만들기를 마치면 솔루션이 아래 예제와 유사하게 표시됩니다.

   ![][4]

## .NET 클라이언트 라이브러리 설치 및 다른 패키지 업데이트

1. 솔루션 탐색기의 솔루션에서 **NuGet 패키지 관리**를 마우스 오른쪽 단추로 클릭합니다.
2. **업데이트** > **안정화 버전** > **모두 업데이트**를 지정합니다.

   ![][11]

3. 모든 종속성도 설치되도록 추가 패키지 설치를 허용합니다.

4. 다음으로, Azure 검색 .NET 클라이언트 라이브러리를 설치합니다. 검색을 올바르게 지정해야 합니다. 그러지 않으면 패키지를 쉽게 찾을 수 없습니다. **NuGet 패키지 관리**를 다시 마우스 오른쪽 단추로 클릭합니다.

5. **온라인** > **nuget.org** > **정식 버전만**을 지정한 다음 *azure.search*를 검색합니다. **설치**를 클릭하여 라이브러리를 설치합니다.

   ![][12]

## System.Configuration에 대한 어셈블리 참조 추가

**DataIndexer**에서는 **System.Configuration**을 사용하여 app.config에서 구성 설정을 읽습니다.

1. **DataIndexer** > **추가** > **참조** > **프레임워크** > **System.Configuration**을 마우스 오른쪽 단추로 클릭합니다. 확인란을 선택합니다.
2. **확인**을 클릭합니다.

## 구성 파일 업데이트

각 프로젝트에는 서비스 이름 및 api-key를 지정하는 구성 파일이 포함되어 있습니다.

1. **DataIndexer**에서 [SERVICE NAME] 및 [SERVICE KEY]를 서비스에 유효한 값으로 업데이트하여 App.config를 다음 예제로 바꿉니다. 서비스 이름은 전체 URL이 아닙니다. 예를 들어 검색 서비스 끝점이 **https://mysearchsrv.search.microsoft.net*인 경우 App.config에 입력할 서비스 이름은 *mysearchsrv*입니다.

		<?xml version="1.0" encoding="utf-8" ?>
		<configuration>
		    <startup> 
		        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
		    </startup>
		    <appSettings>
		      <add key="SearchServiceName" value="[SEARCH SERVICE]" />
		      <add key="SearchServiceApiKey" value="[SEARCH SERVICE API KEY]" />
		    </appSettings>
		</configuration>

2. **SimpleSearchMVCApp**에서 다시 [서비스 이름] 및 [서비스 키]를 서비스에 유효한 값으로 업데이트하여 Web.config를 다음 예제로 바꿉니다.

		<?xml version="1.0" encoding="utf-8"?>
		<!--
			For more information on how to configure your ASP.NET application, please visit
			http://go.microsoft.com/fwlink/?LinkId=152368
			-->
		<configuration>
			<configSections>
				<!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
				<section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=5.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
			</configSections>
			<connectionStrings>
				<add name="DefaultConnection" providerName="System.Data.SqlClient" connectionString="Data Source=(LocalDb)\v11.0;Initial Catalog=aspnet-SimpleMVCApp-20150303114355;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|\aspnet-SimpleMVCApp-20150303114355.mdf" />
			</connectionStrings>
			<appSettings>
				<add key="SearchServiceName" value="[SEARCH SERVICE NAME]" />
				<add key="SearchServiceApiKey" value="[API KEY]" />

				<add key="webpages:Version" value="2.0.0.0" />
				<add key="webpages:Enabled" value="false" />
				<add key="PreserveLoginUrl" value="true" />
				<add key="ClientValidationEnabled" value="true" />
				<add key="UnobtrusiveJavaScriptEnabled" value="true" />
			</appSettings>
			<system.web>
				<httpRuntime targetFramework="4.5" />
				<compilation debug="true" targetFramework="4.5" />
				<authentication mode="Forms">
					<forms loginUrl="~/Account/Login" timeout="2880" />
				</authentication>
				<pages>
					<namespaces>
						<add namespace="System.Web.Helpers" />
						<add namespace="System.Web.Mvc" />
						<add namespace="System.Web.Mvc.Ajax" />
						<add namespace="System.Web.Mvc.Html" />
						<add namespace="System.Web.Optimization" />
						<add namespace="System.Web.Routing" />
						<add namespace="System.Web.WebPages" />
					</namespaces>
				</pages>
				<profile defaultProvider="DefaultProfileProvider">
					<providers>
						<add name="DefaultProfileProvider" type="System.Web.Providers.DefaultProfileProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" applicationName="/" />
					</providers>
				</profile>
				<membership defaultProvider="DefaultMembershipProvider">
					<providers>
						<add name="DefaultMembershipProvider" type="System.Web.Providers.DefaultMembershipProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" enablePasswordRetrieval="false" enablePasswordReset="true" requiresQuestionAndAnswer="false" requiresUniqueEmail="false" maxInvalidPasswordAttempts="5" minRequiredPasswordLength="6" minRequiredNonalphanumericCharacters="0" passwordAttemptWindow="10" applicationName="/" />
					</providers>
				</membership>
				<roleManager defaultProvider="DefaultRoleProvider">
					<providers>
						<add name="DefaultRoleProvider" type="System.Web.Providers.DefaultRoleProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" applicationName="/" />
					</providers>
				</roleManager>
				<!--
								If you are deploying to a cloud environment that has multiple web server instances,
								you should change session state mode from "InProc" to "Custom". In addition,
								change the connection string named "DefaultConnection" to connect to an instance
								of SQL Server (including SQL Azure and SQL  Compact) instead of to SQL Server Express.
					-->
				<sessionState mode="InProc" customProvider="DefaultSessionProvider">
					<providers>
						<add name="DefaultSessionProvider" type="System.Web.Providers.DefaultSessionStateProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" />
					</providers>
				</sessionState>
			</system.web>
			<system.webServer>
				<validation validateIntegratedModeConfiguration="false" />
				<handlers>
					<remove name="ExtensionlessUrlHandler-ISAPI-4.0_32bit" />
					<remove name="ExtensionlessUrlHandler-ISAPI-4.0_64bit" />
					<remove name="ExtensionlessUrlHandler-Integrated-4.0" />
					<add name="ExtensionlessUrlHandler-ISAPI-4.0_32bit" path="*." verb="GET,HEAD,POST,DEBUG,PUT,DELETE,PATCH,OPTIONS" modules="IsapiModule" scriptProcessor="%windir%\Microsoft.NET\Framework\v4.0.30319\aspnet_isapi.dll" preCondition="classicMode,runtimeVersionv4.0,bitness32" responseBufferLimit="0" />
					<add name="ExtensionlessUrlHandler-ISAPI-4.0_64bit" path="*." verb="GET,HEAD,POST,DEBUG,PUT,DELETE,PATCH,OPTIONS" modules="IsapiModule" scriptProcessor="%windir%\Microsoft.NET\Framework64\v4.0.30319\aspnet_isapi.dll" preCondition="classicMode,runtimeVersionv4.0,bitness64" responseBufferLimit="0" />
					<add name="ExtensionlessUrlHandler-Integrated-4.0" path="*." verb="GET,HEAD,POST,DEBUG,PUT,DELETE,PATCH,OPTIONS" type="System.Web.Handlers.TransferRequestHandler" preCondition="integratedMode,runtimeVersionv4.0" />
					<remove name="OPTIONSVerbHandler" />
					<remove name="TRACEVerbHandler" />
				</handlers>
			</system.webServer>
			<entityFramework>
				<defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
					<parameters>
						<parameter value="v12.0" />
					</parameters>
				</defaultConnectionFactory>
			</entityFramework>
			<runtime>
				<assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
					<dependentAssembly>
						<assemblyIdentity name="Newtonsoft.Json" publicKeyToken="30ad4fe6b2a6aeed" culture="neutral" />
						<bindingRedirect oldVersion="0.0.0.0-7.0.0.0" newVersion="7.0.0.0" />
					</dependentAssembly>
					<dependentAssembly>
						<assemblyIdentity name="WebGrease" publicKeyToken="31bf3856ad364e35" culture="neutral" />
						<bindingRedirect oldVersion="0.0.0.0-1.6.5135.21930" newVersion="1.6.5135.21930" />
					</dependentAssembly>
					<dependentAssembly>
						<assemblyIdentity name="Antlr3.Runtime" publicKeyToken="eb42632606e9261f" culture="neutral" />
						<bindingRedirect oldVersion="0.0.0.0-3.5.0.2" newVersion="3.5.0.2" />
					</dependentAssembly>
					<dependentAssembly>
						<assemblyIdentity name="System.Web.Helpers" publicKeyToken="31bf3856ad364e35" />
						<bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
					</dependentAssembly>
					<dependentAssembly>
						<assemblyIdentity name="System.Web.WebPages" publicKeyToken="31bf3856ad364e35" />
						<bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
					</dependentAssembly>
					<dependentAssembly>
						<assemblyIdentity name="System.Web.Mvc" publicKeyToken="31bf3856ad364e35" />
						<bindingRedirect oldVersion="1.0.0.0-5.2.3.0" newVersion="5.2.3.0" />
					</dependentAssembly>
					<dependentAssembly>
						<assemblyIdentity name="System.Web.WebPages.Razor" publicKeyToken="31bf3856ad364e35" />
						<bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
					</dependentAssembly>
				</assemblyBinding>
			</runtime>
		</configuration>


## DataIndexer 수정

이 프로그램은 app.config에 지정된 대로 검색 서비스에 연결하여 인덱스를 만든 후 Azure SQL 데이터베이스에 저장된 USGS 데이터 집합과 함께 로드하는 콘솔 앱입니다. 자습서의 이 부분에서는 인덱서를 사용합니다.

이 프로그램을 실행하기 전에 인덱스를 만들고 인덱서를 실행하며 데이터를 로드하고 메시지를 작성하는 데 사용되는 **Program.cs**를 변경해야 합니다.

### Program.cs 업데이트

1. 솔루션 탐색기에서 **DataIndexer** > **Program.cs**를 엽니다.
2. Program.cs 내용을 다음 코드로 바꿉니다.

	    using System;
	    using System.Configuration;
	    using System.Threading;
	    using Microsoft.Azure.Search;
	    using Microsoft.Azure.Search.Models;
	
	    namespace DataIndexer
	    {
	        class Program
	        {
	            private const string GeoNamesIndex = "geonames";
	            private const string UsgsDataSource = "usgs-datasource";
	            private const string UsgsIndexer = "usgs-indexer";
	
	            private static SearchServiceClient _searchClient;
	            private static SearchIndexClient _indexClient;
	
	            // This Sample shows how to delete, create, upload documents and query an index
	            static void Main(string[] args)
	            {
	                string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
	                string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];
	
	                // Create an HTTP reference to the catalog index
	                _searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
	                _indexClient = _searchClient.Indexes.GetClient(GeoNamesIndex);
	
	                Console.WriteLine("{0}", "Deleting index, data source, and indexer...\n");
	                if (DeleteIndexingResources())
	                {
	                    Console.WriteLine("{0}", "Creating index...\n");
	                    CreateIndex();
	                    Console.WriteLine("{0}", "Sync documents from Azure SQL...\n");
	                    SyncDataFromAzureSQL();
	                }
	                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
	                Console.ReadKey();
	            }
	
	            private static bool DeleteIndexingResources()
	            {
	                // Delete the index, data source, and indexer.
	                try
	                {
	                    _searchClient.Indexes.Delete(GeoNamesIndex);
	                    _searchClient.DataSources.Delete(UsgsDataSource);
	                    _searchClient.Indexers.Delete(UsgsIndexer);
	                }
	                catch (Exception ex)
	                {
	                    Console.WriteLine("Error deleting indexing resources: {0}\r\n", ex.Message);
	                    Console.WriteLine("Did you remember to add your SearchServiceName and SearchServiceApiKey to the app.config?\r\n");
	                    return false;
	                }
	
	                return true;
	            }
	
	            private static void CreateIndex()
	            {
	                // Create the Azure Search index based on the included schema
	                try
	                {
	                    var definition = new Index()
	                    {
	                        Name = GeoNamesIndex,
	                        Fields = new[] 
	                        { 
	                            new Field("FEATURE_ID",     DataType.String)         { IsKey = true,  IsSearchable = false, IsFilterable = false, IsSortable = false, IsFacetable = false, IsRetrievable = true},
	                            new Field("FEATURE_NAME",   DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
	                            new Field("FEATURE_CLASS",  DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
	                            new Field("STATE_ALPHA",    DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
	                            new Field("STATE_NUMERIC",  DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
	                            new Field("COUNTY_NAME",    DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
	                            new Field("COUNTY_NUMERIC", DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
	                            new Field("ELEV_IN_M",      DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
	                            new Field("ELEV_IN_FT",     DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
	                            new Field("MAP_NAME",       DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
	                            new Field("DESCRIPTION",    DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = false, IsSortable = false, IsFacetable = false, IsRetrievable = true},
	                            new Field("HISTORY",        DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = false, IsSortable = false, IsFacetable = false, IsRetrievable = true},
	                            new Field("DATE_CREATED",   DataType.DateTimeOffset) { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
	                            new Field("DATE_EDITED",    DataType.DateTimeOffset) { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true}
	                        }
	                    };
	
	                    _searchClient.Indexes.Create(definition);
	                }
	                catch (Exception ex)
	                {
	                    Console.WriteLine("Error creating index: {0}\r\n", ex.Message);
	                }
	
	            }
	
	            private static void SyncDataFromAzureSQL()
	            {
	                // This will use the Azure Search Indexer to synchronize data from Azure SQL to Azure Search
	                Console.WriteLine("{0}", "Creating Data Source...\n");
	                var dataSource =
	                    new DataSource()
	                    {
	                        Name = UsgsDataSource,
	                        Description = "USGS Dataset",
	                        Type = DataSourceType.AzureSql,
	                        Credentials = new DataSourceCredentials("Server=tcp:azs-playground.database.windows.net,1433;Database=usgs;User ID=reader;Password=EdrERBt3j6mZDP;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;"),
	                        Container = new DataContainer("GeoNamesRI")
	                    };
	
	                try
	                {
	                    _searchClient.DataSources.Create(dataSource);
	                }
	                catch (Exception ex)
	                {
	                    Console.WriteLine("Error creating data source: {0}", ex.Message);
	                    return;
	                }
	
	                Console.WriteLine("{0}", "Creating Indexer and syncing data...\n");
	
	                var indexer =
	                    new Indexer()
	                    {
	                        Name = UsgsIndexer,
	                        Description = "USGS data indexer",
	                        DataSourceName = dataSource.Name,
	                        TargetIndexName = GeoNamesIndex
	                    };
	
	                try
	                {
	                    _searchClient.Indexers.Create(indexer);
	                }
	                catch (Exception ex)
	                {
	                    Console.WriteLine("Error creating and running indexer: {0}", ex.Message);
	                    return;
	                }
	
	                bool running = true;
	                Console.WriteLine("{0}", "Synchronization running...\n");
	                while (running)
	                {
	                    IndexerExecutionInfo status = null;
	
	                    try
	                    {
	                        status = _searchClient.Indexers.GetStatus(indexer.Name);
	                    }
	                    catch (Exception ex)
	                    {
	                        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
	                        return;
	                    }
	
	                    IndexerExecutionResult lastResult = status.LastResult;
	                    if (lastResult != null)
	                    {
	                        switch (lastResult.Status)
	                        {
	                            case IndexerExecutionStatus.InProgress:
	                                Console.WriteLine("{0}", "Synchronization running...\n");
	                                Thread.Sleep(1000);
	                                break;
	
	                            case IndexerExecutionStatus.Success:
	                                running = false;
	                                Console.WriteLine("Synchronized {0} rows...\n", lastResult.ItemCount);
	                                break;
	
	                            default:
	                                running = false;
	                                Console.WriteLine("Synchronization failed: {0}\n", lastResult.ErrorMessage);
	                                break;
	                        }
	                    }
	                }
	            }
	        }
	    }


## DataIndexer 빌드 및 실행

1. **DataIndexer** 프로젝트를 마우스 오른쪽 단추로 클릭합니다.
2. 프로젝트를 빌드합니다.
3. **F5** 키를 눌러 실행합니다.

다음과 같은 메시지가 표시된 콘솔 창이 나타납니다.

![][6]

포털에 새 **geonames** 인덱스가 표시됩니다. 포털을 불어오는 데 몇 분 정도 걸리므로 잠시 기다렸다가 결과를 확인할 수 있습니다.

![][7]

## SimpleSearchMVCApp 수정

**SimpleSearchMVC**는 IIS Express에서 로컬로 실행되는 웹앱으로, 검색 상자를 제공하고 테이블에 검색 결과를 표시합니다.

이 프로그램을 실행하려면 다음 세 가지를 수정해야 합니다.

- 사용자 입력을 허용하는 데 사용되는 **HomeController.cs**를 바꿉니다. 이 예제에서는 *q*라는 변수에 검색 용어가 저장됩니다.

- 검색 용어 입력을 제공하고 검색 결과를 표시하는 웹 페이지인 **index.cshtml**을 바꿉니다.

- 검색 클라이언트를 만들고 검색을 실행하는 클래스인 **FeatureSearch.cs**를 추가합니다.

### HomeController.cs 업데이트

기본 코드를 다음 코드로 바꿉니다.

	    using Microsoft.Azure.Search.Models;
	    using System;
	    using System.Collections.Generic;
	    using System.Linq;
	    using System.Web;
	    using System.Web.Mvc;
	
	    namespace SimpleSearchMVCApp.Controllers
	    {
	        public class HomeController : Controller
	        {
	            //
	            // GET: /Home/
	            private FeaturesSearch _featuresSearch = new FeaturesSearch();
	
	            public ActionResult Index()
	            {
	                return View();
	            }
	
	            public ActionResult Search(string q = "")
	            {
	                // If blank search, assume they want to search everything
	                if (string.IsNullOrWhiteSpace(q))
	                    q = "*";
	
	                return new JsonResult
	                {
	                    JsonRequestBehavior = JsonRequestBehavior.AllowGet,
	                    Data = _featuresSearch.Search(q).Results
	                };
	            }
	        }
	    }


### Index.cshtml 업데이트

기본 코드를 다음 코드로 바꿉니다.

	@{
	    ViewBag.Title = "Azure Search - Feature Search";
	}

	<script src="http://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.10.2.min.js"></script>
	<script type="text/javascript">

	    $(function () {
	        // Execute search if user clicks enter
	        $("#q").keyup(function (event) {
	            if (event.keyCode == 13) {
	                Search();
	            }
	        });
	    });

	    function Search() {
	        // We will post to the MVC controller and parse the full results on the client side
	        // You may wish to do additional pre-processing on the data before sending it back to the client
	        var q = $("#q").val();

	        $.post('/home/search',
	        {
	            q: q
	        },
	        function (data) {
	            var searchResultsHTML = "<tr><td>FEATURE NAME</td><td>FEATURE CLASS</td>";
	            searchResultsHTML += "<td>STATE ALPHA</td><td>COUNTY_NAME</td>";
	            searchResultsHTML += "<td>Elevation (m)</td><td>Elevation (ft)</td><td>MAP NAME</td>";
	            searchResultsHTML += "<td>DESCRIPTION</td><td>HISTORY</td><td>DATE CREATED</td>";
	            searchResultsHTML += "<td>DATE EDITED</td></tr>";
	            for (var i = 0; i < data.length; i++) {
	                searchResultsHTML += "<td>" + data[i].Document.FEATURE_NAME + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.FEATURE_CLASS + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.STATE_ALPHA + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.COUNTY_NAME + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_M + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_FT + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.MAP_NAME + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.DESCRIPTION + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.HISTORY + "</td>";
	                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_CREATED) + "</td>";
	                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_EDITED) + "</td></tr>";
	            }

	            $("#searchResults").html(searchResultsHTML);

	        });

	        function parseJsonDate(jsonDateString) {
	            if (jsonDateString != null)
	                return new Date(parseInt(jsonDateString.replace('/Date(', '')));
	            else
	                return "";
	        }
	    };

	</script>
	<h2>USGS Search for Rhode Island</h2>

	<div class="container">
	    <input type="search" name="q" id="q" autocomplete="off" size="100" /> <button onclick="Search();">Search</button>
	</div>
	<br />
	<div class="container">
	    <div class="row">
	        <table id="searchResults" border="1"></table>
	    </div>
	</div>


### FeaturesSearch.cs 추가

응용 프로그램에 검색 기능을 제공하는 클래스를 추가합니다.

1. 솔루션 탐색기에서 **SimpleSearchMVCApp** > **추가** > **새 항목** > **코드** > **클래스**를 마우스 오른쪽 단추로 클릭합니다.
2. 클래스 이름을 **FeaturesSearch**로 지정합니다.
3. 기본 코드를 다음 코드로 바꿉니다.


	    using System;
	    using System.Configuration;
	    using Microsoft.Azure.Search;
	    using Microsoft.Azure.Search.Models;
	
	    namespace SimpleSearchMVCApp
	    {
	        public class FeaturesSearch
	        {
	            private static SearchServiceClient _searchClient;
	            private static SearchIndexClient _indexClient;
	
	            public static string errorMessage;
	
	            static FeaturesSearch()
	            {
	                try
	                {
	                    string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
	                    string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];
	
	                    // Create an HTTP reference to the catalog index
	                    _searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
	                    _indexClient = _searchClient.Indexes.GetClient("geonames");
	                }
	                catch (Exception e)
	                {
	                    errorMessage = e.Message.ToString();
	                }
	            }
	
	            public DocumentSearchResult Search(string searchText)
	            {
	                // Execute search based on query string
	                try
	                {
	                    SearchParameters sp = new SearchParameters() { SearchMode = SearchMode.All };
	                    return _indexClient.Documents.Search(searchText, sp);
	                }
	                catch (Exception ex)
	                {
	                    Console.WriteLine("Error querying index: {0}\r\n", ex.Message.ToString());
	                }
	                return null;
	            }
	
	        }
	    }


### SimpleSearchMVCApp을 시작 프로젝트로 설정

**SimpleSearchMVCApp** 프로젝트를 마우스 오른쪽 단추로 클릭하여 시작 프로젝트로 설정합니다.

## SimpleSearchMVCApp 빌드 및 실행

이 프로그램을 빌드하고 실행하면 Azure 검색 서비스의 인덱스에 저장된 USG 데이터에 액세스할 수 있는 검색 상자를 제공하는 웹 페이지가 기본 브라우저에 표시됩니다.

![][8]

### USGS 데이터 검색

USGS 데이터 집합에는 Rhode Island 주와 관련된 레코드가 포함되어 있습니다. 빈 검색 상자에서 **검색**을 클릭하면 기본적으로 상위 50개 항목을 가져옵니다.

검색 용어를 입력하면 검색 범위를 좁힐 수 있는 검색 엔진 조건이 제공됩니다. 지역 이름을 입력해 봅니다. *Roger Williams*는 Rhode Island의 최초 주지사였습니다. 유명한 공원, 빌딩 및 학교가 그의 이름을 따라 이름을 지었습니다.

![][9]

구 또는 연산자를 추가하거나 제거하여 검색 범위를 다시 지정하면서 이러한 쿼리를 시도해 볼 수도 있습니다.다.

- Pawtucket OR Pembroke
- goose +cape -neck


## 다음 단계

이것은 .NET 및 USGS 데이터 집합을 기반으로 하는 첫 번째 Azure 검색 자습서입니다. 앞으로 이 자습서를 확장하여 사용자 지정 솔루션에서 사용할 수 있는 추가 검색 기능을 보여 드릴 예정입니다.

Azure 검색에 대한 약간의 배경 지식이 이미 있는 경우 이 샘플을 기반으로 suggesters(사전 입력 또는 자동 완성 쿼리), 필터 및 패싯 탐색을 시작할 수 있습니다. 또한 사용자가 결과 페이지를 차례로 탐색할 수 있도록 개수를 추가하고 문서를 일괄 처리하여 검색 결과 페이지를 개선할 수 있습니다.

Azure 검색을 처음 사용하세요? 다른 자습서를 통해 만들 수 있는 항목에 대한 이해를 높여 보세요. 더 많은 리소스를 보려면 [설명서 페이지](https://azure.microsoft.com/documentation/services/search/)를 방문하세요. [비디오 및 자습서](search-video-demo-tutorial-list.md)의 링크를 통해 추가 정보를 확인할 수도 있습니다.

<!--Image references-->
[1]: ./media/search-get-started-dotnet/create-search-portal-1.PNG
[2]: ./media/search-get-started-dotnet/create-search-portal-2.PNG
[3]: ./media/search-get-started-dotnet/create-search-portal-3.PNG

[4]: ./media/search-get-started-dotnet/AzSearch-DotNet-VSSolutionExplorer.png

[6]: ./media/search-get-started-dotnet/consolemessages.png
[7]: ./media/search-get-started-dotnet/portalindexstatus.png
[8]: ./media/search-get-started-dotnet/usgssearchbox.png
[9]: ./media/search-get-started-dotnet/rogerwilliamsschool.png

[10]: ./media/search-get-started-dotnet/AzSearch-DotNet-MVCOptions.PNG
[11]: ./media/search-get-started-dotnet/AzSearch-DotNet-NuGet-1.PNG
[12]: ./media/search-get-started-dotnet/AzSearch-DotNet-NuGet-2.PNG

<!---HONumber=AcomDC_0224_2016-->