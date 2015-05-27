<properties 
	pageTitle="스케줄러를 사용하여 백 엔드 작업 예약 - 모바일 서비스" 
	description="Azure 모바일 서비스 스케줄러를 사용하여 모바일 앱에 대한 작업을 예약합니다." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="glenga"/>

# 모바일 서비스에서 되풀이 작업 예약 

> [AZURE.SELECTOR-LIST (Platform | Backend)]
- [(Any | .NET)](mobile-services-dotnet-backend-schedule-recurring-tasks.md)
- [(Any | Javascript)](mobile-services-schedule-recurring-tasks.md)
 
이 항목에서는 관리 포털의 작업 스케줄러 기능을 사용하여 정의한 일정에 따라 실행되는 서버 스크립트 코드를 정의하는 방법을 보여 줍니다. 이 경우 스크립트는 원격 서비스(이 예에서는 Twitter)를 주기적으로 확인하여 결과를 새 테이블에 저장합니다. 예약할 수 있는 다른 정기 작업에는 다음이 포함됩니다.

+ 오래되었거나 중복된 데이터 레코드 보관
+ 트윗, RSS 항목, 위치 정보 등의 외부 데이터 요청 및 저장
+ 저장된 이미지 처리 또는 크기 조정

이 자습서에서는 Twitter에 트윗 데이터를 요청하고 이 트윗을 새 Updates 테이블에 저장하는 예약된 작업을 작업 스케줄러를 사용하여 만드는 단계를 안내합니다.

+ [Twitter 액세스 및 저장 자격 증명 등록]
+ [새 Updates 테이블 만들기]
+ [새 예약된 작업 만들기]

## <a name="get-oauth-credentials"></a>Twitter v1.1 API 액세스 및 저장 자격 증명 등록

[AZURE.INCLUDE [mobile-services-register-twitter-access](../includes/mobile-services-register-twitter-access.md)]

## <a name="create-table"></a>새 Updates 테이블 만들기

다음으로, 트윗을 저장할 새 테이블을 만들어야 합니다.

2. 관리 포털에서 모바일 서비스의 **데이터** 탭을 클릭한 후 **+만들기**를 클릭합니다.

3. **테이블 이름**에 _Updates_를 입력한 후 확인 단추를 클릭합니다.

## <a name="add-job"></a>새 예약된 작업 만들기  

이제 Twitter에 액세스하고 새 Updates 테이블에 트윗 데이터를 저장하는 예약된 작업을 만들 수 있습니다.

2. **스케줄러** 탭을 클릭한 후 **+만들기**를 클릭합니다. 

    >[AZURE.NOTE]<em>무료</em> 계층에서 모바일 서비스를 실행하는 경우 한 번에 하나의 예약된 작업만 실행할 수 있습니다. 유료 계층에서는 한 번에 최대 10개의 예약된 작업을 실행할 수 있습니다.

3. 스케줄러 대화 상자에서 _작업 이름_으로 **getUpdates**를 입력하고 일정 간격 및 단위를 설정한 후 확인 단추를 클릭합니다.

   	이를 통해 **getUpdates**라는 새 작업이 만들어집니다.

4. 방금 만든 새 작업을 클릭한 후 **스크립트** 탭을 클릭하고 자리 표시자 함수 **getUpdates**를 다음 코드로 바꿉니다.

		var updatesTable = tables.getTable('Updates');
		var request = require('request');
		var twitterUrl = "https://api.twitter.com/1.1/search/tweets.json?q=%23mobileservices&result_type=recent";

		// Get the service configuration module.
		var config = require('mobileservice-config');
		
		// Get the stored Twitter consumer key and secret. 
		var consumerKey = config.twitterConsumerKey,
		    consumerSecret = config.twitterConsumerSecret
		// Get the Twitter access token from app settings.    
		var accessToken= config.appSettings.TWITTER_ACCESS_TOKEN,
		    accessTokenSecret = config.appSettings.TWITTER_ACCESS_TOKEN_SECRET;
		
		function getUpdates() {   
		    // Check what is the last tweet we stored when the job last ran
		    // and ask Twitter to only give us more recent tweets
		    appendLastTweetId(
		        twitterUrl, 
		        function twitterUrlReady(url){            
		            // Create a new request with OAuth credentials.
		            request.get({
		                url: url,                
		                oauth: {
		                    consumer_key: consumerKey,
		                    consumer_secret: consumerSecret,
		                    token: accessToken,
		                    token_secret: accessTokenSecret
		                }},
		                function (error, response, body) {
		                if (!error && response.statusCode == 200) {
		                    var results = JSON.parse(body).statuses;
		                    if(results){
		                        console.log('Fetched ' + results.length + ' new results from Twitter');                       
		                        results.forEach(function (tweet){
		                            if(!filterOutTweet(tweet)){
		                                var update = {
		                                    twitterId: tweet.id,
		                                    text: tweet.text,
		                                    author: tweet.user.screen_name,
		                                    date: tweet.created_at
		                                };
		                                updatesTable.insert(update);
		                            }
		                        });
		                    }            
		                } else { 
		                    console.error('Could not contact Twitter');
		                }
		            });
		
		        });
		 }
		// Find the largest (most recent) tweet ID we have already stored
		// (if we have stored any) and ask Twitter to only return more
		// recent ones
		function appendLastTweetId(url, callback){
		    updatesTable
		    .orderByDescending('twitterId')
		    .read({success: function readUpdates(updates){
		        if(updates.length){
		            callback(url + '&since_id=' + (updates[0].twitterId + 1));           
		        } else {
		            callback(url);
		        }
		    }});
		}
		
		function filterOutTweet(tweet){
		    // Remove retweets and replies
		    return (tweet.text.indexOf('RT') === 0 || tweet.to_user_id);
		}


   	이 스크립트에서는 `#mobileservices` 해시태그를 포함한 최근 트윗을 요청하기 위해 저장된 자격 증명을 사용하여 Twitter 쿼리 API를 호출합니다. 중복 트윗 및 회신은 테이블에 저장되기 전에 결과에서 제거됩니다.

    >[AZURE.NOTE]이 샘플에서는 각 예약 실행 도중에 몇 개의 행만 테이블에 삽입된다고 가정합니다. 반복해서 많은 행이 삽입되는 경우에는 무료 계층에서 실행할 때 연결이 끊어질 수 있습니다. 이런 경우 일괄 처리로 삽입해야 합니다. 자세한 내용은 <a href="/develop/mobile/how-to-guides/work-with-server-scripts/#bulk-inserts">방법: 대량 삽입 수행</a>을 참조하세요.

6. **Run Once**를 클릭하여 스크립트를 테스트합니다.

   	그러면 작업이 저장되고 스케줄러에서 사용할 수 없는 상태로 유지되면서 실행됩니다.

7. 뒤로 단추를 클릭하고 **데이터**를 클릭한 다음 **업데이트** 테이블을 클릭하고 **찾아보기**를 클릭하여 Twitter 데이터가 테이블에 삽입되었는지 확인합니다.

8. 뒤로 단추를 클릭하고 **스케줄러**를 클릭한 후 **getUpdates**를 선택하고 **Enable**을 클릭합니다.

   	이를 통해 지정된 일정에서 작업을 실행할 수 있게 되며, 이 경우에는 매 시간마다 실행하게 됩니다.

이제 모바일 서비스에서 새로운 예약된 작업을 만들었습니다. 이 작업은 사용하지 않도록 설정하거나 수정할 때까지는 예약된 대로 실행됩니다.

## <a name="nextsteps"> </a>다음 단계

* [모바일 서비스 서버 스크립트 참조] <br/>서버 스크립트의 등록 및 사용에 대해 자세히 알아봅니다.

<!-- Anchors. -->
[Twitter 액세스 및 저장 자격 증명 등록]: #get-oauth-credentials
[새 Updates 테이블 만들기]: #create-table
[새 예약된 작업 만들기]: #add-job
[Next steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-schedule-recurring-tasks/mobile-twitter-my-apps.png
[1]: ./media/mobile-services-schedule-recurring-tasks/mobile-twitter-app-secrets.png
[2]: ./media/mobile-services-schedule-recurring-tasks/mobile-data-tab-empty-cli.png
[3]: ./media/mobile-services-schedule-recurring-tasks/mobile-create-updates-table.png
[4]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-new-job-cli.png
[5]: ./media/mobile-services-schedule-recurring-tasks/mobile-create-job-dialog.png
[6]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-script-new.png
[7]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-script.png
[8]: ./media/mobile-services-schedule-recurring-tasks/mobile-browse-updates-table.png
[9]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-enabled.png
[10]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-app-settings.png
[11]: ./media/mobile-services-schedule-recurring-tasks/mobile-identity-tab-twitter-only.png

<!-- URLs. -->
[모바일 서비스 서버 스크립트 참조]: http://go.microsoft.com/fwlink/?LinkId=262293
[WindowsAzure.com]: http://www.windowsazure.com/
[Azure Management Portal]: https://manage.windowsazure.com/
[Register your apps for Twitter login with Mobile Services]: /develop/mobile/how-to-guides/register-for-twitter-authentication
[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

<!--HONumber=54-->