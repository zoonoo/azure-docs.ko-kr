<properties
   	pageTitle="Linux 자습서: Hadoop 및 Hive로 시작 | Microsoft Azure"
   	description="HDInsight에서 Hadoop을 사용하여 시작하려면 이 Linux 자습서를 수행합니다. Linux 클러스터를 프로비전하고 Hive를 사용하여 데이터를 쿼리하는 방법에 알아봅니다."
   	services="hdinsight"
   	documentationCenter=""
   	authors="nitinme"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="hero-article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="01/13/2016"
   	ms.author="nitinme"/>

# Hadoop 자습서: Linux의 HDInsight에서 Hive와 Hadoop 사용 시작

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-tutorial-get-started-windows.md)
- [Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

이 문서에서는 Linux 기반의 Azure HDInsight를 신속하게 시작할 수 있도록 Linux 기반 Hadoop 클러스터를 만들고, Ambari 웹 UI를 연 다음 Ambari Hive 뷰를 사용하여 Hive 쿼리를 실행하는 방법을 보여 드립니다.

> [AZURE.NOTE]Hadoop과 빅 데이터를 처음 사용하는 경우 [Apache Hadoop](http://go.microsoft.com/fwlink/?LinkId=510084), [MapReduce](http://go.microsoft.com/fwlink/?LinkId=510086), [Hadoop Distributed File System(HDFS)](http://go.microsoft.com/fwlink/?LinkId=510087) 및 [Hive](http://go.microsoft.com/fwlink/?LinkId=510085) 용어에 대해 자세히 알아볼 수 있습니다. HDInsight를 통해 Azure에서 Hadoop을 사용하도록 설정하는 방법을 이해하려면 [HDInsight의 Hadoop 소개](hdinsight-hadoop-introduction.md)(영문)를 참조하세요.

## 필수 조건

이 Hadoop용 Linux 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**: [Azure 무료 평가판 받기](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)를 참조하세요.

## <a name="provision"></a>Linux에서 HDInsight 클러스터 프로비전

클러스터를 프로비전할 때는 Hadoop서비스 및 리소스를 포함하는 Azure 계산 리소스를 만듭니다. 이 섹션에서는 Hadoop 버전 2.2를 포함하는 HDInsight 버전 3.2 클러스터를 프로비전합니다. HDInsight 버전 및 해당 SLA에 대한 내용은 [HDInsight 구성 요소 버전 관리](hdinsight-component-versioning.md)를 참조하세요. HDInsight 클러스터 만들기에 대한 더 자세한 내용은 [사용자 옵션을 사용한 HDInsight 클러스터 프로비전][hdinsight-provision]을 참조하세요.

>[AZURE.NOTE]Windows Server 운영 체제를 실행하는 Hadoop 클러스터를 만들 수도 있습니다. 자세한 내용은 [Windows에서 HDInsight 시작](hdinsight-hadoop-tutorial-get-started-windows.md)을 참조하세요.

다음 단계에 따라 새 클러스터를 만듭니다.

1. [Azure 포털](https://ms.portal.azure.com/)에 로그인합니다.
2. **새로 만들기**, **데이터 분석** 및 **HDInsight**를 차례로 클릭합니다.

    ![Azure 포털에서 새 클러스터 만들기](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.1.png "Azure 포털에서 새 클러스터 만들기")

3. **클러스터 이름**을 입력하고 **클러스터 형식**으로 **Hadoop**을 선택하고 **클러스터 운영 체제** 드롭다운에서 **Ubuntu**를 선택합니다. 클러스터 이름을 사용할 수 있는 경우 클러스터 이름 옆에 녹색 확인 표시가 나타납니다.

	![클러스터 이름 및 유형 입력](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.2.png "클러스터 이름 및 유형 입력")

4. 둘 이상의 구독이 있는 경우 **구독** 항목을 클릭하여 클러스터에 사용할 Azure 구독을 선택합니다.

5. **리소스 그룹**을 클릭하여 기존 리소스 그룹 목록을 표시한 다음 클러스터를 만들 리소스 그룹을 선택합니다. 또는 **새로 만들기**를 클릭한 다음 새 리소스 그룹의 이름을 입력할 수 있습니다. 새 그룹 이름을 사용할 수 있는지 여부를 나타내는 녹색 확인 표시가 나타납니다.

	> [AZURE.NOTE]사용할 수 있는 경우 이 항목은 기존 리소스 그룹 중 하나로 기본 설정됩니다.

6. **자격 증명**을 클릭한 다음 관리자의 암호를 입력합니다. 또한 **SSH 사용자 이름**을 사용해야 합니다. **SSH 인증 형식**의 경우 **암호**를 클릭하고 SSH 사용자에 대한 암호를 지정합니다. 아래쪽의 **선택**을 클릭하여 자격 증명 구성을 저장합니다.

	![클러스터 자격 증명 제공](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.3.png "클러스터 자격 증명 제공")

    > [AZURE.NOTE]SSH는 명령줄을 사용하여 HDInsight 클러스터에 원격으로 액세스하는 데 사용됩니다. 여기에 사용되는 사용자 이름 및 암호는 SSH 통해 클러스터에 연결할 때 사용됩니다.

	HDInsight에서 SSH를 사용하는 방법에 대한 자세한 내용은 다음 문서 중 하나를 참조하세요.

	* [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)


7. **데이터 원본**을 클릭하여 클러스터의 기존 데이터 원본을 선택하거나 새로 만듭니다. HDInsight의 Hadoop 클러스터를 프로비전할 때 Azure 저장소 계정을 지정합니다. Hadoop 분산 파일 시스템(HDFS)의 경우처럼 해당 계정의 특정 Blob 저장소 컨테이너는 기본 파일 시스템으로 지정됩니다. HDInsight 클러스터는 기본적으로 사용자가 지정한 저장소 계정과 동일한 데이터 센터에 프로비전됩니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](hdinsight-use-blob-storage.md)을 참조하세요.

	![데이터 원본 블레이드](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.4.png "데이터 원본 구성 제공")

	현재 HDInsight 클러스터의 데이터 원본으로 Azure 저장소 계정을 선택할 수 있습니다. 다음을 사용하여 **데이터 원본** 블레이드의 항목을 이해합니다.

	- **선택 방법**: 모든 구독에서 저장소 계정을 찾을 수 있도록 하려면 이 항목을 **From all subscriptions(모든 구독에서)**로 설정합니다. 기존 저장소 계정의 **저장소 이름** 및 **액세스 키**를 입력하려면 이 항목을 **액세스 키**로 설정합니다.

	- **저장소 계정 선택/새로 만들기**: 클러스터와 연결할 기존 저장소 계정을 찾아 선택하려면 **저장소 계정 선택**을 클릭합니다. 또는 새 저장소 계정을 만들려면 **새로 만들기**를 클릭합니다. 저장소 계정의 이름을 입력할 때 나타나는 필드를 사용합니다. 이름을 사용할 수 있는 경우 녹색 확인 표시가 나타납니다.

	- **기본 컨테이너 선택**: 클러스터에 사용할 기본 컨테이너의 이름을 입력하려면 이 항목을 사용합니다. 여기에 아무 이름이나 입력할 수 있지만, 컨테이너가 이 특정 클러스터에 사용됨을 쉽게 인식할 수 있도록 클러스터와 같은 이름을 사용하는 것이 좋습니다.

	- **위치**: 저장소 계정이 있거나 저장소 계정을 만들 지리적인 지역입니다.

		> [AZURE.IMPORTANT]기본 데이터 원본의 위치를 선택하면 HDInsight 클러스터의 위치도 설정됩니다. 클러스터와 기본 데이터 원본은 같은 지역에 있어야 합니다.

	**선택**을 클릭하여 데이터 원본 구성을 저장합니다.

8. **노드 가격 책정 계층**을 클릭하여 이 클러스터에 대해 만들어질 노드에 대한 정보를 표시합니다. 클러스터에 필요한 작업자 노드 수를 설정합니다. 클러스터의 예상 비용이 블레이드 내에 표시됩니다.

	![노드 가격 책정 계층 블레이드](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.5.png "클러스터 노드 수 지정")
    
    > [AZURE.IMPORTANT]클러스터 만들기에서 또는 클러스터를 만든 후 확장하여 32개 이상의 작업자 노드를 계획하는 경우 최소한 코어 8개와 14GB RAM을 가진 헤드 노드 크기를 선택해야 합니다.
    >
    > 노드 크기 및 관련된 비용에 대한 자세한 내용은 [HDInsight 가격 책정](https://azure.microsoft.com/pricing/details/hdinsight/)을 참조하세요.

	**선택**을 클릭하여 노드 가격 책정 구성을 저장합니다.

9. **새 HDInsight 클러스터** 블레이드에서 **시작 보드에 고정**이 선택되어 있는지 확인한 다음 **만들기**를 클릭합니다. 그러면 클러스터가 만들어지고 Azure 포털의 시작 보드에 클러스터 타일이 추가됩니다. 아이콘이 클러스터를 프로비전 중임을 나타내고 프로비전이 완료되면 변경되어 HDInsight 아이콘을 표시합니다.

프로비전 중|프로비전 완료
------------------|---------------------
	![시작 보드에 표시기 프로비전](./media/hdinsight-hadoop-linux-tutorial-get-started/provisioning.png)|![프로비전된 클러스터 타일](./media/hdinsight-hadoop-linux-tutorial-get-started/provisioned.png)

> [AZURE.NOTE]클러스터를 만드는데 약간의 시간이 걸리며, 일반적으로 약 15분이 소요됩니다. 시작 보드에 있는 타일 또는 페이지 왼쪽에 있는 **알림** 항목을 사용하여 프로비전 프로세스를 확인하세요.

프로비전이 완료되면 시작 보드에서 클러스터 타일을 클릭하여 클러스터 블레이드를 시작합니다.

##Hive 뷰에 연결

Ambari 뷰는 웹 페이지를 통해 여러 유틸리티를 제공합니다. 다음 섹션에서는 Hive 뷰를 사용하여 HDInsight 클러스터에서 Hive 쿼리를 실행할 것입니다.

> [AZURE.NOTE]Ambari는 Linux 기반 HDInsight 클러스터와 함께 제공되는 관리 및 모니터링 유틸리티입니다. Ambari는 이 문서에 설명되지 않은 여러 기능을 갖고 있습니다. 자세한 내용은 [Ambari 웹 UI를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)를 참조하세요.

Azure 포털에서 Ambari 뷰를 보려면 HDInsight 클러스터를 선택한 다음 __빠른 링크__ 섹션에서 __Ambari 뷰__를 선택합니다.

![빠른 링크 섹션](./media/hdinsight-hadoop-linux-tutorial-get-started/quicklinks.png)

웹 브라우저에서 https://CLUSTERNAME.azurehdinsight.net(으)로 이동한 다음(여기서 __CLUSTERNAME__은 HDInsight 클러스터의 이름) 페이지 메뉴의 사각형 집합(__관리자__ 링크 옆 그리고 페이지 왼쪽의 버튼)을 선택하여 사용 가능한 뷰를 표시하는 방법으로 Ambari로 즉시 이동할 수도 있습니다. __Hive 뷰__를 선택합니다.

![Ambari 뷰 선택](./media/hdinsight-hadoop-linux-tutorial-get-started/selecthiveview.png).

> [AZURE.NOTE]Ambari에 액세스할 때 사이트에 인증하라는 메시지가 표시됩니다. 클러스터를 만들 때 사용한 관리자(기본값 `admin`), 계정 이름 및 암호를 입력합니다.

다음과 유사한 결과가 표시됩니다.

![쿼리 편집기 섹션이 포함된 Hive 뷰 페이지 이미지](./media/hdinsight-hadoop-linux-tutorial-get-started/hiveview.png)

##<a name="hivequery"></a>Hive 쿼리 실행

Hive 뷰에서 다음 단계에 따라 클러스터에 포함된 데이터에 대해 Hive 쿼리를 실행합니다.

1. 페이지의 __쿼리 편집기__ 섹션에서 다음 HiveQL 문을 워크시트에 붙여넣습니다.

		DROP TABLE log4jLogs;
		CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
		STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
		SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

	이러한 문은 다음 작업을 수행합니다.

	- **DROP TABLE** - 테이블이 이미 있는 경우 테이블과 데이터 파일을 삭제합니다.
	- **CREATE EXTERNAL TABLE** - Hive에서 새 “외부” 테이블을 만듭니다. 외부 테이블은 Hive에 테이블 정의만 저장하고, 데이터는 원래 위치에 남아 있습니다.
	- **ROW FORMAT** - 데이터의 형식 지정 방식을 Hive에 알립니다. 이 경우, 각 로그의 필드는 공백으로 구분됩니다.
	- **STORED AS TEXTFILE LOCATION** - 데이터가 저장된 위치(example/data 디렉터리) 및 텍스트로 저장되었음을 Hive에 알립니다.
	- **SELECT** - t4 열에 ERROR 값이 포함된 모든 행의 수를 선택합니다.

	>[AZURE.NOTE]자동화된 데이터 업로드 프로세스와 같은 외부 원본이나 또 다른 MapReduce 작업을 통해 기본 데이터를 업데이트해야 하지만 Hive 쿼리에서 항상 최신 데이터를 사용하려고 할 경우 외부 테이블을 사용해야 합니다. 외부 테이블을 삭제하면 데이터는 삭제되지 *않고* 테이블 정의만 삭제됩니다.

2. 쿼리 편집기 하단의 __실행__ 단추를 사용하여 쿼리를 시작합니다. 버튼이 주황색으로 변하고 텍스트가 __실행 중지__로 바뀔 것입니다. 쿼리 편집기 아래에 __쿼리 프로세스 결과__ 섹션이 나타나고 작업에 대한 정보가 표시될 것입니다.

    > [AZURE.IMPORTANT]일부 브라우저에서는 로그 또는 결과 정보가 올바르게 새로 고침되지 않을 수 있습니다. 작업을 실행했는데 로그가 업데이트되거나 결과가 반환되지 않고 작업이 계속 실행되는 것 같으면 Mozilla FireFox 또는 Google Chrome를 사용해 보세요.
    
3. 쿼리가 완료되면 __쿼리 프로세스 결과__ 섹션에 작업 결과가 표시됩니다. __실행 중지__ 단추는 다시 녹색 __실행__ 단추로 되돌아갑니다. __결과__ 탭에는 다음 정보가 표시됩니다.

        sev       cnt
        [ERROR]   3

    __로그__ 탭을 사용하여 작업에서 생성된 로깅 정보를 볼 수 있습니다. 쿼리에 문제가 있는 경우 이 정보를 사용하여 문제를 해결할 수 있습니다.
    
    > [AZURE.TIP]__쿼리 프로세스 결과__ 섹션의 왼쪽 상단에 __결과 저장__ 드롭다운이 있습니다. 이 드롭다운을 사용하여 결과를 다운로드하거나 HDInsight 저장소에 CSV 파일로 저장할 수 있습니다.

3. 이 쿼리의 처음 네 줄을 선택한 다음 __실행__을 선택합니다. 작업이 완료되어도 결과가 없습니다. 쿼리의 일부를 선택한 상태에서 __실행__ 단추를 사용하면 선택한 문만 실행되기 때문입니다. 이 예에서는 테이블에서 행을 검색하는 최종 문이 선택한 네 줄에 포함되지 않았습니다. 해당 줄만 선택하고 __실행__ 단추를 사용하면 예상된 결과가 표시될 것입니다.

3. __쿼리 편집기__ 하단의 __새 워크시트__ 단추를 사용하여 새 워크시트를 만듭니다. 새 워크시트에 다음 HiveQL 문을 입력합니다.

		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

	이러한 문은 다음 작업을 수행합니다.

	- **CREATE TABLE IF NOT EXISTS** - 테이블이 아직 없는 경우 테이블을 만듭니다. **EXTERNAL** 키워드가 사용되지 않으면 Hive 데이터 웨어하우스에 저장되고 Hive에서 완전히 관리되는 내부 테이블입니다. 외부 테이블과 달리 내부 테이블을 삭제하면 기본 데이터도 삭제됩니다.
	- **STORED AS ORC** - 데이터를 ORC(Optimized Row Columnar) 형식으로 저장합니다. Hive 데이터를 저장하기 위한 고도로 최적화되고 효율적인 형식입니다.
	- **덮어쓰기 삽입... SELECT** - [ERROR]가 포함된 **log4jLogs** 테이블에서 행을 선택하고 데이터를 **errorLogs** 테이블에 삽입합니다.
    
    __실행__ 단추를 사용하여 이 쿼리를 실행합니다. 이 쿼리가 아무런 행도 반환하지 않기 때문에 __결과__ 탭에 정보가 없을 것입니다. 하지만 상태는 __SUCCEEDED__로 표시될 것입니다.
    
4. 쿼리 편집기의 오른쪽에는 아이콘 행이 있습니다. 체인 모양 아이콘을 선택합니다.

    ![아이콘](./media/hdinsight-hadoop-linux-tutorial-get-started/icons.png)
    
    이 아이콘은 쿼리의 __시각적 설명__ 뷰로, 복잡한 쿼리 흐름을 이해하는 데 도움이 될 것입니다. 쿼리 편집기의 __설명__ 단추를 사용하여 이 아이콘과 동일한 텍스트 명령을 볼 수 있습니다.
    
    ![시각적 설명 이미지](./media/hdinsight-hadoop-linux-tutorial-get-started/visualexplain.png)
    
    다른 아이콘은 다음과 같습니다.
    
	* 설정: 기어 아이콘을 통해 설정 `hive.execution.engine` 또는 Tez 매개 변수 등 하이브 설정을 변경할 수 있습니다.
	* Tez: Tez가  쿼리를 수행하는 데 사용되는 방향성 비순환 그래프(DAG)를 표시합니다. 이전에 실행한 쿼리에 대한 DAG를 보려는 경우 __Tez 뷰__를 대신 사용합니다.
	* 알림: “쿼리가 제출되었습니다.” 또는 쿼리 실행 시 오류가 발생한 경우, 알림을 표시합니다.

5. __SQL__ 아이콘을 선택하여 쿼리 편집기로 돌아간 다음 새 워크시트를 만들고 다음 쿼리를 입력합니다.

        SELECT * from errorLogs;
    
    편집기 하단의 __다른 이름으로 저장__ 단추를 사용합니다. 이 쿼리의 이름을 __Errorlogs__라고 입력하고 __확인__을 선택합니다. 워크시트의 이름이 __Errorlogs__로 변경됩니다.
    
    저장된 쿼리가 페이지 상단의 __저장된 쿼리__ 탭에도 표시됩니다. 쿼리를 선택하면 __Errorlogs__가 표시됩니다. 이름을 선택하면 쿼리 편집기에서 해당 쿼리가 열립니다.

4. __Errorlogs__ 쿼리를 실행합니다. 다음과 같은 결과가 표시될 것입니다.

        errorlogs.t1 	errorlogs.t2 	errorlogs.t3 	errorlogs.t4 	errorlogs.t5 	errorlogs.t6 	errorlogs.t7
        2012-02-03 	18:35:34 	SampleClass0 	[ERROR] 	incorrect 	id 	
        2012-02-03 	18:55:54 	SampleClass1 	[ERROR] 	incorrect 	id 	
        2012-02-03 	19:25:27 	SampleClass4 	[ERROR] 	incorrect 	id

## <a name="nextsteps"></a>다음 단계

이 문서에서는 Azure 포털을 사용하여 Linux 기반 HDInsight 클러스터를 만들고, SSH를 사용하여 클러스터에 연결하는 방법과 기본 Hive 쿼리를 수행하는 방법을 배웠습니다.

HDInsight를 사용해서 데이터를 분석하는 데 대한 자세한 내용은 다음을 참조하세요.

- Visual Studio에서 Hive 쿼리를 수행 하는 방법을 비롯하여 HDInsight로 Hive를 사용하는 데 대한 자세한 내용은 [HDInsight로 Hive 사용][hdinsight-use-hive]을 참조하세요.

- 데이터를 변환하는 데 사용되는 언어인 Pig에 대해 알아보려면 [HDInsight로 Pig 사용][hdinsight-use-pig]을 참조하세요.

- Hadoop에서 데이터를 처리하는 프로그램을 작성하는 방법인 MapReduce에 대해 알아보려면 [HDInsight로 MapReduce 사용][hdinsight-use-mapreduce]을 참조하세요.

- Visual Studio에 HDInsight Tools를 사용하여 HDInsight의 데이터를 분석하는 방법에 대해 알아보려면 [HDInsight용 Visual Studio Hadoop 도구를 사용하여 시작](hdinsight-hadoop-visual-studio-tools-get-started.md)을 참조하세요.

사용자 고유의 데이터로 작업을 시작하고 HDInsight이 데이터를 저장하는 방법과 HDInsight로 데이터를 가져오는 방법에 대해 더 알아보려면 다음을 참조하세요.

- HDInsight에서 Azure Blob 저장소를 만드는 방법에 대한 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](hdinsight-use-blob-storage.md)을 참조하세요.

- 데이터를 HDInsight로 업로드하는 방법에 대한 정보는 [HDInsight에 데이터 업로드][hdinsight-upload-data]를 참조하십시오.

HDInsight 클러스터를 만들거나 관리하는 방법에 대해 자세히 알아보려면 다음을 참조하세요.

- Linux 기반 HDInsight 클러스터 관리에 대해 알아보려면 [Ambari를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)를 참조하세요.

- HDInsight 클러스터를 만들 때 선택하는 옵션에 대해 자세히 알아보려면 [사용자 지정 옵션을 사용하여 Linux에 HDInsight 프로비전](hdinsight-hadoop-provision-linux-clusters.md)을 참조하세요.

- Linux 및 Hadoop를 사용하는 것이 익숙하지만 HDInsight에서 Hadoop에 대한 구체적인 정보를 알아보려면 [Linux에서 HDInsight로 작업](hdinsight-hadoop-linux-information.md)을 참조하세요. 다음과 같은 정보를 제공합니다.

	* 클러스터에서 호스트되는 서비스(예: Ambari 및 WebHCat)의 URL
	* 로컬 파일 시스템의 Hadoop 파일 및 예제의 위치
	* 기본 데이터 저장소로 HDFS 대신 Azure 저장소(WASB) 사용


[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell.md
[powershell-open]: ../install-configure-powershell.md#Install

[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.browse.output.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.ClusterStatus.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData2.png

<!---HONumber=AcomDC_0114_2016-->