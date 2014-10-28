<properties linkid="develop-mobile-tutorials-schedule-backend-tasks" urlDisplayName="Schedule Backend Tasks" pageTitle="Schedule Backend Tasks with Scheduler - Mobile Services" metaKeywords="" description="Use the Azure Mobile Services Scheduler to schedule jobs for your mobile app." metaCanonical="" services="" documentationCenter="Mobile" title="Schedule recurring jobs in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# 모바일 서비스에서 되풀이 작업 예약

<div class="dev-center-tutorial-subselector">

[.NET 백 엔드][.NET 백 엔드] | [JavaScript 백 엔드][JavaScript 백 엔드]

</div>

이 항목에서는 관리 포털의 작업 스케줄러 기능을 사용하여 정의한 일정에 따라 실행되는 서버 스크립트 코드를 정의하는 방법을 보여 줍니다. 이 경우 스크립트는 원격 서비스(이 예에서는 Twitter)를 주기적으로 확인하여 결과를 새 테이블에 저장합니다. 예약할 수 있는 다른 정기 작업에는 다음이 포함됩니다.

-   오래되었거나 중복된 데이터 레코드 보관
-   트윗, RSS 항목, 위치 정보 등의 외부 데이터 요청 및 저장
-   저장된 이미지 처리 또는 크기 조정

<!-- // Removed because this shortcode b/c it's old and doesn't use the new Twitter v1.1. APIs >[AZURE.VIDEO Windows-Store-app-Getting-Started-with-the-Windows-Azure-Mobile-Services-Scheduler] --> <!-- // Original video HTML code for reference. <div class="dev-onpage-video-clear clearfix"> <div class="dev-onpage-left-content"> <p> </div> <div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-the-Windows-Azure-Mobile-Services-Scheduler" target="_blank" class="label">watch the tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-with-scheduler-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-the-Windows-Azure-Mobile-Services-Scheduler" target="_blank" class="dev-onpage-video"><span class="icon">Play Video</span></a> <span class="time">5:22</span></div> </div>-->

이 자습서에서는 Twitter에 트윗 데이터를 요청하고 이 트윗을 새 Updates 테이블에 저장하는 예약된 작업을 작업 스케줄러를 사용하여 만드는 단계를 안내합니다.

-   [Twitter 액세스 및 저장 자격 증명 등록][Twitter 액세스 및 저장 자격 증명 등록]
-   [새 Updates 테이블 만들기][새 Updates 테이블 만들기]
-   [새 예약된 작업 만들기][새 예약된 작업 만들기]

## <a name="get-oauth-credentials"></a>Twitter v1.1 API 액세스 및 저장 자격 증명 등록

[WACOM.INCLUDE [mobile-services-register-twitter-access][mobile-services-register-twitter-access]]

## <a name="create-table"></a>새 Updates 테이블 만들기

다음으로, 트윗을 저장할 새 테이블을 만들어야 합니다.

1.  관리 포털에서 모바일 서비스의 **데이터** 탭을 클릭한 후 **+만들기**를 클릭합니다.

    ![][]

    **Create new table** 대화 상자가 표시됩니다.

2.  **테이블 이름**에 *Updates*를 입력한 후 확인 단추를 클릭합니다.

    ![][1]

    **Updates**라는 새 저장소 테이블이 만들어집니다.

## <a name="add-job"></a>새 예약된 작업 만들기

이제 Twitter에 액세스하고 새 Updates 테이블에 트윗 데이터를 저장하는 예약된 작업을 만들 수 있습니다.

1.  **스케줄러** 탭을 클릭한 후 **+만들기**를 클릭합니다.

    ![][2]

    > [WACOM.NOTE]*무료* 계층에서 모바일 서비스를 실행하는 경우 한 번에 하나의 예약된 작업만 실행할 수 있습니다. 유료 계층에서는 한 번에 최대 10개의 예약된 작업을 실행할 수 있습니다.

2.  스케줄러 대화 상자에서 **작업 이름**으로 *getUpdates*를 입력하고 일정 간격 및 단위를 설정한 후 확인 단추를 클릭합니다.

    ![][3]

    이를 통해 **getUpdates**라는 새 작업이 만들어집니다.

3.  방금 만든 새 작업을 클릭한 후 **스크립트** 탭을 클릭합니다.

    ![][4]

4.  자리 표시자 함수 **getUpdates**를 다음 코드로 바꿉니다.

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

    이 스크립트에서는 해시태그 `#mobileservices`를 포함한 최근 트윗을 요청하기 위해 저장된 자격 증명을 사용하여 Twitter 쿼리 API를 호출합니다. 중복 트윗 및 회신은 테이블에 저장되기 전에 결과에서 제거됩니다.

    > [WACOM.NOTE]이 샘플에서는 각 예약 실행 도중에 몇 개의 행만 테이블에 삽입된다고 가정합니다. 반복해서 많은 행이 삽입되는 경우에는 무료 계층에서 실행할 때 연결이 끊어질 수 있습니다. 이런 경우 일괄 처리로 삽입해야 합니다. 자세한 내용은 [방법: 대량 삽입 수행][방법: 대량 삽입 수행]

5.  **Run Once**를 클릭하여 스크립트를 테스트합니다.

    ![][5]

    그러면 작업이 저장되고 스케줄러에서 사용할 수 없는 상태로 유지되면서 실행됩니다.

6.  뒤로 단추를 클릭하고 **데이터**를 클릭한 다음 **업데이트** 테이블을 클릭하고 **찾아보기**를 클릭하여 Twitter 데이터가 테이블에 삽입되었는지 확인합니다.

    ![][6]

7.  뒤로 단추를 클릭하고 **스케줄러**를 클릭한 후 **getUpdates**를 선택하고 **Enable**을 클릭합니다.

    ![][7]

    이를 통해 지정된 일정에서 작업을 실행할 수 있게 되며, 이 경우에는 매 시간마다 실행하게 됩니다.

이제 모바일 서비스에서 새로운 예약된 작업을 만들었습니다. 이 작업은 사용하지 않도록 설정하거나 수정할 때까지는 예약된 대로 실행됩니다.

## <a name="nextsteps"> </a>다음 단계

-   [모바일 서비스 서버 스크립트 참조][모바일 서비스 서버 스크립트 참조]
    서버 스크립트의 등록 및 사용에 대해 자세히 알아보십시오.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [.NET 백 엔드]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks/ ".NET 백 엔드"
  [JavaScript 백 엔드]: /ko-kr/documentation/articles/mobile-services-schedule-recurring-tasks/ "JavaScript 백 엔드"
  [Twitter 액세스 및 저장 자격 증명 등록]: #get-oauth-credentials
  [새 Updates 테이블 만들기]: #create-table
  [새 예약된 작업 만들기]: #add-job
  [mobile-services-register-twitter-access]: ../includes/mobile-services-register-twitter-access.md
  []: ./media/mobile-services-schedule-recurring-tasks/mobile-data-tab-empty-cli.png
  [1]: ./media/mobile-services-schedule-recurring-tasks/mobile-create-updates-table.png
  [2]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-new-job-cli.png
  [3]: ./media/mobile-services-schedule-recurring-tasks/mobile-create-job-dialog.png
  [4]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-script-new.png
  [방법: 대량 삽입 수행]: /ko-kr/develop/mobile/how-to-guides/work-with-server-scripts/#bulk-inserts
  [5]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-script.png
  [6]: ./media/mobile-services-schedule-recurring-tasks/mobile-browse-updates-table.png
  [7]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-enabled.png
  [모바일 서비스 서버 스크립트 참조]: http://go.microsoft.com/fwlink/?LinkId=262293
