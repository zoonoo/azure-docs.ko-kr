<properties 
	pageTitle="연습: 작업자 역할을 사용하여 Application Insights에서 SQL로 내보내기" 
	description="연속 내보내기 기능을 사용하여 Application Insights에서 원격 분석에 대한 자체 분석을 코딩합니다." 
	services="application-insights" 
    documentationCenter=""
	authors="mazharmicrosoft" 
	manager="klin"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="awills"/>
 
# 연습: 작업자 역할을 사용하여 Application Insights에서 SQL로 내보내기

이 문서에서는 [연속 내보내기][export] 및 적은 양의 코드를 사용하여 [Visual Studio Application Insights][start]에서 Azure SQL 데이터베이스로 원격 분석을 이동하는 방법을 보여줍니다.

연속 내보내기는 원격 분석을 JSON 형식으로 Azure 저장소로 이동하므로, JSON 개체를 구문 분석하고 데이터베이스 테이블에 행을 만드는 코드를 작성해 보겠습니다.

보다 일반적으로 말하자면, 연속 내보내기는 앱에서 Application Insights에 보내는 원격 분석을 자체적으로 분석하는 방법입니다. 원격 분석을 사용하여 이 코드 샘플이 기타 작업을 수행하도록 적용할 수 있습니다.

모니터링하려는 앱이 이미 있다고 가정하고 시작합니다.

## Application Insights SDK 추가

응용 프로그램을 모니터링하려면 응용 프로그램에 [Application Insights SDK를 추가][start]합니다. 다양한 플랫폼, IDE, 언어를 위한 다양한 SDK와 도우미 도구가 있습니다. 웹 페이지, Java 또는 ASP.NET 웹 서버 및 여러 종류의 모바일 장치를 모니터링할 수 있습니다. 모든 SDK는 [Application Insights 포털][portal]로 원격 분석을 보내며 여기서 강력한 분석 및 진단 도구를 사용하고 저장소로 데이터를 내보낼 수 있습니다.

시작하기:

1. [Microsoft Azure에서 계정](http://azure.microsoft.com/pricing/)을 만듭니다.
2. [Azure 포털][portal]에서 앱에 대한 새 Application Insights 리소스를 추가합니다.

    ![새로 만들기, 개발자 서비스, Application Insights 선택 후 응용 프로그램의 유형 선택](./media/app-insights-code-sample-export-telemetry-sql-database/010-new-asp.png)


    (사용자의 앱 유형 및 구독이 다를 수 있습니다.)
3. 해당 앱 유형의 SDK를 설정하는 방법을 찾으려면 빠른 시작을 엽니다.

    ![빠른 시작을 선택하고 지침 따르기](./media/app-insights-code-sample-export-telemetry-sql-database/020-quick.png)

    앱 유형이 나열되어 있지 않으면 [시작][start] 페이지를 살펴봅니다.

4. 이 예제에서는 웹앱을 모니터링하므로 Visual Studio에서 Azure 도구를 사용하여 SDK를 설치할 수 있습니다. 여기에 Application Insights 리소스의 이름을 알려줍니다.

    ![Visual Studio의 새 프로젝트 대화 상자에서 Application Insights 추가를 선택하고 원격 분석 보내기 대상에서 새 앱을 만들거나 기존 앱을 사용하도록 선택합니다.](./media/app-insights-code-sample-export-telemetry-sql-database/030-new-project.png)


## Azure에서 저장소 만들기

1. [Azure 포털][portal]에서 구독에 저장소 계정을 만듭니다.

    ![Azure 포털에서 새로 만들기, 데이터, 저장소 선택](./media/app-insights-code-sample-export-telemetry-sql-database/040-store.png)

2. 컨테이너 만들기

    ![새 저장소에서 컨테이너를 선택한 다음 추가 선택](./media/app-insights-code-sample-export-telemetry-sql-database/050-container.png)


## Azure 저장소로 연속 내보내기 시작

1. Azure 포털에서 응용 프로그램에 대해 만든 Application Insights 리소스를 찾습니다.

    ![찾아보기, Application Insights, 응용 프로그램 선택](./media/app-insights-code-sample-export-telemetry-sql-database/060-browse.png)

2. 연속 내보내기를 만듭니다.

    ![설정, 연속 내보내기, 추가를 차례로 선택](./media/app-insights-code-sample-export-telemetry-sql-database/070-export.png)


    이전에 만든 저장소 계정을 선택합니다.

    ![내보내기 대상 설정](./media/app-insights-code-sample-export-telemetry-sql-database/080-add.png)
    
    보려는 이벤트 유형을 설정합니다.

    ![이벤트 유형 선택](./media/app-insights-code-sample-export-telemetry-sql-database/085-types.png)

이제 한동안 사용자가 응용 프로그램을 사용하도록 놓아둡니다. 원격 분석이 제공되어 [메트릭 탐색기][metrics]에는 통계 차트가 표시되고 [진단 검색][diagnostic]에는 개별 이벤트가 표시됩니다.

또한 데이터는 콘텐츠를 검사할 수 있는 사용자 저장소로 내보내집니다. 예를 들어 Visual Studio에는 저장소 브라우저가 있습니다.


![Visual Studio에서 서버 브라우저, Azure, 저장소 열기](./media/app-insights-code-sample-export-telemetry-sql-database/087-explorer.png)

이벤트는 JSON 형식으로 blob 파일에 기록됩니다. 각 파일에는 하나 이상의 이벤트가 있을 수 있습니다. 따라서 이벤트 데이터를 읽고 원하는 필드를 필터링하는 일부 코드를 작성하려고 합니다. 데이터로 온갖 종류의 작업을 수행할 수 있지만, 지금은 데이터를 SQL 데이터베이스로 이동하는 코드를 작성하려고 합니다. 이렇게 하면 흥미로운 많은 쿼리를 쉽게 실행할 수 있습니다.

## Azure SQL 데이터베이스 만들기

다시 한 번 [Azure 포털][portal]의 구독에서 시작하여 데이터를 쓸 데이터베이스(그리고 아직 없는 경우 새 서버도 포함)를 만듭니다.

![새로 만들기, 데이터, SQL](./media/app-insights-code-sample-export-telemetry-sql-database/090-sql.png)


데이터베이스 서버에서 Azure 서비스에 액세스할 수 있는지 확인합니다.


![찾아보기, 서버, 사용자 서버, 설정, 방화벽, Azure에 대한 액세스 허용](./media/app-insights-code-sample-export-telemetry-sql-database/100-sqlaccess.png)


## 작업자 역할 만들기 

이제 내보낸 Blob에서 JSON을 구문 분석하고 데이터베이스에서 레코드를 만드는 [코드](https://sesitai.codeplex.com/)를 작성할 수 있습니다. 저장소 내보내기와 데이터베이스는 모두 Azure에 있으므로 Azure 작업자 역할에서 코드를 실행할 것입니다.


#### 작업자 역할 프로젝트 만들기

Visual Studio에서 작업자 역할에 대한 새 프로젝트를 만듭니다.

![새 프로젝트, Visual C#, 클라우드, Azure 클라우드 서비스](./media/app-insights-code-sample-export-telemetry-sql-database/110-cloud.png)

![새 클라우드 서비스 대화 상자에서 Visual C#, 작업자 역할 선택](./media/app-insights-code-sample-export-telemetry-sql-database/120-worker.png)


#### 저장소 계정에 연결

Azure에서는 저장소 계정에서 연결 문자열을 가져옵니다.

![저장소 계정에서 키를 선택하고 기본 연결 문자열 복사](./media/app-insights-code-sample-export-telemetry-sql-database/055-get-connection.png)

Visual Studio에서는 저장소 계정 연결 문자열로 작업자 역할 설정을 구성합니다.


![솔루션 탐색기의 클라우드 서비스 프로젝트에서 역할을 확장하고 작업자 역할을 엽니다. 설정 탭을 열어 설정 추가를 선택하고 name=StorageConnectionString, type=connection string으로 설정한 다음 클릭하여 값을 설정합니다. 수동으로 설정하고 해당 연결 문자열을 붙여 넣습니다.](./media/app-insights-code-sample-export-telemetry-sql-database/130-connection-string.png)


#### 패키지

솔루션 탐색기에서 작업자 역할을 마우스 오른쪽 단추로 클릭하고 NuGet 패키지 관리를 선택합니다. 다음 패키지를 검색하고 설치합니다.

 * EntityFramework 6.1.2 이상 - Blob의 JSON 내용을 기반으로 하여 즉석에서 DB 테이블 스키마를 생성할 때 사용합니다.
 * JsonFx - JSON을 C# 클래스 속성으로 평면화하는 데 사용합니다.

이 도구를 사용하여 단일 JSON 문서에서 C# 클래스를 생성합니다. 이렇게 하려면 JSON 배열을 단일 C# 속성으로 평면화한 다음 DB 테이블(예: urlData_port)의 단일 열로 평면화하는 등의 사소한 변경이 필요합니다.

 * [JSON C# 클래스 생성기](http://jsonclassgenerator.codeplex.com/)

## 코드 

`WorkerRole.cs`에 이 코드를 지정할 수 있습니다.

#### 가져오기

    using Microsoft.WindowsAzure.Storage;

    using Microsoft.WindowsAzure.Storage.Blob;

#### 저장소 연결 문자열 검색

    private static string GetConnectionString()
    {
      return Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
    }

#### 주기적으로 작업자 실행

기존 Run 메서드를 바꾸고 원하는 간격을 선택합니다. 내보내기 기능은 한 시간에 하나의 JSON 개체를 완료하므로 이 간격은 적어도 1시간이어야 합니다.

    public override void Run()
    {
      Trace.TraceInformation("WorkerRole1 is running");

      while (true)
      {
        Trace.WriteLine("Sleeping", "Information");

        Thread.Sleep(86400000); //86400000=24 hours //1 hour=3600000
                
        Trace.WriteLine("Awake", "Information");

        ImportBlobtoDB();
      }
    }

#### 각 JSON 개체를 테이블 행으로 삽입


    public void ImportBlobtoDB()
    {
      try
      {
        CloudStorageAccount account = CloudStorageAccount.Parse(GetConnectionString());

        var blobClient = account.CreateCloudBlobClient();
        var container = blobClient.GetContainerReference(FilterContainer);

        foreach (CloudBlobDirectory directory in container.ListBlobs())//Parent directory
        {
          foreach (CloudBlobDirectory subDirectory in directory.ListBlobs())//PageViewPerformance
       	  {
            foreach (CloudBlobDirectory dir in subDirectory.ListBlobs())//2015-01-31
            {
              foreach (CloudBlobDirectory subdir in dir.ListBlobs())//22
              {
                foreach (IListBlobItem item in subdir.ListBlobs())//3IAwm6u3-0.blob
                {
                  itemname = item.Uri.ToString();
                  ParseEachBlob(container, item);
                  AuditBlob(container, directory, subDirectory, dir, subdir, item);
                } //item loop
              } //subdir loop
            } //dir loop
          } //subDirectory loop
        } //directory loop
      }
      catch (Exception ex)
      {
		//handle exception
      }
    }

#### 각 Blob 구문 분석

    private void ParseEachBlob(CloudBlobContainer container, IListBlobItem item)
    {
      try
      {
        var blob = container.GetBlockBlobReference(item.Parent.Prefix + item.Uri.Segments.Last());
    
        string json;
    
        using (var memoryStream = new MemoryStream())
        {
          blob.DownloadToStream(memoryStream);
          json = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    
          IEnumerable<string> entities = json.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
    
          recCount = entities.Count();
          failureCount = 0; //resetting failure count
    
          foreach (var entity in entities)
          {
            var reader = new JsonFx.Json.JsonReader();
            dynamic output = reader.Read(entity);
    
            Dictionary<string, object> dict = new Dictionary<string, object>();
    
            GenerateDictionary((System.Dynamic.ExpandoObject)output, dict, "");
    
            switch (FilterType)
            {
              case "PageViewPerformance":
    
              if (dict.ContainsKey("clientPerformance"))
                {
                  GenerateDictionary(((System.Dynamic.ExpandoObject[])dict["clientPerformance"])[0], dict, "");
    	        }
    
              if (dict.ContainsKey("context_custom_dimensions"))
              {
                if (dict["context_custom_dimensions"].GetType() == typeof(System.Dynamic.ExpandoObject[]))
                {
                  GenerateDictionary(((System.Dynamic.ExpandoObject[])dict["context_custom_dimensions"])[0], dict, "");
                }
              }
    
            PageViewPerformance objPageViewPerformance = (PageViewPerformance)GetObject(dict);
    
            try
            {
              using (var db = new TelemetryContext())
              {
                db.PageViewPerformanceContext.Add(objPageViewPerformance);
                db.SaveChanges();
              }
            }
            catch (Exception ex)
            {
              failureCount++;
            }
            break;
    
            default:
            break;
          }
        }
      }
    }
    catch (Exception ex)
    {
      //handle exception 
    }
    }

#### 각 JSON 문서에 대한 사전 준비


    private void GenerateDictionary(System.Dynamic.ExpandoObject output, Dictionary<string, object> dict, string parent)
        {
            try
            {
                foreach (var v in output)
                {
                    string key = parent + v.Key;
                    object o = v.Value;

                    if (o.GetType() == typeof(System.Dynamic.ExpandoObject))
                    {
                        GenerateDictionary((System.Dynamic.ExpandoObject)o, dict, key + "_");
                    }
                    else
                    {
                        if (!dict.ContainsKey(key))
                        {
                            dict.Add(key, o);
                        }
                    }
                }
            }
            catch (Exception ex)
            {
      		//handle exception 
    	    }
        }

#### JSON 문서를 C# 클래스 원격 분석 개체 속성으로 캐스팅

     public object GetObject(IDictionary<string, object> d)
        {
            PropertyInfo[] props = null;
            object res = null;

            try
            {
                switch (FilterType)
                {
                    case "PageViewPerformance":

                        props = typeof(PageViewPerformance).GetProperties();
                        res = Activator.CreateInstance<PageViewPerformance>();
                        break;

                    default:
                        break;
                }

                for (int i = 0; i < props.Length; i++)
                {
                    if (props[i].CanWrite && d.ContainsKey(props[i].Name))
                    {
                        props[i].SetValue(res, d[props[i].Name], null);
                    }
                }
            }
            catch (Exception ex)
            {
      		//handle exception 
    	    }

            return res;
        }

#### JSON 문서에서 생성된 PageViewPerformance 클래스 파일

    public class PageViewPerformance
    {
    	[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
        public Guid Id { get; set; }

        public string url { get; set; }

        public int urlData_port { get; set; }

        public string urlData_protocol { get; set; }

        public string urlData_host { get; set; }

        public string urlData_base { get; set; }

        public string urlData_hashTag { get; set; }

        public double total_value { get; set; }

        public double networkConnection_value { get; set; }

        public double sendRequest_value { get; set; }

        public double receiveRequest_value { get; set; }

        public double clientProcess_value { get; set; }

        public string name { get; set; }

        public string internal_data_id { get; set; }

        public string internal_data_documentVersion { get; set; }

        public DateTime? context_data_eventTime { get; set; }

        public string context_device_id { get; set; }

        public string context_device_type { get; set; }

        public string context_device_os { get; set; }

        public string context_device_osVersion { get; set; }

        public string context_device_locale { get; set; }

        public string context_device_userAgent { get; set; }

        public string context_device_browser { get; set; }

        public string context_device_browserVersion { get; set; }

        public string context_device_screenResolution_value { get; set; }

        public string context_user_anonId { get; set; }

        public string context_user_anonAcquisitionDate { get; set; }

        public string context_user_authAcquisitionDate { get; set; }

        public string context_user_accountAcquisitionDate { get; set; }

        public string context_session_id { get; set; }

        public bool context_session_isFirst { get; set; }

        public string context_operation_id { get; set; }

        public double context_location_point_lat { get; set; }

        public double context_location_point_lon { get; set; }

        public string context_location_clientip { get; set; }

        public string context_location_continent { get; set; }

        public string context_location_country { get; set; }

        public string context_location_province { get; set; }

        public string context_location_city { get; set; }
    }


#### Entity Framework의 SQL 상호 작용에 대한 DBcontext

	public class TelemetryContext : DbContext
    {
        public DbSet<PageViewPerformance> PageViewPerformanceContext { get; set; }
        public TelemetryContext()
            : base("name=TelemetryContext")
        {
        }
    }

`app.config`에 이름이 `TelemetryContext`인 DB 연결 문자열을 추가합니다.

## 스키마(정보만)

PageView에 대해 생성되는 테이블의 스키마입니다.

> [AZURE.NOTE]이 스크립트를 실행하지 않아도 됩니다. JSON에서 특성은 테이블의 열을 결정합니다.

    CREATE TABLE [dbo].[PageViewPerformances](
	[Id] [uniqueidentifier] NOT NULL,
	[url] [nvarchar](max) NULL,
	[urlData_port] [int] NOT NULL,
	[urlData_protocol] [nvarchar](max) NULL,
	[urlData_host] [nvarchar](max) NULL,
	[urlData_base] [nvarchar](max) NULL,
	[urlData_hashTag] [nvarchar](max) NULL,
	[total_value] [float] NOT NULL,
	[networkConnection_value] [float] NOT NULL,
	[sendRequest_value] [float] NOT NULL,
	[receiveRequest_value] [float] NOT NULL,
	[clientProcess_value] [float] NOT NULL,
	[name] [nvarchar](max) NULL,
	[User] [nvarchar](max) NULL,
	[internal_data_id] [nvarchar](max) NULL,
	[internal_data_documentVersion] [nvarchar](max) NULL,
	[context_data_eventTime] [datetime] NULL,
	[context_device_id] [nvarchar](max) NULL,
	[context_device_type] [nvarchar](max) NULL,
	[context_device_os] [nvarchar](max) NULL,
	[context_device_osVersion] [nvarchar](max) NULL,
	[context_device_locale] [nvarchar](max) NULL,
	[context_device_userAgent] [nvarchar](max) NULL,
	[context_device_browser] [nvarchar](max) NULL,
	[context_device_browserVersion] [nvarchar](max) NULL,
	[context_device_screenResolution_value] [nvarchar](max) NULL,
	[context_user_anonId] [nvarchar](max) NULL,
	[context_user_anonAcquisitionDate] [nvarchar](max) NULL,
	[context_user_authAcquisitionDate] [nvarchar](max) NULL,
	[context_user_accountAcquisitionDate] [nvarchar](max) NULL,
	[context_session_id] [nvarchar](max) NULL,
	[context_session_isFirst] [bit] NOT NULL,
	[context_operation_id] [nvarchar](max) NULL,
	[context_location_point_lat] [float] NOT NULL,
	[context_location_point_lon] [float] NOT NULL,
	[context_location_clientip] [nvarchar](max) NULL,
	[context_location_continent] [nvarchar](max) NULL,
	[context_location_country] [nvarchar](max) NULL,
	[context_location_province] [nvarchar](max) NULL,
	[context_location_city] [nvarchar](max) NULL,
    CONSTRAINT [PK_dbo.PageViewPerformances] PRIMARY KEY CLUSTERED 
    (
     [Id] ASC
    )WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]

    GO

    ALTER TABLE [dbo].[PageViewPerformances] ADD  DEFAULT (newsequentialid()) FOR [Id]
    GO


이 예제의 실행을 확인하려면 전체 작업 코드를 [다운로드](https://sesitai.codeplex.com/)한 다음 `app.config` 설정을 변경하고 작업자 역할을 Azure에 게시합니다.


## 관련된 문서

* [작업자 역할을 사용하여 SQL로 내보내기](app-insights-code-sample-export-telemetry-sql-database.md)
* [Application Insights에서 연속 내보내기](app-insights-export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[export]: app-insights-export-telemetry.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[start]: app-insights-get-started.md

 

<!---HONumber=July15_HO4-->