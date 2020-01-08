---
title: Apache Hive로 Twitter 데이터 분석 - Azure HDInsight
description: HDInsight에서 Apache Hive 및 Apache Hadoop을 사용하여 원시 TWitter 데이터를 검색 가능한 Hive 테이블로 변환하는 방법을 알아 봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 12/16/2019
ms.openlocfilehash: f3705170be28f33e5994bd00e363dc7ec7f94642
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435609"
---
# <a name="analyze-twitter-data-using-apache-hive-and-apache-hadoop-on-hdinsight"></a>HDInsight에서 Apache Hive 및 Apache Hadoop을 사용하여 Twitter 데이터 분석

[Apache Hive](https://hive.apache.org/)를 사용하여 Twitter 데이터를 처리하는 방법을 알아봅니다. 결과는 특정 단어가 포함된 많은 트윗을 보낸 Twitter 사용자의 목록이 됩니다.

> [!IMPORTANT]  
> 이 문서의 단계는 HDInsight 3.6에서 테스트했습니다.

## <a name="get-the-data"></a>데이터 가져오기

Twitter를 사용하여 각 트윗에 대한 데이터를 REST API를 통해 JSON(JavaScript Notation) 문서로서 검색할 수 있습니다. [OAuth](https://oauth.net) 는 API에 대한 인증을 필요로 합니다.

### <a name="create-a-twitter-application"></a>Twitter 애플리케이션 만들기

1. 웹 브라우저에서 [https://developer.twitter.com/apps/](https://developer.twitter.com/apps/)에 로그인합니다. Twitter 계정이 없는 경우 **지금 등록** 링크를 선택 합니다.

2. **새 앱 만들기**를 선택합니다.

3. **Name**, **Description**, **Website**를 입력합니다. **Website** 필드의 URL을 구성할 수 있습니다. 다음 표는 사용할 샘플 값을 보여 줍니다.

   | 필드 | 값 |
   |--- |--- |
   | 이름 |MyHDInsightApp |
   | Description |MyHDInsightApp |
   | 웹 사이트 |`https://www.myhdinsightapp.com` |

4. **예, 동의 함**을 선택한 다음 **Twitter 응용 프로그램 만들기**를 선택 합니다.

5. **사용 권한** 탭을 선택 합니다. 기본 권한은 **읽기 전용**입니다.

6. **Keys and Access Tokens** 탭을 선택합니다.

7. **내 액세스 토큰 만들기**를 선택 합니다.

8. 페이지의 오른쪽 위 모서리에서 **테스트 OAuth** 를 선택 합니다.

9. **consumer key**, **Consumer secret**, **Access token** 및 **Access token secret**을 기록해 둡니다.

### <a name="download-tweets"></a>트윗 다운로드

다음 Python 코드는 Twitter에서 10,000개의 트윗을 다운로드하고 **tweets.txt**라는 파일에 저장합니다.

> [!NOTE]  
> 다음 단계는 Python이 이미 설치되어 있으므로 HDInsight 클러스터에서 수행됩니다.

1. [Ssh 명령을](./hdinsight-hadoop-linux-use-ssh-unix.md) 사용 하 여 클러스터에 연결 합니다. CLUSTERNAME을 클러스터의 이름으로 바꿔서 아래 명령을 편집 하 고 명령을 입력 합니다.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 다음 명령을 사용 하 여 [Tweepy](https://www.tweepy.org/), [진행률 표시줄](https://pypi.python.org/pypi/progressbar/2.2)및 기타 필수 패키지를 설치 합니다.

   ```bash
   sudo apt install python-dev libffi-dev libssl-dev
   sudo apt remove python-openssl
   python -m pip install virtualenv
   mkdir gettweets
   cd gettweets
   virtualenv gettweets
   source gettweets/bin/activate
   pip install tweepy progressbar pyOpenSSL requests[security]
   ```

1. 다음 명령을 사용하여 **gettweets.py**라는 파일을 만듭니다.

   ```bash
   nano gettweets.py
   ```

1. `Your consumer secret`, `Your consumer key`, `Your access token`및 `Your access token secret`를 twitter 응용 프로그램의 관련 정보로 바꿔서 아래 코드를 편집 합니다. 그런 다음 편집 된 코드를 **gettweets.py** 파일의 내용으로 붙여넣습니다.

   ```python
   #!/usr/bin/python

   from tweepy import Stream, OAuthHandler
   from tweepy.streaming import StreamListener
   from progressbar import ProgressBar, Percentage, Bar
   import json
   import sys

   #Twitter app information
   consumer_secret='Your consumer secret'
   consumer_key='Your consumer key'
   access_token='Your access token'
   access_token_secret='Your access token secret'

   #The number of tweets we want to get
   max_tweets=100

   #Create the listener class that receives and saves tweets
   class listener(StreamListener):
       #On init, set the counter to zero and create a progress bar
       def __init__(self, api=None):
           self.num_tweets = 0
           self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

       #When data is received, do this
       def on_data(self, data):
           #Append the tweet to the 'tweets.txt' file
           with open('tweets.txt', 'a') as tweet_file:
               tweet_file.write(data)
               #Increment the number of tweets
               self.num_tweets += 1
               #Check to see if we have hit max_tweets and exit if so
               if self.num_tweets >= max_tweets:
                   self.pbar.finish()
                   sys.exit(0)
               else:
                   #increment the progress bar
                   self.pbar.update(self.num_tweets)
           return True

       #Handle any errors that may occur
       def on_error(self, status):
           print status

   #Get the OAuth token
   auth = OAuthHandler(consumer_key, consumer_secret)
   auth.set_access_token(access_token, access_token_secret)
   #Use the listener class for stream processing
   twitterStream = Stream(auth, listener())
   #Filter for these topics
   twitterStream.filter(track=["azure","cloud","hdinsight"])
   ```

    > [!TIP]  
    > 마지막 줄에서 토픽 필터를 조정하여 인기 키워드를 추적합니다. 스크립트를 실행할 때 인기 키워드를 사용하면 데이터를 더 빨리 캡처할 수 있습니다.

1. **Ctrl + X**, **Y**를 차례로 사용하여 파일을 저장합니다.

1. 다음 명령을 사용하여 파일을 실행하고 트윗을 다운로드합니다.

    ```bash
    python gettweets.py
    ```

    진행률 표시기가 나타납니다. 진행률 표시기는 트윗이 다운로드되면서 100%까지 올라갑니다.

   > [!NOTE]  
   > 진행률 표시줄이 앞으로 이동하는 데 시간이 오래 걸리는 경우 추세 항목을 추적하는 필터를 변경해야 합니다. 필터에 항목에 대 한 많은 트 윗 있는 경우 필요한 100 트 윗를 신속 하 게 얻을 수 있습니다.

### <a name="upload-the-data"></a>데이터 업로드

HDInsight 스토리지로 데이터를 복사하려면 다음 명령을 사용합니다.

```bash
hdfs dfs -mkdir -p /tutorials/twitter/data
hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt
```

이러한 명령은 클러스터의 모든 노드에서 액세스할 수 있는 위치에 데이터를 저장합니다.

## <a name="run-the-hiveql-job"></a>HiveQL 작업 실행

1. 다음 명령을 사용하여 [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) 문이 포함된 파일을 만듭니다.

   ```bash
   nano twitter.hql
   ```

    다음 텍스트를 파일의 내용으로 사용합니다.

   ```hiveql
   set hive.exec.dynamic.partition = true;
   set hive.exec.dynamic.partition.mode = nonstrict;
   -- Drop table, if it exists
   DROP TABLE tweets_raw;
   -- Create it, pointing toward the tweets logged from Twitter
   CREATE EXTERNAL TABLE tweets_raw (
       json_response STRING
   )
   STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
   -- Drop and recreate the destination table
   DROP TABLE tweets;
   CREATE TABLE tweets
   (
       id BIGINT,
       created_at STRING,
       created_at_date STRING,
       created_at_year STRING,
       created_at_month STRING,
       created_at_day STRING,
       created_at_time STRING,
       in_reply_to_user_id_str STRING,
       text STRING,
       contributors STRING,
       retweeted STRING,
       truncated STRING,
       coordinates STRING,
       source STRING,
       retweet_count INT,
       url STRING,
       hashtags array<STRING>,
       user_mentions array<STRING>,
       first_hashtag STRING,
       first_user_mention STRING,
       screen_name STRING,
       name STRING,
       followers_count INT,
       listed_count INT,
       friends_count INT,
       lang STRING,
       user_location STRING,
       time_zone STRING,
       profile_image_url STRING,
       json_response STRING
   );
   -- Select tweets from the imported data, parse the JSON,
   -- and insert into the tweets table
   FROM tweets_raw
   INSERT OVERWRITE TABLE tweets
   SELECT
       cast(get_json_object(json_response, '$.id_str') as BIGINT),
       get_json_object(json_response, '$.created_at'),
       concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
       substr (get_json_object(json_response, '$.created_at'),27,4)),
       substr (get_json_object(json_response, '$.created_at'),27,4),
       case substr (get_json_object(json_response,    '$.created_at'),5,3)
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
       cast (get_json_object(json_response, '$.retweet_count') as INT),
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
       cast (get_json_object(json_response, '$.user.followers_count') as INT),
       cast (get_json_object(json_response, '$.user.listed_count') as INT),
       cast (get_json_object(json_response, '$.user.friends_count') as INT),
       get_json_object(json_response, '$.user.lang'),
       get_json_object(json_response, '$.user.location'),
       get_json_object(json_response, '$.user.time_zone'),
       get_json_object(json_response, '$.user.profile_image_url'),
       json_response
   WHERE (length(json_response) > 500);
   ```

1. **Ctrl + X**, **Y**를 차례로 누르고 파일을 저장합니다.

1. 다음 명령을 사용하여 파일에 포함된 HiveQL을 실행합니다.

   ```bash
   beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i twitter.hql
   ```

    이 명령은 **twitter.hql** 파일을 실행합니다. 쿼리가 완료되면 `jdbc:hive2//localhost:10001/>` 프롬프트가 표시됩니다.

1. Beeline 프롬프트에서 다음 쿼리를 사용하여 데이터를 가져왔는지 확인합니다.

   ```hiveql
   SELECT name, screen_name, count(1) as cc
   FROM tweets
   WHERE text like "%Azure%"
   GROUP BY name,screen_name
   ORDER BY cc DESC LIMIT 10;
   ```

    이 쿼리는 메시지 텍스트에 단어 **Azure**를 포함하는 최대 10개의 트윗을 반환합니다.

    > [!NOTE]  
    > `gettweets.py` 스크립트에서 필터를 변경한 경우 **Azure**를 사용한 필터 중 하나로 대체합니다.

## <a name="next-steps"></a>다음 단계

비구조적 JSON 데이터 집합을 구조적 [Apache Hive](https://hive.apache.org/) 테이블로 변환 하는 방법을 배웠습니다. HDInsight에서 Hive에 대한 자세한 내용은 다음 문서를 참조하세요.

* [HDInsight 시작](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [HDInsight를 사용하여 비행 지연 데이터 분석](/azure/hdinsight/interactive-query/interactive-query-tutorial-analyze-flight-data)
