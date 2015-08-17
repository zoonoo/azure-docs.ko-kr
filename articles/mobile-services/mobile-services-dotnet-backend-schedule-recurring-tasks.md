<properties 
	pageTitle="Azure 모바일 서비스에서 되풀이 작업 예약" 
	description="Azure 모바일 서비스 스케줄러를 사용하여 모바일 앱에 대한 작업을 예약합니다." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="glenga"/>

# 모바일 서비스에서 되풀이 작업 예약 

> [AZURE.SELECTOR-LIST (Platform | Backend)]
- [(Any | .NET)](mobile-services-dotnet-backend-schedule-recurring-tasks.md)
- [(Any | Javascript)](mobile-services-schedule-recurring-tasks.md)
 
이 항목에서는 관리 포털의 작업 스케줄러 기능을 사용하여 정의한 일정에 따라 실행되는 서버 스크립트 코드를 정의하는 방법을 보여 줍니다. 이 경우 스크립트는 원격 서비스(이 예에서는 Twitter)를 주기적으로 확인하여 결과를 새 테이블에 저장합니다. 예약할 수 있는 다른 정기 작업에는 다음이 포함됩니다.

+ 오래되었거나 중복된 데이터 레코드 보관
+ 트윗, RSS 항목, 위치 정보 등의 외부 데이터 요청 및 저장
+ 저장된 이미지 처리 또는 크기 조정

이 자습서에서는 작업 스케줄러를 사용하여, Twitter에서 트윗 데이터를 요청하고 트윗을 새 Updates 테이블에 저장하는 예약된 작업을 만드는 방법을 안내합니다.

>[AZURE.NOTE]이 자습서에서는 타사 LINQ to Twitter 라이브러리를 사용하여 OAuth 2.0에서 의 Twitter v1.1. API 액세스를 간소화합니다. 이 자습서를 완료하려면 LINQ to Twitter NuGet 패키지를 다운로드하여 설치해야 합니다. 자세한 내용은 [LINQ to Twitter CodePlex 프로젝트](영문)를 참조하십시오.

##<a name="get-oauth-credentials"></a>Twitter v1.1 API 액세스 및 저장 자격 증명 등록

[AZURE.INCLUDE [mobile-services-register-twitter-access](../../includes/mobile-services-register-twitter-access.md)]

&nbsp;&nbsp;7. Visual Studio의 솔루션 탐색기에서 모바일 서비스 프로젝트에 대한 web.config 파일을 열고, `MS_TwitterConsumerKey` 및 `MS_TwitterConsumerSecret` 앱 설정을 찾은 다음 이 키 값을 포털에서 설정한 Twitter consumer key 및 consumer secret 값으로 바꿉니다.

&nbsp;&nbsp;8. 동일한 섹션에서 다음과 같은 새 앱 설정을 추가하고 자리 표시자를 포털에서 앱 설정으로 지정한 Twitter 액세스 토큰 및 액세스 토큰 암호 값으로 바꿉니다.

	<add key="TWITTER_ACCESS_TOKEN" value="**your_access_token**" />
	<add key="TWITTER_ACCESS_TOKEN_SECRET" value="**your_access_token_secret**" />

모바일 서비스를 로컬 컴퓨터에서 실행하면 이러한 저장된 설정을 사용하여 예약된 작업을 게시하기 전에 테스트할 수 있습니다. Azure에서 실행 중인 경우 모바일 서비스에서는 포털에 설정된 값을 대신 사용하고 이 프로젝트 설정은 무시합니다.

##<a name="install-linq2twitter"></a>LINQ to Twitter 라이브러리 다운로드 및 설치

1. Visual Studio의 **솔루션 탐색기**에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.

2. 왼쪽 창에서 **온라인** 범주를 선택하고, `linq2twitter`를 검색하고, **linqtotwitter** 패키지에서 **설치**를 클릭한 다음 라이선스 계약을 읽고 동의합니다.

  	![][1]

  	Linq to Twitter 라이브러리가 모바일 서비스 프로젝트에 추가됩니다.

다음으로, 트윗을 저장할 새 테이블을 만들어야 합니다.

##<a name="create-table"></a>새 Updates 테이블 만들기

1. Visual Studio의 솔루션 탐색기에서 DataObjects 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**를 확장하여 **클래스**를 클릭하고 **이름**에 `Updates`을(를) 입력한 다음 **추가**를 클릭합니다.

	Updates 클래스에 대한 새 프로젝트 파일이 만들어집니다.

2. **참조** > **참조 추가...** > **어셈블리** 아래의 **프레임워크**를 마우스 오른쪽 단추로 클릭하고 **System.ComponentModel.DataAnnotations**를 선택한 후 **확인**을 클릭합니다.

	![][7]

	이렇게 하면 새 어셈블리 참조가 추가됩니다.

2. 이 새 클래스에서 다음 **using** 문을 추가합니다.
 
		using Microsoft.WindowsAzure.Mobile.Service;
		using System.ComponentModel.DataAnnotations;

3. **Updates** 클래스 정의를 다음 코드로 바꿉니다.

		public class Updates 
	    {
	        [Key]
	        public int UpdateId { get; set; }
	        public long TweetId { get; set; }
	        public string Text { get; set; }
	        public string Author { get; set; }
	        public DateTime Date { get; set; }
    	}

4. Models 폴더를 확장하고 *service\_name*Context.cs라는 데이터 모델 컨텍스트 파일을 열어 입력한 **DbSet**를 반환하는 다음 속성을 추가합니다.

		public DbSet<Updates> Updates { get; set; }

	DbSet에 처음 액세스할 때 생성되는 Updates 테이블은 서비스에서 트윗 데이터를 저장하는 데 사용됩니다.

	>[AZURE.NOTE]기본 데이터베이스 이니셜라이저를 사용할 경우 Entity Framework에서는 Code First 모델 정의에서 데이터 모델 변경이 감지될 때마다 데이터베이스를 삭제하고 다시 만듭니다. 이 데이터 모델을 변경하고 데이터베이스에서 기존 데이터를 유지하려면 Code First 마이그레이션을 사용해야 합니다. Azure에서는 SQL 데이터베이스에 대해 기본 이니셜라이저를 사용할 수 없습니다. 자세한 내용은 [Code First 마이그레이션을 사용하여 데이터 모델을 업데이트하는 방법](mobile-services-dotnet-backend-use-code-first-migrations.md)을 참조하십시오.

이제 Twitter에 액세스하고 새 Updates 테이블에 트윗 데이터를 저장하는 예약된 작업을 만듭니다.

##<a name="add-job"></a>새 예약된 작업 만들기  

1. ScheduledJobs 폴더를 확장하고 SampleJob.cs 프로젝트 파일을 엽니다.

	**ScheduledJob**에서 상속되는 이 클래스는 Azure 관리 포털에서 정해진 일정을 따르거나 요청 시 실행하도록 예약할 수 있는 작업을 나타냅니다.

2. SampleJob.cs 내용을 다음 코드로 바꿉니다.
 
		using System;
		using System.Linq;
		using System.Threading;
		using System.Threading.Tasks;
		using System.Web.Http;
		using Microsoft.WindowsAzure.Mobile.Service;
		using Microsoft.WindowsAzure.Mobile.Service.ScheduledJobs;
		using LinqToTwitter;
		using todolistService.Models;
		using todolistService.DataObjects;
		
		namespace todolistService
		{
		    // A simple scheduled job which can be invoked manually by submitting an HTTP
		    // POST request to the path "/jobs/sample".
		    public class SampleJob : ScheduledJob
		    {
		        private todolistContext context;
		        private string accessToken;
		        private string accessTokenSecret;
		
		        protected override void Initialize(ScheduledJobDescriptor scheduledJobDescriptor, 
					CancellationToken cancellationToken)
		        {
		            base.Initialize(scheduledJobDescriptor, cancellationToken);
		
		            // Create a new context with the supplied schema name.
		            context = new todolistContext();
		        }
		
		        public async override Task ExecuteAsync()
		        {            
		            // Try to get the stored Twitter access token from app settings.  
		            if (!(Services.Settings.TryGetValue("TWITTER_ACCESS_TOKEN", out accessToken) |
		            Services.Settings.TryGetValue("TWITTER_ACCESS_TOKEN_SECRET", out accessTokenSecret)))
		            {
		                Services.Log.Error("Could not retrieve Twitter access credentials.");
		            }
		
		            // Create a new authorizer to access Twitter v1.1 APIs
		            // using single-user OAUth 2.0 credentials.
		            MvcAuthorizer auth = new MvcAuthorizer();
		            SingleUserInMemoryCredentialStore store = 
		                new SingleUserInMemoryCredentialStore()
		            {
		                ConsumerKey = Services.Settings.TwitterConsumerKey,
		                ConsumerSecret = Services.Settings.TwitterConsumerSecret,
		                OAuthToken = accessToken,
		                OAuthTokenSecret = accessTokenSecret
		            };
		
		            // Set the credentials for the authorizer.
		            auth.CredentialStore = store;
		
		            // Create a new LINQ to Twitter context.
		            TwitterContext twitter = new TwitterContext(auth);
		
		            // Get the ID of the most recent stored tweet.
		            long lastTweetId = 0;
		            if (context.Updates.Count() > 0)
		            {
		                lastTweetId = (from u in context.Updates
		                               orderby u.TweetId descending
		                               select u).Take(1).SingleOrDefault()
		                                            .TweetId;
		            }
		
		            // Execute a search that returns a filtered result.
		            var response = await (from s in twitter.Search
		                                  where s.Type == SearchType.Search
		                                  && s.Query == "%23mobileservices"
		                                  && s.SinceID == Convert.ToUInt64(lastTweetId + 1)
		                                  && s.ResultType == ResultType.Recent
		                                  select s).SingleOrDefaultAsync();
		
		            // Remove retweets and replies and log the number of tweets.
		            var filteredTweets = response.Statuses
		                .Where(t => !t.Text.StartsWith("RT") && t.InReplyToUserID == 0);
		            Services.Log.Info("Fetched " + filteredTweets.Count()
		                + " new tweets from Twitter.");
		
		            // Store new tweets in the Updates table.
		            foreach (Status tweet in filteredTweets)
		            {
		                Updates newTweet =
		                    new Updates
		                    {
		                        TweetId = Convert.ToInt64(tweet.StatusID),
		                        Text = tweet.Text,
		                        Author = tweet.User.Name,
		                        Date = tweet.CreatedAt
		                    };
		
		                context.Updates.Add(newTweet);
		            }
		
		            await context.SaveChangesAsync();
		        }
		        protected override void Dispose(bool disposing)
		        {
		            base.Dispose(disposing);
		            if (disposing)
		            {
		                context.Dispose();
		            }
		        }
		    }
		}

	위의 코드에서 _todolistService_ 및 _todolistContext_ 문자열을 각각 *mobile&#95;service&#95;name*Service 및 *mobile&#95;service&#95;name*Context에 해당하는 다운로드한 프로젝트의 네임스페이스 및 DbContext로 바꾸어야 합니다.
   	
	위의 코드에서 **ExecuteAsync** 재정의 메서드는 `#mobileservices` 해시태그를 포함한 최근 트윗을 요청하기 위해 저장된 자격 증명을 사용하여 Twitter 쿼리 API를 호출합니다. 중복 트윗 및 회신은 테이블에 저장되기 전에 결과에서 제거됩니다.

##<a name="run-job-locally"></a>로컬에서 예약된 작업 테스트

Azure에 게시하고 포털에 등록하기 전에 로컬에서 작업 예약을 테스트할 수 있습니다.

1. Visual Studio에서 모바일 서비스 프로젝트를 시작 프로젝트로 설정하고 F5 키를 누릅니다.

	모바일 서비스 프로젝트가 시작되고 새 브라우저 창에 시작 페이지가 표시됩니다.

2. **체험**을 클릭한 후 **POST 작업/{jobName}**을 클릭합니다.

	![][8]
 
4. **사용해보기**를 클릭하고 **{jobName}** 매개 변수 값으로 `Sample`을(를) 입력한 후 **보내기**를 클릭합니다.

	![][9]

	이렇게 하면 Sample 작업 끝점으로 새 POST 요청이 전송됩니다. 로컬 서비스에서 **ExecuteAsync** 메서드가 시작됩니다. 코드를 디버깅하기 위해 이 메서드에 중단점을 설정할 수 있습니다.

3. 서버 탐색기에서 **데이터 연결**, **MSTableConnectionString** 및 **테이블**을 확장하고 **업데이트**를 마우스 오른쪽 단추로 클릭한 다음 **테이블 데이터 표시**를 클릭합니다.

	새 트윗이 데이터 테이블에 행으로 입력됩니다.

##<a name="register-job"></a>서비스 게시 및 새 작업 등록 

모바일 서비스에서 정의된 일정에 따라 작업을 실행할 수 있도록 **스케줄러** 탭에서 작업을 등록해야 합니다.

3. Azure에 모바일 서비스 프로젝트를 다시 게시합니다. 

4. [Azure 관리 포털]에서 모바일 서비스를 클릭한 후 앱을 클릭합니다.

2. **스케줄러** 탭을 클릭한 후 **+만들기**를 클릭합니다.

    >[AZURE.NOTE]<em>무료</em> 계층에서 모바일 서비스를 실행하는 경우 한 번에 하나의 예약된 작업만 실행할 수 있습니다. 유료 계층에서는 한 번에 최대 10개의 예약된 작업을 실행할 수 있습니다.

3. 스케줄러 대화 상자에서 **작업 이름**으로 _Sample_을 입력하고 일정 간격 및 단위를 설정한 후 확인 단추를 클릭합니다.
   
   	![][4]

   	이를 통해 **Sample**이라는 새 작업이 만들어집니다.

4. 방금 만든 새 작업을 클릭한 후 **한 번 실행**을 클릭하여 스크립트를 테스트합니다.

   	그러면 작업이 스케줄러에서 사용할 수 없는 상태로 유지되면서 실행됩니다. 이 페이지에서 작업을 사용하도록 설정하고 언제든지 일정을 변경할 수 있습니다.

	>[AZURE.NOTE]POST 요청을 사용하여 예약된 작업을 시작할 수도 있습니다. 하지만 권한 부여 기본값은 사용자로 설정되므로 요청의 헤더에 응용 프로그램 키를 포함해야 합니다.

4. (옵션) [Azure 관리 포털]에서 모바일 서비스에 연결된 데이터베이스에 대한 관리를 클릭합니다.

    ![][6]

5. 관리 포털에서 앱에서 변경한 내용을 표시하는 쿼리를 실행합니다. 쿼리는 다음 쿼리와 비슷하지만 모바일 서비스 이름을 `todolist` 대신 스키마 이름으로 사용합니다.

        SELECT * FROM [todolist].[Updates]

이제 모바일 서비스에서 새로운 예약된 작업을 만들었습니다. 이 작업은 사용하지 않도록 설정하거나 수정할 때까지는 예약된 대로 실행됩니다.

<!-- Anchors. -->
[Register for Twitter access and store credentials]: #get-oauth-credentials
[Download and install the LINQ to Twitter library]: #install-linq2twitter
[Create the new Updates table]: #create-table
[Create a new scheduled job]: #add-job
[Test the scheduled job locally]: #run-job-locally
[Publish the service and register the job]: #register-job
[Next steps]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-linq2twitter-nuget-package.png
[2]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-services-selection.png
[3]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-schedule-new-job-cli.png
[4]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/create-new-job.png
[5]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/sample-job-run-once.png
[6]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/manage-sql-azure-database.png
[7]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-component-model-reference.png
[8]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-service-start-page.png
[9]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-service-try-this-out.png

<!-- URLs. -->
[Azure 관리 포털]: https://manage.windowsazure.com/
[Register your apps for Twitter login with Mobile Services]: mobile-services-how-to-register-twitter-authentication.md
[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
[LINQ to Twitter CodePlex 프로젝트]: http://linqtotwitter.codeplex.com/

<!---HONumber=August15_HO6-->