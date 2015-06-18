<properties 
	pageTitle="클라우드 데이터 과학 프로세스에서 Hive 쿼리를 HDInsight Hadoop 클러스터에 제출 | Azure" 
	description="Hive 테이블에서 데이터 처리" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="hangzh;bradsev" /> 

#<a name="heading"></a> 클라우드 데이터 과학 프로세스에서 Hive 쿼리를 HDInsight Hadoop 클러스터에 제출

이 문서에서는 Azure의 HDInsight 서비스에서 관리하는 Hadoop 클러스터에 Hive 쿼리를 제출하는 다양한 방법에 대해 설명합니다. 다음을 사용하여 Hive 쿼리를 제출할 수 있습니다. 

* 클러스터 헤드 노드의 Hadoop 명령줄
* IPython Notebook 
* Hive 편집기
* Azure PowerShell 스크립트 

데이터를 탐색할 수 있거나 포함된 Hive UDF(사용자 정의 함수)를 사용하는 기능을 생성할 수 있는 일반 Hive 쿼리가 제공됩니다. 

[뉴욕시 택시 여행 데이터](http://chriswhong.com/open-data/foil_nyc_taxi/) 시나리오에 대한 쿼리 예제는 [Github 리포지토리](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)에 제공됩니다. 이러한 쿼리는 이미 데이터 스키마가 지정되어 있으며 바로 제출하여 실행할 수 있습니다. 

마지막 섹션에서는 사용자가 조정하여 Hive 쿼리 성능을 높일 수 있는 매개 변수에 대해 설명합니다.

## 필수 조건
이 문서에서는 사용자가 다음 작업을 수행한 것으로 가정합니다.
 
* Azure 저장소 계정을 만들었습니다. 지침이 필요한 경우 [Azure 저장소 계정 만들기](../hdinsight-get-started.md#storage)를 참조하세요. 
* Hadoop 클러스터에 HDInsight 서비스를 프로비전했습니다.  지침이 필요한 경우 [HDInsight 클러스터 프로비전](../hdinsight-get-started.md#provision)을 참조하세요.
* Azure HDInsight Hadoop 클러스터의 Hive 테이블에 데이터가 업로드되었습니다. 업로드되지 않은 경우 [데이터를 만들어서 Hive 테이블에 로드](machine-learning-data-science-hive-tables.md)의 지침에 따라 먼저 Hive 테이블에 데이터를 업로드하세요.
* 클러스터에 대한 원격 액세스가 설정되었습니다. 지침이 필요한 경우 [Hadoop 클러스터의 헤드 노드에 액세스](machine-learning-data-science-customize-hadoop-cluster.md#remoteaccess)를 참조하세요. 


- [Hive 쿼리를 제출하는 방법](#submit)
- [데이터 탐색 및 기능 엔지니어링](#explore)
- [고급 항목: Hive 매개 변수를 조정하여 쿼리 속도 개선](#tuning)

## <a name="submit"></a>Hive 쿼리를 제출하는 방법

###1. Hadoop 클러스터 헤드 노드에서 명령줄을 통해

쿼리가 복잡한 경우 Hadoop 클러스터의 헤드 노드에서 바로 Hive 쿼리를 제출하면 일반적으로 Hive 편집기 또는 Azure PowerShell 스크립트를 사용하여 제출하는 것보다 반환 시간이 빠릅니다. 

Hadoop 클러스터의 헤드 노드에 로그인하고, 헤드 노드 바탕 화면에서 Hadoop 명령줄을 열고 `cd %hive_home%\bin` 명령을 입력합니다.

사용자는 세 가지 방법으로 Hadoop 명령줄에서 Hive 쿼리를 제출할 수 있습니다. 

####Hadoop 명령줄에서 직접 Hive 쿼리를 제출합니다. 

사용자는 `hive -e "<사용자의 hive 쿼리>;` 같은 명령줄을 실행하여 Hadoop 명령줄에서 바로 간단한 Hive 쿼리를 제출할 수 있습니다. 다음은 그 예제입니다. 빨간색 상자는 Hive 쿼리를 제출하는 명령을, 녹색 상자는 Hive 쿼리의 출력을 보여 줍니다.

![Create workspace][10]

####.hql 파일로 Hive 쿼리 제출

Hive 쿼리가 좀 더 복잡하고 줄이 여러 개인 경우 명령줄 또는 Hive 명령 콘솔에서 쿼리를 편집하는 방법은 실용적이지 않습니다. 대신 Hadoop 클러스터의 헤드 노드에서 텍스트 편집기를 사용하여 헤드 노드의 로컬 디렉터리에 있는 .hql 파일에 Hive 쿼리를 저장합니다. 그러면 다음과 같이 `-f` 인수를 사용하여 .hql 파일의 Hive 쿼리를 제출할 수 있습니다.
	
	`hive -f "<path to the .hql file>"`

![Create workspace][15]


####Hive 쿼리의 진행 상태 화면 인쇄 숨기기

기본적으로 Hadoop 명령줄에서 Hive 쿼리가 제출되면 맵/감소 작업의 진행 상태가 화면에 인쇄됩니다. 맵/감소 작업의 진행 상태 화면 인쇄를 숨기려면 다음과 같이 명령줄에 `-S` 인수("S"는 대문자)를 사용합니다.

	hive -S -f "<path to the .hql file>"
	hive -S -e "<Hive queries>"

####Hive 명령 콘솔에서 Hive 쿼리를 제출합니다.

또한 사용자는 Hadoop 명령줄에서 `hive` 명령을 실행하여 Hive 명령 콘솔을 먼저 입력한 후 Hive 명령 콘솔에서 Hive 쿼리를 제출할 수 있습니다. 다음은 예제입니다. 이 예제에서 두 빨간색 상자는 각각 Hive 명령 콘솔을 입력하는 데 사용된 명령과 Hive 명령 콘솔에서 제출된 Hive 쿼리를 보여 줍니다. 녹색 상자는 Hive 쿼리의 출력을 보여 줍니다. 

![Create workspace][11]

이전 예제에서는 Hive 쿼리 결과가 화면에 바로 출력됩니다. 또한 사용자는 헤드 로드의 로컬 파일 또는 Azure blob에 출력을 작성할 수 있습니다. 그런 다음 사용자는 다른 도구를 사용하여 Hive 쿼리 출력을 추가로 분석할 수 있습니다.

####Hive 쿼리 결과를 로컬 파일에 출력합니다. 

Hive 쿼리 결과를 헤드 노드의 로컬 디렉터리에 출력하려면 사용자는 다음과 같이 Hadoop 명령줄에서 Hive 쿼리를 제출해야 합니다.

	`hive -e "<hive query>" > <local path in the head node>`

다음 예제에서 Hive 쿼리의 출력은 `C:\apps\temp` 디렉터리의 `hivequeryoutput.txt` 파일에 작성됩니다.

![Create workspace][12]

####Azure blob에 Hive 쿼리 결과 출력

사용자는 Hadoop 클러스터의 기본 컨테이너 내에 있는 Azure blob에 Hive 쿼리 결과를 출력할 수도 있습니다. Hive 쿼리는 다음과 같아야 합니다.

	`insert overwrite directory wasb:///<directory within the default container> <select clause from ...>`

다음 예제에서 Hive 쿼리는 Hadoop 클러스터의 기본 컨테이너 내에 있는 blob 디렉터리 `queryoutputdir`에 작성됩니다. 이때 사용자는 blob 이름 없이 디렉터리 이름만 입력하면 됩니다.  `wasb:///queryoutputdir/queryoutput.txt`처럼 디렉터리 이름과 blob 이름을 모두 입력하면 오류가 발생합니다. 

![Create workspace][13]

Azure 저장소 탐색기 같은 도구를 사용하여 Hadoop 클러스터의 기본 컨테이너를 열면 다음과 같은 Hive 쿼리 출력을 볼 수 있습니다. 필터(빨간색 상자로 강조 표시됨)를 적용하여 이름에 지정된 문자가 포함된 blob만 검색할 수 있습니다.

![Create workspace][14]

###2. Hive 편집기 또는 Azure PowerShell 명령을 통해

사용자는 웹 브라우저에 URL `https://<Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor`을 입력하여 쿼리 콘솔(Hive 편집기)을 사용하거나(로그인하려면 Hadoop 클러스터 자격 증명을 입력해야 함) [PowerShell을 사용하여 Hive 작업을 제출](../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell)할 수도 있습니다. 

## <a name="explore"></a>데이터 탐색, 기능 엔지니어링 및 Hive 매개 변수 조정

이번 섹션에서는 Azure HDInsight Hadoop 클러스터에 Hive를 사용하는 다음과 같은 데이터 랭글링 작업 그리고 일부 Hive 매개 변수를 조정하여 Hive 쿼리 성능을 개선하는 고급 항목에 대해 설명합니다.

1. [데이터 탐색](#hive-dataexploration)
2. [기능 생성](#hive-featureengineering)
3. [고급 항목: Hive 매개 변수를 조정하여 쿼리 속도 개선](#tune-parameters)

> [AZURE.NOTE] 샘플 Hive 쿼리에서는 Azure HDInsight Hadoop 클러스터의 Hive 테이블에 데이터가 업로드된 것으로 가정합니다. 업로드되지 않은 경우 [데이터를 만들어서 Hive 테이블에 로드](machine-learning-data-science-hive-tables.md) 의 지침에 따라 먼저 Hive 테이블에 데이터를 업로드하세요.

###<a name="hive-dataexploration"></a>데이터 탐색
다음은 Hive 테이블에서 데이터를 탐색할 때 사용할 수 있는 몇 가지 샘플 Hive 스크립트입니다.

1. 파티션당 관찰 수 가져오기
	`SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`

2. 일별 관찰 수 가져오기
	`SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);` 

3. 범주 열의 수준 가져오기  
	`SELECT  distinct <column_name> from <databasename>.<tablename>`

4. 두 범주 열 조합의 수준 수 가져오기 
	`SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`

5. 숫자 열의 분포 가져오기  
	`SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`

6. 두 조인 테이블의 레코드 추출 

	    SELECT 
			a.<common_columnname1> as <new_name1>,
			a.<common_columnname2> as <new_name2>,
    		a.<a_column_name1> as <new_name3>,
    		a.<a_column_name2> as <new_name4>,
    		b.<b_column_name1> as <new_name5>,
    		b.<b_column_name2> as <new_name6>
    	FROM
    		(
    		SELECT <common_columnname1>, 
    			<common_columnname2>,
				<a_column_name1>,
				<a_column_name2>,
			FROM <databasename>.<tablename1>
			) a
			join
			(
			SELECT <common_columnname1>, 
    			<common_columnname2>,
				<b_column_name1>,
				<b_column_name2>,
			FROM <databasename>.<tablename2>
			) b
			ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

###<a name="hive-featureengineering"></a>기능 생성

이 섹션에서는 Hive 쿼리를 사용하여 기능을 생성하는 방법에 대해 설명합니다. 

> [AZURE.NOTE]
> 추가 기능을 생성한 후 기존 테이블에 열로 추가하거나 추가 기능 및 기본 키를 사용하여 새 테이블을 만들어서 원래 테이블과 조인할 수 있습니다.  

1. [빈도 기반 기능 생성](#hive-frequencyfeature)
2. [이진 분류에서 범주 변수의 위험](#hive-riskfeature)
3. [날짜/시간 필드에서 기능 추출](#hive-datefeatures)
4. [텍스트 필드에서 기능 추출](#hive-textfeatures)
5. [GPS 좌표 사이의 거리 계산](#hive-gpsdistance)

####<a name="hive-frequencyfeature"></a>빈도 기반 기능 생성

범주 변수의 빈도 수준 또는 여러 범주 변수 조합의 빈도 수준을 계산하는 것이 매우 유용한 경우가 있습니다. 사용자는 다음 스크립트를 사용하여 빈도를 계산할 수 있습니다.

		select 
			a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
		from
		(
			select 
				<column_name1>,<column_name2>, count(*) as sub_count 
			from <databasename>.<tablename> group by <column_name1>, <column_name2>
		)a
		order by frequency desc;
	

####<a name="hive-riskfeature"></a>이진 분류에서 범주 변수의 위험

이진 분류에서 숫자가 아닌 수준을 숫자 위험으로 바꿔서 숫자가 아닌 범주 변수를 숫자 기능으로 변환해야 할 때가 종종 있습니다. 일부 모델은 숫자 기능만 사용할 수 있기 때문입니다. 이 섹션에서는 범주 변수의 위험 값(로그 odd)을 계산하는 일반 Hive 쿼리를 보여 줍니다. 


	    set smooth_param1=1;
	    set smooth_param2=20;
	    select 
	    	<column_name1>,<column_name2>, 
			ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
	    from
	    	(
	    	select 
	    		<column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
	    	from
	    		(
	    		select 
	    			<column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
	    		from <databasename>.<tablename> 
	    		)a
	    	group by <column_name1>, <column_name2>
	    	)b 

이 예제에서 변수 `smooth_param1` 및 `smooth_param2`는 데이터에서 계산된 위험 값을 부드럽게 만들도록 설정되었습니다. 위험 범위는 -Inf~Inf입니다. 위험>0은 대상이 1일 확률이 0.5보다 크다는 뜻입니다. 

위험 테이블이 계산되면 사용자는 위험 값을 위험 테이블에 조인하여 위험 값을 할당할 수 있습니다. Hive 조인 쿼리는 이전 섹션에서 제공되었습니다.

####<a name="hive-datefeature"></a>날짜/시간 필드에서 기능 추출

Hive는 날짜/시간 필드를 처리할 수 있는 UDF가 함께 제공됩니다. Hive의 기본 날짜/시간 형식은 'yyyy-MM-dd 00:00:00'입니다(예: '1970-01-01 12:21:32'). 이 섹션에서는 날짜/시간 필드에서 일 및 월을 추출하는 예제와 기본 형식이 아닌 다른 형식으로 된 날짜/시간 문자열을 기본 형식의 날짜/시간 문자열로 변환하는 예제를 보여 줍니다. 

    	select day(<datetime field>), month(<datetime field>) 
		from <databasename>.<tablename>;

이 Hive 쿼리에서는 `<날짜/시간 필드>`가 기본 날짜/시간 형식으로 되어 있다고 가정합니다.

날짜/시간 필드가 기본 형식이 아닌 경우 먼저 날짜/시간 필드를 Unix 타임스탬프로 변환한 후 Unix 타임스탬프를 기본 형식의 날짜/시간 문자열로 변환해야 합니다. 날짜/시간이 기본 형식으로 변환되면 포함된 날짜/시간 UDF를 적용하여 기능을 추출할 수 있습니다.

		select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
		from <databasename>.<tablename>;

이 쿼리에서 `<날짜/시간 필드>`의 패턴이 `03/26/2015 12:04:39`이면 `'<날짜/시간 필드의 패턴>'`은 `'MM/dd/yyyy HH:mm:ss'`여야 합니다. 이를 테스트하려면 다음 명령을 실행합니다.

		select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
		from hivesampletable limit 1;

이 쿼리에서 `hivesampletable`은 기본적으로 클러스터가 프로비전될 때 모든 Azure HDInsight Hadoop 클러스터가 함께 제공됩니다. 


####<a name="hive-textfeature"></a>텍스트 필드에서 기능 추출

Hive 테이블에 텍스트 필드가 있고 이 텍스트 필드에 공백으로 구분된 단어 문자열이 포함되어 있으면 다음 쿼리는 문자열의 길이와 문자열에 포함된 단어 수를 추출합니다.

    	select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num 
		from <databasename>.<tablename>;

####<a name="hive-gpsdistance"></a>GPS 좌표 사이의 거리 계산

이 섹션에 제공된 쿼리를 뉴욕시 택시 여행 데이터에 바로 적용할 수 있습니다. Hive에 포함된 수학 함수를 적용하여 기능을 생성하는 방법을 보여 주는 것이 이 쿼리의 목적입니다. 

이 쿼리에 사용된 필드는 태우는 위치와 내리는 위치의 GPS 좌표이며 이름은 pickup_longitude, pickup_latitude, dropoff_longitude 및 dropoff_latitude입니다. 태우는 좌표와 내리는 좌표 사이의 직접 거리를 계산하는 쿼리는 다음과 같습니다.

		set R=3959;
		set pi=radians(180);
		select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, 
			${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
			*${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
			*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
			/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
			+cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
			pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance 
		from nyctaxi.trip 
		where pickup_longitude between -90 and 0
		and pickup_latitude between 30 and 90
		and dropoff_longitude between -90 and 0
		and dropoff_latitude between 30 and 90
		limit 10; 

두 GPS 좌표 사이의 거리를 계산하는 수학 방정식은 [여기](http://www.movable-type.co.uk/scripts/latlong.html)서 확인할 수 있습니다. 작성자는 Peter Lapisu입니다. 그의 Javascript에서 toRad() 함수는 단지 `lat_or_lon*pi/180`입니다.이 함수는 각도를 라디안으로 변환하며, `lat_or_lon`은 위도 또는 경도입니다. Hive에서 `atan2` 함수는 제공하지 않지만 `atan` 함수를 제공하므로 위의 Hive 쿼리에서는 [Wikipedia](http://en.wikipedia.org/wiki/Atan2)의 정의에 따라 `atan` 함수를 이용하여 `atan2`함수를 구현했습니다. 

![Create workspace][1]

Hive 포함 UDF의 전체 목록은 [여기](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions)서 확인할 수 있습니다. 

## <a name="tuning"></a> 고급 항목: Hive 매개 변수를 조정하여 쿼리 속도 개선

Hive 클러스터의 기본 매개 변수 설정이 Hive 쿼리 및 쿼리에서 처리하는 데이터에 적합하지 않을 수 있습니다. 이 섹션에서는 사용자가 조정하여 Hive 쿼리 성능을 개선할 수 있는 일부 매개 변수에 대해 설명합니다. 사용자는 매개 변수 조정 쿼리를 먼저 추가한 후 데이터 처리 쿼리를 추가해야 합니다. 

1. Java 힙 공간 : 대용량 데이터 집합 조인 또는 킨 레코드 처리와 관련된 쿼리에서 가장 흔히 발생하는 오류는 **힙 공간 부족**입니다.  `mapreduce.map.java.opts` 및 `mapreduce.task.io.sort.mb` 매개 변수를 원하는 값으로 설정하여 이 문제를 해결할 수 있습니다. 다음은 예제입니다.

		set mapreduce.map.java.opts=-Xmx4096m;
		set mapreduce.task.io.sort.mb=-Xmx1024m;
	

	이 매개 변수는 Java 힙 공간에 4GB 메모리를 할당하고 정렬에 추가 메모리를 할당하여 정렬 작업의 효율성을 높입니다. 힙 공간과 관련된 작업 실패 오류가 발생할 경우 이러한 할당 방법을 사용하면 좋습니다.

2. DFS 블록 크기: 이 매개 변수는 파일 시스템에서 저장하는 최소 데이터 단위를 설정합니다. 예를 들어 DFS 블록 크기가 128MB이면 크기가 128MB 이하인 모든 데이터가 단일 블록에 저장되고 128MB보다 큰 데이터에는 추가 블록이 할당됩니다. 블록 크기를 너무 작게 할 경우 이름 노드에서 해당 파일과 관련된 블록을 찾으려면 훨씬 많은 요청을 처리해야 하므로 Hadoop에서 큰 오버헤드가 발생합니다. 기가바이트 단위(또는 그 이상) 데이터를 처리할 때 권장하는 설정은 다음과 같습니다.

		set dfs.block.size=128m;

3. Hive의 조인 작업 최적화: 맵/감소 프레임워크의 조인 작업은 일반적으로 감소 단계에서 발생하지만 맵 단계("맵조인"이라고도 함)에서 조인 작업을 예약하여 엄청난 성과를 얻을 수 있는 경우도 있습니다. 가능하면 Hive에서 이 작업을 수행하도록 하려면 다음과 같이 설정합니다.

		set hive.auto.convert.join=true;

4. Hive 매퍼 수 지정: Hadoop은 사용자가 감소기 수를 설정할 수 있지만 일반적으로 매퍼 수는 사용자가 설정하지 않습니다. 한 가지 팁을 드리자면 Hadoop 변수인 mapred.min.split.size 및 mapred.max.split.size를 선택하여 매퍼 수를 어느 정도 조절할 수 있습니다. 각 맵 작업의 크기가 아래와 같은 방법으로 결정되기 때문입니다.

		num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))

	일반적으로 mapred.min.split.size의 기본값은 0, mapred.max.split.size의 기본값은 Long.MAX, dfs.block.size의 기본값은 64MB입니다. 보시는 것처럼 데이터 크기를 고려하려 이러한 매개 변수를 "설정"하면 사용되는 매퍼 수를 조정할 수 있습니다. 

5. 아래에 Hive 성능을 최적화하는 고급 옵션 몇 가지가 추가로 설명되어 있습니다. 이러한 옵션을 사용하여 맵 및 감소 작업에 할당된 메모리를 설정할 수 있으며 성능 조정 시 유용하게 활용할 수 있습니다. 주의할 사항으로, `mapreduce.reduce.memory.mb`가 Hadoop 클러스터에 있는 각 작업자 노드의 실제 메모리 크기보다 크면 안 됩니다.

		set mapreduce.map.memory.mb = 2048;
		set mapreduce.reduce.memory.mb=6144;
		set mapreduce.reduce.java.opts=-Xmx8192m;
		set mapred.reduce.tasks=128;
		set mapred.tasktracker.reduce.tasks.maximum=128;

[1]: ./media/machine-learning-data-science-hive-queries/atan2new.png
[10]: ./media/machine-learning-data-science-hive-queries/run-hive-queries-1.png
[11]: ./media/machine-learning-data-science-hive-queries/run-hive-queries-2.png
[12]: ./media/machine-learning-data-science-hive-queries/output-hive-results-1.png
[13]: ./media/machine-learning-data-science-hive-queries/output-hive-results-2.png
[14]: ./media/machine-learning-data-science-hive-queries/output-hive-results-3.png
[15]: ./media/machine-learning-data-science-hive-queries/run-hive-queries-3.png



<!--HONumber=49--> 