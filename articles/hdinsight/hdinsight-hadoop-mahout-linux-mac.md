<properties
	pageTitle="Mahout 및 Hadoop을 사용하여 추천 생성 | Microsoft Azure"
	description="Apache Mahout 기계 학습 라이브러리를 사용하여 Linux 기반 HDInsight(Hadoop)에서 영화 추천을 생성하는 방법을 알아봅니다."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="larryfr"/>

#HDInsight의 Linux 기반 Hadoop와 함께 Apache Mahout를 사용하여 영화 추천 생성(미리 보기)

[AZURE.INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Azure HDInsight에서 [Apache Mahout](http://mahout.apache.org) 기계 학습 라이브러리를 사용하여 영화 추천을 생성하는 방법에 대해 알아봅니다.

Mahout은 Apache Hadoop용 [기계 학습][ml] 라이브러리입니다. Mahout에는 필터링, 분류 및 클러스터링과 같은 데이터 처리를 위한 알고리즘이 포함됩니다. 이 문서에서는 추천 엔진을 사용하여 친구가 본 영화를 기준으로 영화 추천을 생성합니다.

> [AZURE.NOTE]이 문서의 단계에는 HDInsight 클러스터의 Linux 기반 Hadoop가 필요합니다(미리 보기). Windows 기반 클러스터와 함께 Mahout 사용에 대한 자세한 내용은 [HDInsight에서 Windows 기반 Hadoop로 Apache Mahout을 사용하여 영화 추천 생성](hdinsight-mahout.md)을 참조하세요.

##필수 조건

* HDInsight 클러스터의 Linux 기반 Hadoop HDInsight 클러스터 만들기에 대한 자세한 내용은 [HDInsight에서 Linux 기반 Hadoop 사용 시작][getstarted]을 참조하세요.

##<a name="recommendations"></a>이해 권장 사항

Mahout에서 제공하는 기능 중 하나가 추천 엔진입니다. 이 엔진은 `userID`, `itemId` 및 `prefValue`(항목에 대한 사용자 선호도) 형식의 데이터를 허용합니다. 그런 다음 Mahout에서 동시 발생 분석을 수행하여 _특정 항목에 대한 선호도를 가진 사용자가 다른 항목에 대한 선호도도 갖고 있는지_ 확인할 수 있습니다. Mahout은 좋아하는 항목 선호도를 가진 사용자를 확인하며, 이 선호도는 추천하는 데 사용할 수 있습니다.

다음은 영화를 사용하는 매우 단순한 예제입니다.

* __동시 발생__: Joe, Alice 및 Bob은 모두 _스타워즈_, _제국의 역습_ 및 _제다이의 귀환_을 좋아합니다. Mahout은 이러한 영화 중 하나를 좋아하면서 다른 두 개도 좋아하는 사용자를 확인합니다.

* __동시 발생__: Bob 및 Alice는 _보이지 않는 위협_, _클론의 습격_ 및 _시스의 복수_도 좋아합니다. Mahout은 이전의 3개 영화를 좋아하는 사용자가 이 3개 영화도 좋아하는지 확인합니다.

* __유사성 추천__: Joe가 첫 3개 영화를 좋아하므로, Mahout은 유사한 선호도를 가진 다른 사람이 좋아하지만 Joe는 본(좋아하거나 평가한) 적이 없는 영화를 검색합니다. 이 경우 Mahout은 _보이지 않는 위협_, _클론의 습격_ 및 _시스의 복수_를 추천합니다.

##데이터 로드

편의를 위해 [GroupLens Research][movielens]에서 Mahout과 호환되는 형식으로 영화에 대한 평가 데이터를 제공합니다. 다음 단계를 사용하여 데이터를 다운로드하려면 다음 클러스터에 대한 기본 저장소에 로드합니다.

1. SSH를 사용하여 Linux 기반 HDInsight 클러스터에 연결합니다. 연결 시 사용할 주소는 `CLUSTERNAME-ssh.azurehdinsight.net`이며 포트는 `22`입니다.

	SSH를 사용한 HDInsight 연결에 대한 자세한 내용은 다음 문서를 참조합니다.

    * **Linux, Unix 또는 OS X 클라이언트**: [Linux, OS X 또는 Unix로부터 Linux 기반 HDInsight 클러스터에 연결](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)을 참조합니다.

    * **Windows 클라이언트**: [Windows로부터 Linux 기반 HDInsight 클러스터에 연결](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster)을 참조합니다.

2. 1700개 영화에 대한 1,000명 사용자의 100,000개 평가를 포함하는 MovieLens 100k 압축 파일을 다운로드합니다.

        curl -O http://files.grouplens.org/datasets/movielens/ml-100k.zip

3. 다음 명령을 사용하여 압축 파일을 풉니다.

        unzip ml-100k.zip

    **ml-100 k**라는 새 폴더로 내용이 추출됩니다 .

4. HDInsight 저장소로 데이터를 복사하려면 다음 명령을 사용합니다.

        cd ml-100k
        hadoop fs -copyFromLocal ml-100k/u.data /example/data/u.data

    이 파일에 포함된 데이터의 구조는 `userID`, `movieID`, `userRating` 및 `timestamp`이며, 각 사용자의 영화 등급 평가를 보여줍니다. 다음은 데이터의 예제입니다.


		196	242	3	881250949
		186	302	3	891717742
		22	377	1	878887116
		244	51	2	880606923
		166	346	1	886397596

##작업 실행

다음 명령을 실행하여 추천 작업을 실행합니다.

	mahout recommenditembased -s SIMILARITY_COOCCURRENCE --input /example/data/u.data --output /example/data/mahoutout  --tempDir /temp/mahouttemp

> [AZURE.NOTE]작업을 완료하려면 몇 분정도 걸릴 수 있으며 여러 MapReduce 작업을 실행할 수 있습니다.

##출력 보기

1. 작업이 완료되면 다음 명령을 사용하여 생성된 결과를 봅니다.

		hadoop fs -text /example/data/mahoutout/part-r-00000

	출력은 다음과 같이 표시됩니다.

		1	[234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
		2	[282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
		3	[284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
		4	[690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

	첫 번째 열은 `userID`입니다. '[' 및 ']'에 포함된 값은 `movieId`:`recommendationScore`입니다.

2. **ml-100 k** 디렉터리에 포함된 데이터 중 일부는 더 많은 사용자가 데이터에 친숙할 수 있도록 사용할 수 있습니다. 먼저, 다음 명령을 사용하여 데이터를 다운로드합니다.

		hadoop fs -copyToLocal /example/data/mahoutout/part-r-00000 recommendations.txt

	현재 디렉터리에 **recommendations.txt**라는 파일에 출력 데이터를 복사합니다.

3. 권장 사항 출력의 데이터에 대한 영화 이름을 조회하는 새 Python 스크립트를 만들려면 다음 명령을 사용합니다.

		nano show_recommendations.py

	편집기가 열리면 파일의 내용으로 다음을 사용합니다.

		#!/usr/bin/env python

		import sys

		if len(sys.argv) != 5:
		        print "Arguments: userId userDataFilename movieFilename recommendationFilename"
		        sys.exit(1)

		userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

		print "Reading Movies Descriptions"
		movieFile = open(movieFilename)
		movieById = {}
		for line in movieFile:
		        tokens = line.split("|")
		        movieById[tokens[0]] = tokens[1:]
		movieFile.close()

		print "Reading Rated Movies"
		userDataFile = open(userDataFilename)
		ratedMovieIds = []
		for line in userDataFile:
		        tokens = line.split("\t")
		        if tokens[0] == userId:
		                ratedMovieIds.append((tokens[1],tokens[2]))
		userDataFile.close()

		print "Reading Recommendations"
		recommendationFile = open(recommendationFilename)
		recommendations = []
		for line in recommendationFile:
		        tokens = line.split("\t")
		        if tokens[0] == userId:
		                movieIdAndScores = tokens[1].strip("[]\n").split(",")
		                recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
		                break
		recommendationFile.close()

		print "Rated Movies"
		print "------------------------"
		for movieId, rating in ratedMovieIds:
		        print "%s, rating=%s" % (movieById[movieId][0], rating)
		print "------------------------"

		print "Recommended Movies"
		print "------------------------"
		for movieId, score in recommendations:
		        print "%s, score=%s" % (movieById[movieId][0], score)
		print "------------------------"

	**Ctrl X**, **Y**, **Enter**를 차례로 누르고 데이터를 저장합니다.

3. 파일이 실행하도록 다음 명령을 사용합니다.

		chmod +x show_recommendations.py

4. Python 스크립트를 실행합니다.

		./show_recommendations.py 4 ml-100k/u.data ml-100k/u.item recommendations.txt

	이 사용자 ID 4에 대해 생성된 권장 사항을 살펴봅니다.

	* **u.data** 파일은 사용자가 등급을 평가하는 영화를 검색하는데 사용됩니다.
	* **u.item** 파일은 동영상의 이름을 검색하기 위해 사용됩니다.
	* **recommendations.txt**는 이 사용자에 대한 영화 추천을 검색하는데 사용됩니다

	이 명령의 출력은 다음과 유사합니다.

		Reading Movies Descriptions
		Reading Rated Movies
		Reading Recommendations
		Rated Movies
		------------------------
		Mimic (1997), rating=3
		Ulee's Gold (1997), rating=5
		Incognito (1997), rating=5
		One Flew Over the Cuckoo's Nest (1975), rating=4
		Event Horizon (1997), rating=4
		Client, The (1994), rating=3
		Liar Liar (1997), rating=5
		Scream (1996), rating=4
		Star Wars (1977), rating=5
		Wedding Singer, The (1998), rating=5
		Starship Troopers (1997), rating=4
		Air Force One (1997), rating=5
		Conspiracy Theory (1997), rating=3
		Contact (1997), rating=5
		Indiana Jones and the Last Crusade (1989), rating=3
		Desperate Measures (1998), rating=5
		Seven (Se7en) (1995), rating=4
		Cop Land (1997), rating=5
		Lost Highway (1997), rating=5
		Assignment, The (1997), rating=5
		Blues Brothers 2000 (1998), rating=5
		Spawn (1997), rating=2
		Wonderland (1997), rating=5
		In & Out (1997), rating=5
		------------------------
		Recommended Movies
		------------------------
		Seven Years in Tibet (1997), score=5.0
		Indiana Jones and the Last Crusade (1989), score=5.0
		Jaws (1975), score=5.0
		Sense and Sensibility (1995), score=5.0
		Independence Day (ID4) (1996), score=5.0
		My Best Friend's Wedding (1997), score=5.0
		Jerry Maguire (1996), score=5.0
		Scream 2 (1997), score=5.0
		Time to Kill, A (1996), score=5.0
		Rock, The (1996), score=5.0
		------------------------

##임시 데이터 삭제

Mahout 작업은 작업을 처리하는 동안 생성된 임시 데이터를 제거하지 않습니다. 이 `--tempDir` 매개 변수는 쉽게 삭제할 수 있도록 임시 파일을 특정 경로로 분리하기 위해 예제 작업에서 지정되었습니다. 임시 파일을 제거하려면 다음 명령을 사용합니다.

	hadoop fs -rm -f -r wasb:///temp/mahouttemp

> [AZURE.WARNING]임시 파일 또는 출력 파일을 제거하지 않으면, 작업을 다시 실행할 때 같은 `--tempDir` 경로에서 '파일을 덮어쓸 수 없습니다' 오류 메시지가 발생합니다.

##다음 단계

이제 Mahout을 사용하는 방법을 배웠으므로 HDInsight에서 데이터로 작업하는 다른 방법을 검색합니다.

* [HDInsight에서 Hive 사용](../hadoop-use-hive.md)
* [HDInsight에서 Pig 사용](../hadoop-use-pig.md)
* [HDInsight에서 MapReduce 사용](../hadoop-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
 

<!---HONumber=July15_HO4-->