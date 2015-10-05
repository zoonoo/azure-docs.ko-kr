<properties
	pageTitle="Azure HDInsight에서 Spark 클러스터 프로비전 및 Zeppelin과 Jupyter에서 Spark SQL을 사용하여 대화형 분석 | Microsoft Azure"
	description="HDInsight에서 Apache Spark 클러스터를 신속하게 프로비전한 후 Zeppelin 및 Jupyter 노트북에서 Spark SQL을 사용하여 대화형 쿼리를 실행하는 방법에 대한 단계별 지침입니다."
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/07/2015"
	ms.author="nitinme"/>


# 시작: Azure HDInsight에서 Apache Spark 프로비전 및 Spark SQL을 사용하여 대화형 쿼리 실행

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [HDInsight에서 Apache Spark 프로비전 및 Spark SQL을 사용하여 대화형 쿼리 실행](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1.md)

빠른 생성 옵션을 사용하여 HDInsight에서 Apache Spark 클러스터를 프로비전한 후 웹 기반 [Zeppelin](https://zeppelin.incubator.apache.org) 및 [Jupyter](https://jupyter.org) 노트북을 사용하여 Spark 클러스터에서 Spark SQL 대화형 쿼리를 실행하는 방법을 알아봅니다.


   ![HDInsight에서 Apache Spark 사용 시작](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.GetStartedFlow.Spark.png "HDInsight에서 Apache Spark 사용 시작 자습서입니다. 설명된 단계: 저장소 계정 만들기, 클러스터 프로비전, Spark SQL 문 실행")

**필수 조건:**

이 자습서를 시작하기 전에 Azure 구독이 있어야 합니다. [Azure 무료 평가판](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.


## <a name="provision"></a>HDInsight Spark 클러스터 프로비전

이 섹션에서는 Spark 버전 1.3.1을 기반으로 하는 HDInsight 버전 3.2 클러스터를 프로비전합니다. HDInsight 버전 및 해당 SLA에 대한 내용은 [HDInsight 구성 요소 버전 관리](hdinsight-component-versioning.md)를 참조하세요.

>[AZURE.NOTE]이 문서의 단계에서는 기본 구성 설정을 사용하여 HDInsight에서 Apache Spark 클러스터를 만듭니다. 추가 저장소, Azure 가상 네트워크 또는 Hive용 Metastore를 사용하는 등의 기타 클러스터 구성 설정에 대한 자세한 내용은 [사용자 지정 옵션을 사용하여 HDInsight 클러스터 프로비전](hdinsight-apache-spark-provision-clusters.md)을 참조하세요.


**Spark 클러스터를 프로비전하려면**

1. [Azure Preview 포털](https://ms.portal.azure.com/)에 로그인합니다.

2. **새로 만들기**, **데이터 + 분석** 및 **HDInsight**를 차례로 클릭합니다.

    ![Azure Preview 포털에서 새 클러스터 만들기](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.CreateCluster.1.png "Azure Preview 포털에서 새 클러스터 만들기")

3. **클러스터 이름**을 입력하고 **클러스터 형식**으로 **Hadoop**을 선택하고 **클러스터 운영 체제** 드롭다운 메뉴에서 **Windows Server 2012 R2 Datacenter**를 선택합니다. 클러스터 이름을 사용할 수 있는 경우 클러스터 이름 옆에 녹색 확인 표시가 나타납니다.

	![클러스터 이름 및 유형 입력](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.CreateCluster.2.png "클러스터 이름 및 유형 입력")

4. 둘 이상의 구독이 있는 경우 **구독** 항목을 클릭하여 클러스터에 사용할 Azure 구독을 선택합니다.

5. **리소스 그룹**을 클릭하여 기존 리소스 그룹 목록을 확인하고 클러스터를 만들 리소스 그룹을 선택합니다. 또는 **새로 만들기**를 클릭한 다음 새 리소스 그룹의 이름을 입력할 수 있습니다. 새 그룹 이름을 사용할 수 있는지 여부를 나타내는 녹색 확인 표시가 나타납니다.

	> [AZURE.NOTE]사용할 수 있는 경우 이 항목은 기존 리소스 그룹 중 하나로 기본 설정됩니다.

6. **자격 증명**을 클릭한 다음 **클러스터 로그인 사용자 이름** 및 **클러스터 로그인 암호**를 입력합니다. 클러스터 노드에 대해 원격 데스크톱을 사용하도록 설정하려면 **원격 데스크톱 사용**에 대해 **예**를 클릭한 다음 필요한 값을 지정합니다. 이 자습서에서는 원격 데스크톱이 필요하지 않으므로 이를 건너뛸 수 있습니다. 아래쪽의 **선택**을 클릭하여 자격 증명 구성을 저장합니다.

	![클러스터 자격 증명 제공](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.CreateCluster.3.png "클러스터 자격 증명 제공")

7. **데이터 원본**을 클릭하여 클러스터의 기존 데이터 원본을 선택하거나 새로 만듭니다. HDInsight의 Hadoop 클러스터를 프로비전할 때 Azure 저장소 계정을 지정합니다. Hadoop 분산 파일 시스템(HDFS)의 경우처럼 해당 계정의 특정 Blob 저장소 컨테이너는 기본 파일 시스템으로 지정됩니다. HDInsight 클러스터는 기본적으로 사용자가 지정한 저장소 계정과 동일한 데이터 센터에 프로비전됩니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용][hdinsight-storage]을 참조하세요.

	![데이터 원본 블레이드](./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.CreateCluster.4.png "데이터 원본 구성 제공")

	현재 HDInsight 클러스터의 데이터 원본으로 Azure 저장소 계정을 선택할 수 있습니다. 다음을 사용하여 **데이터 원본** 블레이드의 항목을 이해합니다.

	- **선택 방법**: 모든 구독에서 저장소 계정을 찾을 수 있도록 하려면 이 항목을 **From all subscriptions(모든 구독에서)**로 설정합니다. 기존 저장소 계정의 **저장소 이름** 및 **액세스 키**를 입력하려면 이 항목을 **액세스 키**로 설정합니다.

	- **저장소 계정 선택/새로 만들기**: 클러스터와 연결할 기존 저장소 계정을 찾아 선택하려면 **저장소 계정 선택**을 클릭합니다. 또는 새 저장소 계정을 만들려면 **새로 만들기**를 클릭합니다. 저장소 계정의 이름을 입력할 때 나타나는 필드를 사용합니다. 이름을 사용할 수 있는 경우 녹색 확인 표시가 나타납니다.

	- **기본 컨테이너 선택**: 클러스터에 사용할 기본 컨테이너의 이름을 입력하려면 이 항목을 사용합니다. 여기에 아무 이름이나 입력할 수 있지만, 컨테이너가 이 특정 클러스터에 사용됨을 쉽게 인식할 수 있도록 클러스터와 같은 이름을 사용하는 것이 좋습니다.

	- **위치**: 저장소 계정이 있거나 저장소 계정을 만들 지역입니다.

		> [AZURE.IMPORTANT]기본 데이터 원본의 위치를 선택하면 HDInsight 클러스터의 위치도 설정됩니다. 클러스터와 기본 데이터 원본은 같은 지역에 있어야 합니다.

	**선택**을 클릭하여 데이터 원본 구성을 저장합니다.

8. **노드 가격 책정 계층**을 클릭하여 이 클러스터에 대해 만들어질 노드에 대한 정보를 표시합니다. 클러스터에 필요한 작업자 노드 수를 설정합니다. 클러스터의 예상 비용이 블레이드 내에 표시됩니다.

	![노드 가격 책정 계층 블레이드](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.CreateCluster.5.png "클러스터 노드 수 지정")

	**선택**을 클릭하여 노드 가격 책정 구성을 저장합니다.

9. **새 HDInsight 클러스터** 블레이드에서 **시작 보드에 고정**이 선택되어 있는지 확인한 다음 **만들기**를 클릭합니다. 그러면 클러스터가 만들어지고 Azure 포털의 시작 보드에 클러스터 타일이 추가됩니다. 아이콘이 클러스터를 프로비전 중임을 나타내고 프로비전이 완료되면 변경되어 HDInsight 아이콘을 표시합니다.

	| 프로비전 중 | 프로비전 완료 |
	| ------------------ | --------------------- |
	| ![시작 보드에 표시기 프로비전](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/provisioning.png) | ![프로비전된 클러스터 타일](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/provisioned.png) |

	> [AZURE.NOTE]클러스터를 만드는데 약간의 시간이 걸리며, 일반적으로 약 15분이 소요됩니다. 시작 보드에 있는 타일 또는 페이지 왼쪽에 있는 **알림** 항목을 사용하여 프로비전 프로세스를 확인하세요.

10. 프로비전이 완료되면 시작 보드에서 Spark 클러스터 타일을 클릭하여 클러스터 블레이드를 시작합니다.


## <a name="zeppelin"></a>Zeppelin 노트북을 사용하여 대화형 Spark SQL 쿼리 실행

클러스터를 프로비전한 후 웹 기반 Zeppelin 노트북을 사용하여 Spark HDInsight 클러스터에 대해 Spark SQL 대화형 쿼리를 실행할 수 있습니다. 이 섹션에서는 클러스터에서 기본적으로 사용 가능한 샘플 데이터 파일(hvac.csv)을 사용하여 대화형 Spark SQL 쿼리를 실행합니다.

>[AZURE.NOTE]아래 지침에 따라 만드는 노트북은 클러스터에서 기본적으로 사용 가능합니다. Zeppelin을 시작한 후 **Zeppelin HVAC 자습서** 이름으로 이 노트북을 찾습니다.

1. Zeppelin 노트북을 시작합니다. Spark 클러스터 블레이드에서 **빠른 연결**을 클릭한 다음 **클러스터 대시보드** 블레이드에서 **Zeppelin 노트북**을 클릭합니다. 메시지가 표시되면 클러스터에 대한 관리자 자격 증명을 입력합니다. 열리는 페이지의 지시에 따라 노트북을 시작합니다.

2. 새 노트북을 만듭니다. 헤더 창에서 **노트북**을 클릭하고 **새 메모 만들기**를 클릭합니다.

	![새 Zeppelin 노트북 만들기](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.CreateNewNote.png "새 Zeppelin 노트북 만들기")

	같은 페이지의 **노트북** 제목 아래에 **Note XXXXXXXXX**로 시작하는 이름의 새 노트북이 표시됩니다. 새 노트북을 클릭합니다.

3. 새 노트북에 대한 웹 페이지에서 제목을 클릭하고 원하는 경우 노트북의 이름을 변경합니다. Enter 키를 눌러 변경된 이름을 저장합니다. 또한 노트북 헤더의 오른쪽 위 모서리에 **연결됨** 상태가 표시되는지 확인합니다.

	![Zeppelin 노트북 상태](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.NewNote.Connected.png "Zeppelin 노트북 상태")

4. 샘플 데이터를 임시 테이블에 로드합니다. HDInsight에서 Spark 클러스터를 프로비전하면 샘플 데이터 파일인 **hvac.csv**가 **\\HdiSamples\\SensorSampleData\\hvac** 아래 연결된 저장소 계정에 복사됩니다.

	새 노트북에 기본적으로 만들어지는 빈 단락에 다음 코드를 붙여넣습니다.

		// Create an RDD using the default Spark context, sc
		val hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")

		// Define a schema
		case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)

		// Map the values in the .csv file to the schema
		val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
    		s => Hvac(s(0),
            		s(1),
            		s(2).toInt,
            		s(3).toInt,
            		s(6)
        	)
		).toDF()

		// Register as a temporary table called "hvac"
		hvac.registerTempTable("hvac")

	키보드에서 **SHIFT + ENTER**를 누르거나 단락에 대한 **재생** 단추를 클릭하여 코드를 실행합니다. 단락의 오른쪽 위 모서리에 있는 상태가 준비, 보류 중, 실행 중, 완료 순서로 진행됩니다. 출력은 같은 단락 하단에 나타납니다. 스크린샷은 다음과 같습니다.

	![원시 데이터에서 임시 테이블 만들기](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Note.LoadDataIntoTable.png "원시 데이터에서 임시 테이블 만들기")

	또한 각 단락에 제목을 제공할 수도 있습니다. 오른쪽 모서리에서 **설정** 아이콘을 클릭하고 **제목 표시**를 클릭합니다.

5. 이제 **hvac** 테이블에서 Spark SQL 문을 실행할 수 있습니다. 새 단락에 다음 쿼리를 붙여넣습니다. 이 쿼리는 지정된 날짜에서 각 건물에 대한 건물 ID와 대상 및 실제 온도 간의 차이를 검색합니다. **Shift + Enter**를 누릅니다.

		%sql
		select buildingID, (targettemp - actualtemp) as temp_diff, date
		from hvac
		where date = "6/1/13"

	시작 부분의 **%sql** 문은 노트북에서 Spark SQL 인터프리터를 사용함을 알려줍니다. 노트북 헤더의 **인터프리터** 탭에서 정의된 인터프리터를 확인할 수 있습니다.

	다음 스크린샷은 출력을 보여 줍니다.

	![노트북을 사용하여 Spark SQL 문 실행](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Note.SparkSQLQuery1.png "노트북을 사용하여 Spark SQL 문 실행")

	동일한 출력에 대해 서로 다른 표현 간을 전환하려면 표시 옵션(사각형으로 강조 표시됨)을 클릭합니다. **설정**을 클릭하여 출력에서 키 및 값을 구성하는 항목을 선택합니다. 위 화면 캡처에서는 **buildingID**를 키로, **temp\_diff**의 평균을 값으로 사용합니다.

6. 또한 쿼리에 변수를 사용하여 Spark SQL 문을 실행할 수도 있습니다. 다음 코드 예제에서는 쿼리할 수 있는 값이 포함된 쿼리에 변수 **Temp**를 정의하는 방법을 보여 줍니다. 쿼리를 처음 실행하면 드롭다운이 변수에 지정한 값으로 자동으로 채워집니다.

		%sql
		select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
		from hvac
		where targettemp > "${Temp = 65,65|75|85}"

	새 단락에 이 코드를 붙여넣고 **SHIFT + ENTER**를 누릅니다. 다음 스크린샷은 출력을 보여 줍니다.

	![노트북을 사용하여 Spark SQL 문 실행](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Note.SparkSQLQuery2.png "노트북을 사용하여 Spark SQL 문 실행")

	후속 쿼리에 대해서는 드롭다운에서 새 값을 선택하고 쿼리를 다시 실행할 수 있습니다. **설정**을 클릭하여 출력에서 키 및 값을 구성하는 항목을 선택합니다. 위 화면 캡처에서는 **buildingID**를 키로, **temp\_diff**의 평균을 값으로, **targettemp**를 그룹으로 사용합니다.

7. Spark SQL 인터프리터를 다시 시작하여 응용 프로그램을 종료합니다. 맨 위쪽에서 **인터프리터** 탭을 클릭하고 Spark 인터프리터에 대해 **다시 시작**을 클릭합니다.

	![Zeppelin 인터프리터 다시 시작](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Zeppelin.Restart.Interpreter.png "Zeppelin 인터프리터 다시 시작")

## <a name="jupyter"></a>Jupyter 노트북을 사용하여 Spark SQL 쿼리 실행

이 섹션에서는 Jupyter 노트북을 사용하여 Spark 클러스터에 대해 Spark SQL 쿼리를 실행합니다.

>[AZURE.NOTE]아래 지침에 따라 만드는 노트북은 클러스터에서 기본적으로 사용 가능합니다. Jupyter를 시작한 후 **HVACTutorial.ipynb** 이름으로 이 노트북을 찾습니다.

1. Jupyter 노트북을 시작합니다. Spark 클러스터 블레이드에서 **빠른 연결**을 클릭한 다음 **클러스터 대시보드** 블레이드에서 **Jupyter 노트북**을 클릭합니다. 메시지가 표시되면 Spark 클러스터에 대한 관리자 자격 증명을 입력합니다.

2. 새 노트북을 만듭니다. **새로 만들기**를 클릭한 다음 **Python2**를 클릭합니다.

	![새 Jupyter 노트북 만들기](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Note.Jupyter.CreateNotebook.png "새 Jupyter 노트북 만들기")

3. 새 노트북이 만들어지고 Untitled.pynb 이름으로 열립니다. 맨 위에서 노트북 이름을 클릭하고 식별하기 쉬운 이름을 입력합니다.

	![노트북에 대한 이름 제공](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Note.Jupyter.Notebook.Name.png "노트북에 대한 이름 제공")

4. 필요한 모듈을 가져오고 Spark 및 SQL 컨텍스트를 만듭니다. 빈 셀에 다음 코드를 붙여넣은 다음 **SHIFT + ENTER**를 누릅니다.

		from pyspark import SparkContext
		from pyspark.sql import SQLContext
		from pyspark.sql.types import *

		# Create Spark and SQL contexts
		sc = SparkContext('spark://headnodehost:7077', 'pyspark')
		sqlContext = SQLContext(sc)

	Jupyter에서 작업을 실행할 때마다, 웹 브라우저 창 제목에 노트북 제목과 함께 **(사용 중)** 상태가 표시됩니다. 또한 오른쪽 위 모서리에 있는 **Python 2** 텍스트 옆에 단색 원도 표시됩니다. 작업이 완료되면 속이 빈 원으로 변경됩니다.

	 ![Jupyter 노트북 작업의 상태](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Jupyter.Job.Status.png "Jupyter 노트북 작업의 상태")

4. 샘플 데이터를 임시 테이블에 로드합니다. HDInsight에서 Spark 클러스터를 프로비전하면 샘플 데이터 파일인 **hvac.csv**가 **\\HdiSamples\\SensorSampleData\\hvac** 아래 연결된 저장소 계정에 복사됩니다.

	빈 셀에서 다음 코드 예제를 붙여넣고 **SHIFT + ENTER**를 누릅니다. 이 코드 예제는 **hvac**라는 임시 테이블에 데이터로 등록됩니다.

		# Load the data
		hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")

		# Create the schema
		hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

		# Parse the data in hvacText
		hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

		# Create a data frame
		hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

		# Register the data fram as a table to run queries against
		hvacdf.registerAsTable("hvac")

		# Run queries against the table and display the data
		data = sqlContext.sql("select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"")
		data.show()

5. 작업이 성공적으로 완료되면 다음 출력이 표시됩니다.

		buildingID temp_diff date  
		4          8         6/1/13
		3          2         6/1/13
		7          -10       6/1/13
		12         3         6/1/13
		7          9         6/1/13
		7          5         6/1/13
		3          11        6/1/13
		8          -7        6/1/13
		17         14        6/1/13
		16         -3        6/1/13
		8          -8        6/1/13
		1          -1        6/1/13
		12         11        6/1/13
		3          14        6/1/13
		6          -4        6/1/13
		1          4         6/1/13
		19         4         6/1/13
		19         12        6/1/13
		9          -9        6/1/13
		15         -10       6/1/13

6. 커널을 다시 시작하여 응용 프로그램을 종료합니다. 상단 메뉴 모음에서 **커널**을 클릭하고 **다시 시작**을 클릭한 다음 프롬프트에서 **다시 시작**을 한 번 더 클릭합니다.

	![Jupyter 커널 다시 시작](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Jupyter.Restart.Kernel.png "Jupyter 커널 다시 시작")


## <a name="seealso"></a>참고 항목


* [개요: Azure HDInsight에서 Apache Spark](hdinsight-apache-spark-overview.md)
* [HDInsight 클러스터에서 Spark 프로비전](hdinsight-apache-spark-provision-clusters.md)
* [BI 도구와 함께 HDInsight에서 Spark를 사용하여 대화형 데이터 분석 수행](hdinsight-apache-spark-use-bi-tools.md)
* [HDInsight에서 Spark를 사용하여 기계 학습 응용 프로그램 빌드](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [HDInsight에서 Spark를 사용하여 실시간 스트리밍 응용 프로그램 빌드](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)
* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=Sept15_HO4-->