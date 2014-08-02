<properties linkid="manage-services-hdinsight-howto-social-data" urlDisplayName="Analyze Data with HDInsight" pageTitle="Analyzing Twitter data with Hive - Windows Azure Services" metaKeywords="" description="In this tutorial you will query, explore, and analyze data from Twitter using the HDInsight Service for Windows Azure and a complex Hive example." metaCanonical="" services="" documentationCenter="" title="Analyzing Twitter Data with Hive" authors="" solutions="" writer="wenming" manager="" editor="" />

Hive로 Twitter 데이터 분석
==========================

이 자습서에서는 Azure용 Apache Hadoop 기반 HDInsight Service 및 복잡한 Hive 예제를 사용하여 twitter의 데이터를 쿼리하고 탐색하고 분석합니다. 소셜 웹 사이트는 빅데이터 채택의 주요 추진력 중 하나입니다. Twitter와 같은 사이트에서 제공하는 공개 API는 대중적인 추세를 분석하고 이해하는 데 유용한 데이터 원본입니다. 이 자습서에서는 [Azure 포털](http://www.windowsazure.com)을 통해 HDInsight 클러스터를 이미 만들었다고 가정합니다.

이 자습서는 다음과 같이 구성되어 있습니다.

1.  [cURL 및 Twitter 스트리밍 API를 사용하여 Twitter 피드 가져오기](#segment1)
2.  [Hive를 사용하여 대화형으로 Twitter 데이터 처리](#segment2)

## Get Twitter Feed using cURL and Twitter Streaming API

1.  이 단계에서는 curl.exe가 필요합니다. <http://curl.haxx.se/download.html>에서 사용 중인 OS에 해당하는 curl 파일(예: Win64 이진 SSL)을 다운로드합니다.

    ![그림 3](./media/hdinsight-analyze-twitter-date-hive/Figure3.png)

2.  적절한 위치(예: **C:\\twitterdata**)에 **curl.exe**의 압축을 풉니다.

3.  다음과 같이 메모장을 사용하여 **curl.exe**와 동일한 폴더에 **get\_twitter\_stream.cmd** 및 **twitter\_params.txt**를 만듭니다.

    ![그림 4](./media/hdinsight-analyze-twitter-date-hive/Figure4.png)

4.  다음과 같이 트윗을 추적하도록 **twitter\_params.txt** 파일을 편집합니다.

         track=weather,Azure,WindowsAzure,cloud

    원하는 항목 또는 해시 태그를 사용할 수 있습니다.

5.  **get\_twitter\_stream.cmd** 창 명령 스크립트를 편집하여 다음 줄에서 **USER** 대신 twitter 사용자 이름을, **PASSWORD** 대신 twitter 암호를 추가합니다.

         curl -d @twitter_parameters.txt -k https://stream.twitter.com/1/statuses/filter.json -u user:{password} >>twitter_stream_seq2.txt

6.  다음과 같이 get\_twitter\_stream.cmd 스크립트를 명령 프롬프트에서 실행합니다.

    ![그림 5](./media/hdinsight-analyze-twitter-date-hive/Figure5.png)

    다음과 유사한 결과가 표시됩니다.

    ![그림 6](./media/hdinsight-analyze-twitter-date-hive/Figure6.png)

7.  **Ctrl+C**를 눌러 작업을 중지할 수 있습니다. 그런 다음 파일의 이름을 바꾼 후 스크립트를 다시 시작할 수 있습니다. 10분에서 몇 시간까지 프로세스를 실행하도록 둘 수 있습니다. 이 자습서에서는 데이터 크기를 몇백 메가바이트로 제한하십시오.

    Twitter 데이터는 복잡한 중첩 구조를 포함하는 JSon 형식으로 저장됩니다. 다음 단계에서는 기존의 프로그래밍 언어를 사용하여 여러 줄의 코드를 작성하는 대신, 이 중첩 구조를 Hive 테이블로 변환하여 HQL이라고 하는 SQL 같은 언어에서 대화형으로 쿼리할 수 있습니다.

## Process Twitter data interactively using Hive

1.  트윗을 수집하는 동안 Azure 포털을 통해 만든 HDInsight 클러스터에 대한 RDP 세션을 만듭니다.

    HDInsight로 **이동**하여, 이전에 만든 클러스터를 **선택**합니다. 화면 맨 아래에 있는 **RDP 연결** 아이콘을 클릭합니다. 암호를 입력하여 RDP 세션에 로그인합니다. 로그인한 후에는 탐색기 창을 **열어** c: 드라이브로 이동합니다.

    ![RDP 아이콘을 사용하여 연결](./media/hdinsight-analyze-twitter-date-hive/twitter-RDPconnect.PNG)

2.  **Ctrl+C**를 눌러 Curl 트윗 수집 프로세스를 종료하고, Windows 탐색기를 사용하여 twitter 파일이 있는 위치로 이동합니다. **마우스 오른쪽 단추를 클릭**하여 압축(ZIP) 폴더로 보냅니다. 그렇게 하면 업로드 시간이 줄어듭니다.

3.  이제, 로컬 컴퓨터의 Zip 파일에서 **Ctrl-C**를 눌러 복사한 후 원격 데스크톱 세션의 C:에서 c:\\로 이동합니다. 탐색기 창(C:)에서 클릭한 후 **Ctrl-V**를 눌러 RDP 세션을 통해 Zip 파일을 업로드합니다.

    ![RDP 세션을 통해 트윗 업로드](./media/hdinsight-analyze-twitter-date-hive/twitter-uploadingzip.PNG)

4.  파일을 업로드한 후 **마우스 오른쪽 단추로 클릭**하고, c:\\에 압축 풀기를 **선택**하여 원래 텍스트 파일을 다시 가져옵니다. Hadoop 명령줄 창을 **열고** HIVE 및 Hadoop 명령으로 작업을 시작합니다.

    ![그림26](./media/hdinsight-analyze-twitter-date-hive/Figure26.png)

5.  첫 단계로, C:\\를 **입력**하고 **Enter 키를 누릅니다**. 그러면 twitter 데이터가 있는 c:\\ 폴더로 이동됩니다.

6.  그다음에는 다음과 같이 HDFS(Hadoop Distributed File System)에 폴더를 만든 후 -copyFromLocal 스위치를 사용하여 방금 만든 폴더로 twitter 데이터를 복사합니다.

         hadoop fs -mkdir /example/data
         hadoop fs -copyFromLocal c:\twitter_stream_seq2.txt /example/data/

7.  이제 원시 twitter 데이터가 HDInsight 클러스터의 HDFS로 복사되었습니다. 다음 단계로 로드한 데이터의 간단한 테이블 구조를 만듭니다. 이 임시 Hive는 데이터를 저장하고 추가 ETL 처리를 수행할 수 있는 테이블을 구조화합니다. 메모장을 실행하여 다음 코드를 메모장에 붙여넣은 후 "c:\\load\_twitter\_raw.hql"로 저장합니다.

         drop table twitter_raw;

         create table twitter_raw (
             json_response string
         ) 
         partitioned by (filesequence int);

         load data inpath '/example/data/twitter_stream_seq2.txt'
         into table twitter_raw
         partition (filesequence = 1);

8.  파일을 저장한 후에는 다음과 같이 명령 창에서 Hive를 실행합니다.

         C:\apps\dist\hive-0.9.0\bin\hive -f load_twitter_raw.hql

9.  프로세스가 완료되는 데 몇 초 정도 걸립니다.

    ![Twitter 원시 결과 로드](./media/hdinsight-analyze-twitter-date-hive/twitter-load-raw-results.PNG)

10. 이제 중첩 JSon 형식의 대량 twitter 데이터 집합이 임시 Hive 테이블 구조로 변환되었습니다.

11. Hive를 사용하여 twitter 데이터 집합에 대해 쿼리하려면 먼저, 다른 ETL 프로세스를 실행해야 합니다. "twitter\_raw" 테이블에 저장한 데이터에 대해 더욱 자세한 테이블 스키마를 정의합니다. 더욱 복잡한 이 ETL 프로세스는 시간이 더 걸립니다. 명령 프롬프트에서 메모장을 다시 실행하여 다음 Hive 쿼리 코드를 붙여넣은 후 "c:\\twitter\_etl.hql.txt"로 저장합니다.

        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode=nonstrict;

        drop table twitter_temp;

        create table twitter_temp
        (
            id bigint,
            created_at string,
            created_at_date string,
            created_at_year string,
            created_at_month string,
            created_at_day string,
            created_at_time string,
            in_reply_to_user_id_str string,
            text string,
            contributors string,
            retweeted string,
            truncated string,
            coordinates string,
            source string,
            retweet_count int,
            url string,
            hashtags array<string>,
            user_mentions array<string>,
            first_hashtag string,
            first_user_mention string,
            screen_name string,
            name string,
            followers_count int,
            listed_count int,
            friends_count int,
            lang string,
            user_location string,
            time_zone string,
            profile_image_url string,
            json_response string
        )
        partitioned by (filesequence int);

        from twitter_raw
        insert overwrite table twitter_temp
        partition (filesequence)
        select
            cast(get_json_object(json_response, '$.id_str') as bigint),

            get_json_object(json_response, '$.created_at'),
            concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
                substr (get_json_object(json_response, '$.created_at'),27,4)),

            substr (get_json_object(json_response, '$.created_at'),27,4),

            case substr (get_json_object(json_response,     '$.created_at'),5,3)
                when "Jan" then "01"
                when "Feb" then "02"
                when "Mar" then "03"
                when "Apr" then "04"
                when "May" then "05"
                when "Jun" then "06"
                when "Jul" then "07"
                when "Aug" then "08"
                when "Sep" then "09"
                when "Oct" then "10"
                when "Nov" then "11"
                when "Dec" then "12" end,

            substr (get_json_object(json_response, '$.created_at'),9,2),

            substr (get_json_object(json_response, '$.created_at'),12,8),

            get_json_object(json_response, '$.in_reply_to_user_id_str'),
            get_json_object(json_response, '$.text'),
            get_json_object(json_response, '$.contributors'),
            get_json_object(json_response, '$.retweeted'),
            get_json_object(json_response, '$.truncated'),
            get_json_object(json_response, '$.coordinates'),
            get_json_object(json_response, '$.source'),
            cast (get_json_object(json_response, '$.retweet_count') as int),
            get_json_object(json_response, '$.entities.display_url'),
            array(  
                trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
            array(
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
            get_json_object(json_response, '$.user.screen_name'),
            get_json_object(json_response, '$.user.name'),
            cast (get_json_object(json_response, '$.user.followers_count') as int),
            cast (get_json_object(json_response, '$.user.listed_count') as int),
            cast (get_json_object(json_response, '$.user.friends_count') as int),
            get_json_object(json_response, '$.user.lang'),
            get_json_object(json_response, '$.user.location'),
            get_json_object(json_response, '$.user.time_zone'),
            get_json_object(json_response, '$.user.profile_image_url'),
            json_response,
            filesequence
        where (length(json_response) > 500);

12. 다음을 입력합니다.

        C:\apps\dist\hive-0.9.0\bin\hive -f twitter_etl.hql.txt

13. 이 복잡한 Hive 스크립트는 Hadoop 클러스터로 긴 Map Reduce 작업 집합을 시작합니다. 데이터 집합 및 클러스터의 크기에 따라 이 작업은 약 10분 정도 걸릴 수 있습니다.

    ![twitter의 복잡한 ETL](./media/hdinsight-analyze-twitter-date-hive/twitter-etl-complex.PNG)

14. 또한 바탕 화면에서 "Hadoop MapReduce Status" 바로 가기를 두 번 클릭하여 헤드 노드의 작업 추적 페이지를 통해 작업의 진행률을 모니터링할 수도 있습니다.

    ![작업 모니터링 및 추적](./media/hdinsight-analyze-twitter-date-hive/twitter_longjob_browser.PNG)

15. ETL 프로세스가 완료되면 다음과 같이 Hive를 실행합니다.

        C:\apps\dist\hive-0.9.0\bin\hive

16. 다음과 같은 간단한 Hive 쿼리로 실험해 볼 수 있습니다.

 		select name, screen_name, count(1) as cc from twitter_temp where text like "%Azure%" group by name,screen_name order by cc desc limit 10;

1.  이 쿼리는 "Azure"라는 단어가 포함된 트윗을 가장 많이 보낸 사용자 목록을 검색하여 반환합니다.

    ![최종 결과](./media/hdinsight-analyze-twitter-date-hive/twitter_final_query_result.PNG)

## Summary

이 자습서에서는 비구조적 Json 데이터 집합을 구조적 Hive 테이블로 변환해서 Azure의 HDInsight를 사용하여 Twitter 데이터를 쿼리하고 탐색하고 분석하는 방법에 대해 살펴보았습니다. 파일의 업데이트 및 지원에 대해서는 GitHub 리포지토리([여기](https://github.com/wenming/BigDataSamples/tree/master/twittersample))에서 찾아볼 수 있습니다.

다음 단계
---------

이 문서에서는 Hadoop 명령줄을 사용하는 예를 보여 주지만 HDInsight 서비스 대화형 콘솔을 사용하여 작업을 수행할 수도 있습니다. 자세한 내용은 [지침: HDInsight 대화형 JavaScript 및 Hive 콘솔][대화형 콘솔]을 참조하십시오.

