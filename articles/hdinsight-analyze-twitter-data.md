<properties urlDisplayName="Analyze Twitter data with HDInsight Hadoop" pageTitle="HDInsight의 Hadoop에서 Twitter 데이터 분석 | Azure" metaKeywords="" description="Hive를 사용하여 HDInsight의 Hadoop에서 Twitter 데이터를 분석해 특정 단어의 사용 빈도를 확인하는 방법에 대해 알아봅니다." metaCanonical="" services="HDInsight" documentationCenter="" title="Analyze Twitter data with Hadoop in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/08/2014" ms.author="jgao" />

# HDInsight의 Hadoop에서 Twitter 데이터 분석

HDInsight에서 Hive를 사용하여 Hadoop으로 Twitter 데이터를 분석하는 방법을 알아봅니다.

소셜 웹 사이트는 빅 데이터 채택의 주요 추진력 중 하나입니다. Twitter와 같은 사이트에서 제공하는 공개 API는 대중적인 추세를 분석하고 이해하는 데 유용한 데이터 원본입니다. 이 자습서에서는 Twitter 웹 서비스에 연결하여 Twitter 스트리밍 API를 사용해 일부 트윗을 가져옵니다. 그런 다음 Hive를 사용하여 특정 단어가 포함된 트윗을 가장 많이 보낸 Twitter 사용자의 목록을 가져옵니다.

**예상 완료 시간:** 30분

##이 문서에서는 다음을 수행합니다.

- [필수 조건](#prerequisites)
- [Twitter 피드 가져오기](#feed)
- [HiveQL 스크립트 만들기](#script)
- [Hive를 사용하여 데이터 처리](#process)
- [자습서 정리](#cleanup)
- [다음 단계](#nextsteps)

##<a id="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에 다음이 있어야 합니다.

- Azure PowerShell이 설치 및 구성된 **워크스테이션**. 자세한 내용은 [Azure PowerShell 설치 및 구성][powershell-install]을 참조하세요. PowerShell 스크립트를 실행하려면 관리자로 Azure PowerShell을 실행하고 실행 정책을 *RemoteSigned*로 설정해야 합니다. [Windows PowerShell 스크립트 실행][powershell-script](영문)을 참조하세요.

	PowerShell 스크립트를 실행하기 전에 cmdlet을 사용하여 Azure 구독에 연결되어 있는지 확인합니다.

		Add-AzureAccount

	여러 Azure 구독이 있는 경우 다음 cmdlet을 사용하여 현재 구독을 설정합니다.

		Select-AzureSubscription <AzureSubscriptionName>



- **Azure HDInsight 클러스터**. 클러스터 프로비전에 대한 자세한 내용은 [HDInsight 사용 시작][hdinsight-get-started] 또는 [HDInsight 클러스터 프로비전][hdinsight-provision]을 참조하세요. 자습서를 완료하려면 다음 데이터가 필요합니다.

	<table border="1">
	<tr><th>클러스터 속성</th><th>PowerShell 변수 이름</th><th>값</th><th>설명</th></tr>
	<tr><td>HDInsight 클러스터 이름</td><td>$clusterName</td><td></td><td>HDInsight 클러스터 이름입니다.</td></tr>
	<tr><td>Azure 저장소 계정 이름</td><td>$storageAccountName</td><td></td><td>Azure 저장소 계정은 HDInsight 클러스터에서 사용할 수 있습니다. 이 자습서의 경우 클러스터 프로비전 프로세스 도중에 지정된 기본 저장소 계정을 사용합니다.</td></tr>
	<tr><td>Azure Blob 컨테이너 이름</td><td>$containerName</td><td></td><td>이 예에서는 기본 HDInsight 클러스터 파일 시스템에 사용되는 Azure Blob 저장소 컨테이너를 사용합니다. 기본적으로 컨테이너 이름은 HDInsight 클러스터 이름과 동일합니다.</td></tr>
	</table>

**HDInsight 저장소 이해**

HDInsight는 데이터 저장소로 Azure Blob 저장소를 사용합니다.  이를 *WASB* 또는 *Azure 저장소 - Blob*이라고 합니다. WASB는 Azure Blob 저장소에 구현한 Microsoft의 HDFS입니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용][hdinsight-storage]을 참조하세요. 

HDInsight 클러스터를 프로비전하면 HDFS의 경우처럼 Blob 저장소 컨테이너가 기본 파일로 지정됩니다. 프로비전 프로세스 중에 이 컨테이너 외에도 동일한 Azure 저장소 계정 또는 다른 Azure 저장소 계정에서 컨테이너를 추가할 수 있습니다. 저장소 계정 추가에 대한 지침은 [HDInsight 클러스터 프로비전][hdinsight-provision]을 참조하세요. 

> [WACOM.NOTE] 이 자습서에서 사용되는 PowerShell 스크립트를 간소화하기 위해 모든 파일이 */tutorials/twitter*r에 위치한 기본 파일 시스템 컨테이너에 저장됩니다. 기본적으로 이 컨테이너 이름은 HDInsight 클러스터 이름과 동일합니다. 다른 컨테이너를 사용하여 이러한 파일을 저장하는 경우에는 그에 따라 스크립트를 업데이트하세요.

WASB 구문은 다음과 같습니다.

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] HDInsight 클러스터 버전 3.0에서는 *wasb://* 구문만 지원됩니다. 이전 *asv://* 구문은 HDInsight 2.1 및 1.6 클러스터에서 지원되지만, HDInsight 3.0 클러스터에서는 지원되지 않으며 이후 버전에서도 지원되지 않습니다.

> WASB 경로는 가상 경로입니다.  자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용][hdinsight-storage]을 참조하세요. 

기본 파일 시스템 컨테이너에 저장된 파일의 경우 HDInsight에서 다음 URI를 사용하여 이 파일에 액세스할 수 있습니다(tweets.txt를 예로 사용함).

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/twitter/tweets.txt
	wasb:///tutorials/twitter/tweets.txt
	/tutorials/twitter/tweets.txt

저장소 계정에서 바로 파일에 액세스하려는 경우 파일의 Blob 이름은 다음과 같습니다.

	tutorials/twitter/tweets.txt

다음 표는 이 자습서에 사용된 파일을 보여 줍니다.

<table border="1">
<tr><th>파일</th><th>설명</th></tr>
<tr><td>/tutorials/twitter/data/tweets.txt</td><td>Hive 작업의 원본 데이터입니다.</td></tr>
<tr><td>/tutorials/twitter/output</td><td>Hive 작업의 출력 폴더입니다. 기본 파일 Hive 작업 출력 파일 이름은 <strong>000000_0</strong>입니다. </td></tr>
<tr><td>tutorials/twitter/twitter.hql</td><td>HiveQL 스크립트 파일입니다.</td></tr>
<tr><td>/tutorials/twitter/jobstatus</td><td>Hadoop 작업 상태입니다.</td></tr>
</table>

##<a id="feed"></a>Twitter 피드 가져오기

이 자습서에는 [Twitter 스트리밍 API][twitter-streaming-api](영문)를 사용합니다. 사용할 특정 Twitter 스트리밍 API는 [상태/필터][twitter-statuses-filter](영문)입니다.

[트윗 데이터](https://dev.twitter.com/docs/platform-objects/tweets) (영문)는 복잡한 중첩 구조를 포함하는 JSon 형식으로 저장됩니다. 기존의 프로그래밍 언어를 사용하여 여러 줄의 코드를 작성하는 대신, 이 중첩 구조를 Hive 테이블로 변환하여 HiveQL이라고 하는 SQL 같은 언어로 쿼리할 수 있습니다. 

Twitter는 OAuth를 사용하여 API에 대한 권한 있는 액세스를 제공합니다. OAuth은 사용자가 암호를 공유하지 않아도 사용자를 대신하여 응용 프로그램 작동을 승인할 수 있게 하는 인증 프로토콜입니다. 자세한 내용은 [oauth.net](http://oauth.net/) 또는 Hueniverse의 훌륭한 [OAuth 초보자 가이드](http://hueniverse.com/oauth/) (영문)에서 찾을 수 있습니다.

OAuth를 사용하는 첫 단계는 Twitter 개발자 사이트에서 새 응용 프로그램을 만드는 것입니다.

**Twitter 응용 프로그램을 만들려면**

1. [https://apps.twitter.com/](https://apps.twitter.com/)(영문)에 로그인합니다. Twitter 계정이 없는 경우 **Sign up now** 링크를 클릭합니다.
2. **Create New App**을 클릭합니다.
3. **Name**, **Description**, **Website**를 입력합니다. 웹 사이트 필드의 URL을 구성할 수 있습니다. 다음 표는 사용할 샘플 값을 보여 줍니다.

	<table border="1">
	<tr><th>필드</th><th>값</th></tr>
	<tr><td>이름</td><td>MyHDInsightApp</td></tr>
	<tr><td>설명</td><td>MyHDInsightApp</td></tr>
	<tr><td>웹 사이트</td><td>http://www.myhdinsightapp.com</td></tr>
	</table>
4. **Yes, I agree**를 선택하고 **Create your Twitter application**을 클릭합니다.
5. **Permissions** 탭을 클릭합니다. 기본 권한은 **Read only**입니다. 이 자습서에는 이 권한이면 충분합니다. 
6. **API Keys** 탭을 클릭합니다.
7. **Create my access token**을 클릭합니다.
8. 페이지의 오른쪽 위 모서리에서 **Test OAuth**를 클릭합니다.
9. **consumer key**, **Consumer secret**, **Access token** 및 **Access token secret**을 기록해 둡니다. 이 값은 자습서의 뒷부분에서 필요합니다.

이 자습서에서는 PowerShell을 사용하여 웹 서비스를 호출합니다. 웹 서비스 호출에 많이 사용되는 다른 도구는 [*Curl*][curl]입니다. Curl은 [여기][curl-download](영문)에서 다운로드할 수 있습니다.

>[WACOM.NOTE] Windows에서 curl 명령을 사용할 때는 옵션 값에 작은따옴표 대신 큰따옴표를 사용합니다.

**트윗을 가져오려면**

1. Windows PowerShell ISE를 엽니다. Windows 8 시작 화면에서 **PowerShell_ISE**를 입력한 후 **Windows PowerShell ISE**를 클릭하면 됩니다. [Windows 8 및 Windows에서 Windows PowerShell 시작][powershell-start](영문)을 참조하세요.

3. 스크립트 창에서 다음 스크립트를 복사합니다.

		Write-Host "Set variables ..." -ForegroundColor Green
		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<BlobContainerName>"

		$destBlobName = "tutorials/twitter/data/tweets.txt"
		
		$trackString = "Azure, Cloud, HDInsight"
		$track = [System.Uri]::EscapeDataString($trackString);
		$lineMax = 100  #about 3 minutes every 100 lines
		
		$oauth_consumer_key = "<TwitterAppConsumerKey>";
		$oauth_consumer_secret = "<TwitterAppConsumerSecret>";
		$oauth_token = "<TwitterAppAccessToken>";
		$oauth_token_secret = "<TwitterAppAccessTokenSecret>";
		
		Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
		
		Write-Host "Create block blob object ..." -ForegroundColor Green
		$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
		$storageClient = $storageAccount.CreateCloudBlobClient();
		$storageContainer = $storageClient.GetContainerReference($containerName)
		$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
		
		Write-Host "Define a MemoryStream and a StreamWriter for writing ..." -ForegroundColor Green
		$memStream = New-Object System.IO.MemoryStream
		$writeStream = New-Object System.IO.StreamWriter $memStream
		
		Write-Host "Format oauth strings ..." -ForegroundColor Green
		$oauth_nonce = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes([System.DateTime]::Now.Ticks.ToString()));
		$ts = [System.DateTime]::UtcNow - [System.DateTime]::ParseExact("01/01/1970", "dd/MM/yyyy", $null)
		$oauth_timestamp = [System.Convert]::ToInt64($ts.TotalSeconds).ToString();
		
		$signature = "POST&";
		$signature += [System.Uri]::EscapeDataString("https://stream.twitter.com/1.1/statuses/filter.json") + "&";
		$signature += [System.Uri]::EscapeDataString("oauth_consumer_key=" + $oauth_consumer_key + "&");
		$signature += [System.Uri]::EscapeDataString("oauth_nonce=" + $oauth_nonce + "&"); 
		$signature += [System.Uri]::EscapeDataString("oauth_signature_method=HMAC-SHA1&");
		$signature += [System.Uri]::EscapeDataString("oauth_timestamp=" + $oauth_timestamp + "&");
		$signature += [System.Uri]::EscapeDataString("oauth_token=" + $oauth_token + "&");
		$signature += [System.Uri]::EscapeDataString("oauth_version=1.0&");
		$signature += [System.Uri]::EscapeDataString("track=" + $track);
		
		$signature_key = [System.Uri]::EscapeDataString($oauth_consumer_secret) + "&" + [System.Uri]::EscapeDataString($oauth_token_secret);
		
		$hmacsha1 = new-object System.Security.Cryptography.HMACSHA1;
		$hmacsha1.Key = [System.Text.Encoding]::ASCII.GetBytes($signature_key);
		$oauth_signature = [System.Convert]::ToBase64String($hmacsha1.ComputeHash([System.Text.Encoding]::ASCII.GetBytes($signature)));
		
		$oauth_authorization = 'OAuth ';
		$oauth_authorization += 'oauth_consumer_key="' + [System.Uri]::EscapeDataString($oauth_consumer_key) + '",';
		$oauth_authorization += 'oauth_nonce="' + [System.Uri]::EscapeDataString($oauth_nonce) + '",';
		$oauth_authorization += 'oauth_signature="' + [System.Uri]::EscapeDataString($oauth_signature) + '",';
		$oauth_authorization += 'oauth_signature_method="HMAC-SHA1",'
		$oauth_authorization += 'oauth_timestamp="' + [System.Uri]::EscapeDataString($oauth_timestamp) + '",'
		$oauth_authorization += 'oauth_token="' + [System.Uri]::EscapeDataString($oauth_token) + '",';
		$oauth_authorization += 'oauth_version="1.0"';
		
		$post_body = [System.Text.Encoding]::ASCII.GetBytes("track=" + $track); 
				
		Write-Host "Create HTTP web request ..." -ForegroundColor Green
		[System.Net.HttpWebRequest] $request = [System.Net.WebRequest]::Create("https://stream.twitter.com/1.1/statuses/filter.json");
		$request.Method = "POST";
		$request.Headers.Add("Authorization", $oauth_authorization);
		$request.ContentType = "application/x-www-form-urlencoded";
		$body = $request.GetRequestStream();
		
		$body.write($post_body, 0, $post_body.length);
		$body.flush();
		$body.close();
		$response = $request.GetResponse() ;
		
		Write-Host "Start stream reading ..." -ForegroundColor Green
		
		$sReader = New-Object System.IO.StreamReader($response.GetResponseStream())
		
		$inrec = $sReader.ReadLine()
		$count = 0
		while (($inrec -ne $null) -and ($count -le $lineMax))
		{
		    if ($inrec -ne "")
		    {
		        Write-Host $count.ToString() ", " -NoNewline -ForegroundColor Green
		        if ($count%10 -eq 0){
		            write-host " --- " -NoNewline
		            Get-Date -Format hh:mm:sstt
		        }
		
		        $writeStream.WriteLine($inrec)
		        $count ++
		    }
		
		    $inrec=$sReader.ReadLine()
		}
		
		Write-Host "Write to the destination blob ..." -ForegroundColor Green
		$writeStream.Flush()
		$memStream.Seek(0, "Begin")
		$destBlob.UploadFromStream($memStream) 
		
		$sReader.close()
		Write-Host "Complete!" -ForegroundColor Green

4. 스크립트에서 첫 9개의 변수를 설정합니다.	

	<table border="1">
	<tr><th>변수</th><th>설명</th></tr>
	<tr><td>$storageAccountName</td><td>기본 HDInsight 클러스터 파일 시스템에 사용되는 Azure 저장소 계정입니다. 프로비전 시 지정되는 저장소 계정입니다.  <a href="#prerequisites">필수 조건</a>을 참조하세요.</td></tr>
	<tr><td>$containerName</td><td>기본 HDInsight 클러스터 파일 시스템에 사용되는 Blob 컨테이너입니다. 프로비전 시 지정되는 컨테이너입니다.  <a href="#prerequisites">필수 조건</a>을 참조하세요.</td></tr>
	<tr><td>$destBlobName</td><td>출력 Blob 이름입니다.  기본값은 <strong>tutorials/twitter/data/tweets.txt</strong>입니다. 기본값을 변경하는 경우 이에 따라 PowerShell 스크립트를 업데이트해야 합니다.</td></tr>
	<tr><td>$trackString</td><td>웹 서비스가 이 키워드와 관련된 트윗을 반환합니다.  기본값은 <strong>Azure, Cloud, HDInsight</strong>입니다. 기본값을 변경하는 경우 이에 따라 PowerShell 스크립트를 업데이트합니다.</td></tr>
	<tr><td>$lineMax</td><td>이 값은 스크립트가 읽는 트윗의 수를 결정합니다. 트윗 100개를 읽는 데 약 3분이 걸립니다. 더 큰 수를 설정할 수 있지만 그러면 다운로드하는 데 더 많은 시간이 걸립니다.</td></tr>
	<tr><td>$oauth_consumer_key</td><td>앞에서 Twitter 응용 프로그램을 만들 때 기록해 둔 <strong>소비자 키</strong> 입니다.</td></tr>
	<tr><td>$oauth_consumer_secret</td><td>앞에서 기록해 둔 <strong>소비자 비밀</strong> 입니다.</td></tr>
	<tr><td>$oauth_token</td><td>앞에서 기록해 둔 Twitter 응용 프로그램 <strong>액세스 토큰</strong> 입니다.</td></tr>
	<tr><td>$oauth_token_secret</td><td>앞에서 기록해 둔 Twitter 응용 프로그램 <strong>액세스 토큰 비밀</strong> 입니다.</td></tr>
	</table>

5. **F5** 키를 눌러 스크립트를 실행합니다. 문제가 발생하는 경우 해결하려면 모든 줄을 선택하고 **F8** 키를 누릅니다.
6. 출력 끝부분에 "Complete!"가 표시됩니다. 오류 메시지는 빨간색으로 표시됩니다.

유효성 검사 절차로, Azure 저장소 탐색기 또는 Azure PowerShell을 사용하여 Azure Blob 저장소에서 출력 파일 **/tutorials/twitter/data/tweets.txt**를 확인할 수 있습니다.  파일을 나열하는 샘플 PowerShell 스크립트를 보려면 [HDInsight에서 Blob 저장소 사용][hdinsight-storage-powershell]을 참조하세요. 



##<a id="script"></a>HiveQL 스크립트 만들기

Azure PowerShell을 사용하여 여러 HiveQL 문을 한 번에 하나씩 실행하거나 HiveQL 문을 스크립트 파일에 패키지할 수 있습니다. 이 자습서에서는 HiveQL 스크립트를 만듭니다. 스크립트 파일은 WASB에 업로드해야 합니다. 다음 섹션에서는 Azure PowerShell을 사용하여 스크립트 파일을 실행합니다.

HiveQL 스크립트는 다음을 수행합니다.

1. 이미 있는 경우 **tweets_raw 테이블을 삭제합니다**.
2. **tweets_raw Hive 테이블을 만듭니다**. 이 임시 Hive 구조적 테이블에는 추가 ETL 처리를 위한 데이터가 저장됩니다.  파티션에 대한 자세한 내용은 [Hive 자습서][apache-hive-tutorial](영문)를 참조하세요.  
3. 원본 폴더인 /tutorials/twitter/data에서 **데이터를 로드합니다**. 이제 중첩 JSon 형식의 대량 트윗 데이터 집합이 임시 Hive 테이블 구조로 변환되었습니다.
3. 이미 있는 경우 **tweets 테이블을 삭제합니다**.
4. **tweets 테이블을 만듭니다**. Hive를 사용하여 트윗 데이터 집합에 대해 쿼리하려면 먼저, 다른 ETL 프로세스를 실행해야 합니다. 이 ETL 프로세스는 "twitter_raw" 테이블에 저장한 데이터에 대해 더욱 자세한 테이블 스키마를 정의합니다.  
5. **overwrite 테이블을 삽입합니다**. 이 복잡한 Hive 스크립트는 Hadoop 클러스터로 긴 Map Reduce 작업 집합을 시작합니다.  데이터 집합 및 클러스터의 크기에 따라 이 작업은 약 10분 정도 걸릴 수 있습니다.
6. **overwrite 디렉터리를 삽입합니다**. 쿼리를 실행하고 데이터 집합을 파일로 출력합니다. 이 쿼리는 "Azure"라는 단어가 포함된 트윗을 가장 많이 보낸 Twitter 사용자의 목록을 반환합니다.

**Hive 스크립트를 만들어 Azure에 업로드하려면**

1. Windows PowerShell ISE를 엽니다.
2. 스크립트 창에서 다음 스크립트를 복사합니다.

		Write-Host "Define variables ..." -ForegroundColor Green
		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<BlobContainerName>"
		
		$sourceDataPath = "/tutorials/twitter/data"
		$outputPath = "/tutorials/twitter/output"
		
		$hqlScriptFile = "tutorials/twitter/twitter.hql"
		
		$hqlStatements = @"
		set hive.exec.dynamic.partition = true;
		set hive.exec.dynamic.partition.mode = nonstrict;
		
		DROP TABLE tweets_raw;
		CREATE TABLE tweets_raw (
		    json_response STRING
		) 
		PARTITIONED BY (filesequence INT);
		
		LOAD DATA INPATH '$sourceDataPath'
		INTO TABLE tweets_raw
		PARTITION (filesequence = 1);
		
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
		)
		PARTITIONED BY (filesequence INT);
		
		FROM tweets_raw
		INSERT OVERWRITE TABLE tweets
		PARTITION (filesequence)
		SELECT
		    cast(get_json_object(json_response, '$.id_str') as BIGINT),
		    get_json_object(json_response, '$.created_at'),
			concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
			substr (get_json_object(json_response, '$.created_at'),27,4)),
			substr (get_json_object(json_response, '$.created_at'),27,4),
			case substr (get_json_object(json_response,	'$.created_at'),5,3)
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
			json_response,
			filesequence
		WHERE (length(json_response) > 500);
		
		INSERT OVERWRITE DIRECTORY '$outputPath'
		SELECT name, screen_name, count(1) as cc 
			FROM tweets 
			WHERE text like "%Azure%" 
			GROUP BY name,screen_name 
			ORDER BY cc DESC LIMIT 10;
		"@
		
		Write-Host "Define the connection string ..." -ForegroundColor Green
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
		
		Write-Host "Create block blob objects referencing the hql script file" -ForegroundColor Green
		$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
		$storageClient = $storageAccount.CreateCloudBlobClient();
		$storageContainer = $storageClient.GetContainerReference($containerName)
		$hqlScriptBlob = $storageContainer.GetBlockBlobReference($hqlScriptFile)
		
		Write-Host "Define a MemoryStream and a StreamWriter for writing ... " -ForegroundColor Green
		$memStream = New-Object System.IO.MemoryStream
		$writeStream = New-Object System.IO.StreamWriter $memStream
		$writeStream.Writeline($hqlStatements)
		
		Write-Host "Write to the destination blob ... " -ForegroundColor Green
		$writeStream.Flush()
		$memStream.Seek(0, "Begin")
		$hqlScriptBlob.UploadFromStream($memStream)
		
		Write-Host "Complete!" -ForegroundColor Green


4. 스크립트에서 첫 2개의 변수를 설정합니다.	

	<table border="1">
	<tr><th>변수</th><th>설명</th></tr>
	<tr><td>$storageAccountName</td><td>기본 HDInsight 클러스터 파일 시스템에 사용되는 Azure 저장소 계정입니다. 프로비전 시 지정되는 저장소 계정입니다.  <a href="#prerequisites">필수 조건</a>을 참조하세요.</td></tr>
	<tr><td>$containerName</td><td>기본 HDInsight 클러스터 파일 시스템에 사용되는 Blob 컨테이너입니다. 프로비전 시 지정되는 컨테이너입니다.  <a href="#prerequisites">필수 조건</a>을 참조하세요.</td></tr>
	<tr><td>$sourceDataPath</td><td>Hive 쿼리가 데이터를 읽는 WASB 위치입니다. 이 변수를 변경할 필요가 없습니다.</td></tr>
	<tr><td>$outputPath</td><td>Hive 쿼리가 결과를 출력하는 WASB 위치입니다. 이 변수를 변경할 필요가 없습니다.</td></tr>
	<tr><td>$hqlScriptFile</td><td>HiveQL 스크립트 파일의 위치 및 파일 이름입니다.</td></tr>
	</table>

5. **F5** 키를 눌러 스크립트를 실행합니다. 문제가 발생하는 경우 해결하려면 모든 줄을 선택하고 **F8** 키를 누릅니다.
6. 출력 끝부분에 "Complete!"가 표시됩니다. 오류 메시지는 빨간색으로 표시됩니다.

유효성 검사 절차로, Azure 저장소 탐색기 또는 Azure PowerShell을 사용하여 Azure Blob 저장소에서 출력 파일 **/tutorials/twitter/twitter.hql**을 확인할 수 있습니다.  파일을 나열하는 샘플 PowerShell 스크립트를 보려면 [HDInsight에서 Blob 저장소 사용][hdinsight-storage-powershell]을 참조하세요.  


##<a name="process"></a> Hive를 사용하여 Twitter 데이터 처리

준비 작업을 모두 마쳤습니다. 이제 Hive 스크립트를 호출하고 결과를 확인할 수 있습니다.

### Hive 작업 제출

다음 PowerShell 스크립트를 사용하여 Hive 스크립트를 실행합니다. 첫 번째 변수를 설정해야 합니다.

	Write-Host "Set the variables ... " -ForegroundColor Green
	$clusterName = "<HDInsightClusterName>"
	
	$hqlScriptFile = "/tutorials/twitter/twitter.hql"
	$statusFolder = "/tutorials/twitter/jobstatus"
	
	Write-Host "Invoke Hive ... " -ForegroundColor Green
	Use-AzureHDInsightCluster $clusterName
	$response = Invoke-Hive -file $hqlScriptFile -StatusFolder $statusFolder -OutVariable $outVariable
	
	Write-Host "Display the standard error log ... " -ForegroundColor Green
	$jobID = ($response | Select-String job_ | Select-Object -First 1) -replace '\s*$' -replace '.*\s'
	Get-AzureHDInsightJobOutput -cluster $clusterName -JobId $jobID -StandardError 

### 결과 확인

다음 PowerShell 스크립트를 사용하여 Hive 작업 출력을 확인합니다. 처음 2개의 변수를 설정해야 합니다.

	Write-Host "Set the variables ... " -ForegroundColor Green
	$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
	$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
	$blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.
	
	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	Write-Host "Download the blob ..." -ForegroundColor Green
	Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force
	
	Write-Host "Display the output ..." -ForegroundColor Green
	cat "./$blob"

> [WACOM.NOTE] Hive 테이블은 필드 구분 기호로 \001을 사용합니다. 구분 기호는 출력에서 보이지 않습니다. 

분석 결과가 WASB에 배치된 후에는 데이터를 Azure SQL 데이터베이스/SQL Server로 내보내거나, Power Query를 사용하여 데이터를 Excel로 내보내거나, Hive ODBC 드라이버를 사용하여 응용 프로그램을 데이터에 연결할 수 있습니다.  자세한 내용은 [HDInsight에서 Sqoop 사용][hdinsight-use-sqoop], [HDInsight를 사용하여 비행 지연 데이터 분석][hdinsight-analyze-flight-delay-data], [HDInsight에 파워 쿼리로 Excel 연결][hdinsight-power-query] 및 [HDInsight에 Microsoft Hive ODBC 드라이버로 Excel 연결][hdinsight-hive-odbc]을 참조하세요.
   

##<a id="cleanup"></a>자습서 정리

자습서를 다시 실행하려는 경우 다음 항목이 필요합니다.

- **트윗 데이터 파일을 다시 만듭니다**. 원본 트윗 데이터 파일이 Hive 작업에 의해 제거되었습니다.  새로 생성해야 합니다.  파일 이름은 **tutorials/twitter/data/tweets.txt**입니다. [Twitter 피드 가져오기](#feed)를 참조하세요.

##<a id="nextsteps"></a>다음 단계

이 자습서에서는 비구조적 Json 데이터 집합을 구조적 Hive 테이블로 변환해서 Azure의 HDInsight를 사용하여 Twitter 데이터를 쿼리하고 탐색하고 분석하는 방법에 대해 살펴보았습니다. 자세한 내용은 다음을 참조하세요.

- [HDInsight 시작][hdinsight-get-started]
- [HDInsight를 사용하여 비행 지연 데이터 분석][hdinsight-analyze-flight-delay-data]
- [HDInsight에 파워 쿼리로 Excel 연결][hdinsight-power-query]
- [HDInsight에 Microsoft Hive ODBC 드라이버로 Excel 연결][hdinsight-hive-odbc]
- [HDInsight에서 Sqoop 사용][hdinsight-use-sqoop]

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/ko-kr/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell
[powershell-script]: http://technet.microsoft.com/ko-kr/library/ee176961.aspx

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage/#powershell
[hdinsight-analyze-flight-delay-data]: ../hdinsight-analyze-flight-delay-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query/
[hdinsight-hive-odbc]: ../hdinsight-connect-excel-hive-ODBC-driver/


<!--HONumber=35.1-->
