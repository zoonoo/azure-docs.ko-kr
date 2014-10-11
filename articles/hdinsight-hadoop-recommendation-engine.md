<properties linkid="manage-services-hdinsight-recommendation-engine-using-mahout" urlDisplayName="Hadoop Recommendation Engine" pageTitle="Hadoop recommendation engine (.NET) | Azure" metaKeywords="Azure Apache Mahout, Azure recommendation example, Azure recommendation tutorial, Azure recommendation engine" description="A tutorial that teaches how to use the Apache Mahout recommendation engine with Azure to create song suggestions based on listening habits." disqusComments="1" umbracoNaviHide="1" title="Simple recommendation engine using Apache Mahout" authors="jgao" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="jgao"></tags>



# Apache Mahout을 사용한 간단한 추천 엔진

Apache Mahout™은 확장 가능한 컴퓨터 학습 응용 프로그램에서 사용하도록 빌드된 컴퓨터 학습 라이브러리입니다. Mahout과 같은 추천 엔진은 현재 가장 널리 사용 중인 컴퓨터 학습 응용 프로그램 유형 중 하나이며 명확한 여러 마케팅 응용 프로그램을 포함합니다.

Apache Mahout는 항목 기반 공동 작업 필터링을 위해 기본 제공 구현을 제공합니다. 이 방법은 추천 데이터 마이닝을 수행하는 데 널리 사용됩니다. 항목 기반 공동 작업 필터링은 Amazon.com에서 개발되었습니다. 여기서 아이디어는 항목 선호 간의 상관관계를 보여 주는 사용자 선호 데이터를 사용하여 유사 그룹의 미래 사용자 취향을 유추할 수 있다는 것입니다.

이 자습서에서는 [Million Song Dataset][] 사이트를 사용하고 [데이터 집합][]을 다운로드하여 과거 청취 습관을 기준으로 사용자에 대한 음악 추천을 만듭니다.



다음 내용을 배웁니다.

* 추천 엔진을 사용하는 방법

이 자습서는 다음과 같이 구성되어 있습니다.

1. [설정 및 구성][]
2. [데이터 검사 및 형식 지정][]
3. [Mahout 설치][]
4. [Mahout 작업 실행][]

## <a name="setup"></a>설정 및 구성

이 자습서에서는 Azure 및 HDinsight 미리 보기로 설정했으며 샘플을 실행할 수 있는 HDInsight 클러스터를 만들었다고 가정합니다. 아직 완료하지 않은 경우 [Azure HDInsight 시작][] 자습서에서 이러한 필수 조건을 충족하는 방법에 대한 지침을 확인하세요.

## <a name="segment1"></a>데이터 검사 및 형식 지정

이 예제는 사용자가 특정 음악에 대한 선호를 표현하는 방법을 다룹니다. 사용자가 음악을 청취한 횟수를 통해 해당 음악에 대한 사용자 선호를 측정할 수 있다고 가정합니다. 선호 데이터에서 검색된 패턴을 사용하여 표현된 음악 선호에 따라 미래 사용자 선호를 예측할 수 있습니다. [Echo Nest Taste Profile Subset][Million Song Dataset] 웹 페이지의 **설명** 섹션에서 이 데이터 집합의 샘플을 볼 수 있습니다.

![Echo Nest Taste Profile Subset][]

### Million Song Dataset의 샘플 데이터

이 데이터 집합을 Mahout와 함께 사용하려면 다음 두 작업을 수행해야 합니다.

1.	음악 및 사용자의 ID를 정수 값으로 변환합니다.
2.	쉼표로 구분된 파일에 새 값을 순위와 함께 저장합니다.

Visual Studio 2010이 설치되지 않은 경우 이 단계를 건너뛰고 Mahout 작업 실행 섹션으로 이동하여 미리 생성된 버전을 가져오세요.

먼저 Visual Studio 2010을 시작합니다. Visual Studio에서 **파일 -> 새로 만들기 -> 프로젝트**를 선택합니다. **설치된 템플릿** 창의 **Visual C#** 노드 내에서 **창** 범주를 선택한 후 목록에서 **콘솔 응용 프로그램**을 선택합니다. 프로젝트 이름을 "ConvertToMahoutInput"으로 이정하고 **확인** 단추를 클릭합니다.

![콘솔 응용 프로그램 만들기][]

### 콘솔 응용 프로그램 만들기

1. 응용 프로그램이 생성된 후 **Program.cs** 파일을 열고 **Program** 클래스에 다음과 같은 정적 멤버를 추가합니다.


		const char tab = '\u0009';
		static Dictionary<string, int> usersMapping = new Dictionary<string, int>();
		static Dictionary<string, int> songMapping = new Dictionary<string, int>();	


2. `using System.IO;` 문을 추가하고 **Main** 메서드에 다음 코드를 채웁니다.

		var inputStream = File.Open(args[0], FileMode.Open);
		var reader = new StreamReader(inputStream);

		var outStream = File.Open("mInput.txt", FileMode.OpenOrCreate);
		var writer = new StreamWriter(outStream);

		var i = 1;

		var line = reader.ReadLine();
		while (!string.IsNullOrWhiteSpace(line))
		{
    		i++;
    		if (i > 5000)
			break;
    		var outLine = line.Split(tab);

    		int user = GetUser(outLine[0]);
    		int song = GetSong(outLine[1]);

    		writer.Write(user);
	    	writer.Write(',');
	    	writer.Write(song);
	   	 	writer.Write(',');
	   	 	writer.WriteLine(outLine[2]);

    		line = reader.ReadLine();
		}

		Console.WriteLine("saved {0} lines to {1}", i, args[0]);
	
		reader.Close();
		writer.Close();
	
		SaveMapping(usersMapping, "users.csv");
		SaveMapping(songMapping, "mInput.csv");
	
		Console.WriteLine("Mapping saved");
		Console.ReadKey();


3. 이제 ID를 정수로 변환하는 **GetUser** 및 **GetSong** 함수를 만듭니다.

		static int GetUser(string user)
		{
    		if (!usersMapping.ContainsKey(user))
        		usersMapping.Add(user, usersMapping.Count + 1);

    		return usersMapping[user];
		}

		static int GetSong(string song)
		{
    		if (!songMapping.ContainsKey(song))
        		songMapping.Add(song, songMapping.Count + 1);

    		return songMapping[song];
		}

4. 마지막으로, 봇 매핑 사전을 .csv 파일에 저장하는 SaveMapping 메서드를 구현하는 유틸리티를 만듭니다.

		static void SaveMapping(Dictionary<string, int> mapping, string fileName)
		{
    		var stream = File.Open(fileName, FileMode.Create);
    		var writer = new StreamWriter(stream);

    		foreach (var key in mapping.Keys)
    		{
        		writer.Write(key);
        		writer.Write(',');
        		writer.WriteLine(mapping[key]);
    		}

    		writer.Close();
		}

5. [이 링크][데이터 집합]에서 샘플 데이터를 다운로드합니다. 다운로드된 후 **train\_triplets.txt.zip**을 열고 **train\_triplets.txt**를 추출합니다.

    유틸리티를 실행하는 경우 **train\_triplets.txt**의 위치와 함께 명령줄 인수를 포함합니다. **솔루션 탐색기**에서 **ConvertToMahoutInput** 프로젝트 노드를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택하면 됩니다. 프로젝트 속성 페이지의 왼쪽에서 **디버그** 탭을 선택하고 **명령줄 인수** 텍스트 상자에 \<localpath\>train\_triplets.txt의 경로를 추가합니다.

    ![명령줄 인수 설정][]

### 명령줄 인수 설정

- **F5** 키를 눌러 프로그램을 실행합니다. 완료되면 프로젝트가 저장된 위치에서 **bin\\Debug** 폴더를 열고 유틸리티의 출력을 확인합니다. users.txt 및 mInput.txt가 표시되어야 합니다.

## <a name="segment2"></a>Mahout 설치

- HDInsight 클러스터 포털을 열고 **원격 데스크톱** 아이콘을 클릭합니다.

    ![클러스터 관리 아이콘][]

### 원격 데스크톱 아이콘

HDInsight에는 Mahout가 기본적으로 포함되어 있지 않습니다. 그러나 Hadoop 에코시스템에 포함되기 때문에 [Mahout][] 웹 사이트에서 다운로드할 수 있습니다. 최신 버전은 0.7이지만 이 지침 집합은 버전 0.5 또는 0.7에서 호환됩니다.

1. 먼저 [Mahout 버전 0.7][]을 로컬 컴퓨터로 다운로드합니다.

2. 로컬 zip 파일을 선택하고 ctrl-v를 눌러 복사한 후 Hadoop 클러스터에 붙여넣어 클러스터에 복사합니다.

    ![Mahout 업데이트][]

### 헤드 노트에 Mahout 복사

1. 마지막으로, 복사 세션이 완료된 후 zip 파일을 마우스 오른쪽 단추로 클릭하고 C:\\apps\dist에 Mahout 배포의 압축을 풉니다. 이제 mahout가 C:\apps\dist\mahout-distriution-0.7에 설치되었습니다.

2. 간단히 표시하기 위해 폴더 이름을 c:\\apps\\dist\\mahout-0.7로 바꿉니다.

### <a name="segment3"></a>Mahout 작업 실행

1. **bin\Debug** 폴더의 **mInput.txt** 파일을 원격 클러스터의 **c:\\**로 복사합니다. 파일이 복사된 후 압축을 풉니다. 이전 섹션에서 언급한 대로 로컬 컴퓨터에서 파일을 선택한 후 ctrl-C를 누른 다음 RDP 세션 창에 ctrl-v하여 파일을 원격 RDP 세션에 복사합니다.

2. 추천을 생성할 사용자의 ID가 포함된 파일을 만듭니다. **c:\\**에 단일 사용자의 ID가 포함된 **users.txt** 텍스트 파일을 만들면 됩니다.

<div class="dev-callout"> 
<b>참고</b> 
<p>사용자 ID를 새 줄에 입력하여 추가 사용자에 대한 추천을 생성할 수 있습니다. mInput.txt 및 users.txt를 생성하는 데 문제가 있는 경우 이 github <a href="https:/github.com/wenming/BigDataSamples/tree/master/mahout">리포지토리</a>에서 미리 생성된 버전을 다운로드할 수 있습니다. 

모든 항목을 하나의 <a href="https://github.com/wenming/BigDataSamples/archive/master.zip">zip 파일</a>로 다운로드하는 것이 가장 편리합니다. users.txt 및 mInput.txt를 찾아서 원격 클러스터의 c:\ 폴더에 복사합니다.</p> 
</div>

이때 Hadoop 터미널 창을 열고 users.txt 및 mInput.txt가 포함된 폴더로 이동해야 합니다.

![Mahout 명령 창][]

### Hadoop 명령 창

1. **mInput.txt** 및 **users.txt**를 HDFS에 복사합니다. **Hadoop 명령 셸**을 열고 다음 명령을 실행하면 됩니다.

		hadoop dfs -copyFromLocal c:\mInput.txt input\mInput.txt
		hadoop dfs -copyFromLocal c:\users.txt input\users.txt

2. 파일이 HDFS에 복사되었는지 확인합니다.

		hadoop fs -ls input/

    다음이 표시되어야 합니다.

		Found 2 items
		-rwxrwxrwx   1 writer supergroup      53322 2013-03-08 20:32 /user/writer/input/mInput.txt
		-rwxrwxrwx   1 writer supergroup        353 2013-03-08 20:33 /user/writer/input/users.txt

3. 이제 다음 명령을 사용하여 Mahout 작업을 실행할 수 있습니다.

		c:\apps\dist\mahout-0.7\bin>hadoop jar c:\Apps\dist\mahout-0.7\mahout-core-0.7-job.jar org.apache.mahout.cf.taste.hadoop.item.RecommenderJob -s SIMILARITY_COOCCURRENCE --input=input/mInput.txt --output=output --usersFile=input/users.txt

    추천 엔진이 여러 사용자의 기능 요소를 비교하는 데 사용할 수 있는 다른 많은 "distance" 함수가 있는 경우 Similarity 클래스를 실험해 보고 SIMILARITY\_COOCCURRENCE, SIMILARITY\_LOGLIKELIHOOD, SIMILARITY\_TANIMOTO\_COEFFICIENT, SIMILARITY\_CITY\_BLOCK, SIMILARITY\_COSINE, SIMILARITY\_PEARSON\_CORRELATION, SIMILARITY\_EUCLIDEAN\_DISTANCE로 변경할 수 있습니다. 이 자습서에서는 Mahout의 자세한 데이터 과학 측면을 다루지 않습니다.

4. Mahout 작업은 7분 동안 실행되어야 하며, 그 후에 출력 파일이 생성됩니다. 다음 명령을 실행하여 출력 파일의 로컬 복사본을 만듭니다.

		hadoop fs -copyToLocal output/part-r-00000 c:\output.txt

5. **c:\\** 루트 폴더에서 **output.txt** 파일을 열고 내용을 검사합니다. 파일의 구조는 다음과 같습니다.

		user	[song:rating,song:rating, ...]

6. 클러스터에서 Mahout의 다른 부분을 사용하려면 Mahout 배포의 bin 디렉터리에 Mahout.cmd 복사본을 저장해야 합니다.


## <a name="summary"></a>요약

추천 엔진은 여러 최신 소셜 네트워킹 사이트, 온라인 쇼핑, 스트리밍 미디어 및 기타 인터넷 사이트에 대한 중요한 기능을 제공합니다. Mahout는 사용하기 쉽고, 많은 유용한 기능을 포함하며, Hadoop에서 확장 가능한 기본 추천 엔진을 제공합니다.

## 다음 단계

이 문서에서는 Hadoop 명령줄을 사용하는 방법을 설명하지만 HDInsight 대화형 콘솔을 사용하여 작업을 수행할 수도 있습니다. 자세한 내용은 [지침: HDInsight 대화형 JavaScript 및 Hive 콘솔][대화형 콘솔]을 참조하세요.

  [Million Song Dataset]: http://labrosa.ee.columbia.edu/millionsong/tasteprofile
  [데이터 집합]: http://labrosa.ee.columbia.edu/millionsong/sites/default/files/challenge/train_triplets.txt.zip
  [설정 및 구성]: #setup
  [데이터 검사 및 형식 지정]: #segment1
  [Mahout 설치]: #Segment2
  [Mahout 작업 실행]: #segment2
  [Azure HDInsight 시작]: /en-us/manage/services/hdinsight/get-started-hdinsight/
  [Echo Nest Taste Profile Subset]: ./media/hdinsight-hadoop-recommendation-engine/the-echo-nest-taste-profile-subset.png
  [콘솔 응용 프로그램 만들기]: ./media/hdinsight-hadoop-recommendation-engine/creating-a-console-application.png
  [명령줄 인수 설정]: ./media/hdinsight-hadoop-recommendation-engine/setting-command-line-arguments.png
  [클러스터 관리 아이콘]: ./media/hdinsight-hadoop-recommendation-engine/the-manage-cluster-icon.png
  [Mahout]: http://mahout.apache.org/
  [Mahout 버전 0.7]: http://www.apache.org/dyn/closer.cgi/mahout/
  [Mahout 업데이트]: ./media/hdinsight-hadoop-recommendation-engine/uploading-mahout.PNG
  [리포지토리]: https://github.com/wenming/BigDataSamples/tree/master/mahout
  [zip 파일]: https://github.com/wenming/BigDataSamples/archive/master.zip
  [Mahout 명령 창]: ./media/hdinsight-hadoop-recommendation-engine/mahout-commandwindow.PNG
 